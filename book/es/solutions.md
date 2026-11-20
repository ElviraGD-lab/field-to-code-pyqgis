---
title: "Soluciones de los ejercicios"
---

## Lección 1.1. El pliego de condiciones

[Volver a la lección](01-1-specification.md)

(es-sol-1-1-1)=
### Ejercicio 1

**Recordar.** ¿Qué tres números guardamos para cada edificio para poder comparar casas junto a líneas de distinta tensión?

:::{dropdown} Solución
La distancia al eje de la línea, la semianchura de la franja de seguridad y la distancia al borde de la franja.
:::

(es-sol-1-1-2)=
### Ejercicio 2

**Completar.** Escribe las palabras que faltan: «Las casas y las líneas las tomamos dentro del municipio, y las carreteras y los parques de bomberos, ______».

:::{dropdown} Solución
Con un margen alrededor.
:::

(es-sol-1-1-3)=
### Ejercicio 3

**Recordar el prólogo.** ¿Qué pasaba en los dos partes de 2018 en los mismos municipios de montaña?

:::{dropdown} Solución
El temporal rompía las líneas y se iba la luz; los árboles y la nieve cortaban las carreteras y la ayuda no podía llegar.
:::

## Lección 2.1. La consola de Python y la primera capa

[Volver a la lección](02-1-console.md)

(es-sol-2-1-1)=
### Ejercicio 1

¿Qué método de la capa nos dice si QGIS ha podido abrir la fuente?

:::{dropdown} Solución
`isValid()`. Devuelve `True` si la fuente se ha abierto y `False` si no.
:::

(es-sol-2-1-2)=
### Ejercicio 2

Este código tiene un error: Lena no se selecciona. Vamos a encontrarlo y corregirlo.

```python
municipalities.selectByExpression("CODIGO_INE = '33033'")
```

:::{dropdown} Solución
El código es un número, y en la expresión está entre comillas, como si fuera texto. Lo mejor es poner el nombre del campo entre comillas dobles y toda la expresión entre simples: `municipalities.selectByExpression('"CODIGO_INE" = 33033')`.
:::

(es-sol-2-1-3)=
### Ejercicio 3

Recordemos la lección 1.1. ¿El límite del municipio es una entrada o un resultado de la herramienta? ¿Por qué hemos empezado por él?

:::{dropdown} Solución
Es una entrada. El usuario elige el municipio, y todo lo demás (líneas, casas, carreteras, parques de bomberos) lo tomamos dentro de su límite, con margen. Sin el límite no tenemos con qué recortar el resto de los datos.
:::

## Lección 2.2. El script, el sistema de coordenadas y la zona con margen

[Volver a la lección](02-2-script-area.md)

(es-sol-2-2-1)=
### Ejercicio 1

¿Cómo se llama el algoritmo de búfer cuando lo ejecutamos con código mediante `processing.run`?

:::{dropdown} Solución
`"native:buffer"`.
:::

(es-sol-2-2-2)=
### Ejercicio 2

Esta llamada no funciona. Vamos a encontrar el error y corregirlo.

```python
area = processing.run("native:buffer", {
    "INPUT": boundary,
    "DISTANCE": "1 km",
    "OUTPUT": f"{folder}/lena_area.gpkg",
})["OUTPUT"]
```

:::{dropdown} Solución
`"DISTANCE"` tiene que ser un número en las unidades del sistema de coordenadas de la capa, no un texto con unidades. En EPSG:25830 son metros: `"DISTANCE": 1000` o `"DISTANCE": margin_m`.
:::

(es-sol-2-2-3)=
### Ejercicio 3

Recordemos la lección 2.1. ¿Por qué buscamos Lena por el código 33033 y no por el nombre?

:::{dropdown} Solución
El nombre está escrito en mayúsculas, `LENA` en español y `LLENA` en asturiano, y una sola letra mal da un resultado vacío. El código INE es único para cada municipio y no depende del idioma ni de cómo se escriba.
:::
