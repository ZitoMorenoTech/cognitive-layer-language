# CLL — Overview & Reading Guide v2.0
**Start here. Plain-language map of the whole corpus.**

> This document is the on-ramp, not the law. It names the main ideas simply
> and points to where each one is defined precisely. When this guide and a
> spec document disagree, the spec document wins.

**Version note (v1.2 → v2.0):** updated for the `***Global***` container. §4 and
the worked example in §5 now use the container (the old positional `# GLOBAL`
form is invalid under v2.0).

---

## 1. What CLL is (in one breath)

CLL is a way to write instructions for an AI as **labeled layers** instead of
free prose. Prose is ambiguous — the same paragraph produces a different
answer each time. CLL replaces the paragraph with a short, fixed structure so
the same task converges on the same shape of output, every run.

Think of it as the difference between:

- *"Analyze the user's text and make me a neutral, short book card in Spanish."* (prose — re-interpreted every time)
- A five-line block where each line has a fixed job. (structure — read the same way every time)

The payoff is **not** "fewer tokens." It is **convergence**: fewer back-and-forth
iterations to reach a standard, acceptable result (see doc 05 for why we
measure it that way and never claim a flat token percentage).

---

## 2. The mental model: one block = five layers

A **block** is the smallest unit. It always has the same five layers, in the
same order. Each answers one plain question:

| Layer | Plain question | Example |
|---|---|---|
| `Int:` | **What** is the goal? | `Int: A-Analys BookCard` |
| `Ctx:` | **With what** inputs? | `Ctx: D-Data UserText` |
| `Cfg:` | **How** (settings)? | `Cfg: C-Konfig Tone:Neutral` |
| `Act:` | **Do** what action? | `Act: X-Exec Build` |
| `Out-Lang:` | **Deliver** in what form? | `Out-Lang: Natural` |

That is 90% of CLL. Everything else is vocabulary and packaging around this.

---

## 3. The vocabulary (four small ideas)

- **UR — roots.** The words. `Analys`, `Kontext`, `Konfig`, `Data`. One root = one concept.
- **SC — category prefixes.** The letter before the root that says what *kind* of thing it is: `A-` analysis, `D-` data, `C-` config, `X-` execution (plus `O- M- R- S- V-`). So `A-Analys` = "an analysis-type root."
- **CM — compact forms.** Short versions of the roots for runtime: `Analys→Aly`, `Konfig→Kfg`. Same meaning, fewer letters.
- **AVR — the verbs.** A fixed list of allowed actions for the `Act:` line: `Build`, `Generate`, `Evaluate`, `Define`, `Enforce`, … You can't invent verbs; you pick from the registry (doc 07).

The verb also decides the **block type**:
- **Declarative** (`Define`, `Enforce`, `Preserve`…) = sets a rule, produces no output.
- **Executable** (`Build`, `Generate`, `Evaluate`…) = produces output.

---

## 4. The packaging: a CoS

You rarely ship one block. You ship a **CoS** — a small document that groups
blocks. It has three parts, like a recipe card:

1. **META** — the label: version, domain, author, date. (`# META`)
2. **GLOBAL** — the pantry: shared values that apply to everything, wrapped in
   the `***Global***` container.
3. **MODULES** — the actual steps, one or more blocks.

Two helpers live here:

- **Variables (GVS)** — declare a value once inside `***Global***`, reuse it
  everywhere. You can write them formally (`Var: OutLang = "es"`) **or** just
  describe them in plain words inside the container and let the model turn them
  into `Var:` lines. Then reference with `$OutLang`. (doc 09)
- **Literal Preservation** — when you need content kept **exactly** (a template,
  some code), wrap it in `***Literal***` … `***End Literal***`. Inside those
  markers, the rules switch off and the text passes through verbatim. (doc 06)

Note the two containers are **siblings**: `***Global***` for shared values,
`***Literal***` for verbatim content. Neither goes inside the other.

---

## 5. One complete example, annotated

