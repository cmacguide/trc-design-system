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
  layout: "row" | "grid"; // row mobile, grid web
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

## Accessibility

- `<ul role="list">` para semantica explicita
- StatusDot deve ter `aria-label` descrevendo status ("CND OK" / "CND vencida")
- `data-status` para CSS querying
- Cores nunca como unico canal (sempre + label de texto + StatusDot pattern)
