# Rama: Oclusión ambiental (AO)

Esta rama agrega la técnica de **oclusión ambiental** al renderizador del proyecto (`index.html`). Aquí se explica qué se hizo, cómo funciona y cómo probarlo.

## Qué se hizo

- Función `aoAt(...)`: calcula el factor de oclusión de un punto lanzando rayos en su hemisferio.
- Función `renderAO(...)`: renderiza la escena completa usando ese factor (sección `OCLUSIÓN AMBIENTAL` de `index.html`).
- Controles en la interfaz, que solo afectan a esta técnica:
  - **Muestras AO**: deslizador de 1 a 256 rayos por punto (por defecto 16).
  - **Radio AO**: deslizador de 0.2 a 4 (por defecto 1.5).
  - **Ver solo AO (grises)**: casilla para ver únicamente el factor de oclusión.
- **Estudio de muestras AO**: botón que renderiza con 1, 4, 16 y 64 muestras y compara contra una referencia de 256.
- La opción «Oclusión ambiental» en el selector de técnica, y su fila en el historial de mediciones y en la comparación de las tres técnicas.

## Cómo funciona

Para cada píxel:

1. Se lanza un rayo desde la cámara y se busca el objeto más cercano. Si no choca con nada, se pinta el cielo (o blanco en el modo «solo AO»).
2. En el punto de impacto `P` con normal `N` se construye una base ortonormal (tangente, bitangente, normal).
3. Se generan `N` direcciones en el hemisferio sobre `P` con **distribución coseno**: `x = √u1·cos(2πu2)`, `y = √u1·sin(2πu2)`, `z = √(1−u1)`. Así las direcciones cercanas a la normal pesan más, como en la iluminación difusa.
4. Cada dirección se lanza como rayo de sombra con distancia máxima igual al **radio**. Se cuenta cuántos chocan con algo.
5. El resultado es `AO = 1 − choques / N` (1 = totalmente abierto, 0 = totalmente tapado).
6. El color final es `albedo × (0.72·AO + 0.45·ndl·sombra)`, donde `ndl` es el término difuso de la luz direccional y `sombra` es 0 o 1 según un rayo hacia la luz. El albedo es el color base del objeto.

Detalles que conviene saber:

- Los rayos de oclusión reutilizan `occluded(...)`, la misma función que usa el ray tracing para las sombras.
- El origen de cada rayo se desplaza una pequeña distancia sobre la normal para evitar auto-oclusión.
- El generador aleatorio es determinista (`seed = 12345` en cada render), así que dos renders con los mismos parámetros dan la misma imagen.

## Qué esperar al probarlo

- **Pocas muestras (1–4):** imagen con mucho ruido granulado.
- **Muchas muestras (64–256):** imagen suave, pero tarda proporcionalmente más (el tiempo crece de forma lineal con N).
- **Radio pequeño:** solo se oscurecen los rincones muy cercanos a otros objetos.
- **Radio grande:** el oscurecimiento se extiende más, por ejemplo bajo el toldo y las mesas.

## Cómo probarlo

1. Abre `index.html` en el navegador.
2. Elige **Oclusión ambiental** en el selector de técnica.
3. Ajusta muestras y radio, y pulsa **Renderizar**.
4. Marca «Ver solo AO» para ver el factor sin color.
5. Pulsa **Estudio de muestras AO** para ver la relación entre ruido y tiempo.

Recomendación: usar 320×240 y 16 muestras o menos mientras se hacen pruebas.

## Resultados de una prueba

Medidos ejecutando el código en Node (sin navegador), a 480×360 con 32 muestras y radio 1.5:

| Dato | Valor |
|---|---|
| Tiempo | ≈ 45 s |
| Rayos lanzados | ≈ 4.47 millones |

Los tiempos en tu navegador pueden ser distintos. Para los números oficiales de la entrega, usa **Comparar las 3 técnicas** en tu equipo y copia la tabla.

## Limitaciones conocidas

- Cada rayo prueba todos los objetos de la escena (no hay estructura de aceleración), así que la AO es la técnica más lenta.
- No hay reducción de ruido; la calidad depende solo del número de muestras.
- La AO solo oscurece; no aporta sombras duras ni reflejos (eso lo hace el ray tracing).

## Archivos tocados

- `index.html`: sección `OCLUSIÓN AMBIENTAL`, controles de AO y botón del estudio.
- `README_AO.md`: este archivo.