```
# META
Ver: 2.0 Dom:Doc Auth:Zito Date:2026-09        ← the label

# GLOBAL
***Global***
Var: OutLang = "es"                            ← a value reused everywhere
***End Global***

Int: A-Analys SourceScope                      ← a GLOBAL RULE (declarative):
Ctx: D-Data InputOnly                            "only use the input,
Cfg: C-Konfig Domain:Doc AddKnowledge:FALSE       don't add outside knowledge"
Act: X-Exec Enforce                            ← Enforce = rule, no output

# MODULE 1 — Book card
Int: A-Analys BookCard                         ← the actual task
Ctx: D-Data UserText Lang:$OutLang             ← uses the global value
Cfg: C-Konfig Domain:Doc Fields:7
Act: X-Exec Build                              ← Build = produces output
Out-Lang: Natural
```

Read top to bottom: *this is a v2.0 Doc-domain CoS; output language is Spanish
(declared once in the `***Global***` container); a standing rule says use only
the input; then build a 7-field book card from the user's text, in the global
language.* No prose, no ambiguity, same result each run.

The same CoS in **natural mode** — the values described in plain words, resolved
to `Var:` before the modules run:

```
# GLOBAL
***Global***
El idioma de salida siempre es español.
***End Global***
```

---

## 6. Reading order (the through-line)

You don't have to read all 11 in order. Follow the thread:

| # | Document | Read it when you want to… | Depends on |
|---|---|---|---|
| 00 | **This guide** | get the whole picture fast | — |
| 01 | Core Architecture | understand the concepts and the five layers | 00 |
| 02 | SPEC | know the exact rules and constraints | 01 |
| 03 | Grammar | know the precise syntax (BNF) | 02 |
| 01_2 | Canonical Dictionary | look up a root and its CM form | 01 |
| 01_1 | UR→CM Algorithm | understand how roots compress | 01_2 |
| 07 | Action Verb Registry | see which verbs are legal in `Act:` | 01 |
| 08 | CoS Container & Inheritance | package blocks into a CoS / reuse a base | 02 |
| 09 | Global Variable System | declare and reuse values (`***Global***`) | 08 |
| 06 | Literal Preservation | keep content verbatim (templates, code) | 03 |
| 04 | Style Guide | write clean, consistent CLL | all above |
| 05 | Model Compatibility Note | understand claims, benchmarking, corpus↔kernel | — |

**Shortest path to writing CLL:** 00 → 01 → 07 → 08. Add 09 and 06 when you need them; reach for 02/03 when you need the letter of the law.

---

## 7. Two honesty notes (so you don't get surprised)

- **Claims.** CLL's value is *convergence efficiency* (fewer iterations to a
  standard output), measured per model and per provider. Any flat "40–70%
  fewer tokens" line is not the spec — it's runtime drift (doc 05).
- **Corpus vs kernel.** These documents (the *corpus*) are the source of
  truth. The *kernel* that runs on the platform is a separate synthesized
  build. They are decoupled on purpose so documentation can improve without
  cascading into the running validator/compiler. Where they differ, a
  **Kernel Conformance Gap** box records it (docs 01_1, 01_2, 02, 07, 09).
  The corpus is at **v2.0**; the runtime kernel is pinned at v1.2.1-runtime
  until the per-provider regression suite is re-run.

---

## 8. Mini-glossary (dummy definitions)

- **CLL** — the language: labeled layers instead of prose.
- **CoS** — a document made of CLL blocks (META + GLOBAL + modules).
- **Block** — one 5-layer unit.
- **UR / CM** — a root / its short form.
- **SC** — the category letter before a root (`A-`, `D-`, `C-`, …).
- **AVR** — the fixed list of allowed `Act:` verbs.
- **Dcl / Exc** — a block that sets a rule / a block that makes output.
- **GVS** — declare-once values, in the `***Global***` container (`Var:` / `$Name`).
- **LP** — verbatim capsules (`***Literal***`).
- **Kernel** — the runtime build of CLL loaded on the platform.

---
*End of CLL Overview & Reading Guide v2.0*
