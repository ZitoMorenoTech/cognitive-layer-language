# CLL — Action Verb Registry (AVR) v1.2
**Cognitive Layer Language — Registered Verbs for the Act Layer**

> Normative values: this corpus (single source of truth); `cll_norms.json` planned.
> New document (v1.2): formalizes a registry that previously existed only in the runtime kernel.

## 1. Purpose

The Action Verb Registry (AVR) is the closed set of verbs allowed as the
**second token of an `Act:` line**. The verb both names the operation and
**determines the Block Type** (Declarative vs Executable), which in turn
governs whether an `Out-Lang`/`Out` line is required.

## 2. Entry Structure

Format: `Verb | CM | Definition | BlockType | State`

- **Verb:** Standard-mode form used in `Act:` lines (Standard Mode).
- **CM:** Compact-mode form used in `Act:` lines (Compact Mode). Registered; MUST satisfy the Subsequence Conformance Rule (Algorithm v1.2, §6–§7).
- **BlockType:** `Dcl` (Declarative) or `Exc` (Executable).
- **State:** `Proposed → Approved → Deprecated → Removed`.

## 3. Registry (Core)

### 3.1 Declarative Verbs (no output; `Out-Lang`/`Out` OPTIONAL)

| Verb | CM | Definition | State |
|---|---|---|---|
| Define | Def | Declare a rule or structure; no output produced. | Approved:v1.2 |
| Enforce | Enf | Apply a rule with strict validation; reject if non-compliant. | Approved:v1.2 |
| Preserve | Prs | Maintain existing state; prevent modification (governs Literal blocks). | Approved:v1.2 |
| Apply | Apl | Apply a rule best-effort; no strict rejection. | Approved:v1.2 |
| Stabilize | Stb | Re-anchor drifted context; reassert rules. | Approved:v1.2 |
| Finalize | Fnl | Close a template or process. | Approved:v1.2 |

### 3.2 Executable Verbs (produce output; `Out-Lang`/`Out` REQUIRED)

| Verb | CM | Definition | State |
|---|---|---|---|
| Build | Bld | Construct output from inputs. | Approved:v1.2 |
| Generate | Gen | Produce new content or artifact. | Approved:v1.2 |
| Summarize | Smr | Produce condensed output. | Approved:v1.2 |
| Evaluate | Eval | Assess and produce a judgment. | Approved:v1.2 |
| Compare | Cmp | Produce comparative output. | Approved:v1.2 |
| Describe | Dsc | Produce descriptive output without modifying. | Approved:v1.2 |
| Outline | Otl | Produce a structural overview. | Approved:v1.2 |
| Validate | Vld | Verify compliance; produce pass/fail. | Approved:v1.2 |

## 4. Block Type Determination

- If the `Act:` verb is Declarative → the block is Declarative → `Out-Lang`/`Out` is OPTIONAL.
- If the `Act:` verb is Executable → the block is Executable → `Out-Lang`/`Out` is REQUIRED (validator **V6**).
- If no verb marker is present → assume Declarative.

## 5. Validation

- **V5** (Critical): the second token of every `Act:` line MUST be a registered AVR verb (Standard or CM form). Unregistered verbs are INVALID.
- Verbs are matched by position (see §7). A verb CM that coincides with a UR CM (e.g. `Smr`, `Vld`) is resolved positionally.

## 6. Namespace Note (UR ↔ AVR)

Some CM forms are shared with dictionary URs: `Smr` (verb `Summarize` / UR
`Summar`), `Vld` (verb `Validate` / UR `Validate`). This is intended reuse,
not a collision. Disambiguation is positional: the second `Act:` token is an
AVR verb; the same string elsewhere is a UR (Algorithm v1.2, §8).

## 7. Extension Policy

A new domain verb MUST:
- Be classified as `Dcl` or `Exc`.
- Register a CM form that passes the Subsequence Conformance Rule (Algorithm §6).
- Not duplicate an existing AVR verb's CM (UR reuse per §6 is permitted).
- Enter as `Proposed` before approval.
- Be added to the AVR before use — a verb that is not registered fails V5.

## 8. Conformance Check

All 14 core verbs satisfy the Subsequence Conformance Rule; the worked
check is in UR→CM Algorithm v1.2, §7.

> **Kernel Conformance Gap (informative).** The runtime kernel embeds this
> registry with the same 14 verbs. When the kernel migrates to the v1.2
> corpus lengths/dictionary, re-verify that `cll_validator.py`'s hard-coded
> AVR list matches this table (it drives V5).

---
*End of CLL Action Verb Registry v1.2*
