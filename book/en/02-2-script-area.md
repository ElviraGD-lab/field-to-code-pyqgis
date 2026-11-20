---
title: "Lesson 2.2: A script, the coordinate reference system and a working area with a margin"
---

## Where we left off

In lesson 2.1, we loaded a layer of all the municipalities of Asturias from the IDEAS service, found Lena by its code, 33033, and checked it: there's one, it's in the right place, and its area is 318.4 km² against an official 315.5 km². But all of this lives only in the open QGIS window. If we close QGIS, we'll have to type everything in again. And we don't have our own copy of the boundary yet: if the service doesn't respond tomorrow, our work grinds to a halt.

## What we want to find out in this lesson

Can we get two things that every chapter after this one needs, just by running a single script file?

- **Lena's boundary**: a copy in a file on our own disk;
- **the working area**: Lena with a margin on both sides of the boundary. That's where we'll take the lines, homes and roads from.

Why a margin? A municipal boundary doesn't stop conductors or trees. A line may run a few dozen metres beyond the boundary, while a home stands in Lena right next to it. If we clip the lines exactly to the boundary, that home drops out of the calculation. On top of that, as we've seen, the boundary itself isn't exact.

How big should the margin be? The safety strips along the lines are tens of metres wide (lesson 1.1). The error in a boundary at a scale of 1:25,000 is tens of metres too. We need a margin well above both of those, but not so big that we drag half the neighbouring municipalities into the calculation. We're taking 1 km. That's our own decision, not a rule, so it'll be a separate variable that's easy to change. For roads and fire stations, we need a much wider area, because fire crews come from neighbouring municipalities. We'll build that one separately, in the chapter on travel time.

What we expect:

1. The script runs in full with one click, and it doesn't depend on what CRS the project was in.
2. Two GeoPackage files appear in the project folder: Lena's boundary and the working area.
3. Each file contains exactly one polygon.
4. The working area extends beyond Lena all the way round, and it's larger than Lena by roughly the perimeter multiplied by the margin. A bit less than that estimate, in fact: in the inward bends of the boundary, the margin strips overlap.

How we'll know if we've got it wrong:

- the files don't appear, or they're empty;
- the working area is the same as Lena, or smaller;
- the size of the working area differs from the estimate several times over.

## Step 1: From the console to the editor

In lesson 2.1, we typed lines into the console, and each one ran straight away. For work we need to repeat, there's the script editor. It opens with the **Show Editor** button on the console panel. A window like a text editor appears on the right, with line numbers.

Code in the editor doesn't run by itself. We write all the lines, save the file and run it in full with the **Run Script** button (the green triangle). That's what a script is: a file of code that runs from the first line to the last.

If we haven't saved the QGIS project to our working folder yet, we do that now, and next to it we create a new script, `01_area.py`, with the **Save As…** button in the editor. The `.py` ending says it's a Python file. The `01` at the start gives the order: the next scripts will be `02_…`, `03_…`, so the file names show what runs after what.

```{figure} ../images/02-2-editor.png
:alt: Python Console with the script editor open next to it
The script editor (right) next to the Python Console (left). Run Script is the green triangle.
```

## Step 2: The start of the script: the project and the CRS

```python
import processing

project = QgsProject.instance()
project.setCrs(QgsCoordinateReferenceSystem("EPSG:25830"))
folder = project.homePath()
print(project.crs().authid(), folder)
```

- `import processing` brings in the Processing module: the same set of algorithms as in the **Processing Toolbox**. `import` means 'bring me this module'. In the console, it's often already loaded, but a script shouldn't count on that: it brings in everything it needs itself.
- `project.setCrs(...)` sets the project's CRS. In lesson 2.1, we only read it; now we set it ourselves.
- `QgsCoordinateReferenceSystem("EPSG:25830")` is a CRS given by its code. This one is ETRS89 / UTM 30N, in metres. It's the one we use for every distance in the book.
- `project.homePath()` is the folder where the QGIS project file is. That's where the script will put its files. We don't type the folder path by hand: that way, the script works on any computer, wherever the project is.
- `print(...)` with two values separated by a comma prints them on one line, with a space between them.

We run it. We see `EPSG:25830` and the path to our working folder.

## Step 3: The municipalities layer, as in lesson 2.1

