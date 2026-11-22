# Visión por Computador — guía del espacio de trabajo

## Mapa de carpetas

- `practica1/`: cuaderno y script de la primera práctica, junto con sus nueve imágenes de ejemplo y resultados.
- `practica2/`: cuaderno de la segunda práctica y logo UPS. Usa imágenes del dataset común del proyecto.
- `Proyecto Integrador P1/`: proyecto principal. Las 121 imágenes JPEG de trabajo están juntas en `data/propio/originales/`; las fuentes HEIC/JPEG en `data/propio/fuentes_originales/`; los CSV de registro en `metadata/`.
- `CLASES/`: cuadernos del docente y copia local del Grocery Store Dataset usada por la práctica 1.
- `Material Clases/`: guía y presentaciones de las unidades.
- `.venv/`: entorno Python del espacio de trabajo.
- Los resultados de cada práctica se guardan dentro de su carpeta `resultados/`.

## Práctica 1: adquisición y representación de imágenes de productos

Objetivo: analizar fotografías propias de productos de supermercado o ferretería mediante su resolución, profundidad de color, representaciones, histogramas y segmentación por color en HSV.

## Adquisición

Captura al menos tres productos con colores identificables. Fotografía uno de ellos con luz intensa y con luz tenue, manteniendo el encuadre y la distancia. Evita reflejos fuertes y usa un fondo sencillo. Guarda las fotografías de esta práctica en `practica1/imagenes/`, en formato JPG o PNG. Registra el producto, el dispositivo y las condiciones de iluminación de cada captura.

## Ejecución

El archivo principal de trabajo es [`practica1/practica1.ipynb`](practica1/practica1.ipynb). Integrantes: Angel Lucero y Fernando Mejia. Ábrelo en VS Code con las extensiones Python y Jupyter, selecciona el kernel de Python y ejecuta las celdas en orden. Se puede iniciar desde la raíz o desde `practica1`. Cambia `NOMBRE_IMAGEN`, `COLOR` y `UMBRAL` en la celda de selección y vuelve a ejecutar desde esa celda para analizar otra fotografía. Los gráficos aparecen dentro del cuaderno y los resultados se guardan en `practica1/resultados/cuaderno/`.

El notebook sigue las secciones de la rúbrica: planteamiento del problema, requisitos y restricciones; construcción y análisis del dataset; representación de imágenes y espacios de color. Incluye observaciones y conclusiones por paso, galerías de imágenes propias y públicas, auditoría de etiquetas, distribución de clases y comparación de máscaras RGB/HSV.

Para el análisis público se requiere la copia local de Grocery Store Dataset en `CLASES/datos/GroceryStoreDataset/dataset` o `practica1/datos/GroceryStoreDataset/dataset`, con sus imágenes, `classes.csv` y los manifiestos `train.txt`, `val.txt` y `test.txt`. Esta carpeta está excluida de Git: al compartir el proyecto hay que proporcionar también el dataset. El notebook conserva los originales y exporta las incidencias y duplicados exactos encontrados.

Instala las dependencias con `python -m pip install -r requirements.txt`. El cuaderno comienza con `miloFront.jpeg` y segmentación verde.

Para procesar todas las fotografías mediante el script:

```powershell
python -m pip install -r requirements.txt
python practica1/practica1.py
```

El programa procesa todas las imágenes de la carpeta. Cambia `COLOR` al inicio de `practica1.py` por `rojo`, `amarillo`, `verde` o `azul`. Ajusta `RANGOS` según los colores de tus fotos. `UMBRAL = 127` controla la conversión a blanco y negro.

## Fundamento

La resolución se expresa como ancho × alto en píxeles. El programa trabaja con imágenes cargadas en tres canales de 8 bits: 24 bits por píxel y 256 niveles por canal. Esta es la representación utilizada al cargar la fotografía con OpenCV; no certifica la profundidad original del archivo. El tamaño comprimido del archivo y la memoria de la matriz se registran por separado.

OpenCV carga los canales en orden BGR. Para visualizarlos con Matplotlib se convierten a RGB. La escala de grises combina los canales mediante una ponderación aproximada de 0.299R + 0.587G + 0.114B. La imagen binaria asigna 255 a intensidades mayores que el umbral y 0 a las restantes. Aunque contiene dos valores y puede representarse con un bit por píxel, aquí se conserva en una matriz de 8 bits.

HSV representa tono, saturación y brillo. Para matrices de 8 bits, OpenCV utiliza H entre 0 y 179, y S y V entre 0 y 255. Los canales HSV se muestran por separado; la matriz HSV completa no debe interpretarse directamente como una imagen RGB.

Los histogramas muestran cuántos píxeles tienen cada intensidad o tono. Los picos cercanos a 0 en gris corresponden a zonas oscuras; los cercanos a 255, a zonas claras. Al comparar fotografías de distinta resolución, los conteos absolutos dependen también del número de píxeles.

La segmentación selecciona los píxeles dentro de los rangos HSV y aplica la máscara a la imagen original. El rojo requiere dos intervalos porque se encuentra en ambos extremos de H. El porcentaje calculado corresponde a la superficie de la imagen que cumple el rango; no equivale al porcentaje de productos detectados. También puede seleccionar fondos con colores similares. HSV facilita separar el tono del brillo, pero los umbrales siguen necesitando ajustes ante sombras, reflejos o cambios de iluminación.

## Entregables generados

Cada fotografía tiene su propia carpeta dentro de `practica1/resultados/`, con las representaciones, histogramas, máscara y segmentación. Las matrices RGB y HSV se guardan como archivos `.npy`. `resumen.csv` reúne las dimensiones, profundidad de la representación cargada, tamaños y porcentaje segmentado.

## Estado de las fotografías y pendientes de adquisición

El notebook analiza nueve fotografías propias: cinco de leche condensada y cuatro de Milo. Muestra sus resoluciones, memoria, tamaños de archivo, representaciones, histogramas y máscaras con observaciones y conclusiones. La comparación RGB/HSV usa las cuatro vistas de Milo; los cambios de encuadre impiden interpretarla como un experimento controlado de iluminación.

Para completar los requisitos de adquisición de esta guía faltan un tercer producto, el registro de dispositivo, distancia y condiciones de captura, y una pareja del mismo producto con luz intensa y tenue manteniendo encuadre y distancia. No se dispone de máscaras manuales para medir exactitud: el porcentaje seleccionado es área y la IoU entre métodos solo mide su coincidencia.

## Referencias

- [LearnOpenCV: Color spaces in OpenCV](https://learnopencv.com/color-spaces-in-opencv-cpp-python/)
- [PyImageSearch: OpenCV Color Spaces](https://pyimagesearch.com/2021/04/28/opencv-color-spaces-cv2-cvtcolor/)
- [PyImageSearch: OpenCV Image Histograms](https://pyimagesearch.com/2021/04/28/opencv-image-histograms-cv2-calchist/)
- [OpenCV: Changing Colorspaces](https://docs.opencv.org/4.x/df/d9d/tutorial_py_colorspaces.html)
- [OpenCV: Image Histograms](https://docs.opencv.org/4.x/d1/db7/tutorial_py_histogram_begins.html)
