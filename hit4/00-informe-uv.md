## Normalización de coordenadas UV

**Debajo a manera de informe adjuntamos la respuesta de la IA que utilizamos como referecia para entender mejor qué función cumple uv, y por qué es importante normalizar el canvas antes de trabajar con funciones matemáticas.**

### Qué contiene `fragCoord`

`fragCoord` es un `vec2` que la GPU provee automáticamente. Sus valores son coordenadas en **píxeles absolutos** del canvas. Para un canvas de 800×450:

- Píxel inferior-izquierdo: `fragCoord = (0.5, 0.5)`
- Píxel inferior-derecho: `fragCoord = (799.5, 0.5)`
- Píxel superior-derecho: `fragCoord = (799.5, 449.5)`

El `0.5` de offset existe porque `fragCoord` apunta al **centro** de cada píxel, no a su esquina.

---

### Qué contiene `iResolution.xy`

`iResolution` es `vec3`. Sus componentes `.xy` son el ancho y alto del canvas en píxeles: `(800.0, 450.0)` en el ejemplo.

---

### Qué hace la división

```glsl
vec2 uv = fragCoord / iResolution.xy;
```

GLSL divide componente a componente:

```
uv.x = fragCoord.x / iResolution.x
uv.y = fragCoord.y / iResolution.y
```

Numéricamente para tres píxeles de ejemplo en un canvas 800×450:

| `fragCoord` | `uv` resultante |
|---|---|
| `(0.5, 0.5)` | `(0.000625, 0.00111)` ≈ `(0, 0)` |
| `(400.5, 225.5)` | `(0.500625, 0.50111)` ≈ `(0.5, 0.5)` |
| `(799.5, 449.5)` | `(0.999375, 0.99889)` ≈ `(1, 1)` |

El rango `[0.5, 799.5]` se comprime al rango `[0.000625, 0.999375]` — efectivamente `[0, 1]`.

---

### Por qué esto es "normalizar"

Normalizar significa mapear un rango arbitrario al rango `[0, 1]`. La fórmula general es:

```
valor_normalizado = (valor - mínimo) / (máximo - mínimo)
```

En este caso el mínimo es `0` (se ignora el offset de `0.5` por ser despreciable), entonces:

```
uv = (fragCoord - 0) / (iResolution - 0) = fragCoord / iResolution
```

La división hace exactamente eso: escala el rango `[0, resolución]` a `[0, 1]`.

---

### Consecuencia práctica

Sin normalizar, una lógica como `cos(fragCoord.x)` produce resultados distintos en un canvas de 800px que en uno de 1920px — el coseno recibe argumentos completamente distintos. Con `uv`, el argumento siempre está entre 0 y 1 independientemente de la resolución. El shader es **resolution-independent**.
