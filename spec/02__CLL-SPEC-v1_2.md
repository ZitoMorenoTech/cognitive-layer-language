# CLL-SPEC v1.2 (Technical Specification)
**Cognitive Layer Language – Formal Standard**

> Normative values are listed inline in this corpus (single source of truth).
> A machine-readable `cll_norms.json` extract is planned for tooling (Roadmap).

**Changes vs v1.1:**
- `V-` (Validation) added to the reserved prefixes → nine SC (§2).
- `Var` added to the line-key set; custom/unregistered line keys prohibited (§5, V13).
- Block Type rules formalized (§4a) and the Action Verb Registry made normative for `Act:` verbs (§4a → doc 07).
- TypedValue parameters (`>20%`, `10-20x`) admitted (§5.3 → Grammar §5.2).
- CoS Container, GVS and Literal Preservation referenced (§11 → docs 08 / 09 / 06).
- §9 (Convergence Efficiency + mandatory measurement constraints) retained from v1.1; the runtime kernel's flat "40–70%" claim is recorded as drift.

> **Kernel Conformance Gap (informative).** The synthesized runtime kernel diverges from this SPEC on machine-relevant points: UR length `3–6`/`4–10` (spec: 3–13 / CM 2–4), structural-consonant whitelist (spec: removed), `Rating|Rtg` present (spec: rejected), and a flat "40–70% fewer tokens" claim in its §12 (spec §9: prohibited without per-tokenizer measurement). These are migrated on the kernel side only when the per-provider regression suite is re-run (doc 05).

## 1. SPEC Purpose

Defines the formal rules, constraints and structural standards for any
valid CLL implementation: deterministic interpretation, cross-domain
consistency, cognitive stability, convergence efficiency, reproducible
reasoning patterns.

## 2. Reserved Prefixes (SC)

| Prefix | Function | Prefix | Function |
|---|---|---|---|
| `A-` | Analysis | `X-` | Execution |
| `S-` | Structure | `O-` | Output |
| `D-` | Data | `M-` | Memory |
| `C-` | Config | `R-` | Reasoning |
| | | `V-` | Validation |

Rules:
- Prefix must appear at the start of every root token.
- Prefix cannot be omitted in Standard Mode.
- Prefix must remain intact in Compact Mode.
- SC prefixes are **token** prefixes, not **line keys**. A line beginning with a bare prefix (`R:`, `V:`) is INVALID (§5, V13).

## 3. Root Rules (UR)

### 3.1 Length
- Standard Mode: 3–13 characters
- Compact Mode: 2–4 characters

### 3.2 Morphology
- Prefer CVC / CCV / CVCC patterns
- Avoid diphthongs and ambiguous endings
- No digits, no special characters
- Full consonant skeleton retained (no whitelist); duplicate consonants collapse
- Compression governed exclusively by Algorithm v1.2

### 3.3 Semantic stability
- One root = one cognitive concept. No overloading.
- Semantic overlap across domains is grounds for deprecation or rejection (`Rate` deprecated, `Rating` rejected — see Dictionary §2).

## 4. Block Structure (CL)

A valid CLL block contains, in this exact order:

```cll
[CLL: <major>.<minor>]     (optional version header, RECOMMENDED)
Int: <A-Root> <Goal>
Ctx: <D-Root> <Inputs>
Cfg: <C-Root> <Parameters>
Act: <X-Root> <AVR-Verb>
Out-Lang: <OutputMode>     (Out: in Compact Mode; OPTIONAL for Declarative blocks)
```

Rules:
- The version header, when present, MUST be the first line.
- Blocks without a header are interpreted under the parser's default version (CLL Proxy default: latest stable).
- No layer may be omitted in Standard Mode, except `Out-Lang`/`Out` for Declarative blocks.
- Layers appear exactly once. No blank lines inside the block.

## 4a. Block Type Rules (New in v1.2)

Every block MUST be classifiable as Declarative or Executable, determined
by the verb in the `Act:` line:

- **Declarative** verbs: `Def`, `Enf`, `Prs`, `Apl`, `Stb`, `Fnl` — no output; `Out-Lang`/`Out` optional.
- **Executable** verbs: `Bld`, `Gen`, `Smr`, `Eval`, `Cmp`, `Dsc`, `Otl`, `Vld` — output required.

Rules:
- The `Act:` verb MUST come from the Action Verb Registry (doc 07).
- Unregistered verbs are INVALID and must be added to the AVR before use.

## 5. Syntax Rules

