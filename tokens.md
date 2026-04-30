# TRC Design Tokens

> Canonical token names (v2 TRC brand grounding). Componentes em `components/` referenciam estes tokens.

## 1. Typography

### Stack
```css
--font-display: "Fraunces", "Lyon Display", Georgia, serif;
--font-sans:    "Plus Jakarta Sans", "Söhne", -apple-system, sans-serif;
--font-mono:    "JetBrains Mono", "Berkeley Mono", "SF Mono", monospace;
```

**Carregamento (Google Fonts):**
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght,SOFT@9..144,300..900,0..100&family=Plus+Jakarta+Sans:ital,wght@0,200..800;1,200..800&family=JetBrains+Mono:ital,wght@0,100..800;1,100..800&display=swap" rel="stylesheet">
```

### Decisoes criticas

**Fraunces (variable):** usado **apenas** para
- Hero KPIs (Tela 1 Engenheiro: "R$ 2.187.430")
- Decisao statement (Tela 1 Admin: "Liberar pagamento R$ 142.300?")
- Section headlines (h1 page-level)
- Nao usar em body, labels, secondary text

Variable axis exploration:
- `opsz` (optical size): 14-72 — usar 72 para hero, 24 para h1
- `wght` (weight): 400 (body display), 600 (hero), 800 (impacto raro)
- `SOFT` (axis customizado): 0-100 — usar 30-40 para soften corporate edge

**Plus Jakarta Sans:** UI inteiro (body, navigation, labels, buttons, captions). Weights utilizados: 400 (body), 500 (UI), 600 (emphasis), 700 (button labels), 800 (rare callout). Italics apenas para citacoes ou enfase semantica.

**JetBrains Mono:** isola apenas
- Valores monetarios (`R$ 142.300` em listas + tabelas)
- Identificadores tecnicos (CNPJ, CPF, numero medicao "042/2026")
- Datas inline em audit timeline ("27/abr 09:14")
- Code snippets (caso entrem no sistema admin)

Numerais sempre **tabular** (`font-feature-settings: "tnum"`) em mono para alinhamento vertical em tabelas.

### Type Scale
8pt-derived modular scale (1.25 ratio, anchored em 14px body):

| Token | Size / Line | Weight | Use |
|---|---|---|---|
| `--text-hero` | 56px / 64px | Fraunces 600, opsz 72, SOFT 30 | Hero KPI Engenheiro Tela 1 |
| `--text-display` | 36px / 44px | Fraunces 600, opsz 48 | Decision statement Admin Tela 1 |
| `--text-h1` | 28px / 36px | Fraunces 500, opsz 32 | Page title |
| `--text-h2` | 22px / 30px | Plus Jakarta 700 | Section title |
| `--text-h3` | 18px / 26px | Plus Jakarta 600 | Subsection |
| `--text-body` | 14px / 22px | Plus Jakarta 400 | Default body |
| `--text-body-lg` | 16px / 24px | Plus Jakarta 400 | Reading-heavy areas |
| `--text-ui` | 13px / 20px | Plus Jakarta 500 | Buttons, nav, controls |
| `--text-caption` | 12px / 18px | Plus Jakarta 400 | Captions, helper text |
| `--text-label` | 11px / 16px | Plus Jakarta 600, uppercase, letter-spacing 0.06em | Labels, table headers |
| `--text-mono-lg` | 16px / 24px | JetBrains Mono 500, tnum | R$ valores destaque |
| `--text-mono` | 13px / 20px | JetBrains Mono 400, tnum | R$ valores inline |
| `--text-mono-xs` | 11px / 16px | JetBrains Mono 400, tnum | Identificadores tecnicos |

**Letter-spacing convencao:**
- Hero/Display: `letter-spacing: -0.02em` (apertar para impacto)
- H1-H3: `letter-spacing: -0.01em`
- Body/UI: `letter-spacing: 0` (default)
- Labels uppercase: `letter-spacing: 0.06em` (respirar)
- Mono: `letter-spacing: -0.01em` (compactar levemente)

---

## 2. Color

### Filosofia
A paleta deriva **diretamente da marca TRC** (orange `#DB592A` + blue `#2563B5` do logo) com refinamentos para uso em interface: status semaforo herda os tokens da app RN TRC (Bootstrap-derived) com **dessaturacao em backgrounds** para nao gritar em dashboards densos. Light + Dark themes sao first-class. **Nenhum purple/violet. Nenhum gradiente roxo.** Backgrounds preservam `#FFFFFF` por **continuidade de marca** com a app RN existente, mas surface secundaria `#F8F9FA` cria profundidade sem ruido.