```python
url = "https://sig.asturias.es/servicios/services/UnidadesAdministrativas/MapServer/WFSServer"
uri = f"url='{url}' typename='UnidadesAdministrativas:Concejos' srsname='EPSG:25830' version='2.0.0'"
municipalities = QgsVectorLayer(uri, "municipalities", "WFS")
print(municipalities.isValid(), municipalities.featureCount())
```

We already know these lines. There's one difference: we don't add the municipalities layer to the project. The script only needs it as a source, and what we need on the map is Lena.

## Step 4: Lena's boundary, in a file of its own

Instead of the selection from lesson 2.1, we'll use a Processing algorithm that writes the features it finds straight to a file. It's **Extract by Expression**.

```python
boundary = processing.run("native:extractbyexpression", {
    "INPUT": municipalities,
    "EXPRESSION": '"CODIGO_INE" = 33033',
    "OUTPUT": f"{folder}/lena_boundary.gpkg",
})["OUTPUT"]
```

This is our first call to Processing from code. Let's take it apart.

- `processing.run(...)` runs an algorithm. It needs two things: the algorithm's name and its parameters.
- `"native:extractbyexpression"` is the algorithm's name. We can find it in the **Processing Toolbox**: if we hover the mouse over an algorithm, its name shows in the tooltip.
- The parameters go in the curly brackets `{ ... }`. This is a dictionary: 'name: value' pairs separated by commas. The parameter names are the same fields as in the algorithm's window, only in English and in capitals.
- `"INPUT"` is the input layer. We pass the `municipalities` layer itself.
- `"EXPRESSION"` is the same expression as in lesson 2.1.
- `"OUTPUT"` is where to write the result: the file `lena_boundary.gpkg` in the project folder. The `.gpkg` extension means GeoPackage: a single file that holds a layer with its geometry, attributes and CRS.
- We can spread a dictionary over several lines: it's easier to read that way. Python knows the call isn't finished until all the brackets are closed.
- `processing.run` returns a dictionary of results. `["OUTPUT"]` takes the path to the written file from it. We put that path in the variable `boundary`.

That's our copy of the boundary. The chapters that follow will take Lena from this file, not from the service.

## Step 5: The working area: a buffer

```python
margin_m = 1000
area = processing.run("native:buffer", {
    "INPUT": boundary,
    "DISTANCE": margin_m,
    "OUTPUT": f"{folder}/lena_area.gpkg",
})["OUTPUT"]
```

- `margin_m = 1000` is the margin in metres. The `m` in the name reminds us what units the number is in. It's metres here because the layer is in EPSG:25830. In a CRS in degrees, the same number would mean 1,000 degrees.
- `"native:buffer"` is the **Buffer** algorithm.
- `"INPUT": boundary`: the input is the path to the file we've just written. Processing accepts either a layer or a path to a file.
- `"DISTANCE": margin_m` is the width of the buffer. We pass the variable, not a number: if we want a different margin, we change one line.
- We don't give the buffer's other parameters (the number of segments on curves, the style of the ends and corners). Processing then uses the default values, the same ones that are set in the algorithm's window.

## Step 6: Showing the result and checking it

```python
boundary_layer = QgsVectorLayer(boundary, "lena_boundary", "ogr")
area_layer = QgsVectorLayer(area, "lena_area", "ogr")
project.addMapLayer(area_layer)
project.addMapLayer(boundary_layer)
```

- `"ogr"` is the data provider for files: GeoPackage, Shapefile, GeoJSON and many others. For the service in lesson 2.1, it was `"WFS"`; for a file, it's `"ogr"`.
- We add the working area first and the boundary second. The layer added last sits on top, so Lena's boundary will show over the working area.

And the check:

```python
for layer in [boundary_layer, area_layer]:
    feature = next(layer.getFeatures())
    print(layer.name(), layer.featureCount(), feature.geometry().area() / 1_000_000)
```

- `[boundary_layer, area_layer]` is a list of two layers. Square brackets create a list.
- The loop goes through both layers and, for each one, prints its name, its number of features and its area in km².
- `layer.getFeatures()` hands over the layer's features one at a time. `next(...)` takes the first. We expect exactly one feature in each layer, so the first is enough.

## The whole script

