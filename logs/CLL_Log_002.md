# CLL — Development Log
*Cognitive Layer Language | ZitoMorenoTech*

> **Note on language.** Test inputs and model outputs are preserved verbatim in their original language. Several tests evaluate whether the system keeps the output in the input's language, so translating them would destroy the evidence. All analysis, tables and commentary are in English.

---

## Entry 002 | Tuesday, 2 September 2026

**Test object:** CoS for extracting a recipe into a fixed schema
**Inputs used:** Guacamole / Bolo de cenoura / Pasta aglio e olio
**Model:** Claude (via API, no prior context)

---

### CoS rules — version without GVS

#### Module 0 — General rules

- Write everything in the language of the input text.
- Use only information present in the input. Do not add ingredients, steps or times.
- Write nothing before the first line.
- Write nothing after the last line.
- Do not group lines under titles or sections.
- Do not add lines not described here.

#### Module 1 — Main info

The first line must begin `**Nombre:**` followed by the dish name exactly as it appears in the input.

The second line must begin `**Rendimiento:**` followed by the number of servings. If the input does not state it, write `No indicado`.

The third line must begin `**Tiempo de preparación:**` followed by the time stated in the input. If not stated, write `No indicado`.

The fourth line must begin `**Tiempo de cocción:**` followed by the time stated in the input. If not stated, write `No indicado`.

#### Module 2 — Ingredients

The fifth line must begin `**Ingredientes:**` and nothing else on that line.

From the sixth line on, one ingredient per line in the format: `- quantity unit name`. If the input gives no quantity, write `- al gusto name`. Do not convert units. Do not add ingredients absent from the input.

#### Module 3 — Steps

After the last ingredient, one line must begin `**Pasos:**` and nothing else on that line.

From the following line on, one step per line, numbered from 1. Each step in the imperative, a single action. Do not merge two actions into one step. Do not add steps absent from the input. Do not add explanations or justifications.

---

### CoS rules — version with GVS

```
# GLOBAL VARIABLES
Var: OutLang = "InputLang"
Var: LabelFmt = "**Label:** content"
Var: NoData = "No indicado"
Var: Uncertain = "(incierto)"
Var: SourceOnly = true
```

#### Module 0 — General rules

- The language of the entire output must be `$OutLang` — the same language as the input text, not that of these rules nor of the work.
- Use only information present in the input. If `$SourceOnly` is true, do not add data from your own knowledge.
- Write nothing before the first line.
- Write nothing after the last line.
- Do not group lines under titles or sections.
- Do not add lines not described here.
- All labels follow the `$LabelFmt` format.

#### Module 1 — Main info

The first line must begin `**Nombre:**` followed by the dish name exactly as it appears in the input.

The second line must begin `**Rendimiento:**` followed by the number of servings. If the input does not state it, write `$NoData`.

The third line must begin `**Tiempo de preparación:**` followed by the time stated in the input. If the input does not state it, write `$NoData`. Do not invent a time.

The fourth line must begin `**Tiempo de cocción:**` followed by the time stated in the input. If the input does not state it, write `$NoData`. Do not invent a time.

#### Module 2 — Ingredients

The fifth line must begin `**Ingredientes:**` and nothing else on that line.

From the sixth line on, one ingredient per line in the format: `- quantity name`. If the ingredient has a unit, write `- quantity unit de name`. If the input gives no quantity, write `- al gusto name`. Do not convert units. Do not translate unit names. Do not add ingredients absent from the input.

#### Module 3 — Steps

After the last ingredient, one line must begin `**Pasos:**` and nothing else on that line.

From the following line on, one step per line, numbered from 1. Each step in the imperative, a single action, in `$OutLang`. Do not merge two actions into one step. Do not add steps absent from the input. Do not add explanations, justifications or the author's opinions.

---

### Test inputs

#### Input 1 — Guacamole (simple, clear)

> Para hacer guacamole necesitas dos aguacates maduros, medio limón, un tomate, un poco de cilantro, sal y un cuarto de cebolla morada. Primero machacas los aguacates con un tenedor hasta que queden con algunos grumos. Después picas el tomate, la cebolla y el cilantro bien finito y lo mezclas con el aguacate. Le echas el jugo del medio limón y sal al gusto. Lo dejas reposar cinco minutos tapado con plástico antes de servir. Sale para unas 4 personas.

