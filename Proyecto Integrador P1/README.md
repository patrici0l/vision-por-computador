# Proyecto Integrador P1 — Visión por Computador

## Objetivo
Construir un flujo reproducible de análisis, preprocesamiento y segmentación de imágenes de productos y escenas de compra.

## Estado del dataset propio
El conjunto contiene **121 imágenes JPEG listas para OpenCV**: 50 fotos de productos individuales en 11 clases provisionales y 71 escenas de estantes.

- `data/propio/imagenes/`: única carpeta de trabajo con las 121 imágenes. Los nombres indican tipo, categoría y número correlativo.
- `data/propio/fuentes_heic/`: conserva las 71 capturas HEIC originales de las escenas. Cada una comparte el mismo nombre base con su JPEG.
- Las fotos de productos ya están en JPEG y se usan directamente; no se mantienen copias duplicadas en otra carpeta.
- `metadata/registro_capturas.csv` registra las fotos de productos.
- `metadata/registro_escenas_supermercado.csv` relaciona cada fuente HEIC con su JPEG y conserva el identificador de cámara de origen.
- Las categorías siguen siendo provisionales. No hay cajas ni máscaras anotadas; faltan datos de dispositivo y condiciones de captura.

## Estructura
- `data/propio/imagenes/`: archivos `producto_<categoria>_<numero>.jpeg` y `escena_<categoria>_<numero>.jpg`.
- `data/propio/fuentes_heic/`: originales HEIC de las escenas.
- `data/publico/MVTec_D2S/`: ubicación reservada para el dataset público pendiente de solicitar y descargar.
- `metadata/`: registros, protocolo de captura y estado de los datasets.
- `notebooks/`: cuadernos de inventario y representación de color.
- `resultados/figuras/`: salida para las figuras generadas por los cuadernos.

## Cuadernos
Abre `notebooks/01_fase1_inventario.ipynb` y `notebooks/02_fase1_representacion_color.ipynb` y ejecuta las celdas en orden desde la raíz del proyecto o desde `notebooks/`.

## Próximas capturas
La rúbrica solicita al menos 10 clases propias y capturas de imágenes y video de estantes. Registra producto/clase, dispositivo, resolución, iluminación, distancia y ángulo. Evita rostros y datos personales. Consulta metadata/protocolo_captura.md antes de añadir fotos.

## Dataset público y licencia
El dataset público MVTec D2S está pendiente de solicitar y descargar desde su [página oficial](https://www.mvtec.com/research-teaching/datasets/mvtec-d2s). Antes de distribuir datos o resultados, revisa sus condiciones; D2S se publica bajo CC BY-NC-SA 4.0 y no debe usarse con fines comerciales.
