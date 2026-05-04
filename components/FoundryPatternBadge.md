---
name: FoundryPatternBadge
version: 1.0.0
since: S166
---

# FoundryPatternBadge

Badge inline read-only para exibir o padrao de tendencia detectado pelo Foundry Agent em um conjunto de dados de compliance. Aparece na variant `stacked` do `InsightCard` (Card 3 — Compliance por obra) e e reservado para uso futuro em Admin Tela 3 Phase 2.

> **Audit trail:** Validated via canary `s166-engenheiro-tela3-canary.html` section `.foundry-badge[data-status=deteriorando]`, `.foundry-badge[data-status=estavel]`, `.foundry-badge[data-status=oscilatorio]`, `.foundry-badge-confidence` (CSS lines ~588-618 do canary).

---

## Relacao com AISuggestionInline

> **Pattern related:** `AISuggestionInline` (ver `AISuggestionInline.md`). Ambos surfaceiam output do Foundry Agent, mas com papeis distintos:
>
> | Dimensao | AISuggestionInline | FoundryPatternBadge |
> |---|---|---|
> | **Modo** | Interativo — CTA "Aplicar" | **Read-only** — label informativo, sem acao |
> | **Peso visual** | Banda lateral com background gradient e border-left | **Badge inline pill compacto** |
> | **Conteudo** | Sugestao de acao + confidence bar + reasoning expandivel | **Label de padrao** (1 palavra) + confidence % |
> | **Contexto** | Destaque em hero cards, banners de lista | **Inline ao final de cada row** em listas densas |
> | **Fonte** | Foundry RemediationAdvisor (sugestoes ativas) | **Foundry PatternDetector** (classifica historico) |
>
> `FoundryPatternBadge` e a **variante LIGHTER**: read-only label + confidence number, sem CTA, sem reasoning expandivel. Adequado para listas onde acoes interativas saturariam o layout.

---

## Estados (status)

Tres valores validos para `data-status`:

| Status | Significado | Background | Cor do texto |
|---|---|---|---|
| `deteriorando` | Tendencia de piora progressiva no compliance | `--badge-deteriorando-bg` (`#FEE2E2`) | `--badge-deteriorando-fg` (`#B91C1C`) |
| `estavel` | Sem tendencia clara, oscilacoes dentro do normal | `--badge-estavel-bg` (`#F1F3F5`) | `--badge-estavel-fg` (`#6C757D`) |
| `oscilatorio` | Alternancia entre melhora e piora, sem direcao definida | `--badge-oscilatorio-bg` (`#FEF3C7`) | `--badge-oscilatorio-fg` (`#B45309`) |

**Por que estas tres categorias:** sao as tres classificacoes que o Foundry PatternDetector emite na Phase 1 (stub). Cobrem os cenarios operacionais reais: deterioracao indica acao urgente, estavel indica manutencao, oscilatorio indica investigacao.

**Por que vermelho para `deteriorando`:** alinha com o semaforo TRC (`--status-error-*`). Deterioracao de compliance e risco imediato — vermelho comunica urgencia sem precisar de tooltip.

**Por que cinza para `estavel`:** nao e sucesso (verde seria enganoso — "estavel" nao e "otimo"), nao e erro. Cinza neutro comunica "monitorando, sem acao necessaria".

**Por que amarelo para `oscilatorio`:** alinha com `--status-warning-*`. Oscilacao merece atencao mas nao e critica — amarelo e o sinal de "olhe com cuidado".

---

## Props

```typescript
type FoundryPatternBadgeProps = {
  status: "deteriorando" | "estavel" | "oscilatorio";
  confidence: number;    // 0-1, ex: 0.87 → exibe "87%"
  abbreviated?: boolean; // true para mobile (ver regras de abreviacao)
};
```

---

## Regras de abreviacao mobile

Em viewports mobile (<768px) ou containers estreitos (<200px), o label completo pode nao caber na linha. Usar `abbreviated={true}` para ativar abreviacoes validadas no canary:

| Status completo | Abreviacao mobile |
|---|---|
| `deteriorando` | `detr.` |
| `estavel` | `estavel` (sem abreviacao — cabe) |
| `oscilatorio` | `oscil.` |

**Por que abreviacoes pontuadas:** o ponto final sinaliza que e abreviacao (nao palavra nova), preservando legibilidade mesmo sem tooltip.

---

## Confidence number

- Fonte: `var(--font-mono)` (JetBrains Mono)
- Tamanho: `9-10px` (entre `--text-mono-xs` 11px e menor — pode usar `font-size: 9px` diretamente)
- Opacidade: `0.8` (subordinado ao label)
- Formato: `"87%"` (sem decimais, sempre arredondado)

**Por que monospace para confidence:** o numero e um valor tecnico de modelo ML, nao texto editorial. Mono alinha verticalmente em listas e preserva feel de "metrica tecnica", diferenciando de texto humano.

