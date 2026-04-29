# AISuggestionInline

Inline suggestion proveniente do **Foundry Agent** (sistema AI da TRC). Aparece como banda lateral subtle em listas/dashboards quando o agent identifica acao de remediacao ou insight relevante.

Estilo deve sinalizar "AI esta ajudando" sem virar showcase de AI (anti-pattern enterprise).

## Props

```typescript
type AISuggestionProps = {
  agentName: string;       // ex: "Foundry RemediationAdvisor"
  suggestion: string;      // texto curto da sugestao
  confidence: number;      // 0-1
  reasoning?: string;      // expandivel via <details>
  onApply: () => void;
};
```

## Token application

- **Background:** linear-gradient `var(--brand-secondary-soft)` to `transparent`, border-left 3px `var(--brand-secondary)`
- **Icon:** ⚡ (custom SVG via Lucide, **never emoji**) com `transform-origin: center` + animation `ai-icon-pulse` ao mount (1x, sem looping)
- **Confidence visualizado** como mini-bar 4px height x 40px width:
  ```css
  .confidence-bar {
    width: 40px;
    height: 4px;
    background: var(--surface-tertiary);
    border-radius: var(--radius-1);
    overflow: hidden;
  }
  .confidence-fill {
    background: var(--brand-secondary);
    height: 100%;
  }
  ```
- **"Aplicar" CTA:** minimal text-button (no border, color `var(--brand-secondary-pressed)`, weight 600)
- **Reasoning expand:** `<details>` com summary "Por que" + content em `--text-caption`

## Markup pattern

```tsx
<aside className="ai-suggestion-inline" role="complementary">
  <div className="asi-icon">
    <LucideZap size={16} strokeWidth={1.5} aria-hidden />
  </div>
  <div className="asi-body">
    <p className="asi-text">{suggestion}</p>
    {reasoning && (
      <details className="asi-reasoning">
        <summary>Por que</summary>
        <p>{reasoning}</p>
      </details>
    )}
  </div>
  <div className="asi-meta">
    <div
      className="confidence-bar"
      role="meter"
      aria-valuenow={confidence}
      aria-valuemin={0}
      aria-valuemax={1}
      aria-label={`Confianca ${(confidence * 100).toFixed(0)}%`}
    >
      <div
        className="confidence-fill"
        style={{ width: `${confidence * 100}%` }}
      />
    </div>
    <button className="asi-apply" onClick={onApply}>
      Aplicar
    </button>
  </div>
</aside>
```

## Animation (mount)

```css
.ai-suggestion-inline {
  animation: asi-arrive var(--dur-base) var(--ease-out);
}
@keyframes asi-arrive {
  from { opacity: 0; transform: translateX(-8px); }
  to   { opacity: 1; transform: translateX(0); }
}
.asi-icon svg {
  animation: ai-icon-pulse var(--dur-base) var(--ease-out);
}
```

## Accessibility

- `<aside role="complementary">` semantica
- `aria-label` no agent name no body se relevante
- `confidence-bar` com `role="meter"` + `aria-valuenow`
- `<details>` nativo para reasoning (no JS)
- Reduced motion respeitado via media query global
