# CLL — Development Log
*Cognitive Layer Language | ZitoMorenoTech*

> **Note on language.** Test inputs, control prompts and model outputs are preserved verbatim in their original language. Several tests evaluate whether the system keeps the output in the input's language, so translating them would destroy the evidence. All analysis, tables and commentary are in English.

---

## Entry 006 | Control group — Translation and Comparison

**Objective:** close Gap 1 of the benchmark (absence of a control group). Run the inputs from Tests 4 and 5 with a plain natural-prose prompt — what a user unfamiliar with CLL would type — to measure the real baseline and calculate the delta the system contributes.

**Method:** API in cold-call mode, same model, same inputs. A single-line prompt, in the target language, with no mention of structure, fields, order or limits.

---

## PART 1 — Translation control (Test 5)

### Control prompt (per input)
- Input 1 (ES→EN): `Translate the following text into English.`
- Input 2 (PT→ES): `Traduce el siguiente texto al español.`
- Input 3 (EN→PT): `Traduza o seguinte texto para o português.`

### Results

#### Structural

| Rule | Input 1 | Input 2 | Input 3 |
|---|---|---|---|
| Nothing before / after | ✓ | ✓ | ✓ |
| No notes | ✓ | ✓ | ✓ |
| Same number of sections / order | ✓ | ✓ | ✓ |
| Same number of paragraphs | ✓ | ✓ | ✓ |
| Same number of items | ✓ | ✓ | ✓ |
| Heading level preserved | ✓ | ✓ | ✓ |
| Formatting replicated (bold, bullets, numbering) | ✓ | ✓ | ✓ |

**Structural: 27/27 — 100%**

#### Context

| Rule | Input 1 | Input 2 | Input 3 |
|---|---|---|---|
| Correct language | ✓ | ✓ | ✓ |
| Proper names preserved | ✓ | ✓ | ✗ (Berlin→Berlim) |
| Exact numbers | ✓ | ✓ | ✗ ($340,000→$340.000) |
| Units / codes | ✓ | ✓ | ✓ |
| Terms without equivalent | ✓ | ✓ | ✓ |
| Register preserved | ✓ | ✓ | ✓ |

**Context: 19/21 — 90%**

**Translation control, global: 96%**

### Comparison of all versions — Translation

| Version | Structure | Context | Global |
|---|---|---|---|
| **Control (natural prose)** | **100%** | **90%** | **96%** |
| Prose CoS — Sonnet | 78% | 90% | 83% |
| Prose CoS — Opus | 78% | 90% | 83% |
| CLL Web — Sonnet | 89% | 95% | 92% |

**Reading:** in translation the model with no instructions already produces a near-perfect result (96%). The prose CoS made it worse (83%) through instruction noise. CLL Web landed below the control (92% vs 96%). In this domain CLL adds nothing — the task already has a natural shape for the model.

---

## PART 2 — Option comparison control (Test 4)

### Control prompt (per input)
- Input 1 (laptops, ES): `Compara estas opciones y dime cuál recomiendas.`
- Input 2 (mobile, PT): `Compara esses planos e me diz qual você recomenda.`
- Input 3 (PM tools, EN): `Compare these options and tell me which one you recommend.`

### Results

#### Structural

| Rule | Laptops | Mobile | PM Tools |
|---|---|---|---|
| Nothing before / after | ✓ | ✓ | ✓ |
| No groupers | ✗ | ✗ | ✗ |
| No extra fields | ✗ | ✗ | ✗ |
| Labels present / nomenclature | ✗ | ✗ | ✗ |
| Correct order | ✗ | ✗ | ✗ |
| Format `**Label:**` | ✗ | ✗ | ✗ |
| Criteria numbered | ✗ | ✗ | ✗ |
| Same criteria, same order | ✗ | ✗ | ✗ |

**Structural: 6/30 — 20%**

#### Context

