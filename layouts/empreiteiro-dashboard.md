# Empreiteiro Dashboard Layout

Template de pagina para `/empreiteiro/` (Tela 1). Padrao **Featured Action + Compact List** ("Spotlight"). Materializa o JTBD nucleo do Empreiteiro: chegar, ver IMEDIATAMENTE qual a acao mais urgente com R$ valor de risco, agir num tap, e ter contexto compacto das outras construtoras abaixo.

## Estrutura visual (zoom out)

```
┌─────────────────────────────────────┐ <- viewport mobile 375px
│ Header pagina (fixed top, opcional) │
│   • saudacao + nome empreiteiro     │
│   • avatar (32×32 inicial)          │
├─────────────────────────────────────┤
│                                     │
│  [HERO CARD — Featured Action]      │
│  • DualValueCard urgency=critical   │
│  • CTA primary (Iniciar acao)       │
│  • CTA secondary (Adiar / Lembrar)  │
│                                     │
├─────────────────────────────────────┤
│  [FILTER PILLS — FilterPills]       │
│   Todas  |  Atencao (N)  |  OK (N) │  <- horizontal scroll
├─────────────────────────────────────┤
│  Lista compacta "Outras construtoras"
│  ─ Construtora A ── ●  ConstrutoraIndicator
│  ─ Construtora B ── ●  ConstrutoraIndicator
│  ─ Construtora C ── ●  ConstrutoraIndicator
│  ─ Construtora D ── ●  ConstrutoraIndicator
│                                     │
└─────────────────────────────────────┘
        [Mobile Tab Bar bottom 56px]
```

## Componentes invocados

- `DualValueCard` (urgency=critical) — hero
- `FilterPills` — barra de filtros entre Hero e Lista; pills: "Todas" / "Atencao" / "OK" (implementado S168 DSVL 6a aplicacao; ver `components/FilterPills.md`)
- `StatusDot` (size=12, status segundo compliance da construtora) — em cada item da lista
- `ConstrutoraIndicator` (variant=badge, size=sm) — em cada item da lista
- (zero-state) `ActionableNotificationCard` com copy "Tudo em dia! Proxima validacao em X dias" — substitui hero quando ha 0 acoes urgentes

Audit trail: Validated via canary HTML preview pre-push: docs/assets/preview/empreiteiro-tela1-v1-preview.html (section [filter pills container] entre Hero e Lista compacta).

## Token application

