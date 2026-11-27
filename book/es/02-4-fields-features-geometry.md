---
title: "Lección 2.4. Bajamos por el mapa: campos, objetos de la capa, atributos y geometría"
---

## Dónde lo dejamos

En la lección 2.3 preparamos el puesto de trabajo: la carpeta `pyqgis-project`, con el proyecto `project.qgz` y la carpeta `scripts` dentro, y el código lo escribimos en el editor.

Y dibujamos el mapa de QGIS. En el programa están la ventana, las herramientas y el proyecto. En el proyecto están las capas. Cada capa tiene una fuente de datos, un sistema de coordenadas, unos campos y unos objetos de la capa. Cada objeto de la capa tiene atributos y una geometría.

Con código, de momento, solo hemos bajado hasta la capa: hemos sabido su nombre, su sistema de coordenadas y su número de objetos.

## Qué queremos entender

La pregunta de la lección: **¿cómo bajamos por el mapa hasta el fondo: saber qué campos tiene una capa, recorrer cada objeto de la capa y sacar su atributo y su geometría?**

En nuestro proyecto, este es el movimiento principal. Para cada edificio tomaremos el número de viviendas (un atributo) y su contorno (la geometría), para medir la distancia a la línea. Así está hecho todo el cálculo.

Lo que esperamos, por lógica:
- habrá tantos campos como columnas tenga la tabla de atributos;
- tantos objetos de la capa como puntos haya en el mapa;
- cada punto tendrá su propio valor de `name` y sus propias coordenadas X e Y en metros, porque la capa está en EPSG:25830.

Si vemos otra cosa, es que no hemos entendido algo, y lo averiguaremos.

## Paso 1. Una capa que no desaparezca

Nuestra capa de prácticas, `test_points`, es temporal: vive en la memoria y desaparece al cerrar QGIS. Si hemos reiniciado QGIS, la capa ya no está. En ese caso, la volvemos a crear como en la lección 2.3 (paso 2).

Para no tener que hacerlo cada vez, guardamos la capa en un archivo. Es lo que hacemos con todos los resultados del proyecto.

1. En la carpeta `pyqgis-project` creamos la carpeta `data`.
2. Botón derecho sobre la capa `test_points` → **Export → Save Features As…**
3. **Format**: **GeoPackage**. **File name**: `pyqgis-project/data/test_points.gpkg`. **Layer name**: `test_points`. El sistema de coordenadas: **EPSG:25830**.
4. La casilla **Add saved file to map**, activada. Pulsamos **OK**.

En el panel de capas ha aparecido una segunda capa `test_points`, esta vez desde el archivo. La temporal la quitamos del proyecto: botón derecho → **Remove Layer**. Guardamos el proyecto.

Añadimos a la capa un punto más, el C, para que haya tres. Activamos la edición, ponemos el punto, escribimos la letra `C` en `name`, guardamos los cambios y desactivamos la edición.

Abrimos la tabla de atributos. Vemos algo nuevo: además de `name`, ha aparecido la columna `fid`, con los números 1, 2 y 3. La ha añadido GeoPackage. Es el **número del objeto de la capa**: en un archivo GeoPackage, cada fila tiene obligatoriamente su propio número. No lo hemos creado nosotros: lo lleva el propio archivo.

```{figure} ../images/ch02-4-fid-name.png
:alt: Attribute table of test_points from GeoPackage with fields fid and name
test_points saved to GeoPackage: the fid field is added by the file format.
```


## Paso 2. Los campos de la capa

Empezamos por los campos: ¿qué columnas tiene la capa? Lo que esperamos: dos campos, `fid` y `name`.

Creamos en el editor un archivo nuevo, `lesson_2_4.py`, en la carpeta `scripts`. Seleccionamos con el ratón la capa `test_points` y escribimos:

```python
layer = iface.activeLayer()
for field in layer.fields():
    print(field.name(), field.typeName())
```

Lo ejecutamos. En la consola sale más o menos esto:

```text
fid Integer64
name String
```

Vamos por partes:
- `layer = iface.activeLayer()` toma la capa seleccionada, como en la lección 2.3.
- `layer.fields()` le pide a la capa todos sus campos. El método devuelve un conjunto de campos. Se puede recorrer con un bucle `for`, como la lista de la lección 2.2.
- `for field in layer.fields():` significa «para cada campo de los campos de la capa». En cada vuelta, `field` contiene un campo.
- `field.name()` le pregunta al campo su nombre.
- `field.typeName()` le pregunta al campo su tipo: qué valores guarda. `Integer64` son números enteros, y `String`, texto. Son los mismos dos tipos de valores de la lección 2.1: número y cadena.
- `print(a, b)` escribe dos valores en una sola línea, separados por un espacio.

