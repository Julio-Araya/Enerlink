# Reglas de diseño · Caso Enex × Enerlink

Documento de referencia para `index.html`. Antes de agregar o mover cualquier bloque,
revisar TOKENS y REGLAS. Las reglas de layout existen porque son las que se rompen seguido.

---

## 1. Tokens

Todos los tokens viven en `:root` de `index.html`. **No inventar colores nuevos ni escribir
hex sueltos en el CSS de un componente** — si un valor no está acá, no se usa.

### Color

| Token | Valor | Uso |
|---|---|---|
| `--bg` | `#F7F8F5` | Fondo de página. Fondo de cajas "apagadas" (opción no elegida). |
| `--card` | `#FFFFFF` | Fondo de caja de contenido neutra (datos, causas, bloques). |
| `--ink` | `#16201B` | Texto principal, títulos, cifras. |
| `--ink-soft` | `#4B564F` | Texto de cuerpo, párrafos, notas. |
| `--ink-faint` | `#8A938C` | Metadatos, etiquetas, subtextos, captions. |
| `--line` | `#E2E6DF` | Bordes de caja. |
| `--line-soft` | `#EDEFEA` | Separadores internos y fondos de encabezado dentro de una caja. |
| `--accent` | `#1F7A54` | Verde Enerlink. **Solo foco.** Ver regla de acento. |
| `--accent-soft` | `#E7F2EC` | Fondo del elemento en foco. |
| `--danger` | `#B23A2E` | Solo métricas que caen o incumplen (78% ↓). |
| `--danger-soft` | `#F6E7E4` | Fondo de alerta crítica. |
| `--amber` | `#B5771A` | Cálido de Enex: identifica al cliente / lo suyo. |
| `--amber-soft` | `#F6EEDD` | Fondo del cálido de Enex. |

### Tipografía

| Token | Familia | Uso |
|---|---|---|
| `--disp` | Fraunces (serif) | `h1`, `h2`, cifras grandes, nombres de nodo, citas (en itálica). Peso 500. Nunca para cuerpo. |
| `--sans` | Inter | Cuerpo, `h3`, notas, etiquetas de caja. Base 16px / 1.65. |
| `--mono` | JetBrains Mono | Eyebrows, labels de beat, tags, datos tabulados, captions. Siempre con `letter-spacing` y en mayúsculas cuando es etiqueta. |

Escala en uso: cuerpo 16px · `p.lead` 19px · nota dentro de caja 13–14.5px ·
etiqueta mono 10.5–12.5px · cifra de caja 23px · cifra de beat `clamp(52px,8vw,84px)`.

### Regla de acento (la que más se rompe)

El verde (`--accent` / `--accent-soft`) marca **el foco de la sección: la única cosa que el
lector debe mirar**. En la práctica: el cruce del caso en el mapa de industria, la opción
elegida en "La elección", la sección activa en el nav, el eyebrow que ubica la sección.

- Nunca como decoración, relleno, ni para "dar vida" a una caja.
- Nunca en dos elementos que compiten dentro del mismo bloque.
- Todo lo neutro va en gris o blanco: `--card` + `--line`, texto en `--ink-soft`.
- Los actores/opciones que no son el foco quedan neutros aunque sean importantes.
- `--danger` no es acento: es estado (una métrica que cae). `--amber` tampoco: es identidad de Enex.

---

## 2. Reglas de layout

### 2.1 Alineación de texto

Todo el texto de cuerpo, bajadas, notas y pies va **alineado a la izquierda**.
Nunca centrado ni a la derecha.

Excepciones únicas y cerradas:
- Etiquetas *dentro* de un diagrama, donde el centrado es parte de la figura
  (nodos del mapa de industria, nodo raíz de una bifurcación).
- Mockups que imitan una UI de terceros (WhatsApp), donde se replica el original.
- El sello de última actualización al pie del documento.

Un pie de figura no es parte de la figura: va a la izquierda.

### 2.2 Cajas en pares o columnas comparables

Cuando dos o más cajas van lado a lado y el lector las compara (Causa A / Causa B,
roadmap / opción elegida), **usan siempre la misma grilla**: mismo ancho de columna,
mismo gap, misma altura de caja.

- Deben ser **idénticas salvo por el color**: mismo padding, mismo radio, misma estructura
  interna, misma tipografía y jerarquía.
