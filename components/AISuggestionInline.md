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

## Wrap & container behavior (REFINADO S163b — thresholds 320/440 + CTA filled em narrow)

> **Validacao S163b:** esta secao foi validada via canary HTML preview pre-push em `trc-platform/docs/assets/preview/admin-tela-1-v4-preview.html` (substituto #7 visual decision pre-validation).

AISuggestionInline aparece em containers de largura variada (hero ~720px, sidebar ~220px, modal ~480px, list-item ~360px, mobile main ~380px). Usa **container queries** para adaptar layout interno, **NAO `min-width` rigido** (que quebraria sidebar narrow).

**Thresholds prescritivos (refinados S163b apos canary HTML preview):**

| Container width | Layout | CTA "Aplicar" treatment |
|---|---|---|
| `< 320px` (sidebar empreteiro, modal compacto) | Stack vertical full: `icon` / `body` / `meta` | Filled button azul full-width (alta visibilidade em narrow) |
| `320-439px` (mobile main S20 Ultra ~380px) | `icon body` lado-a-lado topo + `meta meta` full-width abaixo | Filled button azul full-width (idem narrow) |
| `>= 440px` (modal medium, hero) | Inline horizontal: `icon body meta` | Text-button minimal (suficiente em wide) |

**Por que 320 e 440 (refinamento dos 280/480 originais):** valores antigos foram catched insuficientes em S163b empirical render — Bolt em mobile 412px com sidebar 30% sobrando ~290px usable produziu texto vertical 1-palavra-por-linha porque container caiu entre 280 (ja era horizontal) e 480 (ainda nao era wide), forcando layout intermediario que comprime CTA + body em colunas estreitas. **320 garante que abaixo dele tudo empilha (zero risco de comprimir)**; **440 e o ponto onde body+meta tem espaco confortavel para inline** (~280px body width minimo legivel).

**Por que CTA filled em narrow:** text-button minimal em container <440px desaparece visualmente apos texto longo wrap (eye perde o hook acionavel). Filled button full-width abaixo do texto reestabelece hierarquia "leu sugestao -> aqui esta o CTA". Em wide (>=440px) text-button volta a funcionar porque ele esta **lateralmente** ao texto (paralelo, nao serial), dispensando enfase visual.

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
  /* Default narrow (<320px container): tudo empilhado vertical */
  grid-template-columns: 1fr;
  grid-template-areas:
    "icon"
    "body"
    "meta";
}

/* Container 320px+: icon esquerda, body direita, meta full-width abaixo */
@container asi (min-width: 320px) {
  .ai-suggestion-inline {
    grid-template-columns: auto 1fr;
    grid-template-areas:
      "icon body"
      "meta meta";
  }
}

/* Container 440px+: icon | body | meta horizontal inline */
@container asi (min-width: 440px) {
  .ai-suggestion-inline {
    grid-template-columns: auto 1fr auto;
    grid-template-areas: "icon body meta";
    align-items: center;
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

/* CTA "Aplicar" treatment por container width */
.asi-apply {
  background: none;
  border: none;
  color: var(--brand-secondary-pressed);
  font-weight: 600;
  font-size: var(--text-caption);
  cursor: pointer;
  padding: var(--space-2) var(--space-3);
  border-radius: var(--radius-2);
  white-space: nowrap;
  font-family: inherit;
}
@container asi (max-width: 439px) {
  .asi-apply {
    background: var(--brand-secondary);
    color: var(--text-inverse);
    width: 100%;
    text-align: center;
  }
}
```

**Por que NAO `min-width: 280px` no container (anti-pattern catched em S163):** componente seria forcado a ultrapassar containers narrow, causando overflow horizontal na page. Container queries resolvem **adaptando o layout interno**, NAO forcando largura externa.

**Caso S163b in-Bolt-render documentado:** AISuggestionInline em mobile S20 Ultra (412px viewport, sidebar TRC 30% = main content ~290px) com container query antiga (280/480) gerou `meta meta` com `Aplicar` text-button comprimido em ~80px lateral -> texto vertical 1-palavra-por-linha (`Concretex` / `tem` / `historico` cada em linha). Fix: thresholds 320/440 + CTA filled em narrow (validado via canary HTML local pre-push, substituto #7).

## Anti-patterns (concept × forbidden × required substitute)

| Concept | Forbidden | Required substitute |
|---|---|---|
| Container query thresholds | `280/480px` (intermediario quebra narrow real-world) | **`320/440px`** — empilha tudo abaixo de 320, inline soh acima de 440 |
| CTA em narrow container | Text-button (some apos wrap longo) | **Filled button azul full-width** |
| Text wrap | `word-break: break-all` ou `hyphens: auto` | **`overflow-wrap: break-word; word-break: normal; hyphens: none`** (sentence-wrap) |
| Container width control | `min-width: 280px` no proprio componente | **Container queries internas** + `width: 100%; max-width: 100%`

## Accessibility

- `<aside role="complementary">` semantica
- `aria-label` no agent name no body se relevante
- `confidence-bar` com `role="meter"` + `aria-valuenow`
- `<details>` nativo para reasoning (no JS)
- Reduced motion respeitado via media query global
