# StatusDot

Indicador visual minimo de status. Building block de `ComplianceTrafficLight`, usado tambem em listas, badges, e timeline indicators.

## Props

```typescript
type DotProps = {
  status: "ok" | "warn" | "block" | "inactive";
  size: 6 | 8 | 12;
  pulse?: boolean; // pulse animation Phase 2 if real-time updates
};
```

Status semantica → token:
- `ok` → `--status-success-line`
- `warn` → `--status-warning-line`
- `block` → `--status-error-line`
- `inactive` → `--status-inactive-line`

## Implementation

```css
.status-dot {
  display: inline-block;
  border-radius: 50%;
  flex-shrink: 0;
}
.status-dot[data-status="ok"]       { background: var(--status-success-line); }
.status-dot[data-status="warn"]     { background: var(--status-warning-line); }
.status-dot[data-status="block"]    { background: var(--status-error-line); }
.status-dot[data-status="inactive"] { background: var(--status-inactive-line); }

.status-dot[data-size="6"]  { width: 6px;  height: 6px; }
.status-dot[data-size="8"]  { width: 8px;  height: 8px; }
.status-dot[data-size="12"] { width: 12px; height: 12px; }
```

## Pulse (real-time updates Phase 2)

```css
.status-dot[data-pulse="true"] {
  animation: dot-pulse 1.6s var(--ease-out) infinite;
}
@keyframes dot-pulse {
  0%, 100% { box-shadow: 0 0 0 0   currentColor; }
  50%      { box-shadow: 0 0 0 4px rgba(0, 0, 0, 0); }
}
```

**Usar pulse com moderacao:** apenas quando o status acabou de mudar (apply via timeout 3-5s), nao continuamente.

## Markup pattern

```tsx
<span
  className="status-dot"
  data-status={status}
  data-size={size}
  data-pulse={pulse ? "true" : undefined}
  role="img"
  aria-label={statusLabel(status)}
/>
```

## Accessibility

- `role="img"` + `aria-label` traduzindo status para texto
- Cor **nunca** como unico canal de informacao — sempre acompanhado de label textual no contexto pai
- Reduced motion: pulse desabilitado via media query
