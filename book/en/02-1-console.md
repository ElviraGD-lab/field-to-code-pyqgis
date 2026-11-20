---
title: "Lesson 2.1: The Python Console and our first layer"
---

## Where we left off

In lesson 1.1, we wrote the specification. The tool's first input is the municipality. Until we have its boundary, we have nothing to clip the lines, homes and roads with. So the first code in the book gets us the boundary of Lena.

## What we want to find out in this lesson

Can we load Lena's boundary from an open Asturian service with code rather than with the mouse, and check that it really is Lena?

What we expect:

1. The service sends a layer of all the municipalities of Asturias, and QGIS accepts it as a working layer.
2. The layer is already in EPSG:25830. That's the coordinate reference system (CRS) we'll use to calculate distances.
3. There's exactly one Lena in the layer, and it's a single polygon in the middle of southern Asturias, on the border with Castile and León.
4. The polygon's area is close to the municipality's official area.

How we'll know if we've got it wrong:

- the layer doesn't load, or it has no features;
- there's no Lena, or there are several;
- the polygon is in the wrong place, for example by the sea;
- the area differs from the official figure several times over, not by a few per cent.

## Step 1: Opening the console

The Python Console is already built into QGIS. We open it from the **Plugins → Python Console** menu, or with Ctrl+Alt+P. A panel appears at the bottom of the window with a `>>>` input line. Whatever we type after `>>>` and confirm with Enter, Python runs straight away.

First, let's ask QGIS which project is open and what CRS it's in:

```python
project = QgsProject.instance()
print(project.crs().authid())
```

Let's go through the lines.

- `QgsProject` is the QGIS project: all the layers, their order and the CRS. Anything that starts with `Qgs` is part of QGIS, not of Python itself.
- `.instance()` means 'give me the project that's open right now'. There's always exactly one open.
- `project = ...` puts the result in a variable. A variable is a name that Python uses to remember a value. From here on, we write `project` instead of the whole long line.
- `project.crs()` is the project's CRS. `.authid()` is its code, for example `EPSG:4326`.
- `print(...)` shows a value in the console.

The dot between words means 'from this object, take this'. We read `project.crs()` as 'the project's CRS'.

What we see depends on the project. In a new project, QGIS often uses `EPSG:4326`, and if the project has already been saved in another CRS, we'll see that one. We saw `EPSG:25830`: our project was already in that CRS. In lesson 2.2, we'll set the project's CRS with code, so we don't depend on what state the project was in before we started.

## Step 2: Where the boundary is

The boundaries of the municipalities of Asturias are available from IDEAS, the Asturian spatial data infrastructure, as an open service. The service works to the WFS standard: it doesn't send a picture, but the features themselves, with their attributes. That's exactly what we need, because we'll be clipping things to this boundary later on.

To query the service, we need two things: its address and the layer name. We took the layer name from the service description. We can open it in a browser by adding `?service=WFS&request=GetCapabilities` to the service address. It lists three layers: `Asturias`, `Localidades` and `Concejos`. We need `UnidadesAdministrativas:Concejos`, the municipalities.

```python
url = "https://sig.asturias.es/servicios/services/UnidadesAdministrativas/MapServer/WFSServer"
uri = f"url='{url}' typename='UnidadesAdministrativas:Concejos' srsname='EPSG:25830' version='2.0.0'"
```

- Text in quotation marks is a string. `url` is a variable that holds the address as a string.
- `uri` is a string that describes the source: the address, the layer name, the CRS and the version of the standard. It's the same set of details we fill in with the mouse in the window for adding a WFS layer.
- The letter `f` before the quotation marks means: put the values of the variables in here. In place of `{url}`, Python inserts the address from the first line. That way, we don't have to write the long address twice.
- `srsname='EPSG:25830'` asks the service to send the coordinates in EPSG:25830, in metres, straight away. The service description says this is its native CRS, so there'll be no reprojection.

These two lines don't load anything. We've only written down where to get the data from.

## Step 3: Loading the layer

```python
municipalities = QgsVectorLayer(uri, "municipalities", "WFS")
print(municipalities.isValid())
print(municipalities.featureCount())
project.addMapLayer(municipalities)
```

- `QgsVectorLayer(...)` creates a vector layer. It needs three values: where to get the data (`uri`), what to call the layer in the Layers panel (`"municipalities"`) and what to read the source with (`"WFS"`, the data provider).
- `.isValid()` answers `True` (yes) if QGIS managed to open the source, and `False` (no) if it didn't. It's the first check we do on any layer.
- `.featureCount()` is the number of features in the layer.
- `project.addMapLayer(...)` adds the layer to the project, so it appears on the map and in the Layers panel. Without this line, the layer exists only in Python's memory.

We see `True` and `78`. Asturias has 78 municipalities, so the service has sent all of them.

## Step 4: What fields the layer has

Before we look for Lena, let's see which fields the features have.

