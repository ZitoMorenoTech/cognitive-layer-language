# CLL Style Guide v2.0
**Authoritative Style and Writing Conventions for CLL**

> Normative values: this corpus (single source of truth); `cll_norms.json` planned.

**Version note (v1.2 → v2.0):** updated for the `***Global***` container. §6a
(Variable Style) now uses the container; a new best practice (§9.11) forbids
declaring `Var:` outside it.

**Changes vs v1.2:**
- §6a rewritten: variables are declared inside `***Global***`, with formal and
  natural authoring modes.
- §9.11 added: one `***Global***` container per CoS; never declare `Var:` outside it.
- Companion-document list aligned to v2.0.

## 1. Purpose

Writing conventions, formatting rules and best practices for consistent,
readable, stable CLL blocks across users, domains and implementations.

**Companion documents:**
- CLL-Core Architecture v2.0 (conceptual foundation)
- CLL-SPEC v2.0 (technical rules)
- CLL-Grammar v2.0 (formal syntax)
- UR→CM Algorithm v2.0 + Canonical Dictionary v2.0 (lexicon)
- Action Verb Registry v2.0 (doc 07)
- CoS Container & Inheritance v2.0 (doc 08)
- Global Variable System v2.0 (doc 09)
- Literal Preservation v2.0 (doc 06)
- Model Compatibility Note v2.0 (doc 05)
- CLL Validator v2.0 (V1–V13 + V17 critical, V14–V16 + V18 extended)

## 2. General Principles

- **2.1 Clarity over compression** — prefer Standard Mode unless Compact Mode is explicitly needed.
- **2.2** One concept per token.
- **2.3 Predictability** — same order, spacing and token structure, always.
- **2.4 Minimal natural language** — only inside quotes, Literal bodies, or a natural-mode `***Global***` body; only when required.
- **2.5 Determinism** — a CLL block should produce the same cognitive behavior (on the same model generation; see doc 05).
- **2.6 Declare the version** — start new blocks with `CLL: 2.0`.
- **2.7 Block-type awareness** — know whether you are writing a Declarative or Executable block before you write it.
- **2.8 Variable reuse** — if a value appears in 2+ modules, extract it to a `Var` inside `***Global***`.
- **2.9 Closed line-key set** — the seven keys are the ONLY valid block-line prefixes; never invent new ones.

## 3. Formatting Rules

- **3.1** Each line starts with Key + `:` + one space. No indentation. No blank lines inside the block.
- **3.2** Exactly one space after `:` and between tokens. No trailing spaces.
- **3.3** Capitalization: Keys in TitleCase; SC prefixes as uppercase letter + hyphen; UR roots in mixed case (`Konfig`, `Analys`); variable names in PascalCase.
- **3.4** Token order: 1) SCURToken 2) ParamTokens 3) VarRefTokens 4) Flags 5) Strings.

## 4. Standard Mode Style

- **4.1** Full UR roots: `Analys`, `Kontext`, `Konfig`, `Exec`, `Fokus`, `Plan`, `Data`.
- **4.2** Full parameter names: `Tone`, `Length`, `Domain`, `Mode`, `Detail`.
- **4.3** Descriptive values: `Tone:Formal`, `Length:Short`, `Domain:Email`.
- **4.4** No abbreviations unless domain-specific.
- **4.5** Full AVR verb names in `Act:` — `Act: X-Exec Build` (not `Act: X-Exec Bld`).

## 5. Compact Mode Style

- **5.1** CM roots ONLY from the Canonical Dictionary (never improvise a compression).
- **5.2** Shortened parameters: `Tn`, `Ln`, `Dom`.
- **5.3** `Out` instead of `Out-Lang`.
- **5.4** Keep compression consistent across the block — never mix Standard and Compact roots.
- **5.6** CM AVR verb forms in `Act:` — `Act: X-Exe Bld` (not `Act: X-Exe Build`).

## 6. Parameter Style

- PascalCase for multi-word parameters (`DetailLevel:High`).
- No underscores or hyphens in parameter names.
- Recommended order: `Tone` → `Length` → `Domain` → `Mode` → `Detail`.

### 6a. Variable Style (v2.0)
- Declare all globals inside a single `***Global***` … `***End Global***`
  container. Group it under a `# GLOBAL` comment label for readability.
