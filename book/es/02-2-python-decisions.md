---
title: "Lección 2.2. Python toma decisiones y repite el trabajo: condición, bucle, función y módulo"
---

## Dónde lo dejamos

En la lección 2.1 aprendimos a guardar valores: números, texto, variables, listas y diccionarios. Por ejemplo, la lista de tensiones de las líneas, `voltages = [20, 45, 400]`, y el diccionario con los datos del municipio.

Pero de momento Python solo recuerda lo que le hemos dado. Por sí mismo no decide nada ni repite nada. Y en nuestro proyecto hay miles de edificios y decenas de líneas, y para cada uno hay que comprobar algo.

## Qué queremos entender

La pregunta de la lección: **¿cómo le decimos a Python «si pasa esto, haz aquello» y «haz lo mismo con cada uno»? ¿Y cómo reunimos ese trabajo en una sola orden, para no tener que escribirlo cada vez?**

Para eso hay cuatro cosas. Las vemos por orden, de la más sencilla a la más complicada:

1. **la condición:** hacer algo solo si se cumple una regla;
2. **el bucle:** repetir lo mismo con cada valor de una lista;
3. **la función:** una orden propia en la que reunimos nosotros mismos el trabajo;
4. **el módulo:** un conjunto de órdenes ya hecho que cargamos.

¿Cómo sabremos si nos hemos equivocado? Si Python responde algo distinto de lo que esperábamos o muestra un error. Por eso, al final, veremos cómo se leen los mensajes de error.

## Paso 1. Comparar: sí o no

Antes de decidir nada, Python tiene que saber hacer una pregunta de «¿sí o no?». Por ejemplo: «¿la tensión es mayor de 1 kV?».

Recordamos de la lección 1.2 que la alta tensión es todo lo que supera 1 kV. Se lo preguntamos a Python:

```python
voltage = 400
voltage > 1
```

Python responde: `True`.

Y para una línea de 0,4 kV, de baja tensión:

```python
voltage = 0.4
voltage > 1
```

Respuesta: `False`.

`True` significa «sí, es verdad», y `False`, «no, no es verdad». Una pregunta así no tiene otras respuestas. Este tipo de valor se llama **lógico**; en inglés, boolean, y en Python su tipo es `bool`.

Los signos de comparación:

| Signo | Qué significa |
|---|---|
| `>` | mayor que |
| `<` | menor que |
| `>=` | mayor o igual que |
| `<=` | menor o igual que |
| `==` | igual a |
| `!=` | distinto de |

**Aquí es fácil equivocarse.** En Python, la igualdad son dos signos: `==`. Un solo signo `=` ya lo conocemos de la lección 2.1: significa «guárdalo con este nombre». Comparemos:

- `code = 33033` es **guardar** el número con el nombre `code`;
- `code == 33033` es **preguntar** si `code` es igual al número 33033. La respuesta es `True` o `False`.

## Paso 2. La condición: si pasa esto, haz aquello

Ahora usamos esa respuesta para tomar una decisión. Escribimos: si la tensión es alta, que muestre una cosa, y si no, otra.

```python
voltage = 0.4
if voltage > 1:
    print("high voltage")
else:
    print("low voltage")
```

Respuesta: `low voltage`.

Vamos parte por parte.

- `if` significa «si». Detrás va una pregunta cuya respuesta es `True` o `False`.
- Los **dos puntos** `:` al final de la línea son obligatorios. Le dicen a Python: «a continuación viene lo que hay que hacer».
- La línea siguiente está desplazada a la derecha **cuatro espacios**. Ese desplazamiento se llama **sangría**. Indica qué líneas pertenecen a la condición. En otros lenguajes se usan llaves para esto; en Python, solo la sangría. Por eso, en Python la sangría no es un adorno, sino parte del significado.
- `print(...)` es una función que muestra un valor en la consola. En la lección 2.1, la consola mostraba la respuesta por sí misma. Dentro de una condición no lo hace, así que se lo pedimos expresamente: `print`.
- `else` significa «si no». Esta parte se ejecuta cuando la respuesta a la pregunta es `False`. También lleva dos puntos, y debajo, otra vez sangría.

**Cómo se escribe esto en la consola.** Después de la primera línea con dos puntos, la consola muestra `...` en lugar de `>>>`. Significa: «espero que sigas». Escribimos las demás líneas con su sangría. Al final pulsamos Enter otra vez, en una línea vacía, y se ejecuta todo.

Cambiamos `voltage = 0.4` por `voltage = 400` y lo volvemos a ejecutar. Respuesta: `high voltage`. El código es el mismo, pero la decisión es otra, porque ha cambiado el valor.