```python
for field in municipalities.fields():
    print(field.name(), field.typeName())
```

This is our first loop.

- `municipalities.fields()` is the list of the layer's fields.
- `for field in ...:` means 'for each field in the list, do what comes below'. On each pass, the variable `field` holds the next field.
- The second line is indented four spaces to the right. The indent tells Python that the line belongs to the loop. Without it, we get an error.
- `field.name()` is the field's name, and `field.typeName()` is its type: integer, string and so on.

In the console, after the first line, `...` appears instead of `>>>`. Python is waiting for the rest of the loop. After the second line, we press Enter once more on an empty line, and the loop runs.

We see six fields: `OBJECTID`, `CODIGO_INE`, `CONCEJO`, `CONCEYU`, `st_area_shape_` and `st_length_shape_`. `CONCEJO` is the name in Spanish, `CONCEYU` is the name in Asturian, and `CODIGO_INE` is the municipality's code from the National Statistics Institute (INE). `OBJECTID` is the feature's number in the service's database, and the last two fields are the area and the perimeter, calculated by the service itself.

The service has its own names for the field types: `xsd:int` is an integer, and `xsd:double` is a decimal number. For the text fields `CONCEJO` and `CONCEYU`, the type came out blank for us. That isn't an error: it's how the service describes strings with a length limit. We can see they're text from the values in the attribute table.

## Step 5: Finding Lena

We can search by name or by code. The name isn't reliable here: it's written in capitals (`LENA`), and in Asturian it's `LLENA`. One wrong letter, and we find nothing. The INE code is unique to each municipality and doesn't depend on the language or the spelling. Lena's code is 33033.

```python
municipalities.selectByExpression('"CODIGO_INE" = 33033')
print(municipalities.selectedFeatureCount())
iface.mapCanvas().zoomToSelected(municipalities)
```

- `selectByExpression(...)` selects features using an expression. It's the same as **Select Features by Expression** with the mouse, and the expression is written the same way: the field name in double quotes, and the value without quotes, because it's a number.
- We've put the whole expression in single quotes. That's how Python knows where the string starts and ends, while the double quotes inside stay part of the expression.
- `selectedFeatureCount()` is the number of selected features. We expect exactly one.
- `iface` is the QGIS window itself: the map, the panels, the menus. `iface.mapCanvas()` is the map, and `.zoomToSelected(...)` zooms in to the selected features of this layer.

We see `1`, and the map has zoomed in to the selected polygon. It's in the middle of southern Asturias, and the boundary runs along the ridge on the border with León.

```{figure} ../images/02-1-lena-selected.png
:alt: Lena municipality selected on the map of Asturian municipalities
Lena selected among the 78 municipalities of Asturias (IDEAS WFS, EPSG:25830).
```

## Step 6: Checking that it's Lena

A single selected feature isn't proof yet. Let's look at its attributes and its area.

```python
lena = municipalities.selectedFeatures()[0]
print(lena["CONCEJO"], lena["CONCEYU"])
print(lena.geometry().area() / 1_000_000)
```

- `selectedFeatures()` is the list of selected features. `[0]` is the first of them. Python counts from zero, so the first item is item zero.
- `lena["CONCEJO"]` is the value of the `CONCEJO` field for this feature. Square brackets with a field name are how we read an attribute.
- `lena.geometry()` is the feature's geometry, and `.area()` is its area in the units of the CRS. In EPSG:25830, that's square metres. We divide by a million to get square kilometres. Python ignores the underscores in `1_000_000`: they're only there to make it easier to read.

We see `LENA LLENA` and an area of 318.355 km².

## What we got

Let's compare this with what we expected.

1. The layer loaded, `isValid()` returned `True`, and there are 78 features – the same as the number of municipalities in Asturias. As expected.
2. The layer is in EPSG:25830 from the start, so the area is calculated in metres with no reprojection. As expected.
3. There's one Lena, and the polygon is where we expected it. As expected.
4. Lena's official area is 315.5 km². Ours is 318.4 km², a difference of less than 1%. The layer description warns that the boundary is at a scale of 1:25,000 and provisional in places. That's good enough for our task: we'll be looking for homes within tens of metres of the lines, and we need the municipal boundary to tell which municipality each home belongs to. But we'll make a note of the difference: if a home stands right on the boundary, an error in the boundary could assign it to the neighbouring municipality.

**So:** Lena's boundary is in the project, and we've checked it in four ways. The next step is to save these lines in a script file, so we don't have to type them every time, and to build a working area with a margin around Lena. That's lesson 2.2.

## If something goes wrong

