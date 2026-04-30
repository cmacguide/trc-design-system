# ComplianceTrafficLight

Visualizacao compacta do status de compliance documentos TRC (CND, FGTS, INSS, ASO). Aparece em cards de obras, headers de obreiros, e dashboards de auditoria.

## Props

```typescript
type ComplianceProps = {
  items: Array<{
    key: "CND" | "FGTS" | "INSS" | "ASO";
    status: "ok" | "warn" | "block";
    detail?: string;
  }>;
  size: "sm" | "md" | "lg";
  layout?: "row" | "grid" | "responsive" | "hero-stack"; // default "responsive"
};
```

Status semantica → token:
- `ok` → `--status-success-*`
- `warn` → `--status-warning-*`
- `block` → `--status-error-*`

## Token application

- **Items render** as `<StatusDot>` + label (sm/md) ou full card (lg)
- **size=lg:** cada item card com bg semantic, border line semantic, padding `var(--space-3)`, radius `var(--radius-2)`
- **Mono detail text:** `--text-mono-xs`
- **Layout grid:**
  ```css
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: var(--space-2);
  ```
- **Layout row:**
  ```css
  display: flex;
  gap: var(--space-2);
  flex-wrap: wrap;
  ```

## Variants visuais

| size | Visual |
|---|---|
| `sm` | Apenas StatusDot 6px + label inline `--text-caption` |
| `md` | StatusDot 8px + label `--text-ui` + detail mono em hover tooltip |
| `lg` | Card completo com bg/border semantic + label + detail mono `--text-mono-xs` visivel |

## Markup pattern

```tsx
<ul
  className="compliance-traffic-light"
  data-layout={layout}
  data-size={size}
  role="list"
>
  {items.map((item) => (
    <li
      key={item.key}
      className="ctl-item"
      data-status={item.status}
    >
      <StatusDot status={item.status} size={size === "lg" ? 12 : 8} />
      <span className="ctl-label">{item.key}</span>
      {item.detail && size !== "sm" && (
        <span className="ctl-detail">{item.detail}</span>
      )}
    </li>
  ))}
</ul>
```

## Responsive behavior (container queries)

`layout="responsive"` (default) usa **container queries**, NAO viewport — porque ComplianceTrafficLight aparece em contextos heterogeneos: hero card admin-decision-focus (~720-920px), obra-list-item (~480px), sidebar Empreteiro (~220px), modal de auditoria (~600px).

```css
.ctl-container {
  container-type: inline-size;
  container-name: ctl;
}

.ctl[data-layout="responsive"] {
  display: grid;
  gap: var(--space-2);
  /* Default <360px container: single column com hero pattern */
  grid-template-columns: 1fr;
}

/* Container 360px+: 2x2 (hierarquia: criticos top, secundarios bottom) */
@container ctl (min-width: 360px) {
  .ctl[data-layout="responsive"] {
    grid-template-columns: 1fr 1fr;
    /* Order natural do array CND/FGTS/INSS/ASO mantem hierarquia mental */
  }
}

/* Container 560px+: 4 cols horizontal */
@container ctl (min-width: 560px) {
  .ctl[data-layout="responsive"] {
    grid-template-columns: repeat(4, 1fr);
    gap: var(--space-3);
  }
}
```

**Por que 2x2 com hierarquia (NAO wrap horizontal generico):** as 4 categorias TEM hierarquia implicita no contexto trabalhista TRC:
- **Top row (CND, FGTS):** criticos federais — bloqueiam pagamento por lei
- **Bottom row (INSS, ASO):** importantes mas com janelas de tolerancia maiores

Wrap horizontal generico (`flex-wrap: wrap`) PERDE essa hierarquia. Grid 2x2 com order natural a preserva. **Anti-pattern: ordenar visualmente por status (verdes top, vermelhos bottom) — destrói o mapping mental constante "CND sempre top-left."**

**Single-column hero (<360px container):** quando container e muito estreito (sidebar empreteiro, modal compacto), usar pattern hero-list:

```css
@container ctl (max-width: 359px) {
  .ctl[data-layout="responsive"] .ctl-item:first-child {
    /* Card critico (geralmente CND) renderiza maior, com mais detail */
    grid-column: 1;
    padding: var(--space-4);
    font-size: var(--text-h3);
  }
  .ctl[data-layout="responsive"] .ctl-item:not(:first-child) {
    /* Outros 3 itens compactos abaixo */
    padding: var(--space-2);
  }
}
```

**Caller responsibility:** envolver o componente com `<div className="ctl-container">` (declara container context). Sem isso, container queries nao disparam — fallback gracioso para grid 2x2 default em qualquer largura.

## Accessibility

- `<ul role="list">` para semantica explicita
- StatusDot deve ter `aria-label` descrevendo status ("CND OK" / "CND vencida")
- `data-status` para CSS querying
- Cores nunca como unico canal (sempre + label de texto + StatusDot pattern)
