---
title: "Lección 2.2. El script, el sistema de coordenadas y la zona con margen"
---

## Dónde lo dejamos

En la lección 2.1 cargamos desde el servicio IDEAS la capa de todos los municipios de Asturias, encontramos Lena por su código, 33033, y la comprobamos: hay una sola, está en su sitio y tiene 318,4 km², frente a los 315,5 km² oficiales. Pero todo eso vive solo en la ventana de QGIS que tenemos abierta. Si cerramos QGIS, habrá que teclearlo todo otra vez. Y todavía no tenemos una copia propia del límite: si mañana el servicio no responde, el trabajo se para.

## Qué queremos entender en esta lección

¿Podemos obtener, ejecutando una sola vez un archivo de script, las dos cosas que necesitan todos los capítulos siguientes?

- **El límite de Lena:** una copia en un archivo de nuestro disco.
- **La zona de trabajo:** Lena con un margen a ambos lados del límite. Dentro de ella tomaremos las líneas, las casas y las carreteras.

¿Para qué el margen? El límite del municipio no detiene ni los conductores ni los árboles. Una línea puede pasar a unas decenas de metros al otro lado del límite, y una casa de Lena puede estar justo en el límite. Si recortamos las líneas exactamente por el límite, esa casa se queda fuera del cálculo. Además, como ya vimos, el propio límite no es exacto.

¿Qué margen hace falta? Las franjas de seguridad a lo largo de las líneas son de decenas de metros (lección 1.1). El error del límite a escala 1:25 000 también es de decenas de metros. Necesitamos un margen que supere con holgura las dos cosas, pero no tan grande como para meter en el cálculo medio municipio vecino. Tomamos 1 km. Es una decisión nuestra, no una norma, así que irá en una variable aparte que se pueda cambiar fácilmente. Para las carreteras y los parques de bomberos hace falta una zona mucho más amplia, porque los bomberos salen de municipios vecinos. Esa la haremos aparte, en el capítulo sobre el tiempo de viaje.

Qué esperamos:

1. El script se ejecuta entero con un solo botón y no depende del sistema de coordenadas que tuviera el proyecto.
2. En la carpeta del proyecto aparecen dos archivos GeoPackage: el límite de Lena y la zona de trabajo.
3. Cada archivo tiene exactamente un polígono.
4. La zona es más ancha que Lena por todas partes, y su superficie supera la de Lena en, más o menos, el perímetro multiplicado por el margen. Algo menos que esa estimación, porque en las entrantes del límite las franjas del margen se solapan.

Cómo sabremos si nos hemos equivocado:

- los archivos no aparecen o están vacíos;
- la zona coincide con Lena o es más pequeña;
- la superficie de la zona es varias veces distinta de la estimación.

## Paso 1. De la consola al editor

En la lección 2.1 escribíamos líneas en la consola, y cada una se ejecutaba al momento. Para el trabajo que hay que repetir existe el editor de scripts. Se abre con el botón **Show Editor** del panel de la consola. A la derecha aparece una ventana parecida a un editor de texto, con números de línea.

El código del editor no se ejecuta solo. Escribimos todas las líneas, guardamos el archivo y lo ejecutamos entero con el botón **Run Script** (el triángulo verde). Eso es un script: un archivo con código que se ejecuta de la primera línea a la última.

Guardamos el proyecto de QGIS en la carpeta de trabajo, si no lo hemos hecho ya, y a su lado creamos un script nuevo, `01_area.py`, con el botón **Save As…** del editor. La terminación `.py` indica que es un archivo de Python. El número `01` del principio marca el orden: los siguientes scripts serán `02_…`, `03_…`, y por el nombre del archivo se ve qué va antes y qué después.

```{figure} ../images/02-2-editor.png
:alt: Python Console with the script editor open next to it
The script editor (right) next to the Python Console (left). Run Script is the green triangle.
```

## Paso 2. El principio del script: el proyecto y el sistema de coordenadas

```python
import processing

project = QgsProject.instance()
project.setCrs(QgsCoordinateReferenceSystem("EPSG:25830"))
folder = project.homePath()
print(project.crs().authid(), folder)
```

