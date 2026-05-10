
---

## Pipeline de Renderizado en WebGL (adición al informe)

### Estructura del pipeline

La GPU opera en dos partes fundamentales: la primera procesa vértices (o streams de datos) en vértices del clip space; la segunda dibuja píxeles basándose en el resultado de la primera.

#### Etapa 1 — Vertex Shader

El vertex shader es una función escrita en GLSL que se ejecuta una vez por cada vértice. Realiza operaciones matemáticas y escribe el resultado en la variable especial `gl_Position` con un valor en clip space. La GPU toma ese valor y lo almacena internamente.

Esto corresponde directamente al **Vertex Shader** descrito en el artículo de Wikipedia: transformación de coordenadas 3D a 2D de pantalla.

#### Etapa 2 — Rasterización

Asumiendo que se dibujan `TRIANGLES`, cada vez que la primera etapa genera 3 vértices la GPU los usa para construir un triángulo. Determina qué píxeles corresponden a los 3 puntos del triángulo y lo rasteriza.

#### Etapa 3 — Fragment Shader

Para cada píxel, la GPU ejecuta el fragment shader preguntando qué color asignar. El fragment shader debe escribir el resultado en la variable especial `gl_FragColor`.

Esto es el **Pixel/Fragment Shader** del artículo anterior operando al final del pipeline.

#### Varyings — Puente entre etapas

WebGL toma los valores computados para cada vértice y, mientras rasteriza el triángulo, interpola entre los valores calculados en los vértices. Para cada píxel llama al fragment shader con el valor interpolado.

---

### Relación con Pixel Shaders 2D

En WebGL, renderizar 2D no elimina el pipeline: se siguen ejecutando vertex shader y fragment shader. La diferencia es que la geometría es un quad (dos triángulos) que cubre el área de pantalla deseada, y todo el trabajo visual recae en el fragment shader — exactamente el caso descrito en el artículo de Wikipedia donde el pixel shader opera sobre sprites, texturas o la pantalla completa como textura de entrada.
