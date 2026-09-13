# CLL — Executive Summary (v2)
### Tests 1–5 · Control Group · Variance
*Cognitive Layer Language | ZitoMorenoTech | September 2026*

Consolidated report of the full experiment on CoS (Cognitive Operating Systems) executed via API in cold-call mode, with no prior conversational context. The experiment has three phases, integrated here in that order:

1. **Design** (Tests 1–5) — how to build a CoS that works. 12 runs, 5 domains, 7 distilled principles.
2. **Baseline** (Control group, Log 006) — how much CLL contributes against not using it. The conditional map.
3. **Reliability** (Variance, Log 007) — how stable the result is run to run.

All metrics are separated into two axes: **structural conformity** (was the form respected?) and **context conformity** (was the content and its constraints respected?). The full arc answers three distinct questions: *how to design it → how much it contributes → how consistent it is*.

> **Note on source documents.** The detailed development logs (001–007) are kept in `/logs`. Within them, test inputs and model outputs are preserved verbatim in their original language — Spanish, Portuguese and English — because several tests evaluate whether the system preserves the input language; translating that material would destroy the property being demonstrated. This summary consolidates the findings of those logs.

---

## 1. Master results table — design phase (12 runs)

| # | Domain | Version | Model | Structure | Context | Global |
|---|---|---|---|---|---|---|
| 1 | Book card | V5 line-based design, ES | Sonnet | 90% | 82% | 86% |
| 2 | Recipe | without GVS, ES | Sonnet | 83% | 81% | 82% |
| 2 | Recipe | with GVS, ES | Sonnet | 87% | 89% | 88% |
| 3 | Technical article | with GVS, ES | Sonnet | 100% | 80% | 89% |
| 3 | Technical article | formal CLL, EN | Sonnet | 96% | 100% | 98% |
| 4 | Option comparison | human v1, EN | Sonnet | 100% | 85% | 92% |
| 4 | Option comparison | formal CLL v1.1 | Sonnet | 90% | 85% | 88% |
| 4 | Option comparison | formal CLL v1.1 | Opus | 90% | 88% | 89% |
| 4 | Option comparison | CLL Web | Sonnet | 90% | 76% | 83% |
| 5 | Translation | direct CoS | Sonnet | 78% | 90% | 83% |
| 5 | Translation | direct CoS | Opus | 78% | 90% | 83% |
| 5 | Translation | CLL Web | Sonnet | 89% | 95% | 92% |

**Averages across the 12 design runs:**
- Mean structure: **89.3%**
- Mean context: **85.9%**
- Mean global: **87.6%**

**Best result:** Test 3, technical article, formal CLL in English — **98%** (Structure 96% / Context 100%).
**Worst result:** Test 2 recipe without GVS and Test 4 CLL Web — **82–83%**.

---

## 2. PHASE 1 — DESIGN: the seven discoveries

Each discovery came from a controlled experiment within tests 1–5, with numbers backing it.

### 2.1 Line-based design (label fused with position)
*Test 1.* Five ways of writing the same rules were tested:

| Version | Approach | Conformity |
|---|---|---|
| V1 | Prose paragraphs | ~50% |
| V2 | Atomized bullets | ~45% |
| V3 | Literal Preservation block | ~55% |
| V4 | Modular by line (v1) | 57% |
| V5 | Refined modular by line | 83% |

Fusing the label with the physical position of the line raised conformity from ~50% to 83%. "The first line must begin with `**Title:**`" beats "use these field names".

### 2.2 GVS (Global Variable System)
*Test 2.* Same rules, with and without global variables:

| Version | Structure | Context | Global |
|---|---|---|---|
| Without GVS | 83% | 81% | 82% |
| With GVS | 87% | 89% | 88% |

**Delta: +6% global, +8% context.** GVS resolved the two chronic failures: language and invented content. Variables declared once and referenced (`$OutLang`, `$MaxSent`, `$NoData`).

### 2.3 The language of the CoS
*Test 3.* Same design, rules in Spanish vs English:

| CoS language | Structure | Context | Global |
|---|---|---|---|
| Spanish | 100% | 80% | 89% |
| English | 96% | 100% | 98% |

**Delta: +9% global, +20% context.** A CoS in English aligns with the linguistic base of Western LLMs. Implication: write the CoS in English even when the output is in another language.

### 2.4 Quantitative limits as global variables
*Test 3.* The sentence limit (`MaxSent`) failed 3/3 in Test 1 when buried in prose. As a global variable `$MaxSent`, it was respected **6/6** in Test 3.

