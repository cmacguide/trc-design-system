# KPICard

Card de metrica chave. Tem dois modos: **hero** (Engenheiro Tela 1, "R$ 2.187.430 Custo Legal Evitado") e **default** (grid 4-col em dashboards).

## Props

```typescript
type KPICardProps = {
  label: string;          // ex: "CUSTO LEGAL EVITADO 2026"
  value: number;          // ex: 2187430
  format: "currency" | "percent" | "count";
  trend?: { delta: number; baseline: string };  // ex: "+18.4% vs 2025"
  sparkline?: number[];
  hero?: boolean;
};
```

## Token application — Hero variant

- **Padding:** `var(--space-5)` (24px all sides)
- **Background:** `var(--surface-secondary)` (`#F8F9FA`, surface elevation contra `--surface-bg`)
- **Border:** 1px solid `var(--border-subtle)`
- **Border radius:** `var(--radius-3)` (12px)
- **Elevation:** `var(--elev-2)`
- **Label:** `--text-label` color `var(--text-tertiary)`
- **Value:** `--text-hero` Fraunces 600 opsz 72 SOFT 30, color `var(--text-primary)`, `font-feature-settings: "tnum"`, `letter-spacing: -0.02em`
- **Trend:** row com sparkline + `--text-caption` color semantic (`--status-success-fg` se positivo, `--status-error-fg` se negativo)

## Token application — Default (non-hero)

- **Padding:** `var(--space-3)`
- **Background:** `var(--surface-elevated)`
- **Border:** 1px solid `var(--border-default)`
- **Border radius:** `var(--radius-2)`
- **Elevation:** `var(--elev-1)`
- **Label:** `--text-label` color `var(--text-tertiary)`
- **Value:** `--text-h2` weight 700, **no Fraunces** (only hero gets Fraunces)
- **Compact, em grid 4-col**

## Markup pattern

```tsx
<article className="kpi-card" data-hero={hero ? "true" : undefined}>
  <header>
    <span className="kpi-label">{label}</span>
  </header>
  <div className="kpi-value">{formatValue(value, format)}</div>
  {trend && (
    <footer className="kpi-trend">
      {sparkline && <Sparkline data={sparkline} />}
      <span
        className="kpi-trend-text"
        data-direction={trend.delta >= 0 ? "up" : "down"}
      >
        {trend.delta >= 0 ? "+" : ""}{trend.delta}% {trend.baseline}
      </span>
    </footer>
  )}
</article>
```

## Animation (hero variant first load)

Number counter via Framer Motion `useSpring`:

```tsx
import { motion, useSpring, useTransform } from "framer-motion";

const value = useSpring(0, { damping: 20, stiffness: 60 });
useEffect(() => { value.set(target); }, [target]);
const display = useTransform(value, (v) => formatValue(Math.round(v), format));
```

**Apenas no first load.** Nao re-animar on filter change (irritante).

## Accessibility

- Label semanticamente associado ao value (header > value structure)
- Format BR-aware via `Intl.NumberFormat("pt-BR", { style: "currency", currency: "BRL" })`
- Reduced motion: counter sai instant ao valor final

## Anti-patterns (evitar)

- NAO usar `--text-hero` em variants non-hero (Fraunces e overhead)
- NAO usar drop-shadow generic (use `--elev-*` tokens)
- NAO aplicar `letter-spacing` positivo em hero (numeros R$ ficam mais autoritarios apertados)
