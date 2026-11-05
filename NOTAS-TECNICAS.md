> **Notas técnicas del Compendio ORV.** Registro de la revisión técnica de las aplicaciones (validación, errores corregidos, fuentes de datos y decisiones del autor). Las rutas, carpetas y comandos que se mencionan se refieren al repositorio de trabajo del autor.

# Correcciones al Compendio ORV · 18 de octubre de 2026

Revisión técnica asistida por Claude. **Validada por el autor, Oscar Robles, el 18 de octubre de 2026.**

Esta carpeta contiene las **38 aplicaciones** del compendio (41 originales menos 3 copias repetidas, sección 3) y el índice `index.html`, con el mismo nombre y en la misma subcarpeta que en `00 Compendio ORV`. También reúne, en `Copias repetidas corregidas/`, las tres copias repetidas ya corregidas, y en `Respaldo/`, el índice anterior. La carpeta se llamó originalmente `00 Compendio ORV - Corregido 2026-10-18`; el autor la renombró `00 Compendio ORV Verificado`. Todas recibieron la capa de adaptación a celular y accesibilidad (sección 2), y 21 de ellas además tienen correcciones de cálculo o de datos (sección 1). Los originales no se tocaron. Cada archivo corregido incluye al inicio, dentro de `<head>`, un comentario `[Corrección 2026-10-18]` que resume lo que cambió.

## Cómo se verificó

- Cada tabla interna se comparó, valor por valor, con las formulaciones de referencia vigentes, calculadas con CoolProp:
  - agua: IAPWS-95;
  - R-134a: Tillner-Roth y Baehr, con la referencia ASHRAE, que es la misma que usa Moran;
  - aire húmedo: ASHRAE / Hyland–Wexler.
- Los cálculos de cada aplicación se ejecutaron en un navegador automático sobre rejillas de cientos de estados, antes y después de la corrección.
- Las 41 aplicaciones (incluidas las 3 copias retiradas después) cargan sin errores de JavaScript, tanto en escritorio como en celular. En escritorio, su aspecto no cambia, salvo donde cambiaron los datos o donde se oscurecieron colores de texto por contraste.
- La accesibilidad se midió con axe-core (criterios WCAG 2.1 A y AA) en las 41 aplicaciones, antes y después de los cambios.

---

## 1. Errores que producían resultados incorrectos

### `02 Sustancias puras/SteamTables_ORV.html`

| Problema | Antes | Después |
|---|---|---|
| Fuera de la tabla, devolvía la última fila sin avisar | 0.35 bar y 700 °C → h = 3488.8 kJ/kg (el valor de 500 °C) | Muestra un aviso con la temperatura máxima tabulada |
| v interpolado linealmente en P entre bloques (v ∝ 1/P) | Error de v de hasta 101 %; por ejemplo, a 0.15 bar y 400 °C daba 38.46 m³/kg en lugar de 20.71 | Error máximo de 1.9 % |
| s interpolada linealmente en P (s ∝ −R ln P) | Error máximo de 2.5 % | Error máximo de 0.5 % |
| Cerca de la saturación se tomaba la fila "Sat" del bloque superior | — | Se interpola a igual grado de sobrecalentamiento |
| Líquido comprimido con T > Tsat del bloque inferior | 40 bar y 240 °C → h = 1008.7 kJ/kg | h = 1038.3 kJ/kg (referencia: 1037.6) |
| Búsquedas inversas con T o P conocida (regla de tres lineal) | — | Bisección sobre la interpolación corregida |

Los valores en los nodos de las tablas de Moran & Shapiro **no cambian**.

### `02 Sustancias puras/R134aTables_ORV.html`

Se aplicaron las mismas correcciones de interpolación que en la tabla de vapor:

- Fuera de la tabla ahora aparece un aviso. Antes, por ejemplo, 8 bar y 150 °C devolvía los datos de 140 °C.

**Cambio de fuente (revisión 3, a solicitud del autor):** las Tablas A-10, A-11 y A-12 se recalcularon con la formulación actual de Tillner-Roth y Baehr (CoolProp), con referencia ASHRAE (h = s = 0 en líquido saturado a −40 °C). Se conservó la misma rejilla de temperaturas y presiones.

