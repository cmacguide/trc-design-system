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

Coluna central com max-width **responsivo + tratamento atmosferico nas laterais em wide**:

| Viewport | Column max-width | Token | Lateral atmosphere |
|---|---|---|---|
| Mobile (<768px) | 100% | `--column-fluid` | n/a (sem laterais) |
| Tablet (768-1023px) | 640px | `--column-reading` | `--surface-bg` puro |
| Desktop (1024-1439px) | 720px | `--column-decision` | `--surface-bg` puro + sidebar app 240px a esquerda |
| Wide (>=1440px) | **920px** | `--column-spread` | **column elevation hairline soh** (ver "Wide treatment" abaixo) — laterais permanecem `--surface-bg` puro, SEM textura decorativa |

**Por que 920px e NAO 1024+ no wide:** acima de 960px a coluna comeca a ter peso de dashboard (perde "uma decisao por vez"). 920px e o ponto onde DualValueCard tem ~440px por lado (confortavel para "EMPREITEIRO aguarda R$ 142.300" + subtitle sem aperto), mantendo asimetria 48/52% que o cerebro percebe como **intencional** (50/50 simetrico = "Bootstrap container").

**Por que NAO simplesmente 720px absoluto em todo wide:** 720/1920 = 37.5% → o cerebro interpreta como "conteudo perdido em mar branco" se nao houver atmosfera lateral. Em 1440px monitor (37.5%→50%), 720 funciona; em 1920px+, vira bug-perception. Solucao: escalar largura E adicionar treatment.

**Tudo o resto da viewport e respiracao** (`var(--surface-bg)`) — proposital, sinaliza "concentrar aqui".

## Componentes invocados

- `BreadcrumbStack` — `Medicoes / Obra X / 042/2026` no topo
- `ComplianceTrafficLight` (size=lg) — elemento visual central, gigante (4 categorias compactas com cores semaforo)
- `DualValueCard` (urgency mapeado: critical se ha bloqueio, warning se borderline, info se aprovado) — reasoning bilateral
- (modal override) input + textarea + button — quando user dispara "Override admin"

## Token application

