# CLL — Development Log
*Cognitive Layer Language | ZitoMorenoTech*

> **Note on language.** Test inputs and model outputs are preserved verbatim in their original language. Several tests evaluate whether the system keeps the output in the input's language, so translating them would destroy the evidence. All analysis, tables and commentary are in English.

---

## Entry 004 | Wednesday, 3 September 2026

**Test object:** CoS for option comparison
**Inputs used:** Laptops for remote work (ES) / Mobile plans (PT) / PM tools (EN)
**Models:** Claude Sonnet and Claude Opus (via API, no prior context)
**Versions tested:** human-language CoS v1 (Sonnet) / formal CLL CoS v1.1 (Sonnet and Opus)

---

### Human-language CoS v1

```
For the entire document:
- Write everything in the same language as the input text.
- Use only information present in the input. Do not add external knowledge.
- Do not write anything before the first line.
- Do not write anything after the last line.
- Do not group lines under titles or sections.
- Do not add lines not described here.

Criteria list:
First line must begin **Criteria:** and then nothing else on that line.
Following lines must list each criterion mentioned in the input, numbered from 1.
One criterion per line. Use the exact name the input gives each criterion.

Option blocks:
After the criteria list, one block per option. Each block begins with a line
that starts **[option name]:** and then nothing else on that line.
Inside each block, evaluate every criterion from the criteria list, in the same
order, one per line, numbered to match. Each criterion answered in one to two
sentences. If the input does not provide information for a criterion, write
exactly: "No information available". Do not skip a criterion. Do not add criteria.
No option receives more criteria or more depth than another.

Recommendation:
Last line must begin **Recommendation:** and then one to three sentences with a
single main reason. The recommendation comes only here, never inside the option
blocks. Do not address the reader in second person.
```

### Formal CLL CoS v1.1

