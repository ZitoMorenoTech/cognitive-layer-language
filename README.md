# Cognitive Layer Language (CLL)

**CLL makes the output predictable where the task is ambiguous. Where the model already knows the shape, CLL stays out of the way.**

CLL is a formal language for writing instructions to an LLM as **labeled layers** instead of free prose. Prose is re-interpreted on every run; a labeled structure is read the same way every time. The result is output whose *shape* is stable across runs and inputs — and mechanically verifiable afterwards.

---

## The finding that defines the scope

CLL was benchmarked against a control group: the same inputs run with a plain natural-language prompt — what a user unfamiliar with CLL would type. The result is not a flat number. It depends entirely on the task:

| Domain | Control (plain prose) | Best CLL | Delta |
|---|---|---|---|
| Translation | 96% | 92% | **−4%** |
| Option comparison | 24% | 92% | **+68%** |

**The value of CLL is a function of the task's structural ambiguity, not its difficulty.**

In translation the model already knows the output mirrors the input — the scaffolding is noise, and CLL slightly hurts. In option comparison the model does not know whether to use a table, prose or bullets, how many criteria, in what order, or where to put the recommendation, so it improvises differently every time (24%). There CLL is worth +68 points.

This answers the obvious objection in advance. *Isn't this just a good prompt?* A good prompt **describes** the output. CLL **declares** the structure before the model decides, and verifies it afterwards. Where the task has a natural shape that difference is worth nothing; where it doesn't, it's worth 68 points.

---

## How it works

The unit is a **block**: five layers, always the same, always in order.

| Layer | Question it answers |
|---|---|
| `Int:` | **What** is the goal? |
| `Ctx:` | **With what** inputs? |
| `Cfg:` | **How** — which settings? |
| `Act:` | **Do** what action? |
| `Out-Lang:` | **Deliver** in what form? |

```cll
CLL: 2.0
Int: A-Analys Kontext
Ctx: D-Data UserReq
Cfg: C-Konfig Tone:Neutral Len:Short
Act: X-Exec Build
Out-Lang: Natural
```

The verb in `Act:` comes from a closed registry and determines the block type: **Declarative** verbs (`Define`, `Enforce`, `Preserve`…) set a rule and produce no output; **Executable** verbs (`Build`, `Generate`, `Compare`…) produce output. Verbs cannot be invented — an unregistered verb is invalid.

Blocks are packaged into a **CoS** (Cognitive Operating System) — META for the label, GLOBAL for shared values and standing rules, then the modules:

```cll
# META
Ver: 2.0 Dom:Doc Auth:Zito Date:2026-09

# GLOBAL
***Global***
Var: OutLang = "es"
***End Global***

Int: A-Analys SourceScope
Ctx: D-Data InputOnly
Cfg: C-Konfig Domain:Doc AddKnowledge:FALSE
Act: X-Exec Enforce

# MODULE 1
Int: A-Analys BookCard
Ctx: D-Data UserText Lang:$OutLang
Cfg: C-Konfig Domain:Doc Fields:7
Act: X-Exec Build
Out-Lang: Natural
```

Two mechanisms sit on top. The **Global Variable System**: declare shared values once inside the `***Global***` container and reference them with `$Name`. You can write them formally (`Var: OutLang = "es"`) **or** just describe them in plain words inside the container and let the model resolve them into variables — so a user who doesn't know CLL can still set up a CoS. And **Literal Preservation**: content wrapped in `***Literal***` markers passes through byte-for-byte with the grammar switched off. The two containers are siblings — neither nests inside the other.

Full specification in [`/spec`](./spec). Start with the [Overview & Reading Guide](./spec/00__CLL_Overview_and_Reading_Guide_v2_0.md).

---

## What the benchmark shows

Twelve runs across five domains (book card, recipe extraction, technical article, option comparison, translation), executed via API in cold-call mode with no conversational context. Conformity is measured on two independent axes: **structure** (was the form respected?) and **context** (was the content and its constraints respected?).

