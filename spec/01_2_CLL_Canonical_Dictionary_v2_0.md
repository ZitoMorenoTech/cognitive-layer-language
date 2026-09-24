# CLL Canonical Dictionary — Formal Specification v2.0

| | |
|---|---|
| **Status** | Stable |
| **Scope** | Universal Roots (UR), Compact Mode (CM), Semantic Definitions |
| **Normative values** | This corpus (see note). A machine-readable `cll_norms.json` extract is planned as the build-time source for tooling (Roadmap, Track A). Until built, the values here are normative. |

**Version note (v1.2 → v2.0):** version aligned to the v2.0 corpus. **No lexicon
change.** The `***Global***` container (Grammar v2.0 §5.8) uses the `Var:` line
key and `$` references, which are independent of this dictionary; the
`Variable | Vr` entry (#51) is unchanged. This is a header-aligned reissue.

**Retained from v1.2:**
- `Variable | Vr` (Approved, #51) supports the Global Variable System (doc 09).
- `Rating` explicitly REJECTED (never admitted). Use `Score` or `Priorit`.
- §6 UR ↔ AVR namespace note (positional disambiguation).

> **Kernel Conformance Gap (informative).** The runtime kernel still ships
> `Rating|Rtg` (remove on migration), uses `Valid|Vld`/`ValidParam|Vp`/
> `ValidCtx|Vcxt` (rename to canonical `Validate`/`ValidateParam`/`ValidateCtx`),
> and omits `Variable|Vr` (add when the GVS is exercised). Migrated on the kernel
> side only when the per-provider regression suite is re-run (doc 05).

## 1. Entry Structure

Format: `StandardUR | CM | Definition | Origin | State:Version | Flags`

- **UR properties:** length 3–13, alphabetic, semantically stable.
- **CM properties:** length 2–4, ordered subsequence of UR, contains first character, unique, no digits.

## 2. Semantic Domains

- **Analytic Operations** (Analys, Summar, Explain)
- **Structural Operations** (Kontext, Konfig)
- **Execution Operations** (Exec, Retry, Abort)
- **Data Operations** (Data, Normalize, Tokenize)
- **Merge Operations** (Merge, MergeSafe, MergePolicy)
- **Reasoning Operations** (Reason, Classif, Score)

Domains ensure non-overlapping semantics. A Proposed UR that overlaps
an existing domain member is rejected or deprecated. Two roots have been
excluded on this basis, both in the scoring/prioritization space:

- `Rate` — **Deprecated v1.1** (overlaps `Score` / `Priorit`).
- `Rating` — **Rejected v1.2**, never admitted (same overlap; appears only as runtime-kernel drift, see the Kernel Conformance Gap).

## 3. Canonical Dictionary Entries

| # | StandardUR | CM | Definition | Origin | State:Version | Flags |
|---:|---|---|---|---|---|---|
| 1 | Analys | Aly | Cognitive analysis operation and diagnostic step. | EN/DE | Approved:v1.1 | (R) |
| 2 | Kontext | Kntx | Context scope defining boundaries and inputs. | DE/NL | Approved:v1.1 | |
| 3 | Konfig | Kfg | Configuration block for reasoning parameters. | DE/EN | Approved:v1.1 | (R) |
| 4 | Exec | Exe | Execution of a defined task or plan. | EN | Approved:v1.1 | (V) |
| 5 | Plan | Pln | Ordered sequence of steps or strategy. | EN/NL | Approved:v1.1 | |
| 6 | Data | Dta | Collection of inputs, payloads or sources. | EN/ID | Approved:v1.1 | (V) |
| 7 | Fokus | Fks | Focal priority or main objective. | DE/SV | Approved:v1.1 | |
| 8 | Output | Outp | Output format or delivery mode. | EN | Approved:v1.1 | (V) |
| 9 | Memory | Mem | Persistent state or stored information. | EN/ID | Approved:v1.1 | (R)(V) |
| 10 | Reason | Rsn | Logical inference or reasoning module. | EN/DE | Approved:v1.1 | |
| 11 | Validate | Vld | Consistency and correctness verification. | EN/NL | Approved:v1.1 | (R) |
| 12 | Summar | Smr | Information condensation and summarization. | EN/SV | Approved:v1.1 | |
| 13 | Extract | Ext | Structured extraction of relevant data. | EN | Approved:v1.1 | (R) |
| 14 | Transform | Trf | Data transformation or mapping. | EN/NL | Approved:v1.1 | (R) |
| 15 | Route | Rte | Routing of subprocesses or modules. | EN | Approved:v1.1 | (V) |
| 16 | Merge | Mrg | Merge of sources or results. | EN/NL | Approved:v1.1 | |
| 17 | Filter | Flr | Filtering based on criteria or rules. | EN | Approved:v1.1 | (R) |
| 18 | Score | Scr | Quantitative scoring or evaluation. | EN | Approved:v1.1 | |
| 19 | Classif | Clf | Classification or labeling of items. | EN/DE | Approved:v1.1 | (R) |
| 20 | Search | Srch | Directed search within corpora or indexes. | EN | Approved:v1.1 | |
| 21 | Fetch | Fch | Retrieval of a specific resource. | EN/ID | Approved:v1.1 | (R) |
| 22 | Normalize | Nrm | Standardization of formats or values. | EN/SV | Approved:v1.1 | (R) |
| 23 | Tokenize | Tknz | Token segmentation for processing. | EN | Approved:v1.1 | |
| 24 | Map | Map | Key→value or schema mapping. | EN/NL | Approved:v1.1 | (V) |
| 25 | ValidateCtx | Vctx | Context-specific validation. | EN/DE | Proposed:v1.1 | (R) |
| 26 | Sanitize | Sntz | Removal of PII or noise. | EN/ID | Approved:v1.1 | (R) |
| 27 | AnomDetect | Anm | Detection of anomalies. | EN | Proposed:v1.1 | (R) |
| 28 | Explain | Expl | Generation of explanations or justifications. | EN/SV | Approved:v1.1 | (V) |
| 29 | PlanOpt | PlnO | Optimization of an existing plan. | EN/NL | Proposed:v1.1 | (V) |
| 30 | Retry | Rty | Retry mechanism for transient failures. | EN | Approved:v1.1 | (R) |
| 31 | Abort | Abt | Safe cancellation of execution. | EN | Approved:v1.1 | (R) |
| 32 | Audit | Aud | Traceability and logging of actions. | EN/DE | Approved:v1.1 | (V) |
| 33 | Diff | Dff | Comparison between versions or results. | EN/NL | Approved:v1.1 | (R) |
| 34 | MergePolicy | MrgP | Rules for resolving merge conflicts. | EN | Proposed:v1.1 | (R) |
| 35 | Priorit | Prt | Assignment of priority. | EN/SV | Approved:v1.1 | (R) |
| 36 | Schedule | Sch | Temporal scheduling of tasks. | EN | Proposed:v1.1 | (R) |
| 37 | Notify | Ntfy | Emission of notifications or alerts. | EN | Approved:v1.1 | |
| 38 | Serialize | Srz | Serialization for transport or storage. | EN/ID | Approved:v1.1 | (R) |
| 39 | Deserialize | Dsrz | Reconstruction from serialized form. | EN/ID | Approved:v1.1 | (R) |
| 40 | ValidateParam | Vp | Parameter validation routine. | EN/DE | Approved:v1.1 | (R) |
| 41 | Fallback | Fbk | Substitution for unknown URs. | EN/NL | Approved:v1.1 | (R) |
| 42 | Deprecate | Dpc | Marking URs as obsolete. | EN | Approved:v1.1 | (R) |
| 43 | MergeHint | MrgH | Suggestion for merge resolution. | EN/NL | Proposed:v1.1 | (R) |
| 44 | Localize | Lclz | Regional or linguistic adaptation. | EN/SV/ID | Proposed:v1.1 | (R) |
| 45 | Probe | Prb | Quick hypothesis test. | EN | Proposed:v1.1 | |
| 46 | Sample | Smpl | Representative sampling. | EN | Approved:v1.1 | |
| 47 | ExplainShort | ExS | Short explanation format. | EN/SV | Approved:v1.1 | (R)(V) |
| 48 | Rate | — | **DEPRECATED v1.1.** Use `Score` (evaluation) or `Priorit` (prioritization). Former CM `Rte2` is invalid (digit) and is removed. See also `Rating` (rejected v1.2). | EN | Deprecated:v1.1 | |
| 49 | MergeSafe | MrgS | Safe merge with conflict avoidance. | EN | Proposed:v1.1 | (R) |
| 50 | ContextHash | CtxH | Deterministic context fingerprint. | EN/DE | Proposed:v1.1 | (R) |
| 51 | Variable | Vr | Named value reference for the Global Variable System (doc 09). | EN/NL | Approved:v1.2 | (R) |

**Rejected (not part of the dictionary):**

| StandardUR | CM | State | Note |
|---|---|---|---|
| Rating | ~~Rtg~~ | **Rejected:v1.2** | Overlaps `Score` / `Priorit` (same reason `Rate` was deprecated). Present only as runtime-kernel drift; not canonical. |

## 4. Lifecycle Model

`Proposed → Approved → Deprecated → Removed`
(plus `Rejected`: proposed but never admitted; retained here only to prevent re-introduction.)

- **Proposed** URs may change morphology or semantics (precedent: `Vcxt` → `Vctx`).
- **Approved** URs are stable and production-safe.
- **Deprecated** URs remain parseable but emit a validator warning and map to their replacement (`Rate` → `Score` | `Priorit`).
- **Rejected** URs are documented as excluded and MUST NOT be admitted (`Rating`).
- **Removed** URs are no longer part of the dictionary.

## 5. Extension Policy

New URs must: pass `Compress(UR)` or register a Minimality reduction;
produce a unique CM; pass the Subsequence Conformance Rule; fit a
semantic domain without overlap; enter as Proposed.

## 6. UR ↔ AVR Namespace Note

A CM string may be shared between a UR in this dictionary and an AVR verb
(doc 07) — e.g. `Smr` (UR `Summar` / verb `Summarize`), `Vld` (UR
`Validate` / verb `Validate`). This is intended reuse, not a collision.
Disambiguation is **positional**: the second token of an `Act:` line is an
AVR verb; the same string anywhere else is a UR (full rule in Algorithm
v2.0, §8). Validators MUST NOT flag the shared CM as a duplicate.

---
*End of file.*
