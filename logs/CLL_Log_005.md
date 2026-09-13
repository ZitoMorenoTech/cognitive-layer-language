# CLL — Development Log
*Cognitive Layer Language | ZitoMorenoTech*

> **Note on language.** Test inputs and model outputs are preserved verbatim in their original language. Several tests evaluate whether the system keeps the output in the input's language, so translating them would destroy the evidence. All analysis, tables and commentary are in English.

---

## Entry 005 | Thursday, 4 September 2026

**Test objects:**
1. Test 5 — Translation with preserved structure
2. Side experiment — Test 4 (comparison) run with a web-generated CoS

**Models:** Claude Sonnet and Claude Opus (via API and via the CLL web compiler)

---

# PART 1 — TEST 5: TRANSLATION WITH PRESERVED STRUCTURE

## Domain note

This is the least deterministic domain in the experiment. Translating means choosing between synonyms and adapting register — lexical content cannot be fixed. The value of CLL here is preserving STRUCTURE (sections, paragraphs, lists, formatting), not words.

## Test inputs

### Input 1 — Technical document (Spanish → English)

```
## Resumen del proyecto

El sistema procesa **1,200 transacciones por segundo** con una latencia promedio de 45ms. La arquitectura se divide en tres capas:

1. **Capa de ingesta**: recibe datos de 47 fuentes externas
2. **Capa de procesamiento**: aplica reglas de negocio y validación
3. **Capa de salida**: distribuye resultados a los sistemas downstream

## Requisitos técnicos

El despliegue requiere un mínimo de 8 nodos con 32GB RAM cada uno. El almacenamiento utiliza PostgreSQL 16 con particionamiento por fecha.

## Limitaciones conocidas

- No soporta procesamiento en tiempo real para lotes superiores a 10,000 registros
- La integración con SAP requiere el módulo RFC que no está incluido en la licencia base
- El failover automático tiene un tiempo de recuperación de 12 segundos
```

### Input 2 — Informal text (Portuguese → Spanish)

```
## Sobre o projeto

A gente começou esse projeto em março de 2024, lá no escritório de Campinas. O time tem 5 devs, 2 QAs e um PO que também faz de Scrum Master quando precisa.

## O que já fizemos

- Migração do banco de Oracle pra PostgreSQL (demorou 3 sprints)
- Implementação do CI/CD com GitHub Actions
- Deploy automatizado no AWS usando Terraform

## Próximos passos

Semana que vem a gente vai começar os testes de carga. O objetivo é aguentar **500 requests por segundo** sem derrubar. Depois disso, o plano é integrar com o sistema de billing deles, que usa uma API REST bem chatinha de trabalhar.
```

### Input 3 — Mixed content (English → Portuguese)

```
## Team Overview

Our engineering team is distributed across three offices:

- **São Paulo**: 12 engineers, frontend and mobile
- **Berlin**: 8 engineers, backend and infrastructure
- **Austin**: 5 engineers, data and ML

## Current Sprint

We're in Sprint 14 of Q3. The velocity has stabilized at 42 story points per sprint. Two blockers remain:

1. The OAuth2 integration with Keycloak is delayed pending security review
2. The data pipeline migration from Airflow to Dagster needs 3 more weeks

## Budget

Total allocated: $340,000 for Q3. Current burn rate: $28,500/week. We're on track to finish $4,500 under budget.
```

---

## User's CoS (final version, 5 blocks, no GVS)

