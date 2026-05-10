## Salidas del Pixel Shader en Shadertoy

Las salidas varían según el tipo de shader. Shadertoy define tres modos de ejecución con firmas distintas.

### Shader de imagen (modo estándar)

Firma: `void mainImage( out vec4 fragColor, in vec2 fragCoord )`

| Tipo | Nombre | Descripción |
|---|---|---|
| `vec4` (out) | `fragColor` | Color de salida del píxel en formato RGBA. Rango `[0.0, 1.0]` por componente. El canal alpha es ignorado por el cliente en este momento, aunque se recomienda dejarlo en 1.0. |
| `vec2` (in) | `fragCoord` | No es salida sino entrada implícita de la firma: coordenadas del píxel actual en pantalla, en píxeles, desde `0.5` hasta `resolución-0.5`. |

### Shader de sonido

Firma: `vec2 mainSound( float time )`

| Tipo | Nombre | Descripción |
|---|---|---|
| `vec2` (return) | *(valor de retorno)* | Amplitud de onda deseada devuelta como par de valores para audio estéreo (canal izquierdo y derecho). |

### Shader VR

Firma: `void mainVR( out vec4 fragColor, in vec2 fragCoord, in vec3 fragRayOri, in vec3 fragRayDir )`

| Tipo | Nombre | Descripción |
|---|---|---|
| `vec4` (out) | `fragColor` | Color de salida del píxel, igual que en el shader de imagen. |
| `vec3` (in) | `fragRayOri` | Origen del rayo que atraviesa el píxel en el espacio del tracker. |
| `vec3` (in) | `fragRayDir` | Dirección del rayo que atraviesa el píxel en el mundo virtual. El shader debe transformar estos valores al espacio de la cámara si existe una cámara móvil en la escena. |

### Observación

En los tres modos la única salida real del fragment shader es el color del píxel (`fragColor` o el valor de retorno en sound). Los parámetros `fragRayOri` y `fragRayDir` en VR son entradas adicionales, no salidas. Shadertoy no expone `gl_FragDepth`; la profundidad no es escribible desde el editor.