- Antes había diferencias de hasta 3 kJ/kg en h y de 0.013 kJ/kg·K en s respecto de la formulación actual, porque la tabla del libro proviene de una formulación anterior.
- Ahora los valores tabulados coinciden con la referencia (desviación mediana de 0.001 %).
- Los valores interpolados tienen un error máximo de 0.3 % en v y de 0.13 % en h; antes era de 7.3 % en v y de 1.6 % en h.
- La tabla y los nueve diagramas del R-134a usan ahora la misma fuente.
- **Para el curso:** los valores ya no coinciden dígito a dígito con las Tablas A-10 a A-12 de Moran; la diferencia es de 1 % como máximo. Sí coinciden, prácticamente, con las Tablas A-11 a A-13 de Çengel, que usan la misma referencia.

### `08 Psicrometria/Carta_Psicrometrica_ORV.html`

- El punto de rocío no bajaba de 0.01 °C. Ahora, por debajo de 0 °C, se usa la presión de saturación sobre hielo (Hyland–Wexler / ASHRAE). Ejemplo a 25 °C y φ = 15 %:
  - antes: 0.01 °C;
  - ahora: −3.0 °C, que es el punto de escarcha y coincide con CoolProp. Sobre agua subenfriada el valor sería −3.4 °C.
- El bulbo húmedo por debajo de 0 °C ahora usa la ecuación ASHRAE para superficie de hielo. El error bajó de 0.46 K a 0.03 K.
- No hay cambios para estados con punto de rocío y bulbo húmedo iguales o mayores que 0 °C.

### `07 Ciclos de potencia y refrigeracion/Ciclo Rankine_ORV.html` (antes también `Rankine_ORV.html`, copia idéntica ya retirada)

Los dos archivos eran idénticos y siguen siéndolo.

**Tabla A-4 interna con valores erróneos**, por ejemplo:

| Estado | Valor erróneo | Valor correcto |
|---|---|---|
| 80 bar y 600 °C | v = 0.05521 m³/kg | 0.04845 m³/kg |
| 160 bar y 360 °C | h = 2617 kJ/kg | 2715.8 kJ/kg |

Se sustituyó por dos fuentes:
- de 0.06 a 140 bar, la Tabla A-4 de Moran & Shapiro, la misma de `SteamTables_ORV`, que ya estaba validada;
- 160, 180 y 200 bar, valores IAPWS-95 redondeados a la precisión de la tabla.

La versión en unidades inglesas se regeneró por conversión exacta.

**Cambios en el motor de cálculo:**
- Cada isobara empieza ahora en el vapor saturado. Antes, entre Tsat y la primera fila tabulada, el motor extrapolaba.
- La interpolación en T es cúbica monótona (PCHIP) y pasa exactamente por los valores tabulados.
- v se interpola como ln v contra ln P.

**Bomba real:** ahora T₂ y s₂ incluyen la disipación. Antes se suponía s₂ = s₁.

**Ciclo no válido:** si la temperatura de entrada a la turbina es menor o igual que Tsat(P), la aplicación lo advierte y no reporta eficiencia. Antes, con 100 bar y 250 °C, calculaba η = 23.9 % con líquido entrando a la turbina.

**Resultado sobre 360 casos** (ciclo simple, con recalentamiento y con regeneración):

| Indicador | Antes | Después |
|---|---|---|
| Error máximo en η | 2.7 % | 0.07 % |
| Error máximo en la calidad de salida | 0.017 | 0.0013 |

Los resultados en SI y en unidades inglesas coinciden entre sí.

### Diagramas del R-134a (9 archivos `Diagrama_*_R134a_ORV.html`)

**Problema:**
- Las tablas internas en unidades inglesas tenían errores de hasta 29 % en v y de hasta 38 kJ/kg en h.
- Los bloques SI por encima de 16 bar, que no existen en la Tabla A-12 de Moran, tenían errores de hasta 11 % en v y 15 kJ/kg en u.

