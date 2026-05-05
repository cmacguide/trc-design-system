---
name: AdminCrossTab
version: 1.0.0
since: S167
---

# AdminCrossTab

Pagina pivot cross-tab para visao Admin (/admin/cross-tab). Cruza obras (linhas) x meses
(colunas) x metrica selecionavel (Valor / Percentual / Status) usando TanStack Table v8
headless. ADR-S167-001.

> **Audit trail:** Validated via canary `s167-admin-tela2-canary.html` — sticky multi-axis
> (col-0 left:0 z-3, row-0 top:0 z-3, corner z-4), gradient hint mobile, 15x5 dataset,
> 3 viewports 1920/768/412px PASS Gate G-1 DSVL.

---

## Stories Cenario A (Storybook Gate 2.5 — F4.5)

### Story A1 — Default (activeMetric="valor", sem filtros)

**Props:**
```typescript
{
  activeMetric: "valor",
  filters: { empreiteira: "", statusGeral: "", busca: "", dateRange: { inicio: "2026-01", fim: "2026-05" } },
  data: MOCK_DATA  // 15 obras x 5 meses
}
```

**Verificacoes visuais obrigatorias:**
- [ ] Tabela renderiza com 15 linhas + row-0 (meses) + row-1 (metrica sub-header)
- [ ] col-0 sticky visivel (z-index:3, background branco) ao scrollar horizontalmente
- [ ] corner z-4 acima de row-0 e col-0 sem sobreposicao
- [ ] Coluna "Total" visivel ao final (apenas quando activeMetric="valor")
- [ ] Celulas valorMedido: JetBrains Mono + font-feature-settings:"tnum" + formato BRL
- [ ] Celulas null: "-" em color var(--pivot-cell-null-color) (#ADB5BD)
- [ ] 9 tokens pivot resolvidos (DevTools: sem var() com fallback vazio)

### Story A2 — Metrica Percentual (activeMetric="percentual")

**Props:**
```typescript
{
  activeMetric: "percentual",
  filters: { empreiteira: "", statusGeral: "", busca: "", dateRange: { inicio: "2026-01", fim: "2026-05" } },
  data: MOCK_DATA
}
```

**Verificacoes visuais obrigatorias:**
- [ ] Coluna "Total" NAO renderizada (apenas em activeMetric="valor")
- [ ] Badges coloridos visiveis: >=70 success (verde) / >=40 warning (amarelo) / <40 error (vermelho)
- [ ] Celulas null ainda mostram "-" em pivot-cell-null-color

### Story A3 — Metrica Status (activeMetric="status")

**Props:**
```typescript
{
  activeMetric: "status",
  filters: { empreiteira: "", statusGeral: "", busca: "", dateRange: { inicio: "2026-01", fim: "2026-05" } },
  data: MOCK_DATA
}
```

**Verificacoes visuais obrigatorias:**
- [ ] StatusDot componente DS visivel em celulas com status="ok"/"warn"/"block"
- [ ] Celulas null: "-" (StatusDot nao renderizado para null)
- [ ] Coluna "Total" ausente

### Story A4 — Viewport Mobile 412px (activeMetric="valor")

**Storybook viewport addon:** width=412px, height=900px

**Verificacoes visuais obrigatorias:**
- [ ] GradientHint visivel na borda direita (overlay branco-transparente, 48px)
- [ ] GradientHint tem pointer-events:none (nao intercepta cliques)
- [ ] Tabela NAO colapsada — estrutura pivot intacta (sem card-list)
- [ ] Top bar mobile visivel (hamburger + TRC brand + botao Filtros)
- [ ] Segmented control Valor/Percentual/Status visivel abaixo do header
- [ ] Scroll horizontal disponivel (tabela wider que viewport)

### Story A5 — Filtro empreiteira ativo

**Props:**
```typescript
{
  activeMetric: "valor",
  filters: { empreiteira: "Construtora Alpha", statusGeral: "", busca: "", dateRange: { inicio: "2026-01", fim: "2026-05" } },
  data: MOCK_DATA
}
```

**Verificacoes visuais obrigatorias:**
- [ ] Apenas linhas da empreiteira "Construtora Alpha" renderizadas (6 obras)
- [ ] Dropdown empreiteira mostra valor selecionado
- [ ] Contagem correta de obras no subtitle da page-header

---

## Variants (para Storybook controls auto-derivados)

| Prop | Tipo | Valores | Default |
|---|---|---|---|
| activeMetric | string | "valor" / "percentual" / "status" | "valor" |
| empreiteira | string | "" / "Construtora Alpha" / "Engenharia Beta" / "Grupo Omega" | "" |
| statusGeral | string | "" / "ok" / "warn" / "block" | "" |
| viewportWidth | number | 412 / 768 / 1920 | 1920 |

---

## Props componente

```typescript
// AdminCrossTab.tsx — props raiz
type AdminCrossTabProps = {
  initialMetric?: ActiveMetric;    // default: "valor"
  initialFilters?: Partial<PivotFilters>;
};

// PivotTable.tsx — props sub-componente
type PivotTableProps = {
  data: PivotRow[];
  activeMetric: ActiveMetric;
  filters: PivotFilters;
  onFiltersChange: (filters: PivotFilters) => void;
};

// PivotCellValue.tsx — renderizador de celula
type PivotCellValueProps = {
  value: MonthData | undefined;
  metric: ActiveMetric;
  obraNome: string;        // para aria-label
  mes: string;             // para aria-label
};

// GradientHint.tsx — overlay scroll indicator
type GradientHintProps = {
  scrollContainerRef: React.RefObject<HTMLDivElement>;
  visible: boolean;
};
```

---

## Anatomy

```
AdminCrossTab
  PageHeader ("Visao Cross-Tab" + export btn)
  FilterBar
    EmpreteiraDropdown
    StatusDropdown
    BuscaInput (debounce 300ms)
    DateRangePicker
    MetricSegmentedControl (Valor | Percentual | Status)
  PivotWrap (flex:1, overflow:hidden)
    PivotScrollContainer (position:relative, overflow:auto)
      GradientHint (position:absolute right:0, pointer-events:none)
      table[role="table"]
        thead
          tr (row-0: mes headers, sticky top:0 z-3)
            th.corner (col-0+row-0, sticky left:0 top:0 z-4)
            th.month x N (sticky top:0 z-3, scope="col")
            th.total (apenas activeMetric="valor")
          tr (row-1: metrica sub-header, sticky top:36px z-3)
            th.corner-sub (sticky left:0 top:36px z-4)
            th.metric x N
        tbody
          tr x 15 obras
            th.obra (sticky left:0 z-3, scope="row")
              StatusDot(statusGeral) + obraNome
            td x N meses
              PivotCellValue(value, metric)
            td.total (apenas activeMetric="valor")
```

---

## Tokens usados

Todos os tokens abaixo existem em `tokens.md` (secao §Pivot Table para os 9 novos).

| Propriedade | Token | Por que |
|---|---|---|
| thead background | `--pivot-header-bg` | wrapper semantico de --surface-tertiary |
| corner background | `--pivot-corner-bg` | wrapper semantico de --surface-tertiary |
| cell padding-x | `--pivot-cell-padding-x` | wrapper de --space-3 (12px) |
| cell padding-y | `--pivot-cell-padding-y` | wrapper de --space-2 (8px) |
| celulas hairline | `--pivot-cell-border` | wrapper de --border-subtle |
| col-0 largura | `--pivot-row-label-width` | 200px fixo para nomes de obras |
| font-size cabecalhos | `--pivot-header-font-size` | wrapper de --text-label (11px) |
| null color | `--pivot-cell-null-color` | wrapper de --text-tertiary |
| hover bg celula | `--pivot-hover-bg` | wrapper de --hover-overlay-light |
| valores monetarios | `--font-mono` + tnum | JetBrains Mono tabular |
| badges percentual | `--status-*-fg/bg` | success/warning/error existentes |

---

## Acessibilidade

- `<table>` semantico nativo — nao div-table
- `<th scope="col">` em todos os cabecalhos de mes
- `<th scope="row">` em col-0 (nome da obra)
- `aria-label` em cada `<td>`: `"{obraNome} / {mes} / {valorFormatado}"`
- `GradientHint`: `aria-hidden="true"`, `pointer-events:none`
- FilterBar: `role="search"`, `aria-label="Filtros da tabela pivot"`
- Scroll container: `role="region"`, `aria-label="Tabela pivot obras por mes"`

---

## ADRs relacionados

- ADR-S167-001: TanStack Table v8 headless — engine escolhido
- ADR-S167-002: 9 tokens pivot como wrappers semanticos — todos declarados em tokens.md §Pivot
- ADR-S167-003: Mock inline (sem API real na Fase 1)
- ADR-S167-004: SKIP cross-highlight hover persistente — apenas hover CSS simples

## Relacionados no DS

- `PivotTable.md` — documentacao de tokens e comportamento
- `StatusDot.md` — usado em modo metrica "status"
- `tokens.md §Pivot Table` — 9 tokens novos S167