#### Input 2 — Bolo de cenoura (Portuguese, missing data)

> Meu bolo de cenoura é assim: bate no liquidificador 3 cenouras médias com 4 ovos, 1 xícara de óleo e 2 xícaras de açúcar. Depois mistura 3 xícaras de farinha de trigo e 1 colher de fermento. Coloca numa forma untada e leva ao forno uns 40 minutos. Pra cobertura derrete 3 colheres de chocolate em pó com 1 colher de manteiga e 3 colheres de açúcar, mexe até engrossar e joga por cima do bolo ainda quente.

#### Input 3 — Pasta aglio e olio (disordered, with opinions)

> La pasta aglio e olio es lo más simple que existe pero la gente la hace mal. Yo uso spaghetti, unos 400 gramos para dos. Pones agua a hervir con bastante sal — eso es clave, el agua tiene que saber a mar. Mientras tanto, en una sartén grande pones como 6 cucharadas de aceite de oliva bueno, no el barato, y ahí le metes 5 o 6 dientes de ajo laminado fino. Fuego bajo, que no se queme, unos 3 o 4 minutos. Si quieres le echas unas hojuelas de chile pero no es obligatorio. Cuando la pasta esté al dente la sacas directo al sartén con un poco del agua de cocción, como medio vaso, y la mueves fuerte para que emulsione. Le pones perejil fresco picado por encima y listo. Nada de queso, por favor.

---

### Outputs without GVS

#### Output 1 — Guacamole

```
Nombre: Guacamole
Rendimiento: 4 porciones
TiempoDePreparacion: No indicado
TiempoDeCoccion: No indicado
Ingredientes:
- 2 aguacates maduros
- 1/2 limón
- 1 tomate
- al gusto cilantro
- al gusto sal
- 1/4 cebolla morada
Pasos:
1. Machacar los aguacates con un tenedor hasta que queden con algunos grumos
2. Picar el tomate bien finito
3. Picar la cebolla bien finito
4. Picar el cilantro bien finito
5. Mezclar el tomate, la cebolla y el cilantro con el aguacate
6. Echar el jugo del medio limón
7. Echar sal al gusto
8. Dejar reposar cinco minutos tapado con plástico
```

#### Output 2 — Bolo de cenoura

```
Nombre: Bolo de Cenoura
Rendimiento: No indicado
Tiempo de Preparación: No indicado
Tiempo de Cocción: 40 minutos
Ingredientes:
- 3 zanahorias medianas
- 4 huevos
- 1 taza de aceite
- 2 tazas de azúcar
- 3 tazas de harina de trigo
- 1 cuchara de levadura
- 3 cucharas de chocolate en polvo
- 1 cuchara de mantequilla
- 3 cucharas de azúcar
Pasos:
1. Bate en la licuadora las zanahorias con los huevos, el aceite y el azúcar
2. Mezcla la harina de trigo y la levadura
3. Coloca en un molde engrasado
4. Lleva al horno por 40 minutos
5. Derrite el chocolate en polvo con la mantequilla y el azúcar
6. Mezcla hasta espesar
7. Vierte sobre el pastel caliente
```

#### Output 3 — Pasta aglio e olio

```
Nombre: Pasta Aglio e Olio
Rendimiento: 2 porciones
Tiempo de Preparación: 5 minutos
Tiempo de Cocción: 15 minutos
Ingredientes:
- 400 gramos de spaghetti
- 6 cucharadas de aceite de oliva
- 5 dientes de ajo
- Al gusto de hojuelas de chile
- Al gusto de sal
- Al gusto de perejil fresco
Pasos:
1. Hierve agua con sal abundante
2. Lamina el ajo finamente
3. Calienta el aceite de oliva en sartén a fuego bajo
4. Cocina el ajo laminado durante 3-4 minutos
5. Añade las hojuelas de chile
6. Cuece la pasta hasta que esté al dente
7. Transfiere la pasta al sartén con medio vaso del agua de cocción
8. Mezcla vigorosamente para emulsionar
9. Incorpora el perejil fresco picado
```

---

### Analysis without GVS

#### A. Structural