### Brand colors (light + dark mode)

```css
/* TRC primary brand — laranja institucional (do logo + RN app) */
--brand-primary:        #DB592A;  /* light mode primary CTA, brand mark */
--brand-primary-hover:  #C24E24;
--brand-primary-pressed:#A8431E;
--brand-primary-soft:   #FBEEE5;  /* tinted background */
--brand-primary-line:   #DB592A;  /* border accent */
--brand-primary-deeper: #8E3A1A;  /* S162 added — emphasis em sub-brand, nao usado em Phase 1 */

/* TRC secondary brand — azul institucional (do logo) */
--brand-secondary:        #2563B5;
--brand-secondary-hover:  #1F549A;
--brand-secondary-pressed:#194580;
--brand-secondary-soft:   #E5EFF9;
--brand-secondary-line:   #2563B5;

/* Logo dual-shape (decorative usage only — splash, brand marks) */
--logo-orange: #DB592A;
--logo-blue:   #2563B5;
```

### Light theme — full token map
```css
:root,
[data-theme="light"] {
  /* Surfaces */
  --surface-bg:        #FFFFFF;  /* page background — TRC RN convention */
  --surface-secondary: #F8F9FA;  /* sections, panels, hero containers */
  --surface-tertiary:  #F1F3F5;  /* nested panels, table headers */
  --surface-elevated:  #FFFFFF;  /* cards over secondary */

  /* Borders & dividers */
  --border-default: #DEE2E6;     /* default 1px borders */
  --border-strong:  #CED4DA;     /* dividers, table cell borders */
  --border-subtle:  #E9ECEF;     /* hairlines */

  /* Text */
  --text-primary:   #212529;     /* default body text */
  --text-secondary: #6C757D;     /* muted, captions, secondary actions */
  --text-tertiary:  #ADB5BD;     /* placeholder, disabled */
  --text-inverse:   #FFFFFF;     /* on dark surfaces */
  --text-brand:     #DB592A;     /* highlighted brand emphasis (rare) */
  --text-link:      #2563B5;     /* hyperlinks, secondary CTA text */

  /* Brand interactive */
  --action-primary-bg:    var(--brand-primary);
  --action-primary-fg:    #FFFFFF;
  --action-primary-hover: var(--brand-primary-hover);

  --action-secondary-bg:  transparent;
  --action-secondary-fg:  var(--brand-secondary);
  --action-secondary-bd:  var(--border-default);
  --action-secondary-hover-bg: var(--brand-secondary-soft);

  /* Status semaphore — TRC RN derived, dessaturated backgrounds */
  --status-error-bg:     #FBEAEC;
  --status-error-fg:     #A02834;
  --status-error-line:   #DC3545;
  --status-error-strong: #842029;

  --status-success-bg:     #E5F2EA;
  --status-success-fg:     #1A6B34;
  --status-success-line:   #28A745;
  --status-success-strong: #14532D;

  --status-warning-bg:     #FFF6DD;
  --status-warning-fg:     #8A6300;
  --status-warning-line:   #FFC107;
  --status-warning-strong: #5C4200;

  --status-info-bg:     #DEF2F5;
  --status-info-fg:     #0F5C6B;
  --status-info-line:   #17A2B8;
  --status-info-strong: #08384A;

  --status-inactive-bg:   #F1F3F5;
  --status-inactive-fg:   #6C757D;
  --status-inactive-line: #ADB5BD;
}
```

