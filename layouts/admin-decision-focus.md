# Admin Decision Focus Layout

Template de pagina para `/admin/medicoes/[id]` (Tela 1 do Fluxo Admin Construtora). Padrao **Decision Focus + Dual-Value Reasoning**. Layout single-column focado em **uma decisao por vez**, intencionalmente desenhado para evitar dispersao em listas e dashboards quando o ato e: ler reasoning bilateral + decidir liberar ou bloquear medicao.

A tela e o **antagonista visual** do dashboard de auditoria (Tela 2). Tela 2 explora muitas medicoes em pivot table; Tela 1 zoom-in em UMA, deliberada e auditavel.

## Estrutura visual (zoom out)

```
┌──────────────────────────────────────────────────────────────┐ <- viewport web 1280-1440px
│  Header app (sidebar collapsed/oculta, BreadcrumbStack)      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│        ┌────────────────────────────────────────┐           │
│        │  H1: "Liberar pagamento R$ 142.300?"  │           │
│        │  Subtitle: medicao 042/2026            │           │
│        │                                        │           │
│        │  [ComplianceTrafficLight size=lg]      │           │
│        │   ████  ████  ████  ████              │           │
│        │  CND   FGTS  INSS  ASO                 │           │
│        │                                        │           │
│        │  Status compacto: 3 verdes, 1 vermelho │           │
│        │                                        │           │
│        │  [DualValueCard urgency=warning]       │           │
│        │  • VOCE evita risco R$ 87k + sancao   │           │
│        │  • EMPREITEIRO aguarda R$ 142.300      │           │
│        │                                        │           │
│        │  [Primary] Notificar empreiteiro      │           │
│        │  [Secondary] Ver auditoria             │           │
│        │                                        │           │
│        │  [Tertiary text link] Override admin   │           │
│        │  (modal com justificativa obrigatoria) │           │
│        └────────────────────────────────────────┘           │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

Coluna central com max-width 720px, centrada horizontalmente. **Tudo o resto da viewport e respiracao** (`var(--surface-bg)`) — proposital, sinaliza "concentrar aqui".

## Componentes invocados

- `BreadcrumbStack` — `Medicoes / Obra X / 042/2026` no topo
- `ComplianceTrafficLight` (size=lg) — elemento visual central, gigante (4 categorias compactas com cores semaforo)
- `DualValueCard` (urgency mapeado: critical se ha bloqueio, warning se borderline, info se aprovado) — reasoning bilateral
- (modal override) input + textarea + button — quando user dispara "Override admin"

## Token application

- **Page background:** `var(--surface-bg)` (#FFFFFF)
- **Coluna central:** max-width 720px, margin auto, padding `var(--space-8) var(--space-6)` (top/bottom 64px, lateral 24px)
- **BreadcrumbStack:** padding `var(--space-3) 0`
- **Header (titulo + subtitulo):**
  - H1 — `--text-h2` Fraunces 700 (display weight, 30-32px), color `--text-primary`. Texto inclui o **valor R$** in-line porque e o sujeito da decisao
  - Subtitle — `--text-body` Plus Jakarta 500, color `--text-tertiary`, margin-top `var(--space-1)`
  - Margin-bottom — `var(--space-7)` (separacao do TrafficLight)
- **ComplianceTrafficLight:** size=lg (centralizado horizontalmente). Gap entre categorias: `var(--space-3)`. Cada categoria com label uppercase abaixo (`--text-label` Plus Jakarta 600).
- **Status sumario compacto:** `--text-body` color `--text-secondary`, margin `var(--space-4) 0 var(--space-7) 0`. Format: "3 verdes, 1 vermelho" — **nao** "3/4 OK" (numerico abstrai semantica).
- **DualValueCard:** width 100% da coluna central, hero card do reasoning
- **Primary CTA:** `var(--brand-secondary)` (azul TRC #2563B5) — autoridade tecnica, distingue do laranja brand-primary que e reservado para identidade. Padding `var(--space-3) var(--space-6)`, height 44px, font-weight 600.
- **Secondary CTA:** transparent + border `1px solid var(--border-default)`, color `--text-secondary`. Mesma height/padding do primary.
- **Action row:** flex gap `var(--space-3)`, margin-top `var(--space-6)`
- **Tertiary "Override admin":** text link `--text-caption` color `--text-tertiary` underline em hover. Margin-top `var(--space-5)`. **Visualmente subdued** — nao queremos que admin clique sem deliberacao. Modal de confirmacao obrigatorio.

## Por que primary CTA e azul (e nao laranja)

Decisao deliberada: na maioria das telas, primary CTA e laranja `--brand-primary`. Mas nesta tela, o **CTA primario e "Notificar empreiteiro"** — uma acao de **comunicacao**, nao de "agir-na-acao". O laranja TRC sinaliza "ESTA E A ACAO QUE VOCE DEVE TOMAR AGORA"; aqui o sistema esta sinalizando "NAO LIBERE". Liberar e a acao escondida atras do override. Notificar e o caminho certo. Azul `--brand-secondary` (autoridade tecnica) reflete que "voce esta confirmando a recomendacao auditavel do sistema". Manter coerencia entre Phase 1+2.

## Markup pattern

```tsx
<main className="admin-decision-focus">
  <BreadcrumbStack
    trail={[
      { label: "Medicoes", href: "/admin/medicoes" },
      { label: medicao.obraNome, href: `/admin/obras/${medicao.obraId}` },
    ]}
    current={`${medicao.numero}`}
  />

  <div className="adf-column">
    <header className="adf-header">
      <h1 className="adf-title">
        Liberar pagamento {formatBRL(medicao.valor)}?
      </h1>
      <p className="adf-subtitle">Medicao {medicao.numero}</p>
    </header>

    <ComplianceTrafficLight
      size="lg"
      status={{
        CND: medicao.compliance.cnd,
        FGTS: medicao.compliance.fgts,
        INSS: medicao.compliance.inss,
        ASO: medicao.compliance.aso,
      }}
    />

    <p className="adf-status-summary" aria-live="polite">
      {summarizeCompliance(medicao.compliance)}
    </p>

    <DualValueCard
      urgency={medicao.canRelease ? "info" : "warning"}
      titleSlot={<h2 className="adf-reasoning-title">Razao bilateral</h2>}
      valueEmpreiteiro={{
        amount: medicao.valor,
        label: "EMPREITEIRO aguarda liberacao",
      }}
      valueConstrutora={{
        amount: medicao.riscoTrabalhistaEvitado,
        label: "VOCE evita risco trabalhista + sancao",
      }}
      action={{ primary: "Notificar empreiteiro -- enviar checklist", secondary: "Ver auditoria" }}
      onPrimary={() => triggerNotificacao(medicao.id)}
      onSecondary={() => router.push(`/admin/auditoria?medicao=${medicao.id}`)}
    />

    {medicao.canRelease === false && (
      <button
        type="button"
        className="adf-override-link"
        onClick={() => openOverrideModal(medicao.id)}
      >
        Override admin -- liberar com justificativa obrigatoria
      </button>
    )}
  </div>