Lo que esperábamos se ha confirmado: dos campos. El tipo está escrito en inglés; en otra versión de QGIS, el nombre del tipo puede escribirse algo distinto, pero significa lo mismo.

**Un campo solo conoce su nombre y su tipo. No contiene valores: los valores están en los objetos de la capa.**

## Paso 3. Los objetos de la capa: recorremos las filas

Ahora bajamos a las filas de la tabla. Lo que esperamos: tres filas, con los números 1, 2 y 3.

Debajo del bucle de los campos escribimos:

```python
for feature in layer.getFeatures():
    print(feature.id())
```

Lo ejecutamos. En la consola:

```text
1
2
3
```

Vamos por partes:
- `layer.getFeatures()` le pide a la capa todos sus objetos de la capa. Get features es «obtener los objetos».
- `for feature in …:`: en cada vuelta, `feature` contiene un objeto de la capa, una fila de la tabla.
- `feature.id()` le pregunta al objeto de la capa su número. Es precisamente el valor de `fid` de la tabla.

Aquí vuelve a sernos útil la regla de la lección 2.1 sobre el singular y el plural. `getFeatures` son todos los objetos; `feature`, uno solo en la vuelta actual. La línea `for feature in layer.getFeatures()` se lee casi como una frase normal: «para cada objeto de los objetos de la capa».

Lo comprobamos a mano: en la tabla hay tres filas, con los números 1, 2 y 3. Coincide.

## Paso 4. El atributo: el valor de la celda

Ahora sacamos de cada fila el valor del campo `name`. Lo que esperamos: `A`, `B` y `C`.

```python
for feature in layer.getFeatures():
    print(feature["name"])
```

Lo ejecutamos:

```text
A
B
C
```

Vemos lo principal, `feature["name"]`:
- los corchetes con el nombre del campo entre comillas significan: «dame el valor de este campo en este objeto de la capa»;
- es exactamente como sacábamos un valor de un diccionario en la lección 2.1: `municipality["code"]`. Allí, la clave era el nombre del valor en el diccionario; aquí, es el nombre del campo.

Esto es un **atributo**: el valor de un campo en un objeto de la capa. El campo `name` es uno para toda la capa, pero cada punto tiene su propio atributo.

## Paso 5. La geometría: dónde está el punto

El último nivel del mapa es la geometría. En la tabla de atributos no se ve, pero cada objeto de la capa la tiene. Lo que esperamos: que cada punto tenga una X y una Y en metros. En EPSG:25830, en Asturias, la X es de cientos de miles de metros, y la Y, de unos cuatro millones setecientos mil. Pero nuestros puntos de prácticas los pusimos en cualquier sitio, así que las cifras pueden ser cualesquiera.

Primero miramos la geometría entera:

```python
for feature in layer.getFeatures():
    geometry = feature.geometry()
    print(geometry.asWkt())
```

En la consola sale más o menos esto (las cifras serán distintas para cada uno):

```text
Point (271050.1 4774520.5)
Point (271310.8 4774390.2)
Point (271180.4 4774705.9)
```

Vamos por partes:
- `feature.geometry()` toma del objeto de la capa su geometría. Es un objeto de la clase `QgsGeometry`, el que está abajo del todo en nuestro mapa.
- `geometry = …` la guarda con el nombre `geometry`, para no escribir tanto.
- `geometry.asWkt()` le pide a la geometría que se describa con texto. WKT viene de Well-Known Text, «texto conocido por todos». Es la forma estándar de escribir una geometría, que entiende cualquier SIG: el tipo de figura y sus coordenadas. `Point (X Y)` es un punto con las coordenadas X e Y.

El texto es cómodo de leer a ojo, pero con él no se puede calcular. Necesitamos números. Sacamos la X y la Y por separado:

```python
for feature in layer.getFeatures():
    point = feature.geometry().asPoint()
    print(feature["name"], point.x(), point.y())
```

Lo ejecutamos:

```text
A 271050.1 4774520.5
B 271310.8 4774390.2
C 271180.4 4774705.9
```

Vamos por partes:
- `feature.geometry().asPoint()` son dos pasos hacia abajo por el mapa: del objeto de la capa tomamos la geometría, y a la geometría le pedimos que se entregue como punto. As point es «como punto».
- Como respuesta obtenemos un punto con su X y su Y. `point.x()` y `point.y()` le preguntan al punto sus coordenadas. Son números, y con ellos ya se puede calcular.
- `print(feature["name"], point.x(), point.y())` escribe en una sola línea el atributo y la geometría de un mismo objeto de la capa.

