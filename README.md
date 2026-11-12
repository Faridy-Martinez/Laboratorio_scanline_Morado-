# Laboratorio_scanline_Morado-
Implementar en equipo, en HTML/Canvas, tres técnicas de render sobre la misma escena sencilla del juego Dress &amp; Magic  (scanline, ray tracing y oclusión ambiental) y comparar su calidad y su tiempo de render.
[Medición y comparación de técnicas de render.md](https://github.com/user-attachments/files/34772914/Medicion.y.comparacion.de.tecnicas.de.render.md)
# Medición y comparación de técnicas de render

Escena de pastelería (esferas, cubos y un plano en rosa, lila, turquesa y crema) renderizada con tres técnicas desde `index.html`: scanline, ray tracing y oclusión ambiental. Todas usan la misma cámara, la misma luz y las mismas resoluciones.

## Cómo se mide

El tiempo de cada técnica se toma con `performance.now()` justo antes y después de la función de render. No incluye el volcado de la imagen al canvas. La página guarda la última medición de cada combinación de técnica y resolución en una tabla en pantalla.

1. Abre `index.html` en el navegador.
2. Elige técnica y resolución (320×200 o 640×400). En oclusión ambiental elige también las muestras por píxel.
3. Pulsa **Renderizar** para una medición, **Medir las 3** para comparar las técnicas a la misma resolución, o **Barrido completo** para llenar toda la tabla.
4. Abre "Tabla en Markdown para el README", pulsa **Copiar tabla** y pégala en la sección siguiente.

Equipo y navegador usados: *completar*

## Tiempos de render

Scanline fue la técnica más rápida en ambas resoluciones: 7.0 ms a 320×200 y 21.2 ms a 640×400.

| Técnica | Resolución | Muestras AO | Tiempo (ms) |
| --- | --- | --- | --- |
| Scanline | 320×200 | - | 7.0 |
| Scanline | 640×400 | - | 21.2 |
| Ray tracing (sombras) | 320×200 | - | 16.21 |
| Ray tracing (sombras) | 640×400 | - | 43.7 |
| Ray tracing + AO (sombras) | 320×200 | 16 | 60.33 |
| Ray tracing + AO | 320×200 | 64 | 172.95 |
| Ray tracing + AO (sombras) | 640×400 | 16 | 194.65 |
| Ray tracing + AO (sombras) | 640×400 | 32 | 338.8 |
| Ray tracing + AO | 640×400 | 64 | 600.24 |

## Comparación de calidad

| Rasgo | Scanline | Ray tracing | Oclusión ambiental |
| --- | --- | --- | --- |
| Sombras proyectadas | No | Sí, bordes duros | No, solo oscurece rincones |
| Contacto con el suelo | Los objetos parecen flotar | Sombra bajo la luz directa | Oscurecimiento suave donde se tocan |
| Reflejos | No | Sí, un rebote en esferas y suelo | No |
| Ruido | No | No | Sí, baja al subir las muestras |
| Bordes | Escalonados | Escalonados | Escalonados |

## Cómo crece el coste

- **Scanline:** depende de cuántos triángulos hay y de cuántos píxeles cubren. No lanza rayos.
- **Ray tracing:** un rayo primario por píxel más un rayo de sombra y hasta dos rebotes de reflejo. El tiempo crece con el número de píxeles y de objetos.
- **Oclusión ambiental:** un rayo primario más tantos rayos como muestras por píxel. Duplicar las muestras duplica casi el tiempo.

## Conclusiones

De acuerdo con los resultados obtenidos, Scanline fue la técnica que presentó el menor tiempo de renderizado, por lo que ofrece un mejor rendimiento. En cambio, Ray tracing combinado con oclusión ambiental y 64 muestras registró los tiempos más elevados.

También se observó que al aumentar la resolución y la cantidad de muestras de AO, el tiempo de procesamiento incrementa considerablemente. Esto demuestra que, aunque la oclusión ambiental permite obtener una iluminación con mayor profundidad visual, requiere más recursos de procesamiento.

<img width="1258" height="875" alt="Captura de pantalla 2026-11-12 170818" src="https://github.com/user-attachments/assets/ddd488d3-db79-4f29-9fed-3334a4d2ac50" />
<img width="1283" height="887" alt="Captura de pantalla 2026-11-12 170743" src="https://github.com/user-attachments/assets/a35dfd6e-b991-49be-84f1-42f29b568163" />
<img width="1330" height="897" alt="Captura de pantalla 2026-11-12 171037" src="https://github.com/user-attachments/assets/a51acd0e-db36-45d1-b688-3814d4feca2a" />
<img width="1327" height="340" alt="Captura de pantalla 2026-11-12 171058" src="https://github.com/user-attachments/assets/b3ef0cec-e589-4d01-bf09-ec6bea99e4eb" />
