---
title: "Lección 2.3. El puesto de trabajo y el mapa de PyQGIS"
---

## Dónde lo dejamos

En las lecciones 2.1 y 2.2 vimos lo básico de Python: números y texto, variables, listas y diccionarios, la condición, el bucle y la función. Todo eso trataba de números y de texto.

Pero nuestros datos son capas de QGIS. Antes de escribir código para ellas, hacen falta dos cosas: un puesto de trabajo cómodo (dónde escribir el código y dónde guardar los archivos) y una visión de conjunto: cómo está hecho QGIS por dentro y cómo se llama cada parte.

## Qué queremos entender

La pregunta de la lección: **¿dónde escribimos el código y cómo está hecho QGIS por dentro: qué está dentro de qué y cómo se llama?**

Primero recordemos lo que ya sabemos hacer con el ratón:
- hay un proyecto abierto;
- en el proyecto hay capas;
- cada capa tiene una tabla de atributos, con filas y columnas;
- cada fila tiene una figura en el mapa: un punto, una línea o un polígono.

Lo que esperamos: que por dentro QGIS esté organizado igual, en el mismo orden. Si el código muestra otra cosa, es que hemos entendido algo mal en algún sitio, y lo averiguaremos.

## Paso 1. Creamos la carpeta y el proyecto

Todo lo que hagamos en el libro lo guardaremos en una sola carpeta. Así, dentro de un mes, encontraremos el proyecto, los scripts y los resultados.

1. Creamos en el ordenador la carpeta `pyqgis-project`.
2. Dentro, creamos la carpeta `scripts`, para los archivos de código.
3. Abrimos QGIS y creamos un proyecto nuevo: **Project → New**.
4. Lo guardamos enseguida en la carpeta `pyqgis-project`: **Project → Save As…**, con el nombre `project`. QGIS guardará el archivo `project.qgz`.

¿Para qué guardar un proyecto vacío? Más adelante, el código le preguntará al proyecto dónde está. Un proyecto sin guardar no tiene respuesta.

## Paso 2. Una capa de prácticas

Para ver cómo está hecha una capa, necesitamos una capa con puntos y con tabla. Los datos reales empezarán en el capítulo 3. Aquí nos haremos una capa pequeña nosotros mismos, en un minuto.

1. **Layer → Create Layer → New Temporary Scratch Layer…**
2. El nombre de la capa, `test_points`. El tipo de geometría, **Point**. El sistema de coordenadas, **EPSG:25830**.
3. En el apartado **New Field** escribimos el nombre del campo, `name`, elegimos el tipo **Text**, pulsamos **Add to Fields List** y después **OK**.

```{figure} ../images/ch02-3-scratch-layer-dialog.png
:alt: New Temporary Scratch Layer dialog with the name field in the fields list
New Temporary Scratch Layer: one text field, name, in EPSG:25830.
```


4. Activamos la edición (el icono del lápiz), cogemos la herramienta **Add Point Feature** y ponemos dos puntos en el mapa. Para cada uno, QGIS nos pedirá el valor del campo `name`: escribimos `A` para el primer punto y `B` para el segundo.
5. Guardamos los cambios y desactivamos la edición.

Abrimos la tabla de atributos: botón derecho sobre la capa → **Open Attribute Table**. Tiene dos filas y una columna: `name`.

```{figure} ../images/ch02-3-two-points.png
:alt: Layers panel with test_points and two points on the map
The practice layer test_points: two points on the map.
```

```{figure} ../images/ch02-3-attribute-table.png
:alt: Attribute table of test_points with one field, name, and rows A and B
Attribute table of test_points: one field (name), two features (A, B).
```


La capa es temporal: al cerrar el proyecto desaparecerá, y QGIS nos avisará. Para esta lección no es ningún problema.

## Paso 3. Dónde escribir el código: la consola y el editor

Abrimos Python: **Plugins → Python Console** (Ctrl+Alt+P). Abajo aparece el panel de la consola con la línea `>>>`.

En la consola, Python ejecuta **una línea al momento**. Es práctico para preguntar algo rápido. Pero si hay varias líneas, sobre todo con sangría, es incómodo escribirlas en la consola, y es fácil equivocarse.

Por eso el código de varias líneas lo escribimos en el **editor**. Se abre con el botón **Show Editor** del panel de la consola. A la derecha aparece una ventana parecida a un editor de texto sencillo.