### Dark theme — full token map
```css
[data-theme="dark"] {
  /* Surfaces — Material Design dark levels (RN convention) */
  --surface-bg:        #121212;
  --surface-secondary: #1E1E1E;
  --surface-tertiary:  #2A2A2A;
  --surface-elevated:  #242424;

  /* Borders & dividers */
  --border-default: #343A40;
  --border-strong:  #495057;
  --border-subtle:  #2A2F33;

  /* Text */
  --text-primary:   #FFFFFF;
  --text-secondary: #ADB5BD;
  --text-tertiary:  #6C757D;
  --text-inverse:   #121212;
  --text-brand:     #FF6B35;
  --text-link:      #4299E1;

  /* Brand interactive — vibrancy boost para dark */
  --brand-primary:        #FF6B35;
  --brand-primary-hover:  #FF8657;
  --brand-primary-pressed:#E55A2B;
  --brand-primary-soft:   rgba(255, 107, 53, 0.12);

  --brand-secondary:        #4299E1;
  --brand-secondary-hover:  #5FAEEA;
  --brand-secondary-pressed:#3182CE;
  --brand-secondary-soft:   rgba(66, 153, 225, 0.14);

  --action-primary-bg:    var(--brand-primary);
  --action-primary-fg:    #121212;
  --action-primary-hover: var(--brand-primary-hover);

  --action-secondary-bg:  transparent;
  --action-secondary-fg:  var(--brand-secondary);
  --action-secondary-bd:  var(--border-default);
  --action-secondary-hover-bg: var(--brand-secondary-soft);

  /* Status semaphore — dark mode (RN tokens) */
  --status-error-bg:     rgba(245, 101, 101, 0.14);
  --status-error-fg:     #FCA5A5;
  --status-error-line:   #F56565;
  --status-error-strong: #FECACA;

  --status-success-bg:     rgba(72, 187, 120, 0.14);
  --status-success-fg:     #86EFAC;
  --status-success-line:   #48BB78;
  --status-success-strong: #BBF7D0;

  --status-warning-bg:     rgba(237, 137, 54, 0.14);
  --status-warning-fg:     #FBD38D;
  --status-warning-line:   #ED8936;
  --status-warning-strong: #FED7AA;

  --status-info-bg:     rgba(66, 153, 225, 0.14);
  --status-info-fg:     #93C5FD;
  --status-info-line:   #4299E1;
  --status-info-strong: #BFDBFE;

  --status-inactive-bg:   #2A2A2A;
  --status-inactive-fg:   #ADB5BD;
  --status-inactive-line: #6C757D;
}
```

### Tema switching strategy

**Phase 1:** light theme only, `[data-theme="light"]` aplicado em `<html>` ou `<body>`.

**Phase 2:** trigger toggle nas 3 PoVs:
- Engenheiro/Admin: toggle manual em settings + `prefers-color-scheme` respect
- Empreiteiro mobile: `prefers-color-scheme` automatic (segue OS)

**Implementation pattern (Tailwind + React):**
```tsx
// app root
<html data-theme={theme}>
  <body className="bg-surface-bg text-text-primary">
    {children}
  </body>
</html>

// theme switcher
const [theme, setTheme] = useState<"light" | "dark">("light");
useEffect(() => {
  const mq = window.matchMedia("(prefers-color-scheme: dark)");
  setTheme(mq.matches ? "dark" : "light");
}, []);
```

### Status semaphore mapping (semantic -> token family)

A nomenclatura semantica `ok | warn | block | inactive | info` mapeia para os tokens TRC:
- **ok** -> `--status-success-*`
- **warn** -> `--status-warning-*`
- **block** -> `--status-error-*`
- **inactive** -> `--status-inactive-*`
- **info** -> `--status-info-*`