## Paso 3. El bucle: lo mismo con cada uno

Ahora no tenemos una línea, sino una lista. Queremos mirar cada tensión por turno.

```python
voltages = [20, 0.4, 400]
for voltage in voltages:
    print(voltage)
```

La respuesta son tres líneas: `20`, `0.4` y `400`.

Vamos por partes:

- `for voltage in voltages:` se lee casi como una frase normal: «para cada `voltage` de `voltages`».
- En cada vuelta, Python mete en `voltage` el siguiente valor de la lista. En la primera vuelta, 20; en la segunda, 0.4, y en la tercera, 400.
- La línea con sangría se ejecuta en cada vuelta.
- Esta repetición se llama **bucle**; en inglés, loop. Cada pasada por él es una **iteración**.

Aquí nos sirve el acuerdo de la lección 2.1 sobre el singular y el plural. `voltages` es la lista entera, y `voltage`, un solo valor en la vuelta actual. La línea `for voltage in voltages` explica por sí misma lo que pasa.

## Paso 4. El bucle y la condición juntos

Unimos los dos pasos. Vamos a contar cuántas líneas de alta tensión hay en la lista.

Primero pensamos cómo lo haríamos a mano. Cogemos una hoja de papel y escribimos «0». Recorremos la lista. Si una línea es de alta tensión, tachamos el número y escribimos uno más. Al final, en la hoja está la respuesta.

En código es lo mismo:

```python
count = 0
for voltage in voltages:
    if voltage > 1:
        count = count + 1
count
```

Respuesta: `2`.

Vamos por partes:

- `count = 0` es nuestra hoja de papel, que de momento tiene un cero. Una variable así se llama **contador**.
- El bucle recorre cada tensión.
- La condición está **dentro** del bucle, así que lleva sangría, y la línea que va debajo lleva **dos** sangrías, ocho espacios. Por la sangría se ve qué está dentro de qué: la condición dentro del bucle y la suma dentro de la condición.
- `count = count + 1` se lee de derecha a izquierda: «toma lo que hay ahora en `count`, súmale 1 y vuelve a guardarlo con el mismo nombre».
- La última línea, `count`, va sin sangría: ya está fuera del bucle. La escribimos después de una línea vacía, cuando el bucle ha terminado.

Comprobamos la respuesta a mano: 20, sí; 0.4, no; 400, sí. Dos líneas. Coincide.

## Paso 5. La función: una orden propia

La regla «más de 1 kV es alta tensión» nos hará falta muchas veces: para líneas de distintas fuentes, para comprobar los datos y en la propia herramienta. Podríamos escribir `voltage > 1` cada vez. Pero si algún día la regla cambia, habrá que buscarla por todo el código.

Es mejor escribir la regla una sola vez y ponerle un nombre. Eso es una **función**: una orden propia.

```python
def is_high_voltage(voltage):
    return voltage > 1
```

Python no responde nada: de momento solo hemos descrito la orden, pero no la hemos ejecutado. Ahora la ejecutamos:

```python
is_high_voltage(400)
is_high_voltage(0.4)
```

Respuestas: `True` y `False`.

Vemos la primera parte:

- `def` viene de define, «definir». Así empieza la descripción de una función.
- `is_high_voltage` es el nombre de la función. Se lee como una pregunta: «¿es alta tensión?». A las funciones que responden «sí» o «no» se les suele poner `is_` delante.
- `(voltage)`: entre paréntesis va el **parámetro**. Es lo que la función recibe como entrada. Dentro de la función se llama `voltage`.
- Los dos puntos y la sangría, como en la condición y en el bucle.
- `return` significa «devuelve». La función devuelve la respuesta a la pregunta `voltage > 1`.

Y la segunda:

- `is_high_voltage(400)` es una **llamada** a la función: el nombre y, entre paréntesis, un valor. Python mete 400 en el parámetro `voltage`, ejecuta las líneas de la función y devuelve la respuesta.

Es el mismo tipo de orden que `len(voltages)` y `print(...)` en la lección anterior. Solo que `len` y `print` ya vienen con Python, y `is_high_voltage` la hemos escrito nosotros.

Ahora usamos la función en nuestro contador:

```python
count = 0
for voltage in voltages:
    if is_high_voltage(voltage):
        count = count + 1
count
```

La respuesta es la misma: `2`. Pero ahora la regla vive en un solo sitio. Si cambia el límite, cambiamos una línea de la función.