> The benchmark below was measured on the v1.2 design. The v2.0 change is how the GLOBAL section is delimited (an explicit `***Global***` container), not how variables behave, so these findings remain representative. The one genuinely new v2.0 capability — natural-mode resolution — is not yet benchmarked (see below).

**Design phase — seven findings, each isolating one variable:**

| Discovery | Effect |
|---|---|
| Fuse the label with its physical line position | ~50% → 83% |
| Global Variable System | +6% global, +8% context |
| Write the CoS in English, whatever the output language | +9% global, +20% context |
| Quantitative limits as global variables | sentence limit went from 0/3 to 6/6 |
| Short precise CoS beats long complete CoS | 5 blocks > 8 blocks |
| Rule wording alone — imperative, self-limiting, verifiable | 9 points |
| Passing through the compiler | +9%, same model and inputs |

**Reliability:** ten runs of the same CoS and input produced a coefficient of variation of **3.6%** (range 89–100%). The structure axis was near-deterministic — one real failure in ten runs. That consistency, not the peak score, is the claim: CLL compresses dispersion.

**Two axes, two mechanisms.** Structure is mechanically verifiable and belongs in a validator. Context depends on judgment and is reported as a band. Log 007 demonstrates this live: the same evaluator scoring the same data twice disagreed on four cells, *all* of them on the context axis.

Full evidence — inputs, CoS, raw outputs and rule-by-rule scoring — in [`/logs`](./logs). Consolidated in the [Executive Summary](./logs/CLL_Executive_Summary_v2.md).

### What the benchmark does not show

- **Small sample.** Three inputs per domain. Enough for design exploration, not for statistical significance.
- **The conditional map has only its two extremes measured** against a control. Three domains in the middle are missing.
- **Variance is measured in a single domain.** The low CV still needs confirming where CLL contributes most.
- **Natural-mode `***Global***` resolution is unmeasured.** The v2.0 natural-language container mode depends on the executing model and has not been benchmarked; its accuracy is treated as unverified until the regression suite runs ([doc 05](./spec/05__CLL_Model_Compatibility_Note_v2_0.md)).
- **Claims are model-generation specific.** A result measured on one generation is a historical fact, not a present guarantee ([doc 05](./spec/05__CLL_Model_Compatibility_Note_v2_0.md)).

No token-savings percentage is claimed. The specification prohibits asserting one without per-tokenizer measurement, and that measurement has not been done.

---

## Repository structure
```
/spec The CLL v2.0 corpus — 12 documents, the source of truth
/logs Development logs 001–007 with the full benchmark evidence
/examples Working CoS examples with their inputs and outputs
```
The **compiler and validator are not in this repository.** They live in a separate private repo. What is published here is the language itself: its specification, the evidence behind its design, and worked examples.

---

## Status

The corpus is at **v2.0** and stable, and it will keep moving — the specification advances as the benchmark produces evidence. Open design questions are marked inside the documents rather than hidden.

The one question that was open in v1.2 — how the GLOBAL section should recognize variable declarations (positional enforcement vs. an explicit container) — is **resolved in v2.0**: the GLOBAL section is now the explicit `***Global***` container, consistent with the existing `***Literal***` container, with two authoring modes (formal `Var:` lines, or natural language resolved to variables before execution).

The **runtime** (compiler and validator, in the private repo) is pinned at `v1.2.1-runtime` and adopts the v2.0 container only when the per-provider regression suite is re-run. The corpus and the runtime are decoupled on purpose; the delta is tracked as a **Kernel Conformance Gap** inside the spec documents.

---

## Author

CLL was designed and developed by **Zito Moreno** ([@ZitoMorenoTech](https://github.com/ZitoMorenoTech)).

---

## License and intended use

Licensed under [Apache 2.0](./LICENSE) — permissive, with a patent grant.

The license governs what you may legally do. [`PRINCIPLES.md`](./PRINCIPLES.md) states what CLL was built for and where the author stands on uses he did not intend. It carries no legal force; it exists because a license controls the terms of use, not the use itself.
