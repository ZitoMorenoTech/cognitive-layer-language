# CLL UR→CM Compression Algorithm — Formal Specification v1.2

| | |
|---|---|
| **Status** | Stable |
| **Scope** | Morphological Compression Rules for Universal Roots (UR) |
| **Normative values** | This corpus (see note below). A machine-readable `cll_norms.json` extract is planned as the build-time source for tooling (Roadmap, Track A). Until it is built, the values stated in this corpus are normative. |

**Changes vs v1.1:**
- Version aligned to the v1.2 corpus. **No morphological rule changed:** the v1.1 algorithm was already ahead of the runtime kernel (it removed the structural-consonant whitelist, unified UR length to 3–13, prohibited digits, added duplicate-collapse and the Subsequence Conformance Rule). That work is retained in substance.
- §7 added: **AVR verb CM forms** (Action Verb Registry, doc 07) are registered forms and MUST satisfy the Subsequence Conformance Rule (§6). A worked check for the 14 core verbs is included.
- §8 added: **UR ↔ AVR namespace note** — some CM forms are shared between a dictionary UR and an AVR verb (`Smr` = UR `Summar` and verb `Summarize`; `Vld` = UR `Validate` and verb `Validate`). Disambiguation is **positional** (the 2nd token of an `Act:` line is an AVR verb; the same string elsewhere is a UR).
- **Normative-values note reframed:** the corpus is the source of truth for the specification; `cll_norms.json` is a downstream machine-readable extract that tooling will consume once built. The runtime kernel is NOT edited in lockstep with this corpus (see the Kernel Conformance Gap in the Dictionary and SPEC).

> **Kernel Conformance Gap (informative).** The synthesized runtime kernel (`CLL-Kernel_v1_2_1`, Micro/Standard) still ships the pre-v1.1 compression rules: a structural-consonant whitelist (two mutually inconsistent lists), UR length `3–6`/`4–10`, and no duplicate-collapse or subsequence rule. These are runtime regressions, not spec changes. They are migrated on the kernel side only when the per-provider regression suite is re-run (Model Compatibility Note, doc 05).

## 1. Purpose

The UR→CM Compression Algorithm defines the deterministic transformation
from Standard Universal Roots (UR) into their Compact Mode (CM) forms,
ensuring morphological consistency, semantic preservation, collision
avoidance and deterministic behavior across all CLL implementations.

## 2. Input and Output

### 2.1 Input: StandardUR
- Alphabetic string, length 3–13
- Derived from EN/DE/NL/SV/ID
- Semantically stable

### 2.2 Output: CM
- Length 2–4
- Deterministically derived
- Unique within the dictionary
- An ordered subsequence of the normalized UR (see §6)

## 3. Morphological Principles

### 3.1 Vowel Reduction
Non-essential vowels are removed. A vowel is preserved only when:

a) it is the initial character of the UR
b) it is required to resolve a collision (per §5 precedence)
c) it is required for minimal semantic recognizability
   *(preserved vowels under this clause MUST be flagged `(V)` in the dictionary)*

### 3.2 Consonant Retention
ALL consonants are retained in original order.
Consecutive duplicate consonants collapse into one
(`Summar` → `smmr` → `smr`).
Y is a consonant by default; it is treated as a vowel only when it is
the sole syllable nucleus of its syllable.

### 3.3 Length Constraint
CM length: 2–4 characters. If the result exceeds 4, pruning applies (§4).

### 3.4 Collision Avoidance
CM forms are globally unique. Collisions resolve via the strict
precedence in §5 — never via digits or arbitrary suffixes.

## 4. Formal Algorithm: `Compress(UR) → CM`

**Step 1 — Normalize**
Lowercase; remove accents/diacritics; remove non-alphabetic characters.

**Step 2 — Remove non-essential vowels**
Remove all vowels (a, e, i, o, u) except those covered by §3.1.

**Step 3 — Consonant skeleton**
Retain all remaining characters in original order.
Collapse consecutive duplicate consonants.

**Step 4 — Enforce length**
If length > 4, prune with this priority:
1. Always preserve the first character of the UR.
2. Preserve consonants in order of appearance.
3. Preserve the final consonant if semantically distinctive.

Remove lowest-priority characters until length ≤ 4.

**Step 4b — Minimality Preference (optional, registered)**
A CM SHORTER than the strict Step 4 output MAY be registered in the
dictionary when it (i) remains an ordered subsequence of the normalized
UR, (ii) contains the UR's first character, (iii) is unique, and
(iv) is flagged `(R)` as a registered reduction.