- **Page background:** `var(--surface-bg)` (#FFFFFF)
- **Coluna central (responsiva):**
  - Mobile (<768px): width 100% (`--column-fluid`), padding `var(--space-6) var(--space-4)` (top/bottom 32px, lateral 16px)
  - Tablet (768-1023px): max-width 640px (`--column-reading`), margin auto, padding `var(--space-7) var(--space-5)`
  - Desktop (1024-1439px): max-width 720px (`--column-decision`), margin auto, padding `var(--space-8) var(--space-6)`
  - Wide (>=1440px): max-width 920px (`--column-spread`), margin auto, padding `var(--space-8) var(--space-7)`, com **column elevation hairline subtle** apenas (ver "Wide treatment" — sem grid lateral decorativo)
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

## Wide screen treatment (>=1440px) — REVISADO S163b

> **Validacao S163b:** esta secao + Action row + anti-patterns adicionados foram validados via canary HTML preview pre-push em `trc-platform/docs/assets/preview/admin-tela-1-v4-preview.html` (substituto #7 visual decision pre-validation).

**Audience-fit decision (S163b):** o tratamento original previa "atmospheric grid lateral / technical-paper texture" para evocar papel tecnico de engenharia. Validacao empirica via canary HTML preview revelou: **audience real (gestor Construtora, compliance officer) NAO processa blueprints diariamente** — semantica "papel tecnico" nao le, decoracao vira ruido percebido como "estranho/bug" em ferramenta de decisao corporativa. Ver anti-pattern explicito abaixo.

**Tratamento revisado (mantido):** apenas **column elevation hairline subtle** delimitando a area de decisao focada. Sem texturas decorativas. As laterais permanecem `--surface-bg` puro.

```css
@media (min-width: 1440px) {
  .adf-column {
    background: var(--surface-bg);
    box-shadow:
      inset 0 0 0 1px var(--border-subtle),
      0 1px 0 var(--border-subtle); /* hairline, NAO dropshadow generic */
  }
}
```

**Por que apenas hairline (e nada mais):** o hairline cumpre **funcao** (sinaliza "esta e a area da decisao") sem **decorar**. Texturas, gradientes, padroes verticais nas laterais cumprem funcao zero para audience compliance — sao soh "design language" sem semantic carrying. Hairline e o **minimo absoluto** que comunica "regiao focada" e ainda funciona em qualquer audience.

**Por que coluna nao precisa de mais presenca em wide:** com `--surface-bg` em `#FAFAF9` (warm-grey near-white, ver tokens.md surface palette) e `.adf-column` em `var(--surface-elevated)` `#FFFFFF`, ja existe **value contrast natural ~1-2%** entre coluna e laterais. O hairline reforca esse contraste. Atmosfera vem de **value layering**, nao de decoracao explicita.

## Action row (CTAs primary + secondary) — responsive

`Notificar empreiteiro` (primary) + `Ver auditoria` (secondary) ficam **side-by-side em desktop+** mas precisam **stack vertical em mobile** porque coluna 100% width nao acomoda 2 botoes confortavelmente (cada um com texto longo, primary "Notificar empreiteiro -- enviar checklist").

```css
.adf-actions {
  display: flex;
  gap: var(--space-3);
  margin-top: var(--space-6);
}
.adf-actions > button {
  flex: 1;
  min-width: 0;
  white-space: normal;       /* permite wrap natural */
  overflow-wrap: break-word;
}

@media (max-width: 767px) {
  .adf-actions {
    flex-direction: column;
  }
}
```

**Por que `flex: 1` + `min-width: 0`:** sem `min-width: 0`, flex children nao encolhem abaixo do conteudo intrinsic — texto longo "Notificar empreiteiro -- enviar checklist" forca botao >300px width, comprimindo "Ver auditoria" em ~80px e cortando texto. `min-width: 0` permite shrink ate fit container.

**Anti-pattern catched S163b:** sem `flex-direction: column` em mobile, primary CTA "Notificar empreiteiro" + secondary "Ver auditoria" lado-a-lado em coluna 412px-padding=380px deu primary ~280px + secondary ~80px, secondary ficou cortado ("Ver / auditoria"). Stack vertical com cada CTA full-width resolve.

## Anti-patterns Wide treatment (concept × forbidden × required substitute)

| Concept | Forbidden | Required substitute |
|---|---|---|
| Atmosfera lateral wide | `repeating-linear-gradient` blueprint texture, gradient meshes, padroes verticais decorativos | **Apenas `--surface-bg` puro** + column elevation hairline. Atmosfera vem de **value layering** (ground vs card), nao decoracao explicita |
| Justificativa "papel tecnico engenharia" | Aplicar para audience compliance trabalhista | **Audience real e gestor Construtora + compliance officer** — processam planilhas/PDFs/emails, NAO blueprints. Semantica "papel tecnico" nao le. Decoracao = ruido |
| Column delimitation wide | Drop-shadow generic, colored borders, dropshadow simetrico | **`box-shadow: inset 0 0 0 1px var(--border-subtle)`** (hairline subtle) — funcional sem decorativo |
| CTAs em mobile | `flex-direction: row` herdado do desktop | **`flex-direction: column`** em `<768px`, cada CTA `width: 100%` + texto sentence-wrap |

## CSS

```css
.admin-decision-focus {
  background: var(--surface-bg);
  min-height: 100vh;
}

.adf-column {
  width: 100%;
  margin: 0 auto;
  padding: var(--space-6) var(--space-4);
}

@media (min-width: 768px) {
  .adf-column {
    max-width: var(--column-reading); /* 640px */
    padding: var(--space-7) var(--space-5);
  }
}

@media (min-width: 1024px) {
  .adf-column {
    max-width: var(--column-decision); /* 720px */
    padding: var(--space-8) var(--space-6);
  }
}

@media (min-width: 1440px) {
  .adf-column {
    max-width: var(--column-spread); /* 920px */
    padding: var(--space-8) var(--space-7);
    background: var(--surface-elevated); /* white card sobre --surface-bg ground = value contrast */
    box-shadow:
      inset 0 0 0 1px var(--border-subtle),
      0 1px 0 var(--border-subtle);
  }
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
  font-family: var(--font-sans);
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
  font-size: var(--text-h3);
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
- NAO aplicar texturas/padroes verticais nas laterais wide — viola NAO#9 (layout viewport-naive expandido S163b: decoracao audience-misfit) e o subset Wide-treatment table acima. Audience compliance NAO le "papel tecnico engenharia" como semantica
- NAO deixar action row (CTAs) `flex-direction: row` em `<768px` — primary forca secondary cortado. Sempre `column` em mobile

## Phase 2 considerations

- **Multi-medicao bulk:** quando admin precisa decidir 5+ medicoes em sequencia, considerar wizard "1 of N" no header (sem perder o "decision focus")
- **AI suggestion inline:** Foundry Agent pode propor "Doc CND vence em 3 dias - lembre empreiteiro agora" — `AISuggestionInline` integrado entre TrafficLight e DualValueCard
- **Theme dark:** ja suportado via tokens base
