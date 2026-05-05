# PivotTable

> Componente Admin Tela 2 — Visao Cross-Tab (/admin/cross-tab). Introduzido em S167.
> Engine: TanStack Table v8 headless (MIT, ~45KB). ADR-S167-001.

## Uso

```tsx
import { AdminCrossTab } from "@/pages/admin/AdminCrossTab";
// roteado em /admin/cross-tab via BrowserRouter
```

## Props principais

| Prop | Tipo | Default | Descricao |
|---|---|---|---|
| data | PivotRow[] | MOCK_DATA | Dataset obras x meses |
| activeMetric | ActiveMetric | "valor" | Metrica de celula ativa |
| filters | PivotFilters | defaults | Estado dos filtros |

## Tipos

```typescript
type ActiveMetric = "valor" | "percentual" | "status";

type MonthData = {
  valorMedido:  number | null;
  percentual:   number | null;
  status:       "ok" | "warn" | "block" | null;
};

type PivotRow = {
  obraId:       string;
  obraNome:     string;
  empreiteira:  string;
  statusGeral:  "ok" | "warn" | "block" | "inactive";
  meses:        Record<string, MonthData>;
};

type PivotFilters = {
  empreiteira:  string;   // "" = todas
  statusGeral:  string;   // "" = todos
  busca:        string;
  activeMetric: ActiveMetric;
  dateRange:    { inicio: string; fim: string }; // "YYYY-MM" format
};
```

## Tokens utilizados (9 pivot + tokens DS base)

```css
/* Pivot-specific (S167, declarados no :root) */
--pivot-header-bg        /* thead background */
--pivot-corner-bg        /* corner intersecao col-0 + row-0 */
--pivot-cell-padding-x   /* horizontal padding das celulas */
--pivot-cell-padding-y   /* vertical padding das celulas */
--pivot-cell-border      /* hairline entre celulas */
--pivot-row-label-width  /* largura fixa col-0 sticky */
--pivot-header-font-size /* font-size cabecalhos mes */
--pivot-cell-null-color  /* cor do "-" em celulas null */
--pivot-hover-bg         /* hover state celula */

/* DS base reutilizados */
--font-mono, --font-sans
--text-primary, --text-secondary, --text-tertiary
--surface-elevated, --surface-bg, --surface-secondary
--border-default, --border-subtle
--status-success-fg/bg, --status-warning-fg/bg, --status-error-fg/bg
--brand-primary, --brand-primary-soft
```

## Comportamento sticky (ADR-S167-001)

```
col-0 (th scope="row"):  position:sticky, left:0, z-index:3
row-0 (mes headers):     position:sticky, top:0, z-index:3
row-1 (metrica label):   position:sticky, top:36px, z-index:3
corner (col-0 + row-0):  position:sticky, left:0, top:0, z-index:4
corner-sub:              position:sticky, left:0, top:36px, z-index:4
```

**PROIBIDO**: `overflow:hidden` em qualquer ancestor do scroll-container.
Comentar no JSX: `// NAO adicionar overflow:hidden em nenhum pai deste elemento`

## Comportamento de celulas

| Metrica | Renderizador |
|---|---|
| "valor" | `<span style={{fontFamily:"JetBrains Mono",fontFeatureSettings:'"tnum"'}}>` + Intl.NumberFormat pt-BR BRL |
| "percentual" | badge colorido: >=70 success / >=40 warning / else error |
| "status" | `<StatusDot status={v} />` (componente DS existente) |
| null | `"-"` em `color: var(--pivot-cell-null-color)` — NUNCA string vazia ou "0" |

## Coluna total

Adicionada apenas quando `activeMetric === "valor"`. Soma dos valorMedido do periodo.
Estilo distinto: `background: var(--surface-secondary)`, `border-left: 2px solid var(--border-default)`.

## Gradient hint mobile (412px)

```tsx
<div style={{
  position: "absolute", right: 0, top: 0, bottom: 0, width: 48,
  background: "linear-gradient(to left, white, transparent)",
  pointerEvents: "none",  // NAO bloqueia foco de teclado
  zIndex: 2,
}} />
```

Desaparece quando `scrollLeft + clientWidth >= scrollWidth - 4px` (listener onScroll).
NUNCA colapsar para card-list em nenhum breakpoint (tabela horizontal em todos os viewports).

## Acessibilidade

- Elemento raiz: `<table>` semantico nativo (nao div-table)
- Cabecalhos mes: `<th scope="col">`
- Cabecalho obra col-0: `<th scope="row">`
- aria-label em cada `<td>`: `"{obraNome} / {mes} / {valorFormatado}"`
- GradientHint: `aria-hidden="true"`, `pointer-events: none`
- Filter-bar: `role="search"`, `aria-label="Filtros da tabela pivot"`
- Scroll region: `role="region"`, `aria-label="Tabela pivot obras por mes"`

## Filtros disponíveis

- Empreiteira (dropdown) — filtra por `row.empreiteira`
- Status Geral (dropdown) — filtra por `row.statusGeral`
- Busca textual (debounce 300ms) — filtra por `row.obraNome`
- Metrica (segmented control): Valor / Percentual / Status
- Periodo (dateRange inicio/fim) — formato "YYYY-MM"

## ADRs relacionados

- ADR-S167-001: TanStack Table v8 headless como engine pivot
- ADR-S167-002: 9 tokens pivot como wrappers semanticos no :root
- ADR-S167-003: Dados mock inline (sem API real na Fase 1)
- ADR-S167-004: SKIP cross-highlight hover persistente (apenas hover CSS simples via --pivot-hover-bg)

## Relacionados no DS

- `StatusDot.md` — usado em modo metrica "status"
- `tokens.md §Pivot Table` — definicao dos 9 tokens pivot