Examples: `Konfig` → strict `Knfg` → registered `Kfg`;
`Validate` → strict `Vldt` → registered `Vld`;
`Variable` → strict `Vrbl` → registered `Vr`

**Step 5 — Resolve collisions (STRICT PRECEDENCE)**
If the CM duplicates an existing CM, apply in order until resolved:
1. Reintroduce the first elided vowel of the UR.
2. Append the next distinguishing consonant from the UR (in order).
3. **PROHIBITED:** digits, arbitrary suffixes, characters not present in the UR.

Same input + same dictionary state ⇒ same output, always.

**Step 6 — Validate**
The CM must satisfy the Subsequence Conformance Rule (§6) and be
unique, stable across versions, and recognizable relative to its UR.

**Step 7 — Return CM.**

## 5. Examples

```text
Konfig       -> Knfg  (registered reduction: Kfg)
Analys       -> Anls  (registered reduction: Aly, legacy canonical)
Summar       -> Smr   (duplicate collapse)
Serialize    -> Srlz  (registered reduction: Srz)
Normalize    -> Nrm
Tokenize     -> Tknz
Notify       -> Ntfy  (final y retained as consonant)
Search       -> Srch  (c, h now legal)
Validate     -> Vldt  (registered reduction: Vld)
Variable     -> Vrbl  (registered reduction: Vr)
ContextHash  -> CtxH
Merge        -> Mrg

Collision example:
  Route -> Rt -> collision/min-length -> reintroduce vowel -> Rte
```

## 6. Subsequence Conformance Rule (Normative)

Every CM in the canonical dictionary MUST:
1. Be an ordered subsequence of its normalized UR.
2. Contain the first character of the UR.
3. Have length 2–4.
4. Contain no digits.
5. Be unique within the dictionary.

This rule is machine-checkable and is the basis of the dictionary
conformance test in `test_suite.py`.

## 7. AVR Verb CM Conformance (New in v1.2)

The Action Verb Registry (doc 07) supplies the verbs allowed in the
`Act:` line. Each AVR verb has a registered CM form. AVR CM forms are
**registered**, not derived inline, but every one of them MUST satisfy
the Subsequence Conformance Rule (§6) so that the same machine check
covers both registries.

Worked check for the 14 core AVR verbs (verb → CM → ordered subsequence?):

```text
Declarative
  Define    -> Def   (d,e,f  ⊑ define)      OK
  Enforce   -> Enf   (e,n,f  ⊑ enforce)     OK
  Preserve  -> Prs   (p,r,s  ⊑ preserve)    OK
  Apply     -> Apl   (a,p,l  ⊑ apply*)      OK  (*duplicate p collapsed)
  Stabilize -> Stb   (s,t,b  ⊑ stabilize)   OK
  Finalize  -> Fnl   (f,n,l  ⊑ finalize)    OK
Executable
  Build     -> Bld   (b,l,d  ⊑ build)       OK
  Generate  -> Gen   (g,e,n  ⊑ generate)    OK
  Summarize -> Smr   (s,m,r  ⊑ summarize*)  OK  (*duplicate m collapsed)
  Evaluate  -> Eval  (e,v,a,l ⊑ evaluate)   OK
  Compare   -> Cmp   (c,m,p  ⊑ compare)     OK
  Describe  -> Dsc   (d,s,c  ⊑ describe)    OK
  Outline   -> Otl   (o,t,l  ⊑ outline)     OK
  Validate  -> Vld   (v,l,d  ⊑ validate)    OK
```

All 14 conform. New AVR verbs added under doc 07's extension policy MUST
pass this same check before approval.

## 8. UR ↔ AVR Namespace Note (New in v1.2)

Two registries can produce the same CM string:

- `Smr` is the CM of the UR `Summar` (dictionary) **and** of the AVR verb `Summarize` (doc 07).
- `Vld` is the CM of the UR `Validate` (dictionary) **and** of the AVR verb `Validate` (doc 07).

This is not a collision to resolve; it is intended reuse across two
positional roles. Disambiguation is **positional**:

- The **second token of an `Act:` line** is interpreted as an **AVR verb**.
- The same string in any other position (`Int`/`Ctx`/`Cfg` roots, or the SCUR root of the `Act:` line's first token) is interpreted as a **UR**.

Validators MUST apply this positional rule and MUST NOT flag the shared
CM as a duplicate.

## 9. Extension Policy

Any new UR added to the CLL must:
- Pass `Compress(UR)` or register a valid Minimality reduction
- Produce a unique CM (across the UR dictionary; AVR reuse per §8 is permitted)
- Fit within a semantic domain (no semantic overlap)
- Enter the dictionary as Proposed before approval

---
*End of file.*
