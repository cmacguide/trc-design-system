# DualValueCard

Component que materializa o **BKM dual-value framing** TRC: toda decisao de compliance trabalhista tem dois lados (Empreiteiro + Construtora) e ambos devem ser representados visualmente em equilibrio.

## Props

```typescript
type DualValueCardProps = {
  valueEmpreiteiro: { amount: number; label: string };
  valueConstrutora: { amount: number; label: string };
  action: { primary: string; secondary?: string };
  urgency: "critical" | "warning" | "info";
  onPrimary: () => void;
  onSecondary?: () => void;
};
```

## Token application

- **Background:** `var(--surface-elevated)` base; urgency=critical adds `var(--status-error-bg)` overlay 60%
- **Border:** 1.5px solid; color = `var(--status-error-line)` (critical), `var(--status-warning-line)` (warning), `var(--border-default)` (info)
- **Padding:** `var(--space-4)` mobile / `var(--space-5)` web
- **Border radius:** `var(--radius-3)` (12px)
- **Elevation:** `var(--elev-1)` default, `var(--elev-2)` on hover
- **Hero title:** `--text-h3` Plus Jakarta 700, color `var(--text-primary)`
- **Dual-value section:** dois rows separados por hairline `1px solid var(--border-subtle)`
- **"VOCE / CONTRAPARTE" label:** `--text-label` uppercase, color `var(--text-tertiary)`
- **R$ amount:** `--text-mono-lg` JetBrains Mono 600, color `var(--text-primary)`
- **Primary button:** bg `var(--brand-primary)`, color `var(--action-primary-fg)`, padding `var(--space-3) var(--space-5)`, radius `var(--radius-2)`, weight 600
- **Secondary button:** bg transparent, border 1px `var(--border-default)`, color `var(--text-secondary)`

## Markup pattern

```tsx
<article className="dual-value-card" data-urgency={urgency}>
  <header className="dvc-header">
    <h3 className="dvc-title">{titleSlot}</h3>
    <Badge urgency={urgency}>{urgencyLabel}</Badge>
  </header>
  <dl className="dvc-values">
    <div className="dvc-row">
      <dt className="dvc-label">VOCE</dt>
      <dd className="dvc-amount">{formatBRL(valueEmpreiteiro.amount)}</dd>
      <dd className="dvc-detail">{valueEmpreiteiro.label}</dd>
    </div>
    <div className="dvc-row">
      <dt className="dvc-label">CONTRAPARTE</dt>
      <dd className="dvc-amount">{formatBRL(valueConstrutora.amount)}</dd>
      <dd className="dvc-detail">{valueConstrutora.label}</dd>
    </div>
  </dl>
  <footer className="dvc-actions">
    <button className="btn-primary" onClick={onPrimary}>{action.primary}</button>
    {action.secondary && (
      <button className="btn-secondary" onClick={onSecondary}>{action.secondary}</button>
    )}
  </footer>
</article>
```

## Responsive behavior (container queries — S163b ADD)