```python
import processing

project = QgsProject.instance()
project.setCrs(QgsCoordinateReferenceSystem("EPSG:25830"))
folder = project.homePath()
print(project.crs().authid(), folder)

url = "https://sig.asturias.es/servicios/services/UnidadesAdministrativas/MapServer/WFSServer"
uri = f"url='{url}' typename='UnidadesAdministrativas:Concejos' srsname='EPSG:25830' version='2.0.0'"
municipalities = QgsVectorLayer(uri, "municipalities", "WFS")
print(municipalities.isValid(), municipalities.featureCount())

boundary = processing.run("native:extractbyexpression", {
    "INPUT": municipalities,
    "EXPRESSION": '"CODIGO_INE" = 33033',
    "OUTPUT": f"{folder}/lena_boundary.gpkg",
})["OUTPUT"]

margin_m = 1000
area = processing.run("native:buffer", {
    "INPUT": boundary,
    "DISTANCE": margin_m,
    "OUTPUT": f"{folder}/lena_area.gpkg",
})["OUTPUT"]

boundary_layer = QgsVectorLayer(boundary, "lena_boundary", "ogr")
area_layer = QgsVectorLayer(area, "lena_area", "ogr")
project.addMapLayer(area_layer)
project.addMapLayer(boundary_layer)

for layer in [boundary_layer, area_layer]:
    feature = next(layer.getFeatures())
    print(layer.name(), layer.featureCount(), feature.geometry().area() / 1_000_000)
```

## What we got

The console printed:

```text
EPSG:25830 /Users/me/lena-project
True 78
lena_boundary 1 318.35508793067146
lena_area 1 410.8970065571663
```

Let's compare this with what we expected.

1. The script ran in full in one go, and the project's CRS is EPSG:25830. As expected.
2. `lena_boundary.gpkg` and `lena_area.gpkg` appeared in the project folder. As expected.
3. Each file has one feature. As expected.
4. The boundary's area is the same as in lesson 2.1: 318.4 km². So nothing happened to Lena when we wrote it to a file. The working area covers 410.9 km².

Let's check that last number with an estimate. A 1 km buffer adds roughly the perimeter multiplied by the width: 95.3 km × 1 km = 95.3 km² (the perimeter is the `st_length_shape_` field from lesson 2.1). At the outward corners, the strip is rounded and adds about π × 1² ≈ 3 km² more. That gives an estimate of about 417 km². We got 410.9 km², 6 km² less. That's just as it should be: in the inward bends of the boundary, the strips from the two sides overlap, and the same area isn't counted twice. The difference is 1.5% of the estimate, so the order of magnitude is right. The working area has come out just as we planned it.

**So:** we have the script `01_area.py`, our own file with Lena's boundary, and a working area with a 1 km margin. Next, we'll load the power lines into this area. That's chapter 3.

```{figure} ../images/02-2-area.png
:alt: Lena boundary over the working area with a 1 km margin
Lena boundary (outline) over the working area with a 1 km margin (EPSG:25830).
```

## If something goes wrong

- **The folder path is empty, and no files have appeared.** The QGIS project hasn't been saved yet, so it has no folder. We save the project and run the script again.
- **Two more layers on the map after every run.** The script adds the layers afresh each time. We remove the old ones by hand in the Layers panel. In chapter 11, 'From script to tool', this problem will go away by itself: the tool will hand back its result rather than adding layers to the project.
- **An error when writing a file that already exists.** If the file is open in another program, it can't be overwritten. We remove the `lena_boundary` and `lena_area` layers from the project and run the script again.
- **`NameError: name 'processing' is not defined`.** The `import processing` line is missing from the start of the script.
- **The working area is huge and fills the whole screen.** Most likely, the layer ended up in degrees, and 1000 became 1,000 degrees. We check `srsname='EPSG:25830'` in the `uri` line.
- **`StopIteration`.** The layer has no features at all, so there's nothing for `next(...)` to take. We check the expression with the municipality's code.

## Your data

For another municipality in Asturias, the code in the expression changes. For Cabrales, for example, it's `'"CODIGO_INE" = 33008'`. It's worth changing the file names too, so we don't overwrite Lena. The margin, `margin_m`, changes in one line. For a mountain municipality, where lines often run along the ridges by the boundary, we can keep the same margin: it already leaves plenty to spare.

## Summary

### New concepts

