# CLL — CoS Container & Inheritance v1.2
**Cognitive Layer Language — CoS Structure Above the Block**

> Normative values: this corpus (single source of truth); `cll_norms.json` planned.
> New document (v1.2): formalizes the CoS container, which previously existed only in the runtime kernel.

## 1. Purpose

A **block** is the smallest CLL unit (`Int`/`Ctx`/`Cfg`/`Act`/`Out`).
A **CoS** (the deliverable authored and stored in the rule library) groups
blocks into a structured document. This document defines the required
container structure and the inheritance model.

## 2. Required Structure

A valid CoS MUST contain, in order:

1. A **META** block.
2. A **GLOBAL** section (display/output rules, optional `Var` declarations).
3. One or more domain **MODULES** (CLL blocks).

Every block MUST have a valid Block Type (doc 07).

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

Appears after META and before the first module (validator **V17**).
Contains display/output conventions and any `Var` declarations (doc 09).
`Var` lines MUST precede the modules that reference them.

```text
# GLOBAL
Var: Pure = "CRWD,PANW,FTNT,ZS"
Var: ARRThr = 20
```

## 5. Inheritance (Base)

A CoS MAY declare `Base:<baseCoS>` in its META block.

```text
# META
Ver: 1.0 Dom:CyberSec Auth:Zito Date:2026-04 Base:FinAnalysis
```

Semantics:
- All modules from `Base` are loaded first.
- A module in the domain CoS **overrides** a Base module with the same `Int:` Goal token.
- Non-overridden Base modules remain active.
- Overriding modules MUST reuse the Base module's Goal name (validator **V18**, Warning).

## 6. Modules

- Each module is a CLL block (Standard or Compact — one mode per CoS, validator **V7**).
- Comment lines (`#`) may separate and label modules; they are not CLL block lines.
- A `Ctx:` line that needs more than 4 tokens MUST be split into sub-modules (validator **V14**).

## 7. CoS-Level Validation Map

| Rule | Scope | Severity |
|---|---|---|
| V7 | Single mode across the whole CoS | Critical |
| V8 | All `$Var` references resolve to GLOBAL declarations | Critical |
| V10 | META block present (`Ver`, `Dom`) | Warning |
| V11 / V12 | Literal governance / no nesting | Critical |
| V13 | No custom line keys | Critical |
| V17 | GLOBAL section after META, before modules | Warning |
| V18 | Inheritance Goal-name match | Warning |

Full definitions: CLL Validator v1.2.1.

## 8. Implementation Note (CLL Proxy)

The CLL Proxy runtime selects a synthesized kernel per CoS by size
(Micro `<1000` chars, Standard `≥1000`). This is a runtime loading choice,
not part of this specification; a CoS is valid independently of which kernel
tier is used to interpret it.

> **Kernel Conformance Gap (informative).** The container structure here
> matches the runtime kernel's CoS requirements. No machine-relevant change
> is introduced by this document; it only lifts the existing runtime behavior
> into the formal corpus.

---
*End of CLL CoS Container & Inheritance v1.2*
