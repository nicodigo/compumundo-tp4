---

## Informe: Tipos de Shaders y Pixel Shaders en 2D

### Definición general

Un shader es una operación programable que se aplica a los datos mientras atraviesan el pipeline de renderizado. Pueden actuar sobre vértices y primitivas, generar o transformar geometrías y fragmentos, y calcular valores en una imagen renderizada.

---

### Tipos de Shaders

**Pixel / Fragment Shaders**
Calculan el color y otros atributos de cada fragmento, una unidad de trabajo de renderizado que afecta como máximo a un píxel de salida.

**Vertex Shaders**
Se ejecutan una vez por cada vértice 3D. Su propósito es transformar la posición 3D de cada vértice en el espacio virtual a la coordenada 2D en la que aparece en pantalla, junto con un valor de profundidad para el Z-buffer.

**Geometry Shaders**
Pueden generar nuevas primitivas gráficas —puntos, líneas, triángulos— a partir de las primitivas enviadas al inicio del pipeline. Se ejecutan después de los vertex shaders y reciben como entrada una primitiva completa.

**Tessellation Shaders**
Añaden dos etapas al modelo tradicional: tessellation control shaders (hull shaders) y tessellation evaluation shaders (domain shaders), que permiten subdividir mallas simples en mallas más finas en tiempo de ejecución según una función matemática.

**Compute Shaders**
Rutinas compiladas para aceleradores de alto rendimiento como GPUs, DSPs o FPGAs, ejecutadas separadamente pero usadas por el programa principal. Comparten unidades de ejecución con vertex y pixel shaders.

**Mesh / Task Shaders**
Introducidos por Nvidia con la microarquitectura Turing en 2018, modelados sobre compute shaders. Permiten al GPU manejar algoritmos más complejos, desplazando trabajo de la CPU al GPU.

**Tensor Shaders**
Programas de acelerador que operan sobre matrices en grandes arrays, tipo de operación común en redes neuronales. Pueden ejecutarse en NPUs o GPUs.

---

### Pixel Shaders operando en 2D

Los pixel shaders pueden aplicarse en etapas intermedias a imágenes 2D —sprites o texturas— en el pipeline, mientras que los vertex shaders siempre requieren una escena 3D.

Usos 2D documentados:

- **Postprocesado de pantalla completa**: el pixel shader conoce las coordenadas de pantalla que se están dibujando y puede samplear la pantalla y los píxeles adyacentes si el contenido completo de la pantalla se pasa como textura al shader. Esto habilita efectos de postprocesado 2D como blur o detección/mejora de bordes para cel shaders.

- **Filtro de video**: el pixel shader es el único tipo de shader que puede actuar como postprocesador o filtro para un stream de video después de que ha sido rasterizado.

- **Sprites y texturas**: aplicación directa sobre assets 2D sin necesidad de geometría 3D subyacente.
