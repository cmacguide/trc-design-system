# FilterPills

Audit trail: Validated via canary HTML preview pre-push: docs/assets/preview/empreiteiro-tela1-v1-preview.html section [filter pills container]

Barra de filtros por status em scroll horizontal. Permite ao Empreiteiro filtrar a lista de construtoras por categoria de compliance. Building block do layout `empreiteiro-dashboard` — posicionado entre o Hero card e a Lista compacta.

## Props

```typescript
interface FilterPillsProps {
  pills: Array<{
    id: string;
    label: string;        // "Todas" | "Atencao" | "OK"
    count?: number;       // contador inline opcional
    statusHint?: 'neutral' | 'attention' | 'ok'; // colorway hint
  }>;
  activePillId: string;
  onPillSelect: (id: string) => void;
  ariaLabel?: string;     // default "Filtrar por status"
}
```

`statusHint` mapeia o colorway semantico da pill sem prescrever logica de filtro — a logica de filtragem e responsabilidade do consumer.

## Anatomy

```
┌────────────────────────────────────────────────────────────┐
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │    Todas     │  │  Atencao (2) │  │    OK (3)    │ ··· │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│   active (dark)      attention tint     success tint       │
└────────────────────────────────────────────────────────────┘
```

- **Container:** row horizontal, `overflow-x: auto`, gap `var(--space-2)`, padding `0 var(--space-4)`, sem scrollbar visivel (scrollbar-width: none)
- **Cada pill:** padding `var(--space-1) var(--space-3)`, border-radius `var(--radius-pill)`, display inline-flex, align-items center, gap `var(--space-1)` entre label e count
- **Active state:** bg `var(--text-primary)` + cor de texto `var(--text-inverse)`
- **Inactive neutral:** bg `var(--surface-secondary)` + cor de texto `var(--text-secondary)`
- **Count badge inline:** `opacity: 0.6` sobre a cor de texto vigente (ativa ou inativa)

## Token application

