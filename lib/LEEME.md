# Bibliotecas locales del Compendio ORV

Copias locales de las bibliotecas que antes se descargaban de internet, para que las aplicaciones funcionen sin conexión (aula, laboratorio).

| Archivo | Versión | Fuente original | Licencia |
|---|---|---|---|
| `plotly-2.27.0.min.js` | 2.27.0 | cdnjs / npm `plotly.js-dist-min` | MIT |
| `chart-4.4.1.umd.min.js` | 4.4.1 | cdnjs / npm `chart.js` | MIT |
| `mathjax-3.2.2/` | 3.2.2 | cdnjs / npm `mathjax` (componente `tex-mml-chtml` y sus fuentes) | Apache 2.0 |
| `fuentes/` | Fontsource 5 | Google Fonts: IBM Plex Sans y Mono, Roboto, Roboto Mono, Nunito, Source Code Pro | SIL OFL 1.1 |

Cada aplicación carga primero la copia local (`../lib/…`). Si la carpeta `lib/` no está —por ejemplo, si se sube a Moodle un archivo HTML suelto— la aplicación recurre automáticamente a la versión de internet de siempre.

No cambie estas versiones sin ejecutar después `npm test`.