**Corrección (revisión 2, a partir del diagrama de Mollier):**
- En la primera versión se conservaron los bloques SI hasta 16 bar (Moran) y se recalcularon los demás. La Tabla A-12 de Moran proviene de una formulación anterior que difiere hasta en 3 kJ/kg en h y 0.013 kJ/kg·K en s. Al unir ambas fuentes, la curva de vapor saturado mostraba un quiebre entre 16 y 18 bar: en el diagrama h-s, la curva retrocedía en s.
- Ahora todas las tablas de los 9 diagramas, en SI y en unidades inglesas, provienen de una sola formulación: Tillner-Roth y Baehr (CoolProp), con referencia ASHRAE (h = s = 0 en líquido saturado a −40 °C). Se conservó la rejilla de presiones y temperaturas.
- Con esto, la curva de vapor saturado del R-134a es continua. Tiene el máximo físico de hg ≈ 281 kJ/kg cerca de 24 bar y es casi vertical a baja presión, como corresponde a este refrigerante.
- **Diagrama h-s (Mollier):** se añadió la línea de líquido saturado. Las isobaras se trazan desde x = 0, de modo que sin ella la cúpula quedaba abierta.

**Nota:** desde la revisión 3, `R134aTables_ORV` usa la misma formulación que los diagramas.

### `02 Sustancias puras/Diagrama_P-v_ORV.html` y `Diagrama_T-v_ORV.html`

- Agua, unidades inglesas, 100 psia y 1600 °F: v = 12.584 → 12.258 ft³/lbm.

### `02 Sustancias puras/Z vs P Vapor de Agua (saturacion)_ORV.html` y `Suite Ecuaciones de Estado_ORV.html`

- Las filas de 310, 330 y 350 °C de la curva de saturación tenían Psat y Z erróneos. Por ejemplo, a 310 °C decía 101.372 bar en lugar de 98.651 bar. Se recalcularon con IAPWS-95.

### `04 Primera ley y volumen de control/Vol control ciclo termodinamico_ORV.html`

**Tabla inglesa de vapor sobrecalentado:** estaba corrupta.
- Solo tenía 12 bloques, y varios mezclaban filas de dos o tres presiones. Por ejemplo, el bloque de 1 psia contenía filas de 10 y 20 psia.
- Había errores de hasta 95 % en v y filas con T = 2 °F.
- Se reconstruyó con IAPWS-95 en la rejilla de la Tabla A-6E de Çengel (1 a 6000 psia), con el vapor saturado como primera fila.

**Tabla inglesa de saturación por presión:** la columna `sg` contenía en realidad s_fg. Se corrigió como sg = sf + sfg en las 71 filas (el error previo llegaba a 84 %).

**Erratas puntuales:**

| Ubicación | Antes | Después |
|---|---|---|
| 32 °F | uf = hf = 2.01; sf = 2.00003 | 0 |
| 560 °F | uf = 548.4 | 557.4 |
| 180 bar y 520 °C | h = 3378 kJ/kg | 3330.7 kJ/kg |

**Textos en inglés de la interfaz:** "Untitled Design" pasa a "Diseño sin título" y "Thermodynamic Cycle Simulator" a "Simulador de ciclos termodinámicos".

### `07 Ciclos de potencia y refrigeracion/Ciclo refrigeracion_ORV.html` (antes también en las unidades 04 y 05)

- Se agregó una validación: las temperaturas de evaporación y condensación deben estar dentro del intervalo de la tabla interna (−60 °C a Tmáx).
- Antes, por ejemplo, el R-134a con T_evap = −80 °C se calculaba con los datos de −60 °C sin ningún aviso.

**Estado de referencia unificado (decisión del autor, validada):**
- Las tablas internas de los cuatro refrigerantes usaban la referencia por omisión de CoolProp: IIR en R134a, R410A y R290 (h = 200 kJ/kg y s = 1 kJ/kg·K en líquido saturado a 0 °C), y otra distinta en R717.
- Ahora los cuatro usan la convención ASHRAE: h = 0 y s = 0 en líquido saturado a −40 °C. Es la misma de `R134aTables_ORV`, de los diagramas del R-134a y de las tablas del R-134a de Moran y de Çengel.
- Es un desplazamiento constante por fluido: Δh = 148.144 (R134a), 142.245 (R410A), 105.123 (R290) y 164.617 kJ/kg (R717). Se verificó contra CoolProp con referencia ASHRAE.
- COP, flujo másico, T₂ y título no cambian; la diferencia máxima, en 16 casos, fue de 1.6 × 10⁻¹³.
- Ejemplo con R-134a, T_evap = −10 °C, T_cond = 45 °C y 5 K de sobrecalentamiento y de subenfriamiento: h₁ pasa de 396.93 a 248.79 kJ/kg y h₃ de 256.30 a 108.15 kJ/kg. Estos valores ya se pueden comparar con la tabla.
- Se aplicó también a las dos copias de `Copias repetidas corregidas/` (unidades 04 y 05).

