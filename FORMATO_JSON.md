# Formato del JSON de preguntas

`index.html` carga el temario desde un JSON externo. Por defecto lee `preguntas.json` (misma carpeta que `index.html`). Para usar un temario nuevo puedes sustituir ese archivo o cargarlo desde la propia web (ver abajo).

## Estructura

```json
{
  "meta": {
    "id": "op2026-01-administrativo",
    "titulo": "OP2026/01 · Administrativo/a",
    "subtitulo": "Ayuntamiento de X — Subgrupo C1"
  },
  "temas": [
    { "short": "Tema 1", "title": "Constitución: Título preliminar" },
    { "short": "Tema 2", "title": "Ley 39/2015 Procedimiento Administrativo" }
  ],
  "preguntas": [
    {
      "n": 1,
      "tema": "Tema 1",
      "q": "¿En qué se constituye España según la Constitución?",
      "o": [
        "En un Estado federal y democrático.",
        "En un Estado social y democrático de Derecho.",
        "En una monarquía absolutista.",
        "En un Estado centralizado."
      ],
      "c": 1
    }
  ]
}
```

## Campos

### `meta`
| Campo | Obligatorio | Descripción |
|---|---|---|
| `titulo` | sí | Título que aparece en la cabecera. |
| `subtitulo` | no | Texto bajo el título. |
| `id` | recomendado | Identificador único del temario (minúsculas, sin espacios). El historial de progreso se guarda por `id`, así que **cada temario debe tener un `id` distinto**. Si falta, se genera a partir del título. |

### `temas[]`
| Campo | Obligatorio | Descripción |
|---|---|---|
| `short` | sí | Nombre corto y único (`"Tema 1"`). Es la clave con la que las preguntas se enlazan al tema. |
| `title` | sí | Título largo que se muestra en el selector. |

### `preguntas[]`
| Campo | Obligatorio | Descripción |
|---|---|---|
| `n` | sí | Número entero **único** de la pregunta. No tiene que ser correlativo ni seguir ningún orden. Es la clave del historial de aciertos/fallos. |
| `tema` | sí | Debe coincidir exactamente con el `short` de un tema. |
| `q` | sí | Enunciado. |
| `o` | sí | Array con **exactamente 4** opciones (texto no vacío). Se barajan al mostrarse. |
| `c` | sí | Índice de la opción correcta en `o`: `0`, `1`, `2` o `3` (empieza en 0: `0` = primera opción). |

## Qué NO hay que escribir
El total de preguntas, el número de preguntas por tema y los rangos de cada tema se calculan solos. Ya no existen `total`, `count`, `lo`, `hi` ni `temaTitulo`.

## Validación
Al cargar, la web comprueba el JSON y muestra la lista de errores indicando la pregunta afectada (campos que faltan, `n` repetido, tema inexistente, `o` sin 4 opciones, `c` fuera de 0–3).

## Cómo cargarlo
1. **Sustituir el archivo**: guarda tu JSON como `preguntas.json` junto a `index.html`. Hay que servir la carpeta por HTTP (`python3 -m http.server`, GitHub Pages, etc.); abrir `index.html` con doble clic (`file://`) hace que el navegador bloquee la lectura.
2. **Desde la web** (funciona también con doble clic): botón **«Cargar otro temario (JSON)»** en la pantalla de inicio. El temario se guarda en el navegador (`localStorage`); **«Volver al temario por defecto»** lo quita.

## Consejos
- Mantén una pregunta por línea en `preguntas`, facilita los diffs.
- Si cambias el contenido de un temario existente sin cambiar su `id`, el historial seguirá asociado a los números `n`. Para un temario nuevo, usa un `id` nuevo.
- Guarda el archivo en UTF-8.