- **Script:** a file of code that runs in full, from the first line to the last. Example: `01_area.py`.
- **Module and `import`:** a ready-made set of functions that we bring into a script. Example: `import processing`.
- **Project CRS:** the CRS in which QGIS shows all the layers on the map. Example: `project.setCrs(QgsCoordinateReferenceSystem("EPSG:25830"))`.
- **Processing algorithm:** a tool from the **Processing Toolbox** that we can run with code. Example: `processing.run("native:buffer", {...})`.
- **Dictionary:** 'name: value' pairs in curly brackets. Example: `{"INPUT": boundary, "DISTANCE": margin_m}`.
- **List:** values separated by commas in square brackets. Example: `[boundary_layer, area_layer]`.
- **GeoPackage:** a single file with a layer, its attributes and its CRS. Example: `lena_boundary.gpkg`.
- **Buffer:** a strip of a given width around a feature. Example: Lena's working area with a 1 km margin.

### New code

```python
import processing
project.setCrs(QgsCoordinateReferenceSystem("EPSG:25830"))
folder = project.homePath()
result = processing.run("native:buffer", {"INPUT": boundary, "DISTANCE": margin_m, "OUTPUT": f"{folder}/lena_area.gpkg"})["OUTPUT"]
layer = QgsVectorLayer(result, "lena_area", "ogr")
feature = next(layer.getFeatures())
```

### Key points

- Work we need to repeat goes in a script, not in the console.
- The script sets the CRS itself and brings in its modules itself. It doesn't rely on what state the project was in before it ran.
- Data from someone else's service gets saved straight away to a file of our own.
- Every number we chose ourselves is a separate variable, with its units in its name.

## Questions for an engineer

**Why a 1 km margin, rather than 10 m or 10 km?**

:::{dropdown} Answer
We need the working area to include the lines just beyond the boundary that could be a danger to homes in Lena. The safety strips and the boundary error are tens of metres, so 10 m is too little. 10 km is too much: the working area would take in thousands of other municipalities' homes and hundreds of kilometres of their lines, the calculation would get slow, and it wouldn't help. 1 km leaves plenty to spare over tens of metres and hardly adds to the amount of data. It's our own decision, and we've put it in a variable so that it's visible and easy to change.
:::

**Why write the boundary to a file if it's already in the service?**

:::{dropdown} Answer
So the calculation can be repeated. The service could be changed or switched off, and the boundary could be refined. If the result changes a month from now, we need to know which boundary we used. The file on our disk is what we actually worked with.
:::

**Why do we build the buffer in EPSG:25830, and not in a CRS in degrees?**

:::{dropdown} Answer
In EPSG:25830, the unit is the metre, and 1000 means 1,000 m anywhere in Lena. In degrees, one degree of longitude and one degree of latitude are different numbers of metres, so the buffer would come out the wrong width and the wrong shape. We calculate all the distances in the book in a metric CRS.
:::

## Exercises

**1.** What's the name of the buffer algorithm when we run it with code through `processing.run`?

[Answer](#en-sol-2-2-1)

**2.** This call doesn't work. Let's find the mistake and fix it.

```python
area = processing.run("native:buffer", {
    "INPUT": boundary,
    "DISTANCE": "1 km",
    "OUTPUT": f"{folder}/lena_area.gpkg",
})["OUTPUT"]
```

[Answer](#en-sol-2-2-2)

**3.** Let's go back to lesson 2.1. Why do we search for Lena by the code 33033 and not by name?

[Answer](#en-sol-2-2-3)

## Open question

We chose a 1 km margin by thinking about tens of metres. But what if there's a substation in the neighbouring municipality, with several lines running from it into Lena? Does that change anything about the choice of margin, or do we only need the working area to find the lines near Lena's homes?

## Notes and sources

- QGIS Documentation, Processing algorithms: ['Extract by expression'](https://docs.qgis.org/latest/en/docs/user_manual/processing_algs/qgis/vectorselection.html), ['Buffer'](https://docs.qgis.org/latest/en/docs/user_manual/processing_algs/qgis/vectorgeometry.html).
- [QGIS User Manual, 'Using processing algorithms from the console'](https://docs.qgis.org/latest/en/docs/user_manual/processing/console.html).
- Boundary: IDEAS, servicio WFS 'Unidades Administrativas', CC BY 4.0 (see lesson 2.1).