### Active pill
- Background: `var(--text-primary)` (#212529 em light mode)
- Text: `var(--text-inverse)` (#FFFFFF)
- Border radius: `var(--radius-pill)` (9999px — reserved para chips e badges de identidade per tokens.md)

### Inactive pill — neutral
- Background: `var(--surface-secondary)` (#F8F9FA)
- Text: `var(--text-secondary)` (#6C757D)

### Inactive pill — statusHint: 'attention'
- Background: `var(--status-warning-bg)` (#FFF6DD)
- Text: `var(--status-warning-fg)` (#8A6300)

### Inactive pill — statusHint: 'ok'
- Background: `var(--status-success-bg)` (#E5F2EA)
- Text: `var(--status-success-fg)` (#1A6B34)

### Count badge
- `opacity: 0.6` (sobre a cor de texto vigente)
- Font: `var(--text-caption)` (12px Plus Jakarta 400)

### Container
- Padding horizontal: `var(--space-4)` (16px, alinhado com `.ed-hero` e `.ed-list-title`)
- Gap entre pills: `var(--space-2)` (8px)

## Markup pattern

```tsx
<div
  className="filter-pills"
  role="tablist"
  aria-label={ariaLabel ?? "Filtrar por status"}
>
  {pills.map((pill) => (
    <button
      key={pill.id}
      className="filter-pill"
      role="tab"
      aria-selected={pill.id === activePillId}
      data-status-hint={pill.statusHint ?? 'neutral'}
      data-active={pill.id === activePillId ? 'true' : undefined}
      onClick={() => onPillSelect(pill.id)}
    >
      <span className="fp-label">{pill.label}</span>
      {pill.count !== undefined && (
        <span className="fp-count" aria-hidden="true">
          {pill.count}
        </span>
      )}
    </button>
  ))}
</div>
```

O count e `aria-hidden="true"` porque a contagem esta incluida no contexto da label acessivel do `tablist` — screen readers nao precisam de redundancia numerica inline.

## CSS

```css
.filter-pills {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: var(--space-2);
  padding: 0 var(--space-4);
  overflow-x: auto;
  scrollbar-width: none;          /* Firefox */
  -ms-overflow-style: none;       /* IE 11 */
}
.filter-pills::-webkit-scrollbar {
  display: none;                  /* Chrome / Safari / Edge */
}

.filter-pill {
  display: inline-flex;
  align-items: center;
  gap: var(--space-1);
  padding: var(--space-1) var(--space-3);
  border-radius: var(--radius-pill);
  border: none;
  cursor: pointer;
  white-space: nowrap;
  flex-shrink: 0;
  font-size: var(--text-ui);      /* 13px Plus Jakarta 500 */
  font-family: var(--font-sans);
  font-weight: 500;
  transition: background var(--dur-fast) var(--ease-out),
              color      var(--dur-fast) var(--ease-out);

  /* inactive neutral default */
  background: var(--surface-secondary);
  color: var(--text-secondary);
}

/* statusHint colorways — inactive apenas */
.filter-pill:not([data-active])[data-status-hint="attention"] {
  background: var(--status-warning-bg);
  color: var(--status-warning-fg);
}
.filter-pill:not([data-active])[data-status-hint="ok"] {
  background: var(--status-success-bg);
  color: var(--status-success-fg);
}

/* active state — sobrescreve qualquer statusHint */
.filter-pill[data-active="true"] {
  background: var(--text-primary);
  color: var(--text-inverse);
}

.fp-count {
  opacity: 0.6;
  font-size: var(--text-caption); /* 12px */
}

/* focus ring — consistente com botoes do sistema */
.filter-pill:focus-visible {
  outline: none;
  box-shadow: var(--focus-ring);
}
```

## Behaviors

### Teclado (ARIA tablist pattern)

- Teclas `ArrowLeft` / `ArrowRight` navegam entre pills sem ativar filtro (roving tabindex)
- `Enter` ou `Space` confirma selecao e dispara `onPillSelect`
- A pill ativa recebe `tabindex="0"`; demais recebem `tabindex="-1"`
- Wrap: `ArrowRight` na ultima pill vai para a primeira; `ArrowLeft` na primeira vai para a ultima

```tsx
function handleKeyDown(e: React.KeyboardEvent, idx: number) {
  const len = pills.length;
  if (e.key === 'ArrowRight') {
    pills[(idx + 1) % len].ref.current?.focus();
  } else if (e.key === 'ArrowLeft') {
    pills[(idx - 1 + len) % len].ref.current?.focus();
  }
}
```

### Selecao

`onPillSelect(id)` e disparado no `click` e no `keydown` Enter/Space. O estado `activePillId` e gerenciado externamente pelo consumer — FilterPills e stateless (controlled component).

### Scroll horizontal

Em mobile 375px com 3+ pills, o container scrola horizontalmente sem indicadores de scroll. A pill ativa pode ser trazida a vista via `scrollIntoView({ behavior: 'smooth', inline: 'nearest' })` apos mudanca de selecao.

## Accessibility

- `role="tablist"` no container, `role="tab"` em cada pill — padrao ARIA para navegacao por categoria
- `aria-selected="true"` na pill ativa, `aria-selected="false"` nas demais
- `aria-label` no tablist descreve a funcao do grupo ("Filtrar por status")
- Roving tabindex: apenas a pill ativa esta no tab order (`tabindex="0"`), demais em `tabindex="-1"` — evita que o usuario precise pressionar Tab N vezes para sair do grupo
- Color nunca e o unico canal de informacao — active state usa mudanca de background + cor de texto + `aria-selected`
- Reduced motion: transicoes desabilitadas via `prefers-reduced-motion`

```css
@media (prefers-reduced-motion: reduce) {
  .filter-pill {
    transition: none;
  }
}
```

## Onde aparece

| Layout | Posicao | Pills default |
|---|---|---|
| empreiteiro-dashboard (Tela 1) | Entre Hero card e Lista compacta | "Todas" (active) / "Atencao" / "OK" |

## Anti-patterns (evitar)

- NAO usar `role="radiogroup"` + `role="radio"` — pills de filtro sao abas de categoria, nao opcoes de formulario; `tablist/tab` comunica corretamente a semantica de navegacao
- NAO aplicar `--radius-2` (8px) em vez de `--radius-pill` — pills de filtro sao a excecao explicita reservada para `--radius-pill` per tokens.md §3
- NAO colocar icones Lucide dentro das pills — sufoca label em mobile e viola anti-pattern #6 do DS
- NAO gerenciar estado interno (activePillId) — FilterPills e controlled component; consumer gerencia o estado
