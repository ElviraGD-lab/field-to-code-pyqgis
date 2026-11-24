---
title: "Lección 2.1. Las primeras líneas en Python: números, texto, variables, listas y diccionarios"
---

## Dónde lo dejamos

El pliego de condiciones está listo. Sabemos qué construimos y cómo lo vamos a comprobar. El siguiente paso del plan es cargar con código el límite del municipio.

Pero para escribir código hay que saber de qué está hecho. Por eso el capítulo 2 es una introducción. En esta lección vemos los primerísimos ladrillos de Python.

## Qué queremos entender

La pregunta de la lección: **¿cómo guarda Python aquello con lo que trabajamos (números, nombres, conjuntos de valores) y cómo llegamos a ello?**

Todo lo que haremos a partir de aquí se apoya en cinco cosas: los números, el texto, las variables, las listas y los diccionarios. Si las tenemos claras, cualquier código del libro se podrá leer por partes.

¿Cómo sabremos si nos hemos equivocado? Si Python responde algo distinto de lo que esperábamos o muestra un error. En los dos casos es una pista de dónde hemos entendido algo mal.

## Paso 1. Dónde escribir el código

El código lo escribiremos directamente en QGIS. Dentro ya hay un sitio donde Python ejecuta las líneas al momento. Se llama **consola de Python**.

La abrimos desde el menú **Plugins → Python Console** o con Ctrl+Alt+P. En la parte de abajo de la ventana aparece un panel. En su última línea está el signo `>>>`. Es el indicador: Python espera que escribamos algo.

Escribimos una línea después de `>>>` y pulsamos Enter. Python la ejecuta y enseguida muestra la respuesta en la línea de abajo.

Empezamos por lo más sencillo:

```python
400 + 20
```

Python responde: `420`.

Parece que no tiene nada de especial: una calculadora. Pero aquí se ve lo principal: **hemos escrito una línea, Python la ha ejecutado y ha respondido.** Así funciona todo el trabajo en la consola.

## Paso 2. Los números

Veamos cómo calcula Python.

```python
400 - 20
400 * 2
400 / 20
```

Escribimos cada línea por separado y pulsamos Enter. Las respuestas son `380`, `800` y `20.0`.

Vamos por partes:

- `-` es la resta; `*`, la multiplicación, y `/`, la división.
- La multiplicación se escribe con un asterisco, no con una cruz ni con un punto: es lo habitual en casi todos los lenguajes de programación.
- La última respuesta es `20.0`, no `20`. En Python, la división siempre da un **número decimal**, aunque la división sea exacta.

De aquí se deduce que en Python hay dos tipos de números:

1. **Los números enteros:** `400`, `20`, `33033`. En inglés, integer; en Python su tipo se llama `int`.
2. **Los números decimales:** `20.0`, `0.6`, `318.4`. En Python su tipo se llama `float`. El nombre viene de floating point, «coma flotante».

**Un detalle importante para nosotros.** En los documentos y las tablas en español, la parte decimal se separa con una coma: 0,6. En Python, en cambio, solo con un **punto**: `0.6`. ¿Qué pasa si escribimos una coma?

```python
0,6
```

Python no muestra ningún error. Responde `(0, 6)`. Nos ha entendido mal: ha pensado que son dos números distintos, 0 y 6. Y eso es peligroso: no hay error, pero el resultado está mal. **Por eso, en el código, la parte decimal va siempre con punto.**

## Paso 3. El texto

Además de números, necesitamos nombres: el nombre del municipio, el tipo de línea, el nombre de la capa. En Python, el texto se escribe entre comillas:

```python
"Lena"
```

Python responde: `'Lena'`.

Este texto entre comillas se llama **cadena**. En inglés, string; en Python, el tipo es `str`. Las comillas le dicen a Python: «esto no es una orden, es solo texto; no intentes ejecutarlo».

Las comillas pueden ser dobles, `"Lena"`, o simples, `'Lena'`: para Python es lo mismo. Python muestra él mismo las cadenas entre comillas simples. Lo importante es abrir y cerrar la cadena con el mismo tipo de comillas.

Comparemos dos líneas:

```python
"400"
400
```

