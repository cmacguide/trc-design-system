# BreadcrumbStack

Breadcrumb minimal com separator tipografico (NAO `/` ou `>`), focused em leiturabilidade institucional. Aparece em paginas drill-down (audit detail, obra detail).

## Props

```typescript
type BreadcrumbProps = {
  trail: Array<{ label: string; href: string }>;
  current: string;
};
```

## Token application

- **Separator:** `›` character (NOT `/` ou `>`), color `var(--border-default)`, font-weight 300
- **Links:** color `var(--brand-secondary)`, hover underline
- **Current:** color `var(--text-primary)`, weight 600, sem hyperlink
- **Padding:** vertical `var(--space-2)`, horizontal `0`
- **Size:** `--text-caption`

## Markup pattern

```tsx
<nav className="breadcrumb-stack" aria-label="Breadcrumb">
  <ol>
    {trail.map((item, idx) => (
      <li key={idx} className="bc-item">
        <a href={item.href} className="bc-link">{item.label}</a>
        <span className="bc-sep" aria-hidden>›</span>
      </li>
    ))}
    <li className="bc-current" aria-current="page">{current}</li>
  </ol>
</nav>
```

## CSS

```css
.breadcrumb-stack {
  font-size: var(--text-caption);
  padding: var(--space-2) 0;
}
.breadcrumb-stack ol {
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-1);
}
.bc-link {
  color: var(--brand-secondary);
  text-decoration: none;
}
.bc-link:hover {
  text-decoration: underline;
}
.bc-sep {
  color: var(--border-default);
  font-weight: 300;
  margin-left: var(--space-1);
}
.bc-current {
  color: var(--text-primary);
  font-weight: 600;
}
```

## Accessibility

- `<nav aria-label="Breadcrumb">` reconhecido por screen readers
- `<ol>` para sinalizar ordem hierarquica
- Current page com `aria-current="page"` (e nao link)
- Separator com `aria-hidden` (decorativo, nao deve ser lido)

## Anti-patterns (evitar)

- NAO usar `/` (sintoma generic SaaS)
- NAO usar `>` (sintoma terminal-CLI, fora de tom)
- NAO usar emoji (📁 → ou similar) — viola anti-pattern #6