**Tablas de vapor sobrecalentado y de líquido subenfriado (revisión 3, solicitada por el autor después de la validación):**
- Antes, los estados 1, 2s y 2 se calculaban con c_p constante del vapor, y el estado 3 con c_p del líquido evaluado a T_cond.
- Ahora la aplicación incluye, para los cuatro refrigerantes, tablas generadas con CoolProp:
  - vapor sobrecalentado: T_sat de −40 a 70 °C, sobrecalentamiento hasta 350 K o hasta el límite de la ecuación de estado;
  - líquido subenfriado: hasta 20 K.
- Se guardan como desviaciones respecto del estado saturado (Δh, Δs), así que no dependen de la referencia y coinciden exactamente con la tabla de saturación cuando ΔT = 0.
- Interpolación PCHIP en ΔT y lineal en T_sat. Las inversiones (s₁ → T₂s y h₂ → T₂) se resuelven por bisección.
- Si la compresión isentrópica termina en la región húmeda, se usa el título.
- Si la descarga sale de la tabla, la aplicación muestra un aviso en lugar de extrapolar.
- El diagrama T–s ahora traza las isobaras reales: 2 → vapor saturado → líquido saturado → 3, y 4 → vapor saturado → 1. Antes eran rectas.

Resultados sobre 817 casos (4 refrigerantes; T_evap de −40 a 20 °C; T_cond de 20 a 70 °C; sobrecalentamiento de 0, 5 y 20 K; η de 0.5, 0.75 y 1), comparados con el ciclo exacto de CoolProp. En 11 casos extremos (por ejemplo, amoniaco de −40 a 70 °C con η = 0.5), la descarga supera el límite de la tabla y la aplicación muestra el aviso:

| Refrigerante | Error máx. en T₂, antes | Ahora | Error máx. en COP, antes | Ahora |
|---|---|---|---|---|
| R-134a | 5.4 K | 0.02 K | 0.24 % | 0.10 % |
| R-410A | 27.2 K | 0.02 K | 105 % (líquido cerca del punto crítico) | 0.04 % |
| R-290 | 5.0 K | 0.01 K | 0.44 % | 0.03 % |
| R-717 (amoniaco) | 89.3 K | 0.07 K | 4.4 % | 0.04 % |

Ejemplo con amoniaco a −10 °C / 45 °C, 5 K de sobrecalentamiento y de subenfriamiento, y η = 0.75: T₂ pasa de 151.6 a 167.7 °C (CoolProp da 167.7 °C).

### `05 Segunda ley y entropia/Balance entropia_ORV.html` y `06 Analisis de exergia/Analisis de Exergia_ORV.html`

- Para vapor sobrecalentado fuera de la tabla interna (10 a 10 000 kPa, T ≤ 600 °C) ahora aparece un aviso.
- Antes la aplicación extrapolaba. A 15 000 kPa, el error en h llegaba a 10 %.

### Tutor IA: retirado

A solicitud del autor, el asistente Tutor IA (que usaba Gemini) se retiró del simulador de volumen de control, la única aplicación donde estaba instalado. Ya no queda en el código ninguna llamada a servicios externos de IA. La carpeta `Tutor IA` no forma parte de esta entrega.

---

## 2. Adaptación a celular y accesibilidad (todas las aplicaciones)

Cada aplicación incluye una capa común: un bloque `<style id="orv-movil-a11y-css">` en `<head>` y un bloque `<script id="orv-movil-a11y-js">` al final de `<body>`. Esta capa no toca el código de cálculo; para quitarla basta con borrar esos dos bloques.

### Experiencia en celular (pantallas de hasta 720 px)

- **Encabezado:** la marca, el título y los botones SI / Inglés se acomodan en varias líneas. Antes se encimaban en unas 25 aplicaciones.
- **Desbordes horizontales:** se eliminaron en las 8 aplicaciones que los tenían. Ahora ninguna se sale de una pantalla de 390 px.
- **Tablas anchas:** se desplazan dentro de su propio recuadro, con una sombra lateral que indica que hay más contenido.
- **Diseños rígidos:** las rejillas y filas que no cabían pasan a una columna.
- **Botón AYUDA:** ahora es un botón circular compacto y la página deja espacio al final, para que no tape el contenido.

