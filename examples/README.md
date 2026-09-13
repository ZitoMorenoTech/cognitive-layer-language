# Examples

Working CoS examples taken from the benchmark. Each one shows the complete CoS, the verbatim inputs, and the raw model outputs — nothing is cleaned up or reconstructed after the fact.

The two examples are deliberately chosen as the extremes of the conditional map: one shows CLL at its most effective, the other shows what the same task looks like with no structure at all.

| Example | Domain | Result | Shows |
|---|---|---|---|
| [01 — Technical article summary](./01-technical-article-summary.md) | Academic summarization | 96% / 100% | Global variables, literal fallback, three input languages, exact number preservation |
| [02 — Option comparison](./02-option-comparison.md) | Structured comparison | 92% vs 24% control | The largest delta in the benchmark, equal-depth enforcement, and the unsolved opinion-filtering problem |

**Inputs and outputs are preserved in their original language** — Spanish, Portuguese and English. Several rules under test concern language preservation, so translating that material would remove the evidence.

For the rule-by-rule scoring behind these results, see [`/logs`](../logs). For the specification the CoS are written against, see [`/spec`](../spec).
