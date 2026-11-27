---
title: "Lesson 2.4: Going down the map: fields, features, attributes and geometry"
---

## Where we left off

In lesson 2.3, we set up our workplace: the `pyqgis-project` folder, with the project `project.qgz` and the `scripts` folder inside it, and we write code in the editor.

And we drew a map of QGIS. The application has the window, the tools and the project. The project holds layers. A layer has a data source, a coordinate reference system, fields and features. Every feature has attributes and one geometry.

So far, our code has only gone down as far as the layer: we found out its name, its coordinate reference system and its number of features.

## What we want to know

The question for this lesson: **how do we go all the way down the map: find out what fields a layer has, go through each feature and get at its attribute and its geometry?**

In our project, this is the key move. For each building, we'll take the number of dwellings (an attribute) and its outline (the geometry), so we can measure the distance to the line. The whole calculation works this way.

What we expect, in terms of meaning:
- there'll be as many fields as there are columns in the attribute table;
- there'll be as many features as there are points on the map;
- each point will have its own `name` value and its own X and Y coordinates in metres, because the layer is in EPSG:25830.

If we see something different, we've misunderstood something, and we'll work out what.

## Step 1: A layer that won't disappear

Our practice layer, `test_points`, is temporary: it lives in memory and disappears when we close QGIS. If QGIS has been restarted, the layer is gone. In that case, we create it again, as in lesson 2.3 (step 2).

So we don't have to do that every time, we'll save the layer to a file. That's what we do with all the project's results.

1. In the `pyqgis-project` folder, we create a folder called `data`.
2. We right-click the `test_points` layer → **Export → Save Features As…**
3. **Format**: **GeoPackage**. **File name**: `pyqgis-project/data/test_points.gpkg`. **Layer name**: `test_points`. The coordinate reference system: **EPSG:25830**.
4. The **Add saved file to map** box is ticked. We press **OK**.

A second `test_points` layer appears in the Layers panel, this time from the file. We remove the temporary one from the project: right-click → **Remove Layer**. We save the project.

Let's add another point, C, to the layer, so that there are three points. We turn on editing, place the point, type the letter `C` in `name`, save the edits and turn editing off.

Let's open the attribute table. We see something new: as well as `name`, there's a `fid` column, with the numbers 1, 2, 3. GeoPackage added it. It's the **feature ID**: in a GeoPackage file, every row must have its own number. We didn't create it; the file keeps track of it itself.

```{figure} ../images/ch02-4-fid-name.png
:alt: Attribute table of test_points from GeoPackage with fields fid and name
test_points saved to GeoPackage: the fid field is added by the file format.
```


## Step 2: The layer's fields

Let's start with the fields: what columns does the layer have? What we expect: two fields, `fid` and `name`.

In the editor, we create a new file, `lesson_2_4.py`, in the `scripts` folder. We select the `test_points` layer with the mouse and write:

```python
layer = iface.activeLayer()
for field in layer.fields():
    print(field.name(), field.typeName())
```

We run it. The console shows something like this:

```text
fid Integer64
name String
```

Let's go through it:
- `layer = iface.activeLayer()` takes the selected layer, as in lesson 2.3.
- `layer.fields()` asks the layer for all its fields. The method returns a set of fields. We can go through it with a `for` loop, as with a list in lesson 2.2.
- `for field in layer.fields():` means 'for each field among the layer's fields'. On each pass, `field` holds one field.
- `field.name()` asks the field for its name.
- `field.typeName()` asks the field for its type: what kind of values it holds. `Integer64` means whole numbers, and `String` means text. These are the same two kinds of value as in lesson 2.1: a number and a string.
- `print(a, b)` prints two values on one line, with a space between them.

Our expectation was confirmed: two fields. The type is written in English; in another version of QGIS, the type name may be written slightly differently, but the meaning is the same.

**A field only knows its name and its type. It holds no values: the values belong to the features.**

## Step 3: Features: going through the rows

Now let's go down to the rows of the table. What we expect: three rows, numbered 1, 2, 3.

Under the loop over the fields, we write:

```python
for feature in layer.getFeatures():
    print(feature.id())
```

We run it. The console shows:

```text
1
2
3
```

Let's go through it:
- `layer.getFeatures()` asks the layer for all its features. 'Get features' means exactly that.
- `for feature in …:` on each pass, `feature` holds one feature: one row of the table.
- `feature.id()` asks the feature for its number. That's the `fid` value from the table.

This is where the rule from lesson 2.1 about singular and plural helps again. `getFeatures` is all the features, and `feature` is one, on the current pass. The line `for feature in layer.getFeatures()` reads almost like an ordinary sentence: 'for each feature among the layer's features'.

Let's check by hand: the table has three rows, numbered 1, 2, 3. As expected.

## Step 4: An attribute: the value in a cell

