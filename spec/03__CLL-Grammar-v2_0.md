# CLL-Grammar v2.0
**Formal Grammar for Cognitive Layer Language**

> Normative values: this corpus (single source of truth); `cll_norms.json` planned.

**Version note (v1.2 → v2.0):** v2.0 is a **major** release because it introduces
a change that is not backward-compatible: global variables and rules now live
inside an explicit `***Global***` container (§5.8), and a CoS that declares
global values *outside* that container is INVALID. Existing v1.x CoS that relied
on the positional `# GLOBAL` comment convention MUST be migrated (§5.8, Migration).

**Changes vs v1.2:**
- **`<GlobalBlock>` production added (§5.8)** — an explicit container for the
  GLOBAL section, consistent with `<LiteralBlock>` (§5.7). Two authoring modes:
  **formal** (`Var:` lines) and **natural** (free text resolved to `Var:` by the
  executing model). This resolves the previously-open GLOBAL design decision.
- **Positional GLOBAL convention retired.** `# GLOBAL` is now only an optional
  human label; the parser recognizes the section by the `***Global***` markers,
  not by position (validator V17 re-purposed — see SPEC v2.0 / Validator).
- `<LineKey>` set unchanged (seven keys, closed set): `Int`, `Ctx`, `Cfg`,
  `Act`, `Out-Lang`, `Out`, `Var`.
- All other productions carried over from Grammar v1.2 unchanged.

## 1. Scope

Structural rules for a valid CLL block in Standard Mode and Compact
Mode. This grammar defines structure and form, not semantics.

## 2. Top-Level Structure

```bnf
<Block> ::= [<VerLine>] <IntLine> <CtxLine> <CfgLine> <ActLine> [<OutLine>]
```

Standard Mode (Executable):

```cll
[CLL: <Version>]
Int: <IntValue>
Ctx: <CtxValue>
Cfg: <CfgValue>
Act: <ActValue>
Out-Lang: <OutValue>
```

`Out-Lang` is OPTIONAL for Declarative blocks. In Compact Mode the last
line MAY be written `Out: <OutValue>`.

A block MAY be followed by a Literal block (§5.7) when its `Cfg:` line
contains the ParamToken `Format:Literal`.

## 3. Version Header

```bnf
<VerLine> ::= "CLL" ":" <SP> <Version>
<Version> ::= <Digit>+ "." <Digit>+
<Digit>   ::= "0"-"9"
```

Optional, but when present MUST be the first line of the block.

## 4. Line Keys

```bnf
<LineKey> ::= "Int" | "Ctx" | "Cfg" | "Act" | "Out-Lang" | "Out" | "Var"
```

Rules:
- Keys are case-sensitive and exact.
- Each line: `<LineKey> ":" <SP> <TokenList>` (or a `<VarLine>` for `Var`).
- The set is **CLOSED**. Any other line key is INVALID (validator class V13).
  Examples of INVALID keys: `R:`, `Note:`, `Rule:`, `Rationale:`, `Comment:`,
  `Why:`, `Because:`, `Reason:`, `Detail:`, `Info:`.
- Comment lines starting with `#` are NOT line keys — they are out-of-block metadata, permitted between blocks.
- SC prefixes (`A-`, `R-`, `V-`, …) are TOKEN prefixes inside a line, not line keys: `Int: R-Reason X` is valid; `R: X` is not.

## 5. Tokens

```bnf
<TokenList> ::= <Token> | <Token> <SP> <TokenList>
<SP>        ::= " "
<Token>     ::= <SCURToken> | <ParamToken> | <FlagToken> | <StringToken> | <VarRefToken>
```

### 5.1 SC+UR Token

```bnf
<SCURToken> ::= <SC> <UR>
<SC> ::= "A-" | "S-" | "D-" | "C-" | "X-" | "O-" | "M-" | "R-" | "V-"
<UR> ::= <RootChar><RootChar> [<RootChar>]*
<RootChar> ::= "A"-"Z" | "a"-"z"                ; NO digits
```

Length rules:
- Standard Mode: 3–13 characters.
- Compact Mode: 2–4 characters (legalizes canonical `Vp`, `Vr`).
- SC prefix always present and intact in both modes.

Examples: `A-Analys` | `D-Data` | `C-Konfig` | `X-Exec` | `A-Aly` (CM) | `V-Valid`

### 5.2 Parameter Token

```bnf
<ParamToken> ::= <ParamKey> ":" <ParamValue>
<ParamKey>   ::= <AlphaNum>+
<ParamValue> ::= <AlphaNum>+ | <TypedValue>
<TypedValue> ::= <Comparator> <Number> <Unit>
<Comparator> ::= ">" | ">=" | "<" | "<=" | "="
<Number>     ::= <Digit>+
<Unit>       ::= "%" | "x" | "d" | "k" | "m" | ""
<AlphaNum>   ::= "A"-"Z" | "a"-"z" | "0"-"9"
```