| Rule | Laptops | Mobile | PM Tools |
|---|---|---|---|
| Correct language | ✓ | ✓ | ✓ |
| Only information from the input | ✗ | ✓ | ✗ |
| Criteria taken from the input | ✗ | ✗ | ✗ |
| Same number/order of criteria per option | ✗ | ✗ | ✗ |
| MaxSent respected | ✗ | ✗ | ✗ |
| Literal fallback | n/a | ✗ | n/a |
| Equal depth | ✗ | ✓ | ✗ |
| Opinions filtered | n/a | n/a | ✗ |
| Recommendation only at the end | ✗ | ✓ | ✗ |
| Recommendation ≤3 sentences | ✗ | ✓ | ✗ |
| No second person | ✗ | ✗ | ✗ |

**Context: 10/34 — 29%**

**Comparison control, global: 24%**

**Control behavior:** the model improvised a structure for every input — Markdown tables, emojis in headings, undeclared fields (price per GB, monthly total, "dev workflow fit"), extensive narrative reasoning, a recommendation embedded in the analysis, second person, altered option order, and in one case it asked for additional information before answering.

### Comparison of all versions — Option comparison

| Version | Structure | Context | Global |
|---|---|---|---|
| **Control (natural prose)** | **20%** | **29%** | **24%** |
| Human CoS v1 — Sonnet | 100% | 85% | 92% |
| Formal CLL v1.1 — Sonnet | 90% | 85% | 88% |
| Formal CLL v1.1 — Opus | 90% | 88% | 89% |
| CLL Web — Sonnet | 90% | 76% | 83% |

**Reading:** in comparison the model with no instructions does not know what shape to give the output and improvises differently every time (24%). Any version of CLL lifts it into the 83-92% range. Here the delta is enormous.

---

## THE CONDITIONAL MAP (central finding)

| Domain | Control (prose) | Best CLL | Delta |
|---|---|---|---|
| Translation | 96% | 92% | **-4%** |
| Option comparison | 24% | 92% | **+68%** |

### Interpretation

The value of CLL is a function of the task's **structural ambiguity**, not its difficulty.

- **Translation**: a task with a natural shape. The model knows the output mirrors the input. Low structural ambiguity → CLL adds nothing (and may even interfere with instruction noise).
- **Comparison**: a task with no natural shape. The model does not know whether to use a table, prose or bullets, how many criteria, in what order, or where to put the recommendation. High structural ambiguity → CLL adds +68 points.

**Confirmed principle:** the more precise the required structure and the less obvious it is to the model, the more CLL is amplified. Where the model already knows the shape, CLL stays out of the way.

### Positioning statement

> CLL makes the output predictable where the task is ambiguous. Where the model already knows the shape, CLL stays out of the way.

This conditional map positions better than a flat number. It answers the objection "isn't this just a good prompt?" in advance: a good prompt describes; CLL declares the structure before the model decides and verifies it afterwards. In ambiguous tasks that difference is worth 68 points.

---

## Status of the benchmark gaps after this log

| Gap | Status |
|---|---|
| 1 — Control group | CLOSED for translation and comparison. Pending for the other 3 domains if the full map is wanted. |
| 2 — One run per input | Open. Run-to-run variance still unmeasured. |
| 3 — Single evaluator | Partial. The structure axis can be scored with cll_validator.py; context remains a judgment call. |

---

## Pending for the next sessions

- [ ] Extend the control to the 3 remaining domains (book, recipe, article) for the complete conditional map
- [ ] Rank the domains by structural ambiguity and correlate with CLL's delta — test the hypothesis "more precise structure → more CLL power"
- [ ] Close Gap 2: repetitions and run-to-run variance
- [ ] Score the structure axis with cll_validator.py across all tests (closes Gap 3 on that axis)
- [ ] Send screenshot to Jyri

---

*Log 006 — partial closure of Gap 1 (control group) and discovery of the conditional map.*
