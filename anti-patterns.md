# Anti-Generic-AI Checklist (9 itens NAO fazer)

> Esta lista e prescritiva. Toda violacao deve ser flagged em code review.
>
> Razao de existir: LLMs de geracao visual (Bolt incluido) tendem a reproduzir os mesmos cliches de "AI-generated UI". Esta lista nomeia esses cliches e prescreve substitutos alinhados com o brand TRC.

## NAO #1: NUNCA usar Inter, Roboto, Arial, system-ui-sans, ou Helvetica

**Por que e generic AI slop:** as 5 mais sugeridas pelos LLMs default. Resultado: produto "parece todos os outros".

**Substituir por:** Plus Jakarta Sans (UI body), Fraunces (display), JetBrains Mono (numerals).

---

## NAO #2: NUNCA usar purple/violet (qualquer shade #6B21A8, #7C3AED, #8B5CF6, ChatGPT-purple, Stripe-purple)

**Por que:** assinatura visual de SaaS-AI generico. Imediatamente lista "feito por LLM".

**Substituir por:** TRC orange `#DB592A` (primary) + TRC blue `#2563B5` (secondary). Ambas vivem em equilibrio, refletindo o logo dual-shape.

---

## NAO #3: NUNCA aceitar `bg-white` sem refinamento — use o token brand TRC

**Por que:** o branco puro `#FFFFFF` e a base institucional TRC (continuidade RN app), MAS aplicado sem decisao adicional vira "Tailwind-default sterile". A diferenciacao vem de **estratificacao com surface secundaria `#F8F9FA`** + uso correto de cards elevated + tokens TRC consistentes (laranja brand para CTA, azul para links). Branco isolado sem sistema = AI slop. Branco como token de marca dentro de um sistema = autoridade institucional.

**Substituir por:** uso disciplinado de
- `--surface-bg` (`#FFFFFF`) — page bg
- `--surface-secondary` (`#F8F9FA`) — sections, panels, hero containers
- `--surface-tertiary` (`#F1F3F5`) — nested panels, table headers
- `--surface-elevated` (`#FFFFFF`) — cards over secondary

Em modais e hero sections, considerar `--surface-secondary` como background principal para criar profundidade.

---

## NAO #4: NUNCA usar gradientes radial/linear em backgrounds de hero ou cards

**Por que:** "AI generated landing page" cliche. Especialmente roxo-pra-rosa, azul-pra-roxo.

**Substituir por:** solid `--surface-bg` ou `--surface-secondary` + sutil noise/grain texture (CSS `background-image: url(noise.svg)` 1-3% opacity) se precisar de atmosfera.

---

## NAO #5: NUNCA usar `border-radius: 9999px` em buttons normais

**Por que:** pill-buttons em todos os lugares = iOS-blob aesthetic, cookie-cutter.

**Substituir por:** `--radius-2` (8px) para buttons. `--radius-pill` (9999px) reservado **apenas** para filter chips e badges de status.

---

## NAO #6: REPLACE every emoji with a specific Lucide React icon

**Por que:** Emoji rendering varia cross-platform (Apple vs Windows vs Android), e LLMs de geracao visual (Bolt incluido) tendem a inserir emoji por default quando precisam expressar status — resulta em UI unprofessional e generic. Lucide React icons sao open-source, vetoriais (escalam sem perda), renderizam identicos cross-platform, e respondem a `currentColor` do CSS — alinhado com o tema dual brand TRC (laranja/azul).

**Substituir por (mapeamento prescritivo — nao opcional):**

| Concept                              | NAO usar           | Lucide icon (USE THIS) |
|---|---|---|
| Document / file                      | 📄 📋 📁           | `FileText` (doc), `Folder` (group), `FileCheck` (validated) |
| Validated / OK / completed           | ✓ ✅ 👍            | `Check` (inline), `CheckCircle2` (status), `BadgeCheck` (verified) |
| Warning / atencao                    | ⚠ ⚠️ 🚨           | `AlertTriangle` (general), `AlertCircle` (info-warning) |
| Blocked / rejected / forbidden       | 🚫 ❌ 🛑           | `Ban` (forbidden), `XCircle` (rejected), `ShieldAlert` (security block) |
| Notification / alert                 | 🔔 📢              | `Bell` (notif), `BellRing` (active notif) |
| AI / Agent / spark                   | ✨ 🤖 🪄           | `Sparkles` (AI suggestion), `Bot` (agent), `Zap` (action AI) |
| Money / R$ / financial               | 💰 💵 💸           | `DollarSign`, `Receipt`, `Banknote` |
| Time / urgency                       | ⏰ ⏳ 🕒            | `Clock` (time), `Hourglass` (urgent waiting), `Timer` (countdown) |
| User / profile                       | 👤 👥              | `User` (single), `Users` (multiple) |
| Home / dashboard                     | 🏠 🏢              | `Home` (home), `Building2` (construtora/empresa) |
| Search / find                        | 🔍 🔎              | `Search` (general), `Filter` (filter list) |
| Settings / config                    | ⚙ ⚙️              | `Settings` (gear), `SlidersHorizontal` (preferences) |
| Calendar / date                      | 📅 📆              | `Calendar` (date), `CalendarClock` (date+time) |
| Phone / call                         | 📞 📱              | `Phone` (call), `Smartphone` (mobile device) |
| Mail / message                       | ✉ 📧              | `Mail` (email), `MessageCircle` (chat), `MessageSquare` (sms) |
| Compliance / shield                  | 🛡 🔒              | `Shield` (compliance), `ShieldCheck` (compliant), `Lock` (locked) |
| Escalate / juridical                 | ⚖ 📑              | `Scale` (juridical), `Gavel` (verdict) |
| Trend up / down                      | 📈 📉              | `TrendingUp`, `TrendingDown` |
| Arrow / direction                    | → ← ↑ ↓ (text)    | `ChevronRight`, `ChevronLeft`, `ArrowUpRight` (external) — text arrows toleraveis em breadcrumb separator (vide BreadcrumbStack `›`) e contextos tipograficos |

