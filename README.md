# TRC Design System

> **Architectural Compliance Modernism** — design-system source-of-truth para o produto TRC (Compliance Trabalhista de Construção Civil).
>
> Extraido de `trc-platform/docs/specs/2026-04-27-frente-b-design-system-spec.md` (v2 TRC brand grounding) em S161.

## Proposito deste repo

Este repo e a **fonte de verdade auditavel** do design-system TRC. Ele existe para ser ingerido por ferramentas de geracao visual (Bolt.new como ferramenta primaria em S161+) sem que essas ferramentas se tornem fonte de verdade.

**Pattern arquitetural:** delegacao com auditoria via Git — ferramenta visual gera output (telas, Storybook), edits sao **proibidos** in-tool, audit acontece via `git diff` neste repo.

## Como usar (Bolt.new)

1. Em Bolt: Team Settings -> Design Systems -> Add -> "From GitHub" -> apontar para este repo
2. Aguardar ingestao (Bolt gera Storybook interno read-only)
3. Em projetos Bolt: prompt deve invocar tokens/componentes deste design-system explicitamente
4. **Nunca editar Storybook in-Bolt.** Mudancas voltam aqui via PR -> Bolt re-ingere.

## Direcao visual: "Architectural Compliance Modernism"

TRC opera no centro de uma intersecao especifica: **construcao civil brasileira** (cultural, fisica, regulamentada) × **compliance trabalhista** (juridico, auditoria, governance) × **enterprise B2B** (deliberacao, decisao agregada, multi-stakeholder). Esta intersecao nao e SaaS generico nem fintech polido; e algo mais aproximado a um **caderno de arquiteto compliance-aware** — precisao tecnica, materialidade tatil, dimensionamento explicito.

A marca TRC ja carrega esta intersecao **literalmente** no logo: duas formas espelhadas (azul superior + laranja inferior) como duas paginas dobradas frente a frente. O design pre-existe ao spec e dialoga diretamente com o **BKM dual-value framing** (Frente A) — a co-responsabilidade trabalhista bilateral. **Empreiteiro + Construtora = as duas asas do logo.** Toda decisao visual reforca essa dualidade equilibrada (nao hierarquica), sem que um lado domine o outro.

A paleta deriva diretamente da marca: **TRC orange `#DB592A`** como cor primaria (acento de acao, hero CTAs, brand mark) e **TRC blue `#2563B5`** como secundaria de marca (autoridade tecnica, links, navegacao). Ambas vivem em equilibrio — nem o "purple-gradient SaaS" generico, nem o azul-corporate-banco, nem o laranja-startup-fast. Sao as cores da TRC. Backgrounds em **branco institucional** (`#FFFFFF` honra a marca RN existente) com surface secundaria `#F8F9FA` sutil para criar profundidade sem ruido.

A tipografia comunica autoridade tecnica sem rigidez burocratica. **Fraunces** (display variable serif com eixo optical) carrega o peso editorial dos numeros R$ heroicos. **Plus Jakarta Sans** (BR-friendly, suporta acentuacao portuguesa nativa) sustenta o UI inteiro. **JetBrains Mono** isola valores monetarios e identificadores tecnicos com tabular numerals.

O sistema espacial e baseado em **8pt grid** mas adota convencoes de **dimensionamento arquitetonico** — shadows estratificadas como camadas de calque, bordas com pesos 1-2px, raios de canto restritos (4/8/12/16px, sem 9999px). Microinteractions usam easings de **engenharia decisiva** (cubic-bezier(0.16, 1, 0.3, 1)), evitando bouncing elastico que sinalizaria toy-product. Tema claro (Phase 1 default) e **tema escuro** sao tratados como first-class.

**A diferenciacao que fica gravada:** a sensacao de que nada na tela e arbitrario — toda dimensao tem proposito, toda cor e uma decisao da marca, toda transicao tem causa.

## Estrutura do repo

```
trc-design-system/
├── README.md           # este arquivo (filosofia + contrato + brand)
├── tokens.md           # typography, color (light+dark), spatial, depth, motion + Tailwind config
├── anti-patterns.md    # 8 itens "NAO fazer" (anti generic-AI checklist)
└── components/
    ├── DualValueCard.md
    ├── ComplianceTrafficLight.md
    ├── AISuggestionInline.md
    ├── StatusDot.md
    ├── KPICard.md
    ├── RiskScoreBar.md
    ├── ActionableNotificationCard.md
    └── BreadcrumbStack.md
```

## Token nomenclature note (v1 -> v2 migration)

O spec original (S154 v1) usava nomenclatura `--paper-*`, `--indigo-*`, `--status-{block,warn,ok}-*`. A revisao v2 (TRC brand grounding) renomeou para `--surface-*`, `--brand-*`, `--text-*`, `--status-{error,warning,success}-*`. **Componentes neste repo usam exclusivamente tokens v2** (canonical names em `tokens.md`).

Mapeamento aplicado durante a extracao:

| v1 (legacy) | v2 (canonical) |
|---|---|
| `--paper-50` / `--paper-100` | `--surface-bg` / `--surface-secondary` |
| `--paper-200` | `--border-subtle` ou `--surface-tertiary` (contextual) |
| `--paper-300` | `--border-default` |
| `--paper-500` / `--paper-700` / `--paper-900` | `--text-tertiary` / `--text-secondary` / `--text-primary` |
| `--indigo-50` | `--brand-secondary-soft` |
| `--indigo-500` | `--brand-secondary` |
| `--indigo-700` | `--brand-secondary-pressed` |
| `--pure-white` | `#FFFFFF` ou `--text-inverse` |
| `--status-block-*` | `--status-error-*` |
| `--status-warn-*` | `--status-warning-*` |
| `--status-ok-*` | `--status-success-*` |

## Lineage

- **Origem:** `trc-platform/docs/specs/2026-04-27-frente-b-design-system-spec.md` (S154, APPROVED)
- **Skill aplicada:** `frontend-design` (Anthropic-verified) durante criacao do spec
- **Ancorado em:** Frente A — 3 Fluxos Hi-Fi (BKM dual-value framing)
- **Sessao de extracao:** S161 (pivot pos-Lovable, Bolt-only validado)
- **Workflow ID:** 34883895-ea02-4301-b096-3d11ab1d1e0e

## License

Privado. Uso interno TRC + ferramentas autorizadas (Bolt.new, Cursor).
