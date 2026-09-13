# CLL — Global Variable System (GVS) v1.2
**Cognitive Layer Language — Declared, Reusable Values**

> Normative values: this corpus (single source of truth); `cll_norms.json` planned.
> New document (v1.2): formalizes the GVS, which previously existed only in the runtime kernel.

## 1. Purpose

The Global Variable System (GVS) lets a CoS declare a value once and reuse it
across modules, avoiding repetition and keeping shared constants (ticker
lists, thresholds, config) in a single place.

## 2. Declaration

```bnf
<VarLine>  ::= "Var:" <SP> <VarName> <SP> "=" <SP> <VarValue>
<VarName>  ::= <Alpha> <AlphaNum>*
<VarValue> ::= <StringToken> | <Number> | <FlagToken>
```

Rules:
- `VarName` is PascalCase, 2–12 characters.
- Values: quoted strings, comma-separated lists (inside a quoted string), or numbers.
- Declared in the **GLOBAL** section, before any module that references them.
- **Read-only** after declaration.

Examples:

```text
Var: Pure = "CRWD,PANW,FTNT,ZS"
Var: Hybrid = "MSFT,CSCO,IBM"
Var: ARRThr = 20
```

## 3. Reference

```bnf
<VarRefToken> ::= "$" <VarName>
```

Rules:
- Referenced only inside `Ctx:` or `Cfg:` lines.
- Resolved before block execution.
- Not nestable (`$($x)` is invalid).
- References never compress in Compact Mode (`$Pure` stays `$Pure`).

Example:

```cll
Ctx: D-Data Tickers:$Pure Threshold:$ARRThr
```

## 4. Resolution & Validation

- **V8** (Critical): every `$VarName` reference MUST resolve to a declared `Var` line. An unresolved reference is INVALID.
- Resolution collects all GLOBAL `Var` declarations, then scans `Ctx`/`Cfg` lines for `$` references.
- A `Var` declared but never referenced is permitted (no error).

## 5. Relationship to the Dictionary

The GVS mechanism (the `Var:` line key and `$` references) is independent of
the lexicon. The Canonical Dictionary entry `Variable | Vr` (#51) exists so a
Validation/Reasoning token can *refer to* the concept of a variable in a
block goal (e.g. `Int: A-Analys Variable`); it is not required to declare or
reference a variable — that is done purely with `Var:` and `$Name`.

## 6. Style

- Names descriptive but concise: `Pure`, `Hybrid`, `ARRThr` (not `T`, not `MyVeryLongVariableName`).
- Extract to a `Var` when a value appears in 2+ modules (Style Guide §2.8).
- Keep declarations grouped in the GLOBAL section under a `# GLOBAL` comment.

## 7. Full Example (fragment)

```text
# META
Ver: 1.2 Dom:CyberSec Auth:Zito Date:2026-09

# GLOBAL
Var: Pure = "CRWD,PANW,FTNT,ZS"
Var: ARRThr = 20

# MODULE 1
Int: A-Analys Growth
Ctx: D-Data Targets:$Pure Threshold:$ARRThr
Cfg: C-Konfig Domain:CyberSec
Act: X-Exec Evaluate
Out-Lang: Natural
```

> **Kernel Conformance Gap (informative).** The GVS matches the runtime
> kernel. The only dictionary-side addition is `Variable|Vr`, absent from the
> kernel; add it when the GVS is exercised at runtime (see Dictionary §Gap).

---
*End of CLL Global Variable System v1.2*
