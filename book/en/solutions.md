---
title: "Answers to the exercises"
---

## Lesson 1.1: Who the tool is for and what it should produce

[Back to the lesson](01-1-specification.md)

(en-sol-1-1-1)=
### Exercise 1

**Recall.** Who are the tool's three users?

:::{dropdown} Answer
The distribution company, fire crews and the 112 emergency service, and the municipality.
:::

(en-sol-1-1-2)=
### Exercise 2

**Fill the gap.** What are the missing words? 'Everything that depends on the place, we give ______; we don't write it ______.'

:::{dropdown} Answer
As input; inside the code.
:::

(en-sol-1-1-3)=
### Exercise 3

**Back to the prologue.** What two things happened in both 2018 reports?

:::{dropdown} Answer
The storm brought down lines and the power went off; trees and snow blocked the roads, and help couldn't get through.
:::

## Lesson 1.2: How we calculate and how we check

[Back to the lesson](01-2-calculation-and-checks.md)

(en-sol-1-2-1)=
### Exercise 1

**Recall.** Which three numbers do we store for each building so we can compare homes near lines of different voltages?

:::{dropdown} Answer
The distance to the centre line, the half-width of the safety strip, and the distance to the edge of the strip.
:::

(en-sol-1-2-2)=
### Exercise 2

**Fill the gap.** What are the missing words? 'We take the homes and lines inside the municipality, and the roads and fire stations ______.'

:::{dropdown} Answer
With a margin around it.
:::

(en-sol-1-2-3)=
### Exercise 3

**Back to lesson 1.1.** Why do we give the tool everything that depends on the place as input?

:::{dropdown} Answer
So that nothing is left in the code that only holds true for Lena. Otherwise, in another municipality, the tool will quietly calculate the wrong answer and show no error.
:::

## Lesson 2.1: Our first lines of Python: numbers, text, variables, lists and dictionaries

[Back to the lesson](02-1-python-values.md)

(en-sol-2-1-1)=
### Exercise 1

**Recall.** Which brackets create a list, and which create a dictionary?

:::{dropdown} Answer
A list uses square brackets `[ ]`, and a dictionary uses curly brackets `{ }`.
:::

(en-sol-2-1-2)=
### Exercise 2

**Fix it.** There's a mistake in this code. Let's find it:

```python
margin_km = 1,5
```

:::{dropdown} Answer
The decimal part is separated by a comma, so Python gets two numbers, 1 and 5. The correct version is `margin_km = 1.5`.
:::

(en-sol-2-1-3)=
### Exercise 3

**Back to lesson 1.2.** Which three numbers do we store for each building so we can compare homes near lines of different voltages?

:::{dropdown} Answer
The distance to the centre line, the half-width of the safety strip, and the distance to the edge of the strip.
:::

## Lesson 2.2: Python makes decisions and repeats work: conditions, loops, functions and modules

[Back to the lesson](02-2-python-decisions.md)

(en-sol-2-2-1)=
### Exercise 1

**Recall.** What's the difference between `=` and `==`?

:::{dropdown} Answer
`=` puts a value under a name; `==` asks whether two values are equal and answers `True` or `False`.
:::

(en-sol-2-2-2)=
### Exercise 2

**Fix it.** There are two mistakes in this loop. Let's find them:

```python
for voltage in voltages
print(voltage)
```

:::{dropdown} Answer
There's no colon at the end of the first line, and no indent on the second. The correct version is:

```python
for voltage in voltages:
    print(voltage)
```
:::

(en-sol-2-2-3)=
### Exercise 3

**Back to lesson 1.1.** Who are the three users of our tool?

:::{dropdown} Answer
The distribution company, fire crews and the 112 emergency service, and the municipality.
:::

## Lesson 2.3: Our workplace and a map of PyQGIS

[Back to the lesson](02-3-workplace-and-map.md)

(en-sol-2-3-1)=
### Exercise 1

**Recall.** Where on the diagram is the geometry: with the project, with the layer or with the feature?

:::{dropdown} Answer
With the feature, one for each.
:::

(en-sol-2-3-2)=
### Exercise 2

**Fix it.** This line in the editor should show the number of features, but nothing appeared in the console. Let's find the mistake:

```python
layer.featureCount()
```

:::{dropdown} Answer
In the editor, we need `print`, otherwise Python shows nothing. The correct version is:

```python
print(layer.featureCount())
```
:::

(en-sol-2-3-3)=
### Exercise 3

**Back to lesson 1.2.** What does the regulation measure the safety strip from: the centre line or the conductor?

:::{dropdown} Answer
From the conductor that the wind has blown furthest out. That's why our strip, measured from the centre line, is a lower bound.
:::

## Lesson 2.4: Going down the map: fields, features, attributes and geometry

[Back to the lesson](02-4-fields-features-geometry.md)

(en-sol-2-4-1)=
### Exercise 1

**Recall.** How do we get the value of the `name` field from the feature `feature`?

:::{dropdown} Answer
`feature["name"]`.
:::

(en-sol-2-4-2)=
### Exercise 2

**Fix it.** There's one mistake in this loop. Let's find it:

```python
for feature in layer.getFeatures:
    print(feature["name"])
```

:::{dropdown} Answer
`getFeatures` has no brackets. The correct version is `for feature in layer.getFeatures():`.
:::

(en-sol-2-4-3)=
### Exercise 3

**Back to lesson 2.1.** Which brackets create a dictionary, and which do we use to get a value out of it by its key?

:::{dropdown} Answer
We create it with curly brackets `{ }`, and we get a value with square brackets `[ ]`: `municipality["code"]`.
:::
