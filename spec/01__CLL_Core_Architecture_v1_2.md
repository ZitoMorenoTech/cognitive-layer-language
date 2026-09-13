# CLL — Core Architecture v1.2
**Cognitive Layer Language – Core System**

> Normative values are listed inline across this corpus, which is the
> single source of truth. A machine-readable `cll_norms.json` extract is
> planned as the build-time source for tooling (Roadmap, Track A).

**Changes vs v1.1:**
- SC set extended with `V-` (Validation) → nine Semantic Categories (§3.2).
- Three modules formalized and given dedicated documents: Block Types (§3.6), Global Variable System (§3.7 → doc 09), Literal Preservation (§3.8 → doc 06).
- Action Verb Registry introduced as the source of `Act:` verbs (§3.5, §4 → doc 07).
- CoS Container structure (META / GLOBAL / MODULES) referenced (§8 → doc 08).
- Convergence Efficiency framing retained from v1.1 (the runtime kernel's flat "40–70%" claim is drift; see the Kernel Conformance Gap in SPEC v1.2, §9).

## 1. Purpose

The Cognitive Layer Language (CLL) is a formal system designed to modulate,
structure and direct the reasoning processes of an AI model. It achieves this
by defining compact, stable and universal semantic layers that provide a
consistent cognitive framework across tasks, domains and model architectures.

**Cognitive Precedence Rules:**
1. **Ambiguity Reduction** – ensuring clarity and eliminating multiple interpretations.
2. **Cognitive Precision** – promoting accurate, well-defined reasoning operations.
3. **Consistency** – maintaining stable and reproducible cognitive behavior across similar contexts.

**Design Goals (emergent outcomes, not precedence rules):**

4. **Standardization of mental processes** – uniform reasoning structures across tasks.
5. **Convergence Efficiency** – reducing the number of iterations and the total cost (human time + tokens across the full task loop) required to reach a standardized, acceptable output. Per-prompt token savings are a possible second-order effect, never the primary claim (see CLL-SPEC §9).

## 2. Language Foundations (Qualitative)

The CLL draws design inspiration from five base languages. This is a
qualitative design rationale, not a quantitative model: the functional
justification of any root is its measured behavior in target tokenizers
and models, not its linguistic genealogy.

| Language | Functional Category | Design Contribution |
|---|---|---|
| EN | Cognitive base | Universal compatibility, dominant in training corpora, broad semantics |
| DE | Structural roots | Precise conceptual roots, compound formation, semantic categorization |
| NL | Linguistic bridge | Compact morphology, balance between simplicity and precision |
| SV | Cognitive minimalism | Semantic cleanliness, morphological regularity |
| ID | Morphological simplicity | Extreme regularity, low inflection, suited to compressed forms |

## 3. Core Modules

### 3.1 UR – Universal Roots

Short, stable, cross-linguistic roots derived from the five base languages.
Length: 3–13 (Standard), 2–4 (Compact). Full lexicon in the Canonical
Dictionary v1.2.

Examples: `Analys`, `Kontext`, `Konfig`, `Fokus`, `Exec`, `Plan`, `Data`

Properties:
- Stable semantics across contexts
- Extremely low ambiguity
- One root = one cognitive concept (no overloading)

### 3.2 SC – Semantic Categories

Semantic prefixes that classify the cognitive function of each token.

| Prefix | Function | Prefix | Function |
|---|---|---|---|
| `A-` | Analysis | `X-` | Execution |
| `S-` | Structure | `O-` | Output |
| `D-` | Data | `M-` | Memory |
| `C-` | Config | `R-` | Reasoning |
| | | `V-` | Validation |

**Note:** SC prefixes are **token** prefixes, not **line keys**. They appear
inside `Int`/`Ctx`/`Cfg`/`Act` lines as part of an SCUR token. A line that
begins with `R:` or `V:` (a bare prefix used as a line key) is INVALID
(validator class V13; see Grammar v1.2, §4).

### 3.3 MP – Morphological Patterns

Rules ensuring consistency, compactness and structural predictability.

Core patterns:
- CVC / CCV / CVCC structures preferred
- Truncated roots for compactness
- Removal of unnecessary inflections
- Full consonant skeleton retained during compression (the v1.0
  "structural consonant" whitelist was removed in Algorithm v1.1 and remains removed)
- Consecutive duplicate consonants collapse (`Summar` → `smmr` → `Smr`)

### 3.4 CM – Compact Mode

Cognitive compression mode for long, dense or resource-constrained tasks.
Derivation is fully defined by the UR→CM Compression Algorithm v1.2.

**Conformance rule:** every CM is an ordered subsequence of its normalized UR,
contains its first character, is length 2–4, and is unique.

Example:

```cll
A-Aly Kntx
C-Kfg Tn
X-Exe Bld
```

### 3.5 CL – Cognitive Layers

Structured reasoning flow. Canonical order:

```cll
[CLL: 1.2]        (optional version header, recommended)
Int: A-Analys Goal
Ctx: D-Data Input
Cfg: C-Konfig Params
Act: X-Exec Build            (verb from the Action Verb Registry, doc 07)
Out-Lang: Natural
```

### 3.6 BT – Block Types (New in v1.2)

Every block is one of two types, determined by its `Act:` verb (from the AVR):

- **Declarative (Dcl)** — stores a rule/constraint; produces no output; `Out-Lang`/`Out` OPTIONAL. Verbs: `Def`, `Enf`, `Prs`, `Apl`, `Stb`, `Fnl`.
- **Executable (Exc)** — produces output; `Out-Lang`/`Out` REQUIRED. Verbs: `Bld`, `Gen`, `Smr`, `Eval`, `Cmp`, `Dsc`, `Otl`, `Vld`.

If no marker is present, assume Declarative. Full registry in doc 07.

### 3.7 GVS – Global Variable System (New in v1.2)

Values declared once at CoS level and reused across modules:

```cll
Var: Tickers = "CRWD,PANW,FTNT"
Ctx: D-Data Targets:$Tickers
```

Declared in the GLOBAL section, PascalCase, read-only, resolved before
execution. Full specification in doc 09.

### 3.8 LP – Literal Preservation (New in v1.2)

Verbatim content capsules for templates, code and embedded DSLs, marked
by `***Literal***` … `***End Literal***` and governed by a preceding CLL
block whose `Cfg:` line contains `Format:Literal`. Full specification in
doc 06.

## 4. CLL Execution Flow

`Int → Ctx → Cfg → Act → Out-Lang`

1. Intention interpretation
2. Context analysis
3. Cognitive configuration
4. Action execution (verb resolved against the AVR, doc 07)
5. Output generation (skipped for Declarative blocks)

## 5. Design Principles

- Clarity over length
- Precision over aesthetics
- Universality over localism
- Stability over variation
- Semantics over syntax
- Measured claims over assumed claims

## 6. Example Layer (Standard)

```cll
CLL: 1.2
Int: A-Analys Kontext
Ctx: D-Data UserReq
Cfg: C-Konfig Tone:Neutral Len:Short
Act: X-Exec Build
Out-Lang: Natural
```

## 7. Example Layer (Compact)

```cll
CLL: 1.2
Int: A-Aly Kntx
Ctx: D-Dta Req
Cfg: C-Kfg Tn:Ntrl Ln:S
Act: X-Exe Bld
Out: Nat
```

## 8. CoS Container

A block is the smallest unit; a **CoS** is the deliverable that groups
blocks. Every CoS contains a META block, a GLOBAL section, and one or more
domain MODULES, and may inherit from a base CoS. Full specification in doc 08.

---
*End of CLL Core Architecture v1.2*