### Interaction states
```css
:root {
  /* Focus ring — TRC brand orange para discoverability */
  --focus-ring:        0 0 0 3px rgba(219, 89, 42, 0.22);
  --focus-ring-strong: 0 0 0 3px rgba(219, 89, 42, 0.45);

  /* Subtle overlays */
  --hover-overlay-light:   rgba(33, 37, 41, 0.04);
  --pressed-overlay-light: rgba(33, 37, 41, 0.08);
  --hover-overlay-dark:    rgba(255, 255, 255, 0.06);
  --pressed-overlay-dark:  rgba(255, 255, 255, 0.10);

  --selection-bg-light: rgba(219, 89, 42, 0.18);
  --selection-bg-dark:  rgba(255, 107, 53, 0.22);
}
```

---

## 3. Spatial System

### Base unit & scale
**8pt grid** com escala restrita (nao exponencial Tailwind padrao, mais **arquitetonica**):

```css
:root {
  --space-0: 0;
  --space-1: 4px;     /* hairline padding */
  --space-2: 8px;     /* tight inline */
  --space-3: 12px;    /* compact */
  --space-4: 16px;    /* default body padding */
  --space-5: 24px;    /* section spacing */
  --space-6: 32px;    /* major separator */
  --space-7: 48px;    /* page padding desktop */
  --space-8: 64px;    /* hero spacing */
  --space-9: 96px;    /* generous editorial */
}
```

**Justificativa:** escala foge do default Tailwind (4/8/12/16/20/24/32/...) para forcar decisoes intencionais. 24->32 (e nao 24->28) cria respiro perceptivel em section spacing.

### Container widths
```css
:root {
  --container-narrow:  720px;   /* reading-focused (audit timeline detail) */
  --container-default: 1280px;  /* primary content */
  --container-wide:    1440px;  /* dashboard wide */
  --container-full:    1920px;  /* exception only */
}
```

### Mobile breakpoints
```css
:root {
  --bp-mobile:    375px;   /* iPhone SE / SE2 / SE3 base */
  --bp-mobile-lg: 414px;   /* iPhone Plus */
  --bp-tablet:    768px;
  --bp-desktop:   1024px;
  --bp-wide:      1280px;
  --bp-xwide:     1440px;  /* monitor wide — admin-decision-focus expansao */
}
```

### Layout columns (intent-driven)

Tokens para `max-width` de coluna central. Nomes encodam **proposito**, nao valor — ao gerar layouts, escolher pelo intent (decision / reading / spread / dashboard), nao pelo numero.

```css
:root {
  --column-fluid:     100%;    /* mobile, sidebar children, ou onde precisa ocupar tudo */
  --column-reading:   640px;   /* long-form text (prosa, articles, FAQs) */
  --column-decision:  720px;   /* decision-focus (admin-decision-focus, single-action pages) */
  --column-spread:    920px;   /* wide-screen layouts que precisam expandir sem virar dashboard */
  --column-dashboard: 1280px;  /* dashboards explicitos (Tela 2 admin, list-views) */
}
```

**Quando usar cada token:**

| Token | Uso correto | Anti-uso |
|---|---|---|
| `--column-fluid` | Mobile, layouts dentro de sidebar, modal compacto | Pagina main em desktop (vira "stretched body" sem foco) |
| `--column-reading` | Tela "Termos de uso", "Sobre", FAQ | Decision-focus (estreito demais para DualValueCard) |
| `--column-decision` | admin-decision-focus, "Editar perfil" | Dashboard explicito (vira fragmentado) |
| `--column-spread` | admin-decision-focus em wide >=1440px, empreiteiro-dashboard wide | Dashboards (use `--column-dashboard` ou nada) |
| `--column-dashboard` | Tela 2 admin (auditoria multi-medicao), Tela 3 admin (kanban obras) | Decision-focus (perde foco) |

### Container query breakpoints (componentes)

Para componentes que usam `container-type: inline-size`, breakpoints **separados** dos viewport (porque componente reage a largura DELE, nao da viewport).

```css
:root {
  --cb-compact:   280px;   /* container compacto (sidebar children, list items) */
  --cb-medium:    480px;   /* container medio (modal, hero card mobile) */
  --cb-wide:      720px;   /* container amplo (main content, hero wide) */
}
```

