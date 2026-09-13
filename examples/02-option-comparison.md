# Example 2 — Option comparison

**Domain:** structured comparison of alternatives
**Result:** 92% with CLL vs 24% for the plain-prose control (from [log 004](../logs/CLL_Log_004.md) and [log 006](../logs/CLL_Log_006.md))
**Why this example:** the largest delta in the benchmark. This is the case CLL exists for — a task with no natural output shape, where the model improvises differently every time unless the structure is declared.

The task: compare a set of options against the criteria the user stated, giving every option the same criteria in the same order and the same depth, with the recommendation appearing exactly once, at the end.

---

## The CoS

```cll
# META
Ver: 1.1 Dom:OptionComparison Auth:ZitoMorenoTech Date:2026-09

# GLOBAL VARIABLES
Var: OutLang = "InputLang"
Var: SourceOnly = true
Var: MaxSent = 1
Var: NoData = "No information available"

# GLOBAL RULES
Int: A-Analys GlobalRules
Ctx: D-Data $OutLang $SourceOnly
Cfg: C-Konfig SameLang:TRUE NoExternal:TRUE NoInterpret:TRUE NoInfer:TRUE NoPreface:TRUE NoPostface:TRUE NoSections:TRUE OnlyDescribed:TRUE ColonAfterLabel:TRUE
Act: X-Exec Enforce

# MODULE 1: CRITERIA LIST
Int: A-Analys CriteriaList
Ctx: D-Data InputCriteria InputOptions
Cfg: C-Konfig Prefix:Criteria LinePos:1 Format:NumberedList ExtractFromInput:TRUE ExactNames:TRUE
Act: X-Exec Generate
Out-Lang: $OutLang

# MODULE 2: OPTION BLOCKS
Int: A-Analys OptionBlocks
Ctx: D-Data InputOptions InputCriteria
Cfg: C-Konfig Prefix:OptionName LinePos:After:CriteriaList SameCriteria:TRUE SameOrder:TRUE MaxSent:$MaxSent SentenceType:Complete Fallback:$NoData FallbackPreserve:TRUE EqualDepth:TRUE
Act: X-Exec Generate
Out-Lang: $OutLang

# OPINION FILTER
Int: A-Analys OpinionFilter
Ctx: D-Data InputOpinions OutputContent
Cfg: C-Konfig NoOpinion:TRUE NoRephrase:TRUE NoSoften:TRUE FactOnly:TRUE OpinionFallback:$NoData
Act: X-Exec Enforce

# FALLBACK TEMPLATE
Int: A-Analys FallbackLiteral
Ctx: D-Data $NoData
Cfg: C-Konfig Format:Literal
Act: X-Exec Preserve
***Literal***
No information available
***End Literal***

# MODULE 3: RECOMMENDATION
Int: A-Analys Recommendation
Ctx: D-Data OptionBlocks
Cfg: C-Konfig Prefix:Recommendation LinePos:Last MaxSent:3 SingleReason:TRUE DataOnly:TRUE
Act: X-Exec Generate
Out-Lang: $OutLang

# CONTENT RESTRICTIONS
Int: A-Analys ContentRestrictions
Ctx: D-Data OutputContent
Cfg: C-Konfig NoEvaluation:TRUE NoSecondPerson:TRUE NumbersFaithful:TRUE NoBias:TRUE NoInferCause:TRUE
Act: X-Exec Enforce
```

---

## Run A — Spanish input

**Input**

> Estoy entre tres laptops para trabajo remoto. La MacBook Air M3 cuesta $1,099, tiene 16GB de RAM, batería de 18 horas y pesa 1.24 kg. La ThinkPad X1 Carbon cuesta $1,449, tiene 16GB de RAM, batería de 14 horas y pesa 1.12 kg. La Dell XPS 14 cuesta $1,299, tiene 16GB de RAM, batería de 12 horas y pesa 1.46 kg. Me importa la portabilidad, la duración de batería y el precio. No necesito GPU dedicada.

**Output**

```
**Criterios:**
1. Portabilidad
2. Duración de batería
3. Precio
**MacBook Air M3:**
1. Pesa 1.24 kg, lo que la hace muy portátil.
2. Ofrece 18 horas de batería, la mayor duración entre las tres opciones.
3. Cuesta $1,099, siendo la opción más económica.
**ThinkPad X1 Carbon:**
1. Pesa 1.12 kg, siendo la laptop más ligera de las tres.
2. Proporciona 14 horas de batería.
3. Cuesta $1,449, siendo la opción más cara.
**Dell XPS 14:**
1. Pesa 1.46 kg, siendo la menos portátil por peso.
2. Ofrece 12 horas de batería, la menor duración.
3. Cuesta $1,299, posicionándose en precio medio.
**Recomendación:**
MacBook Air M3, ya que combina el precio más bajo con la mejor duración de batería y un peso competitivo.
```

---

## Run B — Portuguese input, one field missing

The user does not know whether one plan includes roaming. The fallback must appear rather than an inference.

**Input**

> Tô escolhendo entre dois planos de celular. O plano da Claro tem 50GB por R$89,90 e inclui roaming nacional. O da Vivo tem 80GB por R$99,90 mas não sei se tem roaming. Os dois têm ligações ilimitadas. Quero comparar por dados, preço e cobertura.

**Output**

```
**Critérios:**
1. Dados
2. Preço
3. Cobertura
**Claro:**
1. Dados: 50GB inclusos no plano.
2. Preço: R$89,90 por mês.
3. Cobertura: inclui roaming nacional.
**Vivo:**
1. Dados: 80GB inclusos no plano.
2. Preço: R$99,90 por mês.
3. Cobertura: No information available.
**Recomendação:** A escolha depende da prioridade: a Vivo oferece 30GB a mais por uma diferença de R$10,00, enquanto a Claro tem roaming nacional confirmado. Recomenda-se verificar a informação de cobertura da Vivo antes de decidir.
```

---

## The same input without CLL

This is the control run from [log 006](../logs/CLL_Log_006.md) — the same material, prompted in plain prose: *"Compara estas opciones y dime cuál recomiendas."*

The model scored **20% on structure, 29% on context**. Across the three control inputs it improvised Markdown tables, emojis in headings, and fields nobody asked for (price per GB, monthly total, "dev workflow fit"). It embedded the recommendation inside the analysis, addressed the reader in second person, reordered the options, and in one case asked for more information before answering at all.

Nothing there is *wrong* as an answer. It is simply a different answer every time — which is precisely the problem when the output feeds a pipeline, a document template, or a comparison that has to be auditable.

---

## What to notice

**This is where the +68 points come from.** The task has no natural shape: nothing about "compare these options" tells the model how many criteria to use, in what order, whether to tabulate, or where the recommendation belongs. Declaring the structure removes every one of those decisions.

**`EqualDepth:TRUE` is the rule that makes a comparison fair.** Without it the model writes more about whichever option it prefers, which quietly biases the reader before the recommendation is even reached.

**The fallback protects against inference.** Run B's missing roaming field could easily have been filled with a plausible guess. `Fallback:$NoData` combined with `NoInfer:TRUE` makes the gap visible instead.

**The honest failure: opinion filtering.** Neither model tested handled it well. Given a source containing "honestly a nightmare to configure", one model passed the judgment through nearly verbatim while another softened it to "complex to configure" — and softening is still not removing. This is the clearest case in the whole benchmark where prompting has a ceiling and a post-output validator is the only real answer.
