# DualValueCard

Component que materializa o **BKM dual-value framing** TRC: toda decisao de compliance trabalhista tem dois lados (Empreiteiro + Construtora) e ambos devem ser representados visualmente em equilibrio.

## Props

```typescript
type ValueDescriptor = {
  amount: number;
  label: string;
  format?: "currency" | "integer-with-suffix"; // default: "currency"
  suffix?: string; // required when format === "integer-with-suffix" (e.g. "construtora(s)")
};

type DualValueCardProps = {
  title?: string;
  valueEmpreiteiro: ValueDescriptor;
  valueConstrutora: ValueDescriptor;
  action: { primary: string; secondary?: string };
  urgency: "critical" | "warning" | "info";

  // S169 PoV customization (optional — DualValueCard remains BKM-neutral by default)
  empreiteiroLabel?: string;     // default "EMPREITEIRO" — override para PoV (ex: "VOCÊ" em Empreiteiro PoV)
  construtoraLabel?: string;     // default "CONSTRUTORA"
  headerExtras?: React.ReactNode; // slot opcional entre title e urgency badge (ex: ConstrutoraIndicator chip)
  primaryVariant?: "brand" | "accent" | "urgency"; // default "brand"; "urgency" derives bg from urgency prop (critical=error-line, warning=warning-line, info=brand-primary)

  onPrimary: () => void;
  onSecondary?: () => void;
};
```

### Format flexibility (S169 ADD)

DualValueCard suporta dois formatos de `amount` para acomodar BKM-neutral (currency) e PoVs assimetricos (currency + integer-with-suffix). Casos canonicos:

| PoV | valueEmpreiteiro | valueConstrutora | Exemplo de uso |
|---|---|---|---|
| BKM-neutral (Admin Tela 1) | `format: 'currency'` (default) | `format: 'currency'` (default) | "R$ 87.300" \| "R$ 142.300" |
| Empreiteiro Tela 1 (1st person) | `format: 'currency'` | `format: 'integer-with-suffix', suffix: 'construtora(s)'` | "R$ 87.300" \| "2 construtora(s)" |

Render rule (helper `renderAmount(v: ValueDescriptor): string`):
- `format === 'integer-with-suffix'` → `${amount} ${suffix}` (sem locale, plain integer)
- default → `formatBRL(amount)` (BRL formatado)

**Por que NAO so trocar para `formattedAmount?: string` prop:** porque o spec mantem invariante de que `amount: number` e a fonte unica de verdade (consumer nao formata, componente formata). Se passamos string ja formatada, perdemos type safety + re-render computacional + a/b testing com Intl locales.

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
  - `primaryVariant="urgency"`: bg derivado da urgency prop — `var(--status-error-line)` (critical) | `var(--status-warning-line)` (warning) | `var(--brand-primary)` (info fallback)
- **Secondary button:** bg transparent, border 1px `var(--border-default)`, color `var(--text-secondary)`

## Markup pattern

```tsx
function renderAmount(v: ValueDescriptor): string {
  return v.format === 'integer-with-suffix'
    ? `${v.amount} ${v.suffix ?? ''}`.trim()
    : formatBRL(v.amount);
}

<article className="dual-value-card" data-urgency={urgency}>
  <header className="dvc-header">
    <h3 className="dvc-title">{title ?? 'Análise bilateral'}</h3>
    {headerExtras /* opcional: ConstrutoraIndicator chip ou similar */}
    <Badge urgency={urgency}>{urgencyLabel}</Badge>
  </header>
  <dl className="dvc-values">
    <div className="dvc-row">
      <dt className="dvc-label">{empreiteiroLabel ?? 'EMPREITEIRO'}</dt>
      <dd className="dvc-amount">{renderAmount(valueEmpreiteiro)}</dd>
      <dd className="dvc-detail">{valueEmpreiteiro.label}</dd>
    </div>
    <div className="dvc-row">
      <dt className="dvc-label">{construtoraLabel ?? 'CONSTRUTORA'}</dt>
      <dd className="dvc-amount">{renderAmount(valueConstrutora)}</dd>
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
