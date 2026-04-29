# Anti-Generic-AI Checklist (8 itens NAO fazer)

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

## NAO #6: NUNCA usar emoji como icon (📄 ✓ ⚠ 🔔 nao!)

**Por que:** unprofessional, cross-platform inconsistent rendering, generic.

**Substituir por:** Lucide React icons (open-source) com `stroke-width: 1.5px` consistente, color via `currentColor`.

---

## NAO #7: NUNCA usar elastic/spring/bounce easings em animacoes

**Por que:** "toy product" aesthetic, sinaliza app-de-consumo nao enterprise tool.

**Substituir por:** `cubic-bezier(0.16, 1, 0.3, 1)` (ease-out engineering) para state changes. Spring **apenas** em number counters de hero KPI (uma excecao tematicamente justificada: o numero "rola" como contador analogico).

---

## NAO #8: NUNCA usar drop-shadows simetricas/genericas (`box-shadow: 0 4px 6px rgba(0,0,0,0.1)`)

**Por que:** Tailwind default shadow, lazy.

**Substituir por:** `--elev-1` ate `--elev-4` — 2-3 layered shadows com offsets/spreads variados que evocam camadas de calque.
