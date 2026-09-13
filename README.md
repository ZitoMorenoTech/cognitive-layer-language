# Cognitive Layer Language (CLL)

**CLL makes the output predictable where the task is ambiguous. Where the model already knows the shape, CLL stays out of the way.**

CLL is a formal language for writing instructions to an LLM as **labeled layers** instead of free prose. Prose is re-interpreted on every run; a labeled structure is read the same way every time. The result is output whose *shape* is stable across runs and inputs — and mechanically verifiable afterwards.

---

## The finding that defines the scope

CLL was benchmarked against a control group: the same inputs run with a plain natural-language prompt — what a user unfamiliar with CLL would type. The result is not a flat number. It depends entirely on the task:

| Domain | Control (plain prose) | Best CLL | Delta |
|---|---|---|---|
| Translation | 96% | 92% | **−4%** |
| Option comparison | 24% | 92% | **+68%** |

**The value of CLL is a function of the task's structural ambiguity, not its difficulty.**

In translation the model already knows the output mirrors the input — the scaffolding is noise, and CLL slightly hurts. In option comparison the model does not know whether to use a table, prose or bullets, how many criteria, in what order, or where to put the recommendation, so it improvises differently every time (24%). There CLL is worth +68 points.

This answers the obvious objection in advance. *Isn't this just a good prompt?* A good prompt **describes** the output. CLL **declares** the structure before the model decides, and verifies it afterwards. Where the task has a natural shape that difference is worth nothing; where it doesn't, it's worth 68 points.

---

## How it works

The unit is a **block**: five layers, always the same, always in order.

| Layer | Question it answers |
|---|---|
| `Int:` | **What** is the goal? |
| `Ctx:` | **With what** inputs? |
| `Cfg:` | **How** — which settings? |
| `Act:` | **Do** what action? |
| `Out-Lang:` | **Deliver** in what form? |

```cll
CLL: 1.2
Int: A-Analys Kontext
Ctx: D-Data UserReq
Cfg: C-Konfig Tone:Neutral Len:Short
Act: X-Exec Build
Out-Lang: Natural
```

The verb in `Act:` comes from a closed registry and determines the block type: **Declarative** verbs (`Define`, `Enforce`, `Preserve`…) set a rule and produce no output; **Executable** verbs (`Build`, `Generate`, `Compare`…) produce output. Verbs cannot be invented — an unregistered verb is invalid.

Blocks are packaged into a **CoS** (Cognitive Operating System) — META for the label,
