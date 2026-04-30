# ConstrutoraIndicator

Badge ou chip identificador da Construtora associada a uma medicao, doc ou notificacao no Fluxo Empreiteiro. Materializa a meia do **BKM dual-value framing** que e a contraparte do Empreiteiro logado — toda lista no PoV Empreiteiro precisa identificar instantaneamente "qual das minhas construtoras" cada item afeta. Aparece em 3 das 9 telas Phase 1 (Empreiteiro Tela 1, 2, 3).

## Props

```typescript
type ConstrutoraIndicatorProps = {
  construtoraId: string;                // ID interno (para click handlers / dedup)
  construtoraNome: string;              // nome completo (ex: "Construtora Vitta") — vira `title` para tooltip
  size: "sm" | "md";                    // sm = 18×18px (lista compacta), md = 24×24px (cards hero)
  variant: "chip" | "badge";            // chip = retangular `--radius-2` com nome inline; badge = circular `--radius-pill` com iniciais apenas
  initials?: string;                    // override iniciais; default = primeiras 2 letras significativas (CV para "Construtora Vitta")
  onClick?: () => void;                 // opcional — torna interativo (drill-down para detail Construtora)
};
```

## Token application

- **Background:** `var(--brand-primary)` (#DB592A) — laranja TRC, materializa "essa e UMA das construtoras" sem competir com `--status-*` que carrega significado de compliance
- **Texto/iniciais:** `var(--action-primary-fg)` (branco institucional) — contraste AA garantido contra laranja `#DB592A`
- **Font (iniciais):** `--text-mono` (JetBrains Mono 600) — tabular numerals + autoridade tecnica, alinhado com tratamento de identificadores em todo o sistema
- **Font (nome no chip):** `--text-label` Plus Jakarta 600
- **Border radius:**
  - `variant="badge"` → `var(--radius-pill)` (excecao permitida per anti-pattern #5; `pill` reservado a chips e badges de identidade)
  - `variant="chip"` → `var(--radius-2)` (8px) — alinhado com buttons, manten coerencia retangular do sistema
- **Padding:**
  - `variant="badge"`, `size="sm"` → 0 (forma circular pura 18×18px)
  - `variant="badge"`, `size="md"` → 0 (forma circular pura 24×24px)
  - `variant="chip"`, `size="sm"` → `0 var(--space-2)` horizontal, height 20px
  - `variant="chip"`, `size="md"` → `0 var(--space-3)` horizontal, height 28px
- **Gap entre badge+nome em chip:** `var(--space-1)` (4px)
- **Hover (somente quando `onClick`):** background `var(--brand-primary-deeper)`, transition 150ms `var(--ease-out)`

## Dimensoes precisas

| variant × size | width | height | font-size | conteudo |
|---|---|---|---|---|
| badge × sm | 18px | 18px | 9px | iniciais (1-2 chars) |
| badge × md | 24px | 24px | 11px | iniciais (1-2 chars) |
| chip × sm | auto (min 60px) | 20px | 11px | badge sm + nome truncado |
| chip × md | auto (min 80px) | 28px | 13px | badge md + nome completo |

## Iniciais — algoritmo default

Se `initials` nao fornecido:

1. Split em palavras `construtoraNome.split(/\s+/)`
2. Filtrar palavras genericas: `["construtora", "construcoes", "engenharia", "ltda", "sa", "me", "epp", "do", "de", "da", "dos", "das", "e"]` (case-insensitive)
3. Pegar **primeira letra** das 2 primeiras palavras significativas resultantes
4. Uppercase + concatenar

**Exemplos:**
- "Construtora Vitta" → `["Construtora", "Vitta"]` → filtra → `["Vitta"]` → 1 palavra significativa → fallback: pega 2 primeiras letras de Vitta → `VI` ❌
- (corrigido) **Quando apos filtro restar 1 palavra:** pegar primeira letra dessa palavra + primeira letra da palavra original imediatamente apos `Construtora` (mesmo se generica) → "Construtora Vitta" → `CV` ✓
- "JFL Engenharia e Construcoes" → filtra → `["JFL"]` → pega 2 primeiras letras → `JF` ✓
- "VL Engenharia" → filtra → `["VL"]` → pega 2 primeiras letras → `VL` ✓
- "Andrade Gutierrez" → filtra → `["Andrade", "Gutierrez"]` → `AG` ✓

## Markup pattern

### Badge (size sm, lista compacta)

```tsx
<span
  className="construtora-indicator"
  data-variant="badge"
  data-size="sm"
  title={construtoraNome}
  role={onClick ? "button" : "img"}
  aria-label={`Construtora: ${construtoraNome}`}
  onClick={onClick}
>
  {initials || computeInitials(construtoraNome)}
</span>
```

### Chip (size md, cards hero)

```tsx
<span
  className="construtora-indicator"
  data-variant="chip"
  data-size="md"
  role={onClick ? "button" : "img"}
  aria-label={`Construtora: ${construtoraNome}`}
  onClick={onClick}
>
  <span className="ci-badge">{initials || computeInitials(construtoraNome)}</span>
  <span className="ci-name">{construtoraNome}</span>
</span>
```

## CSS

```css
.construtora-indicator {
  display: inline-flex;
  align-items: center;
  background: var(--brand-primary);
  color: var(--action-primary-fg);
  font-family: var(--font-mono);
  font-weight: 600;
  user-select: none;
  white-space: nowrap;
  flex-shrink: 0;
}

/* badge variant */
.construtora-indicator[data-variant="badge"] {
  justify-content: center;
  border-radius: var(--radius-pill);
}
.construtora-indicator[data-variant="badge"][data-size="sm"] {
  width: 18px;
  height: 18px;
  font-size: 9px;
}
.construtora-indicator[data-variant="badge"][data-size="md"] {
  width: 24px;
  height: 24px;
  font-size: 11px;
}

/* chip variant */
.construtora-indicator[data-variant="chip"] {
  border-radius: var(--radius-2);
  gap: var(--space-1);
}
.construtora-indicator[data-variant="chip"][data-size="sm"] {
  padding: 0 var(--space-2);
  height: 20px;
  font-size: 11px;
}
.construtora-indicator[data-variant="chip"][data-size="md"] {
  padding: 0 var(--space-3);
  height: 28px;
  font-size: 13px;
}
.construtora-indicator[data-variant="chip"] .ci-badge {
  /* mini-badge dentro do chip */
  background: rgba(255, 255, 255, 0.20);
  border-radius: var(--radius-pill);
  padding: 2px 4px;
  font-size: inherit;
  line-height: 1;
}
.construtora-indicator[data-variant="chip"] .ci-name {
  font-family: var(--font-sans);
  font-weight: 600;
}

/* interactive */
.construtora-indicator[role="button"] {
  cursor: pointer;
  transition: background 150ms var(--ease-out);
}
.construtora-indicator[role="button"]:hover {
  background: var(--brand-primary-deeper);
}
.construtora-indicator[role="button"]:focus-visible {
  outline: 2px solid var(--focus-ring);
  outline-offset: 2px;
}
```

## Accessibility

- `role="img"` (estatico) ou `role="button"` (interativo) sinaliza semantica para screen readers
- `aria-label` sempre traduz para "Construtora: [nome completo]" — iniciais sao decorativas, nome e a informacao
- `title` no badge fornece tooltip com nome completo no hover (badge mostra so iniciais)
- Cor `--brand-primary` **nunca** e o unico canal — sempre acompanhada de iniciais ou nome
- `:focus-visible` ring usa token `--focus-ring` consistente com botoes do sistema

## Anti-patterns (evitar)

- NAO usar emoji ou icone Lucide dentro do badge (📷 🏢) — viola anti-pattern #6 e ofusca as iniciais
- NAO usar `--status-*` colors (verde/amarelo/vermelho) — confundiria com compliance status. ConstrutoraIndicator carrega **identidade** (qual construtora), nao **estado** (compliance)
- NAO inverter cores (background branco + texto laranja) — perde a saturacao reconhecivel da brand mark
- NAO usar para a Construtora **logada** (PoV Admin) — PoV Admin nao precisa identificar "qual construtora" porque ele *e* a construtora. Componente e **PoV Empreiteiro only**

## Onde aparece

| Tela | Variant | Size | Notas |
|---|---|---|---|
| Empreiteiro Tela 1 (Dashboard) | badge | sm | um por item da lista compacta, alinhado a esquerda |
| Empreiteiro Tela 2 (Upload) | badge | sm | grupo de N badges (uma por construtora afetada por aquele doc), wrap com gap `--space-1` |
| Empreiteiro Tela 3 (Renovacao) | chip | md | um chip dentro de cada card numerado, identificando a construtora cuja renovacao expira |