### Accesibilidad (resultados de axe-core, WCAG 2.1 A/AA)

| Problema | Antes | Después |
|---|---|---|
| Campos sin etiqueta accesible (`label`) | 150 | 0 |
| Listas desplegables sin nombre (`select-name`) | 39 | 0 |
| Contraste de color insuficiente | 252 | 20 |
| Regiones desplazables sin acceso por teclado | 6 | 0 |
| Controles anidados (`nested-interactive`) | 6 | 0 |
| Marco sin título (`frame-title`) | 1 | 0 |
| **Total** | **454** | **20** |

Qué hace la capa:

- **Teclado:** los elementos que solo respondían al clic del ratón ahora se pueden enfocar con Tab y activar con Enter o Espacio. Antes había entre 1 y 16 de estos elementos por aplicación. El foco se ve con un contorno naranja.
- **Etiquetas:** cada campo recibe un nombre accesible, tomado de su rótulo visible. Así, un lector de pantalla anuncia, por ejemplo, «Temperatura» en lugar de «campo de edición».
- **Contraste:** los grises y tonos de texto que no alcanzaban 4.5:1 se oscurecieron ligeramente, conservando el tono. Por ejemplo, #6b7a99 pasó a #596782 y #1976d2 a #1667b7, solo en el texto. Los fondos, bordes y líneas de las gráficas no cambian.
- **Movimiento reducido:** se respeta la preferencia del sistema de reducir animaciones.
- **cp-cv-k:** los interruptores de gases ahora se anuncian como interruptores, con su estado de encendido o apagado.

Los 20 casos de contraste restantes son:

- el «· IA ·» naranja del logotipo sobre el encabezado azul, que es parte de la identidad visual;
- leyendas de gráficas cuyo color se asigna desde el código de dibujo;
- algunos textos blancos sobre botones de color medio.

Corregirlos implicaría cambiar la paleta de la marca.

---

## 3. Copias repetidas retiradas del compendio

Por instrucción del autor, en el compendio solo queda una copia de cada aplicación, con terminación `_ORV`. Las copias retiradas no se borraron: se movieron a la carpeta de trabajo de su unidad. Se aplicó tanto en `00 Compendio ORV` como en esta carpeta.

| Archivo retirado del compendio | Movido a (carpeta de trabajo) | Observación |
|---|---|---|
| `04 …/Ciclo refrigeracion_ORV.html` | `04 Primera ley  Siste Vol Control/Ciclo refrigeracion_ORV (copia compendio).html` | Difería de la de la unidad 07 solo en el texto de ayuda |
| `05 …/Ciclo Refrigeracion_ORV.html` | `05 Segunda ley y entropia/Ciclo Refrigeracion_ORV (copia compendio).html` | Ídem |
| `07 …/Rankine_ORV.html` | `07 Ciclos potencia y refrigeración/Rankine_ORV (copia compendio).html` | Idéntica a `Ciclo Rankine_ORV.html` (y a la que ya existía en la carpeta de trabajo) |
| 4 respaldos `Vol control ciclo termodinamico_ORV…BACKUP-2026-09-08/09/09b/13.html` | `04 Primera ley  Siste Vol Control/` (mismo nombre) | Respaldos, no aplicaciones |
| Las 3 copias equivalentes de esta carpeta corregida | Subcarpeta `Copias repetidas corregidas/` de esta misma carpeta, por unidad y con su nombre original | Ya traen las correcciones |

Se conservan `07 Ciclos de potencia y refrigeracion/Ciclo Rankine_ORV.html` y `07 Ciclos de potencia y refrigeracion/Ciclo refrigeracion_ORV.html`.

**Índice (`index.html`):** las tarjetas de refrigeración de las unidades 04 y 05 se conservan, porque el tema es pertinente ahí, pero ahora abren la copia única de la unidad 07 (lo indica su descripción). Se quitó la tarjeta «Simulador del Ciclo Rankine (variante RNE)». Los conteos quedaron: unidad 07, 2 apps; total, 39 tarjetas para 38 archivos. El índice de esta carpeta incluye además la capa de celular y accesibilidad. El índice anterior de `00 Compendio ORV` se respaldó, sin cambios, en `Respaldo/index_original_antes_2026-10-18.html` dentro de esta carpeta.

