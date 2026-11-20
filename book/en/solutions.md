---
title: "Answers to the exercises"
---

## Lesson 1.1: The specification

[Back to the lesson](01-1-specification.md)

(en-sol-1-1-1)=
### Exercise 1

**Recall.** Which three numbers do we store for each building so we can compare homes near lines of different voltages?

:::{dropdown} Answer
The distance to the centre line, the half-width of the safety strip, and the distance to the edge of the strip.
:::

(en-sol-1-1-2)=
### Exercise 2

**Fill the gap.** What are the missing words? 'We take the homes and lines inside the municipality, and the roads and fire stations ______.'

:::{dropdown} Answer
With a margin around it.
:::

(en-sol-1-1-3)=
### Exercise 3

**Back to the prologue.** What happened in the same mountain municipalities in both 2018 reports?

:::{dropdown} Answer
The storm brought down lines and the power went off; trees and snow blocked the roads, and help couldn't get through.
:::

## Lesson 2.1: The Python Console and our first layer

[Back to the lesson](02-1-console.md)

(en-sol-2-1-1)=
### Exercise 1

Which layer method tells us whether QGIS managed to open the source?

:::{dropdown} Answer
`isValid()`. It returns `True` if the source opened, and `False` if it didn't.
:::

(en-sol-2-1-2)=
### Exercise 2

There's a mistake in this code: Lena doesn't get selected. Let's find it and fix it.

```python
municipalities.selectByExpression("CODIGO_INE = '33033'")
```

:::{dropdown} Answer
The code is a number, but in the expression it's in quotes, as if it were text. It's better to put the field name in double quotes and the whole expression in single quotes: `municipalities.selectByExpression('"CODIGO_INE" = 33033')`.
:::

(en-sol-2-1-3)=
### Exercise 3

Let's go back to lesson 1.1. Is the municipal boundary an input or an output of the tool? Why did we start with it?

:::{dropdown} Answer
An input. The user chooses the municipality, and we take everything else – lines, homes, roads, fire stations – inside its boundary, with a margin. Without the boundary, we have nothing to clip the rest of the data with.
:::

## Lesson 2.2: A script, the coordinate reference system and a working area with a margin

[Back to the lesson](02-2-script-area.md)

(en-sol-2-2-1)=
### Exercise 1

What's the name of the buffer algorithm when we run it with code through `processing.run`?

:::{dropdown} Answer
`"native:buffer"`.
:::

(en-sol-2-2-2)=
### Exercise 2

This call doesn't work. Let's find the mistake and fix it.

```python
area = processing.run("native:buffer", {
    "INPUT": boundary,
    "DISTANCE": "1 km",
    "OUTPUT": f"{folder}/lena_area.gpkg",
})["OUTPUT"]
```

:::{dropdown} Answer
`"DISTANCE"` has to be a number in the units of the layer's CRS, not text with units. In EPSG:25830, that's metres: `"DISTANCE": 1000` or `"DISTANCE": margin_m`.
:::

(en-sol-2-2-3)=
### Exercise 3

Let's go back to lesson 2.1. Why do we search for Lena by the code 33033 and not by name?

:::{dropdown} Answer
The name is written in capitals, `LENA` in Spanish and `LLENA` in Asturian, and one wrong letter gives an empty result. The INE code is unique to each municipality and doesn't depend on the language or the spelling.
:::
