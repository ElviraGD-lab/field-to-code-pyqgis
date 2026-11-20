---
title: "Lección 2.1. La consola de Python y la primera capa"
---

## Dónde lo dejamos

En la lección 1.1 escribimos el pliego de condiciones. La primera entrada de la herramienta es el municipio. Mientras no tengamos su límite, no tenemos con qué recortar las líneas, las casas y las carreteras. Por eso el primer código del libro sirve para obtener el límite de Lena.

## Qué queremos entender en esta lección

¿Podemos cargar el límite de Lena desde un servicio abierto de Asturias con código, sin usar el ratón, y comprobar que de verdad es el suyo?

Qué esperamos:

1. El servicio devuelve una capa con todos los municipios de Asturias, y QGIS la acepta como capa válida.
2. La capa ya viene en el sistema de coordenadas EPSG:25830. Es el que usaremos para calcular distancias.
3. En la capa hay exactamente una Lena, y es un único polígono en el centro del sur de Asturias, en el límite con Castilla y León.
4. La superficie del polígono se acerca a la superficie oficial del municipio.

Cómo sabremos si nos hemos equivocado:

- la capa no se carga o no tiene ningún objeto;
- no aparece Lena o aparecen varias;
- el polígono está en otro sitio, por ejemplo junto al mar;
- la superficie es varias veces distinta de la oficial, no un pequeño porcentaje.

## Paso 1. Abrimos la consola

La consola de Python ya viene dentro de QGIS. La abrimos desde el menú **Plugins → Python Console** o con Ctrl+Alt+P. En la parte de abajo de la ventana aparece un panel con la línea de entrada `>>>`. Todo lo que escribimos después de `>>>` y confirmamos con Enter, Python lo ejecuta al momento.

Primero le preguntamos a QGIS qué proyecto está abierto y en qué sistema de coordenadas está:

```python
project = QgsProject.instance()
print(project.crs().authid())
```

Vamos línea a línea.

- `QgsProject` es el proyecto de QGIS: todas las capas, su orden y el sistema de coordenadas. Todo lo que empieza por `Qgs` es parte de QGIS, no de Python.
- `.instance()` significa «dame el proyecto que está abierto ahora». Siempre hay exactamente uno abierto.
- `project = ...` guarda el resultado en una variable. Una variable es un nombre con el que Python recuerda un valor. A partir de aquí escribimos `project` en lugar de toda la línea larga.
- `project.crs()` es el sistema de coordenadas del proyecto. `.authid()` es su código, por ejemplo `EPSG:4326`.
- `print(...)` muestra el valor en la consola.

El punto entre palabras significa «de este objeto, toma esto». `project.crs()` se lee «del proyecto, su sistema de coordenadas».

Lo que veamos depende del proyecto. En un proyecto nuevo, QGIS suele poner `EPSG:4326`, y si el proyecto ya se guardó en otro sistema, saldrá ese. Nosotros hemos visto `EPSG:25830`: nuestro proyecto ya estaba en ese sistema. En la lección 2.2 fijaremos el sistema del proyecto con código, para no depender de cómo estuviera el proyecto antes de empezar.

## Paso 2. Dónde está el límite

El límite de los municipios de Asturias está en un servicio abierto de IDEAS, la infraestructura de datos espaciales de Asturias. El servicio funciona con el estándar WFS: no devuelve una imagen, sino los propios objetos con sus atributos. Es justo lo que necesitamos, porque después vamos a recortar con ese límite.

Para llamar al servicio hacen falta dos cosas: la dirección y el nombre de la capa. El nombre de la capa lo sacamos de la descripción del servicio. Se puede abrir en el navegador con la dirección del servicio y el añadido `?service=WFS&request=GetCapabilities`. Ahí aparecen tres capas: `Asturias`, `Localidades` y `Concejos`. La que necesitamos es `UnidadesAdministrativas:Concejos`, los municipios.

```python
url = "https://sig.asturias.es/servicios/services/UnidadesAdministrativas/MapServer/WFSServer"
uri = f"url='{url}' typename='UnidadesAdministrativas:Concejos' srsname='EPSG:25830' version='2.0.0'"
```

- El texto entre comillas es una cadena. `url` es una variable con la cadena de la dirección.
- `uri` es una cadena con la descripción de la fuente: la dirección, el nombre de la capa, el sistema de coordenadas y la versión del estándar. Son los mismos datos que rellenamos con el ratón en la ventana para añadir una capa WFS.
- La letra `f` delante de las comillas significa: pon aquí los valores de las variables. En lugar de `{url}`, Python mete la dirección de la primera línea. Así no hay que escribir dos veces la dirección larga.
- `srsname='EPSG:25830'` le pide al servicio que devuelva las coordenadas directamente en EPSG:25830, en metros. La descripción del servicio dice que es su sistema nativo, así que no habrá reproyección.

