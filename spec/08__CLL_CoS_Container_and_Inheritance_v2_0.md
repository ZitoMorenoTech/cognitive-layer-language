# CLL — CoS Container & Inheritance v2.0
**Cognitive Layer Language — CoS Structure Above the Block**

> Normative values: this corpus (single source of truth); `cll_norms.json` planned.

**Version note (v1.2 → v2.0):** major release. The GLOBAL section is now the
explicit `***Global***` container (Grammar v2.0, §5.8) rather than a positional
`# GLOBAL` comment. Existing v1.x CoS MUST be migrated (§4, Migration).

**Changes vs v1.2:**
- §4 rewritten: GLOBAL section = the `***Global***` container; position is free;
  at most one per CoS; the `# GLOBAL` comment is now an optional label only.
- §7 validation map updated: V17 re-purposed from a positional Warning to a
  container-integrity Critical check.

## 1. Purpose

A **block** is the smallest CLL unit (`Int`/`Ctx`/`Cfg`/`Act`/`Out`).
A **CoS** (the deliverable authored and stored in the rule library) groups
blocks into a structured document. This document defines the required
container structure and the inheritance model.

## 2. Required Structure

A valid CoS MUST contain:

1. A **META** block.
2. A **GLOBAL** section, delimited by the `***Global***` container (optional —
   present only when the CoS declares global variables/rules).
3. One or more domain **MODULES** (CLL blocks).

META MUST come first. The `***Global***` container and the modules MAY appear in
any order after META (the container is recognized by its markers, not its
position), but by convention the GLOBAL section is written before the modules
for readability. Every block MUST have a valid Block Type (doc 07).

## 3. META Block

```text
# META
Ver: <version> Dom:<domain> Auth:<author> Date:<YYYY-MM> [Base:<baseCoS>]
```

Rules:
- The META block is the first non-comment content of the CoS.
- Minimum required fields: `Ver` and `Dom` (validator **V10**, Warning).
- `Auth` and `Date` are recommended.
- `Base` is optional and enables inheritance (§5).

## 4. GLOBAL Section

The GLOBAL section is the `***Global***` container (Grammar v2.0, §5.8). It
holds display/output conventions expressed as `Var` declarations and any global
values the modules reference.

```text
# GLOBAL
***Global***
Var: Pure = "CRWD,PANW,FTNT,ZS"
Var: ARRThr = 20
***End Global***
```

Rules:
- **At most one `***Global***` container per CoS** (validator **V17**, Critical).
- Every `Var:` declaration MUST live inside it; a `Var:` line outside the
  container is INVALID (**V17**).
- The container's `Var` declarations are resolved before any module that
  references them runs.
- Two authoring modes are available (formal `Var:` lines or natural free text);
  see doc 09.
- The `# GLOBAL` comment is an optional human label, not a parser signal.

**Migration from v1.x.** A CoS that declared globals as bare `Var:` lines under a
positional `# GLOBAL` comment MUST wrap them in the container:

```text
BEFORE (v1.x — now INVALID)     AFTER (v2.0)
# GLOBAL                        # GLOBAL
Var: Pure = "..."               ***Global***
Var: ARRThr = 20                Var: Pure = "..."
                                Var: ARRThr = 20
                                ***End Global***
```

## 5. Inheritance (Base)

A CoS MAY declare `Base:<baseCoS>` in its META block.

```text
# META
Ver: 2.0 Dom:CyberSec Auth:Zito Date:2026-04 Base:FinAnalysis
```

Semantics:
- All modules from `Base` are loaded first.
- A module in the domain CoS **overrides** a Base module with the same `Int:` Goal token.
- Non-overridden Base modules remain active.
- Overriding modules MUST reuse the Base module's Goal name (validator **V18**, Warning).
- If both the Base and the domain CoS declare a `***Global***` container, the
  domain container's `Var` set is merged over the Base's; a name declared in both
  takes the domain value. (A duplicate name *within a single* container remains
  invalid.)

## 6. Modules

- Each module is a CLL block (Standard or Compact — one mode per CoS, validator **V7**).
- Comment lines (`#`) may separate and label modules; they are not CLL block lines.
- A `Ctx:` line that needs more than 4 tokens MUST be split into sub-modules (validator **V14**).

## 7. CoS-Level Validation Map

| Rule | Scope | Severity |
|---|---|---|
| V7 | Single mode across the whole CoS | Critical |
| V8 | All `$Var` references resolve to declared `Var` lines | Critical |
| V10 | META block present (`Ver`, `Dom`) | Warning |
| V11 / V12 | Literal governance / no nesting | Critical |
| V13 | No custom line keys | Critical |
| V17 | Global container integrity (≤1 container; all `Var:` inside it) | **Critical (v2.0)** |
| V18 | Inheritance Goal-name match | Warning |

Full definitions: CLL Validator v2.0.

## 8. Implementation Note (CLL Proxy)

The CLL Proxy runtime selects a synthesized kernel per CoS by size
(Micro `<1000` chars, Standard `≥1000`). This is a runtime loading choice,
not part of this specification; a CoS is valid independently of which kernel
tier is used to interpret it.

> **Kernel Conformance Gap (informative).** The runtime kernel still expects the
> positional `# GLOBAL` convention and has no `***Global***` container or
> natural-mode resolution. Migrating cascades to `cll_compiler.py` and
> `cll_validator.py` (V17 re-purpose) and is gated behind the per-provider
> regression suite (doc 05).

---
*End of CLL CoS Container & Inheritance v2.0*
