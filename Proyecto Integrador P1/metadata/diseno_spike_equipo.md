# Spike: búsqueda secuencial por fases

`Nov 25, 2026` · `@PatricioLucero` (PM) · **Equipo:** Patricio Lucero, Fernando Mejia, Bryam Mejia, Erick Salinas

**Pregunta:** ¿podemos ajustar la red para nuestro dataset en una semana? **Métricas:** Dice macro (criterio principal) y exactitud (`ACC`), en validación. **Dataset:** 121 imágenes propias — 50 productos en 10 clases y 71 escenas de estantes (train 85 | val 18 | test 18), entrada $224 \times 224\text{ px}$. **Time-box:** hasta el `Nov 28, 2026`.

## Espacio de búsqueda

| Hiperparámetro | Valores | Cantidad | Fase |
| :--- | :--- | :--- | :--- |
| Épocas | 5, 10, 20, 30, 50, 80, 100, 150 | 8 | 1 |
| Batch size | 4, 8, 16, 32 | 4 | 1 |
| Optimizador | RMSprop, Adam | 2 | 1 |
| Kernel (inicializador) | he_normal, glorot_uniform | 2 | 1 |
| Neuronas L1, L2, L3 | 4, 8, 16, 32, 64, 128 | 6 cada una | 2 |
| Dropout d1, d2, d3 | 0; 0,1; 0,2; 0,3 | 4 cada uno | 3 |

Sin batch 64, 128, 256 y 512: con 85 imágenes de train serían solo 1 o 2 pasos por época. Sin épocas $\ge 200$ ni neuronas 256: con 121 imágenes el modelo memorizaría el train (*overfitting*).

## Reparto por fases

| Fase | Responsable | Varía | Corridas |
| :--- | :--- | :--- | :--- |
| 0. Preparación | Patricio Lucero (PM) | Pipeline (CLAHE + Gaussiano), split 85/18/18, baseline, tiempo por época | 1–3 |
| 1. Compilación | Fernando Mejia | Épocas, batch, optimizador, kernel | 128 → 16 |
| 2. Neuronas | Bryam Mejia | L1, L2, L3 | 216 |
| 3. Dropout y cierre | Erick Salinas | d1–d3; top 3 × 3 semillas; test | 64 + 9 |

Cada fase fija los ganadores de la anterior (la Fase 1 usa $L_1 = L_2 = L_3 = 16$ y $d_1 = d_2 = d_3 = 0{,}1$). Total: **417 corridas** ($128 + 216 + 64 + 9$), **305 con épocas del historial** (búsqueda completa: **1.769.472**).

* **Selección:** gana el mayor Dice macro en validación; desempata la exactitud (`ACC`).
* **Velocidad:** *early stopping* y épocas del historial (Fase 1: $128 \rightarrow 16$ corridas de 150 épocas).
* **Kernel y azar:** hipótesis `he_normal` (ReLU); top 3 con 3 semillas; test una sola vez.

---

## Guía detallada de decisiones y trabajo por integrante

### Fase 0 — Preparación, Dataset y Línea Base (`1–3 corridas`)
**Responsable:** **Patricio Lucero (PM)**

1. **Limpieza y estructura del dataset (121 imágenes en `data/propio/imagenes/`):**
   - **50 fotos de productos individuales** distribuidas en **10 clases reales**:
     - `producto_vinagre_manzana_natures_heart` (6), `producto_arroz_el_dorado` (5), `producto_atun_van_camps` (5), `producto_azucar_blanca_san_carlos` (5), `producto_cereal_milo` (5), `producto_leche_condensada` (5), `producto_leche_deslactosada_artesanal` (5), `producto_manteca_vegetal_los_3_chanchitos` (5), `producto_margarina_girasol` (5), `producto_aceite_oro` (4).
     - *Decisión de curación:* Unificar en `metadata/registro_capturas.csv` la etiqueta de `producto_cereal_milo_05` (`cereal milo` $\rightarrow$ misma clase que `01..04`) para trabajar con 10 clases exactas.
   - **71 escenas de estantes de supermercado** distribuidas en **10 categorías de pasillo**:
     - `bebidas` (19), `abarrotes_cereales` (12), `cuidado_bucal` (10), `frutas_verduras` (8), `snacks_golosinas` (5), `aseo_mixto` (4), `cuidado_personal` (4), `detergentes` (3), `lacteos_yogur` (3), `limpieza_hogar` (3).
