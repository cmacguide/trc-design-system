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

## Wrap & container behavior

AISuggestionInline aparece em containers de largura variada (hero ~720px, sidebar ~220px, modal ~480px, list-item ~360px). Usa **container queries** para adaptar layout interno, **NAO `min-width` rigido** (que quebraria sidebar narrow).

```css
.ai-suggestion-inline {
  container-type: inline-size;
  container-name: asi;

  width: 100%;
  max-width: 100%;
  box-sizing: border-box;
  /* CRITICAL: NAO declarar min-width fixo aqui — quebra em containers narrow */

  display: grid;
  gap: var(--space-3);
  /* Default narrow (<280px container): tudo empilhado vertical */
  grid-template-columns: 1fr;
  grid-template-areas:
    "icon"
    "body"
    "meta";
}

/* Container 280px+: icon esquerda, body direita, meta abaixo */
@container asi (min-width: 280px) {
  .ai-suggestion-inline {
    grid-template-columns: auto 1fr;
    grid-template-areas:
      "icon body"
      "meta meta";
  }
}

/* Container 480px+: icon | body | meta horizontal */
@container asi (min-width: 480px) {
  .ai-suggestion-inline {
    grid-template-columns: auto 1fr auto;
    grid-template-areas: "icon body meta";
  }
}

.asi-icon { grid-area: icon; }
.asi-body { grid-area: body; min-width: 0; /* permite shrink */ }
.asi-meta { grid-area: meta; min-width: 0; }

.asi-text {
  /* CRITICAL: wrap por sentence, NAO por character */
  overflow-wrap: break-word;
  word-break: normal;       /* NAO break-all (quebra palavra) */
  hyphens: none;
  min-width: 0;
}
```

**Por que NAO `min-width: 280px` no container (anti-pattern catched em S163):** componente seria forcado a ultrapassar containers narrow, causando overflow horizontal na page. Container queries resolvem **adaptando o layout interno**, NAO forcando largura externa.

**Caso S163 in-Bolt-render documentado:** AISuggestionInline em mobile S20 Ultra (412px viewport, sidebar TRC 30% = main content ~290px) sem container query gerou `width: max-content` por default → texto vertical 1-palavra-por-linha ("Concretex" / "tem" / "historico" cada em linha). Fix: adicionar container query + overflow-wrap. Verificar visualmente pos-ingest (substituto #6 docs-only verification).

## Accessibility

- `<aside role="complementary">` semantica
- `aria-label` no agent name no body se relevante
- `confidence-bar` com `role="meter"` + `aria-valuenow`
- `<details>` nativo para reasoning (no JS)
- Reduced motion respeitado via media query global
