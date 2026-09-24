# CLL — Model Compatibility Note v2.0
**Cognitive Layer Language — Optimization Provenance**

**Version note (v1.1 → v2.0):** version aligned to the v2.0 corpus. **No change
to the provenance or benchmarking practice.** §6 is updated to add the
`***Global***` container (and its natural-mode resolution) to the list of
machine-relevant items that migrate to the kernel only on regression re-run.

**Changes vs v1.0:**
- §6 added: the corpus ↔ kernel decoupling and the Kernel Conformance Gap.
- §6 updated (v2.0): the `***Global***` container and natural-mode resolution
  added to the gap; natural-mode resolution's model-capability dependence noted.

## 1. Purpose

This note documents the model context under which the CLL was
designed and optimized, and the implications of migrating to
newer model generations. The CLL is a model-facing language:
its effectiveness is partially a function of the interpreting
model, not only of the specification itself.

## 2. Optimization Provenance

- CLL v1.0 – v2.0 was designed and empirically refined against Claude Opus-class models (Opus 4.x generation) and, more recently, Sonnet-class models via API.
- Validation behavior (V1–V18), compression conventions (CM), and layer interpretation (Int/Ctx/Cfg/Act/Out) were tuned to the instruction-following and inference profile of that generation.
- As of 2026, primary usage runs on a user-selectable executing model (Sonnet-class by default), with the design goal of swapping the model freely — including strong open-weight and eventually local models.

## 3. Why Model Migration Matters for CLL

### 3.1 Tokenizer dependence
CM forms (`Kntx`, `Kfg`, `Aly`) have no intrinsic token cost; their
cost depends on each provider's tokenizer vocabulary. Ratios
measured on one model/provider DO NOT transfer automatically.
Each provider requires its own measurement.

### 3.2 Interpretation strength
Stronger model generations infer intent from less scaffolding.
Two consequences:

a) Compliance with CLL structure tends to improve (fewer validator failures, more consistent layer adherence).
b) The marginal value of rigid structure may shift from "necessary for output consistency" toward "valuable for auditability, reproducibility and pipeline automation."

Both effects should be measured, not assumed.

### 3.3 Convergence baseline shift
The core CLL value metric is iterations-to-standardized-output.
Newer models lower the natural-language baseline (fewer
iterations even without CLL). The CLL advantage must therefore
be re-benchmarked per model generation to remain an honest,
current claim. A claim validated on one generation is a historical
fact, not a present guarantee.

### 3.4 Natural-mode resolution capability (New in v2.0)
The `***Global***` container's **natural mode** (free text resolved to `Var:`
lines) is performed by the **executing model** in the same call. Its reliability
therefore tracks model capability directly. Empirically, extraction is stable at
Sonnet-class and above and degrades below the Haiku→Sonnet boundary, where it may
fail silently. For models below that floor, or for reproducibility-critical CoS,
authors should use **formal mode** (explicit `Var:` lines). V8 (all `$` references
resolve) is the safety net that catches a failed extraction.

## 4. Required Practices

### 4.1 Kernel tagging
Every CLL Kernel release must declare the model(s) it was validated against:

```text
Kernel: v1.2.1-runtime | Validated: claude-opus-4-x | Status: Legacy
Kernel: v2.0           | Validated: <pending regression> | Status: Pending
```

### 4.2 Per-provider regression suite
On each new model adoption, run the fixed benchmark set:
- Dictionary conformance (UR→CM algorithm)
- Block parsing and validator pass rates (V1–V18)
- Convergence benchmark: iterations and total tokens to acceptance criterion, CLL vs natural-language baseline
- Output variance across N repeated runs (consistency)
- **Natural-mode `***Global***` extraction accuracy** (New in v2.0): fraction of natural-mode bodies resolved to the correct `Var:` set, per model.

### 4.3 Compatibility matrix
Maintain a matrix: Kernel version × Provider/Model × benchmark results.
This is the empirical backbone of any efficiency claim made by CLL Proxy.

## 5. Current Status

- **Opus-era validation:** complete (historical baseline).
- **Sonnet-class validation:** in progress (current default executing model).
- **v2.0 (`***Global***`) validation:** PENDING. Treat natural-mode reliability as unverified until the regression suite (§4.2) is executed on the target model.
- **Multi-provider validation (open-weight/local):** partial.

## 6. Corpus ↔ Kernel Decoupling

The **corpus** (docs 00–09) is the single source of truth for the CLL
specification. The **synthesized runtime kernel** (`CLL-Kernel`, Micro/Standard/
Full) is a downstream build loaded into model context by CLL Proxy alongside the
validator.

These are decoupled on purpose:

- Corpus changes cost nothing at runtime (documentation only).
- Kernel changes cascade to `cll_validator.py` (hard-coded `COMPACT_ROOTS`,
  AVR list, V-checks), `cll_compiler.py`, and stored CoS in `rules.db`.

The corpus therefore advances to **v2.0** immediately, while the runtime kernel
stays **pinned at v1.2.1-runtime**. The delta is tracked as the **Kernel
Conformance Gap**. Machine-relevant gap items migrate to the kernel **only** when
§4.2's regression suite is re-run — never in lockstep with a corpus edit:

- Pre-existing (v1.x): UR length 3–13 (kernel 3–6/4–10), removed consonant
  whitelist, `Rating` removal, `Variable` addition, canonical
  `Validate`/`ValidateParam`/`ValidateCtx` names, the flat "40–70%" claim.
- **New in v2.0:** the `***Global***` container (parsing + the V17 re-purpose in
  `cll_validator.py`) and **natural-mode resolution** (new logic in
  `cll_compiler.py`). These are the largest v2.0 cascade items.

The planned `cll_norms.json` is the mechanism that will close this gap
permanently: once built, both the corpus (as its printed reference) and the
tooling (as its runtime source) read the same normative values, so the two
cannot drift again.

---
*End of CLL Model Compatibility Note v2.0*
