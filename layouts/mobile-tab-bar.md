# Mobile Tab Bar

Navegacao primaria do PoV Empreiteiro em viewport mobile (375px iPhone). Estrutura **fixa bottom** com 4 itens. **NAO usar hamburger menu** ou drawer-overlay como navegacao primaria — anti-pattern explicito (afasta do tom enterprise auditavel; consome 1 tap a mais para qualquer transicao).

## Estrutura

4 itens fixos, nessa ordem (esquerda → direita):

| Posicao | Item | Icone Lucide | Rota | Badge? |
|---|---|---|---|---|
| 1 | Inicio | `Home` | `/empreiteiro/` | nao |
| 2 | Documentos | `FileText` | `/empreiteiro/documentos` | sim — count de docs pendentes (numero) |
| 3 | Alertas | `Bell` | `/empreiteiro/alertas` | sim — dot vermelho se ha urgentes; count se >0 |
| 4 | Perfil | `User` | `/empreiteiro/perfil` | nao |

## Token application

- **Container:** position fixed bottom, full-width 100%, `var(--surface-elevated)` background
- **Border-top:** 1px solid `var(--border-subtle)` — separa visualmente do scroll content sem sombra (sombra inverteria acima e violaria sistema de elevacao)
- **Height:** 56px (intent area iOS HIG-aligned, comoda para tap em todas as 4 posicoes em iPhones com home indicator)
- **Safe area inset:** `padding-bottom: env(safe-area-inset-bottom)` para iPhones com home indicator (X+)
- **Z-index:** `var(--z-sticky)` — acima do scroll content, abaixo de modal e slide-over panels
- **Item layout:** flex row, equal-spaced (4 cols), align center
- **Icon size:** 20px stroke-width 1.5 (consistencia com Lucide convention do sistema)
- **Icon color (inativo):** `var(--text-tertiary)`
- **Icon color (ativo):** `var(--brand-primary)` — laranja TRC sinaliza "voce esta aqui"
- **Label font:** `--text-caption` Plus Jakarta 500 (10-11px)
- **Label color (inativo):** `var(--text-tertiary)`
- **Label color (ativo):** `var(--brand-primary)` (mesma do icone — para garantir contraste AA tanto sobre branco quanto sobre superficie elevada)
- **Active indicator:** **NAO** usar barra superior, underline, ou pill background — apenas a cor mudada (Lucide stroke + label) sinaliza ativo. Manter visualmente quieto (anti-pattern: tab bar com bolha animada que segue o tap = toy-product aesthetic)

## Badge no icone

Badge apenas em `Documentos` e `Alertas`. Posicao: top-right do icone (offset `-4px right, -4px top`).

- **Documentos:** circulo `--brand-primary` 16×16px com numero branco. Aparece quando `count > 0`. Hide quando `count === 0`.
- **Alertas:** dot puro 8×8px `--status-error-line` quando ha urgentes (sem numero); circulo 16×16px `--brand-primary` com numero quando count > 1 e nao-urgente. Logica esquadrinhada na tela de notificacoes (Tela 3 Empreiteiro).

## Markup pattern

```tsx
<nav className="mobile-tab-bar" aria-label="Navegacao primaria">
  {tabs.map((tab) => {
    const isActive = currentRoute.startsWith(tab.route);
    return (
      <a
        key={tab.id}
        href={tab.route}
        className="tab-item"
        data-active={isActive ? "true" : undefined}
        aria-current={isActive ? "page" : undefined}
      >
        <span className="tab-icon-wrap">
          <Icon name={tab.icon} size={20} strokeWidth={1.5} />
          {tab.badge && (
            <span
              className="tab-badge"
              data-variant={tab.badge.variant}
              aria-label={tab.badge.ariaLabel}
            >
              {tab.badge.value}
            </span>
          )}
        </span>
        <span className="tab-label">{tab.label}</span>
      </a>
    );
  })}
</nav>
```

## CSS

```css
.mobile-tab-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  height: 56px;
  padding-bottom: env(safe-area-inset-bottom);
  display: flex;
  background: var(--surface-elevated);
  border-top: 1px solid var(--border-subtle);
  z-index: var(--z-sticky);
}

.tab-item {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 2px;
  text-decoration: none;
  color: var(--text-tertiary);
  transition: color 150ms var(--ease-out);
  -webkit-tap-highlight-color: transparent;
}

.tab-item[data-active="true"] {
  color: var(--brand-primary);
}

.tab-icon-wrap {
  position: relative;
  display: inline-flex;
}

.tab-badge {
  position: absolute;
  top: -4px;
  right: -4px;
  font-family: var(--font-mono);
  font-size: 9px;
  font-weight: 600;
  color: white;
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

.tab-badge[data-variant="count"] {
  background: var(--brand-primary);
  border-radius: var(--radius-pill);
  min-width: 16px;
  height: 16px;
  padding: 0 4px;
}

.tab-badge[data-variant="dot"] {
  background: var(--status-error-line);
  width: 8px;
  height: 8px;
  border-radius: var(--radius-pill);
  text-indent: -9999px; /* dot puro, sem numero visivel */
}

.tab-label {
  font-family: var(--font-ui);
  font-size: var(--text-caption);
  font-weight: 500;
  line-height: 1;
}
```

## Layout adjustments na pagina

Conteudo principal precisa de bottom-padding para nao ficar coberto pela tab bar:

```css
.empreiteiro-page-content {
  padding-bottom: calc(56px + env(safe-area-inset-bottom) + var(--space-4));
}
```

## Accessibility

- `<nav aria-label="Navegacao primaria">` reconhecido por screen readers
- `aria-current="page"` no link ativo
- Badge tem `aria-label` semantico (ex: "5 documentos pendentes", "Alertas urgentes pendentes")
- Tap target minimo 44×44px (height 56px / 4 items em viewport 375px = 93.75px de width por item, supera HIG)
- `-webkit-tap-highlight-color: transparent` evita flash cinza iOS default (anti generic-AI checklist alinhado)

## Anti-patterns (evitar)

- NAO usar hamburger ou drawer como navegacao primaria — afasta da imediacao auditavel TRC
- NAO usar mais de 4 itens (consenso UX mobile; 5+ obriga overflow drawer e quebra a regra "1 tap = 1 secao primaria")
- NAO animar transition entre tabs com bounce/spring — viola anti-pattern #7
- NAO usar emoji nos icones — viola anti-pattern #6 (Lucide-only)
- NAO colocar a tab bar no topo (top-bar) — Empreiteiro PoV e mobile thumb-zone friendly, navegacao no bottom respeita ergonomia

## Phase 2 considerations

- **Active state animation:** considerar fade in/out de cor 150ms (ja aplicado), mas evitar slide indicator
- **Vibe haptic feedback:** Phase 2 com PWA + native bridge, vibrate(10ms) no tap (suportado em iOS 16.4+)
- **Theme dark:** auto-derivado de tokens `--surface-elevated`, `--text-tertiary`, `--brand-primary` que ja tem variants dark em `tokens.md`