</main>
```

## CSS

```css
.admin-decision-focus {
  background: var(--surface-bg);
  min-height: 100vh;
}

.adf-column {
  max-width: 720px;
  margin: 0 auto;
  padding: var(--space-8) var(--space-6);
}

.adf-header {
  text-align: center;
  margin-bottom: var(--space-7);
}

.adf-title {
  font-family: var(--font-display); /* Fraunces */
  font-size: var(--text-h2);
  font-weight: 700;
  color: var(--text-primary);
  margin: 0;
  letter-spacing: -0.01em;
}

.adf-subtitle {
  font-family: var(--font-ui);
  font-size: var(--text-body);
  font-weight: 500;
  color: var(--text-tertiary);
  margin: var(--space-1) 0 0;
}

.adf-status-summary {
  text-align: center;
  font-size: var(--text-body);
  color: var(--text-secondary);
  margin: var(--space-4) 0 var(--space-7);
}

.adf-reasoning-title {
  font-size: var(--text-h4);
  font-weight: 600;
  color: var(--text-primary);
  margin: 0;
}

.adf-override-link {
  display: block;
  margin: var(--space-5) auto 0;
  background: transparent;
  border: none;
  font-size: var(--text-caption);
  color: var(--text-tertiary);
  text-decoration: underline;
  cursor: pointer;
}
.adf-override-link:hover {
  color: var(--text-secondary);
}
```

## Override Modal (separado, ativado via tertiary link)

Quando admin clica "Override admin", abre modal com:

- Titulo: "Override liberacao -- justificativa obrigatoria"
- Warning callout `--status-warning-bg` background: "Esta acao sera registrada como discrepancia e auditada na Tela 2"
- Textarea label: "Justificativa (minimo 50 caracteres)" — `required`, `minLength={50}`
- Select: "Categoria do override" — opcoes: "Doc em renovacao", "Aceite formal cliente", "Forca maior", "Outro (descrever)"
- Buttons: "Cancelar" (secondary) + "Confirmar override" (primary `--status-error-line` background — sinaliza "destrutivo")

Modal usa `--elev-modal` token, backdrop `--z-modal-bg` overlay 60% escuro.

## Comportamento dinamico

- **Status compliance:** se mudar durante a sessao (ex: empreiteiro acabou de subir CND valida), TrafficLight atualiza com transition 300ms `var(--ease-out)`. Nao usar bounce.
- **Notificar empreiteiro CTA:** apos click, mostrar inline confirmation "Notificacao enviada — pendencias listadas" + reset estado em 4s
- **Override modal trigger:** se medicao.canRelease === true, link override **nao aparece** (acao desnecessaria; primary CTA muda para "Liberar pagamento")

## Accessibility

- `<main>` semantico com `<h1>` unico
- `aria-live="polite"` no status-summary para screen readers receberem updates dinamicos
- ComplianceTrafficLight ja tem accessibility interna (cada dot com `aria-label`)
- Override link e `<button>` (nao `<a>`) porque dispara modal, nao navegacao
- Keyboard nav: Tab order = breadcrumb → primary CTA → secondary CTA → override link
- Reduced motion: TrafficLight transition desabilitado

## Anti-patterns (evitar)

- NAO usar layout multi-column (sidebar + main) — viola o "decision focus" intent
- NAO usar primary CTA laranja — confunde semantica de acao
- NAO usar gradientes em background ou cards — viola anti-pattern #4
- NAO mostrar override link como CTA principal — deve ser visualmente subdued
- NAO usar emoji em titulo ou subtitle — viola anti-pattern #6

## Phase 2 considerations

- **Multi-medicao bulk:** quando admin precisa decidir 5+ medicoes em sequencia, considerar wizard "1 of N" no header (sem perder o "decision focus")
- **AI suggestion inline:** Foundry Agent pode propor "Doc CND vence em 3 dias - lembre empreiteiro agora" — `AISuggestionInline` integrado entre TrafficLight e DualValueCard
- **Theme dark:** ja suportado via tokens base