Cambiamos una cosa y miramos. Movemos con el ratón el punto A (herramienta **Vertex Tool**, y guardamos los cambios) y volvemos a ejecutar el script. Cambiarán la X y la Y de A, y las de B y C se quedarán como estaban. Así que, efectivamente, cada objeto de la capa tiene su propia geometría.

**Así que hemos recorrido el mapa hasta el fondo: capa → objeto de la capa → atributo y geometría → coordenadas.**

## Paso 6. Todo el camino en un solo script

Lo juntamos todo. El archivo `lesson_2_4.py` puede quedar así:

```python
layer = iface.activeLayer()

for field in layer.fields():
    print(field.name(), field.typeName())

for feature in layer.getFeatures():
    point = feature.geometry().asPoint()
    print(feature.id(), feature["name"], point.x(), point.y())
```

El primer bucle recorre las columnas de la tabla, y el segundo, las filas. Lo guardamos y lo volvemos a ejecutar, para asegurarnos de que todo funciona junto.

## Por qué el código es así

- **El plural es un conjunto, y el singular, uno solo.** `fields()` son todos los campos, y `field`, un campo. `getFeatures()` son todos los objetos de la capa, y `feature`, uno. Así están hechos los nombres en todo PyQGIS, y así llamamos también a nuestras variables.
- **`get` al principio del nombre** significa «obtener, entregar». `getFeatures` es «obtener los objetos de la capa». En QGIS hay muchos métodos así.
- **`as` al principio del nombre** significa «en forma de, como». `asWkt` es «en forma de texto WKT», y `asPoint`, «en forma de punto». Al mismo objeto se le puede pedir que se entregue de distintas formas. Para las líneas será `asPolyline`, y para los polígonos, `asPolygon`; los veremos en la lección 2.5.
- **El atributo se saca con corchetes**, como un valor de un diccionario: `feature["name"]`. Los paréntesis significarían «ejecuta el método», y aquí no ejecutamos nada: sacamos un valor por su nombre.

## Qué ha salido

Lo comparamos con lo que esperábamos:

| Qué esperábamos | Qué hemos obtenido | Cómo es en el código |
|---|---|---|
| tantos campos como columnas | dos: `fid` y `name` | `layer.fields()` |
| tantos objetos como puntos | tres, con los números 1, 2 y 3 | `layer.getFeatures()`, `feature.id()` |
| cada punto con su propio `name` | A, B y C | `feature["name"]` |
| cada punto con su propia X e Y en metros | sí | `feature.geometry().asPoint()` |

Lo que esperábamos se ha confirmado. Lo único inesperado ha sido el campo `fid`: lo añadió el formato GeoPackage cuando guardamos la capa en un archivo.

## Si algo falla

- **`KeyError: 'Name'`.** No hay ningún campo con ese nombre. Casi siempre es por una mayúscula: el campo se llama `name`, y hemos escrito `Name`. Los nombres de los campos los vemos con el bucle del paso 2.
- **`TypeError` con las palabras `is not iterable`.** Se nos han olvidado los paréntesis del método en la línea del bucle: `for feature in layer.getFeatures:`. Python no puede recorrer un método mientras no se haya ejecutado. Hace falta `layer.getFeatures()`.
- **En la consola no aparece nada.** Se nos ha olvidado `print`, o la línea con `print` va sin sangría, es decir, fuera del bucle.
- **`AttributeError: 'NoneType' object has no attribute 'fields'`.** En el panel de capas no hay ninguna capa seleccionada. Seleccionamos `test_points`.
- **Error en la línea con `asPoint`.** Está seleccionada una capa de líneas o de polígonos, no de puntos. `asPoint` solo funciona con puntos.

## Tus datos

Sirve cualquier capa de puntos propia: por ejemplo, unos puntos de levantamiento. El código no cambia; solo hay que cambiar `"name"` por el nombre de nuestro campo. Qué campos hay nos lo dirá el bucle del paso 2.

Con una capa de líneas o de polígonos, el paso 5 será distinto: eso es la lección siguiente.

## Resumen