**Observación previa, sin cambio:** `02 Sustancias puras/Cuestionario de Vapor Interactivo_ORV.html` ya no tenía tarjeta en el índice original; no se agregó. *Decisión del autor (19-oct-2026): se mantiene sin tarjeta.*

**Sin cambio:** `Relacion_Unidades_Aplicaciones_ORV.docx` y `.tex` del compendio original todavía listan las copias retiradas; conviene actualizarlos.

## 4. Lo que no se modificó y requiere decisión del autor

1. ~~Estado de referencia del R-134a en el ciclo de refrigeración.~~ **Resuelto:** el autor decidió unificar la referencia (ASHRAE) y lo validó; véase la sección 1.
2. ~~Temperatura de descarga en el ciclo de refrigeración con amoniaco.~~ **Resuelto** con tablas de vapor sobrecalentado (revisión 3); véase la sección 1.
3. **Simulador de volumen de control.** El código está compilado (minificado) y su interpolación cerca de la saturación no se modificó. Solo se corrigieron los datos.
4. **En celular, con limitaciones:**
   - Las gráficas de Plotly funcionan, pero en 390 px quedan estrechas y algunas etiquetas de los ejes se enciman.
   - ~~Las tres subaplicaciones que la Suite de ecuaciones de estado carga dentro de marcos no recibieron la capa.~~ **Resuelto el 20-oct-2026:** las tres tienen ahora la capa de celular y accesibilidad. En la Calculadora de estado desaparecieron 3 fallas de accesibilidad (2 campos sin etiqueta y 1 lista sin nombre) y el desborde horizontal en celular (508 px de contenido en 352 px de pantalla).
5. **No validado:** la carta de compresibilidad generalizada (datos promediados) y las calculadoras de puntos de los diagramas de agua. Los datos de estas últimas sí se verificaron y su lógica de interpolación ya devuelve `null` fuera de rango.

## 5. Cómo validar y sustituir

1. Abra cada archivo corregido junto al original, en dos pestañas del navegador.
2. Pruebe los casos de la sección 1. Por ejemplo, en `SteamTables_ORV`, pestaña **Sobrecalentado / Comprimido**, use T = 700 °C y P = 0.35 bar y verifique que aparece el aviso.
3. Si todo está conforme, copie los archivos de esta carpeta (incluido `index.html`) a `00 Compendio ORV`, en la misma subcarpeta, reemplazando los originales. Conviene antes respaldar los originales o tenerlos bajo control de versiones con Git.
4. Si publica el compendio en Moodle o GitHub Pages, vuelva a subir los archivos sustituidos.

## 6. Fuente única de datos (19 de octubre de 2026)

Las tablas del agua y del R-134a de 23 aplicaciones se alimentan ahora de `datos/agua.json` y `datos/r134a.json`, en la carpeta raíz del proyecto (véase `datos/LEEME.md`). Al unificarlas cambiaron 611 valores en 10 aplicaciones; ninguno afecta las tablas de Moran ni del R-134a que validó el autor:

- **Diagramas del agua (9):** las temperaturas de saturación en unidades inglesas se unificaron a IAPWS-95 (cambios ≤ 0.15 °F); además se corrigieron 4 valores SI que no coincidían con la Tabla A-4 de Moran (por ejemplo, u a 40 bar y 700 °C: 3461.3 → 3462.1 kJ/kg).
- **Ciclo Rankine:** la tabla de saturación y las isobaras de 160 a 200 bar coinciden ahora con las tablas de Moran, como el resto del compendio. El error de la eficiencia frente a IAPWS-95 bajó de 0.073 % a 0.057 % (360 casos).

Para comprobar en cualquier momento que las aplicaciones siguen coincidiendo con la fuente: `npm run verificar`.

**Decisiones del autor (19-oct-2026):** se conservan las dos fuentes del agua (Moran para SteamTables, diagramas y Rankine; IAPWS-95 para Balance de entropía y Análisis de exergía) y los dos orígenes de las tablas del agua en unidades inglesas. Véase `datos/LEEME.md`.

## 7. Funcionamiento sin internet (19 de octubre de 2026)

