
---

## Documentación: "Learn to Paint with Mathematics" — Inigo Quilez

Video introductorio de la serie. Quilez construye en vivo una palmera al atardecer en Shadertoy, partiendo de una pantalla negra hasta una escena completa, usando exclusivamente código GLSL.

### Proceso demostrado

1. **Color plano** → asignar RGB constante a todos los píxeles.
2. **Gradiente** → modular el color con las coordenadas UV del píxel (`.x`, `.y`).
3. **Distancia** → calcular distancia de cada píxel a un punto central para crear degradados radiales.
4. **Círculo / estrella** → `smoothstep` sobre la distancia; `cos(atan(...))` agrega picos al radio.
5. **Tronco** → `smoothstep` sobre distancia horizontal + `abs` para simetría.
6. **Borrado** → operador "over": selección matemática que suprime regiones no deseadas.
7. **Curvatura** → onda sinusoidal deforma el eje vertical del tronco.
8. **Detalle de silueta** → coseno de alta frecuencia sobre el borde del tronco.
9. **Conexión al suelo** → exponencial que suaviza la base.
10. **Cielo atardecer** → gradiente vertical naranja→amarillo con `sqrt(p.y)` para empujar el naranja hacia abajo.

### Toolbox explícito mencionado por Quilez

| Función | Uso artístico |
|---|---|
| `smoothstep` | Umbrales y transiciones suaves |
| `cos` / `sin` | Variación periódica, picos, curvatura |
| `atan` | Posición angular alrededor de un punto |
| `abs` | Simetría |
| `exp` | Curvas de atenuación/conexión |
| `sqrt` | Redistribución no lineal de un rango |
| `mix` | Mezcla de colores |
| `length` | Distancia euclidiana entre píxeles |

### Conclusiones (actualizadas)

**Complejidad visual = complejidad de código.** Una línea produce color plano; 16 líneas producen una escena. La relación es directa y acumulativa.

**El toolbox es pequeño.** Quilez menciona ~20 funciones como vocabulario completo. Con ese conjunto acotado se construye cualquier imagen, sin importar la complejidad final.

**El método es experimental, no deductivo.** Añadir `x` al argumento del coseno sin saber el resultado exacto es parte del proceso. La exploración aleatoria es una herramienta legítima.

**Cada píxel es independiente.** No hay estado, no hay comunicación entre píxeles. La imagen completa emerge de la misma función evaluada un millón de veces en paralelo — uno por píxel.