- `import processing` carga el módulo Processing, el mismo conjunto de algoritmos que hay en **Processing Toolbox**. `import` significa «tráeme este módulo». En la consola muchas veces ya está cargado, pero el script no debe contar con ello: todo lo que necesita lo carga él mismo.
- `project.setCrs(...)` fija el sistema de coordenadas del proyecto. En la lección 2.1 solo lo leíamos; ahora lo fijamos nosotros.
- `QgsCoordinateReferenceSystem("EPSG:25830")` es un sistema de coordenadas a partir de su código. Es ETRS89 / UTM 30N, en metros. Es el que usamos para todas las distancias del libro.
- `project.homePath()` es la carpeta donde está el archivo del proyecto de QGIS. Ahí guardará el script sus archivos. No escribimos la ruta de la carpeta a mano: así el script funciona en cualquier ordenador, esté donde esté el proyecto.
- `print(...)` con dos valores separados por una coma los escribe en una sola línea, separados por un espacio.

Lo ejecutamos. Vemos `EPSG:25830` y la ruta de nuestra carpeta de trabajo.

## Paso 3. La capa de municipios, como en la lección 2.1

```python
url = "https://sig.asturias.es/servicios/services/UnidadesAdministrativas/MapServer/WFSServer"
uri = f"url='{url}' typename='UnidadesAdministrativas:Concejos' srsname='EPSG:25830' version='2.0.0'"
municipalities = QgsVectorLayer(uri, "municipalities", "WFS")
print(municipalities.isValid(), municipalities.featureCount())
```

Estas líneas ya las conocemos. Hay una diferencia: no añadimos la capa de municipios al proyecto. El script solo la necesita como fuente, y en el mapa lo que nos interesa es Lena.

## Paso 4. El límite de Lena, a un archivo propio

En lugar de la selección de la lección 2.1, usamos un algoritmo de Processing que escribe directamente en un archivo los objetos que encuentra. Es **Extract by Expression**.

```python
boundary = processing.run("native:extractbyexpression", {
    "INPUT": municipalities,
    "EXPRESSION": '"CODIGO_INE" = 33033',
    "OUTPUT": f"{folder}/lena_boundary.gpkg",
})["OUTPUT"]
```

Es nuestra primera llamada a Processing desde el código. Vamos por partes.

- `processing.run(...)` ejecuta un algoritmo. Necesita dos cosas: el nombre del algoritmo y sus parámetros.
- `"native:extractbyexpression"` es el nombre del algoritmo. Se puede encontrar en **Processing Toolbox**: si pasamos el ratón por encima de un algoritmo, su nombre aparece en la información emergente.
- Entre las llaves `{ ... }` van los parámetros. Es un diccionario: pares «nombre: valor» separados por comas. Los nombres de los parámetros son los mismos campos que en la ventana del algoritmo, pero en inglés y en mayúsculas.
- `"INPUT"` es la capa de entrada. Le pasamos la propia capa `municipalities`.
- `"EXPRESSION"` es la misma expresión que en la lección 2.1.
- `"OUTPUT"` indica dónde escribir el resultado: el archivo `lena_boundary.gpkg` en la carpeta del proyecto. La extensión `.gpkg` significa GeoPackage: un solo archivo que contiene una capa con su geometría, sus atributos y su sistema de coordenadas.
- El diccionario se puede escribir en varias líneas: así se lee mejor. Python entiende que la llamada no ha terminado mientras no se cierren todos los paréntesis y llaves.
- `processing.run` devuelve un diccionario de resultados. `["OUTPUT"]` saca de él la ruta del archivo escrito. Esa ruta la guardamos en la variable `boundary`.

Esta es nuestra copia del límite. Los capítulos siguientes tomarán Lena de este archivo, no del servicio.

## Paso 5. La zona de trabajo: un búfer

```python
margin_m = 1000
area = processing.run("native:buffer", {
    "INPUT": boundary,
    "DISTANCE": margin_m,
    "OUTPUT": f"{folder}/lena_area.gpkg",
})["OUTPUT"]
```

