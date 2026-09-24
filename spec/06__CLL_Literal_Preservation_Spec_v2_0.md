# CLL — Literal Preservation (LP) Specification v2.0
**Cognitive Layer Language — Verbatim Content Capsules**

> Normative values: this corpus (single source of truth); `cll_norms.json` planned.
> Extends: CLL-Grammar v2.0 (§5.7), CLL-SPEC v2.0 (§5, §11)

**Version note (v1.2 → v2.0):** the LP mechanism itself is **unchanged**. The
version is aligned to the v2.0 corpus, and §7 is added to state how LP relates to
the new sibling container, `***Global***` (Grammar v2.0, §5.8).

**Changes vs v1.2:**
- §7 added — relationship to the `***Global***` container: the two are **sibling
  containers**, never nested in each other.
- Version aligned to v2.0. No change to markers, governance, semantics or
  validator codes (V11/V12).

## 1. Purpose

Literal Preservation (LP) lets a CoS carry content that the pipeline and
the receiving LLM MUST treat VERBATIM: output templates, exact-format
skeletons, placeholder structures (`{titulo}`, `{fecha}`), and embedded
non-CLL DSLs (Power Query M, SQL, regex). LP is a sanctioned exception to CLL
strictness: inside the markers, the grammar does not apply.

## 2. Syntax

```bnf
<LiteralBlock> ::= <LitOpen> <Newline> <LiteralBody> <Newline> <LitClose>
<LitOpen>      ::= "***Literal***"
<LitClose>     ::= "***End Literal***"
<LiteralBody>  ::= any character sequence not containing <LitOpen>
```

Rules:
- `<LitOpen>` and `<LitClose>` MUST each appear on their own line, with no leading or trailing characters.
- `<LiteralBody>` preserves whitespace, indentation, newlines and special characters.

Example:

```cll
Int: A-Analys PQTemplate
Ctx: D-Data SourceCode
Cfg: C-Konfig Format:Literal Domain:PQ
Act: X-Exec Preserve
***Literal***
Reporte: {titulo} - Hallazgos: {lista}
***End Literal***
```

## 3. Semantics (Processor Obligations)

Any conforming CLL processor MUST:

- **3.1** Emit the `<LiteralBody>` to the model verbatim: no CLL translation (UR→CM never applies inside), no normalization, no trimming, no re-encoding of whitespace or newlines.
- **3.2** Exempt the `<LiteralBody>` from ALL structural validation: prohibited connectors, narrative prose, the multi-line restriction, token grammar and line-key checks (V13) — none apply inside the markers.
- **3.3** Preserve literal order and count when a CoS carries several literals.
- **3.4** Round-trip guarantee: `extract(parse(cos)) == original bodies`, byte-for-byte.

**Internal transport compression (clarification).** A processor MAY compress
repeated strings inside a literal for internal transport (e.g. extracting a
string that repeats 3+ times, 5+ chars, as an internal `Var`) **only if the
literal is restored byte-for-byte before it reaches the model** (§3.4). Such
compression is invisible: it never changes what the model receives. This is
an implementation optimization, not a change to the verbatim contract.

**Rationale for 3.2:** a template legitimately contains everything the grammar
prohibits outside (free prose, connectors, blank lines). Validating inside a
literal would defeat its purpose.

## 4. Structural Rules

- **4.1** A Literal block MUST be immediately preceded by a governing CLL block whose `Cfg:` line contains `Format:Literal` (validator **V11**). A Literal block with no governing block, or a governing block missing `Format:Literal`, makes the CoS INVALID.
- **4.2** The governing block typically uses a Declarative verb; `Preserve` (`Prs`) is the recommended default. Any Declarative verb (`Def`, `Enf`, `Prs`, `Apl`, `Stb`, `Fnl`) is acceptable.
- **4.3** Multi-line literals are exempt from the multi-line prohibition (SPEC §5.2). The exemption is safe because the body is opaque to the parser.
- **4.4** **Nesting is prohibited** (validator **V12**): `***Literal***` cannot appear inside a `<LiteralBody>`.
- **4.5** Markers MUST be balanced. An unbalanced marker count is a validation error in the V11/V12 family.
- **4.6** During structural validation, processors replace each literal with an opaque placeholder, validate the masked CoS, then restore. The placeholder participates in grammar as a FlagToken.

Multi-line template example (allowed):

```cll
Cfg: C-Konfig Format:Literal Domain:Doc
Act: X-Exec Preserve
***Literal***
Informe Ejecutivo
1. Resumen: {resumen}
2. Hallazgos y Riesgos: {hallazgos}
3. Recomendacion: {recomendacion}
***End Literal***
```

## 5. Interaction with Compact Mode

CM compression applies to everything OUTSIDE literals and to NOTHING inside
them. A CoS may be fully compressed around an uncompressed template; this is
the intended usage for standardized outputs. `Format:Literal` does not
compress (no `Fmt:Lit` shorthand). The governing block follows the mode of
the surrounding CoS; the literal payload itself is mode-agnostic.

## 6. Validator Codes

- **V11** (Critical): a Literal block lacks a governing CLL block with `Format:Literal` in `Cfg`.
- **V12** (Critical): nested `***Literal***` markers.
- Unbalanced markers map to the V11/V12 family.
- **W-LP** (optional): literal longer than a configurable size threshold (advisory only; never blocks).

## 7. Relationship to the Global Container (New in v2.0)

CLL v2.0 introduces a second marker-delimited container, `***Global***` (Grammar
v2.0, §5.8), for the GLOBAL section. The two are **sibling containers**, not
composable:

- A `***Literal***` block MUST NOT appear inside a `***Global***` block, and a
  `***Global***` block MUST NOT appear inside a `***Literal***` block. Both are
  covered by their respective no-nesting rules (V12 for Literal; V17 for Global).
- They serve opposite purposes: Literal makes content **opaque and verbatim**
  (the grammar switches off, nothing is interpreted); Global (formal mode) is
  **fully structured** (`Var:` lines), and Global (natural mode) is **interpreted
  and resolved** to `Var:` before execution — the reverse of verbatim.
- A CoS may contain both: a `***Global***` container for its shared values and
  one or more `***Literal***` blocks for its verbatim templates, each governed by
  its own preceding CLL block.

---
*End of CLL Literal Preservation Spec v2.0*