```
Int: A-Trf TranslateContent
Ctx: D-Dta SourceText TargetLanguage
Cfg: C-Kfg NoPrelude:TRUE NoPostlude:TRUE NoNotes:TRUE
Act: X-Exe Trf
Out: Nat

Int: A-Kntx StructurePreserve
Ctx: D-Dta SectionCount SectionOrder ParagraphCount ListItems
Cfg: C-Kfg ExactSections:TRUE SameOrder:TRUE ExactParagraphs:TRUE NoMerge:TRUE NoSplit:TRUE
Act: X-Exe Enf
Out: Nat

Int: A-Trf TitleTranslate
Ctx: D-Dta SectionTitles OriginalPosition
Cfg: C-Kfg InPosition:TRUE Translated:TRUE
Act: X-Exe Apl
Out: Nat

Int: A-Kntx ContentPreserve
Ctx: D-Dta ProperNames Numbers Units Codes TermEquivalence
Cfg: C-Kfg ProperNames:NoTranslate Numbers:Exact Units:Exact Codes:Exact NoEquiv:TransPlusBracket
Act: X-Exe Enf
Out: Nat

Int: A-Trf FormatReplicate
Ctx: D-Dta Headings Bullets Numbering Bold Italic
Cfg: C-Kfg Exact:TRUE PreserveAll:TRUE
Act: X-Exe Apl
Out: Nat
```

---

## Test 5 results

### Comparison of the three versions

| Version | Structure | Context | Global |
|---|---|---|---|
| Direct human CoS — Sonnet | 78% | 90% | 83% |
| Direct human CoS — Opus | 78% | 90% | 83% |
| **CLL via Web — Sonnet** | **89%** | **95%** | **92%** |

### Analysis of the direct CoS (Sonnet and Opus, identical result)

**Structure 78% — the floor of the experiment.** Both models systematically stripped the Markdown formatting: bold, bullets, numbering and heading level disappeared across all 6 runs (3 inputs × 2 models). This is not variance — it is model behavior when translating: it prioritizes linguistic content over visual form.

**Context 90% — high.** The content translated well: names preserved, register maintained ("bem chatinha" → "quite annoying"), technical terms correct. Two failures: Input 2 came out in English instead of Spanish (likely a target configuration error), and Input 3 converted thousands separators from comma to period.

**Sonnet = Opus exactly.** The first time in the experiment that both models give an identical percentage with identical failures. Translation is a task where Opus's extra capability adds nothing — both treat Markdown as discardable decoration.

### Analysis of the CoS via Web (Sonnet)

**Structure 89% — 11 points above the direct route.** The Markdown formatting both models were destroying is now partially preserved. Input 2 came out with perfect formatting. The other two fail only on the first heading and on minor marker changes (`-` → `*`).

**Context 95% — 5 points higher.** Key change: thousands separators were preserved exactly (`$340,000` with a comma, not `$340.000`). The only persistent failure is the Input 2 language (English instead of Spanish).

**Global 92% vs 83% for the direct route — 9 points of improvement** with the same model, same inputs, same task. The only differing variable: passing through the CLL web compiler.

---

## Test 5 findings

**1. A clean apples-to-apples comparison.** Same model (Sonnet), same inputs, same task. One went through the CLL web, the other through a direct CoS. Difference: 9 points (83% → 92%). It is the cleanest result of the experiment because it isolates the compiler's effect.

**2. Sonnet via CLL Web (92%) beat Opus direct (83%).** The system made a cheaper model produce a better result than a more expensive one without CLL. This is the central product argument.

**3. The user's CoS (5 blocks) beat the formal CoS written with GVS (8 blocks).** Less is more: separate translating from preserving, use the verb Trf rather than Gen, one block per dimension without mixing, and do not use GVS where it adds nothing (there are no recurring variables in translation). Good criterion: use GVS when it helps, not by default.

**4. Markdown formatting is the natural limit in translation.** Both models destroy it on a direct run. The post-output validator is indispensable here — verifying marker preservation is a deterministic operation.

**5. Format vs content tension confirmed.** In tests 1-4 content was the challenge and structure held. In test 5 it inverts. They are two independent axes needing different mechanisms: prose for content, mechanical verification for format.

---

# PART 2 — SIDE EXPERIMENT: TEST 4 WITH THE WEB'S CoS

The option comparison (Test 4) was re-run using the CoS the web generates automatically, on Sonnet, to compare against the earlier versions.

## Results

| Version | Structure | Context | Global |
|---|---|---|---|
| Human CoS v1 — Sonnet | 100% | 85% | 92% |
| Formal CLL v1.1 — Sonnet | 90% | 85% | 88% |
| Formal CLL v1.1 — Opus | 90% | 88% | 89% |
| **CLL Web — Sonnet** | **90%** | **76%** | **83%** |