- `margin_m = 1000` es el margen en metros. La `m` del nombre nos recuerda en qué unidades está el número. Aquí son metros porque la capa está en EPSG:25830. En un sistema en grados, el mismo número serían 1000 grados.
- `"native:buffer"` es el algoritmo **Buffer**.
- `"INPUT": boundary`: la entrada es la ruta del archivo que acabamos de escribir. Processing acepta tanto una capa como la ruta de un archivo.
- `"DISTANCE": margin_m` es la anchura del búfer. Le pasamos la variable, no el número: si queremos otro margen, cambiamos una sola línea.
- El resto de los parámetros del búfer (el número de segmentos de los redondeos y la forma de los extremos y las esquinas) no los indicamos. Así Processing usa los valores por defecto, los mismos que aparecen en la ventana del algoritmo.

## Paso 6. Mostramos el resultado y lo comprobamos

```python
boundary_layer = QgsVectorLayer(boundary, "lena_boundary", "ogr")
area_layer = QgsVectorLayer(area, "lena_area", "ogr")
project.addMapLayer(area_layer)
project.addMapLayer(boundary_layer)
```

- `"ogr"` es el proveedor de datos para archivos: GeoPackage, Shapefile, GeoJSON y muchos otros. Para el servicio de la lección 2.1 usábamos `"WFS"`; para un archivo, `"ogr"`.
- Añadimos primero la zona y después el límite. La última capa añadida queda encima, así que el límite de Lena se verá sobre la zona.

Y la comprobación:

```python
for layer in [boundary_layer, area_layer]:
    feature = next(layer.getFeatures())
    print(layer.name(), layer.featureCount(), feature.geometry().area() / 1_000_000)
```

- `[boundary_layer, area_layer]` es una lista de dos capas. Los corchetes crean una lista.
- El bucle recorre las dos capas y, para cada una, escribe el nombre, el número de objetos y la superficie en km².
- `layer.getFeatures()` entrega los objetos de la capa de uno en uno. `next(...)` toma el primero. En cada capa esperamos exactamente un objeto, así que con el primero basta.

## El script completo

```python
import processing

project = QgsProject.instance()
project.setCrs(QgsCoordinateReferenceSystem("EPSG:25830"))
folder = project.homePath()
print(project.crs().authid(), folder)

url = "https://sig.asturias.es/servicios/services/UnidadesAdministrativas/MapServer/WFSServer"
uri = f"url='{url}' typename='UnidadesAdministrativas:Concejos' srsname='EPSG:25830' version='2.0.0'"
municipalities = QgsVectorLayer(uri, "municipalities", "WFS")
print(municipalities.isValid(), municipalities.featureCount())

boundary = processing.run("native:extractbyexpression", {
    "INPUT": municipalities,
    "EXPRESSION": '"CODIGO_INE" = 33033',
    "OUTPUT": f"{folder}/lena_boundary.gpkg",
})["OUTPUT"]

margin_m = 1000
area = processing.run("native:buffer", {
    "INPUT": boundary,
    "DISTANCE": margin_m,
    "OUTPUT": f"{folder}/lena_area.gpkg",
})["OUTPUT"]

boundary_layer = QgsVectorLayer(boundary, "lena_boundary", "ogr")
area_layer = QgsVectorLayer(area, "lena_area", "ogr")
project.addMapLayer(area_layer)
project.addMapLayer(boundary_layer)

for layer in [boundary_layer, area_layer]:
    feature = next(layer.getFeatures())
    print(layer.name(), layer.featureCount(), feature.geometry().area() / 1_000_000)
```

## Qué ha salido

La consola ha escrito:

```text
EPSG:25830 /Users/me/lena-project
True 78
lena_boundary 1 318.35508793067146
lena_area 1 410.8970065571663
```

Lo comparamos con lo que esperábamos.

1. El script se ha ejecutado entero de una vez, y el sistema de coordenadas del proyecto es EPSG:25830. Coincide.
2. En la carpeta del proyecto han aparecido `lena_boundary.gpkg` y `lena_area.gpkg`. Coincide.
3. Cada archivo tiene un objeto. Coincide.
4. La superficie del límite es la misma que en la lección 2.1: 318,4 km². Así que a Lena no le ha pasado nada al escribirla en el archivo. La superficie de la zona es de 410,9 km².

