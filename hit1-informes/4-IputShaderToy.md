## Entradas del Shader en Shadertoy

Variables `uniform` inyectadas automáticamente por la plataforma en cada frame, sin declaración explícita requerida.

### Entradas generales (visibles en "Shader Inputs")

| Tipo | Nombre | Descripción |
|---|---|---|
| `vec3` | `iResolution` | Resolución del viewport en píxeles (x, y, z=pixel aspect ratio) |
| `float` | `iTime` | Tiempo de reproducción del shader en segundos |
| `float` | `iTimeDelta` | Tiempo de renderizado del último frame en segundos |
| `float` | `iFrameRate` | Frames por segundo actuales |
| `int` | `iFrame` | Número de frame actual desde el inicio |
| `vec3[4]` | `iChannelResolution` | Resolución en píxeles de cada canal de entrada |
| `vec4` | `iMouse` | Coordenadas del mouse: `.xy` posición actual (si LMB presionado), `.zw` posición del último click |
| `samplerXX` | `iChannel0..3` | Canales de entrada de textura (2D o Cubemap); admiten imagen, video, audio o buffer |

### Entradas condicionales (no visibles en la UI)

Existen en el entorno GLSL y son accesibles en código, pero Shadertoy no las expone en "Shader Inputs" porque su utilidad depende del tipo de shader o del contenido asignado a los canales.

| Tipo | Nombre | Condición | Descripción |
|---|---|---|---|
| `float[4]` | `iChannelTime` | Canal con video o audio activo | Tiempo de reproducción individual por cada canal; vale 0 con canales vacíos o imágenes estáticas |
| `vec4` | `iDate` | Siempre disponible | Fecha y hora del sistema: (año, mes, día, segundos desde medianoche); algunos navegadores con configuración de privacidad estricta pueden limitarla |
| `float` | `iSampleRate` | Shader de tipo Sound (`mainSound()`) | Frecuencia de muestreo de audio, típicamente 44100 Hz; sin utilidad práctica en shaders de imagen estándar |

### Observación de integración con el informe

Todas estas entradas operan exclusivamente en el **Fragment Shader** (Etapa 3 del pipeline). Shadertoy elimina las etapas 1 y 2 al renderizar siempre sobre un quad pantalla-completa fijo; el vertex shader es trivial y constante. Todo el trabajo creativo y de post-processing ocurre en `mainImage()`, recibiendo estas variables como contexto de ejecución por píxel.
