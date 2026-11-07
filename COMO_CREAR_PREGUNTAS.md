# Cómo crear preguntas para la batería

Guía para redactar un banco de preguntas nuevo (a mano o con ayuda de una IA) que se cargue en la web sin errores. El formato técnico del JSON está en `FORMATO_JSON.md`; aquí se explica **cómo escribir las preguntas**.

## 1. Preparar el material
Antes de redactar nada, reúne por cada tema:
- El **texto oficial** de la norma o documento (BOE, BOPV, BOG, PDF del temario). No redactes de memoria: las cifras, plazos y números de artículo se equivocan con facilidad.
- Ejemplos de **preguntas reales** del examen, para copiar su estilo.
- El nombre y el alcance del tema (por ejemplo «Ley 12/2008, arts. 1-13»).

## 2. Numeración
- Cada tema tiene un `short` único: `"Tema 13"`.
- `n` es un entero único. Convención usada: **número de tema × 1000 + orden dentro del tema**.
  - Tema 13, pregunta 1 → `13001`; Tema 44, pregunta 77 → `44077`.
- Así puedes añadir preguntas a un tema sin renumerar nada y el historial de progreso del usuario (que se guarda por `n`) no se rompe. **No cambies ni reutilices un `n` ya publicado.**

## 3. Estructura de una pregunta
```json
{"n": 20052, "tema": "Tema 20",
 "q": "El Decreto 185/2015 entró en vigor:",
 "o": ["A los veinte días de su publicación.",
       "El día siguiente al de su publicación en el BOPV.",
       "El 25 de diciembre de 2016.",
       "El 1 de enero de 2016."],
 "c": 1}
```
- `q`: enunciado. `o`: **exactamente 4** opciones. `c`: índice de la correcta (0 = primera).
- Una pregunta por línea en el fichero, para que los cambios se vean bien en git.

## 4. Reglas de redacción
**Enunciado**
- Cita la fuente al empezar cuando haga falta: «Según la Ley 12/2008, de 5 de diciembre…», «Según el RD 888/2022…».
- Una sola idea por pregunta, y que tenga **una única respuesta correcta** inequívoca según el texto.
- Mejor frase afirmativa. Evita dobles negaciones.
- Las preguntas pueden terminar en dos puntos y completarse con la opción («…su objeto es regular un sistema de carácter:») o ser una pregunta directa.

**Opciones**
- 4 opciones de **longitud y forma parecidas**; la correcta no debe ser siempre la más larga ni la más detallada.
- Los **distractores deben ser plausibles**: cifras cercanas, el órgano parecido, el plazo de otra norma, el término contrario. Evita opciones absurdas que se descarten sin saber nada.
- Evita «todas las anteriores» y «ninguna de las anteriores», y las combinaciones tipo «a y b son correctas».
- Evita palabras que delatan la respuesta («siempre», «nunca», «solo») usadas únicamente en los distractores.
- Reparte la correcta entre las cuatro posiciones (A/B/C/D). Las opciones se barajan al mostrarlas, pero conviene que el JSON esté equilibrado.
- No repitas dos opciones iguales ni dos correctas posibles.

**Contenido**
- Cubre cada tema de forma proporcional a su peso: artículos clave, plazos, órganos y competencias, definiciones, requisitos, procedimientos, excepciones.
- Mezcla preguntas de memoria literal (plazos, cifras) con preguntas de comprensión.
- No metas opiniones ni información que no esté en la fuente.

## 5. Ejemplos
**Bien** — distractores plausibles, una sola correcta:
```json
{"n": 24039, "tema": "Tema 24",
 "q": "Según el Real Decreto 888/2022, de 18 de octubre, junto con la resolución se notificará:",
 "o": ["El dictamen propuesta.", "El plan de revisión.",
       "El informe del médico de familia.", "La tarjeta acreditativa."],
 "c": 0}
```
**Mal** — dos defectos: la correcta es la única larga y concreta, y los distractores son absurdos:
```json
{"q": "El plazo para resolver es:",
 "o": ["Nunca.", "Un día.", "Seis meses desde la solicitud, ampliable por causa justificada.", "Cien años."],
 "c": 2}
```

## 6. Flujo recomendado
1. Un tema cada vez: pega el texto de la norma y pide/redacta N preguntas (por ejemplo 30–100 según la extensión).
2. Marca las preguntas con **datos exactos** (cifras, plazos, fechas, artículos) para verificarlas contra la fuente.
3. Ensambla el JSON siguiendo `FORMATO_JSON.md` (campos `meta`, `temas`, `preguntas`).
4. **Valida** (ver abajo) y carga el fichero con el botón «Cargar otro temario (JSON)» para probarlo antes de publicarlo.
5. Haz una pasada de repaso: lee unas cuantas preguntas al azar de cada tema, comprueba la respuesta y el equilibrio de posiciones.
6. Si todo está bien, guárdalo como `preguntas.json` y súbelo.

## 7. Validación rápida
La web ya valida al cargar, pero puedes comprobarlo antes con Python:
```bash
python3 - <<'PY'
import json
d = json.load(open("preguntas.json", encoding="utf-8"))
temas = {t["short"] for t in d["temas"]}
vistos, errores = set(), []
for q in d["preguntas"]:
    n = q["n"]
    if n in vistos: errores.append(f"{n}: n repetido")
    vistos.add(n)
    if q["tema"] not in temas: errores.append(f"{n}: tema inexistente")
    if len(q["o"]) != 4 or not all(q["o"]): errores.append(f"{n}: necesita 4 opciones")
    if len(set(q["o"])) != 4: errores.append(f"{n}: opciones repetidas")
    if q["c"] not in (0, 1, 2, 3): errores.append(f"{n}: c fuera de 0-3")
print(len(d["preguntas"]), "preguntas,", len(errores), "errores")
print(*errores, sep="\n")
PY
```

## 8. Lista de comprobación final
- [ ] Todas las preguntas tienen su `tema` definido en `temas`.
- [ ] `n` únicos y con la convención tema × 1000 + orden.
- [ ] 4 opciones distintas y `c` entre 0 y 3.
- [ ] Una sola respuesta correcta clara según la fuente.
- [ ] Datos exactos revisados contra el texto oficial.
- [ ] Respuestas correctas repartidas entre A, B, C y D.
- [ ] JSON válido y en UTF-8.
- [ ] `meta.id` distinto si es un temario nuevo.