Las aplicaciones ya no descargan nada al abrirse. Plotly 2.27.0 (20 aplicaciones), Chart.js 4.4.1 (3), MathJax 3.2.2 (1) y las fuentes tipográficas (29 aplicaciones, el índice y las 3 subaplicaciones de la Suite) se cargan desde la carpeta `lib/` de este compendio (6.1 MB; véase `lib/LEEME.md`).

- **Respaldo:** si se usa un archivo HTML suelto, sin la carpeta `lib/` (por ejemplo, subido individualmente a Moodle), la aplicación descarga las bibliotecas de internet como antes.
- **Al publicar** el compendio (GitHub Pages, Moodle como carpeta, memoria USB), incluya la carpeta `lib/`.
- **Comprobación:** la prueba `pruebas/04-sin-internet.spec.js` bloquea toda conexión y verifica que las 38 aplicaciones y el índice funcionen, con sus gráficas, ecuaciones y fuentes. `npm run bibliotecas` informa si alguna aplicación vuelve a depender de internet.

## 8. Unidad 09 «Rankine · Modalidades» (21 de octubre de 2026)

Se incorporó `09 Rankine Modalidades/Rankine Mejorado_ORV.html` (Rankine Mejorado v5.1), que reúne siete configuraciones del ciclo: simple, recalentamiento, regeneración con calentador abierto o cerrado, recalentamiento con extracción en la turbina de alta, en la de baja o en ambas, y ciclo supercrítico, con análisis exergético y banco de validación integrado.

**Cómo se verificó.** Se contrastó con CoolProp (formulación IAPWS-IF97) en 4 410 ciclos de las siete configuraciones, en todo el intervalo de los controles (P_max de 20 a 300 bar, T_max de 300 a 700 °C, P_c de 0.05 a 5 bar, η_T y η_P ideales y reales), y se comparó el resultado en SI y en unidades inglesas.

| Defecto encontrado en la v5.0 | Efecto | Corrección |
|---|---|---|
| El vapor sobrecalentado por debajo de 0.5 bar se leía en la isobara de 0.5 bar | En ciclos con recalentamiento y descarga sobrecalentada, η hasta 12.8 puntos menor (20 bar/600 °C, P_r = 2 bar, P_c = 0.05 bar: 28.1 % en lugar de 40.4 %) | Isobaras IF97 de 0.04, 0.1 y 0.2 bar |
| La isobara superior se extrapolaba por debajo de su T de saturación (cerca de la campana y del punto crítico) | Error en h de hasta 32 kJ/kg | Interpolación sobre la isoterma en ln p, con el vapor saturado como nodo; isobaras de 190, 210, 230 y 240 bar; inversión por bisección |
| Con T_max ≤ T_sat(P_max) la turbina recibía líquido saturado | Resultados sin sentido físico, sin aviso | Entrada de vapor saturado (x = 1) con aviso; en ciclo supercrítico, T ≥ 400 °C |
| Calentador cerrado: agua de alimentación como líquido saturado a T_3 | η hasta 0.91 puntos fuera | Líquido comprimido mediante las relaciones de Maxwell (0.14 puntos) |
| Tabla de saturación con cinco entradas mal transcritas de Moran A-3 (4.5, 120, 140 y 160 bar), ocho presiones ausentes y dos filas cercanas al punto crítico erróneas | Hasta 9.8 kJ/kg | Filas de la fuente única (`datos/agua.json`) e IF97 cerca del punto crítico; interpolación en ln p; s_c = 4.4070 kJ/kg·K |
| Tablas inglesas con isobaras de presión redondeada (7 psia para 0.5 bar = 7.25 psia) | Pequeñas diferencias entre SI e inglés | Derivadas de las SI con factores exactos al cargar la página (diferencia en η: 0.0000 puntos) |
| Cursor de los diagramas: mostraba la isobara tabulada más próxima | Presión y temperatura aproximadas | Búsqueda continua |
| Desborde de 589 px en celular; 64 fallas de contraste | — | Capa común de celular y accesibilidad; fuentes desde `lib/` |

**Resultado.** Error máximo en η frente a IAPWS-IF97 de 0.02 puntos en seis configuraciones y de 0.14 puntos con calentador cerrado; banco de validación integrado 12/12 (motor), 12/12 (literatura) y 40/40 (invariantes). Se añadieron seis casos a `pruebas/02-casos-validados.spec.js`.