## Paso 6. El módulo: conjuntos de órdenes ya hechos

No todo hay que escribirlo nosotros. Python tiene conjuntos de órdenes ya hechos para distintas tareas. Un conjunto así se llama **módulo**. Para usarlo, hay que cargarlo con la orden `import`.

Por ejemplo, el módulo `math`, para matemáticas:

```python
import math
math.pi
math.sqrt(16)
```

Respuestas: `3.141592653589793` y `4.0`.

Vamos por partes:

- `import math` significa «tráeme el módulo `math`». Sin esta línea, Python no sabe qué es `math`.
- `math.pi` es el número π del módulo `math`. El punto significa «dentro de»: el `pi` que está en `math`.
- `math.sqrt(16)` es la función `sqrt` (de square root, «raíz cuadrada») del módulo `math`. La raíz de 16 es 4.

El propio QGIS también es un conjunto de módulos. Todo lo que hagamos con las capas son órdenes de los módulos de QGIS. Por ejemplo, las herramientas de Processing están en un módulo que se llama justamente así: `processing`. Cómo están hechos los módulos de QGIS lo veremos en las lecciones 2.3 a 2.5.

## Paso 7. Cómo leer un mensaje de error

Habrá errores, y es normal. Lo importante es saber leerlos. Vamos a provocar un error a propósito: pedimos el cuarto valor de una lista que tiene tres.

```python
voltages[3]
```

Python responde más o menos así:

```text
Traceback (most recent call last):
  File "<input>", line 1, in <module>
IndexError: list index out of range
```

Parece aterrador, pero se lee fácil. **Lo leemos de abajo arriba.**

1. **La última línea es la más importante.** `IndexError` es el tipo de error: algo falla con un número de posición. Después de los dos puntos viene la explicación: «el índice de la lista está fuera de rango». Es decir, ese número no existe.
2. **Más arriba, dónde ha pasado:** `line 1`, la primera línea de lo que hemos ejecutado. Cuando el código sea largo, este número nos dirá en qué línea buscar.
3. **La primera línea,** `Traceback (most recent call last)`, significa «el camino hasta el error, con el último paso abajo». No hace falta leerla.

Así que hemos pedido un número que no existe. La lista tiene tres valores, con los números 0, 1 y 2. Lo corregimos a `voltages[2]`.

## Qué ha salido

Lo comparamos con la pregunta de la lección. Ahora sabemos:

| Qué hace falta | Cómo lo escribimos | Ejemplo |
|---|---|---|
| hacer una pregunta de «sí o no» | un signo de comparación | `voltage > 1` → `True` |
| hacer algo solo si es verdad | `if … :` y sangría | `if voltage > 1:` |
| hacer algo con cada uno | `for … in … :` y sangría | `for voltage in voltages:` |
| una orden propia | `def nombre(parámetro):` y `return` | `is_high_voltage(400)` |
| cargar órdenes ya hechas | `import` | `import math` |

Y sabemos que un mensaje de error se lee desde la última línea.

Volvamos a mirar el contador del paso 5. Ya es un pequeño cálculo de verdad. En lugar de tres números podría haber mil líneas, y el código no cambiaría. Así es como contaremos las líneas, los edificios y el tiempo de viaje en los capítulos del proyecto.

## Si algo falla

- **`SyntaxError: expected ':'`.** Faltan los dos puntos al final de una línea con `if`, `else`, `for` o `def`.
- **`IndentationError: expected an indented block`.** Después de una línea con dos puntos no hay sangría. Ponemos cuatro espacios.
- **`IndentationError: unexpected indent`.** Hay sangría donde no debería. Por ejemplo, un espacio de más al principio de una línea normal.
- **`SyntaxError` en una línea con `if`.** Muchas veces, en lugar de la comparación `==`, se ha escrito la asignación `=`.
- **La función no devuelve nada.** Si la llamamos y la respuesta sale vacía, comprobamos si tiene `return`. Sin él, la función devuelve `None`: «nada».
- **`NameError: name 'math' is not defined`.** Se nos ha olvidado `import math`.

## Tus datos

Si en otro país el límite de la alta tensión es distinto, cambia una sola línea: la de dentro de la función `is_high_voltage`. Todo lo demás se queda igual. Precisamente para eso hemos puesto la regla en una función.

## Resumen

**Conceptos nuevos**