Comprobamos esta última cifra con una estimación. Un búfer de 1 km de ancho añade a la superficie, más o menos, el perímetro multiplicado por la anchura: 95,3 km × 1 km = 95,3 km² (el perímetro es el campo `st_length_shape_` de la lección 2.1). En las esquinas salientes la franja se redondea y añade otros π × 1² ≈ 3 km². En total, la estimación es de unos 417 km². Hemos obtenido 410,9 km², 6 km² menos. Es lo esperable: en las entrantes del límite, las franjas de los dos lados se solapan, y esa superficie no se cuenta dos veces. La diferencia es de un 1,5 % de la estimación, así que el orden de magnitud es correcto. La zona ha salido tal como la habíamos pensado.

Resultado de la lección: tenemos el script `01_area.py`, un archivo propio con el límite de Lena y una zona de trabajo con 1 km de margen. A partir de aquí cargaremos en esta zona las líneas eléctricas. Eso es el capítulo 3.

```{figure} ../images/02-2-area.png
:alt: Lena boundary over the working area with a 1 km margin
Lena boundary (outline) over the working area with a 1 km margin (EPSG:25830).
```

## Si algo falla

- **La ruta de la carpeta está vacía y los archivos no aparecen.** El proyecto de QGIS todavía no se ha guardado y no tiene carpeta. Guardamos el proyecto y volvemos a ejecutar el script.
- **Cada vez que lo ejecutamos aparecen dos capas más en el mapa.** El script vuelve a añadir las capas en cada ejecución. Las antiguas las quitamos a mano en el panel de capas. En el capítulo 11, «Del script a la herramienta», la herramienta devolverá el resultado por sí misma, en lugar de añadir capas al proyecto.
- **Error al escribir un archivo que ya existe.** Si el archivo está abierto en otro programa, no se puede sobrescribir. Quitamos las capas `lena_boundary` y `lena_area` del proyecto y volvemos a ejecutarlo.
- **`NameError: name 'processing' is not defined`.** Falta la línea `import processing` al principio del script.
- **La zona es enorme y ocupa toda la pantalla.** Lo más probable es que la capa haya quedado en grados y que 1000 se haya convertido en 1000 grados. Revisamos `srsname='EPSG:25830'` en la línea `uri`.
- **`StopIteration`.** La capa no tiene ningún objeto, y `next(...)` no tiene nada que tomar. Revisamos la expresión con el código del municipio.

## Tus datos

Para otro municipio de Asturias cambia el código de la expresión. Por ejemplo, para Cabrales: `'"CODIGO_INE" = 33008'`. También conviene cambiar los nombres de los archivos, para no sobrescribir los de Lena. El margen `margin_m` se cambia en una sola línea. En un municipio de montaña, donde las líneas suelen ir por las cordilleras a lo largo del límite, se puede dejar el mismo margen: ya tiene holgura de sobra.

## Resumen

### Conceptos nuevos

- **Script:** un archivo con código que se ejecuta entero, de la primera línea a la última. Ejemplo: `01_area.py`.
- **Módulo e `import`:** un conjunto de funciones ya hechas que se carga en el script. Ejemplo: `import processing`.
- **Sistema de coordenadas del proyecto:** el sistema en el que QGIS muestra todas las capas en el mapa. Ejemplo: `project.setCrs(QgsCoordinateReferenceSystem("EPSG:25830"))`.
- **Algoritmo de Processing:** una herramienta de **Processing Toolbox** que se puede ejecutar con código. Ejemplo: `processing.run("native:buffer", {...})`.
- **Diccionario:** pares «nombre: valor» entre llaves. Ejemplo: `{"INPUT": boundary, "DISTANCE": margin_m}`.
- **Lista:** valores separados por comas entre corchetes. Ejemplo: `[boundary_layer, area_layer]`.
- **GeoPackage:** un solo archivo con la capa, sus atributos y su sistema de coordenadas. Ejemplo: `lena_boundary.gpkg`.
- **Búfer:** una franja de anchura fija alrededor de un objeto. Ejemplo: la zona de trabajo de Lena con 1 km de margen.

