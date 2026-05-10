
---

## División del Pipeline: 3D vs 2D

| Etapa | Dominio | Descripción |
|---|---|---|
| **1. Vertex Shader** | 3D | Transforma posiciones 3D a clip space |
| **2. Rasterización** | 3D → 2D | Convierte primitivas geométricas a fragmentos de píxel |
| **3. Fragment Shader** | 2D | Calcula el color de cada píxel rasterizado |
| **4. Z-buffer / Depth Test** | 3D | Determina visibilidad por profundidad |
| **5. Render to Texture** | 2D | Escribe el frame renderizado a un buffer en memoria |
| **6. Post-processing** | 2D | Aplica efectos sobre el buffer ya rasterizado |

La división ocurre en la rasterización: todo lo anterior es dominio 3D; todo lo posterior opera sobre una imagen 2D plana.

---

## Post-processing

### Definición

El post-processing es el proceso de modificar la calidad percibida de un video durante la reproducción, ejecutado después del proceso de decodificación. En renderizado 3D en tiempo real —como videojuegos— la escena se renderiza primero a un buffer en la memoria de la tarjeta de video; luego pixel shaders (y opcionalmente vertex shaders) aplican filtros de post-processing al buffer de imagen antes de mostrarlo en pantalla.

### Etapa del pipeline

El post-processing se ejecuta en la **Etapa 6**, después del fragment shader y del render-to-texture. Opera enteramente en 2D: el input es el frame completo como textura, el output es el frame modificado. Esto conecta directamente con lo descrito en el artículo de Wikipedia sobre shaders: el pixel shader como único tipo capaz de actuar como filtro sobre un stream de video ya rasterizado.

### Efectos representativos

El post-processing permite efectos que requieren conciencia de la imagen completa —ya que normalmente cada objeto 3D se renderiza de forma aislada. Entre ellos: ambient occlusion (SSAO), anti-aliasing (FXAA, SMAA), bloom, blur (depth of field, motion blur), color grading, chromatic aberration, vignette, HDR tone mapping y lens flare.