En el editor trabajamos siempre igual:
1. Escribimos las líneas de código.
2. Guardamos el archivo con el botón **Save** en la carpeta `scripts`. El nombre, en inglés: por ejemplo, `lesson_2_3.py`.
3. Lo ejecutamos con el botón **Run Script** (el triángulo verde).
4. El resultado lo vemos en la consola, a la izquierda.

```{figure} ../images/ch02-3-console-editor.png
:alt: Python Console on the left and the code editor on the right
Python Console (left) and the code editor (right) after running the first line.
```


**Aquí hay una diferencia importante.** En la consola, cuando escribimos simplemente `code`, Python muestra el valor por sí mismo. En el editor no: Python ejecuta la línea y se calla. Para ver el resultado, en el editor siempre escribimos `print(...)`.

Vamos a comprobarlo. Escribimos en el editor una línea:

```python
print("QGIS is ready")
```

Guardamos y ejecutamos. En la consola aparece: `QGIS is ready`.

**Así que la regla es sencilla: una línea corta puede ir en la consola; todo lo demás, en el editor y con `print`.**

## Paso 4. Cómo está hecho QGIS por dentro

Ahora, lo principal. Miramos QGIS de arriba abajo y le ponemos nombre a cada parte.

```{mermaid}
flowchart TB
    QGIS["QGIS (application)"] --> WINDOW["Window: iface<br/>map canvas, Layers panel"]
    QGIS --> TOOLS["Tools: processing<br/>buffer, clip, ..."]
    QGIS --> PROJECT["Project: QgsProject<br/>.qgz file"]
    PROJECT --> LAYER["Layer: QgsVectorLayer<br/>many in a project"]
    LAYER --> SOURCE["Data source<br/>file, web service"]
    LAYER --> CRS["Coordinate reference system"]
    LAYER --> FIELDS["Fields<br/>table columns"]
    LAYER --> FEATURES["Features: QgsFeature<br/>table rows"]
    FEATURES --> ATTRS["Attributes<br/>values in the row cells"]
    FEATURES --> GEOM["Geometry: QgsGeometry<br/>one per feature: point, line, polygon"]
```

Recorremos el esquema de arriba abajo. Cada parte la comparamos con lo que vemos con el ratón.

1. **QGIS es el programa.** Dentro hay tres cosas distintas: la ventana, las herramientas y el proyecto.
2. **La ventana.** Es lo que vemos en la pantalla: el mapa, el panel de capas, los menús. En el código, la ventana se llama `iface`, de interface, «interfaz». A través de ella podemos saber, por ejemplo, qué capa está seleccionada con el ratón.
3. **Las herramientas.** El búfer, el recorte, la intersección: todo lo que hay en el panel **Processing Toolbox**. En el código llegamos a ellas a través de `processing`. Las veremos en la lección 2.6.
4. **El proyecto.** Es lo que se guarda en el archivo `.qgz`: qué capas están abiertas y en qué sistema de coordenadas está el proyecto. Siempre hay un solo proyecto abierto.
5. **La capa.** En un proyecto puede haber muchas. Cada capa tiene cuatro cosas:
   - **la fuente de datos:** de dónde saca la capa sus datos: un archivo del disco, un servicio web o la memoria, como nuestra capa temporal. Con el ratón: propiedades de la capa → **Source**;
   - **el sistema de coordenadas:** en nuestro caso, EPSG:25830;
   - **los campos:** las columnas de la tabla de atributos. Nosotros tenemos un campo, `name`;
   - **los objetos de la capa:** las filas de la tabla. Nosotros tenemos dos: los puntos A y B.
6. **Cada objeto de la capa tiene atributos y geometría.**
   - **Los atributos** son los valores de las celdas de su fila. Por ejemplo, `A` en el campo `name`.
   - **La geometría** es su figura en el mapa. Cada objeto tiene exactamente una: un punto, una línea o un polígono. En la tabla de atributos no se ve; se ve en el mapa.

**Aquí es fácil liarse: un campo y un atributo son cosas distintas.**
- **El campo** es la columna, igual para todas las filas. Por ejemplo, el campo `name`, de tipo «texto».
- **El atributo** es el valor de ese campo en una fila concreta. Por ejemplo, `A` en el primer punto.

El campo es el encabezado de la columna; el atributo, lo que está escrito en la celda.

**Así que la geometría no vive en la capa en general, sino en cada objeto de la capa: aparte de los atributos, pero en la misma fila.**

## Paso 5. Clase, objeto y método

Debajo de cada parte del esquema hay un nombre en inglés: `QgsProject`, `QgsVectorLayer`, `QgsFeature`, `QgsGeometry`. Son **clases**. Veamos con un ejemplo de campo qué significa eso.