2. **Partición estratificada 70 % / 15 % / 15 % (`SEED = 42`):**
   - **Train:** **85 imágenes** (`35` productos + `50` escenas). *El aumento de datos (iluminación, rotación $\pm 15^\circ$, escala, oclusión, desenfoque) se aplica únicamente sobre este subconjunto.*
   - **Validación (`Val`):** **18 imágenes** (`8` productos + `10` escenas). *Criterio para elegir los ganadores de las Fases 1, 2 y 3.*
   - **Prueba (`Test`):** **18 imágenes** (`7` productos + `11` escenas). *Reservado para una única evaluación final en la Fase 3.*
3. **Pipeline de preprocesamiento fijo:**
   - Redimensionado a $224 \times 224\text{ px}$ (`cv2.INTER_AREA`) conservando proporción con `cv2.BORDER_REFLECT`.
   - Conversión a HSV, aplicación de **CLAHE** (`clipLimit = 2.0`, `tileGridSize = (8, 8)`) en el canal `V` y suavizado **Gaussiano** `(3, 3)` con $\sigma = 0{,}8$.
4. **Configuración Baseline (Valores Fijos Iniciales para Fase 1):**
   - Capas ocultas fijas: $L_1 = 16,\ L_2 = 16,\ L_3 = 16$.
   - Dropout fijo: $d_1 = 0{,}1,\ d_2 = 0{,}1,\ d_3 = 0{,}1$.
   - Medición del tiempo por época en corrida base para confirmar viabilidad dentro del *time-box*.

---

### Fase 1 — Parámetros de Compilación y Entrenamiento (`128 combinaciones → 16 corridas reales`)
**Responsable:** **Fernando Mejia**

* **Parámetros FIJOS recibidos de Fase 0:**
  * Neuronas: $L_1 = 16,\ L_2 = 16,\ L_3 = 16$
  * Dropout: $d_1 = 0{,}1,\ d_2 = 0{,}1,\ d_3 = 0{,}1$
* **Parámetros VARIABLES a evaluar:**
  1. **Épocas (`8 valores`):** `[5, 10, 20, 30, 50, 80, 100, 150]`
     * *Justificación:* Se descarta `1` época por subentrenamiento y valores $\ge 200$ (`200, 500, 1000`) por alto costo computacional y riesgo de sobreajuste en 85 imágenes de entrenamiento.
  2. **Batch size (`4 valores`):** `[4, 8, 16, 32]`
     * *Justificación:* Con 85 imágenes en `train`, los tamaños `64, 128, 256, 512` producirían solo 1 o 2 actualizaciones de gradiente por época ($\lceil 85/64 \rceil = 2$, $\lceil 85/128 \rceil = 1$). Con `4` a `32` se aseguran entre 3 y 22 pasos por época.
  3. **Optimizador (`2 valores`):** `[RMSprop, Adam]`
  4. **Kernel inicializador (`2 valores`):** `[he_normal, glorot_uniform]`
* **Estrategia de aceleración (`128 → 16 corridas`):**
  * En lugar de entrenar 128 modelos independientes, se ejecutan solo las **$4 \times 2 \times 2 = 16$ combinaciones** de `(batch_size, optimizador, kernel)` hasta `150 épocas`, extrayendo del historial (`history`) el **Dice macro** y la **exactitud (`ACC`)** en las épocas `5, 10, 20, 30, 50, 80, 100 y 150`.
* **Criterio de decisión y entregable a Fase 2:**
  * Seleccionar la cuádrupla `(época*, batch_size*, optimizador*, kernel*)` con mayor **Dice macro en validación** (desempatando por `ACC` y menor tiempo). Estos 4 valores quedan **FIJOS** para Bryam.

