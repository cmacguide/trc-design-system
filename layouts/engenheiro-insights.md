---
name: engenheiro-insights
version: 1.0.0
since: S166
---

# Layout: engenheiro-insights

Layout da pagina **Insights Gerenciais** do ponto de vista Engenheiro. Grade 4-card responsiva dentro do app-shell existente.

**Rota:** `/engenheiro/insights`

> **Audit trail:** Validated via canary `s166-engenheiro-tela3-canary.html` section `.insights-grid` e `.page-content` (CSS lines ~428-447, HTML lines ~799-1146 do canary). Viewports 1920px (4col), 768px (2x2), 412px (stacked) validados visualmente — Gate 1 PASS S166.

---

## Posicao na hierarquia

Este layout e um **filho de `app-shell.md`**. Renderiza dentro de `.app-main`. Nao redeclara sidebar, header ou drawer — esses elementos pertencem ao app-shell.

```
app-shell
  app-sidebar (←  app-shell.md)
  app-header  (←  app-shell.md)
  app-main
    engenheiro-insights   ← ESTE LAYOUT
      .page-content
        .page-header   (h1 + subtitle)
        .insights-grid (4 × InsightCard)
```

---

## Page header

| Elemento | Token / Valor | Por que |
|---|---|---|
| `<h1 class="page-title">` | `--font-display` (Fraunces), `--text-h1` (28px), `font-weight: 500`, `font-variation-settings: "opsz" 32, "SOFT" 30` | Segue padrao de h1 page-level do design-system (ver tokens.md §1) |
| Texto h1 | "Insights gerenciais" | Nome descritivo da secao — minusculas iniciais (nao ALL CAPS, nao Title Case excessivo) |
| `<p class="page-subtitle">` | `--text-body` (14px), `color: var(--text-secondary)` | Complementa h1 com contexto temporal ("Visao executiva compliance") |
| Margin-bottom do page-header | `var(--space-6)` = 24px | Separa titulo do grid sem "ilha" — mesmo padrao do canary |

---

## Densidade rhythm prescritiva

| Gap | Valor desktop (>=1024px) | Valor tablet/mobile (<=768px) | Token de origem |
|---|---|---|---|
| h1 → grid | `var(--space-6)` = **24px** | `var(--space-6)` = 24px (mantido) | `--space-6` |
| Entre cards no grid (1920px wide) | **24px** | — | `--space-6` |
| Entre cards no grid (768px tablet) | **16px** | — | `--space-4` |
| Entre cards no grid (412px mobile) | **16px** | **16px** | `--space-4` |
| Padding lateral da page-content (desktop) | `var(--space-7)` = 48px | `var(--space-6)` = 24px (tablet) | `--space-7` / `--space-6` |
| Padding lateral da page-content (mobile) | — | `var(--space-4)` = 16px | `--space-4` |

---

## Grid breakpoints (prescritivos)

```css
.insights-grid {
  display: grid;
  gap: 24px;  /* var(--space-6) — desktop default */
}

/* 1920px wide + desktop >=1024px: 4 colunas iguais */
@media (min-width: 1024px) {
  .insights-grid {
    grid-template-columns: repeat(4, 1fr);
    gap: 24px;
  }
}

/* Tablet 768-1023px: 2x2 */
@media (max-width: 1023px) and (min-width: 768px) {
  .insights-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 16px;
  }
}

/* Mobile <768px: stacked vertical */
@media (max-width: 767px) {
  .insights-grid {
    grid-template-columns: 1fr;
    gap: 16px;
    padding: 0 var(--space-4);
    /* Nota: padding-inline aqui APENAS se page-content nao declarar padding proprio */
  }
}
```

**Valores exatos validados no canary:**

| Viewport | `grid-template-columns` | `gap` |
|---|---|---|
| 1920px (wide desktop, sidebar 240px) | `repeat(4, 1fr)` | `24px` |
| 768px (tablet, sidebar icon-rail 64px) | `repeat(2, 1fr)` | `16px` |
| 412px (mobile, sem sidebar) | `1fr` | `16px` |

---

## Sidebar reference (app-shell.md)

Este layout nao declara a sidebar — usa a comportamento definido em `app-shell.md`:

| Breakpoint | Sidebar | Efeito no grid |
|---|---|---|
| Desktop >=1024px | Persistent 240px | Content area = viewport - 240px; grid 4-col usa esse espaco |
| Tablet 768-1023px | Icon-rail 64px | Content area = viewport - 64px; grid 2-col |
| Mobile <768px | Off-canvas (zero width quando fechada) | Content area = 100vw; grid 1-col com padding 16px |

---

## Ordem dos cards no grid

A ordem dos cards segue a prioridade informacional para o Engenheiro (da mais urgente para mais contextual):

| Posicao | Card | Variant `InsightCard` |
|---|---|---|
| 1 | Vencimentos proximos 30 dias | `bar` |
| 2 | Taxa bloqueio biometria | `line` |
| 3 | Compliance por obra | `stacked` |
| 4 | Alertas ativos | `alerts` |

Em tablet 2x2: Card 1+2 na linha 1, Card 3+4 na linha 2. Em mobile stacked: ordem mantida.

---

## Markup pattern

