# CLL — Global Variable System (GVS) v2.0
**Cognitive Layer Language — Declared, Reusable Values**

> Normative values: this corpus (single source of truth); `cll_norms.json` planned.

**Version note (v1.2 → v2.0):** major release. Global variables are now declared
**inside an explicit `***Global***` container** (Grammar v2.0, §5.8). A `Var:`
line outside that container is INVALID. This is the not-backward-compatible
change that makes v2.0 a major version.

**Changes vs v1.2:**
- Declaration moved into the `***Global***` container (§2). The positional
  `# GLOBAL` comment convention is retired — the container is what the parser
  reads, and it may sit anywhere in the CoS.
- **Two authoring modes** formalized (§2a): **formal** (`Var:` lines) and
  **natural** (free text resolved to `Var:` by the executing model).
- V17 re-purposed from a positional Warning to a container-integrity Critical
  check (§4).

## 1. Purpose

The Global Variable System (GVS) lets a CoS declare a value once and reuse it
across modules, avoiding repetition and keeping shared constants (ticker
lists, thresholds, config) in a single place.

## 2. Declaration

All global variables are declared inside the `***Global***` container:

```bnf
<GlobalBlock> ::= "***Global***" <Newline> <GlobalBody> <Newline> "***End Global***"
<VarLine>     ::= "Var:" <SP> <VarName> <SP> "=" <SP> <VarValue>
<VarName>     ::= <Alpha> <AlphaNum>*
<VarValue>    ::= <StringToken> | <Number> | <FlagToken>
```

Rules:
- `VarName` is PascalCase, 2–12 characters.
- Values: quoted strings, comma-separated lists (inside a quoted string), or numbers.
- Declared **inside the `***Global***` block**, which may appear anywhere in the
  CoS but MUST precede execution (resolution happens before any module runs).
- **At most one `***Global***` block per CoS.**
- **Read-only** after declaration.

Formal example:

```cll
***Global***
Var: Pure = "CRWD,PANW,FTNT,ZS"
Var: Hybrid = "MSFT,CSCO,IBM"
Var: ARRThr = 20
***End Global***
```

## 2a. Authoring Modes (New in v2.0)

The container body accepts two modes with one syntax:

- **Formal mode** — the body is `Var:` lines, written directly. Intended for
  super-users fluent in CLL. Validated by the grammar as-is.
- **Natural mode** — the body is free natural-language text describing the
  intended values. The **executing model** resolves it to canonical `Var:` lines
  before the modules run (resolution contract in the Compiler spec). This is the
  on-ramp for users who should not have to learn CLL syntax: they state *what*
  they want; the system produces the `Var:` form.

Natural example (resolves to the three `Var:` lines above):

```cll
***Global***
El portafolio puro son CRWD, PANW, FTNT y ZS.
El hibrido son MSFT, CSCO, IBM.
El umbral minimo de ARR es 20%.
***End Global***
```

**Both modes produce the same internal `Var:` set.** After resolution, V8
verifies that every `$VarName` reference resolves — this is the safety net that
catches an incomplete or failed natural-mode extraction.

**Capability floor (informative).** Natural-mode resolution quality tracks the
executing model. Below a threshold (empirically near the Haiku→Sonnet boundary),
extraction may fail silently. For models below the floor, or for reproducibility-
critical CoS, use formal mode. See doc 05.

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

- **V8** (Critical): every `$VarName` reference MUST resolve to a `Var` declared
  inside the `***Global***` block. An unresolved reference is INVALID.
- **V17** (Critical, re-purposed in v2.0): container integrity — at most one
  `***Global***` block per CoS, and every `Var:` declaration MUST appear inside
  it. A `Var:` line outside the container, or a second container, is INVALID.
- Resolution collects all `Var` declarations from the container (resolving
  natural-mode text first), then scans `Ctx`/`Cfg` lines for `$` references.
- A `Var` declared but never referenced is permitted (no error).

## 5. Relationship to the Dictionary

The GVS mechanism (the `Var:` line key, the `***Global***` container, and `$`
references) is independent of the lexicon. The Canonical Dictionary entry
`Variable | Vr` (#51) exists so a Validation/Reasoning token can *refer to* the
concept of a variable in a block goal (e.g. `Int: A-Analys Variable`); it is not
required to declare or reference a variable — that is done purely with the
`***Global***` container, `Var:`, and `$Name`.

## 6. Style

- Names descriptive but concise: `Pure`, `Hybrid`, `ARRThr` (not `T`, not `MyVeryLongVariableName`).
- Extract to a `Var` when a value appears in 2+ modules (Style Guide §2.8).
- Keep the `***Global***` container as a single, self-contained section; an
  optional `# GLOBAL` comment above it is a fine human label.

## 7. Full Example (fragment)

```text
# META
Ver: 2.0 Dom:CyberSec Auth:Zito Date:2026-09

# GLOBAL
***Global***
Var: Pure = "CRWD,PANW,FTNT,ZS"
Var: ARRThr = 20
***End Global***

# MODULE 1
Int: A-Analys Growth
Ctx: D-Data Targets:$Pure Threshold:$ARRThr
Cfg: C-Konfig Domain:CyberSec
Act: X-Exec Evaluate
Out-Lang: Natural
```

> **Kernel Conformance Gap (informative).** The `***Global***` container is a
> v2.0 addition NOT yet present in the runtime kernel, which still recognizes the
> positional `# GLOBAL` convention. Migrating it cascades to `cll_compiler.py`
> (natural-mode resolution + container parsing) and `cll_validator.py` (V17
> re-purpose), and is gated behind the per-provider regression suite (doc 05).
> The dictionary addition `Variable|Vr` remains a separate gap item.

---
*End of CLL Global Variable System v2.0*