**Por que separados de `--bp-*`:** viewport breakpoints respondem a TELA do usuario; container breakpoints respondem ao CONTAINER do componente. Mesmo componente (ex: AISuggestionInline) pode estar em viewport desktop 1920px mas container sidebar 220px — viewport diz "wide", container diz "compact". Componente deve obedecer container.

### Border radius (restrita)
```css
:root {
  --radius-1:    4px;     /* tight, inputs */
  --radius-2:    8px;     /* cards */
  --radius-3:    12px;    /* modals, hero cards */
  --radius-4:    16px;    /* iPhone phone-frame mockup */
  --radius-pill: 9999px;  /* RESERVED — only filter pills, never buttons */
}
```

**Anti-AI:** `--radius-pill` (9999px) **nao usar em buttons normais** (sintoma cookie-cutter). Botoes default = `--radius-2`.

---

## 4. Depth & Elevation

### Filosofia
Sombras devem evocar **camadas de calque** sobre papel arquitetonico, nao dropshadow SaaS-genericas. Pesos e direcao consistentes (luz 145deg, top-left subtle), spread minimo, opacity baixa.

```css
:root {
  /* Layered like tracing paper */
  --elev-0: none;
  --elev-1: 0 1px 0 rgba(26, 22, 17, 0.04),
            0 1px 2px rgba(26, 22, 17, 0.06);
  --elev-2: 0 1px 0 rgba(26, 22, 17, 0.05),
            0 2px 4px rgba(26, 22, 17, 0.04),
            0 8px 16px -8px rgba(26, 22, 17, 0.08);
  --elev-3: 0 1px 0 rgba(26, 22, 17, 0.06),
            0 4px 8px rgba(26, 22, 17, 0.05),
            0 16px 32px -12px rgba(26, 22, 17, 0.10);
  --elev-4: 0 1px 0 rgba(26, 22, 17, 0.08),
            0 8px 16px rgba(26, 22, 17, 0.06),
            0 24px 48px -16px rgba(26, 22, 17, 0.14);
  --elev-modal: 0 24px 48px -8px rgba(26, 22, 17, 0.20),
                0 0 0 1px rgba(26, 22, 17, 0.04);

  /* Inset for "pressed paper" feel — used in selected items */
  --elev-inset: inset 0 0 0 1px rgba(61, 77, 126, 0.22),
                inset 0 1px 0 rgba(255, 255, 255, 0.4);
}
```

### Z-index scale (semantica explicita)
```css
:root {
  --z-base:     0;
  --z-elevated: 10;   /* cards levantados */
  --z-sticky:   20;   /* nav, headers */
  --z-overlay:  30;   /* dropdowns, popovers */
  --z-modal-bg: 40;
  --z-modal:    50;
  --z-toast:    60;
  --z-tooltip:  70;
}
```

---

## 5. Animations & Microinteractions

### Filosofia
Engineering precision over playful bounce. Tudo move-se com cubic-bezier de **decisao confiante**, nao spring-based. Durations curtos (180-320ms), sem looping decorativo.

### Easings
```css
:root {
  /* Default — engineering decisive */
  --ease-out:    cubic-bezier(0.16, 1, 0.3, 1);
  --ease-in-out: cubic-bezier(0.65, 0, 0.35, 1);
  --ease-in:     cubic-bezier(0.7, 0, 1, 0.7);
  /* Subtle paper feel — content arriving */
  --ease-paper:  cubic-bezier(0.22, 1, 0.36, 1);
  /* NEVER use elastic / overshoot easings — toy-feel */
}
```

### Durations
```css
:root {
  --dur-instant: 80ms;   /* hover overlay fade */
  --dur-fast:    160ms;  /* button press, focus ring */
  --dur-base:    240ms;  /* card state change, dropdown open */
  --dur-slow:    400ms;  /* modal entrance, page transition */
  --dur-paper:   600ms;  /* hero KPI counter rolling, decision reveal */
}
```

### Key moments