- **`isValid()` returned `False`.** Most often, it's a typo in the address or the layer name. We compare `uri` with the lesson text letter by letter. If everything matches, we open the service description in a browser: if that shows an error too, the service is down at the moment, and we try again later.
- **`featureCount()` returned `-1`.** QGIS doesn't know the number of features yet. We wait for the layer to draw on the map and run the line again.
- **`selectedFeatureCount()` returned `0`.** We check the quotes: the field name in double quotes, the whole expression in single quotes, the number with no quotes.
- **`IndentationError`.** The loop has no indent, or the indent differs from line to line. We indent with four spaces.
- **`NameError: name 'project' is not defined`.** The console has been restarted, and the variables are gone. We run the steps again from the start.

## Your data

For another municipality in Asturias, only the code in step 5 changes. We can look up the codes of all 78 municipalities in the layer's attribute table: we right-click the layer, choose **Open Attribute Table** and look at the `CODIGO_INE` column. Cabrales, for example, has the code 33008.

```{figure} ../images/02-1-attribute-table.png
:alt: Attribute table of the municipalities layer with Lena selected
Attribute table of the municipalities layer: 78 features, Lena selected (CODIGO_INE 33033).
```

For a municipality outside Asturias, this service won't do: it only covers Asturias. Other autonomous communities have similar services, and so does the National Geographic Institute (IGN) for the whole of Spain. The address, the layer name and the name of the code field change, but the lines of code themselves stay the same.

## Summary

### New concepts

- **Python Console:** the line in QGIS where code runs as soon as we press Enter. Example: `print(project.crs().authid())`.
- **Variable:** a name under which Python remembers a value. Example: `project = QgsProject.instance()`.
- **String:** text in quotation marks. Example: `"municipalities"`.
- **WFS:** a service standard that sends features with their attributes, not a picture. Example: the IDEAS municipalities service.
- **Data provider:** the part of QGIS that can read a particular type of source. Example: `"WFS"`.
- **Loop:** repeating the same lines for each item in a list. Example: `for field in municipalities.fields():`.
- **INE code:** a numeric code for a municipality, one per municipality. Example: 33033 for Lena.

### New code

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

### Key points

- Any layer we load with code, we check first: `isValid()` and the number of features.
- We search for a feature by its code, not by its name.
- A single feature found isn't proof yet: we look at its attributes, where it is on the map and its area.

## Questions for an engineer

**Why do we ask the service for EPSG:25830 straight away, rather than reprojecting later?**

:::{dropdown} Answer
EPSG:25830 is the service's native CRS, so the coordinates arrive with no reprojection and no new errors. Every reprojection is one more place where we can get the CRS wrong. The fewer reprojections there are, the easier it is to check the result.
:::

**The boundary differs from the official area by less than 1%. When would an error like that matter to us?**

:::{dropdown} Answer
When a feature stands right on the boundary. A home a few metres from the boundary could end up in the wrong municipality, and then we'd count it in the wrong place. For a municipal report, that's a trifle, but for an individual home on the boundary, it isn't. That's why in lesson 2.2 we take a working area with a margin, and later we'll check the homes near the boundary separately.
:::

**Why can't we assume the service will always work?**

:::{dropdown} Answer
The service belongs to another organisation. It could be moved, the layer could be renamed, or it could be switched off for a while. So the tool has to keep a copy of the data, and the address and the layer name have to be inputs, not strings hard-coded into the script. We'll save the copy in lesson 2.2, and we'll record the download date in the run report in the last chapter.
:::

## Exercises

**1.** Which layer method tells us whether QGIS managed to open the source?

[Answer](#en-sol-2-1-1)

**2.** There's a mistake in this code: Lena doesn't get selected. Let's find it and fix it.

```python
municipalities.selectByExpression("CODIGO_INE = '33033'")
```

[Answer](#en-sol-2-1-2)

**3.** Let's go back to lesson 1.1. Is the municipal boundary an input or an output of the tool? Why did we start with it?

[Answer](#en-sol-2-1-3)

## Open question

The municipal boundary follows ridges and rivers, but fire crews drive along roads. Should our search area for fire stations and roads match the municipal boundary, or be wider? And if it's wider, by how much?

## Notes and sources

- [IDEAS, Gobierno del Principado de Asturias. WFS service 'Unidades Administrativas', layer `Concejos`, EPSG:25830, licence CC BY 4.0](https://sig.asturias.es/servicios/services/UnidadesAdministrativas/MapServer/WFSServer?service=WFS&request=GetCapabilities). Checked 20.11.2026.
- [Description of the `Concejos` layer (accuracy about 1:25,000, some boundary lines provisional; geometry source: IGN)](https://sig.asturias.es/servicios/rest/services/UnidadesAdministrativas/MapServer/2). Checked 20.11.2026.
- [Lena's area of 315.5 km²: Turismo Asturias, 'Lena'](https://www.turismoasturias.es/es/descubre/donde-ir/municipios/lena). Checked 20.11.2026.
- [PyQGIS Developer Cookbook, 'Loading Layers'](https://docs.qgis.org/latest/en/docs/pyqgis_developer_cookbook/loadlayer.html). Checked 20.11.2026.
