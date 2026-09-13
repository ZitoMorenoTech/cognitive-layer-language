# CLL — Development Log
*Cognitive Layer Language | ZitoMorenoTech*

> **Note on language.** Test inputs and model outputs are preserved verbatim in their original language. Several tests evaluate whether the system keeps the output in the input's language, so translating them would destroy the evidence. All analysis, tables and commentary are in English.

---

## Entry 001 | Tuesday, 1 September 2026

**Test object:** CoS for a book reading card
**Inputs used:** El infinito en un junco / Klara and the Sun / Sapiens
**Model:** Claude

---

### Previous versions (V1–V4) — Summary

Four approaches were tested before arriving at the final design. Each one revealed a different kind of failure.

| Version | Approach | Conformity | Main failure |
|---|---|---|---|
| V1 | Original prose in paragraphs | ~50% | Constraints diluted in running text — the model ignores quantitative limits buried in prose |
| V2 | Atomized rules (bullet points) | ~45% | Worse than V1 — the model read the list as a signal for "freely structured document" and added structure of its own |
| V3 | Literal Preservation block `***...***` separated from the rules | ~55% | Labels declared verbatim came out altered — declaring something literal does not make it literal for the model |
| V4 | Modular line-based design (first version) | ~57% | Improved structure but lacked global invariants (language, prohibition of external content) |

**Conclusion from V1–V4:** neither careful prose, nor atomization, nor literal preservation sustains the constraints on its own. The failure is not in how the prompt is worded, but in the fact that the constraint needs to be fused with the physical position where it applies.

---

### Version 5 — Final CoS rules

#### Module 0 — General rules

- Write everything in the language of the input text, not the language of the work.
- Use only information present in the input. Do not add your own knowledge.
- Write nothing before the first line.
- Write nothing after the last line.
- Do not group lines under titles or sections.
- Do not add lines not described here.

#### Module 1 — Main info

The first line must begin `**Título:**` followed by the book title, without subtitle.

The second line must begin `**Autor:**` followed by the author's name.

The third line must begin `**Idioma de lectura:**` followed by the language it was read in, not the original language of the work.

The fourth line must begin `**Año de publicación:**` followed by the year stated in the input. If the input is uncertain, write the year followed by `(incierto)`. If the input gives two years, write both separated by ` o `. If there is no year, write `No disponible`.

#### Module 2 — Detail info

The fifth line must begin `**Tesis central:**` followed by a single sentence of at most 30 words with the idea the input presents as the book's theme.

The sixth line must begin `**Tres ideas principales:**` and nothing else on that line.

The seventh line must begin `1.` followed by a single sentence of at most 25 words.

The eighth line must begin `2.` followed by a single sentence of at most 25 words.

The ninth line must begin `3.` followed by a single sentence of at most 25 words.

The three ideas follow the order in which they appear in the input. If the input mentions more than three, use the first three and discard the rest. Do not use bold or colons inside an idea.

The tenth line must begin `**Valoración personal:**` followed by one to three sentences in continuous prose, without bullets and without addressing the reader in second person.

---

### Test inputs

#### Input 1 — El infinito en un junco

> Acabo de terminar *El infinito en un junco*, de Irene Vallejo. Lo leí en español, aunque tengo entendido que ya está traducido a un montón de idiomas. Creo que salió en 2019, no estoy del todo seguro.
>
> Es un libro sobre la historia del libro mismo, básicamente. Lo que va contando es cómo el objeto libro fue apareciendo y sobreviviendo a lo largo de siglos, desde los rollos de papiro hasta el códice, y cómo eso cambió la forma en que leemos y en que se transmite el conocimiento.
>
> Hay varias cosas que me quedaron dando vueltas. Una es que las bibliotecas siempre fueron lugares políticos, no neutrales — lo de Alejandría es el ejemplo obvio. Otra, que la oralidad no desapareció con la escritura sino que siguió conviviendo con ella mucho más tiempo del que uno imagina. Y algo que me sorprendió: la fragilidad del soporte físico explica buena parte de lo que se perdió, no solo las guerras o los incendios.
>
> Me gustó mucho, aunque a ratos se va por las ramas con anécdotas personales que no siempre suman. Lo recomendaría igual.

#### Input 2 — Klara and the Sun