- **Una clase** es la descripción de un tipo de cosa: qué tiene y qué se puede hacer con ella. Por ejemplo, «punto de levantamiento»: cualquier punto tiene un número y unas coordenadas, y se puede medir la distancia a otro punto. Es una descripción, no un punto concreto.
- **Un objeto** es una cosa concreta de ese tipo. El punto número 12, con sus coordenadas, es un objeto. Nuestro proyecto, `project.qgz`, es un objeto de la clase `QgsProject`. Nuestra capa, `test_points`, es un objeto de la clase `QgsVectorLayer`.
- **Un método** es una acción o una pregunta que se le puede hacer a un objeto: «¿Cómo te llamas?», «¿Cuántas filas tienes?». En el código, el método se escribe con un punto después del objeto y con paréntesis.

De aquí sale la gramática de cualquier línea de PyQGIS:

```text
object.method()
```

- a la izquierda del punto, a quién preguntamos;
- el punto significa «de este objeto»;
- a la derecha, qué preguntamos;
- los paréntesis significan «ejecuta». Sin paréntesis, el método no se ejecuta.

**Este es el segundo sitio donde es fácil liarse: la palabra «objeto» significa aquí dos cosas distintas.**
- **Un objeto de la capa** es una fila de la tabla de atributos: el punto A o el punto B. En inglés, feature; la clase es `QgsFeature`.
- **Un objeto** en Python es cualquier cosa concreta del código: un proyecto, una capa, un punto, un texto.

Para no confundirlos, en el libro a una fila de la tabla siempre la llamamos por su nombre completo: **objeto de la capa**.

## Paso 6. Comprobamos el esquema con código

Ahora bajamos por el esquema con código y vemos si coincide con lo que sabemos.

Primero, el proyecto. Lo que esperamos: que Python muestre dónde está nuestro archivo `project.qgz`.

Escribimos en el editor (la línea anterior se puede borrar):

```python
project = QgsProject.instance()
print(project.fileName())
```

Guardamos y ejecutamos. En la consola aparece la ruta completa del archivo, que termina en `pyqgis-project/project.qgz`.

Vamos por partes:
- `QgsProject` es la clase del proyecto.
- `.instance()` es un método que devuelve **precisamente el** proyecto que está abierto ahora. En inglés, instance significa «instancia». Siempre hay un solo proyecto abierto, y es el que obtenemos.
- `project = …` guarda ese proyecto con el nombre `project`, como en la lección 2.1.
- `project.fileName()` le pregunta al proyecto: «¿dónde está tu archivo?».
- `print(...)` muestra la respuesta en la consola. Al fin y al cabo, estamos en el editor.

Ahora bajamos un nivel, a la capa. Seleccionamos con el ratón la capa `test_points` en el panel de capas. Lo que esperamos: el nombre `test_points`, el sistema de coordenadas EPSG:25830 y 2 objetos de la capa.

Añadimos debajo de las dos primeras líneas:

```python
layer = iface.activeLayer()
print(layer.name())
print(layer.crs().authid())
print(layer.featureCount())
```

Guardamos y ejecutamos. En la consola:

```text
.../pyqgis-project/project.qgz
test_points
EPSG:25830
2
```

Vemos las líneas nuevas por partes:
- `iface.activeLayer()` le pregunta a la ventana: «¿qué capa está seleccionada ahora?». Active es «activa», la que está seleccionada con el ratón.
- `layer.name()` le pregunta a la capa su nombre.
- `layer.crs().authid()`: aquí bajamos **dos pasos por el esquema**. `layer.crs()` toma de la capa su sistema de coordenadas. También es un objeto. `.authid()` toma de él el código, `EPSG:25830`.
- `layer.featureCount()` le pregunta a la capa cuántos objetos de la capa tiene. Feature count es «número de objetos».

Cambiamos una cosa y miramos. Añadimos con el ratón un tercer punto, C, guardamos los cambios y volvemos a ejecutar el script. El último número pasará a ser `3`. El código no ha cambiado; han cambiado los datos.

## Por qué el código es así