La primera es un texto de tres caracteres: 4, 0, 0. La segunda es un número. Por fuera se parecen, pero para Python son cosas distintas. Con un número se puede calcular; con un texto, no. Vamos a comprobarlo:

```python
"400" + "20"
```

Respuesta: `'40020'`. Python no ha sumado los números: ha pegado dos trozos de texto. **Así que siempre miramos qué tenemos entre manos: un número o un texto.** Nos servirá en el capítulo 5: en los datos abiertos, la tensión de una línea muchas veces está escrita como texto, y habrá que convertirla en número.

## Paso 4. Las variables

Hasta ahora escribimos los valores cada vez de nuevo. Pero en el trabajo real, el mismo valor hace falta muchas veces. Por ejemplo, el código del municipio de Lena es 33033. Lo necesitaremos para el límite, para los edificios y para el informe.

Para no escribirlo cada vez, le damos un nombre al valor:

```python
code = 33033
```

Python no responde nada. Es lo normal: no hemos preguntado nada, solo hemos guardado el valor. Ahora preguntamos:

```python
code
```

Respuesta: `33033`.

Esto es una **variable**: un nombre con el que Python recuerda un valor. Vamos a ver la línea `code = 33033` por partes:

- a la izquierda, el nombre: `code`;
- el signo `=` significa «guárdalo con este nombre». No es el «igual» de las matemáticas, sino **asignar**: poner un valor bajo un nombre;
- a la derecha, el valor: `33033`.

Es cómodo leerla de derecha a izquierda: «el número 33033 lo guardamos con el nombre `code`».

Ahora podemos trabajar con las variables igual que con los valores:

```python
voltage = 400
voltage / 20
```

Respuesta: `20.0`.

**¿Por qué los nombres en inglés?** Todo el código del mundo se escribe en inglés, y en QGIS todos los nombres también son ingleses. Así cualquier ingeniero de cualquier país podrá leer nuestro código. En el libro siempre llamamos a una variable por lo que contiene: `code` es el código; `voltage`, la tensión, y `name`, el nombre.

Unas cuantas reglas para los nombres:

- letras latinas minúsculas, sin espacios;
- si hay varias palabras, las unimos con un guion bajo: `municipality_name`;
- un nombre no puede empezar por una cifra;
- las mayúsculas y las minúsculas son distintas: para Python, `Code` y `code` son dos nombres diferentes.

## Paso 5. Cadenas con valores insertados

Muchas veces hay que montar un texto a partir de trozos: por ejemplo, la etiqueta «Municipality: Lena, code 33033». Para eso Python tiene una forma de escribir muy práctica:

```python
name = "Lena"
f"Municipality: {name}, code {code}"
```

Respuesta: `'Municipality: Lena, code 33033'`.

Vamos por partes:

- la letra `f` delante de las comillas le dice a Python que en esta cadena habrá valores que insertar;
- entre las llaves, `{name}` y `{code}`, van nombres de variables. Python pone sus valores en su lugar.

Una cadena así se llama **f-string**, de la palabra format, «dar formato». La veremos en el capítulo 3, cuando montemos la dirección del servicio con los límites de los municipios.

## Paso 6. Las listas

Ahora imaginemos que no tenemos un valor, sino varios. Por ejemplo, las tensiones de las líneas que aparecen en el municipio: 20, 45 y 400 kV. Podríamos crear tres variables, pero es incómodo. Es mejor juntarlas:

```python
voltages = [20, 45, 400]
```

Esto es una **lista**: varios valores en orden, entre corchetes y separados por comas. En inglés, list.

Fijémonos en el nombre: `voltages`, en plural. Una tensión es `voltage`; una lista de tensiones, `voltages`. No es una regla de Python, sino un acuerdo para las personas: por el nombre se ve enseguida dónde hay un solo valor y dónde un conjunto.

¿Cómo sacamos un valor de la lista? Por su número:

```python
voltages[0]
```

Respuesta: `20`.

¿Por qué 0 y no 1? **En Python se cuenta desde cero.** El primer valor es el número 0; el segundo, el número 1, y el tercero, el número 2. Al principio cuesta acostumbrarse, así que conviene aprenderlo desde ya. El número dentro de la lista se llama **índice**.

```python
voltages[2]
voltages[-1]
```