- Altura pareja por `stretch` (default de grid), nunca a mano. Si un lado tiene menos
  contenido, la nota de cierre se empuja abajo con `margin-top:auto` sobre una caja
  `display:flex;flex-direction:column`.
- Nada de `max-width` distinto entre dos filas que se comparan.

### 2.3 Diagramas de bifurcación (árbol)

Cuando un elemento arriba se ramifica en dos cajas abajo, y más abajo hay otro par de cajas
relacionadas (hoy: "Un síntoma, dos causas" → "La elección"), **todas las filas del árbol
comparten la misma definición de columnas**, para que Causa A caiga siempre sobre Causa A
y Causa B sobre Causa B, alineadas verticalmente.

Implementación: las variables del árbol están en `:root` y son la única fuente.

```css
--tree-cols: 1fr 1fr;   /* columnas de toda fila del árbol */
--tree-gap: 16px;       /* gap de toda fila del árbol */
--tree-max: 880px;      /* ancho total del árbol, incluida la raíz */
```

Cualquier fila del árbol (`.tree-row`) usa `grid-template-columns:var(--tree-cols)`,
`gap:var(--tree-gap)` y `max-width:var(--tree-max)`. La raíz y las líneas del SVG viven
dentro del mismo contenedor de `--tree-max` para quedar centradas sobre las mismas columnas.

Prohibido: darle a una fila su propio `grid-template-columns` o su propio `max-width`.
Si el árbol necesita otro ancho, se cambia la variable, no la fila.

### 2.4 Rótulos: eyebrows y labels (jerarquía y contraste)

Todos los rótulos en mayúsculas —eyebrow de sección (`01 · LA INDUSTRIA`), label de beat
(`EL SÍNTOMA`, `LA ELECCIÓN`), encabezado de columna, clave de fila, insignia de caja—
**heredan un único tratamiento**. Está definido en una sola regla agrupada en `index.html`,
justo después de `section`, y alimentado por tres tokens:

```css
--label-size:13px;      /* rótulo normal */
--label-size-sm:11.5px; /* insignia inline dentro de una caja */
--label-ls:.08em;       /* espaciado entre letras */
```

Reglas:

- **Contraste**: el color por defecto es `--ink-soft` (7.7:1 sobre `--bg`, AA holgado).
  Los rótulos que marcan el foco de su bloque van en `--accent` (5.0:1, AA).
  **Nunca `--ink-faint` en un rótulo**: da 2.9:1 sobre el fondo y desaparece proyectado.
- **Tamaño**: mínimo 13px (11.5px solo para insignias inline, que van dentro de una caja
  y se leen con su contexto al lado). Nada por debajo de eso.
- **Espaciado**: `.08em` es el techo. El espaciado alto se ve elegante en pantalla cercana
  y se vuelve ilegible proyectado; si un rótulo cuesta leerlo, se baja el `letter-spacing`
  antes que cualquier otra cosa. Mayúsculas + mono se mantienen porque a este tamaño
  y contraste ya no estorban.
- **Peso** 500 en mono, para que la mayúscula no se vea deshilachada.

Al agregar un rótulo nuevo: sumar su selector a la regla agrupada y dejar en su propia
regla solo lo posicional (margen, padding, borde). Nunca redefinir `font-size`, `color`,
`letter-spacing` ni `text-transform` por componente — si un rótulo necesita otro tamaño,
se discute el token, no se parcha el componente.

`--ink-faint` queda reservado para lo que no hay que leer: numeración auxiliar del nav,
líneas y símbolos de diagrama, separadores. Nunca para un rótulo ni para un título de caja.

---

## 3. Checklist antes de cerrar un cambio

1. ¿Usé solo tokens existentes?
2. ¿El verde está en un único elemento, y es el foco real de la sección?
3. ¿Todo el texto de cuerpo quedó a la izquierda?
4. ¿Las cajas que se comparan comparten grilla, padding y altura?
5. ¿Las filas del árbol usan `--tree-cols` / `--tree-gap` / `--tree-max`?
6. ¿Todo rótulo nuevo entró a la regla agrupada, sin tamaño ni color propios?
7. ¿La idea se dice una sola vez? (sin lead + nota + cierre repitiendo lo mismo)
