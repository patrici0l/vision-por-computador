# Protocolo de captura del dataset propio

## Meta de la Entrega 1
- Incluir al menos **10 clases de productos diferentes**.
- Capturar imágenes y video de estantes, como pide la guía.
- El curso no fija un mínimo de fotografías. Como objetivo práctico, intentar reunir 20–30 fotos por clase si el tiempo y acceso lo permiten.

## Qué cuenta como clase
Usar el producto/SKU que se desea reconocer, definido por su marca, variante y presentación. Distintas vistas del mismo envase son variaciones de una clase, no clases nuevas. Confirmar la marca y presentación de las fotos actuales de Milo y leche condensada antes de cerrar sus nombres de clase.

## Capturas recomendadas por clase
1. Foto individual del frente del envase para identificar etiqueta y nombre.
2. Varias fotos en el estante con otros productos alrededor.
3. Variar moderadamente distancia, ángulo e iluminación; incluir algunos casos con oclusión parcial natural.
4. Evitar una colección formada solo por ráfagas o fotogramas casi idénticos.
5. Grabar escenas cortas de los estantes para demostrar el uso en video.

## Datos que registrar
Completar una fila en `metadata/registro_capturas.csv` por cada imagen: clase, SKU si se conoce, dispositivo, fecha/hora si se conoce, resolución, iluminación, distancia, ángulo, fondo, oclusión y notas. No inventar campos desconocidos; se pueden marcar como `no registrado` cuando se redacte el informe.

## Anotaciones
- Dibujar una caja por producto visible y asignar su clase.
- Para medir segmentación con IoU o Dice, crear máscaras de píxeles de un subconjunto representativo. Las cajas no equivalen a máscaras.
- Mantener las anotaciones ligadas al nombre de archivo original.

## División y privacidad
- Separar entrenamiento/validación/prueba en proporción 70/15/15 por sesión o escena, no por fotograma, para evitar que capturas casi iguales queden en distintos conjuntos.
- Evitar rostros, placas, datos personales y conversaciones identificables en fotos y videos.
- Conservar una copia intacta de originales y trabajar sobre copias/procesamiento derivado.