Estas dos líneas no cargan nada. Solo hemos anotado de dónde sacar los datos.

## Paso 3. Cargamos la capa

```python
municipalities = QgsVectorLayer(uri, "municipalities", "WFS")
print(municipalities.isValid())
print(municipalities.featureCount())
project.addMapLayer(municipalities)
```

- `QgsVectorLayer(...)` crea una capa vectorial. Necesita tres valores: de dónde sacar los datos (`uri`), cómo llamar a la capa en el panel de capas (`"municipalities"`) y con qué leer la fuente (`"WFS"`, el proveedor de datos).
- `.isValid()` responde `True` (sí) si QGIS ha podido abrir la fuente y `False` (no) si no ha podido. Es la primera comprobación que hacemos con cualquier capa.
- `.featureCount()` dice cuántos objetos tiene la capa.
- `project.addMapLayer(...)` añade la capa al proyecto, y aparece en el mapa y en el panel de capas. Sin esta línea, la capa solo existe en la memoria de Python.

Vemos `True` y `78`. Asturias tiene 78 municipios, así que el servicio los ha devuelto todos.

## Paso 4. Qué campos tiene la capa

Antes de buscar Lena, miramos qué campos tienen los objetos.

```python
for field in municipalities.fields():
    print(field.name(), field.typeName())
```

Es nuestro primer bucle.

- `municipalities.fields()` es la lista de campos de la capa.
- `for field in ...:` significa «para cada campo de la lista, haz lo que viene debajo». En cada vuelta, la variable `field` contiene el campo siguiente.
- La segunda línea está desplazada a la derecha cuatro espacios. Esa sangría le dice a Python que la línea forma parte del bucle. Sin sangría, da error.
- `field.name()` es el nombre del campo, y `field.typeName()`, su tipo: número entero, cadena, etcétera.

En la consola, después de la primera línea aparece `...` en lugar de `>>>`. Python espera el resto del bucle. Después de la segunda línea pulsamos Enter otra vez en una línea vacía, y el bucle se ejecuta.

Vemos seis campos: `OBJECTID`, `CODIGO_INE`, `CONCEJO`, `CONCEYU`, `st_area_shape_` y `st_length_shape_`. `CONCEJO` es el nombre en español; `CONCEYU`, en asturiano, y `CODIGO_INE`, el código del municipio del Instituto Nacional de Estadística. `OBJECTID` es el número del objeto en la base de datos del servicio, y los dos últimos campos son la superficie y el perímetro que ha calculado el propio servicio.

El servicio llama a los tipos de campo a su manera: `xsd:int` es un número entero, y `xsd:double`, un número decimal. En los campos de texto `CONCEJO` y `CONCEYU`, el tipo nos ha salido vacío. No es un error: así describe el servicio las cadenas con longitud limitada. Que son texto se ve en los valores de la tabla de atributos.

## Paso 5. Encontramos Lena

Podemos buscar por el nombre o por el código. Aquí el nombre no es fiable: está escrito en mayúsculas (`LENA`), y en asturiano es `LLENA`. Con una sola letra mal, no encontramos nada. El código INE es único para cada municipio y no depende del idioma ni de cómo se escriba. El de Lena es 33033.

```python
municipalities.selectByExpression('"CODIGO_INE" = 33033')
print(municipalities.selectedFeatureCount())
iface.mapCanvas().zoomToSelected(municipalities)
```

- `selectByExpression(...)` selecciona objetos según una expresión. Es lo mismo que **Select Features by Expression** con el ratón, y la expresión se escribe igual: el nombre del campo entre comillas dobles y el valor sin comillas, porque es un número.
- Toda la expresión va entre comillas simples. Así Python sabe dónde empieza y dónde acaba la cadena, y las comillas dobles de dentro siguen siendo parte de la expresión.
- `selectedFeatureCount()` dice cuántos objetos hay seleccionados. Esperamos exactamente uno.
- `iface` es la propia ventana de QGIS: el mapa, los paneles, los menús. `iface.mapCanvas()` es el mapa, y `.zoomToSelected(...)` acerca el mapa a los objetos seleccionados de esta capa.

Vemos `1`, y el mapa se acerca al polígono seleccionado. Está en el centro del sur de Asturias, y el límite sigue la cordillera que lo separa de León.

```{figure} ../images/02-1-lena-selected.png
:alt: Lena municipality selected on the map of Asturian municipalities
Lena selected among the 78 municipalities of Asturias (IDEAS WFS, EPSG:25830).
```