- **Todas las clases de QGIS empiezan por `Qgs`** y se escriben con mayúscula: `QgsProject`, `QgsVectorLayer`, `QgsFeature`. Si vemos `Qgs`, es parte de QGIS.
- **Los métodos se escriben con minúscula**, y cada palabra siguiente, con mayúscula: `fileName`, `activeLayer`, `featureCount`. Es lo habitual en QGIS. Nuestros propios nombres los escribimos de otra manera, con guion bajo: `is_high_voltage`. Por eso, por la forma del nombre, se ve enseguida qué viene de QGIS y qué hemos escrito nosotros.
- **Los nombres de los métodos son palabras inglesas corrientes.** El nombre del archivo es `fileName`, y el número de objetos, `featureCount`. Si sabemos qué queremos preguntar, muchas veces podemos adivinar el nombre. Cómo comprobar lo que hemos adivinado lo vemos más abajo, en «Secretos del programador».
- **En la consola de QGIS, las clases `Qgs…` e `iface` ya están cargadas.** No hace falta escribir `import`, como con `math` en la lección 2.2. En un script independiente, que se ejecuta fuera del editor de QGIS, sí hará falta: llegaremos a eso en el capítulo 12.

## Qué ha salido

Lo comparamos con lo que esperábamos. Por dentro, QGIS está organizado igual que lo que vemos con el ratón:

| Con el ratón | Parte del esquema | En el código |
|---|---|---|
| el proyecto abierto | proyecto | `QgsProject.instance()` |
| la capa seleccionada en el panel de capas | ventana | `iface.activeLayer()` |
| el nombre de la capa | capa | `layer.name()` |
| el sistema de coordenadas en las propiedades de la capa | sistema de coordenadas | `layer.crs().authid()` |
| el número de filas de la tabla de atributos | objetos de la capa | `layer.featureCount()` |

Lo que esperábamos se ha confirmado. Y hemos ganado lo principal: ahora cada parte tiene un nombre, y sabemos qué está dentro de qué. Los campos, los atributos y la geometría, de momento, solo los hemos visto en el esquema. Los sacaremos con código en la lección siguiente.

## Si algo falla

- **En el editor, no aparece nada en la consola.** Se nos ha olvidado `print(...)`. En el editor, Python no muestra nada por sí mismo.
- **`project.fileName()` ha escrito una línea vacía.** El proyecto no está guardado. Lo guardamos (paso 1) y volvemos a ejecutar.
- **`AttributeError: 'NoneType' object has no attribute 'name'`.** En el panel de capas no hay ninguna capa seleccionada, y `iface.activeLayer()` ha devuelto «nada»: `None`. Seleccionamos una capa con el ratón.
- **En lugar del nombre sale `<built-in method name of QgsVectorLayer object at 0x…>`.** Se nos han olvidado los paréntesis: `layer.name` en lugar de `layer.name()`. Python ha mostrado el propio método en vez de ejecutarlo.
- **`NameError: name 'QgsProject' is not defined`.** Revisamos las mayúsculas y las minúsculas: `QgsProject`, no `QGSProject` ni `qgsproject`.

## Tus datos

En lugar de la capa de prácticas, podemos seleccionar cualquier capa vectorial nuestra: un GeoPackage, un shapefile, una capa de un servicio. El código no cambia. Solo cambian las respuestas: el nombre, el sistema de coordenadas y el número de objetos de la capa.

## Resumen

**Conceptos nuevos**
- **Consola:** el sitio donde Python ejecuta una línea al momento. *Ejemplo:* `>>>` en la parte de abajo de la ventana de QGIS.
- **Editor:** la ventana para el código de varias líneas; el código se guarda en un archivo y se ejecuta con un botón. *Ejemplo:* `scripts/lesson_2_3.py`.
- **Proyecto:** todo lo que se guarda en el archivo `.qgz`; siempre hay uno abierto. *Ejemplo:* `QgsProject.instance()`.
- **Ventana:** lo que se ve en la pantalla: el mapa y el panel de capas. *Ejemplo:* `iface.activeLayer()`.
- **Capa:** una fuente de datos, un sistema de coordenadas, unos campos y unos objetos de la capa. *Ejemplo:* `test_points`.
- **Campo:** una columna de la tabla de atributos. *Ejemplo:* `name`.
- **Objeto de la capa:** una fila de la tabla de atributos junto con su figura en el mapa. *Ejemplo:* el punto A.
- **Atributo:** el valor de un campo en una fila. *Ejemplo:* `A`.
- **Geometría:** la figura de un objeto de la capa en el mapa; cada uno tiene una. *Ejemplo:* un punto.
- **Clase:** la descripción de un tipo de cosa. *Ejemplo:* `QgsVectorLayer`.
- **Objeto:** una cosa concreta de ese tipo. *Ejemplo:* nuestra capa `test_points`.
- **Método:** una pregunta o una acción que se le puede hacer a un objeto; con un punto y con paréntesis. *Ejemplo:* `layer.name()`.