---

### Fase 2 — Densidad de Capas / Neuronas (`216 corridas`)
**Responsable:** **Bryam Mejia**

* **Parámetros FIJOS:**
  * Ganadores de Fase 1 entregados por Fernando: `(época*, batch_size*, optimizador*, kernel*)`.
  * Dropout base: $d_1 = 0{,}1,\ d_2 = 0{,}1,\ d_3 = 0{,}1$.
* **Parámetros VARIABLES a evaluar:**
  1. **Capa 1 (`L1` — 6 valores):** `[4, 8, 16, 32, 64, 128]`
  2. **Capa 2 (`L2` — 6 valores):** `[4, 8, 16, 32, 64, 128]`
  3. **Capa 3 (`L3` — 6 valores):** `[4, 8, 16, 32, 64, 128]`
  * *Total de combinaciones:* $6 \times 6 \times 6 = \mathbf{216\text{ corridas}}$.
  * *Justificación:* Se evalúan arquitecturas crecientes (tipo pirámide invertida, p. ej. `32 → 64 → 128`), constantes y decrecientes (tipo embudo, p. ej. `128 → 64 → 32`), descartando `256` por exceso de parámetros frente a las 85 imágenes de entrenamiento.
* **Criterio de decisión y entregable a Fase 3:**
  * Seleccionar la tripleta `(L1*, L2*, L3*)` con mayor **Dice macro en validación** (desempatando por `ACC` y menor número de parámetros). Estos 3 valores quedan **FIJOS** para Erick.

---

### Fase 3 — Regularización (Dropout), Estabilidad por Semillas y Cierre en Test (`64 + 9 = 73 corridas`)
**Responsable:** **Erick Salinas** (cierre conjunto con **Patricio Lucero**)

* **Parámetros FIJOS:**
  * Ganadores de Fase 1: `(época*, batch_size*, optimizador*, kernel*)`.
  * Ganadores de Fase 2: `(L1*, L2*, L3*)`.
* **Parámetros VARIABLES a evaluar (`64 corridas`):**
  1. **Dropout 1 (`d1` — 4 valores):** `[0; 0,1; 0,2; 0,3]`
  2. **Dropout 2 (`d2` — 4 valores):** `[0; 0,1; 0,2; 0,3]`
  3. **Dropout 3 (`d3` — 4 valores):** `[0; 0,1; 0,2; 0,3]`
  * *Total de combinaciones:* $4 \times 4 \times 4 = \mathbf{64\text{ corridas}}$.
* **Verificación de estabilidad frente al azar (`+ 9 corridas`):**
  * Se toman las **3 mejores configuraciones (*Top 3*)** de la Fase 3 y se re-entrenan con **3 semillas aleatorias distintas** (`seeds = [42, 7, 123]` $\rightarrow 3 \times 3 = 9$ corridas).
  * Se elige como ganadora definitiva la configuración con mejor **promedio $\pm$ desviación estándar de Dice macro en validación**.
* **Evaluación única en Test (`1 corrida final`):**
  * Con el modelo ganador definitivo congelado, se evalúa **una sola vez** sobre las **18 imágenes de `test`** para reportar el Dice macro, la exactitud (`ACC`) y la matriz de confusión final del proyecto.

---

## Resumen Matemático del Ahorro Computacional

| Enfoque | Cálculo de Combinaciones | Corridas Evaluadas | Corridas Reales Ejecutadas |
| :--- | :--- | :---: | :---: |
| **Búsqueda Completa (Fuerza Bruta)** | $8 \times 4 \times 2 \times 2 \times 6^3 \times 4^3$ | $1.769.472$ | $1.769.472$ |
| **Nuestro Spike por Fases** | $128\text{ (F1)} + 216\text{ (F2)} + 64\text{ (F3)} + 9\text{ (Semillas)}$ | **$417$** | **$305$** *(con historial en F1)* |
| **Reducción lograda** | Factor de reducción: **$5.801\times$ menos entrenamientos** | $99{,}98\%$ menos | Viable dentro del *time-box* de 1 semana |