**Conceptos nuevos**
- **Conjunto de campos:** todos los campos de una capa; se puede recorrer con un bucle. *Ejemplo:* `layer.fields()`.
- **Tipo de campo:** qué valores guarda un campo: números enteros, decimales, texto. *Ejemplo:* `String`.
- **Número del objeto de la capa:** el número de fila que lleva el propio archivo. *Ejemplo:* `fid`, `feature.id()`.
- **Atributo:** el valor de un campo en un objeto de la capa; se saca con corchetes. *Ejemplo:* `feature["name"]`.
- **WKT:** la forma estándar de escribir una geometría con texto. *Ejemplo:* `Point (271050.1 4774520.5)`.

**Código nuevo**

```python
layer = iface.activeLayer()

for field in layer.fields():
    print(field.name(), field.typeName())

for feature in layer.getFeatures():
    point = feature.geometry().asPoint()
    print(feature.id(), feature["name"], point.x(), point.y())
```

**Lo esencial**
- Los campos son columnas: un nombre y un tipo. Los valores no están en los campos, sino en los objetos de la capa.
- Los objetos de la capa se recorren con un bucle: `for feature in layer.getFeatures():`.
- El atributo, con corchetes, como un valor de un diccionario: `feature["name"]`.
- La geometría, con un método: `feature.geometry()`. Las coordenadas del punto, con `asPoint()` y luego `x()` e `y()`.
- Los resultados del proyecto se guardan en archivos, no se dejan en capas temporales.

## Secretos del programador

1. **¿No recordamos el nombre de un campo?** No lo adivinamos: escribimos los nombres de todos los campos con el bucle del paso 2. Es más rápido y más seguro que abrir la tabla de atributos.
2. **Ver la geometría a ojo.** `asWkt()` convierte cualquier geometría en texto. Si a una geometría le pasa algo raro, primero escribimos su WKT y miramos qué hay de verdad.
3. **Comprobar un solo objeto, no todos.** Si la capa es grande, en el bucle se puede poner `break` (lección 2.2) después de la línea con `print`. Veremos solo el primer objeto de la capa y comprobaremos rápido que el código lee lo que tiene que leer.

## Preguntas para un ingeniero

1. **El campo `name` no contiene el valor `A`. Entonces, ¿dónde está `A`?**

:::{dropdown} Respuesta
En el objeto de la capa. El campo es solo la columna: su nombre y su tipo. El valor `A` es el atributo del primer punto, y lo sacamos de él: `feature["name"]`.
:::

2. **¿Para qué hemos guardado la capa de prácticas en un GeoPackage, si ya funcionaba?**

:::{dropdown} Respuesta
La capa temporal desaparece al cerrar QGIS. Todo lo que necesita el proyecto tiene que estar en un archivo, para que mañana y dentro de un mes se abra igual. Así será también con nuestros resultados reales.
:::

3. **En el proyecto mediremos la distancia de cada edificio a la línea. ¿Qué de esta lección nos hará falta para ello?**

:::{dropdown} Respuesta
El bucle por los objetos de la capa de edificios, el atributo (por ejemplo, el número de viviendas) y la geometría de cada edificio. La distancia se calcula a partir de la geometría, y el atributo dice cuántas viviendas hay.
:::

## Ejercicios

1. **Recordar.** ¿Cómo se saca el valor del campo `name` del objeto de la capa `feature`? [Solución](#es-sol-2-4-1)

2. **Corregir.** Este bucle tiene un error. Vamos a encontrarlo: [Solución](#es-sol-2-4-2)

   ```python
   for feature in layer.getFeatures:
       print(feature["name"])
   ```

3. **Recordar la lección 2.1.** ¿Con qué signos se crea un diccionario y con cuáles se saca de él un valor por su clave? [Solución](#es-sol-2-4-3)

## La pregunta abierta

La geometría de un punto es sencilla: un par de X e Y. Pero las líneas eléctricas son líneas, y los edificios, polígonos. ¿Cómo es su geometría, cómo sabemos la longitud de una línea y la superficie de un edificio, y por qué hay que calcularlas en EPSG:25830 y no en grados? Con esto empieza la lección 2.5.

## Notas y fuentes

1. QGIS Project, *PyQGIS Developer Cookbook*, apartado «Using Vector Layers»: recorrido de los objetos de la capa, atributos y geometría. https://docs.qgis.org/latest/en/docs/pyqgis_developer_cookbook/vector.html
2. QGIS Project, documentación de PyQGIS 4.2, clase `QgsFeature`. https://qgis.org/pyqgis/4.2/core/QgsFeature.html
3. QGIS Project, documentación de PyQGIS 4.2, clase `QgsGeometry`. https://qgis.org/pyqgis/4.2/core/QgsGeometry.html
4. Open Geospatial Consortium, *Simple Feature Access*, formato WKT. https://www.ogc.org/standard/sfa/
