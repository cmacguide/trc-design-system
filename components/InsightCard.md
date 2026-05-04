---
name: InsightCard
version: 1.0.0
since: S166
---

# InsightCard

Wrapper card para visualizacoes gerenciais na pagina Insights Gerenciais (`/engenheiro/insights`). Empacota header, area de grafico/lista, e rodape com link de navegacao em uma unidade coesa.

> **Audit trail:** Validated via canary `s166-engenheiro-tela3-canary.html` sections `.insight-card[data-variant=bar]`, `.insight-card[data-variant=line]`, `.insight-card[data-variant=stacked]`, `.insight-card[data-variant=alerts]` — CSS `.insight-card`, `.card-header`, `.card-chart`, `.card-footer` (lines ~452-525 do canary).

---

## Variants

| Variant | Card | Uso |
|---|---|---|
| `bar` | Card 1 — Vencimentos proximos 30 dias | Bar chart SVG com threshold vermelho |
| `line` | Card 2 — Taxa bloqueio biometria | Line chart SVG com threshold amarelo tracejado |
| `stacked` | Card 3 — Compliance por obra | Stacked bars por obra + `FoundryPatternBadge` inline |
| `alerts` | Card 4 — Alertas ativos | Lista clicavel de alertas com `StatusDot` + counter chip |

---

## Props

```typescript
type InsightCardProps = {
  variant: "bar" | "line" | "stacked" | "alerts";
  title: string;          // ex: "Vencimentos proximos 30 dias"
  subtitle: string;       // ex: "Documentos por dia"
  footerText: string;     // ex: "Total: 286 docs proximos 30 dias"
  footerLink?: {
    label: string;        // ex: "ver detalhes →"
    href: string;         // ex: "/engenheiro/vencimentos"
  };
  children: React.ReactNode;  // area de grafico ou lista
};
```

---

## Anatomy

```
┌──────────────────────────────────────────────────────┐
│  .card-header                                        │
│    .card-title     (font-size: --text-ui, 700)       │
│    .card-subtitle  (font-size: --text-caption, muted)│
├──────────────────────────────────────────────────────┤
│                                                      │
│  .card-chart  (flex: 1, align-items: flex-end)       │
│  — bar/line: SVG chart fill                          │
│  — stacked: .compliance-rows (ver InsightCard §7)    │
│  — alerts: .alert-list (ver InsightCard §8)          │
│                                                      │
├──────────────────────────────────────────────────────┤
│  .card-footer                                        │
│    .card-footer-text     "Total: 286 docs"           │
│    .card-footer-link     "ver detalhes →"            │
└──────────────────────────────────────────────────────┘
```

---

## Tokens usados

Todos os tokens abaixo existem em `tokens.md` — **nenhum token novo inventado nesta spec**.

| Propriedade CSS | Token | Valor light | Por que |
|---|---|---|---|
| `background` | `--surface-elevated` | `#FFFFFF` | Card sobre warm-grey ground = value contrast natural |
| `border-radius` | `--radius-3` | `12px` | Cards maiores usam radius-3; inputs usam radius-1 |
| `box-shadow` | `--elev-2` | ver tokens.md §4 | Elevacao sutil, "calque sobre papel" |
| `border` | `--border-subtle` | `#E9ECEF` | Hairline funcional para definir card sem sombra pesada |
| `padding` | `--space-5` | `24px` | Respiro interno confortavel para conteudo denso |
| `.card-header margin-bottom` | `--space-4` | `16px` | Separa header do corpo sem "ilha" |
| `.card-footer border-top` | `--border-subtle` | `#E9ECEF` | Divisor hairline, nao pesado |
| `.card-footer padding-top` | `--space-3` | `12px` | Compacto mas com respiro |
| `.card-title color` | `--text-primary` | `#212529` | Texto principal |
| `.card-subtitle color` | `--text-tertiary` | `#ADB5BD` | Subordinado — caption muted |
| `.card-footer-text color` | `--text-secondary` | `#6C757D` | Informativo secundario |
| `.card-footer-link color` | `--brand-primary` | `#DB592A` | CTA link brand TRC |
| `.card-chart background` (stacked bars) | `--surface-tertiary` | `#F1F3F5` | Trilha das barras "vazia" |

