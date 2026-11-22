# Proyecto Integrador P1 — Visión por Computador

## Objetivo de la Entrega 1
Desarrollar un flujo reproducible de análisis, preprocesamiento y segmentación clásica de productos. El dataset público seleccionado es [MVTec D2S](https://www.mvtec.com/research-teaching/datasets/mvtec-d2s); el dataset propio se irá ampliando con capturas de productos en estantes.

## Estado actual
- Dataset propio: **50 fotos de productos individuales** en 11 clases provisionales y **71 escenas de supermercado** con varios productos por foto.
- Las 121 imágenes JPEG de trabajo están juntas en `data/propio/originales/`. Las escenas se distinguen por el prefijo `supermercado_` y se documentan en su registro separado.
- Las 112 fuentes nuevas (71 HEIC y 41 JPEG) están juntas en `data/propio/fuentes_originales/`; no se editaron sus contenidos.
- Fotos de productos individuales: `metadata/registro_capturas.csv`. Escenas de supermercado: `metadata/registro_escenas_supermercado.csv`.
- Las clases y categorías son provisionales. No hay cajas ni máscaras anotadas; faltan datos de dispositivo y condiciones de captura.
- Dataset público MVTec D2S: pendiente de solicitar/descargar desde su formulario oficial.

## Estructura
- `data/propio/originales/`: 121 imágenes de trabajo en una sola carpeta.
- `data/propio/fuentes_originales/`: fuentes HEIC/JPEG conservadas.
- `data/propio/anotaciones/`: espacio reservado para cajas y máscaras (pendientes).
- `data/publico/MVTec_D2S/`: ubicación local del dataset público; se excluye de Git.
- `metadata/`: registro de capturas y estado de datasets.
- `notebooks/`: cuadernos de Fase 1 y Fase 2. Por ahora están `01_fase1_inventario.ipynb` y `02_fase1_representacion_color.ipynb`.
- `informes/`: informe técnico y presentación.
- `resultados/`: figuras generadas; se excluye de Git.

## Primer cuaderno
Abrir `notebooks/01_fase1_inventario.ipynb` y ejecutar sus celdas en orden desde la raíz del proyecto o desde `notebooks/`. El cuaderno inventaría las imágenes disponibles; al descargar D2S, volverá a contar también las imágenes públicas.

## Protocolo de captura
Consultar `metadata/protocolo_captura.md` antes de recolectar nuevas fotos y completar una fila por imagen en el registro.

## Captura propia pendiente
La rúbrica pide al menos 10 clases propias y capturas de imágenes y video de estantes. No fija un número mínimo de fotografías. Para aumentar variedad, se recomienda avanzar hacia 20–30 imágenes por clase si el tiempo lo permite. Registrar producto/clase, dispositivo, resolución, iluminación, distancia y ángulo. Evitar rostros y datos personales.

## Licencia y uso
MVTec D2S requiere completar el formulario de descarga de MVTec y se publica bajo CC BY-NC-SA 4.0; no usarlo para fines comerciales. Conservar la atribución y revisar las condiciones oficiales antes de distribuir datos o resultados.
