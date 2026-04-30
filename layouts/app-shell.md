# App Shell Layout

> **Validacao S163b:** spec criado a partir de canary HTML preview validado pre-push em `trc-platform/docs/assets/preview/admin-tela-1-v4-preview.html` (substituto #7 visual decision pre-validation).

Layout global do app web TRC: **sidebar de navegacao + header + main content**. Aplica a TODAS as paginas autenticadas (admin, empreiteiro, construtora). Define o **comportamento responsivo da sidebar** (drawer mobile / icon-rail tablet / persistent desktop) e o **density rhythm** dos elementos repetitivos da sidebar.

Este e o layout-pai. Layouts filhos (`admin-decision-focus`, `empreiteiro-dashboard`, `mobile-tab-bar`) renderizam dentro de `.app-shell > .app-main`.

## Estrutura visual (zoom out)

```
WIDE >=1024px (desktop + wide):                   TABLET 768-1023px:                          MOBILE <768px:
┌──────────────────────────────────────────┐    ┌──────────────────────────────────────┐    ┌────────────────────────────┐
│ [240px sidebar] │ Header (search + bell) │    │ [64] │ Header (search + bell)        │    │ ☰ Header (search + avatar) │
│  ◆ TRC          ├────────────────────────┤    │  ◆   ├───────────────────────────────┤    ├────────────────────────────┤
│  Compliance     │                        │    │  ▣   │                               │    │                            │
│  ─────────      │   .app-main            │    │  ⚐   │   .app-main                   │    │   .app-main                │
│  ▣ Obras        │                        │    │  ▤   │                               │    │                            │
│  ⚐ Empreiteiros │   (layout filho aqui)  │    │  ⚙   │   (layout filho aqui)         │    │   (layout filho aqui)      │
│  ▤ Medicoes ›   │                        │    │      │                               │    │                            │
│  ⚙ Auditoria    │                        │    │      │                               │    │   ┌─drawer (oculto)──┐     │
│                 │                        │    │      │                               │    │   │ TRC Compliance   │     │
│  Construtora X  │                        │    │      │                               │    │   │  ▣ Obras         │     │
│  CNPJ 12.345/   │                        │    │      │                               │    │   │  ⚐ Empreiteiros  │     │
└──────────────────────────────────────────┘    └──────────────────────────────────────┘    │   │  ...             │     │
                                                                                             │   └──────────────────┘     │
                                                                                             └────────────────────────────┘
```

## Viewport behavior (prescritivo)

| Viewport | Sidebar | Hamburger | Brand block | Item labels | Footer (Construtora info) |
|---|---|---|---|---|---|
| **Mobile (<768px)** | **Off-canvas drawer** — escondida via `transform: translateX(-100%)`, abre por tap-hamburger | **Visivel** no header (lado esquerdo) | Visivel quando drawer aberto (full) | Visivel quando drawer aberto | Visivel quando drawer aberto |
| **Tablet (768-1023px)** | **Icon-rail 64px** — persistent, soh icones | Oculta (sidebar persistent) | Soh logo `◆` (sem TRC text, sem tagline) | **Ocultos** (apenas icones) | Oculto |
| **Desktop (1024-1439px)** | **Persistent 240px** | Oculta | Full (`◆ TRC` + `Compliance Trabalhista` tagline inline) | Visiveis | Visivel |
| **Wide (>=1440px)** | **Persistent 240px** (igual desktop) | Oculta | Full | Visiveis | Visivel |

**Por que drawer mobile e nao icon-rail mobile:** mobile <412px viewport, sidebar 64px icon-rail consome 15%+ do width util. Conteudo principal (`admin-decision-focus`, `empreiteiro-dashboard`) ja e estreito — perder mais 15% degrada legibilidade. Drawer (full-width quando aberto, zero quando fechado) preserva 100% main content.

**Anti-pattern catched em S163b:** sidebar persistent em mobile `<412px viewport` ocupando 33% width forcou cards CTL em coluna ~80px com word-break vertical. Fix: drawer escondida + hamburger, NAO sidebar collapse parcial.

## Density rhythm da sidebar (prescritivo)

Esta secao foi catched em S163b — Bolt produziu rhythm "tight uncomfortable + ilha desconectada" porque design-system v3 nao prescrevia spacing. Especificacao agora **explicita**.

### Brand block

| Aspecto | Regra | Por que |
|---|---|---|
| Layout | `display: flex; align-items: center; gap: var(--space-2)` (8px). Logo + "TRC" + tagline em **linha horizontal**. | Stacked vertical (logo em cima, TRC + tagline embaixo) desperdica vertical real estate em uma area que precisa estar PROXIMA dos nav items para criar mapping mental "marca -> navegacao". |
| Tagline ("Compliance Trabalhista") | `font-size: var(--text-caption)` (12px), `color: var(--text-tertiary)`, `margin-top: 2px` da label TRC | Visualmente subordinada ao logo+TRC (olho processa "marca primaria" + "contexto secundario"). |
| Margin-bottom para nav | `var(--space-6)` (24px) | NAO usar `var(--space-8)` (48px) ou maior — gap excessivo cria desconexao "ilhas separadas". |
| Brand color | TRC label em `var(--brand-primary)` (laranja) | Vincula brand TRC ao logo. NAO usar text-primary preto/cinza (logo perde forca). |

### Nav items

| Aspecto | Regra | Anti-pattern |
|---|---|---|
| Gap entre items | `gap: 2px` (no `<ul>`) | `gap: 0` vira lista sem ritmo; `gap: >=4px` vira items "soltos sem agrupamento". |
| Padding por item | `padding: var(--space-2) var(--space-3)` (8px 12px) | Padding `<6px` vertical = tight uncomfortable; `>12px` = items "espacados demais como menu touch-mobile". |
| Icon size | 16px width × 16px height (Lucide stroke-width 1.5) | Icons `<14px` ilegiveis; `>20px` desbalanceiam vs label. |
| Icon-label gap | `var(--space-3)` (12px) interno do item via flex gap | `gap: <8px` vira "icon colado na label"; `>16px` vira "duas regioes sem relacao". |
| Item height total | ~32px (icon 16px + 8+8 padding) | Heights variaveis criam rhythm inconsistente; padronizar. |
| Active state | `background: rgba(brand-primary, 0.08)`, `color: var(--brand-primary)`, `font-weight: 600` + chevron `›` direita | NAO usar bg solido `var(--brand-primary)` (fica gritante na sidebar de uso continuo); 8% tint e o sweet spot. |

### Footer (Construtora info)

| Aspecto | Regra |
|---|---|
| Posicionamento | `margin-top: auto` (push to bottom) + `padding-top: var(--space-4)` + `border-top: 1px solid var(--border-subtle)` |
| Tipografia | `font-size: var(--text-caption)` (12px), `color: var(--text-tertiary)`, `line-height: 1.4` |
| Conteudo | Linha 1: nome da Construtora; Linha 2: CNPJ formatado |
| Visibilidade | Apenas em desktop+wide (oculto em tablet icon-rail e mobile drawer fechada) |

## Token application — surfaces

| Element | Token | Hex | Por que |
|---|---|---|---|
| `<body>`, `.app-main` | `--surface-bg` | `#FAFAF9` | Ground — luminance 98% warm-grey. Ver P12 surface palette. |
| `.app-sidebar` | `--surface-elevated` | `#FFFFFF` | Elevated chrome — value contrast com main = boundary natural sem border pesada. |
| `.app-header` | `--surface-elevated` | `#FFFFFF` | Mesmo principio do sidebar. |
| `.search-input` | `--surface-bg` | `#FAFAF9` | Recessed dentro do header (header e elevated, input e ground = "afundado") |

## Markup pattern

```tsx
<div className="app-shell" data-sidebar-open={drawerOpen ? "true" : "false"}>
  <aside className="app-sidebar" aria-label="Navegacao principal">
    <header className="sb-brand">
      <span className="sb-logo" aria-hidden>◆</span>
      <span className="sb-brand-text">TRC</span>
      <span className="sb-brand-tagline">Compliance Trabalhista</span>
    </header>
    <nav>
      <ul className="sb-nav" role="list">
        <li><a className="sb-item" href="/obras"><LucideHome size={16} /><span className="sb-label">Obras</span></a></li>
        <li><a className="sb-item" href="/empreiteiros"><LucideUsers size={16} /><span className="sb-label">Empreiteiros</span></a></li>
        <li><a className="sb-item is-active" href="/medicoes"><LucideFileText size={16} /><span className="sb-label">Medicoes</span><LucideChevronRight size={14} className="sb-active-arrow" aria-hidden /></a></li>
        <li><a className="sb-item" href="/auditoria"><LucideShield size={16} /><span className="sb-label">Auditoria</span></a></li>
      </ul>
    </nav>
    <footer className="sb-footer">
      <p>{construtora.nome}</p>
      <p>CNPJ {formatCNPJ(construtora.cnpj)}</p>
    </footer>
  </aside>

  <header className="app-header">
    <button
      className="app-hamburger"
      type="button"
      aria-label="Abrir navegacao"
      aria-expanded={drawerOpen}
      aria-controls="app-sidebar"
      onClick={() => setDrawerOpen(true)}
    >
      <LucideMenu size={20} />
    </button>
    <input
      className="app-search"
      type="search"
      placeholder="Buscar obras, empreiteiros, medicoes..."
      aria-label="Buscar"
    />
    <div className="app-header-actions">
      <button className="app-bell" aria-label="Notificacoes"><LucideBell size={18} /></button>
      <span className="app-avatar" aria-label={user.nome}>{user.iniciais}</span>
    </div>
  </header>

  <main className="app-main">{children}</main>

  {/* Mobile drawer overlay (apenas mobile) */}
  {drawerOpen && (
    <button
      type="button"
      className="app-drawer-backdrop"
      aria-label="Fechar navegacao"
      onClick={() => setDrawerOpen(false)}
    />
  )}
</div>
```

## CSS

```css
.app-shell {
  display: grid;
  grid-template-areas: "sidebar header" "sidebar main";
  grid-template-columns: 240px 1fr;
  grid-template-rows: 56px 1fr;
  min-height: 100vh;
  background: var(--surface-bg);
  position: relative;
}

/* Tablet 768-1023px: icon-rail 64px */
@media (max-width: 1023px) and (min-width: 768px) {
  .app-shell {
    grid-template-columns: 64px 1fr;
  }
  .sb-brand-text,
  .sb-brand-tagline,
  .sb-label,
  .sb-active-arrow,
  .sb-footer { display: none; }
  .sb-brand { justify-content: center; }
  .sb-item { justify-content: center; padding: var(--space-2); }
}

/* Mobile <768px: drawer */
@media (max-width: 767px) {
  .app-shell {
    grid-template-columns: 0 1fr;
  }
  .app-sidebar {
    position: fixed; top: 0; bottom: 0; left: 0;
    width: 240px;
    transform: translateX(-100%);
    transition: transform .25s var(--ease-out);
    z-index: var(--z-modal);
  }
  .app-shell[data-sidebar-open="true"] .app-sidebar {
    transform: translateX(0);
    box-shadow: 2px 0 12px rgba(0, 0, 0, 0.12);
  }
  .app-shell[data-sidebar-open="true"] .app-drawer-backdrop {
    position: fixed; inset: 0;
    background: rgba(0, 0, 0, 0.4);
    z-index: calc(var(--z-modal) - 1);
    border: none; cursor: pointer;
  }
  .app-hamburger { display: inline-flex; }
}

.app-sidebar {
  grid-area: sidebar;
  background: var(--surface-elevated);
  border-right: 1px solid var(--border-subtle);
  padding: var(--space-4) var(--space-3);
  display: flex; flex-direction: column;
  overflow-y: auto;
}

.sb-brand {
  display: flex; align-items: center;
  gap: var(--space-2);
  margin-bottom: var(--space-6);
  font-weight: 700;
  color: var(--brand-primary);
  font-size: var(--text-body);
  letter-spacing: 0.5px;
}
.sb-brand-tagline {
  font-size: var(--text-caption);
  color: var(--text-tertiary);
  font-weight: 400;
  letter-spacing: 0;
  margin-left: var(--space-1);
}

.sb-nav {
  list-style: none; padding: 0; margin: 0;
  display: grid; gap: 2px;
}
.sb-item {
  display: flex; align-items: center; gap: var(--space-3);
  padding: var(--space-2) var(--space-3);
  border-radius: var(--radius-2);
  color: var(--text-primary);
  font-size: var(--text-caption);
  text-decoration: none;
  transition: background .15s var(--ease-out);
}
.sb-item:hover { background: var(--surface-secondary); }
.sb-item.is-active {
  background: var(--brand-primary-soft);
  color: var(--brand-primary);
  font-weight: 600;
}
.sb-active-arrow { margin-left: auto; opacity: 0.7; }

.sb-footer {
  margin-top: auto;
  padding-top: var(--space-4);
  border-top: 1px solid var(--border-subtle);
  font-size: var(--text-caption);
  color: var(--text-tertiary);
  line-height: 1.4;
}
.sb-footer p { margin: 0; }

.app-header {
  grid-area: header;
  display: flex; align-items: center;
  gap: var(--space-3);
  padding: 0 var(--space-4);
  background: var(--surface-elevated);
  border-bottom: 1px solid var(--border-subtle);
}
.app-hamburger {
  display: none;
  background: none;
  border: 1px solid var(--border-subtle);
  border-radius: var(--radius-2);
  padding: 6px 10px;
  cursor: pointer;
  color: var(--text-primary);
}
.app-search {
  flex: 1; max-width: 480px;
  padding: 8px 12px;
  border: 1px solid var(--border-default);
  border-radius: var(--radius-2);
  background: var(--surface-bg);
  font-size: var(--text-caption);
  font-family: inherit;
}
.app-header-actions {
  margin-left: auto;
  display: flex; align-items: center; gap: var(--space-2);
}
.app-bell {
  background: var(--surface-secondary); border: none;
  width: 32px; height: 32px; border-radius: 50%;
  display: inline-flex; align-items: center; justify-content: center;
  cursor: pointer;
}
.app-avatar {
  width: 32px; height: 32px; border-radius: 50%;
  background: var(--brand-secondary-soft);
  color: var(--brand-secondary);
  font-weight: 600; font-size: var(--text-caption);
  display: inline-flex; align-items: center; justify-content: center;
}

.app-main {
  grid-area: main;
  overflow-y: auto;
  background: var(--surface-bg);
}
```

## Mobile drawer interaction

- **Open:** tap-hamburger -> `drawerOpen = true` -> sidebar slide-in 250ms + backdrop fade-in
- **Close:** (a) tap-backdrop, (b) tap qualquer `.sb-item` (navegacao implica fechar), (c) tecla ESC
- **Focus management:** ao abrir, focus no primeiro `.sb-item`. Ao fechar, focus retorna para `.app-hamburger`.
- **Scroll lock:** quando drawer aberta, `body { overflow: hidden }` para evitar scroll do background
- **Reduced motion:** transition desabilitada via `@media (prefers-reduced-motion: reduce)`

## Accessibility

- `<aside aria-label="Navegacao principal">` semantica explicita
- `.app-hamburger` com `aria-expanded` + `aria-controls` apontando para sidebar
- `.app-drawer-backdrop` e `<button>` (nao div) — focus + keyboard accessible
- Active item via `.is-active` class + `aria-current="page"` no `<a>`
- Tab order: hamburger -> search -> bell -> avatar -> main content -> sidebar items (quando aberta)
- Reduced motion respeita preferencia do usuario

## Anti-patterns (concept × forbidden × required substitute)

| Concept | Forbidden | Required substitute |
|---|---|---|
| Sidebar mobile | Persistent (qualquer width) ou collapsed parcial 64px | **Drawer off-canvas** + hamburger |
| Sidebar tablet | Persistent full 240px | **Icon-rail 64px** (icones soh) |
| Brand block layout | Logo + TRC + tagline stacked vertical | **Inline horizontal** (logo + TRC label + tagline subtle a direita) |
| Brand-to-nav gap | `>= var(--space-8)` (48px+) ou nenhum gap | **`var(--space-6)`** (24px) — separa sem desconectar |
| Nav item gap | `gap: 0` (lista sem rhythm) ou `gap: >=8px` (items soltos) | **`gap: 2px`** (rhythm coeso) |
| Active state | Background solido brand-primary (gritante) | **`brand-primary-soft` (8% tint)** + chevron arrow |
| Sidebar footer info | Visivel em mobile drawer collapsed (vaza brand info) | **Visivel apenas em desktop+wide**; tablet icon-rail e mobile-fechado oculto |
| Decoracao decorativa (gradients, padroes) | Em qualquer parte do shell | **Soh tokens** (`--surface-*`, `--border-*`); shell e chrome neutro, conteudo da personalidade |

## Phase 2 considerations

- **User menu dropdown:** clicar `.app-avatar` abre dropdown com "Perfil / Configuracoes / Sair"
- **Notificacoes panel:** clicar `.app-bell` abre panel lateral direito (variant do drawer mobile)
- **Search com sugestoes:** `.app-search` ganha autocomplete dropdown ao digitar
- **Workspace switcher:** se usuario gerencia multiplas Construtoras, switcher acima do nav