---

## Dimensoes

| Propriedade | Desktop (>=1024px) | Mobile (<768px) |
|---|---|---|
| `min-height` | **320px** | `auto` (conteudo dita) |
| `padding` | `var(--space-5)` = 24px | `var(--space-5)` = 24px (mantido) |

**Por que 320px desktop:** garante que cards com poucas rows (ex: Card 3 com 5 obras) nao colem excessivamente. Grid 4-col em 1920px com sidebar 240px = ~408px por card — 320px de altura minima cria proporcao razoavel para charts SVG respirarem.

---

## Markup pattern

```tsx
<article
  className="insight-card"
  data-variant={variant}
  aria-label={title}
>
  <div className="card-header">
    <div className="card-title">{title}</div>
    <div className="card-subtitle">{subtitle}</div>
  </div>

  <div className="card-chart">
    {children}
  </div>

  <div className="card-footer">
    <span className="card-footer-text">{footerText}</span>
    {footerLink && (
      <a className="card-footer-link" href={footerLink.href}>
        {footerLink.label}
      </a>
    )}
  </div>
</article>
```

---

## CSS

```css
.insight-card {
  background: var(--surface-elevated);
  border-radius: var(--radius-3);
  box-shadow: var(--elev-2);
  border: 1px solid var(--border-subtle);
  padding: var(--space-5);
  display: flex;
  flex-direction: column;
  min-height: 320px;
}

/* Mobile: sem min-height — conteudo dita altura */
@media (max-width: 767px) {
  .insight-card {
    min-height: auto;
  }
}

/* --- Header --- */
.card-header {
  margin-bottom: var(--space-4);  /* 16px */
}
.card-title {
  font-size: var(--text-ui);      /* 13px */
  font-weight: 700;
  color: var(--text-primary);
  letter-spacing: -0.01em;
  line-height: 1.3;
}
.card-subtitle {
  font-size: var(--text-caption); /* 12px */
  color: var(--text-tertiary);
  margin-top: 3px;
}

/* --- Chart area --- */
.card-chart {
  flex: 1;
  display: flex;
  align-items: flex-end;
  min-height: 140px;
}
@media (max-width: 767px) {
  .card-chart {
    min-height: 120px;
  }
}
.card-chart svg {
  width: 100%;
  height: 100%;
  overflow: visible;
}

/* --- Footer --- */
.card-footer {
  margin-top: var(--space-4);     /* 16px */
  padding-top: var(--space-3);    /* 12px */
  border-top: 1px solid var(--border-subtle);
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--space-2);
}
.card-footer-text {
  font-size: var(--text-caption);
  color: var(--text-secondary);
  line-height: 1.4;
}
.card-footer-link {
  font-size: var(--text-caption);
  color: var(--brand-primary);
  font-weight: 600;
  white-space: nowrap;
  text-decoration: none;
  transition: color var(--dur-instant) var(--ease-out);
}
.card-footer-link:hover {
  color: var(--brand-primary-hover);
}
```

---

## Variant: `bar` — Card 1 Vencimentos

**Conteudo do `.card-chart`:** SVG inline, viewBox 300×148.

Elementos SVG obrigatorios:
- Linhas de grid Y: `stroke: var(--border-subtle)`, `stroke-dasharray: 3 3` (exceto baseline)
- Barras normais: `class="bar-neutral"` → `fill: var(--chart-stroke-neutral)` (token adicionado em S166, ver tokens.md §6)
- Barra threshold: `class="bar-threshold"` → `fill: var(--chart-stroke-critical)`
- Linha threshold: `class="chart-threshold-line"` → `stroke: var(--status-warning-fg)`, `stroke-dasharray: 4 3`, 1.5px
- Labels de eixo: `font-family: var(--font-mono)`, `font-size: 9px`, `fill: var(--text-tertiary)`

**Por que threshold vermelho em >20 docs:** dia com >20 vencimentos num periodo de 30d significa acumulo critico — probabilidade alta de miss e multa trabalhista. Threshold visual facilita identificacao instantanea sem ler todos os valores.

