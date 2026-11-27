---
title: "Soluciones de los ejercicios"
---

## Lección 1.1. Para quién es la herramienta y qué tiene que devolver

[Volver a la lección](01-1-specification.md)

(es-sol-1-1-1)=
### Ejercicio 1

**Recordar.** Nombra los tres usuarios de la herramienta.

:::{dropdown} Solución
La distribuidora, los bomberos y el 112, y el ayuntamiento.
:::

(es-sol-1-1-2)=
### Ejercicio 2

**Completar.** Escribe las palabras que faltan: «Todo lo que depende del lugar se lo damos ______, no lo escribimos ______».

:::{dropdown} Solución
Como entrada; dentro del código.
:::

(es-sol-1-1-3)=
### Ejercicio 3

**Recordar el prólogo.** ¿Qué dos cosas pasaban en los dos partes de 2018?

:::{dropdown} Solución
El temporal rompía las líneas y se iba la luz; los árboles y la nieve cortaban las carreteras y la ayuda no podía llegar.
:::

## Lección 1.2. Cómo calculamos y cómo lo comprobamos

[Volver a la lección](01-2-calculation-and-checks.md)

(es-sol-1-2-1)=
### Ejercicio 1

**Recordar.** ¿Qué tres números guardamos para cada edificio para poder comparar casas junto a líneas de distinta tensión?

:::{dropdown} Solución
La distancia al eje de la línea, la semianchura de la franja de seguridad y la distancia al borde de la franja.
:::

(es-sol-1-2-2)=
### Ejercicio 2

**Completar.** Escribe las palabras que faltan: «Las casas y las líneas las tomamos dentro del municipio, y las carreteras y los parques de bomberos, ______».

:::{dropdown} Solución
Con un margen alrededor.
:::

(es-sol-1-2-3)=
### Ejercicio 3

**Recordar la lección 1.1.** ¿Por qué todo lo que depende del lugar se lo damos a la herramienta como entrada?

:::{dropdown} Solución
Para que en el código no quede nada que solo valga para Lena. Si no, en otro municipio la herramienta calculará mal en silencio y no mostrará ningún error.
:::

## Lección 2.1. Las primeras líneas en Python: números, texto, variables, listas y diccionarios

[Volver a la lección](02-1-python-values.md)

(es-sol-2-1-1)=
### Ejercicio 1

**Recordar.** ¿Qué signos crean una lista y cuáles un diccionario?

:::{dropdown} Solución
La lista, los corchetes `[ ]`; el diccionario, las llaves `{ }`.
:::

(es-sol-2-1-2)=
### Ejercicio 2

**Corregir.** Este código tiene un error. Vamos a encontrarlo:

```python
margin_km = 1,5
```

:::{dropdown} Solución
La parte decimal va separada con una coma, y Python obtendrá dos números, 1 y 5. Lo correcto es: `margin_km = 1.5`.
:::

(es-sol-2-1-3)=
### Ejercicio 3

**Recordar la lección 1.2.** ¿Qué tres números guardamos para cada edificio para poder comparar casas junto a líneas de distinta tensión?

:::{dropdown} Solución
La distancia al eje de la línea, la semianchura de la franja de seguridad y la distancia al borde de la franja.
:::

## Lección 2.2. Python toma decisiones y repite el trabajo: condición, bucle, función y módulo

[Volver a la lección](02-2-python-decisions.md)

(es-sol-2-2-1)=
### Ejercicio 1

**Recordar.** ¿En qué se diferencian `=` y `==`?

:::{dropdown} Solución
`=` guarda un valor con un nombre; `==` pregunta si dos valores son iguales y responde `True` o `False`.
:::

(es-sol-2-2-2)=
### Ejercicio 2

**Corregir.** Este bucle tiene dos errores. Vamos a encontrarlos:

```python
for voltage in voltages
print(voltage)
```

:::{dropdown} Solución
Faltan los dos puntos al final de la primera línea y falta la sangría en la segunda. Lo correcto es:

```python
for voltage in voltages:
    print(voltage)
```
:::

(es-sol-2-2-3)=
### Ejercicio 3

**Recordar la lección 1.1.** ¿Quiénes son los tres usuarios de nuestra herramienta?

:::{dropdown} Solución
La distribuidora, los bomberos y el 112, y el ayuntamiento.
:::

## Lección 2.3. El puesto de trabajo y el mapa de PyQGIS

[Volver a la lección](02-3-workplace-and-map.md)

(es-sol-2-3-1)=
### Ejercicio 1

**Recordar.** ¿Dónde está la geometría en el esquema: en el proyecto, en la capa o en el objeto de la capa?

:::{dropdown} Solución
En el objeto de la capa, una en cada uno.
:::

(es-sol-2-3-2)=
### Ejercicio 2

**Corregir.** Esta línea del editor debería mostrar el número de objetos de la capa, pero en la consola no ha aparecido nada. Vamos a encontrar el error:

```python
layer.featureCount()
```

:::{dropdown} Solución
En el editor hace falta `print`; si no, Python no muestra nada. Lo correcto es:

```python
print(layer.featureCount())
```
:::

(es-sol-2-3-3)=
### Ejercicio 3

**Recordar la lección 1.2.** ¿Desde dónde mide la norma la franja de seguridad: desde el eje de la línea o desde el conductor?

:::{dropdown} Solución
Desde el conductor en su máxima desviación por el viento. Por eso nuestra franja trazada desde el eje es una estimación por defecto.
:::

## Lección 2.4. Bajamos por el mapa: campos, objetos de la capa, atributos y geometría

[Volver a la lección](02-4-fields-features-geometry.md)

(es-sol-2-4-1)=
### Ejercicio 1

**Recordar.** ¿Cómo se saca el valor del campo `name` del objeto de la capa `feature`?

:::{dropdown} Solución
`feature["name"]`.
:::

(es-sol-2-4-2)=
### Ejercicio 2

**Corregir.** Este bucle tiene un error. Vamos a encontrarlo:

```python
for feature in layer.getFeatures:
    print(feature["name"])
```

:::{dropdown} Solución
A `getFeatures` le faltan los paréntesis. Lo correcto es: `for feature in layer.getFeatures():`.
:::

(es-sol-2-4-3)=
### Ejercicio 3

**Recordar la lección 2.1.** ¿Con qué signos se crea un diccionario y con cuáles se saca de él un valor por su clave?

:::{dropdown} Solución
Se crea con llaves, `{ }`, y el valor se saca con corchetes, `[ ]`: `municipality["code"]`.
:::
