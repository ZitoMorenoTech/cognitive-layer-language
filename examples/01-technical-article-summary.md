# Example 1 — Technical article summary

**Domain:** academic / technical summarization
**Result:** structure 96% / context 100% (from [log 003](../logs/CLL_Log_003.md))
**Why this example:** the best result in the benchmark, and the clearest demonstration of the Global Variable System and the literal fallback working together across three input languages.

The task: extract a five-field summary from an unstructured article abstract, in the abstract's own language, preserving every number exactly and never evaluating the work's quality.

---

## The CoS

```cll
# META
Ver: 1.0 Dom:AcademicSummary Auth:ZitoMorenoTech Date:2026-09

# GLOBAL VARIABLES
Var: OutLang = "InputLang"
Var: NoData = "Not available"
Var: SourceOnly = true
Var: MaxSent = 3
Var: NoDeclared = "Not declared by the authors"

# GLOBAL RULES
Int: A-Analys GlobalRules
Ctx: D-Data $OutLang $SourceOnly
Cfg: C-Konfig SameLang:TRUE NoExternal:TRUE NoInterpret:TRUE NoPreface:TRUE NoPostface:TRUE NoSections:TRUE OnlyDescribed:TRUE
Act: X-Exec Enforce

# MODULE 1: CONTEXT
Int: A-Analys ContextSection
Ctx: D-Data InputFramework InputAntecedent
Cfg: C-Konfig Prefix:Context MaxSent:$MaxSent LinePos:1
Act: X-Exec Generate
Out-Lang: $OutLang

# MODULE 2: PROBLEM
Int: A-Analys ProblemSection
Ctx: D-Data InputProblem InputQuestion
Cfg: C-Konfig Prefix:Problem MaxSent:$MaxSent LinePos:2
Act: X-Exec Generate
Out-Lang: $OutLang

# MODULE 3: METHOD
Int: A-Analys MethodSection
Ctx: D-Data InputApproach
Cfg: C-Konfig Prefix:Method MaxSent:$MaxSent LinePos:3
Act: X-Exec Generate
Out-Lang: $OutLang

# MODULE 4: RESULT
Int: A-Analys ResultSection
Ctx: D-Data InputFinding InputConclusion
Cfg: C-Konfig Prefix:Result MaxSent:$MaxSent LinePos:4 PreserveNumbers:TRUE PreserveUnits:TRUE
Act: X-Exec Generate
Out-Lang: $OutLang

# MODULE 5: LIMITATIONS
Int: A-Analys LimitationSection
Ctx: D-Data InputLimitation DeclaredStatus
Cfg: C-Konfig Prefix:Limitations MaxSent:$MaxSent LinePos:5 Fallback:$NoDeclared
Act: X-Exec Compare
Out-Lang: $OutLang

# FALLBACK TEMPLATE
Int: A-Analys LimitationFallback
Ctx: D-Data $NoDeclared
Cfg: C-Konfig Format:Literal
Act: X-Exec Preserve
***Literal***
Not declared by the authors
***End Literal***

# CONTENT RESTRICTIONS
Int: A-Analys ContentRestrictions
Ctx: D-Data OutputContent
Cfg: C-Konfig NoEvaluation:TRUE NoSecondPerson:TRUE NumbersFaithful:TRUE UnitsFaithful:TRUE NoOpinion:TRUE
Act: X-Exec Enforce
```

---

## Run A — English input, complete

**Input**

> This study examined the effect of intermittent fasting on cognitive performance in adults aged 25-45. We recruited 120 participants and randomly assigned them to either a 16:8 fasting protocol or a control group maintaining regular eating patterns over 12 weeks. Cognitive function was assessed using the Cambridge Neuropsychological Test Automated Battery (CANTAB) at baseline, week 6, and week 12. Results showed that the fasting group demonstrated a 14% improvement in sustained attention scores compared to 3% in the control group (p<0.01). Working memory showed no significant differences between groups. The main limitation of our study is the relatively short duration and the fact that participants were not blinded to their group assignment. We also acknowledge that dietary compliance was self-reported.

**Output**