Tokens de chart (`--chart-stroke-neutral`, `--chart-stroke-critical`, `--chart-stroke-warning`): ver tokens.md §6 — adicionados em S166.

---

## Variant: `line` — Card 2 Biometria

**Conteudo do `.card-chart`:** SVG inline, viewBox 300×148.

Elementos SVG obrigatorios:
- Polyline principal: `class="line-main"` → `stroke: var(--chart-line-series)` (azul brand secundario `#2563B5`), `stroke-width: 2`, sem fill
- Dots: `class="line-dot"` → `fill: var(--chart-line-series)`, raio 3px
- Ultimo dot: `class="line-dot-last"` → `fill: var(--chart-line-series)`, `stroke: white`, `stroke-width: 2`, raio 4px
- Linha threshold 15%: `class="chart-threshold-line"` → dashed amber — **mesmo padrao do Card 1**

**Por que threshold amarelo tracejado em 15% biometria:** taxa de bloqueio acima de 15% indica que >1 em 7 trabalhadores nao passou biometria na entrada — risco operacional e de compliance NR-01/NR-35 crescente. Tracejado (nao solido) porque e limite de atencao (warn), nao corte critico (block).

**Rodape especial — trend indicator:**
```tsx
<div className="card-footer">
  <div className="trend-indicator">
    <span style={{ color: "var(--status-error-fg)", fontSize: "14px", fontWeight: 700 }}>↗</span>
    <span className="trend-delta">+10pp em 90d (8% → 18%)</span>
  </div>
  {/* Sem footerLink nesta variant — tendencia e a informacao, nao navegacao */}
</div>
```

---

## Variant: `stacked` — Card 3 Compliance + FoundryPatternBadge

**Conteudo do `.card-chart`:** lista de obras com stacked bars + badge de padrao.

```tsx
<div className="card-chart" style={{ flexDirection: "column", alignItems: "stretch", paddingTop: "4px" }}>
  <div className="compliance-rows">
    {obras.map(obra => (
      <div className="compliance-row" key={obra.id}>
        <span className="compliance-obra-label">{obra.nome}</span>
        <div className="stacked-bar-wrap">
          <div className="stacked-bar-seg stack-green" style={{ width: `${obra.pctGreen}%` }} />
          <div className="stacked-bar-seg stack-yellow" style={{ width: `${obra.pctYellow}%` }} />
          <div className="stacked-bar-seg stack-red" style={{ width: `${obra.pctRed}%` }} />
        </div>
        <FoundryPatternBadge status={obra.pattern} confidence={obra.confidence} />
      </div>
    ))}
  </div>
</div>
```

CSS das rows:
```css
.compliance-rows {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
  flex: 1;
  justify-content: space-around;
}
.compliance-row {
  display: flex;
  align-items: center;
  gap: var(--space-2);
}
.compliance-obra-label {
  font-size: var(--text-caption);
  font-weight: 600;
  color: var(--text-secondary);
  min-width: 46px;
  flex-shrink: 0;
}
.stacked-bar-wrap {
  flex: 1;
  height: 18px;
  border-radius: var(--radius-1);
  overflow: hidden;
  display: flex;
  background: var(--surface-tertiary);
}
.stacked-bar-seg {
  height: 100%;
  transition: opacity var(--dur-instant) var(--ease-out);
}
.stacked-bar-seg:hover { opacity: 0.85; }
.stack-green  { fill: var(--chart-stack-ok);      background: var(--chart-stack-ok); }
.stack-yellow { fill: var(--chart-stack-warning);  background: var(--chart-stack-warning); }
.stack-red    { fill: var(--chart-stack-critical); background: var(--chart-stack-critical); }
```

Para cores das barras empilhadas: usar tokens `--chart-stack-ok`, `--chart-stack-warning`, `--chart-stack-critical` (adicionados em S166, ver tokens.md §6).

---

## Variant: `alerts` — Card 4 Alertas + StatusDot

**Conteudo do `.card-chart`:** lista clicavel de alertas.