---

## Phase roadmap

| Phase | Comportamento |
|---|---|
| **Phase 1 (atual)** | Stub mock — label + confidence sao estaticos, definidos pelo engenheiro no seed data. Nenhuma inferencia real. |
| **Phase 2** | Conectado ao Foundry PatternDetector real. Badge recebe `patternId` e faz query ao endpoint `/foundry/patterns/{id}`. Confidence calculado sobre historico real de 90 dias. |

---

## Markup pattern

```tsx
<span
  className="foundry-badge"
  data-status={status}
  aria-label={`Padrao ${status}, confianca ${Math.round(confidence * 100)}%`}
>
  {abbreviated ? abbreviate(status) : status}
  {" "}
  <span className="foundry-badge-confidence">
    {Math.round(confidence * 100)}%
  </span>
</span>

// Helper
function abbreviate(status: "deteriorando" | "estavel" | "oscilatorio"): string {
  const map = { deteriorando: "detr.", estavel: "estavel", oscilatorio: "oscil." };
  return map[status];
}
```

---

## CSS

```css
.foundry-badge {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  padding: 2px 7px;
  border-radius: var(--radius-pill);
  font-size: 10px;
  font-weight: 600;
  line-height: 1.5;
  letter-spacing: 0.01em;
  white-space: nowrap;
  font-family: var(--font-sans);
}

/* Status colors — usando variáveis locais mapeadas de tokens de status */
.foundry-badge[data-status="deteriorando"] {
  background: var(--badge-deteriorando-bg);   /* #FEE2E2 — status-error-bg variant */
  color: var(--badge-deteriorando-fg);         /* #B91C1C — status-error-fg */
}
.foundry-badge[data-status="estavel"] {
  background: var(--badge-estavel-bg);         /* #F1F3F5 — surface-tertiary */
  color: var(--badge-estavel-fg);              /* #6C757D — text-secondary */
}
.foundry-badge[data-status="oscilatorio"] {
  background: var(--badge-oscilatorio-bg);     /* #FEF3C7 — status-warning-bg variant */
  color: var(--badge-oscilatorio-fg);          /* #B45309 — status-warning-fg */
}

/* Confidence number — monospace, subdued */
.foundry-badge-confidence {
  font-family: var(--font-mono);
  font-size: 9px;
  opacity: 0.8;
  font-weight: 500;
  letter-spacing: 0;
}
```

**Nota sobre tokens de badge:** as variaveis `--badge-*-bg` e `--badge-*-fg` sao **aliases locais** que devem ser declaradas no escopo do componente ou no arquivo de tokens do projeto apontando para os tokens semanticos existentes:

```css
/* Em tokens.md ou no scope raiz do projeto */
:root {
  --badge-deteriorando-bg: var(--status-error-bg);    /* ou #FEE2E2 */
  --badge-deteriorando-fg: var(--status-error-fg);    /* ou #B91C1C */
  --badge-estavel-bg:      var(--surface-tertiary);   /* #F1F3F5 */
  --badge-estavel-fg:      var(--text-secondary);     /* #6C757D */
  --badge-oscilatorio-bg:  var(--status-warning-bg);  /* ou #FEF3C7 */
  --badge-oscilatorio-fg:  var(--status-warning-fg);  /* #B45309 */
}
```

Isso garante que mudancas no tema (dark mode, brand update) propagam automaticamente.

---

## Anti-patterns (concept × forbidden × required substitute)

| Concept | Forbidden | Required substitute |
|---|---|---|
| Badge como CTA | Adicionar `onClick` ou link no badge | **Read-only apenas** — para acoes usar `AISuggestionInline` |
| Confidence format | `"0.87"` (float) ou `"87.3%"` (decimal) | **`"87%"`** (inteiro, percentual) — precisao falsa nao agrega |
| Confidence font | `var(--font-sans)` para o numero | **`var(--font-mono)`** — numero tecnico de modelo ML |
| Cores hardcoded | `background: #FEE2E2; color: #B91C1C` inline | **`--badge-deteriorando-bg` / `--badge-deteriorando-fg`** — aliases semanticos |
| Mobile overflow | Label completo sem tratamento em container estreito | **`abbreviated={true}`** ativa abreviacoes validadas (`detr.`, `oscil.`) |
| Status border | `border: 1px solid` colorido (visual pesado em listas) | **Apenas background + cor do texto** — pill sem border |

---

## Acessibilidade

- `aria-label` explicito: `"Padrao deteriorando, confianca 87%"` — badge comunica via texto, nao apenas cor
- Cor nao e unico canal: o label textual ("deteriorando") repete a informacao do background colorido
- Elemento `<span>` (nao button) — read-only, sem role interativo
- Reduced motion: sem animacoes no badge (nao aplicavel)