## Paso 6. Comprobamos que es Lena

Un objeto seleccionado todavía no es una prueba. Miramos sus atributos y su superficie.

```python
lena = municipalities.selectedFeatures()[0]
print(lena["CONCEJO"], lena["CONCEYU"])
print(lena.geometry().area() / 1_000_000)
```

- `selectedFeatures()` es la lista de objetos seleccionados. `[0]` es el primero. En Python se cuenta desde cero, así que el primer elemento es el cero.
- `lena["CONCEJO"]` es el valor del campo `CONCEJO` de este objeto. Los corchetes con el nombre del campo son la forma de leer un atributo.
- `lena.geometry()` es la geometría del objeto, y `.area()`, su superficie en las unidades del sistema de coordenadas. En EPSG:25830 son metros cuadrados. Dividimos entre un millón y obtenemos kilómetros cuadrados. Python ignora los guiones bajos de `1_000_000`: solo sirven para leer mejor el número.

Vemos `LENA LLENA` y una superficie de 318,355 km².

## Qué ha salido

Lo comparamos con lo que esperábamos.

1. La capa se ha cargado: `isValid()` ha devuelto `True` y hay 78 objetos, tantos como municipios tiene Asturias. Coincide.
2. La capa viene directamente en EPSG:25830, y la superficie se calcula en metros sin reproyectar. Coincide.
3. Hay una sola Lena, y el polígono está donde esperábamos. Coincide.
4. La superficie oficial de Lena es de 315,5 km². Nos salen 318,4 km²: menos de un 1 % de diferencia. La descripción de la capa avisa de que el límite corresponde a una escala 1:25 000 y de que en algunos tramos es provisional. Para nuestra tarea basta: vamos a buscar casas a decenas de metros de las líneas, y el límite del municipio solo nos sirve para saber a qué municipio pertenece cada casa. Pero anotamos la diferencia: si una casa está justo en el límite, el error del límite puede asignarla al vecino.

Resultado de la lección: el límite de Lena está en el proyecto, y lo hemos comprobado de cuatro maneras. El paso siguiente es guardar estas líneas en un archivo de script, para no tener que teclearlas cada vez, y crear alrededor de Lena una zona con margen. Eso es la lección 2.2.

## Si algo falla

- **`isValid()` ha devuelto `False`.** Casi siempre es una errata en la dirección o en el nombre de la capa. Comparamos `uri` con el texto de la lección letra a letra. Si todo está bien, abrimos la descripción del servicio en el navegador: si ahí también hay un error, el servicio no funciona en este momento y lo intentamos más tarde.
- **`featureCount()` ha devuelto `-1`.** QGIS todavía no sabe cuántos objetos hay. Esperamos a que la capa se dibuje en el mapa y volvemos a ejecutar esa línea.
- **`selectedFeatureCount()` ha devuelto `0`.** Revisamos las comillas: el nombre del campo, entre dobles; toda la expresión, entre simples, y el número, sin comillas.
- **`IndentationError`.** En el bucle falta la sangría o no es igual en todas las líneas. La sangría se hace con cuatro espacios.
- **`NameError: name 'project' is not defined`.** Se ha reiniciado la consola y se han perdido las variables. Volvemos a hacer los pasos desde el principio.

## Tus datos

Para otro municipio de Asturias solo cambia el código del paso 5. Los códigos de los 78 municipios se pueden ver en la tabla de atributos de la capa: botón derecho sobre la capa, **Open Attribute Table**, columna `CODIGO_INE`. Por ejemplo, el de Cabrales es 33008.

```{figure} ../images/02-1-attribute-table.png
:alt: Attribute table of the municipalities layer with Lena selected
Attribute table of the municipalities layer: 78 features, Lena selected (CODIGO_INE 33033).
```

Para un municipio de fuera de Asturias este servicio no sirve: solo es asturiano. Hay servicios parecidos en otras comunidades autónomas y en el Instituto Geográfico Nacional (IGN), para toda España. Cambian la dirección, el nombre de la capa y el nombre del campo con el código, pero las líneas de código son las mismas.

## Resumen

### Conceptos nuevos

- **Consola de Python:** la línea de QGIS en la que el código se ejecuta nada más pulsar Enter. Ejemplo: `print(project.crs().authid())`.
- **Variable:** un nombre con el que Python recuerda un valor. Ejemplo: `project = QgsProject.instance()`.
- **Cadena:** texto entre comillas. Ejemplo: `"municipalities"`.
- **WFS:** el estándar de un servicio que devuelve objetos con atributos, no una imagen. Ejemplo: el servicio de municipios de IDEAS.
- **Proveedor de datos:** la parte de QGIS que sabe leer un tipo concreto de fuente. Ejemplo: `"WFS"`.
- **Bucle:** repetir las mismas líneas para cada elemento de una lista. Ejemplo: `for field in municipalities.fields():`.
- **Código INE:** el código numérico de un municipio, único para cada uno. Ejemplo: 33033, el de Lena.