Examples: `Tone:Neutral` | `Len:Short` | `Tn:Ntrl` (CM) | `ARRThr:>20%` | `EVRange:10-20x` | `Format:Literal`

### 5.3 Flag Token

```bnf
<FlagToken> ::= <AlphaNum>+
```

Examples: `Nat` | `CM` | `PQ`

### 5.4 String Token

```bnf
<StringToken> ::= "\"" <StringBody> "\""
<StringBody>  ::= ( <SafeChar> | <EscapedQuote> )*
<SafeChar>    ::= any character except "\"" and "\\"
<EscapedQuote>::= "\\" "\""
```

Example: `"Explain the term \"token\" briefly"`

### 5.5 Variable Reference Token

```bnf
<VarRefToken> ::= "$" <VarName>
<VarName>     ::= <Alpha> <AlphaNum>*
```

Rules: MUST reference a declared `Var` line inside the `***Global***`
container (§5.8); resolved before execution; not nestable (`$($x)` invalid).
Examples: `$Pure`, `$ARRThr`.

### 5.6 Variable Declaration Line

```bnf
<VarLine>  ::= "Var:" <SP> <VarName> <SP> "=" <SP> <VarValue>
<VarValue> ::= <StringToken> | <Number> | <FlagToken>
```

Examples: `Var: Pure = "CRWD,PANW,FTNT,ZS"` | `Var: ARRThr = 20`.
`VarName` is PascalCase, 2–12 characters. A `<VarLine>` MUST appear inside the
`***Global***` container (§5.8). Full semantics in doc 09.

### 5.7 Literal Block

```bnf
<LiteralBlock> ::= <LitOpen> <Newline> <LiteralBody> <Newline> <LitClose>
<LitOpen>      ::= "***Literal***"
<LitClose>     ::= "***End Literal***"
<LiteralBody>  ::= any character sequence not containing <LitOpen>
<Newline>      ::= "\n"
```

Rules: markers on their own lines, no leading/trailing characters;
`<LiteralBody>` preserves whitespace and special characters; MUST NOT
contain a nested `<LitOpen>`; MUST be immediately preceded by a CLL block
whose `Cfg:` line contains `Format:Literal`; lines inside are exempt from
line-key validation (V13). Full specification in doc 06.

### 5.8 Global Block (New in v2.0)

The GLOBAL section of a CoS is delimited by an explicit container, modeled on
the Literal block (§5.7). It is the single, canonical home for global variables
(and, where a domain defines them, global rules). It has **two authoring modes**
sharing one syntax.

```bnf
<GlobalBlock>        ::= <GblOpen> <Newline> <GlobalBody> <Newline> <GblClose>
<GblOpen>            ::= "***Global***"
<GblClose>           ::= "***End Global***"
<GlobalBody>         ::= <FormalGlobalBody> | <NaturalGlobalBody>
<FormalGlobalBody>   ::= <VarLine> (<Newline> <VarLine>)*
<NaturalGlobalBody>  ::= any character sequence not containing <GblOpen>
<Newline>            ::= "\n"
```

**Structural rules** (mirror §5.7):
- `<GblOpen>` and `<GblClose>` MUST each appear on their own line, with no
  leading or trailing characters.
- **At most ONE `***Global***` block per CoS** (a single global scope). More
  than one is INVALID. *(This is stricter than Literal, which permits several.)*
- **Position is free** within the CoS. The parser recognizes the section by its
  markers, not by where it sits. A `# GLOBAL` comment above the block is an
  optional human label, never a parser signal.
- Nesting is prohibited: a `***Global***` block MUST NOT contain another
  `***Global***` or a `***Literal***` marker.
- **Mandatory-container rule:** every global variable declaration MUST live
  inside this block. A `Var:` line outside `***Global***` is INVALID.

**Authoring modes:**
- **Formal mode** (`<FormalGlobalBody>`): the body is one or more `Var:` lines,
  validated directly by this grammar. Intended for super-users who know CLL.
- **Natural mode** (`<NaturalGlobalBody>`): the body is free natural-language
  text describing the intended values. It is **opaque to this grammar** — the
  executing model resolves it to canonical `Var:` lines before the modules run.
  This grammar only asserts that the container may hold such text; the
  resolution contract is defined in the Compiler specification, and the result
  is checked by V8 (all `$VarName` references MUST resolve).

