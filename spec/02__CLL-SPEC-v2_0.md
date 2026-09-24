# CLL-SPEC v2.0 (Technical Specification)
**Cognitive Layer Language – Formal Standard**

> Normative values are listed inline in this corpus (single source of truth).
> A machine-readable `cll_norms.json` extract is planned for tooling (Roadmap).

**Version note (v1.2 → v2.0):** major release. The GLOBAL section is now an
explicit `***Global***` container (§11a → Grammar v2.0 §5.8, doc 09), and
declaring globals outside it is INVALID (V17, now Critical). This break is what
makes v2.0 major; existing v1.x CoS require migration.

**Changes vs v1.2:**
- **`***Global***` container made normative** for the GLOBAL section (§5.4, §11a).
  Two authoring modes: formal (`Var:`) and natural (free text resolved before
  execution).
- **V17 re-purposed and promoted** from a positional Warning to a container-
  integrity **Critical** check: ≤1 `***Global***` per CoS, and every `Var:`
  inside it.
- All v1.2 rules otherwise retained: `V-` prefix (nine SC), closed line-key set
  incl. `Var`, Block Type rules + AVR, TypedValues, Literal Preservation.
- §9 (Convergence Efficiency + mandatory measurement constraints) retained; the
  runtime kernel's flat "40–70%" claim remains recorded as drift.

> **Kernel Conformance Gap (informative).** In addition to the v1.2 gap items
> (UR length, consonant whitelist, `Rating` removal, canonical Validate names),
> v2.0 adds the `***Global***` container and natural-mode resolution, absent from
> the runtime kernel. These migrate on the kernel side only when the per-provider
> regression suite is re-run (doc 05).

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

## 4a. Block Type Rules

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
Literal blocks and natural-mode Global blocks), free-form prose, digits inside
UR/CM roots, numeric collision suffixes, nested Literal or Global blocks,
Literal markers without a governing `Format:Literal` block, `Var:` declarations
outside the `***Global***` container, more than one `***Global***` block, and
**custom/unregistered line keys** (`R:`, `Note:`, `Rule:`, `Rationale:`,
`Comment:`, `Why:`, `Because:`, `Reason:`, `Detail:`, `Info:`, …) — only the
seven keys in §5.1 are valid (V13).

**5.3 Allowed:** quoted strings (with `\"` escaping), `Key:Value` pairs,
TypedValues (`ARRThr:>20%`, `EVRange:10-20x`), compact flags,
`$VarName` references, domain-specific tokens, Literal blocks marked
by `***Literal***`/`***End Literal***` when governed by a `Format:Literal` block,
and the `***Global***` container for the GLOBAL section.

**5.4 GLOBAL section (New in v2.0).** Global variables are declared inside the
`***Global***` … `***End Global***` container (Grammar v2.0 §5.8). The container
appears at most once, may sit anywhere after META, and holds either `Var:` lines
(formal mode) or natural-language text resolved to `Var:` before execution
(natural mode). A `Var:` line outside the container is INVALID (V17).

## 6. Compact Mode (CM)

**6.1** Derivation exclusively via UR→CM Algorithm v1.2.

**6.2** Layer changes: `Out-Lang` → `Out`. Parameters shortened
(`Tone` → `Tn`, `Length` → `Ln`, `Domain` → `Dom`). Line keys never compress.
`Format:Literal` does not compress. `$VarName` references do not compress.
The `***Global***` / `***Literal***` markers do not compress.

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
CLL: 2.0
***Global***
Var: Threshold = 20
***End Global***
Int: A-Analys Kontext
Ctx: D-Data UserReq Thr:$Threshold
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
Var: Threshold = 20                  <- Var: outside ***Global*** (V17)
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
  *(Registry grammar: planned for a later release — see Roadmap, Track A.)*

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

## 11. CoS Container, Variables, Literals

- **CoS Container** (META / GLOBAL / MODULES, inheritance): doc 08.
- **Global Variable System** (`***Global***`, `Var:` / `$Ref`): doc 09.
- **Literal Preservation** (`***Literal***`, `Format:Literal`): doc 06.
- **Validation schema** (V1–V13 + V17 critical, V14–V16 + V18 extended): CLL Validator v2.0.

## 11a. GLOBAL Container (New in v2.0)

The GLOBAL section is delimited by `***Global***` … `***End Global***`:

- At most one container per CoS; position free after META.
- Formal mode: `Var:` lines. Natural mode: free text resolved to `Var:` by the
  executing model before the modules run (Compiler spec; safety net is V8).
- Every global `Var:` MUST be inside the container (V17, Critical).

---
*End of CLL-SPEC v2.0*
