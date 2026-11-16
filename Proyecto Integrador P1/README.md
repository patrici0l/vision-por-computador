# Proyecto Integrador P1 — Visión por Computador

## Objetivo de la Entrega 1
Desarrollar un flujo reproducible de análisis, preprocesamiento y segmentación clásica de productos. El dataset público seleccionado es [MVTec D2S](https://www.mvtec.com/research-teaching/datasets/mvtec-d2s); el dataset propio se irá ampliando con capturas de productos en estantes.

## Estado actual
- Dataset propio: 9 fotografías únicas copiadas desde `practica1/imagenes`; representan provisionalmente dos productos y vistas individuales.
- Cajas y máscaras del dataset propio: todavía no anotadas.
- Dataset público MVTec D2S: pendiente de solicitar/descargar desde su formulario oficial. No se copió ni modificó ningún archivo fuente.
- Registro de capturas: `metadata/registro_capturas.csv`. Las condiciones desconocidas quedaron vacías para completarlas, no se inventaron.

## Estructura
- `data/propio/originales/`: originales propios.
- `data/propio/anotaciones/`: anotaciones propias.
- `data/publico/MVTec_D2S/`: ubicación local del dataset público; se excluye de Git.
- `metadata/`: registro de capturas y estado de datasets.
- `notebooks/`: cuadernos de Fase 1 y Fase 2. Por ahora están `01_fase1_inventario.ipynb` y `02_fase1_representacion_color.ipynb`.
- `informes/`: informe técnico y presentación.
- `resultados/`: figuras y salidas generadas; se excluye de Git.

## Primer cuaderno
Abrir `notebooks/01_fase1_inventario.ipynb` y ejecutar sus celdas en orden desde la raíz del proyecto o desde `notebooks/`. El cuaderno inventaría las imágenes disponibles; al descargar D2S, volverá a contar también las imágenes públicas.

## Protocolo de captura
Consultar `metadata/protocolo_captura.md` antes de recolectar nuevas fotos y completar una fila por imagen en el registro.

## Captura propia pendiente
La rúbrica pide al menos 10 clases propias y capturas de imágenes y video de estantes. No fija un número mínimo de fotografías. Para aumentar variedad, se recomienda avanzar hacia 20–30 imágenes por clase si el tiempo lo permite. Registrar producto/clase, dispositivo, resolución, iluminación, distancia y ángulo. Evitar rostros y datos personales.

## Licencia y uso
MVTec D2S requiere completar el formulario de descarga de MVTec y se publica bajo CC BY-NC-SA 4.0; no usarlo para fines comerciales. Conservar la atribución y revisar las condiciones oficiales antes de distribuir datos o resultados.