Las dos veces la respuesta es `400`. El índice `2` es el tercer valor. Y `-1` significa «el último»: contamos desde el final. Es práctico cuando no sabemos cuántos valores tiene la lista.

¿Y cuántos tiene? Se lo preguntamos:

```python
len(voltages)
```

Respuesta: `3`. `len` viene de length, «longitud». Es una **función**: una orden de Python ya hecha. Las funciones se escriben así: el nombre y, entre paréntesis, aquello con lo que trabajan. Hablaremos más de las funciones en la lección siguiente.

## Paso 7. Los diccionarios

Una lista va bien cuando los valores significan lo mismo, por ejemplo, cuando todos son tensiones. Pero a veces un objeto tiene varias propiedades distintas. Un municipio tiene un nombre, un código y un margen alrededor del límite en metros. Se pueden meter en una lista: `["Lena", 33033, 1000]`. Pero dentro de una semana nadie se acordará de qué significa el tercer número.

Para eso existe el **diccionario**. En él, cada valor está bajo su propio nombre:

```python
municipality = {"name": "Lena", "code": 33033, "margin_m": 1000}
```

Vamos por partes:

- el diccionario se escribe entre **llaves**;
- dentro van pares separados por comas: el nombre, dos puntos y el valor;
- el nombre de cada par se llama **clave**; en inglés, key. Aquí las claves son cadenas, así que van entre comillas.

¿Cómo sacamos un valor? Por su clave, entre corchetes:

```python
municipality["code"]
```

Respuesta: `33033`.

Se parece a la tabla de atributos de QGIS. Un objeto tiene campos, y en cada campo hay un valor. Un diccionario es lo mismo: la clave hace de nombre del campo, y al lado está el valor.

Los diccionarios nos harán falta muy pronto. Cuando en el capítulo 3 ejecutemos herramientas de Processing con código, todos sus parámetros se pasan precisamente con un diccionario: «la capa de entrada es esta», «la distancia del búfer es tanto».

## Qué ha salido

Lo comparamos con la pregunta de la lección. Ahora sabemos cómo guarda Python aquello con lo que trabajamos:

| Qué guardamos | Cómo lo escribimos | Ejemplo |
|---|---|---|
| un número entero | solo cifras | `33033` |
| un número decimal | con **punto** | `0.6` |
| un texto | entre comillas | `"Lena"` |
| un valor con nombre | nombre `=` valor | `code = 33033` |
| varios valores en orden | entre corchetes | `[20, 45, 400]` |
| propiedades con nombre | entre llaves, clave: valor | `{"code": 33033}` |

Y sabemos cómo sacar un valor: de una lista, por su número, empezando por cero; de un diccionario, por su clave.

## Si algo falla

- **Sale `(0, 6)` en lugar de un número.** Hay una coma en un número decimal. La cambiamos por un punto: `0.6`.
- **`SyntaxError: unterminated string literal`.** La cadena se abre con unas comillas, pero no se cierra: `"Lena`. Añadimos las comillas que faltan. Las palabras en inglés del error significan «cadena sin terminar».
- **`NameError: name 'code' is not defined`.** Python no conoce ese nombre. O todavía no hemos ejecutado la línea `code = 33033`, o nos hemos equivocado en una letra, o se ha reiniciado la consola y se ha olvidado todo. Volvemos a ejecutar la línea con el `=`.
- **`IndexError: list index out of range`.** Hemos pedido un número que no existe: por ejemplo, `voltages[3]`, cuando hay tres valores (con los números 0, 1 y 2).
- **`KeyError: 'Code'`.** En el diccionario no hay esa clave. Muchas veces el problema es una mayúscula: la clave es `"code"` y hemos escrito `"Code"`.

Los mensajes de error siempre se leen **desde la última línea**: ahí está qué error es y de qué trata.

## Tus datos

Para otro municipio, cambiamos en el diccionario el nombre y el código. El código del municipio está en los datos abiertos de la estadística nacional. Para los municipios españoles, es el código INE.

## Resumen

**Conceptos nuevos**

