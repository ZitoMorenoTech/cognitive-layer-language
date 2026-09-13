# CLL — Model Compatibility Note v1.1
**Cognitive Layer Language — Optimization Provenance**

**Changes vs v1.0:**
- §6 added: the corpus ↔ kernel decoupling and the Kernel Conformance Gap.
- Provenance updated to note the v1.2 corpus and the pinned runtime kernel.

## 1. Purpose

This note documents the model context under which the CLL was
designed and optimized, and the implications of migrating to
newer model generations. The CLL is a model-facing language:
its effectiveness is partially a function of the interpreting
model, not only of the specification itself.

## 2. Optimization Provenance

- CLL v1.0 – v1.2.1 was designed and empirically refined against Claude Opus-class models (Opus 4.x generation).
- Validation behavior (V1–V18), compression conventions (CM), and layer interpretation (Int/Ctx/Cfg/Act/Out) were tuned to the instruction-following and inference profile of that generation.
- As of June 2026, primary usage has migrated to Claude Fable 5 (Mythos-class). CLL Proxy additionally targets Gemini, GPT and Groq-hosted models (multi-provider).

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
current claim. A claim validated on Opus is a historical fact,
not a present guarantee.

## 4. Required Practices

### 4.1 Kernel tagging
Every CLL Kernel release must declare the model(s) it was validated against:

```text
Kernel: v1.2.1 | Validated: claude-opus-4-x | Status: Legacy
Kernel: v1.3   | Validated: claude-fable-5  | Status: Current
```

### 4.2 Per-provider regression suite
On each new model adoption, run the fixed benchmark set:
- Dictionary conformance (UR→CM algorithm)
- Block parsing and validator pass rates (V1–V18)
- Convergence benchmark: iterations and total tokens to acceptance criterion, CLL vs natural-language baseline
- Output variance across N repeated runs (consistency)

### 4.3 Compatibility matrix
Maintain a matrix: Kernel version × Provider/Model × benchmark results.
This is the empirical backbone of any efficiency claim made by CLL Proxy.

## 5. Current Status

- **Opus-era validation:** complete (historical baseline).
- **Fable 5 validation:** PENDING. Treat all efficiency and consistency claims as unverified for Fable 5 until the regression suite is executed.
- **Multi-provider validation (Gemini/GPT/Groq):** partial.

## 6. Corpus ↔ Kernel Decoupling (New in v1.1)

The **corpus** (docs 01–09) is the single source of truth for the CLL
specification. The **synthesized runtime kernel** (`CLL-Kernel_v1_2_1`,
Micro/Standard/Full) is a downstream build loaded into model context by
CLL Proxy alongside the validator.

These are decoupled on purpose:

- Corpus changes cost nothing at runtime (documentation only).
- Kernel changes cascade to `cll_validator.py` (hard-coded `COMPACT_ROOTS`,
  AVR list, V-checks), `cll_compiler.py`, and stored CoS in `rules.db`.

The corpus therefore advances to v1.2 immediately, while the runtime kernel
stays **pinned at v1.2.1-runtime**. The delta between the (now-correct) corpus
and the running kernel is tracked as the **Kernel Conformance Gap**, itemized
in the Dictionary, SPEC and Algorithm documents. Machine-relevant gap items
(UR length 3–13, removed consonant whitelist, `Rating` removal, `Variable`
addition, canonical `Validate`/`ValidateParam`/`ValidateCtx` names) migrate to
the kernel **only** when §4.2's regression suite is re-run — never in lockstep
with a corpus edit.

The planned `cll_norms.json` is the mechanism that will close this gap
permanently: once built, both the corpus (as its printed reference) and the
tooling (as its runtime source) read the same normative values, so the two
cannot drift again.

---
*End of CLL Model Compatibility Note v1.1*
