# CLL — Development Log
*Cognitive Layer Language | ZitoMorenoTech*

> **Note on language.** Test inputs and model outputs are preserved verbatim in their original language. Several tests evaluate whether the system keeps the output in the input's language, so translating them would destroy the evidence. All analysis, tables and commentary are in English.

---

## Entry 007 | Friday, 11 September 2026

**Test object:** run-to-run variance — closing Gap 2 of the benchmark.
**Objective:** measure whether the same CoS, with the same input, produces consistent results run to run. This is the property CLL promises and the only one tests 1–6 had not measured: not "how good is the output?" but "how predictable is it?".
**Method:** 10 runs of the same CoS + input, via API in cold-call mode, default temperature, independent sessions with no history.

---

### Experiment design

The best-performing condition in the experiment was chosen as the base case: the CoS from **Test 3, version 2** (technical article, formal CLL with GVS in English) against **Input 1** (intermittent fasting study, English, complete). It is the ideal candidate because the input is objective — without the emotional ambiguity that destabilized Klara in Test 1 — and because if there is variance in the best case, it will show clearly.

**Model:** Claude Sonnet, via the CLL web compiler. The compiler's metadata reported the internal label `claude-sonnet-4-5`; it is recorded as consistent with the model used in the experiment's earlier runs. Documented explicitly for traceability.

**Note on the reference:** the "98%" headline from Test 3 is the aggregate across all three inputs. Input 1 in isolation, on its original run, scored **100% / 100%** (perfect structure and context — it was the only one of the three that preserved bold formatting, being an English input). That original run is **run 0** of this study.

---

### Results — 10 runs

Evaluated by independent double pass (see the following section for why). The values reported are those of the **second, verified pass**.

| Run | Structure | Context | Global | Observed deviation |
|---|---|---|---|---|
| Run 0 (reference) | 100% | 100% | 100% | — (original Test 3 run) |
| R1 | 100% | 90% | 95% | "middle-aged adults" added in Problem |
| R2 | 100% | 90% | 95% | "healthy adults" added in Problem |
| R3 | 100% | 100% | 100% | clean |
| R4 | 100% | 100% | 100% | clean |
| R5 | 100% | 90% | 95% | "middle-aged adults" added in Problem |
| R6 | 100% | 100% | 100% | clean |
| R7 | 100% | 90% | 95% | "middle-aged adults" added in Context |
| R8 | 100% | 100% | 100% | clean |
| R9 | 100% | 100% | 100% | clean |
| R10 | 88% | 90% | 89% | no bold + "middle-aged" added |

---

### Variance statistics (n = 10)

| Axis | Mean | Range | Std. deviation | Coeff. of variation |
|---|---|---|---|---|
| Structure | 98.8% | 88% – 100% | 3.6 | 3.6% |
| Context | 95.0% | 90% – 100% | 5.0 | 5.3% |
| **Global** | **96.9%** | **89% – 100%** | **3.5** | **3.6%** |

**Reading the dispersion:**

- **Low coefficient of variation on both axes (3.6% and 5.3%).** Consistency is high: no run deviated from the mean by more than a single percentage digit, apart from the structural outlier R10.
- **Structure is near-deterministic.** 9 of 10 runs scored 100%. Excluding the single real structural failure (R10), the standard deviation of that axis drops to **0** — nine identical runs. The 3.6 dispersion is produced by one event.
- **Context is binary, not gradual.** All ten values were exactly 90% or 100%, never intermediate. A run either "fails" (adds an extra descriptor) or it does not. There is no progressive degradation.

**Honest observation about the reference:** the mean of the 10 runs (96.9%) falls *below* run 0 (100%). The original run of Input 1 was a clean extraction — the realistic expectation over 10 runs for this input is **~97%, not 100%**. The "100% on Input 1" headline was partly a lucky single run. This is exactly the kind of nuance that only appears when measuring variance, and the reason Gap 2 mattered.

---

### The double pass — Gap 3 live

The 10 runs were scored **twice, independently**, without looking at the first pass's numbers. Both passes, performed by the same evaluator on identical data, **disagreed on four cells**:

| Run | Pass 1 (S/C) | Pass 2 (S/C) | Nature of the error |
|---|---|---|---|
| R1 | 88 / 90 | **100** / 90 | Structural false positive: penalized bold formatting that was present |
| R5 | 88 / 90 | **100** / 90 | Structural false positive: counted a non-existent MaxSent failure |
| R2 | 100 / 100 | 100 / **90** | Context false negative: missed the added "healthy adults" |
| R10 | 88 / 100 | 88 / **90** | Context false negative: missed the added "middle-aged" |

Impact on the averages: Structure 93.8% → **98.8%**, Context 94.0% → **95.0%**, Global 93.9% → **96.9%**.

**The pattern of the errors is the finding, not the noise.** The two structure errors were **false positives** (the evaluator invented failures that did not exist); the two context errors were **false negatives** (the evaluator missed real failures). All of the evaluator's inconsistency concentrated in the context axis — the structure axis never produced a genuine disagreement, only an arithmetic error corrected on re-reading.

This demonstrates live the weakness the executive summary declared as Gap 3: the context axis depends on judgment and is not reproducible even within the same evaluator. The architectural conclusion is direct: **the structure axis must migrate to `cll_validator.py`** (mechanical verification, zero evaluator variance) and **the context axis must be reported as a judgment band**, not as a flat number.

---

### Failure taxonomy (10 runs)

| Failure | Frequency | Axis | Nature |
|---|---|---|---|
| Added descriptor ("middle-aged" / "healthy") | 5/10 | Context | **Systematic** — the model interprets and enriches; not present in the input |
| Bold formatting absent on labels | 1/10 | Structure | **Probabilistic** — same failure as in Test 3, but here on an English input |
| Limitations as a nominal list without a verb | 2/10 | — | Minor stylistic variant, not penalized (all 3 limitations present and faithful) |