#### Page load (hero KPI)
```css
.hero-kpi {
  opacity: 0;
  transform: translateY(8px);
  animation: hero-arrive var(--dur-paper) var(--ease-paper) 100ms forwards;
}
.hero-secondary {
  animation-delay: 240ms;
}
@keyframes hero-arrive {
  to { opacity: 1; transform: translateY(0); }
}
```
Stagger: hero (100ms) -> 4 secondaries (200ms total, staggered 50ms entre).

#### Number counter (R$ 2.187.430)
Usar Motion (Framer Motion) com `useSpring`:
```tsx
import { motion, useSpring } from "framer-motion";
const value = useSpring(0, { damping: 20, stiffness: 60 });
// counter sobe de 0 ate target em ~1200ms ease-out
```
**Apenas no first load** — nao re-animar on filter change (irritante).

#### Hover (cards interativos)
```css
.card-interactive {
  transition: box-shadow var(--dur-base) var(--ease-out),
              transform   var(--dur-base) var(--ease-out);
}
.card-interactive:hover {
  box-shadow: var(--elev-3);
  transform: translateY(-1px); /* lift sutil, nao 3D-flashy */
}
```

#### Status change (compliance verde -> vermelho)
Ao receber update WebSocket "CND vencida":
```css
.status-changed {
  animation: status-pulse 400ms var(--ease-out);
}
@keyframes status-pulse {
  0%   { box-shadow: 0 0 0 0  var(--status-error-line); }
  100% { box-shadow: 0 0 0 6px transparent; }
}
```

#### AI Suggestion arrive (Foundry Agent inline)
```tsx
<motion.div
  initial={{ opacity: 0, x: -8 }}
  animate={{ opacity: 1, x: 0 }}
  transition={{ duration: 0.32, ease: [0.16, 1, 0.3, 1] }}
>
  ⚡ SMS+chat 87%
</motion.div>
```
+ pulse subtle no icon (1x ao chegar, sem looping):
```css
@keyframes ai-icon-pulse {
  0%   { transform: scale(1); }
  50%  { transform: scale(1.12); }
  100% { transform: scale(1); }
}
```

#### Modal entrance
```css
.modal {
  animation: modal-arrive var(--dur-slow) var(--ease-paper);
}
@keyframes modal-arrive {
  from { opacity: 0; transform: scale(0.96) translateY(8px); }
  to   { opacity: 1; transform: scale(1) translateY(0); }
}
.modal-backdrop {
  animation: backdrop-fade var(--dur-base) var(--ease-out);
}
```

#### Notification arrive
```tsx
<motion.div
  initial={{ opacity: 0, y: -12, scale: 0.98 }}
  animate={{ opacity: 1, y: 0, scale: 1 }}
  exit={{ opacity: 0, y: -8, scale: 0.98 }}
  transition={{ type: "tween", duration: 0.32, ease: [0.16, 1, 0.3, 1] }}
/>
```

**Reduced motion:**
```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## Apendice — Tailwind Config Extension

Para integracao Bolt + shadcn/ui (defaults), aqui esta o extend pronto:

```typescript
// tailwind.config.ts
import type { Config } from "tailwindcss";

