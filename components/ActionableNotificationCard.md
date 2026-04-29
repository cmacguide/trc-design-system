# ActionableNotificationCard

Notification card com priority numerada e dual-value framing. Usado na Tela 3 do Empreiteiro (lista de notificacoes acionaveis), com priority 1/2/3 ordenando urgencia.

## Props

```typescript
type NotificationProps = {
  priority: 1 | 2 | 3;
  dualValue: DualValueCardProps;
  primaryCTA: string;
  secondaryCTA: string;
  countdown?: string;  // ex: "5 min" ou "7 dias"
};
```

## Token application

- **Numbered badge top-left:** 18×18px **square** (NOT circle, mais arquitetonico), bg semantic, color `var(--text-inverse)`, weight 700
  - priority=1 → bg `var(--status-error-line)`
  - priority=2 → bg `var(--status-warning-line)`
  - priority=3 → bg `var(--status-info-line)`
- **Layout:** vertical em mobile (stacked), horizontal em web (side-by-side)
- **Primary CTA:** full-width em mobile, half-width em web
- **Countdown text:** `--text-caption` color `var(--text-tertiary)`, top-right alinhado
- **Padding:** `var(--space-4)` mobile / `var(--space-5)` web
- **Background:** `var(--surface-elevated)`
- **Border:** 1.5px solid; cor matches priority badge
- **Border radius:** `var(--radius-3)`
- **Elevation:** `var(--elev-2)`

## Markup pattern

```tsx
<article
  className="actionable-notification-card"
  data-priority={priority}
>
  <span className="anc-badge" aria-label={`Prioridade ${priority}`}>
    {priority}
  </span>
  {countdown && (
    <span className="anc-countdown">{countdown}</span>
  )}
  <div className="anc-body">
    <DualValueCard {...dualValue} />
  </div>
  <footer className="anc-actions">
    <button className="btn-primary" onClick={dualValue.onPrimary}>
      {primaryCTA}
    </button>
    <button className="btn-secondary" onClick={dualValue.onSecondary}>
      {secondaryCTA}
    </button>
  </footer>
</article>
```

## Animation (mount + dismiss)

```tsx
import { motion } from "framer-motion";

<motion.div
  initial={{ opacity: 0, y: -12, scale: 0.98 }}
  animate={{ opacity: 1, y: 0, scale: 1 }}
  exit={{ opacity: 0, y: -8, scale: 0.98 }}
  transition={{ type: "tween", duration: 0.32, ease: [0.16, 1, 0.3, 1] }}
/>
```

## Accessibility

- `<article>` semantic
- Badge prioridade com `aria-label` traducao explicita
- Countdown anuncia tempo remanescente em texto, nao apenas visual
- Keyboard: Tab cycle entre primary/secondary CTAs em ordem visual
- Reduced motion: enter/exit instant