```tsx
<div className="card-chart" style={{ alignItems: "stretch", flex: 1 }}>
  <div className="alert-list" style={{ width: "100%" }}>
    {alerts.map(alert => (
      <div
        className="alert-row"
        key={alert.id}
        onClick={() => navigate(`/engenheiro/drilldown/${alert.id}`)}
        role="button"
        tabIndex={0}
        title={`Ver detalhe: ${alert.title}`}
      >
        <StatusDot status={alert.severity} size={8} />
        <div className="alert-text-wrap">
          <div className="alert-main-text">{alert.title}</div>
          <div className="alert-sub-text">{alert.source} · ha {alert.age}</div>
        </div>
        <span
          className="alert-counter-chip"
          data-severity={alert.severity === "block" ? "high" : undefined}
        >
          {alert.count}
        </span>
      </div>
    ))}
  </div>
</div>
```

Mapeamento `alert.severity` para `StatusDot.status`: `"block"` → `"block"` (vermelho), `"warn"` → `"warn"` (amarelo), `"ok"` → `"ok"` (verde).

CSS dos alerts:
```css
.alert-list {
  display: flex;
  flex-direction: column;
}
.alert-row {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  padding: var(--space-3) var(--space-2);
  border-radius: var(--radius-1);
  cursor: pointer;
  transition: background var(--dur-instant) var(--ease-out);
}
.alert-row:hover {
  background: var(--surface-secondary);
}
.alert-row + .alert-row {
  border-top: 1px solid var(--border-subtle);
}
.alert-text-wrap {
  flex: 1;
  min-width: 0;
}
.alert-main-text {
  font-size: var(--text-ui);
  font-weight: 500;
  color: var(--text-primary);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.alert-sub-text {
  font-size: var(--text-caption);
  color: var(--text-tertiary);
  margin-top: 1px;
}
.alert-counter-chip {
  background: var(--surface-tertiary);
  color: var(--text-secondary);
  font-size: var(--text-mono-xs);
  font-family: var(--font-mono);
  font-weight: 600;
  padding: 2px 7px;
  border-radius: var(--radius-pill);
  flex-shrink: 0;
}
.alert-counter-chip[data-severity="high"] {
  background: var(--status-error-bg);
  color: var(--status-error-fg);
}
```

---

## Integracao com outros componentes

- **FoundryPatternBadge** (ver `FoundryPatternBadge.md`): usado dentro da variant `stacked` como label inline apos cada stacked bar
- **StatusDot** (ver `StatusDot.md`): usado na variant `alerts` como indicador visual de severidade; sempre acompanhado de label textual

---

## Anti-patterns (concept × forbidden × required substitute)

| Concept | Forbidden | Required substitute |
|---|---|---|
| Card background | `background: var(--surface-bg)` (card some no ground) | **`background: var(--surface-elevated)`** — value contrast natural sobre warm-grey |
| Decoracao gradient bg | `background: linear-gradient(...)` na propria card | **Subtle shadow** `var(--elev-2)` + hairline border `var(--border-subtle)` |
| Min-height mobile | `min-height: 320px` fixo em mobile (espaco desperdicado) | **`min-height: auto` em mobile** — conteudo dita altura |
| Footer separator | Sem border (footer "flutua" no card) | **`border-top: 1px solid var(--border-subtle)`** + `padding-top: var(--space-3)` |
| Link footer | `<button>` estilizado ou underline pesado | **`<a className="card-footer-link">`** com cor brand-primary e hover brand-primary-hover |
| Chart colors semantica | Cores hardcoded hex inline no SVG | **Tokens `--chart-stroke-*` e `--chart-stack-*`** (ver tokens.md §6) |
| Threshold visual | Sem indicador visual (usuario precisa ler valores) | **Linha tracejada + label** para limites warn/critical em todos os charts |

---

## Acessibilidade

- `<article aria-label={title}>` semantica — cada card e uma region com label descritivo
- SVG charts com `aria-label` descritivo no elemento `<svg>`
- `.alert-row` com `role="button"` + `tabIndex={0}` + handler `onKeyDown` (Enter/Space)
- `.alert-counter-chip` informativo: complementa `StatusDot` (cor nao e unico canal)
- Reduced motion: transitions via tokens `--dur-*` + `@media (prefers-reduced-motion: reduce)` global