**Two new findings from this taxonomy:**

1. **The bold-formatting failure is not deterministic by language.** In Test 3 it only appeared with non-English inputs; here it appeared on an English input (R10, 1 of 10). Reclassified: it is a **low-frequency probabilistic event**, not a rule tied to the input language.
2. **The added descriptor is the experiment's central chronic failure**, confirmed once more. The model inserts "middle-aged"/"healthy" — categories absent from the input — in half the runs. It is the same "the model interprets, it does not copy" pattern present since Test 1. No rule wording eliminated it; it is the strongest evidence for the post-output validator.

---

### Key observations

**The promise of predictability holds up with data.** Ten cold runs, global between 89% and 100%, CV of 3.6%. CLL does not merely produce good outputs — it produces *consistently* good ones. This was the claim that remained unproven.

**When context fails, it always fails in the same direction.** The five context failures are the same type of error (an extra descriptor), not five different errors. CLL's variance is not chaotic: it is a single, identified, mechanically detectable failure mode.

**The ceiling for this input is ~97%, not 100%.** Measuring variance revealed that the perfect headline was a lucky run. Reporting the mean of 10 runs is more honest and more defensible than citing the best case.

**Incidental compression data (single run):** R10's metadata reported 2,268 input tokens, 211 output tokens, "savings 66.7%", cost avoided $0.000736. Recorded but **not used as a claim** — it is a single unqualified data point, and the efficiency metric requires the Convergence Efficiency framework before asserting token percentages. Pending systematic measurement.

---

### Synthesis of the three validity logs (005, 006, 007)

Logs 005–007 are not CoS design logs (that was 001–004). They are the three that test the **validity of the benchmark**, each attacking a different gap. Read together they tell a coherent story about *when* CLL contributes, *how much*, and *how reliable* the number is.

#### The conditional map (from 006)

CLL's contribution is a function of the task's **structural ambiguity**, not its difficulty:

| Domain | Control (natural prose) | Best CLL | Delta |
|---|---|---|---|
| Translation | 96% | 92% | **−4%** |
| Option comparison | 24% | 92% | **+68%** |

In translation the model already knows the output mirrors the input — CLL even interferes with instruction noise. In comparison the model does not know what shape to give the output and improvises differently each time — CLL is worth +68 points. Log 005 set the low anchor: it proved that in translation even the web compiler (92%) loses to leaving the model alone (96%), and it also showed that **Sonnet via CLL Web (92%) beats Opus direct (83%)** — the system makes a cheaper model win, but only in the domain where CLL rules.

#### The separation of the two axes (confirmed by all three)

The pattern running through 005, 006 and 007 is that structure and context are independent axes with different mechanisms:

| Axis | What the three logs show together | Mechanism it needs |
|---|---|---|
| **Structure** | Near-deterministic with CLL (1 real failure in 10 runs, L007). Without CLL it collapses only in ambiguous tasks (20% in comparison, L006). | `cll_validator.py` — mechanical, zero evaluator variance |
| **Context** | Band of 88–100%. Dominant failure is interpretive and systematic. Depends on evaluator judgment (double pass, L007). | Post-output validator + reporting as a band, not a flat number |

**The image that sums it up:** the control (without CLL) is bimodal — excellent in translation (100/90), poor in comparison (20/29); its quality depends entirely on the type of task. All CLL conditions cluster in a narrow range (78–100 structure, 88–95 context). CLL does not make the output *better* everywhere — it makes it **land in the same reliable zone regardless of the task's ambiguity**. It compresses dispersion. Log 007 proves that compression was not a single-run fluke.

---

### Status of the benchmark gaps

| Gap | Status after L007 |
|---|---|
| 1 — Control group | CLOSED for translation and comparison (L006). Pending for book, recipe and article if the full conditional map is wanted. |
| 2 — Run-to-run variance | **CLOSED** for the technical-article domain (10 runs, CV 3.6%). Pending replication in other domains. |
| 3 — Single evaluator | Demonstrated live (double pass, L007). Closure route defined: structure axis → `cll_validator.py`; context axis → judgment band. |

---

### Cumulative design findings

1. **Fusing the label with the line position** reduces structural violations (Test 1)
2. **GVS resolves language and invented-content failures** (Test 2)
3. **An English CoS improves context fidelity** (Test 3)
4. **$MaxSent as a global variable sustains quantitative limits** (Test 3)
5. **The model does not copy, it interprets** — post-output validator indispensable (all tests)
6. **CLL's contribution is a function of the task's structural ambiguity** (Test 4/5/control, L006)
7. **A short precise CoS beats a long complete one** (Test 5)
8. **Run-to-run consistency is high and its variance has a single identifiable failure mode** (L007)
9. **The structure axis is mechanically verifiable; the context axis requires judgment and must be reported as a band** (double pass, L007)

---

### Pending for the next sessions

- [ ] Replicate the variance study in a second domain (ideally option comparison — the largest delta) to confirm the low CV holds where CLL contributes most
- [ ] Extend the control group to the 3 remaining domains (book, recipe, article) for the complete conditional map
- [ ] Migrate the structure axis to `cll_validator.py` across all tests (closes Gap 3 on that axis)
- [ ] Formalize context reporting as a judgment band with explicit severity criteria
- [ ] Define the Convergence Efficiency framework before reporting any token-savings metric
- [ ] Send screenshot to Jyri

---

*Log 007 — closure of Gap 2 (variance) for the technical-article domain and synthesis of the three validity logs (005–007).*