**Mode consistency:** the `***Global***` and `***End Global***` markers do not
compress in Compact Mode (no shorthand), exactly like `Format:Literal`. `Var:`
line keys never compress. Natural-mode text is mode-agnostic.

**Capability floor (informative).** Natural mode depends on the executing
model's extraction quality. Below a capability threshold (empirically around
the Haiku→Sonnet boundary), extraction may fail silently; V8 is the safety net,
and formal mode is the fallback. Full guidance in the Compiler spec and doc 05.

Formal example:

```cll
***Global***
Var: Pure = "CRWD,PANW,FTNT,ZS"
Var: ARRThr = 20
***End Global***
```

Natural example (resolved to the two `Var:` lines above before execution):

```cll
***Global***
El portafolio puro son CRWD, PANW, FTNT y ZS.
El umbral minimo de ARR es 20%.
***End Global***
```

**Migration from v1.x.** Wrap the previous positional GLOBAL section in the
container:

```text
BEFORE (v1.x — now INVALID):        AFTER (v2.0):
# GLOBAL                            ***Global***
Var: Pure = "CRWD,PANW,FTNT,ZS"     Var: Pure = "CRWD,PANW,FTNT,ZS"
Var: ARRThr = 20                    Var: ARRThr = 20
                                    ***End Global***
```

The `# GLOBAL` comment may be kept above `***Global***` as a label, but it no
longer carries any parser meaning.

## 6. Per-Line Grammar

- **6.1** `Int:` first token MUST be an `A-` prefixed SCURToken.
- **6.2** `Ctx:` first token SHOULD be a `D-` prefixed SCURToken; MAX 4 tokens after the SCUR root.
- **6.3** `Cfg:` first token SHOULD be a `C-` prefixed SCURToken; additional tokens MUST be ParamToken or FlagToken (may include `Format:Literal`).
- **6.4** `Act:` first token SHOULD be an `X-` prefixed SCURToken; the **second token MUST be a registered AVR verb** (doc 07).
- **6.5** `Out-Lang` / `Out:` `<OutValue> ::= <FlagToken> | <StringToken>`; REQUIRED for Executable blocks, OPTIONAL for Declarative.
- **6.6** `Var:` per §5.6; MUST appear inside the `***Global***` container (§5.8); read-only.

## 7. Compact Mode Constraints

- UR length 2–4.
- Parameters SHOULD be shortened (`Tone`→`Tn`, `Length`→`Ln`, `Domain`→`Dom`).
- `Out-Lang` MUST be written as `Out`.
- Line keys NEVER compress; `$VarName`, `Format:Literal`, and the
  `***Global***`/`***Literal***` markers never compress.
- Natural language minimized; quoted when unavoidable (natural-mode
  `***Global***` text is the sanctioned exception, §5.8).

## 8. Prohibited Patterns

- Custom/unregistered line keys (`R:`, `Note:`, `Rule:`, `Rationale:`, …) — V13
- **`Var:` declarations outside the `***Global***` container** — V17 (New in v2.0)
- **More than one `***Global***` block in a CoS** — V17 (New in v2.0)
- Conjugated natural-language sentences (outside natural-mode `***Global***` and Literal bodies)
- Connectors (and/but/also) outside quoted strings (same exceptions)
- Multi-line values (except inside Literal and natural-mode Global bodies); free-form prose
- Missing SC prefix in Standard Mode
- Digits inside `<UR>`; numeric collision suffixes (`Rte2`)
- More than 4 tokens in a `Ctx` line
- Unregistered verbs in the `Act:` line
- Nested Literal or Global blocks; Literal markers without a governing `Format:Literal` block
- Blank lines inside a block

## 9. Minimal Valid Blocks

Standard (Executable):

```cll
CLL: 2.0
Int: A-Analys Kontext
Ctx: D-Data UserReq
Cfg: C-Konfig Tone:Neutral Len:Short
Act: X-Exec Build
Out-Lang: Natural
```

Standard (Declarative):

```cll
Int: A-Analys StepNames
Ctx: D-Data PascalCase
Cfg: C-Konfig Domain:PQ
Act: X-Exec Define
```

Compact (Executable):

```cll
CLL: 2.0
Int: A-Aly Kntx
Ctx: D-Dta Req
Cfg: C-Kfg Tn:Ntrl Ln:S
Act: X-Exe Bld
Out: Nat
```

With a Global block (formal mode):

```cll
***Global***
Var: Tickers = "CRWD,PANW,FTNT"
***End Global***
Int: A-Analys Growth
Ctx: D-Data Targets:$Tickers
Cfg: C-Konfig Domain:CyberSec
Act: X-Exec Evaluate
Out-Lang: Natural
```

---
*End*
