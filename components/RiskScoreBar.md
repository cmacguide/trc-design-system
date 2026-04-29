# RiskScoreBar

Barra horizontal mostrando score de risco compliance (0-100). Cor evolui em zonas semanticas. Usado em listas de obras, perfis de empreiteiros, e dashboards de portfolio.

## Props

```typescript
type RiskScoreProps = {
  score: number;  // 0-100
  breakdown?: Array<{ key: string; value: number }>;
};
```

## Score zones (color mapping)

| Range | Zona | Token |
|---|---|---|
| 0-30  | ok    | `--status-success-line` |
| 30-60 | warn  | `--status-warning-line` |
| 60-100 | block | `--status-error-line` |

## Token application

- **Bar container:**
  ```css
  .risk-bar {
    width: 100%;
    height: 6px;
    background: var(--surface-tertiary);
    border-radius: var(--radius-1);
    overflow: hidden;
  }
  ```
- **Fill:** width = `${score}%`, color via `data-level`
  ```css
  .risk-fill[data-level="ok"]    { background: var(--status-success-line); }
  .risk-fill[data-level="warn"]  { background: var(--status-warning-line); }
  .risk-fill[data-level="block"] { background: var(--status-error-line); }
  ```
- **Score number:** `--text-mono` weight 600
- **Hover tooltip:** breakdown weights formatted (ex: "CND 40% / INSS 30% / ASO 20% / FGTS 10%")

## Markup pattern

```tsx
<div className="risk-score">
  <span className="risk-score-number">{score}</span>
  <div
    className="risk-bar"
    role="meter"
    aria-valuenow={score}
    aria-valuemin={0}
    aria-valuemax={100}
    aria-label={`Risk score ${score}/100`}
  >
    <div
      className="risk-fill"
      data-level={zoneFor(score)}
      style={{ width: `${score}%` }}
    />
  </div>
  {breakdown && (
    <Tooltip content={breakdownText(breakdown)}>
      <Info size={14} strokeWidth={1.5} />
    </Tooltip>
  )}
</div>
```

## Accessibility

- `role="meter"` + `aria-valuenow/min/max`
- Score numerico **sempre** visivel (cor nao e canal unico)
- Breakdown via tooltip nao bloqueia leitura primaria do score
- Reduced motion: width fill nao animado