export default {
  content: ["./src/**/*.{ts,tsx}"],
  theme: {
    extend: {
      fontFamily: {
        display: ['"Fraunces"', '"Lyon Display"', "Georgia", "serif"],
        sans:    ['"Plus Jakarta Sans"', '"Söhne"', "-apple-system", "sans-serif"],
        mono:    ['"JetBrains Mono"', '"Berkeley Mono"', "SF Mono", "monospace"],
      },
      fontSize: {
        hero:      ["56px", { lineHeight: "64px", letterSpacing: "-0.02em" }],
        display:   ["36px", { lineHeight: "44px", letterSpacing: "-0.02em" }],
        h1:        ["28px", { lineHeight: "36px", letterSpacing: "-0.01em" }],
        h2:        ["22px", { lineHeight: "30px", letterSpacing: "-0.01em" }],
        h3:        ["18px", { lineHeight: "26px" }],
        body:      ["14px", { lineHeight: "22px" }],
        "body-lg": ["16px", { lineHeight: "24px" }],
        ui:        ["13px", { lineHeight: "20px" }],
        caption:   ["12px", { lineHeight: "18px" }],
        label:     ["11px", { lineHeight: "16px", letterSpacing: "0.06em" }],
      },
      colors: {
        // TRC primary brand (orange)
        brand: {
          DEFAULT: "#DB592A", hover: "#C24E24", pressed: "#A8431E",
          soft:    "#FBEEE5", dark:  "#FF6B35",
        },
        // TRC secondary brand (blue)
        accent: {
          DEFAULT: "#2563B5", hover: "#1F549A", pressed: "#194580",
          soft:    "#E5EFF9", dark:  "#4299E1",
        },
        // Surfaces (light mode)
        surface: {
          DEFAULT: "#FFFFFF", secondary: "#F8F9FA",
          tertiary:"#F1F3F5", elevated:  "#FFFFFF",
        },
        // Borders (light mode)
        border: {
          DEFAULT: "#DEE2E6", strong: "#CED4DA", subtle: "#E9ECEF",
        },
        // Text (light mode)
        text: {
          primary:   "#212529", secondary: "#6C757D", tertiary: "#ADB5BD",
          inverse:   "#FFFFFF", brand:     "#DB592A", link:     "#2563B5",
        },
        // Status semaphore (dessaturated bg)
        status: {
          "error-bg":     "#FBEAEC", "error-fg":    "#A02834", "error-line":    "#DC3545", "error-strong":   "#842029",
          "success-bg":   "#E5F2EA", "success-fg":  "#1A6B34", "success-line":  "#28A745", "success-strong": "#14532D",
          "warning-bg":   "#FFF6DD", "warning-fg":  "#8A6300", "warning-line":  "#FFC107", "warning-strong": "#5C4200",
          "info-bg":      "#DEF2F5", "info-fg":     "#0F5C6B", "info-line":     "#17A2B8", "info-strong":    "#08384A",
          "inactive-bg":  "#F1F3F5", "inactive-fg": "#6C757D", "inactive-line": "#ADB5BD",
        },
      },
      spacing: {
        "1": "4px",  "2": "8px",  "3": "12px",
        "4": "16px", "5": "24px", "6": "32px",
        "7": "48px", "8": "64px", "9": "96px",
      },
      borderRadius: {
        "1": "4px", "2": "8px", "3": "12px", "4": "16px",
      },
      boxShadow: {
        "elev-1":     "0 1px 0 rgba(26,22,17,0.04), 0 1px 2px rgba(26,22,17,0.06)",
        "elev-2":     "0 1px 0 rgba(26,22,17,0.05), 0 2px 4px rgba(26,22,17,0.04), 0 8px 16px -8px rgba(26,22,17,0.08)",
        "elev-3":     "0 1px 0 rgba(26,22,17,0.06), 0 4px 8px rgba(26,22,17,0.05), 0 16px 32px -12px rgba(26,22,17,0.10)",
        "elev-4":     "0 1px 0 rgba(26,22,17,0.08), 0 8px 16px rgba(26,22,17,0.06), 0 24px 48px -16px rgba(26,22,17,0.14)",
        "elev-modal": "0 24px 48px -8px rgba(26,22,17,0.20), 0 0 0 1px rgba(26,22,17,0.04)",
        "elev-inset": "inset 0 0 0 1px rgba(61,77,126,0.22), inset 0 1px 0 rgba(255,255,255,0.4)",
      },
      transitionTimingFunction: {
        "out":    "cubic-bezier(0.16, 1, 0.3, 1)",
        "in-out": "cubic-bezier(0.65, 0, 0.35, 1)",
        "paper":  "cubic-bezier(0.22, 1, 0.36, 1)",
      },
      transitionDuration: {
        "instant": "80ms", "fast": "160ms", "base": "240ms",
        "slow":    "400ms", "paper": "600ms",
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
} satisfies Config;
```