> **Validacao S163b:** esta secao foi validada via canary HTML preview pre-push em `trc-platform/docs/assets/preview/admin-tela-1-v4-preview.html` (substituto #7 visual decision pre-validation).

DualValueCard aparece em containers de largura variada: hero admin-decision-focus wide ~920px (lado-a-lado confortavel ~440px cada), modal compacto ~480px, mobile main ~380px (insuficiente para 2-col), sidebar empreteiro ~220px (impossivel). Usa **container queries** para colapsar para layout vertical-stacked quando width insuficiente.

**Thresholds prescritivos:**

| Container width | Layout | Label/Value treatment | Subtitle |
|---|---|---|---|
| `>= 440px` (hero web, modal medium+) | **2-col side-by-side** EMPREITEIRO \| CONSTRUTORA | `<dl>` com label uppercase em cima, value mono abaixo full-width | Below value full-width |
| `< 440px` (mobile main, modal narrow, hero tablet 640 menos padding) | **1-col stacked vertical** com hairline separator entre os 2 lados | Label + value em **linha horizontal** (label esquerda, value mono direita) | Full-width abaixo do label/value pair |

**Por que 440px threshold:** abaixo de 440 cada lado teria `<200px width util` (apos padding card var(--space-5) 20px lateral × 2 = 40px subtraidos), insuficiente para "EMPREITEIRO aguarda liberacao" + valor monetario `R$ 142.300` na mesma coluna sem word-break. Acima de 440, cada lado tem `>=200px` confortavel.

**Por que label inline com value em <440px (e nao label em cima):** stacked vertical com label em cima desperdica linha — em mobile o valor monetario e o **dado primario** que precisa ser scannable rapido. Layout label-esquerda-value-direita-mesma-linha mantem o R$ alinhado a direita (eye scan natural por valor) com label apenas como **rotulo identificador**, nao como header.

```css
.dual-value-card {
  container-type: inline-size;
  container-name: dvc;
  /* ...existing styles... */
}

.dvc-values {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: var(--space-5);
  /* (default >=440px ja aplicado por nao haver media query restrictiva) */
}

@container dvc (max-width: 439px) {
  .dvc-values {
    grid-template-columns: 1fr;
    gap: 0; /* hairline borders fazem o trabalho de separacao */
  }

  .dvc-row {
    display: grid;
    grid-template-columns: auto 1fr;
    align-items: baseline;
    column-gap: var(--space-3);
    row-gap: var(--space-1);
    padding: var(--space-3) 0;
    border-bottom: 1px solid var(--border-subtle);
  }
  .dvc-row:last-child {
    border-bottom: none;
  }

  .dvc-label {
    grid-column: 1;
    align-self: baseline;
  }
  .dvc-amount {
    grid-column: 2;
    text-align: right;
    font-size: var(--text-h3); /* mantem peso visual do mono */
  }
  .dvc-detail {
    grid-column: 1 / -1;
    margin-top: var(--space-1);
  }
}
```

## Badge URGENTE positioning (responsive)

Em containers >= 440px, badge URGENTE no canto superior direito do `.dvc-header` (alinhado ao topo do title).

Em containers < 440px, badge URGENTE deve **NAO sobrepor o title** quando title quebra em multiplas linhas. Solucao prescritiva: `align-self: start` no badge + `flex-shrink: 0` + title com `min-width: 0` para permitir shrink natural.

```css
.dvc-header {
  display: grid;
  grid-template-columns: 1fr auto;
  align-items: start;
  gap: var(--space-3);
  margin-bottom: var(--space-5);
}
.dvc-title {
  min-width: 0;       /* permite title encolher e quebrar */
  overflow-wrap: break-word;
  word-break: normal;
}
.dvc-badge {
  flex-shrink: 0;
  align-self: start;
  white-space: nowrap;
}
```

**Anti-pattern catched S163b:** badge URGENTE em mobile sem `flex-shrink: 0` colapsou para 0px width, "URGENTE" sobreposto ao title quebrado.

## Anti-patterns (concept × forbidden × required substitute)

| Concept | Forbidden | Required substitute |
|---|---|---|
| Layout `< 440px` | 2-col forcado (EMPREITEIRO \| CONSTRUTORA lado-a-lado) | **1-col stacked com hairline separator** entre lados |
| Label-value em narrow | Label em cima, value full-width abaixo (desperdica linha em mobile) | **Label esquerda, value mono direita, mesma linha** + subtitle full-width |
| Subtitle wrap | `word-break: break-all` quando coluna estreita | **`overflow-wrap: break-word; word-break: normal`** (sentence-wrap) |
| Badge URGENTE em narrow | Sem `flex-shrink: 0` (colapsa) ou sem `align-self: start` (sobrepoe title) | **Header como `display: grid` com `1fr auto`** + badge `flex-shrink: 0` + title `min-width: 0` |
| Container width control | `min-width: 440px` no card (overflow horizontal page mobile) | **Container queries internas** + width 100% adapta |

## Accessibility & semantica

- Estrutura `<dl>` para dual-value (relacao termo-definicao semantica)
- Urgency exposto via `data-urgency` para querying CSS sem class branching
- Botao primary deve ter `aria-describedby` apontando para `.dvc-title` quando contexto extra-curto
- Reduced motion: hover lift desabilitado via media query
- Em layout stacked <440px, ordem do reading order = ordem do `<dl>` (Empreiteiro primeiro, Construtora depois) — preserva semantica bilateral mesmo sem visual side-by-side