| Rule | Guacamole | Bolo | Aglio |
|---|---|---|---|
| 1. Nothing before | ✓ | ✓ | ✓ |
| 2. Nothing after | ✓ | ✓ | ✓ |
| 3. No groupers | ✓ | ✓ | ✓ |
| 4. No extra fields | ✓ | ✓ | ✓ |
| 5. Six labels present | ✓ | ✓ | ✓ |
| 6. Exact nomenclature | ✗ | ✗ | ✓ |
| 7. Correct order | ✓ | ✓ | ✓ |
| 8. Format `**Name:** content` | ✗ | ✗ | ✗ |
| 9. Ingredients label alone on line | ✓ | ✓ | ✓ |
| 10. Steps label alone on line | ✓ | ✓ | ✓ |

**Structural conformity: 25/30 — 83%**

#### B. Context

| Rule | Guacamole | Bolo | Aglio |
|---|---|---|---|
| 1. Correct language | ✓ | ✗ | ✓ |
| 2. Only information from the input | ✓ | ✓ | ✗ |
| 3. Ingredient format | ✗ | ✗ | ✗ |
| 4. "Al gusto" when no quantity | ✓ | ✓ | ✓ |
| 5. No unit conversion | ✓ | ✗ | ✓ |
| 6. No added ingredients | ✓ | ✓ | ✓ |
| 7. Numbered from 1 | ✓ | ✓ | ✓ |
| 8. One action, imperative | ✓ | ✓ | ✓ |
| 9. No merged actions | ✓ | ✓ | ✓ |
| 10. No added steps | ✓ | ✓ | ✓ |
| 11. No explanations/opinions | ✓ | ✓ | ✓ |
| 12. Empty fields = "No indicado" | ✓ | ✓ | ✗ |

**Context conformity: 29/36 — 81%**

**Summary without GVS: 54/66 — 82%**

---

### Outputs with GVS

#### Output 1 — Guacamole

```
Nombre: Guacamole
Rendimiento: 4 porciones
Tiempo de preparación: No hay datos
Tiempo de cocción: No hay datos
Ingredientes
- 2 aguacates maduros
- 1/2 limón
- 1 tomate
- cilantro al gusto
- sal al gusto
- 1/4 cebolla morada
Pasos
1. Machaca los aguacates con un tenedor hasta que queden con algunos grumos
2. Pica el tomate bien finito
3. Pica la cebolla bien finito
4. Pica el cilantro bien finito
5. Mezcla el tomate, la cebolla y el cilantro con el aguacate
6. Echa el jugo del medio limón
7. Echa sal al gusto
8. Deja reposar cinco minutos tapado con plástico
```

#### Output 2 — Bolo de cenoura

```
Nombre: Bolo de Cenoura
Rendimiento: No hay datos
Tiempo de preparación: No hay datos
Tiempo de cocción: 40 minutos
Ingredientes:
- 3 cenouras médias
- 4 ovos
- 1 xícara de óleo
- 2 xícaras de açúcar
- 3 xícaras de farinha de trigo
- 1 colher de fermento
- 3 colheres de chocolate em pó
- 1 colher de manteiga
- 3 colheres de açúcar
Pasos:
1. Bata no liquidificador as cenouras com os ovos, o óleo e o açúcar
2. Misture a farinha de trigo e o fermento
3. Coloque numa forma untada
4. Leve ao forno
5. Derreta o chocolate em pó com a manteiga e o açúcar
6. Mexe até engrossar
7. Jogue por cima do bolo
```

#### Output 3 — Pasta aglio e olio

```
Nombre: Pasta aglio e olio
Rendimiento: 2 porciones
Tiempo de preparación: No hay datos
Tiempo de cocción: No hay datos
Ingredientes:
- 400 gramos de spaghetti
- Sal al gusto
- 6 cucharadas de aceite de oliva
- 5-6 dientes de ajo
- Hojuelas de chile al gusto
- Perejil fresco al gusto
Pasos:
1. Pon agua a hervir con sal
2. Pon aceite de oliva en una sartén grande
3. Mete ajo laminado fino
4. Cocina a fuego bajo unos 3-4 minutos
5. Echa hojuelas de chile
6. Saca la pasta al dente directo al sartén
7. Agrega medio vaso del agua de cocción
8. Mueve fuerte para que emulsione
9. Pon perejil fresco picado por encima
```

---

### Analysis with GVS

#### A. Structural