- **Page background:** `var(--surface-bg)` (#FFFFFF)
- **Section spacing:** `var(--space-6)` entre Header / Hero / Lista
- **Hero margin:** `var(--space-4)` lateral, `var(--space-5)` top
- **Lista section:**
  - Title "Outras construtoras" — `--text-h3` Plus Jakarta 600, color `--text-secondary`, padding `var(--space-3) var(--space-4)`
  - Container — `var(--surface-secondary)` background com `var(--radius-3)` corner radius
  - Item — display flex, align center, gap `var(--space-3)`, padding `var(--space-3) var(--space-4)`, border-bottom `1px solid var(--border-subtle)` (last child sem border)
  - Item layout: `[StatusDot] [ConstrutoraNome (flex 1)] [ConstrutoraIndicator badge]`

## Markup pattern

### Estado normal (>= 1 acao urgente)

```tsx
<main className="empreiteiro-dashboard">
  <header className="ed-header">
    <div className="ed-greeting">
      <span className="ed-hello">Bom dia,</span>
      <h1 className="ed-name">{empreiteiroNome}</h1>
    </div>
    <Avatar size={32} initials={empreiteiroIniciais} />
  </header>

  <section className="ed-hero" aria-labelledby="hero-title">
    <DualValueCard
      urgency="critical"
      titleSlot={<h2 id="hero-title">{heroTitle}</h2>}
      valueEmpreiteiro={featuredAction.valueEmpreiteiro}
      valueConstrutora={featuredAction.valueConstrutora}
      action={featuredAction.action}
      onPrimary={() => router.push(featuredAction.primaryHref)}
      onSecondary={() => featuredAction.onAdiar()}
    />
  </section>

  <FilterPills
    pills={[
      { id: 'all',       label: 'Todas',   statusHint: 'neutral' },
      { id: 'attention', label: 'Atencao', count: atencaoCount,   statusHint: 'attention' },
      { id: 'ok',        label: 'OK',      count: okCount,        statusHint: 'ok' },
    ]}
    activePillId={activeFilter}
    onPillSelect={(id) => setActiveFilter(id)}
  />

  <section className="ed-list" aria-labelledby="list-title">
    <h3 id="list-title" className="ed-list-title">Outras construtoras</h3>
    <ol className="ed-list-items">
      {outrasConstrutoras.map((c) => (
        <li key={c.id} className="ed-list-item">
          <button
            className="ed-list-row"
            onClick={() => router.push(`/empreiteiro/construtoras/${c.id}`)}
            aria-label={`${c.nome}: ${statusLabel(c.compliance)}`}
          >
            <StatusDot status={c.compliance} size={12} />
            <span className="ed-construtora-nome">{c.nome}</span>
            <ConstrutoraIndicator
              construtoraId={c.id}
              construtoraNome={c.nome}
              variant="badge"
              size="sm"
            />
          </button>
        </li>
      ))}
    </ol>
  </section>
</main>
```

### Estado zero (0 acoes urgentes — hero substituido)

```tsx
<section className="ed-hero ed-hero--zero">
  <ActionableNotificationCard
    priority="info"
    title="Tudo em dia!"
    body={`Proxima validacao em ${diasAteProximaValidacao} dias.`}
    icon="CheckCircle2"
  />
</section>
```

## CSS

```css
.empreiteiro-dashboard {
  display: flex;
  flex-direction: column;
  gap: var(--space-6);
  padding: var(--space-5) 0 calc(56px + env(safe-area-inset-bottom) + var(--space-4));
  /* bottom-padding: tab bar 56px + safe area + breathing space */
}

.ed-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 var(--space-4);
}
.ed-hello {
  display: block;
  color: var(--text-tertiary);
  font-size: var(--text-caption);
}
.ed-name {
  font-family: var(--font-display); /* Fraunces */
  font-size: 22px;
  font-weight: 600;
  color: var(--text-primary);
  margin: 0;
}

.ed-hero {
  padding: 0 var(--space-4);
}

.ed-list-title {
  font-size: var(--text-h3);
  font-weight: 600;
  color: var(--text-secondary);
  padding: 0 var(--space-4);
  margin: 0;
}

.ed-list-items {
  list-style: none;
  margin: var(--space-3) var(--space-4) 0;
  padding: 0;
  background: var(--surface-secondary);
  border-radius: var(--radius-3);
  overflow: hidden;
}

.ed-list-row {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  width: 100%;
  padding: var(--space-3) var(--space-4);
  background: transparent;
  border: none;
  border-bottom: 1px solid var(--border-subtle);
  text-align: left;
  cursor: pointer;
  transition: background 150ms var(--ease-out);
}
.ed-list-row:hover {
  background: var(--surface-tertiary);
}
.ed-list-item:last-child .ed-list-row {
  border-bottom: none;
}

.ed-construtora-nome {
  flex: 1;
  font-size: var(--text-body);
  color: var(--text-primary);
}
```

## Comportamento dinamico

- **Pull-to-refresh:** disponivel; trigger refetch da `featuredAction` + `outrasConstrutoras`
- **Tap no StatusDot:** expande inline tooltip com pendencias detalhadas (reusar pattern `<details>` do `AISuggestionInline`); colapsado apos 4s sem interacao
- **Featured action recalculada server-side:** ranking R$ × urgencia. Quando empreiteiro completa, proxima entra automaticamente (re-fetch on resume tab)
- **Loading skeleton:** durante fetch inicial, mostrar `DualValueCard` skeleton (cinza `--surface-tertiary` + shimmer 1.4s `var(--ease-in-out)`)

## Accessibility

- `<main>` semantico com `<h1>` para nome + `<h2>` em hero + `<h3>` em lista
- Items da lista sao `<button>` (nao `<a>`) porque trigger e scope-de-app — usar `aria-label` que cita nome + status compliance para screen readers
- Hierarquia heading respeitada (h1 > h2 > h3)
- Reduced motion: hover transition desabilitado, shimmer skeleton trocado por estatico

## Anti-patterns (evitar)

- NAO mostrar **todas as construtoras** com mesmo peso visual — perde o "Featured Action" pattern e vira lista pura
- NAO usar carousels horizontais para acoes (swipe entre cards) — ofusca prioridade
- NAO usar gradientes no hero card — viola anti-pattern #4
- NAO colocar tudo num scroll infinito — limite de 8 construtoras na lista compacta; >8 vai pra "Ver todas" CTA secundario abaixo
- NAO usar emoji em nome construtora ou status (substituir por StatusDot + ConstrutoraIndicator) — viola anti-pattern #6

## Variants futuros (Phase 2)

- **Pinned action:** empreiteiro pode pinar uma acao para fixar no hero
- **FilterPills:** barra "Todas / Atencao / OK" entre Hero e Lista — implementado S168 (DSVL 6a aplicacao); ver `components/FilterPills.md`
- **Smart sort:** Foundry Agent re-ordena por probabilidade de exito × valor (Phase 2)