**Código nuevo**

```python
print("QGIS is ready")

project = QgsProject.instance()
print(project.fileName())

layer = iface.activeLayer()
print(layer.name())
print(layer.crs().authid())
print(layer.featureCount())
```

**Lo esencial**
- Una línea corta, en la consola. Todo lo demás, en el editor, con `print`, en un archivo de la carpeta `scripts`.
- QGIS: la ventana, las herramientas y el proyecto. En el proyecto, las capas. En la capa, la fuente, el sistema de coordenadas, los campos y los objetos de la capa. En el objeto de la capa, los atributos y una geometría.
- El campo es la columna; el atributo, el valor de la celda.
- Una línea de PyQGIS se lee así: `object.method()`, es decir, a quién, qué y ejecutar.

## Secretos del programador

1. **¿Se nos ha olvidado qué sabe hacer un objeto?** Escribimos en la consola `layer.` y pulsamos Tab: la consola mostrará los métodos de la capa. Si tecleamos las primeras letras, por ejemplo `layer.fea`, solo quedarán los que encajan.
2. **Comprobar lo que hemos adivinado del nombre de un método.** `help(layer.featureCount)` en la consola muestra qué hace el método y qué devuelve. Aquí va sin paréntesis después del nombre: no ejecutamos el método, sino que preguntamos por él.
3. **¿No recordamos la clase?** La respuesta de la consola nos da la pista. Si ha escrito `<QgsVectorLayer: 'test_points' (memory)>`, la clase de la capa es `QgsVectorLayer`, y la fuente, la memoria (memory). La descripción de la clase la buscamos por su nombre en la documentación de PyQGIS.

## Preguntas para un ingeniero

1. **¿Por qué el código de varias líneas lo escribimos en el editor y no en la consola?**

:::{dropdown} Respuesta
En la consola hay que teclear cada línea de nuevo y vigilar la sangría a mano, así que es fácil equivocarse. En el editor, el código está en un archivo: se puede corregir, guardar y volver a ejecutar entero. Dentro de un mes lo encontraremos en la carpeta `scripts`.
:::

2. **En la tabla de atributos no hay ninguna columna con las coordenadas de los puntos. Entonces, ¿dónde guarda QGIS dónde está cada punto?**

:::{dropdown} Respuesta
En la geometría del objeto de la capa. Se guarda en cada objeto de la capa, aparte de los atributos, por eso no se ve en la tabla, pero sí en el mapa.
:::

3. **En nuestra herramienta le preguntaremos a cada capa su sistema de coordenadas. ¿Por qué hay que hacerlo antes de los cálculos?**

:::{dropdown} Respuesta
La distancia a una línea en metros solo se calcula correctamente en un sistema de coordenadas cuya unidad sea el metro, como EPSG:25830. Si una capa está en otro sistema, la cifra saldrá mal, y el programa no mostrará ningún error. Por eso, primero comprobamos y luego calculamos.
:::

## Ejercicios

1. **Recordar.** ¿Dónde está la geometría en el esquema: en el proyecto, en la capa o en el objeto de la capa? [Solución](#es-sol-2-3-1)

2. **Corregir.** Esta línea del editor debería mostrar el número de objetos de la capa, pero en la consola no ha aparecido nada. Vamos a encontrar el error: [Solución](#es-sol-2-3-2)

   ```python
   layer.featureCount()
   ```

3. **Recordar la lección 1.2.** ¿Desde dónde mide la norma la franja de seguridad: desde el eje de la línea o desde el conductor? [Solución](#es-sol-2-3-3)

## La pregunta abierta

Ya sabemos cuántos objetos tiene la capa. Pero ¿qué hay en ellos? ¿Cómo los recorremos uno por uno, leemos el atributo `name` de cada punto y sacamos su geometría con las coordenadas? Con esto empieza la lección 2.4.

## Notas y fuentes

1. QGIS Project, *PyQGIS Developer Cookbook*, apartado «Introduction»: la consola de Python y el editor. https://docs.qgis.org/latest/en/docs/pyqgis_developer_cookbook/intro.html
2. QGIS Project, *QGIS User Guide*, apartado «QGIS Python console». https://docs.qgis.org/latest/en/docs/user_manual/plugins/python_console.html
3. QGIS Project, documentación de PyQGIS 4.2, clase `QgsProject`. https://qgis.org/pyqgis/4.2/core/QgsProject.html