### Código nuevo

```python
import processing
project.setCrs(QgsCoordinateReferenceSystem("EPSG:25830"))
folder = project.homePath()
result = processing.run("native:buffer", {"INPUT": boundary, "DISTANCE": margin_m, "OUTPUT": f"{folder}/lena_area.gpkg"})["OUTPUT"]
layer = QgsVectorLayer(result, "lena_area", "ogr")
feature = next(layer.getFeatures())
```

### Lo más importante

- El trabajo que hay que repetir se escribe en un script, no en la consola.
- El script fija él mismo el sistema de coordenadas y carga él mismo los módulos. No cuenta con cómo estuviera el proyecto antes de ejecutarlo.
- Los datos de un servicio ajeno se guardan enseguida en un archivo propio.
- Cada número que elegimos nosotros va en una variable aparte, con las unidades en el nombre.

## Preguntas para un ingeniero

**¿Por qué un margen de 1 km y no de 10 m o de 10 km?**

:::{dropdown} Respuesta
Queremos que entren en la zona las líneas que están justo al otro lado del límite y que pueden ser peligrosas para las casas de Lena. Las franjas de seguridad y el error del límite son de decenas de metros, así que 10 m es poco. 10 km es demasiado: entrarían en la zona miles de casas ajenas y cientos de kilómetros de líneas ajenas, el cálculo se haría largo y no ganaríamos nada. 1 km supera con mucha holgura las decenas de metros y casi no aumenta el volumen de datos. Es una decisión nuestra, y la hemos puesto en una variable para que se vea y se pueda cambiar fácilmente.
:::

**¿Para qué escribir el límite en un archivo si ya está en el servicio?**

:::{dropdown} Respuesta
Para que el cálculo se pueda repetir. El servicio puede cambiar o desactivarse, y el límite se puede corregir. Si dentro de un mes el resultado cambia, tenemos que saber con qué límite calculamos. El archivo del disco es con lo que trabajamos de verdad.
:::

**¿Por qué hacemos el búfer en EPSG:25830 y no en un sistema en grados?**

:::{dropdown} Respuesta
En EPSG:25830 la unidad es el metro, y 1000 significa 1000 m en cualquier punto de Lena. En grados, un grado de longitud y uno de latitud no equivalen al mismo número de metros, y el búfer saldría con otra anchura y otra forma. Todas las distancias del libro las calculamos en un sistema métrico.
:::

## Ejercicios

**1.** ¿Cómo se llama el algoritmo de búfer cuando lo ejecutamos con código mediante `processing.run`?

[Solución](#es-sol-2-2-1)

**2.** Esta llamada no funciona. Vamos a encontrar el error y corregirlo.

```python
area = processing.run("native:buffer", {
    "INPUT": boundary,
    "DISTANCE": "1 km",
    "OUTPUT": f"{folder}/lena_area.gpkg",
})["OUTPUT"]
```

[Solución](#es-sol-2-2-2)

**3.** Recordemos la lección 2.1. ¿Por qué buscamos Lena por el código 33033 y no por el nombre?

[Solución](#es-sol-2-2-3)

## La pregunta abierta

Hemos elegido un margen de 1 km pensando en decenas de metros. ¿Y si en el municipio vecino hay una subestación y de ella salen hacia Lena varias líneas a la vez? ¿Cambia eso algo en la elección del margen, o la zona solo sirve para encontrar las líneas que pasan cerca de las casas de Lena?

## Notas y fuentes

- QGIS Documentation, Processing algorithms: [«Extract by expression»](https://docs.qgis.org/latest/en/docs/user_manual/processing_algs/qgis/vectorselection.html), [«Buffer»](https://docs.qgis.org/latest/en/docs/user_manual/processing_algs/qgis/vectorgeometry.html).
- [QGIS User Manual, «Using processing algorithms from the console»](https://docs.qgis.org/latest/en/docs/user_manual/processing/console.html).
- Límite: IDEAS, servicio WFS «Unidades Administrativas», CC BY 4.0 (véase la lección 2.1).