- **Consola de Python:** el sitio de QGIS donde Python ejecuta una línea al momento. *Ejemplo:* `400 + 20` → `420`.
- **Número entero y número decimal:** `int` y `float`; la parte decimal va con punto. *Ejemplo:* `33033`, `0.6`.
- **Cadena:** texto entre comillas. *Ejemplo:* `"Lena"`.
- **Variable:** un nombre con el que Python recuerda un valor. *Ejemplo:* `code = 33033`.
- **f-string:** una cadena con valores insertados. *Ejemplo:* `f"code {code}"`.
- **Lista:** valores en orden entre corchetes; la numeración empieza en 0. *Ejemplo:* `voltages[0]`.
- **Diccionario:** pares «clave: valor» entre llaves. *Ejemplo:* `municipality["code"]`.
- **Función:** una orden ya hecha; el nombre y unos paréntesis. *Ejemplo:* `len(voltages)`.

**Código nuevo**

```python
code = 33033
name = "Lena"
f"Municipality: {name}, code {code}"
voltages = [20, 45, 400]
voltages[0]
len(voltages)
municipality = {"name": "Lena", "code": 33033, "margin_m": 1000}
municipality["code"]
```

**Lo esencial**

- El signo `=` significa «guárdalo con este nombre».
- Un número y un texto son cosas distintas: `400` y `"400"`.
- La parte decimal, solo con punto.
- En una lista se cuenta desde cero.
- El nombre de una variable dice lo que contiene; una lista, en plural.

## Secretos del programador

1. **¿No estamos seguros de qué tenemos entre manos?** Se lo preguntamos a Python: `type(voltage)`. Responderá `int`, `float`, `str`, `list` o `dict`. Es la forma más rápida de comprobar si tenemos un número o un texto.
2. **No hace falta teclear la línea otra vez.** En la consola, la flecha ↑ recupera las líneas anteriores. ¿Hemos encontrado un error? Pulsamos ↑, corregimos una letra y Enter.
3. **¿Qué paréntesis o corchetes van?** Ayuda una pista sencilla: los paréntesis `()` sirven para ejecutar una función; los corchetes `[]`, para una lista o para «sacar por número o por clave», y las llaves `{}`, para un diccionario o para insertar un valor en una f-string.

## Preguntas para un ingeniero

1. **¿Por qué Python responde `(0, 6)` a `0,6`, y por qué eso es peor que un error?**

:::{dropdown} Respuesta
Para Python, la coma separa valores, así que ve dos números, 0 y 6. No muestra ningún error, y el código sigue adelante con un valor equivocado. Un error lo veríamos enseguida, pero un valor equivocado puede pasar desapercibido hasta el resultado final.
:::

2. **¿Para qué un diccionario, si los mismos tres valores se pueden meter en una lista, `["Lena", 33033, 1000]`?**

:::{dropdown} Respuesta
En una lista, los valores solo se distinguen por su número, y dentro de una semana nadie se acordará de qué significa el tercero. En un diccionario, cada valor tiene un nombre: `"margin_m"` dice enseguida que es el margen en metros.
:::

3. **¿Por qué llamamos a la lista `voltages` y no `voltage`?**

:::{dropdown} Respuesta
Por el nombre se tiene que ver qué hay dentro. El singular es un solo valor; el plural, un conjunto de valores. A Python le da igual, pero a nosotros nos resulta más fácil leer el código.
:::

## Ejercicios

1. **Recordar.** ¿Qué signos crean una lista y cuáles un diccionario? [Solución](#es-sol-2-1-1)

2. **Corregir.** Este código tiene un error. Vamos a encontrarlo: [Solución](#es-sol-2-1-2)

   ```python
   margin_km = 1,5
   ```

3. **Recordar la lección 1.2.** ¿Qué tres números guardamos para cada edificio para poder comparar casas junto a líneas de distinta tensión? [Solución](#es-sol-2-1-3)

## La pregunta abierta

Ya sabemos guardar valores. Pero el código no es solo guardar. Necesitamos que Python tome decisiones por sí mismo («si la tensión es mayor de 1 kV, tomamos la línea») y que repita por sí mismo el trabajo para cada edificio. ¿Cómo se escribe eso? Con esto empieza la lección 2.2.

## Notas y fuentes

1. Python Software Foundation, *The Python Tutorial*, apartado 3, «An Informal Introduction to Python»: https://docs.python.org/3/tutorial/introduction.html.
2. QGIS Documentation, «Python Console»: https://docs.qgis.org/latest/en/docs/user_manual/plugins/python_console.html.