### 2.5 Short and precise CoS > long and complete CoS
*Test 5.* Two CoS for the same translation task: the user's 5-block version (92% via web) beat the formal 8-block version with GVS. Separate translating from preserving, use the right verb (Trf not Gen), one block per dimension, GVS only where it helps.

### 2.6 The quality of rule writing
*Tests 4 vs 5.* Rules that work are imperative, self-limiting and verifiable without judgment; rules that fail describe states and require judgment. T5 (good rules) = 92%; T4 CLL Web (weak rules) = 83%. Nine points attributable to wording alone.

| Criterion | Good rule (T5) | Weak rule (T4) |
|---|---|---|
| Imperative | "Keep", "Do not merge" | "Each criterion answered in…" (describes a state) |
| Self-limiting | Names its own violation ("do not split") | Leaves the possible error implicit |
| Verifiable without judgment | "Same number of sections" (binary) | "No more depth than another" (requires judgment) |

### 2.7 The web compiler effect
*Test 5.* Same model, same inputs, same task:

| Route | Global |
|---|---|
| Direct CoS (Sonnet) | 83% |
| CoS via CLL web (Sonnet) | 92% |

**Delta: +9%.** And further: **Sonnet via CLL Web (92%) > Opus direct (83%)** — the system made a cheaper model outperform a more expensive one.

---

## 3. PHASE 2 — BASELINE: the conditional map

*Log 006.* The translation and comparison inputs were run with a natural-prose prompt — what a user unfamiliar with CLL would type — to measure the real baseline and isolate how much the system contributes.

### The control group

| Domain | Structure | Context | Global |
|---|---|---|---|
| Translation (control) | 100% | 90% | 96% |
| Option comparison (control) | 20% | 29% | 24% |

In translation, the model without instructions already produces near-perfection. In comparison, it improvises a different structure every time — tables, emojis, undeclared fields, embedded recommendations, second person.

### The conditional map (central finding of the experiment)

| Domain | Control (prose) | Best CLL | Delta |
|---|---|---|---|
| Translation | 96% | 92% | **−4%** |
| Option comparison | 24% | 92% | **+68%** |

**The value of CLL is a function of the task's structural ambiguity, not its difficulty.**

- **Translation** — a task with a natural shape. The model knows the output mirrors the input. Low ambiguity → CLL adds nothing (and can even interfere with instruction noise).
- **Comparison** — a task with no natural shape. The model does not know what form to give the output. High ambiguity → CLL adds 68 points.

### Positioning statement

> CLL makes the output predictable where the task is ambiguous. Where the model already knows the shape, CLL stays out of the way.

This map answers the objection "isn't this just a good prompt?" in advance: a good prompt describes; CLL declares the structure before the model decides, and verifies it afterwards. In ambiguous tasks that difference is worth 68 points.

---

## 4. PHASE 3 — RELIABILITY: variance

*Log 007.* 10 runs of the same CoS (Test 3, technical article, formal CLL EN) with the same input (intermittent fasting), via API in cold-call mode, independent sessions. This is the property CLL promises and the only one tests 1–6 had not measured: not "how good?" but "how predictable?".

### Variance statistics (n = 10)

| Axis | Mean | Range | Std. deviation | Coeff. of variation |
|---|---|---|---|---|
| Structure | 98.8% | 88% – 100% | 3.6 | 3.6% |
| Context | 95.0% | 90% – 100% | 5.0 | 5.3% |
| **Global** | **96.9%** | **89% – 100%** | **3.5** | **3.6%** |

- **Low coefficient of variation on both axes.** No run deviated from the mean by more than a single percentage digit, apart from one structural outlier. Consistency is high.
- **Structure is near-deterministic** — 9 of 10 runs scored 100%. The 3.6 dispersion is produced by a single event.
- **Context is binary** — all ten values were exactly 90% or 100%, never intermediate. A run either fails (adds an extra descriptor) or it does not.

### The realistic ceiling

The mean of the 10 runs (96.9%) falls below the original run (100%). That run was a clean extraction; the realistic expectation over 10 runs for this input is **~97%, not 100%**. Measuring variance revealed that the perfect headline was a single lucky run — reporting the mean is more honest and more defensible.

### The double pass — single evaluator demonstrated live

The 10 runs were scored twice, independently. Both passes — same evaluator, identical data — differed in four cells, **all in the context axis or in arithmetic errors of structure, none in a genuine structural disagreement**. The structural errors were false positives (invented failures); the context ones were false negatives (undetected failures). This proves the context axis is not reproducible even within the same evaluator, and defines the route to closing Gap 3: structure → mechanical validator; context → judgment band.

---