**Regras de aplicacao Lucide:**

- `stroke-width: 1.5px` (consistente em todo o sistema; nao mistura com 2px ou 1px)
- `color: currentColor` (herda do contexto via CSS) — nunca hardcode hex
- Tamanho: 16px (inline texto), 20px (buttons/tabs), 24px (cards/headers), 32px+ (hero/empty-states)
- Importar via `import { Check, Bell, FileText } from 'lucide-react'` — nao importar pacote inteiro
- Emoji ainda sao OK em **conteudo de usuario** (mensagens digitadas, comments) — NUNCA em UI chrome ou data-driven status

**Casos limite:**
- Texto que nomeia a feature (ex: "Compartilhar via WhatsApp 💚") — `MessageCircle` ou logo oficial WhatsApp (sem emoji)
- Empty state copy (ex: "Tudo em dia! 🎉") — substitui por `CheckCircle2` em verde + sem emoji no texto
- Notification preview (ex: "Você tem 3 docs ⏰ vencendo") — `Clock` icon ANTES da copy, sem emoji

---

## NAO #7: NUNCA usar elastic/spring/bounce easings em animacoes

**Por que:** "toy product" aesthetic, sinaliza app-de-consumo nao enterprise tool.

**Substituir por:** `cubic-bezier(0.16, 1, 0.3, 1)` (ease-out engineering) para state changes. Spring **apenas** em number counters de hero KPI (uma excecao tematicamente justificada: o numero "rola" como contador analogico).

---

## NAO #8: NUNCA usar drop-shadows simetricas/genericas (`box-shadow: 0 4px 6px rgba(0,0,0,0.1)`)

**Por que:** Tailwind default shadow, lazy.

**Substituir por:** `--elev-1` ate `--elev-4` — 2-3 layered shadows com offsets/spreads variados que evocam camadas de calque.

---

## NAO #9: NUNCA criar layout/component viewport-naive (sem comportamento responsivo declarado)

**Por que:** LLMs de geracao visual (Bolt incluido) **geram literalmente** o que esta no spec. Se o spec nao menciona breakpoint, Bolt assume largura unica → bugs catastroficos em mobile (texto vertical palavra-por-palavra) + WEB wide (coluna estreita perdida em mar branco). Esse foi o catch S163 que motivou esta regra (re-render mobile S20 Ultra mostrou AISuggestionInline com texto vertical 1-palavra-por-linha; web 1920px mostrou admin-decision-focus com 30% aproveitamento percebido como "estranho/bug").

**Substituir por (mapeamento prescritivo — nao opcional):**

| Aspecto | NAO faca | FACA |
|---|---|---|
| Page layout max-width | `max-width: 720px` absoluto sem media queries | Escala 4-tier via `@media` + tokens `--column-*` (`fluid` mobile, `reading` 640 tablet, `decision` 720 desktop, `spread` 920 wide) |
| Componente em container variavel | `min-width: 280px` rigido (quebra sidebar 220px) | `container-type: inline-size` + `@container` queries com tokens `--cb-compact/medium/wide` |
| Grid de cards (4 itens) | `grid-template-columns: repeat(4, 1fr)` fixo (quebra palavra mobile) | Responsive grid via container queries: 2x2 narrow → 4-col wide; preservar hierarquia visual (criticos top, secundarios bottom) |
| Texto inline em flex/grid container | sem `overflow-wrap` declarado (gera `width: max-content` default → texto vertical) | `overflow-wrap: break-word; word-break: normal; hyphens: none; min-width: 0` no texto + `min-width: 0` em todos flex/grid children |
| Sidebar em viewport variavel | sempre visivel ou hamburger-collapse | bottom tab bar (mobile + tablet portrait), sidebar 240px (tablet landscape + desktop) — orientation-aware |
| Wide screen (>=1440px) | manter coluna estreita central sem treatment (vira "perdida em mar branco") | Coluna 920px max + atmospheric treatment (technical-grid lateral subtle + column elevation hairline) — vide admin-decision-focus.md "Wide treatment" |

**Verificacao obrigatoria pos-spec:** todo layout/component deve responder explicitamente as perguntas:

1. "Como isso renderiza em viewport 375 / 412 / 768 / 1024 / 1440 / 1920?"
2. "Como isso renderiza em container 220 / 480 / 720 / 920px (componentes)?"

Se a resposta for "nao sei" para qualquer um, adicionar comportamento explicito ao spec ANTES de commit. Validar pos-ingest com **visual smoke test em 3 viewports minimo** (mobile 412, tablet 768, desktop 1440) — substituto #6 do docs-only verification.

**Casos limite:**
- Componentes que sempre vivem em container fixo (ex: badge inline em texto) podem omitir container query — declarar **explicitamente** "Single-context, no responsive needed" no spec
- Layouts que sao deliberadamente fixed-width (ex: phone-mockup em landing page) podem omitir media queries — declarar **explicitamente** "Fixed-frame layout, viewport-independent"
- Quando container queries nao estao disponiveis (ex: Tailwind 3 sem plugin), fallback para media queries com **comentario TODO** apontando o downgrade

**Validacao tecnica:** confirmar que stack alvo suporta `container-type: inline-size` antes de assumir (Tailwind 4 OK, Tailwind 3 precisa plugin, CSS native suportado em todos browsers modernos 2024+).