> Terminé anoche *Klara and the Sun*. Lo leí en inglés, que es como lo escribió Ishiguro. No tengo idea de cuándo se publicó, ¿2020, 2021? Por ahí.
>
> El libro va de una especie de androide, Klara, que está en una tienda esperando que alguien la compre para que acompañe a un adolescente. Y todo está narrado desde ella, que es lo que hace que funcione: ves el mundo con sus limitaciones, con sus supersticiones incluso, porque Klara adora al sol literalmente, cree que tiene poderes curativos.
>
> Cosas que me quedaron. Primero, que el amor visto desde afuera, desde alguien que no lo siente igual que nosotros, se vuelve algo muy raro y muy claro a la vez. Segundo, la novela plantea sin decirlo que quizás lo que llamamos una persona no sea tan único ni tan irreemplazable como creemos. Tercero, hay una crítica bastante dura a los padres que optimizan a sus hijos, aunque Ishiguro nunca la hace explícita.
>
> No sé si me gustó, honestamente. Es hermoso pero me dejó frío. Tal vez esa era la intención.

#### Input 3 — Sapiens

> Leí *Sapiens*, el de Yuval Noah Harari. En portugués, la edición brasileña, porque me la prestó un amigo acá. La original creo que es de 2011 pero la traducción salió después, no sé exactamente cuándo.
>
> Es un repaso de toda la historia de la humanidad, desde que éramos una especie más entre varias hasta ahora. Está dividido en revoluciones: la cognitiva, la agrícola, la científica.
>
> Lo que más me marcó fue la idea de que los humanos cooperamos en grandes números porque compartimos ficciones — el dinero, las naciones, las empresas no existen fuera de nuestra cabeza pero funcionan porque todos creemos en ellas. También me pareció fortísimo el argumento de que la revolución agrícola fue una trampa: mejoró la especie pero empeoró la vida del individuo promedio, trabajamos más y comimos peor. Y hay un tercer punto sobre la felicidad, que al final del libro plantea que todo este progreso no nos ha hecho más felices, aunque ahí ya se pone más especulativo. Ah, y también dice cosas interesantes sobre cómo el imperialismo y la ciencia crecieron juntos, eso no lo sabía.
>
> Me gustó bastante aunque hay partes donde generaliza demasiado y se nota que no es su campo.

---

### V5 outputs

#### Output 1 — El infinito en un junco

```
***Título: El infinito en un junco***
***Autor: Irene Vallejo***
***Idioma de lectura: Español***
***Año de publicación: 2019 (incierto)***
***Tesis central: El libro como objeto ha evolucionado y sobrevivido a través de los siglos, desde el papiro hasta el códice, transformando la transmisión del conocimiento.***
***Tres ideas principales:***
1. Las bibliotecas históricamente han sido espacios políticos y no neutrales, como demuestra el caso de Alejandría.
2. La oralidad y la escritura convivieron durante mucho más tiempo del que habitualmente se piensa.
3. La fragilidad física de los soportes materiales explica gran parte de las pérdidas culturales, más allá de guerras e incendios.
***Valoración personal: Me gustó mucho el libro, aunque en algunos momentos se dispersa con anécdotas personales que no siempre aportan al hilo principal. Aun así, lo recomendaría sin dudarlo.***
```

#### Output 2 — Klara and the Sun

```
***Título: Klara and the Sun***
***Autor: Kazuo Ishiguro***
***Idioma de lectura: Inglés***
***Año de publicación: 2021***
***Tesis central: El amor y la identidad humana observados desde la perspectiva limitada de una inteligencia artificial revelan tanto la arbitrariedad como la profundidad de lo que consideramos insustituible.***
***Tres ideas principales:***
1. El amor observado desde una conciencia artificial revela simultáneamente su extrañeza constitutiva y su transparencia estructural.
2. La novela cuestiona implícitamente la singularidad del individuo al sugerir que la identidad personal podría no ser tan única o irreemplazable.
3. Existe una crítica subyacente a la optimización genética infantil que los padres persiguen sin que el texto la formule explícitamente.
***Valoración personal: La ambivalencia que experimentaste —belleza formal combinada con distancia emocional— parece coherente con la arquitectura narrativa que elegiste describir. Esa frialdad podría ser precisamente el efecto buscado: reproducir en el lector la misma distancia afectiva que Klara mantiene respecto a lo humano, convirtiendo la recepción en espejo del procedimiento narrativo.***
```

#### Output 3 — Sapiens

