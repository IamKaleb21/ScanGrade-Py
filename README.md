
# ScanGrade-Py: Calificación Automática de Hojas de Pruebas

## Autores
- **Aarón Arteaga Rodríguez**
- **Carlos Aguilar Villanueva**

## Descripción

Este proyecto tiene como objetivo la calificación automática de hojas de exámenes mediante técnicas de procesamiento de imágenes. La solución está diseñada para procesar imágenes de hojas de respuesta y detectar automáticamente las respuestas marcadas, para luego compararlas con un patrón de respuestas correcto y calcular la calificación correspondiente. Utiliza bibliotecas como OpenCV para detectar contornos, ajustar la perspectiva de la hoja y realizar la detección de marcadores.

## Tabla de Contenidos

1. [Instalación](#instalación)
2. [Configuración](#configuración)
3. [Uso](#uso)
4. [Características](#características)
5. [Dependencias](#dependencias)
6. [Documentación](#documentación)
7. [Ejemplos](#ejemplos)
8. [Problemas Comunes](#problemas-comunes)
9. [Contribuidores](#contribuidores)
10. [Licencia](#licencia)

## Instalación

Para instalar las dependencias necesarias, primero asegúrate de tener Python 3.x instalado en tu sistema. Después, puedes instalar los requisitos usando `pip`:

    ```bash
    pip install opencv-python-headless numpy imutils matplotlib
    ```

### Opcional: Crear un entorno virtual

Es recomendable trabajar en un entorno virtual para evitar conflictos entre bibliotecas:

```bash
bash# Crear un entorno virtual
python -m venv venv

# Activar el entorno (en Windows)
venv\Scripts\activate

# Activar el entorno (en MacOS/Linux)
source venv/bin/activate

# Instalar las dependencias
pip install -r requirements.txt
```

## Configuración

### Preparación de las imágenes

Para que el sistema funcione correctamente, las imágenes de las hojas de prueba deben tener ciertas características:

* Las hojas de respuesta deben ser claras y legibles.
* Asegúrate de que las hojas estén bien alineadas al tomar la foto o escanearlas. El sistema puede corregir pequeñas desviaciones, pero una inclinación excesiva podría afectar la precisión.

### Archivos de referencia de respuestas

El proyecto utiliza un archivo con las respuestas correctas para comparar con las respuestas detectadas en la hoja. Este archivo puede ser un formato simple, como un diccionario en Python que asocia cada pregunta con la respuesta correcta:

```python
pythonrespuestas_correctas = {
    "pregunta_1": "A",
    "pregunta_2": "C",
    "pregunta_3": "B",
    ...
}
```

## Uso

El flujo básico para usar este sistema es el siguiente:

1. **Cargar la imagen de la hoja de respuestas**: El primer paso es tomar una imagen de la hoja que deseas calificar.
2. **Preprocesar la imagen**: Utilizando OpenCV, se realizan operaciones como detección de bordes, contornos y ajuste de perspectiva para asegurarse de que la hoja esté correctamente alineada.
3. **Detección de marcadores**: El sistema detecta las marcas circulares o cuadradas que representan las respuestas seleccionadas.
4. **Comparar con respuestas correctas**: Una vez detectadas las respuestas, se comparan con un archivo de respuestas correctas para calcular la calificación.

### Ejecución paso a paso

```python
pythonimport cv2
import numpy as np
import imutils
from imutils.perspective import four_point_transform
import matplotlib.pyplot as plt

# Cargar la imagen de la hoja de respuestas
imagen = cv2.imread('hoja_prueba.jpg')

# Convertir la imagen a escala de grises
imagen_gris = cv2.cvtColor(imagen, cv2.COLOR_BGR2GRAY)

# Aplicar detección de bordes y contornos
bordes = cv2.Canny(imagen_gris, 75, 200)
contornos = cv2.findContours(bordes.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
contornos = imutils.grab_contours(contornos)

# Ordenar contornos para obtener la hoja de prueba
cnts = sorted(contornos, key=cv2.contourArea, reverse=True)[:5]
for c in cnts:
    perimetro = cv2.arcLength(c, True)
    aproximacion = cv2.approxPolyDP(c, 0.02 * perimetro, True)
    if len(aproximacion) == 4:
        hoja_contorno = aproximacion
        break

# Transformar la perspectiva para obtener una vista superior
hoja_transformada = four_point_transform(imagen, hoja_contorno.reshape(4, 2))

# Mostrar la hoja ajustada
plt.imshow(cv2.cvtColor(hoja_transformada, cv2.COLOR_BGR2RGB))
plt.title('Hoja Transformada')
plt.axis('off')
plt.show()
```

## Características

* **Detección de contornos**: Identificación de los límites de la hoja de respuestas para corregir la perspectiva y asegurar que la imagen esté alineada correctamente.
* **Transformación de perspectiva**: Ajuste de la perspectiva de la hoja de respuestas, lo que permite corregir imágenes tomadas desde ángulos oblicuos.
* **Detección de marcadores**: Utiliza algoritmos de procesamiento de imágenes para detectar los marcadores de respuesta (círculos o cuadrados) seleccionados por el usuario.
* **Calificación automática**: El sistema compara las respuestas detectadas con un archivo de respuestas correctas para asignar una calificación.

## Dependencias

Las principales dependencias de este proyecto son:

* **OpenCV** (`opencv-python-headless`): Biblioteca fundamental para el procesamiento de imágenes y detección de contornos.
* **NumPy**: Para manipulación eficiente de matrices y datos.
* **Imutils**: Para facilitar operaciones de manipulación de imágenes, como ajustes de perspectiva y contorno.
* **Matplotlib**: Para la visualización de imágenes y resultados de procesamiento.

## Documentación

Puedes encontrar más información sobre las bibliotecas utilizadas en la documentación oficial:

* [OpenCV]()
* [NumPy]()
* [Imutils](https://github.com/jrosebr1/imutils)
* [Matplotlib]()

## Ejemplos

Un ejemplo de cómo utilizar este sistema para una hoja de respuestas de opción múltiple:

1. Carga la imagen de la hoja de prueba.
2. Realiza las transformaciones necesarias para corregir la perspectiva.
3. Detecta los marcadores de respuesta.
4. Compara las respuestas con un archivo de respuestas correctas.

El resultado será una calificación automática de la hoja.

```python
python# Código simplificado para ejecutar el flujo completo
calificacion = calificar_hoja('hoja_prueba.jpg', respuestas_correctas)
print(f"Calificación obtenida: {calificacion}")
```

## Problemas Comunes

### 1\. **La hoja no se detecta correctamente**:

Asegúrate de que la imagen tenga buena iluminación y que la hoja no esté demasiado inclinada. El sistema puede corregir algunas desviaciones, pero una inclinación muy extrema o una imagen borrosa puede afectar el procesamiento.

### 2\. **Errores en la detección de respuestas**:

Asegúrate de que los marcadores (círculos o cuadros) sean lo suficientemente claros y estén bien marcados. Respuestas ligeras o incompletas podrían no ser detectadas.

### 3\. **La transformación de perspectiva no funciona**:

Revisa que el contorno de la hoja haya sido correctamente detectado. Si el contorno de la hoja no está completo o se detectan múltiples objetos en la imagen, el ajuste de perspectiva podría fallar.

## Contribuidores

* **Aarón Arteaga Rodríguez**
* **Carlos Aguilar Villanueva**

## Licencia

Este proyecto está licenciado bajo los términos de la [Licencia MIT](), lo que significa que es de código abierto y libre para ser utilizado y modificado bajo los términos de dicha licencia.