- **Formal mode:** `Var: PascalName = <Value>` lines (2–12 chars; `Pure`,
  `Hybrid`, `ARRThr`). Prefer this for authored/reviewed CoS.
- **Natural mode:** free text describing the values; the executing model resolves
  it to `Var:` lines before execution. Use this for end-user input. Don't mix a
  half-formal, half-natural body — pick one mode per container.
- Reference with `$Name` in `Ctx`/`Cfg` lines. Read-only. See doc 09.

### 6b. TypedValue Style
- Thresholds with comparators: `ARRThr:>20%`, `GMThr:>=70%`.
- Ranges with a hyphen: `EVRange:10-20x`.

## 7. String Style

- Only when natural language is unavoidable; always quoted.
- Escape internal quotes as `\"` (Grammar §5.4).
- Short instructions, never paragraphs: `"Explain briefly"`.
- If content has line breaks or non-CLL syntax, use a Literal block (doc 06), not a long StringToken.

## 8. Naming Conventions

### 8.1 UR Naming
- Lengths: Standard 3–13, CM 2–4.
- Any consonant is legal; consecutive duplicates collapse.
- CM must be an ordered subsequence of the UR containing its first character.
- No digits. No ambiguous endings.

### 8.2 Domain Extensions
Short, clear identifiers (`PQ`, `SQL`, `Doc`, `Email`, `CyberSec`), declared
in the domain registry; domain actions registered in the AVR (doc 07).

### 8.3 Variable Naming
PascalCase, 2–12 characters (`Pure`, `ARRThr`; not `T` or `MyVeryLongVariableName`).

## 9. Best Practices

- **9.1** Start with the goal: `Int: A-Analys <Goal>`
- **9.2** Keep `Ctx` minimal — only necessary inputs (max 4 tokens).
- **9.3** Keep `Cfg` explicit — never assume defaults.
- **9.4** Keep the `Act:` verb from the AVR — never invent verbs.
- **9.5** Keep `Out` predictable — `Natural` unless the domain requires otherwise.
- **9.6** Never invent CM forms inline; propose missing roots through the dictionary lifecycle.
- **9.7** Include META in every CoS (doc 08).
- **9.8** Reach for Literals only when native CLL cannot express the content (doc 06) — every Literal adds opacity the AI cannot reason about.
- **9.11** One `***Global***` container per CoS; never declare `Var:` outside it (validator V17).

### 9.10 Never invent line keys
The valid line keys are closed: `Int`, `Ctx`, `Cfg`, `Act`, `Out-Lang`, `Out`, `Var`.
Do not append rationale, notes or rules as separate lines with custom keys.

WRONG (`R:` is an invented key; the content is redundant):

```cll
Int: A-Enf UserIdent
Ctx: D-Dta ModBy Field
Cfg: C-Kfg Req:TRUE Blk:TRUE
Act: X-Exe Enf
Out: Nat
R: User MUST provide preferred name. CoS MUST NOT proceed.
```

The structured tokens already encode the rule (`Act:X-Exe Enf` + `Cfg:Req:TRUE Blk:TRUE`). If rationale truly must be expressed, use a `#` comment line above the block, or a separate Declarative block using `R-` as a **token** prefix (`Int: R-Reason UserIdentRationale`), never as a line key.

## 10. Examples

### 10.1 Standard (Recommended)

```cll
CLL: 2.0
***Global***
Var: OutLang = "Spanish"
***End Global***
Int: A-Analys Kontext
Ctx: D-Data UserReq Lang:$OutLang
Cfg: C-Konfig Tone:Neutral Len:Short Domain:Email
Act: X-Exec Build
Out-Lang: Natural
```

### 10.2 Compact (High Compression)

```cll
CLL: 2.0
Int: A-Aly Kntx
Ctx: D-Dta Req
Cfg: C-Kfg Tn:Ntrl Ln:S Dom:Email
Act: X-Exe Bld
Out: Nat
```

## 11. Literal Preservation Style

- Governing block immediately precedes `***Literal***`; no blank line between them.
- Governing `Cfg:` MUST contain `Format:Literal`; default `Act:` verb is `Preserve` (`Prs`).
- Markers on their own lines; indentation inside the literal is preserved as-is.
- Works identically in Standard and Compact modes; the payload is mode-agnostic.
- `***Literal***` and `***Global***` are sibling containers; never nest one in the other.

---
*End of CLL Style Guide v2.0*