## 5. The ceiling of prompting — persistent failures

These fail reproducibly across multiple tests and are NOT solved by writing better rules. They are the argument for why a post-output validator is necessary:

| Failure | Frequency | Description |
|---|---|---|
| The model paraphrases literals | 5+ tests | "Not stated" → "No data available"; `**` → `***`. It interprets instead of copying. |
| Added descriptor not in input | 5/10 runs (L007) | Inserts "middle-aged"/"healthy", categories absent from the input. |
| Opinion filtering | Test 4 | Neither Sonnet nor Opus removes opinion; they soften it. |
| Markdown format in translation | Test 5 | Both models strip bold/bullets/numbering when translating. |
| Compound uncertainty | Tests 1, 3 | Simple doubt is marked correctly; doubt with two candidates fails. |

**Cross-cutting conclusion:** the model never treats an instruction as an absolute literal — it always interprets. Prompt-based constraint has a measurable ceiling. Mechanical verification (a validator) is indispensable as the final layer.

---

## 6. The separation of the two axes (finding that unifies all three phases)

| Axis | What the three phases show together | Mechanism it needs |
|---|---|---|
| **Structure** | Near-deterministic with CLL (1 real failure in 10 runs). Without CLL it collapses only in ambiguous tasks (20% in comparison). Solved by line-based design. | `cll_validator.py` — mechanical verification, zero evaluator variance |
| **Context** | Band of 76%–100%. Depends on domain, CoS language and GVS. Dominant failure is interpretive and systematic. | Post-output validator + reporting as a judgment band, not a flat number |

**The image that sums it up:** the control without CLL is bimodal — excellent in translation (100/90), poor in comparison (20/29); its quality depends entirely on the type of task. All CLL conditions cluster in a narrow range (78–100 structure, 88–95 context). CLL does not make the output *better* everywhere — it makes it **land in the same reliable zone regardless of the task's ambiguity**. It compresses dispersion, and Phase 3 proves that compression was not a single-run fluke.

---

## 7. Sonnet vs Opus

| Test | Sonnet | Opus | Difference |
|---|---|---|---|
| 4 — Comparison | 88% | 89% | +1% Opus |
| 5 — Translation | 83% | 83% | 0% |

The conformity difference is 0–1 point. The real difference is one of **quality, not percentage**: Opus writes complete sentences, respects the literal fallback and maintains uniform depth; Sonnet complies in number but not always in spirit. A capability threshold exists — above it, the ceiling is set by the CoS, not the model. From Haiku to Sonnet there is a jump; from Sonnet to Opus it flattens.

---

## 8. Design principles (distilled)

1. **Fuse the label with the line position** — do not declare format separately.
2. **Write the CoS in English** — even when the output is in another language (+20% context).
3. **Use GVS for recurring invariants** — language, limits, fallbacks; not by default.
4. **Quantitative limits belong in global variables** — not buried in prose.
5. **Rules must be imperative, self-limiting and verifiable without judgment.**
6. **Short and precise CoS > long and complete** — one block per responsibility.
7. **Verify afterwards** — no prompt guarantees literals; the validator closes the loop.
8. **Apply CLL according to the task's structural ambiguity** — maximum contribution where the model does not know the shape; minimal (or negative) where it already does.
9. **Report structure with a mechanical validator and context as a judgment band** — they are independent axes with different measurement reliability.

---

## 9. Methodological honesty — status of the gaps

| Gap | Current status |
|---|---|
| 1 — Control group | CLOSED for translation and comparison (L006). Pending for book, recipe and article if the full conditional map is wanted. |
| 2 — Run-to-run variance | CLOSED for technical article (L007, CV 3.6%). Pending replication in other domains. |
| 3 — Single evaluator | Route defined (L007): structure axis → `cll_validator.py`; context axis → judgment band. |

**Limitations that remain:**
- Small sample — 3 inputs per domain in the design phase; adequate for design exploration, insufficient for statistical significance.
- The conditional map has only its two extremes measured against a control (translation and comparison); the three intermediate domains are missing to trace the full curve.
- Variance is measured in a single domain; the low CV still needs confirmation where CLL contributes most (comparison).

**What the experiment DOES demonstrate:** that CoS design improves monotonically with specific iterations; that CLL's contribution is a function of the task's structural ambiguity (+68 points at the ambiguous extreme); that the result is consistent run to run (CV 3.6%); and that structure and context are independent axes requiring different verification mechanisms. This is solid design evidence, with baseline and reliability already measured — a foundation for a formal benchmark.

---

*Consolidated from development logs 001–007. Source material for the first article and for formalizing the benchmark.*
