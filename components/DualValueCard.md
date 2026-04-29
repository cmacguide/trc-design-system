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

## Accessibility & semantica

- Estrutura `<dl>` para dual-value (relacao termo-definicao semantica)
- Urgency exposto via `data-urgency` para querying CSS sem class branching
- Botao primary deve ter `aria-describedby` apontando para `.dvc-title` quando contexto extra-curto
- Reduced motion: hover lift desabilitado via media query