- **Valor lógico:** `True` (sí) o `False` (no). *Ejemplo:* `400 > 1` → `True`.
- **Condición:** líneas que solo se ejecutan si la respuesta es `True`. *Ejemplo:* `if voltage > 1:`.
- **Sangría:** cuatro espacios al principio de una línea; indica que la línea está dentro de una condición, un bucle o una función. *Ejemplo:* `    print("high voltage")`.
- **Bucle:** la repetición de las mismas líneas con cada valor. *Ejemplo:* `for voltage in voltages:`.
- **Contador:** una variable que, dentro de un bucle, aumenta de 1 en 1. *Ejemplo:* `count = count + 1`.
- **Función:** una orden propia con su nombre, sus parámetros y su respuesta. *Ejemplo:* `def is_high_voltage(voltage):`.
- **Parámetro:** lo que la función recibe como entrada. *Ejemplo:* `voltage` en `is_high_voltage(voltage)`.
- **Módulo:** un conjunto de órdenes ya hecho; se carga con `import`. *Ejemplo:* `import math`.

**Código nuevo**

```python
def is_high_voltage(voltage):
    return voltage > 1

voltages = [20, 0.4, 400]
count = 0
for voltage in voltages:
    if is_high_voltage(voltage):
        count = count + 1
count

import math
math.pi
```

**Lo esencial**

- `=` es guardar con un nombre; `==` es preguntar «¿es igual?».
- Después de `if`, `else`, `for` y `def` van dos puntos, y debajo, sangría.
- Por la sangría se ve qué está dentro de qué.
- Una regla que hace falta muchas veces se escribe una sola vez, en una función.
- Los errores se leen de abajo arriba: la última línea dice qué ha pasado.

## Secretos del programador

1. **¿Se nos ha olvidado qué hace una función?** `help(len)` o `help(math.sqrt)` muestra su descripción en la propia consola, sin internet.
2. **¿Qué hay dentro de un módulo?** `dir(math)` muestra la lista de todo lo que contiene el módulo `math`. Así podemos encontrar la orden que necesitamos aunque no recordemos su nombre exacto.
3. **Comprobamos el bucle a mano.** Antes de fiarnos de la respuesta de un bucle, recorremos dos o tres valores en papel, como en el paso 4. Si en papel sale lo mismo que en Python, la lógica es correcta.

## Preguntas para un ingeniero

1. **¿Por qué es tan importante la sangría en Python?**

:::{dropdown} Respuesta
La sangría es la única forma de decirle a Python qué líneas pertenecen a una condición, a un bucle o a una función. Una sangría de más o de menos cambia el significado: una línea puede ejecutarse una sola vez en lugar de en cada vuelta, o no ejecutarse nunca.
:::

2. **¿Para qué escribir la función `is_high_voltage`, si basta con escribir `voltage > 1`?**

:::{dropdown} Respuesta
La regla vive en un solo sitio y tiene un nombre claro. Si el límite cambia, cambiamos una línea, en lugar de buscar `> 1` por todo el código. Y el código se lee mejor: `if is_high_voltage(voltage)` dice enseguida qué estamos comprobando.
:::

3. **En el contador del paso 4, la última línea, `count`, va sin sangría. ¿Qué pasa si la ponemos con sangría, dentro del bucle?**

:::{dropdown} Respuesta
Se ejecutará en cada vuelta, y veremos los valores intermedios del contador, no solo el total. Por eso, la línea que tiene que ejecutarse después del bucle se escribe sin sangría.
:::

## Ejercicios

1. **Recordar.** ¿En qué se diferencian `=` y `==`? [Solución](#es-sol-2-2-1)

2. **Corregir.** Este bucle tiene dos errores. Vamos a encontrarlos: [Solución](#es-sol-2-2-2)

   ```python
   for voltage in voltages
   print(voltage)
   ```

3. **Recordar la lección 1.1.** ¿Quiénes son los tres usuarios de nuestra herramienta? [Solución](#es-sol-2-2-3)

## La pregunta abierta

Ya sabemos escribir cálculos sencillos en Python. Pero nuestros datos no son listas de números, sino capas de QGIS: límites, líneas, edificios. ¿Cómo es QGIS por dentro, visto desde Python? ¿Qué son en el código una capa, un objeto y sus campos? Con esto empieza la lección 2.3.

## Notas y fuentes

1. Python Software Foundation, *The Python Tutorial*, apartado 4, «More Control Flow Tools»: https://docs.python.org/3/tutorial/controlflow.html.
2. Python Software Foundation, *The Python Tutorial*, apartado 6, «Modules»: https://docs.python.org/3/tutorial/modules.html.
3. Python Software Foundation, *The Python Tutorial*, apartado 8, «Errors and Exceptions»: https://docs.python.org/3/tutorial/errors.html.