## Analysis of the Web CoS (Test 4)

**Structure 90%.** A single but constant failure: `***Criteria:***` with a triple asterisk (the Literal Preservation marker leaking through) instead of `**Criteria:**`. It appears in all three inputs.

**Context 76% — the lowest of the four versions.** Failures:
- MaxSent (3/3): responses stretched to 1-2 sentences with added interpreted context.
- Added content (Input 1): "$200-350 frente a las competidoras", a calculation not present in the input.
- Second person (3/3): "para tus necesidades", "you need" — the web CoS did not block second person.
- Translated fallback (Input 2): "Não há informação disponível" instead of the literal.
- Opinions (Input 3): "Very difficult to configure according to your description" — the judgment was kept.

## Key finding from the side experiment

**The web is only as good as the CoS it generates.** In Test 5 the web's CoS was the best (92%) because the user's translation CoS was short and precise. In Test 4 the web's CoS was the worst (83%) because the generated CoS was incomplete: no effective MaxSent control, no second-person block, no strong opinion filter.

The hand-written formal CLL v1.1 CoS — with MaxSent=1, NoSecondPerson, OpinionFilter and FallbackPreserve — scored 88-89%, five points higher than the web's.

**Conclusion:** output quality depends on CoS quality, not on whether it went through the web. The web automates conversion to CLL but does not compensate for weak source rules. The expert author remains the bottleneck — exactly as the architecture predicted.

---

## Full cumulative table (all tests)

| Test | Version | Structure | Context | Global |
|---|---|---|---|---|
| T1 — Book | V5, ES | 90% | 82% | 86% |
| T2 — Recipe | without GVS, ES | 83% | 81% | 82% |
| T2 — Recipe | with GVS, ES | 87% | 89% | 88% |
| T3 — Article | with GVS, ES | 100% | 80% | 89% |
| T3 — Article | formal CLL, EN | 96% | 100% | 98% |
| T4 — Comparison | human v1, Sonnet | 100% | 85% | 92% |
| T4 — Comparison | formal CLL, Sonnet | 90% | 85% | 88% |
| T4 — Comparison | formal CLL, Opus | 90% | 88% | 89% |
| T4 — Comparison | CLL Web, Sonnet | 90% | 76% | 83% |
| T5 — Translation | direct, Sonnet | 78% | 90% | 83% |
| T5 — Translation | direct, Opus | 78% | 90% | 83% |
| T5 — Translation | CLL Web, Sonnet | 89% | 95% | 92% |

---

## Cross-cutting observations (5 tests)

**CLL's benefit varies by domain.** Maximum in technical domains with quantitative constraints (T3: 98%). Lower in interpretive domains (T4 comparison with opinions). Different in translation (T5: format is the challenge, not content).

**A short precise CoS beats a long complete one.** Confirmed in T5 (the user's 5 blocks > 8 blocks with GVS). Modular design with one responsibility per block is the winning pattern.

**GVS is used where it helps, not by default.** Useful where there are recurring variables (T3: MaxSent, OutLang, NoData). Unnecessary in translation.

**Opinion filtering is the unsolved problem.** Neither Sonnet nor Opus handles it well. It is the clearest case where the post-output validator is indispensable.

**Sonnet vs Opus: a difference of quality, not conformity.** Percentages are close (1-3 points). Opus produces complete sentences and respects literals better; Sonnet complies in number but not always in spirit. In translation they are identical.

---

## Pending for the next sessions

- [ ] Run the control group (without CoS) for all 5 domains — the baseline
- [ ] Formalize GVS in spec v1.2 (exists in the Kernel, missing from the formal documents)
- [ ] Resolve the design tension: is $NoData/fallback translated with the output or is it an invariable literal?
- [ ] Investigate the `***` Literal marker leaking into outputs
- [ ] Add to the translation CoS: ExactListCount, a heading-level rule, a target language block
- [ ] Adjust the web's CoS generator to include NoSecondPerson and MaxSent control by default
- [ ] Send screenshot to Jyri

---

*Next entry: Friday, 5 September 2026*