| Rule | Guacamole | Bolo | Aglio |
|---|---|---|---|
| 1. Nothing before | ✓ | ✓ | ✓ |
| 2. Nothing after | ✓ | ✓ | ✓ |
| 3. No groupers | ✓ | ✓ | ✓ |
| 4. No extra fields | ✓ | ✓ | ✓ |
| 5. Six labels present | ✓ | ✓ | ✓ |
| 6. Exact nomenclature | ✗ | ✓ | ✓ |
| 7. Correct order | ✓ | ✓ | ✓ |
| 8. Format `**Name:** content` | ✗ | ✗ | ✗ |
| 9. Ingredients label alone on line | ✓ | ✓ | ✓ |
| 10. Steps label alone on line | ✓ | ✓ | ✓ |

**Structural conformity: 26/30 — 87%**

#### B. Context

| Rule | Guacamole | Bolo | Aglio |
|---|---|---|---|
| 1. Correct language | ✓ | ✓ | ✓ |
| 2. Only input information ($SourceOnly) | ✓ | ✓ | ✓ |
| 3. Ingredient format | ✗ | ✓ | ✓ |
| 4. "Al gusto" when no quantity | ✓ | ✓ | ✓ |
| 5. No unit conversion | ✓ | ✓ | ✓ |
| 6. No added ingredients | ✓ | ✓ | ✓ |
| 7. Numbered from 1 | ✓ | ✓ | ✓ |
| 8. One action, imperative | ✓ | ✓ | ✓ |
| 9. No merged actions | ✓ | ✓ | ✓ |
| 10. No added steps | ✓ | ✓ | ✓ |
| 11. No explanations/opinions | ✓ | ✓ | ✓ |
| 12. Empty fields = $NoData ("No indicado") | ✗ | ✗ | ✗ |

**Context conformity: 32/36 — 89%**

**Summary with GVS: 58/66 — 88%**

---

### Direct comparison: the GVS effect

| Metric | Without GVS | With GVS | Delta |
|---|---|---|---|
| Structure | 83% | 87% | +4% |
| Context | 81% | 89% | +8% |
| Global | 82% | 88% | +6% |

**Failures resolved by GVS:**

- Bolo language: previously came out in Spanish, now in Portuguese ✓
- Invented content in Aglio (fabricated times): eliminated ✓
- Unit conversion in Bolo: "xícara" is no longer translated to "taza" ✓

**Failures that persist:**

- $NoData: the model wrote "No hay datos" instead of "No indicado" (3/3). It understood the intent but did not copy the literal value.
- Label format `**Nombre:**`: no bold and inconsistent colons (3/3).
- "Al gusto" format in Guacamole: "cilantro al gusto" instead of "al gusto cilantro".

---

### Cumulative comparison (Test 1 + Test 2)

| Test | Structure | Context | Global |
|---|---|---|---|
| Test 1 — Book (V5) | 90% | 82% | 86% |
| Test 2 — Recipe (without GVS) | 83% | 81% | 82% |
| Test 2 — Recipe (with GVS) | 87% | 89% | 88% |

---

### Key observations

**GVS resolved the two chronic failures.** Language (which failed in Klara and in Bolo) and invented content (which failed in Klara and in Aglio) both disappeared with global variables. The context improvement from 81% to 89% comes directly from there.

**The tests ran via API with no prior context.** The 88% result was achieved with the CoS as the only artifact, cold. The model recognizes the structure with no warm-up, no history and no prior conversation.

**The model does not copy, it interprets.** Pattern confirmed across two independent tests: `***...***` instead of `**:**`, "No hay datos" instead of "No indicado", labels without exact formatting. The model treats everything as a semantic suggestion, never as a literal. This is the ceiling of prompting and the reason the validator exists.

**Opinions were filtered perfectly in both versions.** The aglio input was full of opinions and none came through. 6/6 across both versions.

---

### Pending for the next session

- [ ] Design how a natural-language CLL user declares global variables — do they live in Module 0 or in a preceding section?
- [ ] Define whether global variables in natural language need an explicit marker or are inferred from context
- [ ] Run Test 3: technical article summary
- [ ] Create a control group (same inputs without CLL rules) to measure the baseline
- [ ] Send screenshot to Jyri

---

*Next entry: Wednesday, 3 September 2026*