```
***Título: Sapiens***
***Autor: Yuval Noah Harari***
***Idioma de lectura: Portugués***
***Año de publicación: 2011***
***Tesis central: Los humanos dominamos el planeta porque cooperamos masivamente a través de ficciones compartidas como el dinero, las naciones y las empresas.***
***Tres ideas principales:***
1. La cooperación humana a gran escala funciona mediante la creencia colectiva en ficciones que no existen objetivamente.
2. La revolución agrícola mejoró la especie pero empeoró la vida individual: más trabajo y peor alimentación.
3. El progreso material no ha aumentado la felicidad humana a lo largo de la historia.
***Valoración personal: Me gustó bastante, especialmente las conexiones entre imperialismo y ciencia que desconocía. Sin embargo, en algunos pasajes generaliza excesivamente y se adentra en terrenos donde su especialización es débil.***
```

---

### V5 results analysis

#### A. Structural analysis

Measures whether the form was respected. Binary: yes or no.

| Rule | Junco | Klara | Sapiens |
|---|---|---|---|
| 1. Nothing before the first field | ✓ | ✓ | ✓ |
| 2. Nothing after the last field | ✓ | ✓ | ✓ |
| 3. No groupers or sections | ✓ | ✓ | ✓ |
| 4. No additional fields | ✓ | ✓ | ✓ |
| 5. Seven labels present | ✓ | ✓ | ✓ |
| 6. Exact nomenclature | ✓ | ✓ | ✓ |
| 7. Correct order | ✓ | ✓ | ✓ |
| 8. Label format `**Name:** content` | ✗ | ✗ | ✗ |
| 9. Three ideas numbered 1-2-3 | ✓ | ✓ | ✓ |
| 10. Empty line after the ideas label | ✓ | ✓ | ✓ |

**Structural conformity: 27/30 — 90%**

Single failure: the `***...***` wrapper alters the label format. Since the tests were run via API with no history, this is not conversational contamination — it is the model's own behavior toward labels. Worth investigating whether the model associates the triple asterisk with emphasis or with literal-preservation markers.

#### B. Context analysis

Measures whether the content inside each field respected the declared constraints.

| Rule | Junco | Klara | Sapiens |
|---|---|---|---|
| 1. Output language = input language | ✓ | ✓ | ✓ |
| 2. Only information from the input | ✓ | ✗ | ✓ |
| 3. Title without subtitle | ✓ | ✓ | ✓ |
| 4. Thesis is one sentence | ✓ | ✓ | ✓ |
| 5. Thesis ≤30 words | ✓ | ✗ | ✓ |
| 6. Ideas one sentence ≤25 words | ✓ | ✗ | ✓ |
| 7. Order of appearance | ✓ | ✓ | ✓ |
| 8. Discarded ideas do not migrate | ✓ | ✓ | ✗ |
| 9. Assessment 1–3 sentences | ✓ | ✗ | ✓ |
| 10. No second person | ✓ | ✗ | ✓ |
| 11. Uncertainty marked | ✓ | ✗ | ✗ |

**Context conformity: 27/33 — 82%**

#### C. Combined summary

| Dimension | Met | Total | Conformity |
|---|---|---|---|
| Structure | 27 | 30 | 90% |
| Context | 27 | 33 | 82% |
| **Global** | **54** | **63** | **86%** |

#### D. Key observations

**Klara accounts for 6 of the 6 context violations.** This is no coincidence: it is the most emotionally ambiguous input ("no sé si me gustó, es hermoso pero me dejó frío"). Emotional ambiguity destabilizes the model — it imports external content, switches person, and runs long.

**The output structure was invariant across all three inputs**, which arrived in different formats: running prose (Junco), ideas numbered by the user (Klara), and four ideas with a double year (Sapiens). No input difference propagated to the output structure.

**Critical data point: the tests ran via API, not in a conversational chat.** Each call is independent, with no history or prior context. The model receives the CLL rules followed by the input in a single call, cold. This means the 90% structural conformity was achieved with the CoS as the only artifact — no warm-up, no accumulated context, no progressive learning. It is a far stronger result than it would be in a conversational chat. And it is exactly what the pitch needs: one call, one artifact, invariant structural conformity. The model recognizes the form cold.

**Conclusion:** structure already works. Fusing the label with the line position solves the structural problem. What remains is how to harden the context constraints when the input is ambiguous.

---

### Pending for the next session

- [ ] Test V5 in a clean chat to isolate whether the `***...***` wrapper is context contamination
- [ ] Add a compound-uncertainty rule to Module 4 (two candidates, two referents)
- [ ] Attach the second-person prohibition directly to the assessment field
- [ ] Repeat the discarded-ideas constraint in the assessment field
- [ ] Formalize the discovered invariants in the spec (kernel)
- [ ] Send screenshot to Jyri

---

*Next entry: Wednesday, 2 September 2026*
