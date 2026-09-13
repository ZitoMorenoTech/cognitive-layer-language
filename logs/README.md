# Development Logs

Chronological record of the CLL benchmark experiment. Each log documents one test session with the full evidence: verbatim inputs, the complete CoS used, the raw outputs, and a rule-by-rule conformity analysis on two axes (structure and context).

**A note on language.** The logs are written in English. Test inputs and model outputs are preserved verbatim in their original language — Spanish, Portuguese and English. This is deliberate: several tests evaluate whether the system keeps the output in the input's language, so translating that material would destroy the very property being demonstrated.

For the consolidated findings, see [`CLL_Executive_Summary_v2.md`](./CLL_Executive_Summary_v2.md).

---

## Index

| Log | Test | What it covers | Headline result |
|---|---|---|---|
| **001** | Book card | Five rule-writing approaches (V1–V5) compared on the same task. Discovery of line-based design: fusing the label with its physical line position. | ~50% → 86% |
| **002** | Recipe extraction | Same rules with and without the Global Variable System (GVS). Discovery that declared-once variables fix language and invented-content failures. | 82% → 88% |
| **003** | Technical article | Same CoS design written in Spanish vs English. Discovery that an English CoS aligns with the linguistic base of Western LLMs. Best result of the experiment. | 89% → 98% |
| **004** | Option comparison | Human-written CoS vs formal CLL, across Sonnet and Opus. The domain where opinion filtering proves hardest — and where a well-written natural-language CoS beat the formal one. | 88–92% |
| **005** | Translation | Structure-preserving translation, plus a cross-check of Test 4 using the web-generated CoS. Discovery that a short precise CoS beats a long complete one. | 83% → 92% |
| **006** | Control group | Baseline runs with plain natural-prose prompts, no CoS. Discovery of the **conditional map**: CLL's contribution scales with the task's structural ambiguity. | −4% / +68% |
| **007** | Variance | Ten runs of the same CoS and input, measuring run-to-run consistency. Also demonstrates single-evaluator drift via a double scoring pass. | CV 3.6% |

---

## Reading order

- **For the argument:** 006 (the conditional map) then 007 (reliability). These two carry the central claims.
- **For the design rationale:** 001 → 003 in order. Each one isolates a single variable and shows what changed.
- **For the full picture:** the executive summary consolidates all seven.

## A note on evidence

Every log includes the raw material, not just the scores: the exact input text, the exact CoS, and the exact model output. The conformity tables can be audited against them. Where a result was later revised — as in log 007, where a second scoring pass corrected four cells — the revision and its reasoning are recorded rather than silently replaced.
