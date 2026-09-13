# CLL-Grammar v1.2
**Formal Grammar for Cognitive Layer Language**

> Normative values: this corpus (single source of truth); `cll_norms.json` planned.

**Changes vs v1.1:**
- `<LineKey>` set extended with `Var` (§4) → seven keys, closed set.
- `<SC>` extended with `V-` (§5.1) → nine categories.
- `<VarRefToken>` and `<VarLine>` added (§5.5, §5.6).
- `<TypedValue>` added to `<ParamValue>` (§5.2).
- `<LiteralBlock>` production added (§5.7).
- Per-line rule for `Act:` now requires the second token to be an AVR verb (§6.4).
- Custom/unregistered line keys explicitly prohibited (§4, §8, V13).

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

### 5.5 Variable Reference Token (New in v1.2)

```bnf
<VarRefToken> ::= "$" <VarName>
<VarName>     ::= <Alpha> <AlphaNum>*
```

Rules: MUST reference a declared `Var` line; resolved before execution;
not nestable (`$($x)` invalid). Examples: `$Pure`, `$ARRThr`.

### 5.6 Variable Declaration Line (New in v1.2)

```bnf
<VarLine>  ::= "Var:" <SP> <VarName> <SP> "=" <SP> <VarValue>
<VarValue> ::= <StringToken> | <Number> | <FlagToken>
```

Examples: `Var: Pure = "CRWD,PANW,FTNT,ZS"` | `Var: ARRThr = 20`.
`VarName` is PascalCase, 2–12 characters. Full semantics in doc 09.

### 5.7 Literal Block (New in v1.2)

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

## 6. Per-Line Grammar

- **6.1** `Int:` first token MUST be an `A-` prefixed SCURToken.
- **6.2** `Ctx:` first token SHOULD be a `D-` prefixed SCURToken; MAX 4 tokens after the SCUR root.
- **6.3** `Cfg:` first token SHOULD be a `C-` prefixed SCURToken; additional tokens MUST be ParamToken or FlagToken (may include `Format:Literal`).
- **6.4** `Act:` first token SHOULD be an `X-` prefixed SCURToken; the **second token MUST be a registered AVR verb** (doc 07).
- **6.5** `Out-Lang` / `Out:` `<OutValue> ::= <FlagToken> | <StringToken>`; REQUIRED for Executable blocks, OPTIONAL for Declarative.
- **6.6** `Var:` per §5.6; GLOBAL section only; read-only.

## 7. Compact Mode Constraints

- UR length 2–4.
- Parameters SHOULD be shortened (`Tone`→`Tn`, `Length`→`Ln`, `Domain`→`Dom`).
- `Out-Lang` MUST be written as `Out`.
- Line keys NEVER compress; `$VarName` and `Format:Literal` never compress.
- Natural language minimized; quoted when unavoidable.

## 8. Prohibited Patterns

- Custom/unregistered line keys (`R:`, `Note:`, `Rule:`, `Rationale:`, …) — V13
- Conjugated natural-language sentences
- Connectors (and/but/also) outside quoted strings
- Multi-line values (except inside Literal blocks); free-form prose
- Missing SC prefix in Standard Mode
- Digits inside `<UR>`; numeric collision suffixes (`Rte2`)
- More than 4 tokens in a `Ctx` line
- Unregistered verbs in the `Act:` line
- Nested Literal blocks; Literal markers without a governing `Format:Literal` block
- Blank lines inside the block

## 9. Minimal Valid Blocks

Standard (Executable):

```cll
CLL: 1.2
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
CLL: 1.2
Int: A-Aly Kntx
Ctx: D-Dta Req
Cfg: C-Kfg Tn:Ntrl Ln:S
Act: X-Exe Bld
Out: Nat
```

---
*End*
