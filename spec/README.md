# CLL Specification — Corpus v1.2

The normative definition of the Cognitive Layer Language. **This corpus is the single source of truth**: where a document here and any other material disagree, this corpus wins.

New to CLL? Read [`00__CLL_Overview_and_Reading_Guide_v1_2.md`](./00__CLL_Overview_and_Reading_Guide_v1_2.md) first. It explains the whole system in plain language and points to where each idea is defined precisely.

---

## Documents

| # | Document | Read it when you want to… |
|---|---|---|
| 00 | [Overview & Reading Guide](./00__CLL_Overview_and_Reading_Guide_v1_2.md) | get the whole picture fast |
| 01 | [Core Architecture](./01__CLL_Core_Architecture_v1_2.md) | understand the concepts and the five layers |
| 01_1 | [UR→CM Compression Algorithm](./01_1_CLL_UR_CM_Compression_Algorithm_v1_2.md) | understand how roots compress to compact forms |
| 01_2 | [Canonical Dictionary](./01_2_CLL_Canonical_Dictionary_v1_2.md) | look up a root and its compact form |
| 02 | [SPEC](./02__CLL-SPEC-v1_2.md) | know the exact rules and constraints |
| 03 | [Grammar](./03__CLL-Grammar-v1_2.md) | know the precise syntax (BNF) |
| 04 | [Style Guide](./04__CLL-Style-Guide-v1_2.md) | write clean, consistent CLL |
| 05 | [Model Compatibility Note](./05__CLL_Model_Compatibility_Note_v1_1.md) | understand how claims are scoped and benchmarked |
| 06 | [Literal Preservation Spec](./06__CLL_Literal_Preservation_Spec_v1_2.md) | keep content verbatim (templates, code, embedded DSLs) |
| 07 | [Action Verb Registry](./07__CLL_Action_Verb_Registry_v1_2.md) | see which verbs are legal in an `Act:` line |
| 08 | [CoS Container & Inheritance](./08__CLL_CoS_Container_and_Inheritance_v1_2.md) | package blocks into a CoS, or inherit from a base |
| 09 | [Global Variable System](./09__CLL_Global_Variable_System_v1_2.md) | declare and reuse values with `Var:` and `$Name` |

**Shortest path to writing CLL:** 00 → 01 → 07 → 08. Add 09 and 06 when you need them; reach for 02 and 03 when you need the letter of the law.

---

## Two things to know before reading

**On claims.** CLL's efficiency claim is *convergence efficiency* — fewer iterations to reach a standardized, acceptable output — measured per model and per provider. Flat token-reduction percentages are explicitly prohibited without per-tokenizer measurement (SPEC §9).

**On the corpus and the runtime.** These documents are the specification. The runtime that executes CLL is a separate build maintained in a private repository, and the two are decoupled on purpose so documentation can improve without cascading into running tooling. Where a document notes a difference between the two, it is marked as a *Kernel Conformance Gap* box and labelled informative — it records an implementation lag, not a change to the specification.