**Fuente de datos.** La tabla de saturación coincide con Moran A-3 de la fuente única; las de vapor sobrecalentado y supercrítico son IAPWS-IF97, propias de esta aplicación (no forman parte de `datos/agua.json`).


## 9. Refrigerantes en el simulador de ciclos por volumen de control (26 de octubre de 2026)

`04 Primera ley y volumen de control/Vol control ciclo termodinamico_ORV.html` trabajaba solo con agua y aire. Ahora el selector de sustancia (encabezado y medidores) incluye **R-134a, R-410A, R-290 (propano) y R-717 (amoniaco)**, en unidades SI e inglesas.

**Datos.** Tablas generadas con CoolProp 8.0 (R-134a: Tillner-Roth y Baehr; R-410A como pseudo-fluido puro de Lemmon; propano: Lemmon et al.; amoniaco: Gao et al.), con la **referencia ASHRAE** (h = 0 y s = 0 en líquido saturado a −40 °C), la misma de `R134aTables_ORV`, de los diagramas del R-134a y del ciclo de refrigeración:
- saturación de −60 °C al punto crítico (cada 2 K, más fino cerca del punto crítico) y por presión (≈ 70 presiones, escala logarítmica);
- vapor sobrecalentado en 44–56 isobaras por fluido (hasta 0.96 P_c), con sobrecalentamientos de 0 a 250 K y hasta 200 °C (250 °C en amoniaco);
- líquido comprimido: propiedades del líquido saturado a la misma T corregidas con (∂v/∂P)_T, (∂h/∂P)_T y (∂s/∂P)_T.
Las tablas inglesas se derivan de las SI al cargar la página (la referencia ASHRAE coincide, porque −40 °C = −40 °F). u se calcula como h − Pv.

**Interpolación.** El vapor sobrecalentado se interpola a sobrecalentamiento constante (ΔT = T − T_sat) entre isobaras, lineal en ln P para u, h, s y en 1/P para v. Así la isobara superior no se extrapola por debajo de su saturación (el defecto que se corrigió en el Rankine Mejorado). El motor de pares (cualquier par de x, T, P, v, u, h, s), los medidores, la selección de fase y los diagramas T-s, P-v, T-v y h-s (con campana e isóbaras guía) funcionan igual que con agua.

**Cómo se verificó.**
| Prueba | Resultado |
|---|---|
| 1 200 estados al azar (300 por fluido) contra CoolProp | h: error máx. 0.9 kJ/kg (vapor) y 1.3 kJ/kg (líquido comprimido, amoniaco); inversiones (P,h) y (P,s): T con error ≤ 0.6 K |
| Ciclo ideal −10/45 °C, compresor isentrópico, los cuatro fluidos, SI e inglés | COP con error ≤ 0.11 % (R-134a 3.516 / 3.516; R-410A 3.245 / 3.249; R-290 3.451 / 3.452; R-717 3.806 / 3.807) |
| Çengel, Ej. 11-1 (R-134a, 0.14 / 0.8 MPa) | h₁ = 239.18, h₂ = 275.40, h₃ = 95.50 kJ/kg; x₄ = 0.3225; COP = 3.97 |
| Plantillas existentes (agua y aire) | Resultados idénticos a la versión anterior |

**Limitaciones.** Por encima de la presión crítica se usa la isobara tabulada más próxima (con aviso en el estado). Con fase «Automática», los pares (T, h) y (T, u) en líquido comprimido son casi indeterminados (h apenas depende de P) y se interpretan como mezcla de título ≈ 0, igual que con agua; para resolverlos elija la fase «Líquido».

**Otros cambios en la misma aplicación.**
- Nueva plantilla «Refrigeración R-134a — Analizado» (Çengel, Ej. 11-1).
- **Corrección del panel de COP.** Calculaba COP_R = Q_cedido/W (que es el COP de bomba de calor) y aparecía también en ciclos de potencia. Ahora COP_R = Q_L/W y COP_BC = Q_H/W, y solo se muestra en ciclos sin turbina. La plantilla «Refrigeración — Analizado» (agua) decía COP ≈ 5.0; el valor correcto es ≈ 4.8.
- Respaldo de la versión anterior: `Respaldo/Vol control ciclo termodinamico_ORV_antes_refrigerantes_2026-10-26.html`.