```
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

### Test inputs

#### Input 1 — Laptops for remote work (Spanish)

> Estoy entre tres laptops para trabajo remoto. La MacBook Air M3 cuesta $1,099, tiene 16GB de RAM, batería de 18 horas y pesa 1.24 kg. La ThinkPad X1 Carbon cuesta $1,449, tiene 16GB de RAM, batería de 14 horas y pesa 1.12 kg. La Dell XPS 14 cuesta $1,299, tiene 16GB de RAM, batería de 12 horas y pesa 1.46 kg. Me importa la portabilidad, la duración de batería y el precio. No necesito GPU dedicada.

#### Input 2 — Mobile plans (Portuguese, missing data)

> Tô escolhendo entre dois planos de celular. O plano da Claro tem 50GB por R$89,90 e inclui roaming nacional. O da Vivo tem 80GB por R$99,90 mas não sei se tem roaming. Os dois têm ligações ilimitadas. Quero comparar por dados, preço e cobertura.

#### Input 3 — PM tools (English, with opinions)

> We're evaluating project management tools for our team of 12. Jira is $7.75/user/month, very powerful but honestly a nightmare to configure. Asana is $10.99/user/month, much cleaner UI, good for non-technical teams. Linear is $8/user/month, fast and developer-focused, but it's newer so fewer integrations. Notion is $8/user/month, super flexible but can become a mess without discipline. We care about ease of setup, price per user, and integration ecosystem.

---

### Outputs — human CoS v1 (Sonnet)

#### Output 1 — Laptops

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

#### Output 2 — Mobile plans

```
**Critérios:**
1. Dados
2. Preço
3. Cobertura
**Claro:**
1. 50GB de internet
2. R$89,90 mensais
3. Inclui roaming nacional
**Vivo:**
1. 80GB de internet
2. R$99,90 mensais
3. Sem informação disponível
**Recomendação:**
Vivo oferece 60% mais dados por R$10 a mais, representando melhor custo-benefício em volume de internet.
```

#### Output 3 — PM Tools

```
**Criteria:**
1. Ease of setup
2. Price per user
3. Integration ecosystem
**Jira:**
1. Very powerful but complex to set up.
2. $7.75/user/month.
3. Extensive integration ecosystem.
**Asana:**
1. Clean UI, easier setup experience.
2. $10.99/user/month.
3. Good integration options available.
**Linear:**
1. Fast and streamlined setup.
2. $8/user/month.
3. Limited integrations as newer platform.
**Notion:**
1. Flexible but requires discipline to maintain.
2. $8/user/month.
3. Growing integration ecosystem.
**Recommendation:**
Asana offers the best balance, with straightforward setup minimizing implementation time and strong integrations supporting team workflow, justifying the higher cost.
```

---

### Analysis — human CoS v1 (Sonnet)

#### A. Structural

| Rule | Laptops | Mobile | PM Tools |
|---|---|---|---|
| 1. Nothing before | ✓ | ✓ | ✓ |
| 2. Nothing after | ✓ | ✓ | ✓ |
| 3. No groupers | ✓ | ✓ | ✓ |
| 4. No extra fields | ✓ | ✓ | ✓ |
| 5. Labels present | ✓ | ✓ | ✓ |
| 6. Exact nomenclature | ✓ | ✓ | ✓ |
| 7. Correct order | ✓ | ✓ | ✓ |
| 8. Format `**Label:**` | ✓ | ✓ | ✓ |
| 9. Criteria numbered | ✓ | ✓ | ✓ |
| 10. Same criteria, same order | ✓ | ✓ | ✓ |

**Structural conformity: 30/30 — 100%**

#### B. Context

| Rule | Laptops | Mobile | PM Tools |
|---|---|---|---|
| 1. Correct language | ✓ | ✓ | ✓ |
| 2. Only information from the input | ✓ | ✓ | ✗ |
| 3. Criteria taken from the input | ✓ | ✓ | ✓ |
| 4. Same number of criteria per option | ✓ | ✓ | ✓ |
| 5. Same criteria order | ✓ | ✓ | ✓ |
| 6. MaxSent respected (1-2) | ✓ | ✗ | ✓ |
| 7. Literal fallback | n/a | ✗ | ✓ |
| 8. Equal depth | ✓ | ✗ | ✓ |
| 9. Opinions filtered | n/a | n/a | ✗ |
| 10. Recommendation only at the end | ✓ | ✓ | ✓ |
| 11. Recommendation ≤3 sentences | ✓ | ✓ | ✓ |
| 12. No second person | ✓ | ✓ | ✓ |

**Context conformity: 29/34 — 85%**

**Global, human CoS v1: 59/64 — 92%**

---

### Outputs — formal CLL CoS v1.1 (Sonnet)

#### Output 1 — Laptops

```
Criterios:
1. Portabilidad
2. Duración de batería
3. Precio
MacBook Air M3:
1. Pesa 1.24 kg.
2. Batería de 18 horas.
3. Cuesta $1,099.
ThinkPad X1 Carbon:
1. Pesa 1.12 kg.
2. Batería de 14 horas.
3. Cuesta $1,449.
Dell XPS 14:
1. Pesa 1.46 kg.
2. Batería de 12 horas.
3. Cuesta $1,299.
Recomendación: MacBook Air M3 tiene el precio más bajo ($1,099) y la batería más duradera (18 horas).
```

#### Output 2 — Mobile plans

```
**Critérios:**
1. Dados
2. Preço
3. Cobertura
**Claro:**
Dados: 50GB
Preço: R$89,90
Cobertura: Inclui roaming nacional
**Vivo:**
Dados: 80GB
Preço: R$99,90
Cobertura: Sem informação disponível
**Recomendação:** A Vivo oferece 30GB a mais por R$10 de diferença.
```

#### Output 3 — PM Tools

```
**Criteria:**
1. Ease of setup
2. Price per user
3. Integration ecosystem
**Jira:**
1. Nightmare to configure.
2. $7.75/user/month.
3. No information available.
**Asana:**
1. No information available.
2. $10.99/user/month.
3. No information available.
**Linear:**
1. No information available.
2. $8/user/month.
3. Fewer integrations.
**Notion:**
1. No information available.
2. $8/user/month.
3. No information available.
**Recommendation:** Jira has the lowest price per user at $7.75/user/month.
```

---

### Analysis — formal CLL CoS v1.1 (Sonnet)

#### A. Structural

| Rule | Laptops | Mobile | PM Tools |
|---|---|---|---|
| 1. Nothing before | ✓ | ✓ | ✓ |
| 2. Nothing after | ✓ | ✓ | ✓ |
| 3. No groupers | ✓ | ✓ | ✓ |
| 4. No extra fields | ✓ | ✓ | ✓ |
| 5. Labels present | ✓ | ✓ | ✓ |
| 6. Exact nomenclature | ✓ | ✓ | ✓ |
| 7. Correct order | ✓ | ✓ | ✓ |
| 8. Format `**Label:**` | ✗ | ✓ | ✓ |
| 9. Criteria numbered | ✓ | ✗ | ✓ |
| 10. Same criteria, same order | ✓ | ✗ | ✓ |

**Structural conformity: 27/30 — 90%**

#### B. Context

| Rule | Laptops | Mobile | PM Tools |
|---|---|---|---|
| 1. Correct language | ✓ | ✓ | ✓ |
| 2. Only information from the input | ✓ | ✓ | ✗ |
| 3. Criteria taken from the input | ✓ | ✓ | ✓ |
| 4. Same number of criteria per option | ✓ | ✓ | ✓ |
| 5. Same criteria order | ✓ | ✓ | ✓ |
| 6. MaxSent = 1 complete sentence | ✓ | ✗ | ✓ |
| 7. Literal fallback untranslated | n/a | ✗ | ✓ |
| 8. Equal depth | ✓ | ✗ | ✓ |
| 9. Opinions filtered | n/a | n/a | ✗ |
| 10. Recommendation only at the end | ✓ | ✓ | ✓ |
| 11. Recommendation ≤3 sentences | ✓ | ✓ | ✓ |
| 12. No second person | ✓ | ✓ | ✓ |

**Context conformity: 29/34 — 85%**

**Global, formal CLL Sonnet: 56/64 — 88%**

---

### Outputs — formal CLL CoS v1.1 (Opus)

#### Output 1 — Laptops

```
Criterios:
1. Portabilidad
2. Duración de batería
3. Precio
MacBook Air M3:
1. Portabilidad: Pesa 1.24 kg.
2. Duración de batería: 18 horas.
3. Precio: $1,099.
ThinkPad X1 Carbon:
1. Portabilidad: Pesa 1.12 kg.
2. Duración de batería: 14 horas.
3. Precio: $1,449.
Dell XPS 14:
1. Portabilidad: Pesa 1.46 kg.
2. Duración de batería: 12 horas.
3. Precio: $1,299.
Recomendación: La MacBook Air M3 ofrece la mayor duración de batería (18 horas) y el precio más bajo ($1,099) de las tres opciones, con un peso de 1.24 kg que solo es superado en ligereza por la ThinkPad X1 Carbon por 120 gramos. Considerando los tres criterios indicados en conjunto, es la opción que mejor los satisface simultáneamente.
```

#### Output 2 — Mobile plans

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

#### Output 3 — PM Tools

```
**Criteria:**
1. Ease of setup
2. Price per user
3. Integration ecosystem
**Jira**
1. Ease of setup: Highly powerful but complex to configure.
2. Price per user: $7.75/user/month.
3. Integration ecosystem: No information available.
**Asana**
1. Ease of setup: Clean UI, suitable for non-technical teams.
2. Price per user: $10.99/user/month.
3. Integration ecosystem: No information available.
**Linear**
1. Ease of setup: Fast and developer-focused.
2. Price per user: $8/user/month.
3. Integration ecosystem: Fewer integrations due to being a newer tool.
**Notion**
1. Ease of setup: Highly flexible but can become disorganized without discipline.
2. Price per user: $8/user/month.
3. Integration ecosystem: No information available.
**Recommendation:** Jira offers the lowest price per user at $7.75, while Linear and Notion match at $8 and provide a more straightforward experience. Linear is the only option with explicitly described integration limitations, which is relevant given that integration ecosystem is a stated priority.
```

---

### Analysis — formal CLL CoS v1.1 (Opus)

#### A. Structural

| Rule | Laptops | Mobile | PM Tools |
|---|---|---|---|
| 1. Nothing before | ✓ | ✓ | ✓ |
| 2. Nothing after | ✓ | ✓ | ✓ |
| 3. No groupers | ✓ | ✓ | ✓ |
| 4. No extra fields | ✓ | ✓ | ✓ |
| 5. Labels present | ✓ | ✓ | ✓ |
| 6. Exact nomenclature | ✓ | ✓ | ✗ |
| 7. Correct order | ✓ | ✓ | ✓ |
| 8. Format `**Label:**` | ✗ | ✓ | ✗ |
| 9. Criteria numbered | ✓ | ✓ | ✓ |
| 10. Same criteria, same order | ✓ | ✓ | ✓ |

**Structural conformity: 27/30 — 90%**

#### B. Context

| Rule | Laptops | Mobile | PM Tools |
|---|---|---|---|
| 1. Correct language | ✓ | ✓ | ✓ |
| 2. Only information from the input | ✓ | ✓ | ✗ |
| 3. Criteria taken from the input | ✓ | ✓ | ✓ |
| 4. Same number of criteria per option | ✓ | ✓ | ✓ |
| 5. Same criteria order | ✓ | ✓ | ✓ |
| 6. MaxSent = 1 complete sentence | ✓ | ✓ | ✓ |
| 7. Literal fallback untranslated | n/a | ✓ | ✓ |
| 8. Equal depth | ✓ | ✓ | ✓ |
| 9. Opinions filtered | n/a | n/a | ✗ |
| 10. Recommendation only at the end | ✓ | ✓ | ✓ |
| 11. Recommendation ≤3 sentences | ✗ | ✓ | ✗ |
| 12. No second person | ✓ | ✓ | ✓ |

**Context conformity: 30/34 — 88%**

**Global, formal CLL Opus: 57/64 — 89%**

---

### Direct comparison: three versions

| Version | Model | Structure | Context | Global |
|---|---|---|---|---|
| Human CoS v1 | Sonnet | 100% | 85% | 92% |
| Formal CLL v1.1 | Sonnet | 90% | 85% | 88% |
| Formal CLL v1.1 | Opus | 90% | 88% | 89% |

---

### Sonnet vs Opus differences (formal CLL v1.1)

**Structure: identical in percentage (90%), different in failures.** Both fail on bold formatting but on different inputs.

**Context: Opus wins by 3 points (88% vs 85%).** The specific differences:

| Dimension | Sonnet | Opus |
|---|---|---|
| Literal fallback | Translated to "Sem informação disponível" | Wrote "No information available" exactly |
| Complete sentences | Fragments in Input 2 ("50GB", "R$89,90") | Always complete sentences ("50GB inclusos no plano") |
| Depth | Uneven in Input 2 | Uniform across all three |
| Opinions | Oscillates: literal "Nightmare" or over-filters | Always softens ("complex to configure") |
| Recommendation | Concise, within the limit | Runs long, exceeds in 2 of 3 |

**Neither resolved opinion filtering correctly.** Sonnet either lets it through or strips valid data along with the opinion. Opus rephrases instead of removing. This is where the validator is most needed.

---

### Cumulative comparison (all tests)

| Test | Structure | Context | Global |
|---|---|---|---|
| Test 1 — Book (V5, ES) | 90% | 82% | 86% |
| Test 2 — Recipe (without GVS, ES) | 83% | 81% | 82% |
| Test 2 — Recipe (with GVS, ES) | 87% | 89% | 88% |
| Test 3 — Article (with GVS, ES) | 100% | 80% | 89% |
| Test 3 — Article (formal CLL, EN) | 96% | 100% | 98% |
| Test 4 — Comparison (human v1, Sonnet) | 100% | 85% | 92% |
| Test 4 — Comparison (formal CLL, Sonnet) | 90% | 85% | 88% |
| Test 4 — Comparison (formal CLL, Opus) | 90% | 88% | 89% |

---

### Key observations

**The human CoS v1 beat formal CLL in this test (92% vs 88-89%).** A counterintuitive but revealing result: for comparisons, well-written natural language was more effective than CLL formalization. The human CoS's perfect structure (100%) suggests this domain has fewer ambiguous gaps than the previous ones.

**Formal CLL shines in technical domains, not interpretive ones.** Test 3 (technical article) is where formal CLL reached 98%. Test 4 (comparison with opinions) drops back to 88-89%. The benefit of formalization is greater where there are strict quantitative constraints and smaller where the domain requires interpretive judgment.

**Sonnet vs Opus: 1 point of global difference, a difference of quality.** The numeric distance is marginal, but Opus produces complete sentences, respects the literal fallback, and maintains uniform depth. Sonnet meets the rule in number but not in spirit. Both fail at opinion filtering — that is the ceiling of prompting for this kind of constraint.

**The monotonic trend broke.** From 82% to 98% across tests 1-3, test 4 returns to 88-92%. This is not a regression of the system — it is a domain with a new challenge (opinions mixed with data). It confirms that with three tests it was premature to speak of a linear trend.

---

### Pending for the next sessions

- [ ] Run Test 5: translation with preserved structure
- [ ] Run the control group (without CoS) for the 4 domains
- [ ] Formalize GVS in spec v1.2
- [ ] Resolve the design tension: is $NoData translated with $OutLang or is it an invariable literal?
- [ ] Investigate why the human CoS beat formal CLL in comparisons
- [ ] Send screenshot to Jyri

---

*Next entry: Thursday, 4 September 2026*