Now let's get the value of the `name` field from each row. What we expect: `A`, `B`, `C`.

```python
for feature in layer.getFeatures():
    print(feature["name"])
```

We run it:

```text
A
B
C
```

Let's go through the main thing, `feature["name"]`:
- square brackets with a field name in quotation marks mean: 'give me the value of this field for this feature';
- this is exactly how we got a value out of a dictionary in lesson 2.1: `municipality["code"]`. There, the key was the name of a value in the dictionary; here, it's the name of a field.

This is an **attribute**: the value of one field for one feature. There's one `name` field for the whole layer, but each point has its own attribute.

## Step 5: Geometry: where the point is

The last level of the map is the geometry. We can't see it in the attribute table, but every feature has one. What we expect: each point has an X and a Y in metres. In EPSG:25830, across Asturias, X is hundreds of thousands of metres, and Y is about four million seven hundred thousand. But we placed our practice points anywhere, so the numbers could be anything.

First, let's look at the geometry as a whole:

```python
for feature in layer.getFeatures():
    geometry = feature.geometry()
    print(geometry.asWkt())
```

The console shows something like this (everyone's numbers will be different):

```text
Point (271050.1 4774520.5)
Point (271310.8 4774390.2)
Point (271180.4 4774705.9)
```

Let's go through it:
- `feature.geometry()` takes the feature's geometry. It's an object of the class `QgsGeometry`, the one at the very bottom of our map.
- `geometry = …` puts it under the name `geometry`, so we don't have to write it out in full.
- `geometry.asWkt()` asks the geometry to describe itself as text. WKT stands for Well-Known Text. It's a standard way of writing geometry that any GIS understands: the type of shape and its coordinates. `Point (X Y)` is a point with the coordinates X and Y.

Text is easy to read by eye, but we can't calculate with it. We need numbers. Let's get X and Y separately:

```python
for feature in layer.getFeatures():
    point = feature.geometry().asPoint()
    print(feature["name"], point.x(), point.y())
```

We run it:

```text
A 271050.1 4774520.5
B 271310.8 4774390.2
C 271180.4 4774705.9
```

Let's go through it:
- `feature.geometry().asPoint()` goes two steps down the map: from the feature, we take the geometry, and we ask the geometry to give itself to us as a point. 'As point' means exactly that.
- What we get back is a point with an X and a Y. `point.x()` and `point.y()` ask the point for its coordinates. These are numbers, so now we can calculate with them.
- `print(feature["name"], point.x(), point.y())` puts the attribute and the geometry of one feature on a single line.

Let's change one thing and see what happens. We move point A with the mouse (the **Vertex Tool**, then save the edits) and run the script again. A's X and Y change, while B and C stay as they were. So each feature really does have its own geometry.

**So we've gone all the way down the map: layer → feature → attribute and geometry → coordinates.**

## Step 6: The whole path in one script

Let's put it all together. The `lesson_2_4.py` file can end up like this:

```python
layer = iface.activeLayer()

for field in layer.fields():
    print(field.name(), field.typeName())

for feature in layer.getFeatures():
    point = feature.geometry().asPoint()
    print(feature.id(), feature["name"], point.x(), point.y())
```

The first loop goes through the columns of the table, and the second goes through the rows. We save it and run it again, to make sure everything works together.

## Why the code looks like this

- **Plural means a set; singular means one.** `fields()` is all the fields, and `field` is one field. `getFeatures()` is all the features, and `feature` is one. That's how names work throughout PyQGIS, and it's how we name our own variables too.
- **`get` at the start of a name** means 'get, hand over'. `getFeatures` means 'get the features'. QGIS has lots of methods like this.
- **`as` at the start of a name** means 'in the form of, as'. `asWkt` means 'as WKT text', and `asPoint` means 'as a point'. We can ask the same object to give itself to us in different forms. For lines, it'll be `asPolyline`, and for polygons, `asPolygon`; we'll look at them in lesson 2.5.
- **We get an attribute with square brackets**, like a value from a dictionary: `feature["name"]`. Round brackets would mean 'run a method', but we're not running anything; we're getting a value by its name.

## What we found

Let's compare this with what we expected:

| What we expected | What we got | How it looks in code |
|---|---|---|
| as many fields as columns | two: `fid` and `name` | `layer.fields()` |
| as many features as points | three, numbered 1, 2, 3 | `layer.getFeatures()`, `feature.id()` |
| each point has its own `name` | A, B, C | `feature["name"]` |
| each point has its own X and Y in metres | yes | `feature.geometry().asPoint()` |

Our expectation was confirmed. The only surprise was the `fid` field: the GeoPackage format added it when we saved the layer to a file.

## If something goes wrong

- **`KeyError: 'Name'`.** There's no field with that name. Most often, it's a capital letter: the field is called `name`, but we wrote `Name`. We check the field names with the loop from step 2.
- **A `TypeError` with the words `is not iterable`.** We forgot the brackets on the method in the loop line: `for feature in layer.getFeatures:`. Python can't go through a method until it has been run. We need `layer.getFeatures()`.
- **Nothing appeared in the console.** We forgot `print`, or the line with `print` has no indent, so it's outside the loop.
- **`AttributeError: 'NoneType' object has no attribute 'fields'`.** No layer is selected in the Layers panel. We select `test_points`.
- **An error on the line with `asPoint`.** The selected layer has lines or polygons, not points. `asPoint` only works for points.

## Your data

Any point layer of our own will do: survey stations, for example. The code doesn't change; we only need to replace `"name"` with the name of our own field. The loop from step 2 will show what fields there are.

For a layer with lines or polygons, step 5 will be different: that's the next lesson.

## Summary

**New terms**
- **Set of fields:** all of a layer's fields; we can go through it with a loop. *Example:* `layer.fields()`.
- **Field type:** what kind of values a field holds: whole numbers, decimals, text. *Example:* `String`.
- **Feature ID:** the row number that the file itself keeps track of. *Example:* `fid`, `feature.id()`.
- **Attribute:** the value of a field for one feature; we get it with square brackets. *Example:* `feature["name"]`.
- **WKT:** a standard way of writing geometry as text. *Example:* `Point (271050.1 4774520.5)`.

**New code**

```python
layer = iface.activeLayer()

for field in layer.fields():
    print(field.name(), field.typeName())

for feature in layer.getFeatures():
    point = feature.geometry().asPoint()
    print(feature.id(), feature["name"], point.x(), point.y())
```

**Key points**
- Fields are columns: a name and a type. The values don't belong to the fields, but to the features.
- We go through the features with a loop: `for feature in layer.getFeatures():`.
- An attribute: with square brackets, like a value from a dictionary: `feature["name"]`.
- Geometry: with a method, `feature.geometry()`. A point's coordinates: `asPoint()`, then `x()` and `y()`.
- We save the project's results to files rather than keeping them in temporary layers.

## Programmer's secrets

1. **Can't remember a field's name?** We don't guess: we print the names of all the fields with the loop from step 2. It's quicker and more reliable than opening the attribute table.
2. **Looking at geometry by eye.** `asWkt()` turns any geometry into text. If something seems odd about a geometry, we first print its WKT and see what's really there.
3. **Checking one feature, not all of them.** If a layer is large, we can put `break` (lesson 2.2) in the loop after the line with `print`. We'll see only the first feature, and we can quickly check that the code is reading what it should.

## Questions for an engineer

1. **The `name` field doesn't have the value `A`. So where is `A`?**

:::{dropdown} Answer
With the feature. A field is only a column: its name and type. The value `A` is an attribute of the first point, and we get it from that point: `feature["name"]`.
:::

2. **Why did we save the practice layer as a GeoPackage, when it worked as it was?**

:::{dropdown} Answer
A temporary layer disappears when we close QGIS. Everything the project needs has to sit in a file, so that it opens the same way tomorrow and a month from now. That's how it'll be with our real results too.
:::

3. **In the project, we'll measure the distance from each building to the line. What from this lesson will we need for that?**

:::{dropdown} Answer
A loop over the features of the buildings layer, an attribute (the number of dwellings, for example) and the geometry of each building. The distance is calculated from the geometry, and the attribute shows how many homes there are.
:::

## Exercises

1. **Recall.** How do we get the value of the `name` field from the feature `feature`? [Answer](#en-sol-2-4-1)

2. **Fix it.** There's one mistake in this loop. Let's find it: [Answer](#en-sol-2-4-2)

   ```python
   for feature in layer.getFeatures:
       print(feature["name"])
   ```

3. **Back to lesson 2.1.** Which brackets create a dictionary, and which do we use to get a value out of it by its key? [Answer](#en-sol-2-4-3)

## The open question

A point's geometry is simple: one pair of X and Y. But power lines are lines, and buildings are polygons. How does their geometry work, how do we find the length of a line and the area of a building, and why do we need to calculate them in EPSG:25830 rather than in degrees? That's where lesson 2.5 begins.

## Notes and sources

1. QGIS Project, *PyQGIS Developer Cookbook*, section 'Using Vector Layers': iterating over features, attributes, geometry. https://docs.qgis.org/latest/en/docs/pyqgis_developer_cookbook/vector.html
2. QGIS Project, PyQGIS 4.2 documentation, class `QgsFeature`. https://qgis.org/pyqgis/4.2/core/QgsFeature.html
3. QGIS Project, PyQGIS 4.2 documentation, class `QgsGeometry`. https://qgis.org/pyqgis/4.2/core/QgsGeometry.html
4. Open Geospatial Consortium, *Simple Feature Access*, the WKT format. https://www.ogc.org/standard/sfa/
