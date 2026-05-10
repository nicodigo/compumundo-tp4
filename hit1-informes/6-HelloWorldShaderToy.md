## Análisis del Shader "Hello World" de Shadertoy

---

### El código

```glsl
void mainImage( out vec4 fragColor, in vec2 fragCoord ) {
    vec2 uv = fragCoord/iResolution.xy;
    vec3 col = 0.5 + 0.5*cos(iTime+uv.xyx+vec3(0,2,4));
    fragColor = vec4(col,1.0);
}
```

---

### 1. Qué representa `uv`

`fragCoord` contiene la posición del píxel actual en **píxeles absolutos**: el píxel inferior-izquierdo es `(0.5, 0.5)` y el superior-derecho es `(resolución - 0.5)`. Depende del tamaño del canvas.

`uv` normaliza esas coordenadas dividiéndolas por `iResolution.xy`:

```glsl
vec2 uv = fragCoord / iResolution.xy;
```

El resultado es un vector donde cada componente va de `0.0` a `1.0`, independientemente de la resolución. `uv = (0,0)` es la esquina inferior-izquierda; `uv = (1,1)` es la esquina superior-derecha.

---

### 2. Por qué trabajar en UV y no en XY absoluto

Trabajar en píxeles absolutos acopla el shader a una resolución específica. Si el canvas mide 800×450 y se escribe una lógica basada en esos valores, el resultado cambia visualmente al cambiar el tamaño.

UV desacopla la lógica del shader de la resolución. Un valor `uv.x = 0.5` siempre significa "el centro horizontal", sin importar si el canvas mide 800 o 1920 píxeles. Además, normalizar permite usar funciones matemáticas como `sin`, `cos` o interpolaciones directamente sin escalar manualmente.

Variante común: centrar y corregir aspecto.

```glsl
vec2 uv = (fragCoord - 0.5 * iResolution.xy) / iResolution.y;
```

Aquí `uv = (0,0)` es el centro; el rango en X depende del aspect ratio.

---

### 3. Cómo se logra la animación con entradas estáticas

```glsl
vec3 col = 0.5 + 0.5*cos(iTime + uv.xyx + vec3(0,2,4));
```

`iResolution` y `fragCoord` son estáticos por frame, pero `iTime` es un `float` que la plataforma incrementa continuamente con el tiempo en segundos.

El fragment shader se ejecuta una vez por píxel por frame. En cada frame, `iTime` tiene un valor distinto, lo que desplaza el argumento del coseno. El coseno produce valores distintos → colores distintos → el resultado cambia frame a frame → animación.

La animación no requiere estado: cada frame se recalcula desde cero. No hay memoria entre frames; la ilusión de movimiento es puramente matemática.

---

### 4. Por qué `col` es `vec3` siendo una operación entre flotantes

```glsl
vec3 col = 0.5 + 0.5 * cos(iTime + uv.xyx + vec3(0,2,4));
```

GLSL define **operadores aritméticos escalares-vectoriales**: cuando un `float` opera con un `vecN`, el float se aplica componente a componente a todo el vector. Las reglas son:

- `0.5 * vec3(a,b,c)` → `vec3(0.5*a, 0.5*b, 0.5*c)`
- `0.5 + vec3(a,b,c)` → `vec3(0.5+a, 0.5+b, 0.5+c)`
- `cos(vec3(a,b,c))` → `vec3(cos(a), cos(b), cos(c))`

La mayoría de funciones matemáticas de GLSL (`sin`, `cos`, `abs`, `sqrt`, etc.) están sobrecargadas para operar sobre escalares y vectores de cualquier dimensión. El tipo resultante sigue al tipo de mayor dimensión en la expresión.

---

### 5. Constructores de `vec4`, swizzling y propiedades de los tipos vectoriales

#### Constructores de `vec4`

```glsl
vec4(float, float, float, float)  // 4 componentes individuales
vec4(vec3, float)                 // vec3 + 1 float
vec4(float, vec3)                 // 1 float + vec3
vec4(vec2, vec2)                  // dos vec2
vec4(vec2, float, float)          // vec2 + 2 floats
vec4(float)                       // repite el scalar en los 4 componentes
```

En el shader: `vec4(col, 1.0)` usa el constructor `vec4(vec3, float)`. `col` aporta RGB y `1.0` es el canal alpha.

#### Componentes de `fragColor`

`fragColor` es `vec4` con semántica de color:

| Componente | Acceso | Representa |
|---|---|---|
| x / r | `.x` o `.r` | Rojo |
| y / g | `.y` o `.g` | Verde |
| z / b | `.z` o `.b` | Azul |
| w / a | `.w` o `.a` | Alpha (opacidad) |

---

#### Swizzling: qué es `uv.xyx`

El **swizzling** es la capacidad de reordenar, repetir o seleccionar componentes de un vector usando sufijos de letra. `uv` es `vec2` con componentes `.x` e `.y`. Al escribir `uv.xyx` se construye un nuevo `vec3` tomando: componente x, componente y, componente x nuevamente.

```glsl
vec2 uv = vec2(0.3, 0.7);
uv.xyx  // → vec3(0.3, 0.7, 0.3)
```

Esto permite reutilizar datos de un vector de menor dimensión para alimentar una operación que requiere mayor dimensión, sin declarar variables intermedias. En el shader, se usa para calcular tres fases de coseno distintas (R, G, B) a partir de solo dos valores UV.

---

#### Propiedades de swizzling por tipo

GLSL define tres grupos de letras intercambiables según el contexto semántico:

| Grupo | Letras | Uso típico |
|---|---|---|
| Posición | `x y z w` | Coordenadas espaciales |
| Color | `r g b a` | Componentes de color |
| Textura | `s t p q` | Coordenadas de textura |

Los tres grupos son equivalentes: `.x == .r == .s` en el mismo vector. No se pueden mezclar grupos en un mismo swizzle (`.xg` es inválido).

**`vec2`** — componentes disponibles: `x/r/s`, `y/g/t`

```glsl
vec2 v = vec2(1.0, 2.0);
v.x      // float: 1.0
v.yx     // vec2: (2.0, 1.0)  — invertido
v.xxy    // vec3: (1.0, 1.0, 2.0)
v.xyxy   // vec4: (1.0, 2.0, 1.0, 2.0)
```

**`vec3`** — agrega `z/b/p`

```glsl
vec3 v = vec3(1.0, 2.0, 3.0);
v.z      // float: 3.0
v.zyx    // vec3: (3.0, 2.0, 1.0)  — invertido
v.xy     // vec2: (1.0, 2.0)  — reducción de dimensión
v.xyzz   // vec4: (1.0, 2.0, 3.0, 3.0)
```

**`vec4`** — agrega `w/a/q`

```glsl
vec4 v = vec4(1.0, 2.0, 3.0, 4.0);
v.w      // float: 4.0
v.rgb    // vec3: (1.0, 2.0, 3.0)  — descarta alpha
v.ar     // vec2: (4.0, 1.0)  — alpha y rojo
v.wzyx   // vec4: (4.0, 3.0, 2.0, 1.0)  — invertido completo
```

El swizzle puede usarse también como **l-value** (lado izquierdo de asignación):

```glsl
vec4 v = vec4(0.0);
v.rgb = vec3(1.0, 0.5, 0.2);  // asigna tres componentes a la vez
v.xy = v.yx;                   // intercambia x e y
```

Restricción: en una asignación, cada componente puede aparecer solo una vez en el lado izquierdo (`.xx = ...` es inválido). En el lado derecho, puede repetirse libremente.