### Código nuevo

```python
project = QgsProject.instance()
url = "https://sig.asturias.es/servicios/services/UnidadesAdministrativas/MapServer/WFSServer"
uri = f"url='{url}' typename='UnidadesAdministrativas:Concejos' srsname='EPSG:25830' version='2.0.0'"
municipalities = QgsVectorLayer(uri, "municipalities", "WFS")
print(municipalities.isValid())
project.addMapLayer(municipalities)
municipalities.selectByExpression('"CODIGO_INE" = 33033')
lena = municipalities.selectedFeatures()[0]
print(lena.geometry().area() / 1_000_000)
```

### Lo más importante

- Cualquier capa cargada con código se comprueba primero: `isValid()` y el número de objetos.
- Los objetos se buscan por el código, no por el nombre.
- Un objeto encontrado todavía no es una prueba: miramos los atributos, el sitio en el mapa y la superficie.

## Preguntas para un ingeniero

**¿Por qué le pedimos al servicio directamente EPSG:25830 en lugar de reproyectar después?**

:::{dropdown} Respuesta
EPSG:25830 es el sistema nativo del servicio, así que las coordenadas llegan sin reproyectar y sin errores nuevos. Cada reproyección es un sitio más donde podemos equivocarnos con el sistema de coordenadas. Cuantas menos reproyecciones, más fácil es comprobar el resultado.
:::

**El límite se aleja de la superficie oficial menos de un 1 %. ¿Cuándo empezará a importarnos ese error?**

:::{dropdown} Respuesta
Cuando un objeto esté justo en el límite. Una casa a pocos metros del límite puede caer en el municipio que no es, y entonces la contaremos donde no toca. Para el informe del municipio es un detalle, pero para una casa concreta junto al límite no lo es. Por eso en la lección 2.2 tomamos una zona con margen, y las casas junto al límite las revisaremos aparte más adelante.
:::

**¿Por qué no podemos dar por hecho que el servicio funcionará siempre?**

:::{dropdown} Respuesta
El servicio es de otra organización. Pueden cambiarlo de sitio, cambiar el nombre de la capa o desactivarlo durante un tiempo. Por eso la herramienta tiene que guardar una copia de los datos, y la dirección y el nombre de la capa tienen que ser entradas, no cadenas fijas metidas en el código. La copia la guardaremos en la lección 2.2, y la fecha de descarga la anotaremos en el informe de ejecución, en el último capítulo.
:::

## Ejercicios

**1.** ¿Qué método de la capa nos dice si QGIS ha podido abrir la fuente?

[Solución](#es-sol-2-1-1)

**2.** Este código tiene un error: Lena no se selecciona. Vamos a encontrarlo y corregirlo.

```python
municipalities.selectByExpression("CODIGO_INE = '33033'")
```

[Solución](#es-sol-2-1-2)

**3.** Recordemos la lección 1.1. ¿El límite del municipio es una entrada o un resultado de la herramienta? ¿Por qué hemos empezado por él?

[Solución](#es-sol-2-1-3)

## La pregunta abierta

El límite del municipio sigue las cordilleras y los ríos, pero los bomberos van por carretera. ¿La zona en la que buscamos parques de bomberos y carreteras tiene que coincidir con el límite del municipio o ser más amplia? ¿Cuánto más amplia?

## Notas y fuentes

- [IDEAS, Gobierno del Principado de Asturias. Servicio WFS «Unidades Administrativas», capa `Concejos`, EPSG:25830, licencia CC BY 4.0](https://sig.asturias.es/servicios/services/UnidadesAdministrativas/MapServer/WFSServer?service=WFS&request=GetCapabilities). Consultado el 20.11.2026.
- [Descripción de la capa `Concejos` (precisión de aproximadamente 1:25 000, parte de los límites provisionales; fuente de la geometría: IGN)](https://sig.asturias.es/servicios/rest/services/UnidadesAdministrativas/MapServer/2). Consultado el 20.11.2026.
- [Superficie de Lena, 315,5 km²: Turismo Asturias, «Lena»](https://www.turismoasturias.es/es/descubre/donde-ir/municipios/lena). Consultado el 20.11.2026.
- [PyQGIS Developer Cookbook, «Loading Layers»](https://docs.qgis.org/latest/en/docs/pyqgis_developer_cookbook/loadlayer.html). Consultado el 20.11.2026.
