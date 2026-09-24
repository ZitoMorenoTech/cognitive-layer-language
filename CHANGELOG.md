# Changelog

All notable changes to the CLL corpus are recorded here. This file tracks the
**specification** (the `/spec` corpus). The runtime (compiler and validator)
lives in a separate private repository and is versioned independently; where the
two differ, the spec documents carry a *Kernel Conformance Gap* box.

The format is loosely based on [Keep a Changelog](https://keepachangelog.com/).
Versions refer to the corpus as a whole.

---

## [2.0] — 2026-09

Major release. Introduces one not-backward-compatible change: the GLOBAL section
is now an explicit container. v1.x CoS require a small migration (see below).

### Changed (breaking)
- **The GLOBAL section is now the explicit `***Global***` … `***End Global***`
  container** (Grammar §5.8), consistent with the existing `***Literal***`
  container. It may appear anywhere after META; at most one per CoS.
- **Declaring a `Var:` outside the container is now invalid.** Validator **V17**
  is re-purposed from a positional Warning ("GLOBAL after META") to a
  container-integrity **Critical** check, and promoted into the critical set
  (now 14 critical / 4 extended).

### Added
- **Natural authoring mode** for the `***Global***` container: instead of writing
  `Var:` lines, an author may describe the values in plain natural language, and
  the executing model resolves them into canonical `Var:` lines before the
  modules run. Formal mode (explicit `Var:` lines) remains available and is
  preferred for reproducibility-critical CoS. This is the on-ramp for authors who
  do not know CLL syntax.
- Sibling-container rule: `***Global***` and `***Literal***` never nest inside
  one another.

### Migration from v1.x
Wrap the previous positional GLOBAL section in the container:

```text
BEFORE (v1.x — now invalid)     AFTER (v2.0)
# GLOBAL                        # GLOBAL
Var: Pure = "..."               ***Global***
Var: ARRThr = 20                Var: Pure = "..."
                                Var: ARRThr = 20
                                ***End Global***
```

The `# GLOBAL` comment may stay as a human label; it no longer carries any parser
meaning.

### Notes
- Documents with no normative change were reissued with a v2.0 header for corpus
  consistency (01_1 Algorithm, 01_2 Dictionary, 07 AVR); 05 Model Compatibility
  Note added natural-mode resolution to its benchmarking practice and gap list.
- No new efficiency figures are claimed. The v1.2 benchmark (see `/logs`) was
  measured on the v1.2 design and remains representative, since the GVS mechanism
  is unchanged — only its delimitation. Natural-mode resolution is **not** yet
  benchmarked.
- **Runtime status:** the compiler/validator remain pinned at `v1.2.1-runtime`
  and adopt the `***Global***` container only when the per-provider regression
  suite is re-run. The corpus is not blocked on that migration.

---

## [1.2] — 2026-09

Initial public release of the corpus.

### Added
- Full 12-document corpus (00–09) as the single source of truth.
- Benchmark evidence: development logs 001–007 and the Executive Summary in
  `/logs`.
- Worked CoS examples in `/examples`.
- `V-` (Validation) semantic prefix → nine semantic categories.
- Block Types (Declarative / Executable) and the Action Verb Registry as the
  normative source of `Act:` verbs.
- Global Variable System (`Var:` / `$Name`) and Literal Preservation
  (`***Literal***`).
- Closed line-key set with V13 (no custom line keys).

---

*Older internal iterations (v1.0–v1.1) predate the public release and are not
tracked here.*
