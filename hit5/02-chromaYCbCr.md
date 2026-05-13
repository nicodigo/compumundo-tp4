## Hit 5 - Chroma rgb


```glsl
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = fragCoord/iResolution.xy;
    vec4 tx0 = texture(iChannel0, uv);
    vec4 tx1 = texture(iChannel1, uv);


    // Transformación a YCbCr
    vec3 rgb = tx1.rgb;

    float Y  =  0.299 * rgb.r + 0.587 * rgb.g + 0.114 * rgb.b;
    float Cb = -0.169 * rgb.r - 0.331 * rgb.g + 0.500 * rgb.b;
    float Cr =  0.500 * rgb.r - 0.419 * rgb.g - 0.081 * rgb.b;

    // Chroma en CbCr
    vec3 ch = vec3(40.0, 145.0, 40.0) / 255.0;
    float c_Y  =  0.299 * ch.r + 0.587 * ch.g + 0.114 * ch.b;
    float c_Cb = -0.169 * ch.r - 0.331 * ch.g + 0.500 * ch.b;
    float c_Cr =  0.500 * ch.r - 0.419 * ch.g - 0.081 * ch.b;   

    // obtengo distancia en el plano del sujeto al chroma
    float dist = sqrt(pow((Cb - c_Cb), 2.0) + pow((Cr - c_Cr), 2.0));

    // suavizo la transición entre sujeto y fondo
    float r0 = 0.07;
    float r1 = 0.2;
    float alpha = smoothstep(r0, r1, dist);

    // -- Spill suppression -- //
    // Componente del color en la dirección del fondo
    float spillAmount = max(0.0, dot(vec2(Cb, Cr), normalize(vec2(c_Cb, c_Cr))));

    // Solo corregir proporcionalmente a cuánto borde es el píxel
    float correction = spillAmount * (1.0 - alpha);

    // Restar la componente verde del color original
    tx1.g -= correction;

    vec3 resultado = mix(tx0.rgb, tx1.rgb, alpha);
    fragColor = vec4(resultado, 1.0);
}
```

### Chroma en YCbCr
![Chroma](./imgs/chroma_YCbCr.png)