```
**Context:** The study investigated how intermittent fasting affects cognitive performance in adults between 25 and 45 years old, using a randomized controlled trial design with 120 participants divided between a 16:8 fasting protocol and a control group with regular eating patterns over 12 weeks.
**Problem:** The research question addressed whether intermittent fasting would influence cognitive function compared to regular eating patterns.
**Method:** Participants were randomly assigned to either the 16:8 fasting protocol or control group, with cognitive function measured using the Cambridge Neuropsychological Test Automated Battery (CANTAB) at baseline, week 6, and week 12.
**Result:** The fasting group showed a 14% improvement in sustained attention scores compared to 3% in the control group (p<0.01), while working memory showed no significant differences between groups.
**Limitations:** The study had a relatively short duration, participants were not blinded to their group assignment, and dietary compliance was self-reported.
```

---

## Run B — Spanish input, no limitations stated

This run exercises the fallback: the abstract declares no limitations, so `$NoDeclared` must be reproduced literally rather than paraphrased or invented.

**Input**

> Se desarrolló un modelo predictivo para estimar la demanda energética en redes 5G utilizando datos históricos de 47 estaciones base en São Paulo durante 18 meses. El modelo combina series temporales con gradient boosting y alcanza un RMSE de 4.2 kWh por estación, superando al baseline ARIMA en un 31%. La predicción a 72 horas mantiene un error inferior al 8% en el 89% de los casos.

**Output**

```
Context: Modelo predictivo para demanda energética en redes 5G mediante datos de 47 estaciones base en São Paulo durante 18 meses.
Problem: Estimación de la demanda energética en infraestructura 5G.
Method: Combinación de series temporales con gradient boosting.
Result: RMSE de 4.2 kWh por estación, superación del baseline ARIMA en 31%, error inferior al 8% en el 89% de los casos para predicción a 72 horas.
Limitations: Not declared by the authors
```

---

## Run C — Portuguese input, informal, opinionated

This run exercises the opinion filter. The source is casual and contains the authors' own judgments ("achamos que o resultado é bom"), which must not survive into the summary.

**Input**

> A gente fez um trabalho sobre detecção de fake news em português usando modelos de linguagem. O problema é que quase tudo que existe é pra inglês e quando você tenta aplicar direto no português o desempenho cai muito, tipo 40% pior. Usamos o BERTimbau fine-tuned com um dataset que a gente mesmo montou, 15 mil notícias coletadas entre 2020 e 2023 do Twitter e de sites de checagem. O F1-score ficou em 0.847, que é bem melhor que o baseline multilingual que dava 0.71. Achamos que o resultado é bom mas obviamente o dataset é pequeno e muito focado em política, então não dá pra generalizar pra outros domínios. Também tem o problema que as fake news mudam muito rápido e o modelo pode ficar obsoleto.

**Output**

```
Context: Modelos multilíngues apresentam queda de desempenho (~40%) na detecção de fake news em português comparado ao inglês, motivando desenvolvimento de solução específica para o idioma.
Problem: Escassez de recursos para detecção de desinformação em português e baixa performance de modelos generalistas aplicados diretamente ao idioma.
Method: Fine-tuning do BERTimbau com dataset proprietário de 15.000 notícias coletadas do Twitter e sites de checagem (2020-2023).
Result: F1-score de 0.847, superando baseline multilíngue (0.71).
Limitations: Dataset limitado em tamanho e concentrado no domínio político, restringindo generalização para outros domínios; risco de obsolescência devido à evolução rápida das estratégias de desinformação.
```

---

## What to notice

**Three languages, one CoS.** `$OutLang = "InputLang"` carried the output language in all three runs without a separate rule per language. The CoS itself is written in English — that is deliberate and worth +20 points of context conformity over the same design written in Spanish ([log 003](../logs/CLL_Log_003.md)).

**Every number survived.** 14%, 3%, p<0.01, 4.2 kWh, 31%, 72 hours, 8%, 89%, 0.847, 0.71 — all reproduced exactly, with no unit conversion and no derived figures invented.

**The literal fallback held.** Run B produced "Not declared by the authors" verbatim rather than paraphrasing it or fabricating limitations. This was the first time in the benchmark that a literal value was copied without drift.

**The opinions did not survive.** Run C's source is full of first-person judgment; none of it reached the output, while the substantive limitations the authors stated were kept.

**The one failure.** Runs B and C dropped the bold markers on the labels. Content, structure, order and field count were perfect; only the visual formatting slipped. It is cosmetic, and it is exactly the kind of failure a mechanical validator catches — which is why the specification treats structure as a verifiable axis rather than a matter of prompting harder.