```tsx
// pages/engenheiro/insights.tsx

export default function EngenheiroInsights() {
  return (
    <div className="page-content">
      <header className="page-header">
        <h1 className="page-title">Insights gerenciais</h1>
        <p className="page-subtitle">Visao executiva compliance</p>
      </header>

      <section className="insights-grid" aria-label="Painel de insights gerenciais">
        <InsightCard
          variant="bar"
          title="Vencimentos proximos 30 dias"
          subtitle="Documentos por dia"
          footerText="Total: 286 docs proximos 30 dias"
          footerLink={{ label: "ver detalhes →", href: "/engenheiro/vencimentos" }}
        >
          <VencimentosBarChart />
        </InsightCard>

        <InsightCard
          variant="line"
          title="Taxa bloqueio biometria"
          subtitle="Ultimas 12 semanas"
          footerText=""  {/* ver InsightCard §line — footer especial com trend */}
        >
          <BiometriaLineChart />
        </InsightCard>

        <InsightCard
          variant="stacked"
          title="Compliance por obra"
          subtitle="Status ativos / total — 5 obras"
          footerText="2 de 5 obras em deterioracao"
          footerLink={{ label: "ver todas as obras →", href: "/engenheiro/obras" }}
        >
          <ComplianceStackedRows obras={obras} />
        </InsightCard>

        <InsightCard
          variant="alerts"
          title="Alertas ativos"
          subtitle="12 alertas requerem atencao"
          footerText="Mostrando 5 de 12"
          footerLink={{ label: "ver todos (12) →", href: "/engenheiro/alertas" }}
        >
          <AlertList alerts={alertas} />
        </InsightCard>
      </section>
    </div>
  );
}
```

---

## CSS do layout

```css
/* Page content padding (herda parcialmente de app-shell, refinado aqui) */
.page-content {
  flex: 1;
  padding: var(--space-7);  /* 48px desktop */
}
@media (max-width: 1023px) and (min-width: 768px) {
  .page-content {
    padding: var(--space-6) var(--space-5);  /* 24px 20px tablet */
  }
}
@media (max-width: 767px) {
  .page-content {
    padding: var(--space-4);  /* 16px mobile */
  }
}

/* Page header */
.page-header {
  margin-bottom: var(--space-6);  /* 24px — ritmo prescrito */
}
.page-title {
  font-family: var(--font-display);
  font-size: var(--text-h1);       /* 28px */
  font-weight: 500;
  font-variation-settings: "opsz" 32, "SOFT" 30;
  letter-spacing: -0.01em;
  color: var(--text-primary);
  margin-bottom: var(--space-2);   /* 8px antes do subtitle */
}
@media (max-width: 767px) {
  .page-title {
    font-size: var(--text-h2);     /* 22px mobile */
  }
}
.page-subtitle {
  font-size: var(--text-body);     /* 14px */
  color: var(--text-secondary);
  font-weight: 400;
}

/* Insights grid */
.insights-grid {
  display: grid;
  gap: var(--space-6);             /* 24px desktop */
}
@media (min-width: 1024px) {
  .insights-grid {
    grid-template-columns: repeat(4, 1fr);
  }
}
@media (max-width: 1023px) and (min-width: 768px) {
  .insights-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: var(--space-4);           /* 16px tablet */
  }
}
@media (max-width: 767px) {
  .insights-grid {
    grid-template-columns: 1fr;
    gap: var(--space-4);           /* 16px mobile */
  }
}
```

---

## Phase 1 / Phase 2 roadmap

| Feature | Phase 1 | Phase 2 |
|---|---|---|
| Modo de exibicao | **Static dashboard** — 4 cards fixos com mock data realista | View toggle: Trends / Calendar / Risk Table (abas ou segmented control no page-header) |
| URL deep-link | `/engenheiro/insights` (unico) | `?view=calendar`, `?view=risk-table` (URL param para compartilhar view especifica) |
| `FoundryPatternBadge` | Stub — label e confidence estaticos do seed | Foundry PatternDetector real, confianca calculada sobre 90d historico |
| `BiometriaLineChart` | SVG inline com mock data hardcoded | Series reais do endpoint `/api/metricas/biometria?range=90d` |

**Nota Phase 2:** ao adicionar o view toggle, o `page-header` deve expandir para incluir o controle de navegacao de view (segmented control ou tabs), mantendo h1 + subtitle acima.

---

## Anti-patterns (concept × forbidden × required substitute)

| Concept | Forbidden | Required substitute |
|---|---|---|
| Grid gap inconsistente | Usar `20px` no desktop (valor canary interno) como target | **`24px` (var(--space-6))** no design-system — canary usava `--gap-card: 20px` local; o prescritivo do DS e `--space-6` |
| Layout proprio de sidebar | Redeclarar sidebar ou mudar `grid-template-columns` de `app-shell` | **Apenas `.page-content` e `.insights-grid`** — sidebar e chrome sao do app-shell |
| Padding mobile excessivo | `padding: 24px` em mobile (consome ~12% do 412px util) | **`padding: var(--space-4)`** = 16px — preserva espaco para os cards |
| Ordem dos cards | Reordenar por alfabeto ou data de criacao | **Prioridade informacional**: urgencia (vencimentos → biometria → compliance → alertas) |
| h1 text-transform uppercase | `text-transform: uppercase` no titulo da pagina | **Sem uppercase** — Fraunces com weight/optical-size ja comunica hierarquia |

---

## Acessibilidade

- `<section aria-label="Painel de insights gerenciais">` — region label para screen readers
- `<h1>` unico por pagina; sem h1 duplicado na sidebar
- Tab order natural: page-header → Card 1 → Card 2 → Card 3 → Card 4 (footer links de cada card)
- Cards `<article>` com `aria-label` individual (via `InsightCard`)
- Reduced motion: transitions e animacoes respeitam `@media (prefers-reduced-motion: reduce)` global