**5.1** Each line: `<Key>:` + single space + value tokens.
Line keys form a **closed set**: `Int`, `Ctx`, `Cfg`, `Act`, `Out-Lang`, `Out`, `Var`.

**5.2 Prohibited:** natural connectors (and/but/also) outside quotes,
conjugated verbs, narrative sentences, multi-line values (except inside
Literal blocks), free-form prose, digits inside UR/CM roots, numeric
collision suffixes, nested Literal blocks, Literal markers without a
governing `Format:Literal` block, and **custom/unregistered line keys**
(`R:`, `Note:`, `Rule:`, `Rationale:`, `Comment:`, `Why:`, `Because:`,
`Reason:`, `Detail:`, `Info:`, …) — only the seven keys in §5.1 are valid (V13).

**5.3 Allowed:** quoted strings (with `\"` escaping), `Key:Value` pairs,
TypedValues (`ARRThr:>20%`, `EVRange:10-20x`), compact flags,
`$VarName` references, domain-specific tokens, and Literal blocks marked
by `***Literal***`/`***End Literal***` when governed by a `Format:Literal` block.

## 6. Compact Mode (CM)

**6.1** Derivation exclusively via UR→CM Algorithm v1.2.

**6.2** Layer changes: `Out-Lang` → `Out`. Parameters shortened
(`Tone` → `Tn`, `Length` → `Ln`, `Domain` → `Dom`). Line keys never compress.
`Format:Literal` does not compress. `$VarName` references do not compress.

**6.3** Example:

```cll
Int: A-Aly Kntx
Ctx: D-Dta Req
Cfg: C-Kfg Tn:Ntrl Ln:S
Act: X-Exe Bld
Out: Nat
```

## 7. Valid / Invalid Patterns

### 7.1 Valid

```cll
CLL: 1.2
Int: A-Analys Kontext
Ctx: D-Data UserReq
Cfg: C-Konfig Tone:Neutral Len:Short
Act: X-Exec Build
Out-Lang: Natural
```

### 7.2 Invalid

```text
Int: I want you to analyze this      <- narrative sentence
Ctx: because the user said so        <- connector, prose
Act: X-Rte2 Build                    <- digit in root
R: extra reasoning line              <- custom line key (V13)
```

## 8. Domain Extensions

Domains may extend the Core with domain roots, parameters and actions
(CLL-PQ, CLL-SQL, CLL-QA, CLL-Doc, CLL-Ref, CLL-CyberSec).

Rules:
- Extensions must not override Core semantics.
- Extensions must follow SC and UR rules and remain CM-compatible.
- Domain actions MUST be registered in the AVR (doc 07).
- Extension tokens MUST be declared in a domain registry so validators
  can distinguish legitimate domain tokens from typos.
  *(Registry grammar: planned for v1.3 — see Roadmap, Track A.)*

## 9. Efficiency Principles

The CLL's primary efficiency claim is **CONVERGENCE EFFICIENCY**:

> Reduction in the number of iterations and in the total cost
> (human time + total tokens across the full task loop) required to
> reach a standardized output that meets a fixed acceptance criterion,
> relative to a natural-language baseline.

**Rationale:** task complexity drives ambiguity; ambiguity drives iteration.
A CLL/CoS rule package has a high first-iteration entry cost which is
amortized across subsequent tasks, reducing marginal cost per task and
output variance.

**Mandatory constraints on claims:**
- Per-prompt token-reduction percentages MUST NOT be asserted without
  per-tokenizer measurement. Character count is not token count.
- Every published figure MUST cite the model/provider, the benchmark
  task set, and the acceptance criterion used.
- Claims are valid only for the model generation on which they were
  measured (see Model Compatibility Note, doc 05).

> The runtime kernel's §12 "Reduction: 40–70% fewer tokens" is a flat,
> unqualified figure that violates the constraints above. It is drift, not
> spec, and is listed in the Kernel Conformance Gap.

## 10. Design Principles (Mandatory)

Clarity over length. Precision over aesthetics. Universality over
localism. Stability over variation. Semantics over syntax. Cognitive
determinism over stylistic freedom. Measured claims over assumed claims.

## 11. CoS Container, Variables, Literals (New in v1.2)

- **CoS Container** (META / GLOBAL / MODULES, inheritance): doc 08.
- **Global Variable System** (`Var:` / `$Ref`): doc 09.
- **Literal Preservation** (`***Literal***`, `Format:Literal`): doc 06.
- **Validation schema** (V1–V13 critical, V14–V18 extended): CLL Validator v1.2.1.

---
*End of CLL-SPEC v1.2*
