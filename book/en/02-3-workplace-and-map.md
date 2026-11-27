---
title: "Lesson 2.3: Our workplace and a map of PyQGIS"
---

## Where we left off

In lessons 2.1 and 2.2, we went through the basics of Python: numbers and text, variables, lists and dictionaries, conditions, loops and functions. All of that was about numbers and text.

But our data is QGIS layers. Before we write code for them, we need two things: a convenient workplace (where to write code and where to keep files) and the overall picture – how QGIS works inside, and what each part of it is called.

## What we want to know

The question for this lesson: **where do we write code, and how does QGIS work inside: what sits inside what, and what is it called?**

Let's start by recalling what we already know from working with the mouse:
- one project is open;
- the project has layers;
- a layer has an attribute table: rows and columns;
- every row has a shape on the map: a point, a line or a polygon.

What we expect: inside QGIS, everything is arranged the same way, in the same order. If the code shows us something different, we've misunderstood something somewhere, and we'll work out what.

## Step 1: Creating a folder and a project

We'll keep everything we do in the book in a single folder. That way, a month from now, we'll be able to find the project, the scripts and the results.

1. We create a folder on the computer called `pyqgis-project`.
2. Inside it, we create a folder called `scripts`, for files with code.
3. We open QGIS and create a new project: **Project → New**.
4. We save it straight away to the `pyqgis-project` folder: **Project → Save As…**, with the name `project`. QGIS saves the file `project.qgz`.

Why save an empty project? Later on, the code will ask the project where it's saved. An unsaved project has no answer.

## Step 2: A practice layer

To see how a layer works, we need a layer with points and a table. The real data starts in chapter 3. Here, we'll make a small layer ourselves, in a minute.

1. **Layer → Create Layer → New Temporary Scratch Layer…**
2. The layer name is `test_points`. The geometry type is **Point**. The coordinate reference system is **EPSG:25830**.
3. Under **New Field**, we type the field name `name`, choose the type **Text**, press **Add to Fields List**, and then **OK**.

```{figure} ../images/ch02-3-scratch-layer-dialog.png
:alt: New Temporary Scratch Layer dialog with the name field in the fields list
New Temporary Scratch Layer: one text field, name, in EPSG:25830.
```


4. We turn on editing (the pencil icon), take the **Add Point Feature** tool and place two points on the map. For each one, QGIS asks for the value of the `name` field: we type `A` for the first point and `B` for the second.
5. We save the edits and turn editing off.

Let's open the attribute table: right-click the layer → **Open Attribute Table**. It has two rows and one column, `name`.

```{figure} ../images/ch02-3-two-points.png
:alt: Layers panel with test_points and two points on the map
The practice layer test_points: two points on the map.
```

```{figure} ../images/ch02-3-attribute-table.png
:alt: Attribute table of test_points with one field, name, and rows A and B
Attribute table of test_points: one field (name), two features (A, B).
```


The layer is temporary: it will disappear when we close the project, and QGIS will warn us about that. For this lesson, that doesn't matter.

## Step 3: Where to write code: the console and the editor

We open Python: **Plugins → Python Console** (Ctrl+Alt+P). The console panel appears at the bottom, with the `>>>` line.

In the console, Python runs **one line straight away**. That's handy for asking something quickly. But when there are several lines, especially with indents, typing them in the console is awkward, and it's easy to make a mistake.

So we write code that's more than one line in the **editor**. It opens with the **Show Editor** button on the console panel. A window like a simple text editor appears on the right.

We always work in the editor the same way:
1. We write the lines of code.
2. We save the file with the **Save** button, in the `scripts` folder. The name is in English, for example `lesson_2_3.py`.
3. We run it with the **Run Script** button (the green triangle).
4. We look at the result in the console, on the left.

```{figure} ../images/ch02-3-console-editor.png
:alt: Python Console on the left and the code editor on the right
Python Console (left) and the code editor (right) after running the first line.
```


**There's one important difference here.** In the console, when we just type `code`, Python shows the value by itself. In the editor, it won't: Python runs the line and says nothing. To see the result in the editor, we always write `print(...)`.

Let's check. We type one line in the editor:

```python
print("QGIS is ready")
```

We save it and run it. The console shows: `QGIS is ready`.

**So the rule is simple: one short line can go in the console; everything else goes in the editor, with `print`.**

## Step 4: How QGIS works inside

Now for the main part. Let's look at QGIS from the top down and name each part.

```{mermaid}
flowchart TB
    QGIS["QGIS (application)"] --> WINDOW["Window: iface<br/>map canvas, Layers panel"]
    QGIS --> TOOLS["Tools: processing<br/>buffer, clip, ..."]
    QGIS --> PROJECT["Project: QgsProject<br/>.qgz file"]
    PROJECT --> LAYER["Layer: QgsVectorLayer<br/>many in a project"]
    LAYER --> SOURCE["Data source<br/>file, web service"]
    LAYER --> CRS["Coordinate reference system"]
    LAYER --> FIELDS["Fields<br/>table columns"]
    LAYER --> FEATURES["Features: QgsFeature<br/>table rows"]
    FEATURES --> ATTRS["Attributes<br/>values in the row cells"]
    FEATURES --> GEOM["Geometry: QgsGeometry<br/>one per feature: point, line, polygon"]
```

Let's go down the diagram from the top. We'll compare each part with what we see when we use the mouse.

1. **QGIS is the application.** Inside it, there are three different things: the window, the tools and the project.
2. **The window.** This is what we see on the screen: the map, the Layers panel, the menus. In code, the window is called `iface`, short for 'interface'. Through it, we can find out, for example, which layer is currently selected with the mouse.
3. **The tools.** Buffer, clip, intersection: everything in the **Processing Toolbox** panel. In code, we reach them through `processing`. We'll look at them in lesson 2.6.
4. **The project.** This is what gets saved to the `.qgz` file: which layers are open, and what coordinate reference system the project is in. There's always exactly one project open.
5. **A layer.** A project can have many of them. Each layer has four things:
   - **a data source**: where the layer gets its data from – a file on disk, a web service, or memory, as with our temporary layer. With the mouse: layer properties → **Source**;
   - **a coordinate reference system**: ours is EPSG:25830;
   - **fields**: the columns of the attribute table. Ours has one field, `name`;
   - **features**: the rows of the table. Ours has two: points A and B.
6. **Every feature has attributes and a geometry.**
   - **Attributes** are the values in the cells of its row. For example, `A` in the `name` field.
   - **Geometry** is its shape on the map. A feature has exactly one: a point, a line or a polygon. We can't see it in the attribute table; we see it on the map.

**It's easy to get confused here: a field and an attribute are different things.**
- **A field** is a column, the same for every row. For example, the `name` field, with the type 'text'.
- **An attribute** is the value of that field in one particular row. For example, `A` for the first point.

A field is the column heading; an attribute is what's written in the cell.

**So geometry doesn't live in the layer as a whole, but with each feature: separately from the attributes, but in the same row.**

## Step 5: Classes, objects and methods

Under each part of the diagram there's an English name: `QgsProject`, `QgsVectorLayer`, `QgsFeature`, `QgsGeometry`. These are **classes**. Let's use an example from fieldwork to see what that means.

- **A class** is a description of a kind of thing: what it has and what we can do with it. For example, 'survey station': every survey station has a number and coordinates, and we can measure the distance from it to another station. That's a description, not a particular station.
- **An object** is one particular thing of that kind. Station number 12, with its coordinates, is an object. Our project, `project.qgz`, is an object of the class `QgsProject`. Our layer, `test_points`, is an object of the class `QgsVectorLayer`.
- **A method** is an action, or a question we can put to an object: 'What's your name?', 'How many rows do you have?'. In code, a method is written after the object, with a dot, and with brackets.

That gives us the grammar of every line of PyQGIS:

```text
object.method()
```

- to the left of the dot is who we're asking;
- the dot means 'of this object';
- to the right is what we're asking;
- the brackets mean 'run it'. Without the brackets, the method won't run.

**Here's the second place where it's easy to get confused: a feature and an object are different things.**
- **A feature** is a row of the attribute table: point A or point B. Its class is `QgsFeature`.
- **An object** in Python is any particular thing in code: a project, a layer, a point, a piece of text.

So a feature is an object too, but not every object is a feature. To keep them apart, in the book we always call a row of the table a **feature**.

## Step 6: Checking the diagram with code

Now let's go down the diagram in code and see whether it matches what we know.

First, the project. What we expect: Python will show where our `project.qgz` file is.

We write in the editor (we can delete the previous line):

```python
project = QgsProject.instance()
print(project.fileName())
```

We save it and run it. The console shows the full path to the file, ending in `pyqgis-project/project.qgz`.

Let's go through it:
- `QgsProject` is the project class.
- `.instance()` is a method that hands over **the very** project that's open right now. The English word 'instance' means one particular example of something. There's always exactly one project open, and that's the one we get.
- `project = …` puts this project under the name `project`, as in lesson 2.1.
- `project.fileName()` asks the project: 'where's your file?'.
- `print(...)` shows the answer in the console. We're in the editor, after all.

Now let's go one level down, to the layer. We select the `test_points` layer with the mouse in the Layers panel. What we expect: the name `test_points`, the coordinate reference system EPSG:25830 and 2 features.

We add these lines under the first two:

```python
layer = iface.activeLayer()
print(layer.name())
print(layer.crs().authid())
print(layer.featureCount())
```

We save it and run it. The console shows:

```text
.../pyqgis-project/project.qgz
test_points
EPSG:25830
2
```

Let's go through the new lines:
- `iface.activeLayer()` asks the window: 'which layer is selected right now?'. 'Active' means the one selected with the mouse.
- `layer.name()` asks the layer for its name.
- `layer.crs().authid()` goes **two steps down the diagram**. `layer.crs()` takes the layer's coordinate reference system. That's an object too. `.authid()` takes its code from it: `EPSG:25830`.
- `layer.featureCount()` asks the layer how many features it has.

Let's change one thing and see what happens. We add a third point, C, with the mouse, save the edits and run the script again. The last number becomes `3`. The code didn't change; the data did.

## Why the code looks like this

- **All QGIS classes start with `Qgs`** and are written with a capital letter: `QgsProject`, `QgsVectorLayer`, `QgsFeature`. When we see `Qgs`, we know it's part of QGIS.
- **Methods start with a small letter**, and each word after that starts with a capital: `fileName`, `activeLayer`, `featureCount`. That's the convention in QGIS. We write our own names differently, with underscores: `is_high_voltage`. So we can tell at a glance from a name whether it came from QGIS or we wrote it.
- **Method names are ordinary English words.** The file name is `fileName`; the number of features is `featureCount`. If we know what we want to ask, we can often guess the name. How to check a guess is in 'Programmer's secrets' below.
- **In the QGIS console, the `Qgs…` classes and `iface` are already loaded.** There's no need to write `import`, as we did for `math` in lesson 2.2. In a separate script that runs outside the QGIS editor, we will need it – we'll come to that in chapter 12.

## What we found

Let's compare this with what we expected. Inside QGIS, everything is arranged the same way as we see it with the mouse:

| With the mouse | Part of the diagram | In code |
|---|---|---|
| the open project | project | `QgsProject.instance()` |
| the layer selected in the Layers panel | window | `iface.activeLayer()` |
| the layer's name | layer | `layer.name()` |
| the coordinate reference system in the layer properties | coordinate reference system | `layer.crs().authid()` |
| the number of rows in the attribute table | features | `layer.featureCount()` |

Our expectation was confirmed. And we've gained the main thing: now every part has a name, and we know what sits inside what. So far, we've only seen fields, attributes and geometry on the diagram. We'll get at them with code in the next lesson.

## If something goes wrong

- **Nothing appeared in the console from the editor.** We forgot `print(...)`. In the editor, Python doesn't show anything by itself.
- **`project.fileName()` printed an empty line.** The project hasn't been saved. We save it (step 1) and run the script again.
- **`AttributeError: 'NoneType' object has no attribute 'name'`.** No layer is selected in the Layers panel, so `iface.activeLayer()` returned 'nothing': `None`. We select a layer with the mouse.
- **Instead of the name, we get `<built-in method name of QgsVectorLayer object at 0x…>`.** We forgot the brackets: `layer.name` instead of `layer.name()`. Python showed the method itself rather than running it.
- **`NameError: name 'QgsProject' is not defined`.** We check the capital and small letters: `QgsProject`, not `QGSProject` or `qgsproject`.

## Your data

Instead of the practice layer, we can select any vector layer of our own: a GeoPackage, a shapefile, a layer from a service. The code doesn't change. Only the answers change: the name, the coordinate reference system and the number of features.

## Summary

**New terms**
- **Console:** the place where Python runs one line straight away. *Example:* `>>>` at the bottom of the QGIS window.
- **Editor:** the window for code that's more than one line; we save the code to a file and run it with a button. *Example:* `scripts/lesson_2_3.py`.
- **Project:** everything that gets saved to the `.qgz` file; there's always exactly one open. *Example:* `QgsProject.instance()`.
- **Window:** what we see on the screen: the map and the Layers panel. *Example:* `iface.activeLayer()`.
- **Layer:** a data source, a coordinate reference system, fields and features. *Example:* `test_points`.
- **Field:** a column of the attribute table. *Example:* `name`.
- **Feature:** a row of the attribute table, together with its shape on the map. *Example:* point A.
- **Attribute:** the value of a field in one row. *Example:* `A`.
- **Geometry:** a feature's shape on the map, one per feature. *Example:* a point.
- **Class:** a description of a kind of thing. *Example:* `QgsVectorLayer`.
- **Object:** one particular thing of that kind. *Example:* our layer `test_points`.
- **Method:** a question or action we can put to an object; written after a dot, with brackets. *Example:* `layer.name()`.

**New code**

```python
print("QGIS is ready")

project = QgsProject.instance()
print(project.fileName())

layer = iface.activeLayer()
print(layer.name())
print(layer.crs().authid())
print(layer.featureCount())
```

**Key points**
- One short line goes in the console. Everything else goes in the editor, with `print`, in a file in the `scripts` folder.
- QGIS: the window, the tools and the project. The project holds layers. A layer has a source, a coordinate reference system, fields and features. A feature has attributes and one geometry.
- A field is a column; an attribute is a value in a cell.
- A line of PyQGIS reads like this: `object.method()` – who, what, run it.

## Programmer's secrets

1. **Forgotten what an object can do?** We type `layer.` in the console and press Tab: the console shows the layer's methods. If we type the first few letters, for example `layer.fea`, only the matching ones remain.
2. **Checking a guess about a method name.** `help(layer.featureCount)` in the console shows what the method does and what it returns. Here there are no brackets after the name: we're not running the method, we're asking about it.
3. **Can't remember the class?** The console's answer gives us a hint. If it says `<QgsVectorLayer: 'test_points' (memory)>`, the layer's class is `QgsVectorLayer`, and its source is memory. We look up the class description in the PyQGIS documentation by its name.

## Questions for an engineer

1. **Why do we write code that's more than one line in the editor, and not in the console?**

:::{dropdown} Answer
In the console, we have to type each line afresh and keep track of the indents by hand, so it's easy to make a mistake. In the editor, the code sits in a file: we can correct it, save it and run the whole thing again. And a month from now, we'll find it in the `scripts` folder.
:::

2. **The attribute table has no column with the coordinates of the points. So where does QGIS store where a point is?**

:::{dropdown} Answer
In the feature's geometry. It's stored with each feature, separately from the attributes, so we can't see it in the table, but we can see it on the map.
:::

3. **In our tool, we'll ask every layer for its coordinate reference system. Why do we need to do that before any calculations?**

:::{dropdown} Answer
A distance to a line in metres can only be calculated properly in a coordinate reference system whose unit is the metre, such as EPSG:25830. If a layer turns out to be in a different system, the number will be wrong, and the software won't show an error. So we check first, and then we calculate.
:::

## Exercises

1. **Recall.** Where on the diagram is the geometry: with the project, with the layer or with the feature? [Answer](#en-sol-2-3-1)

2. **Fix it.** This line in the editor should show the number of features, but nothing appeared in the console. Let's find the mistake: [Answer](#en-sol-2-3-2)

   ```python
   layer.featureCount()
   ```

3. **Back to lesson 1.2.** What does the regulation measure the safety strip from: the centre line or the conductor? [Answer](#en-sol-2-3-3)

## The open question

We've found out how many features the layer has. But what's in them? How do we go through them one by one, read the `name` attribute of each point and get at its geometry, with its coordinates? That's where lesson 2.4 begins.

## Notes and sources

1. QGIS Project, *PyQGIS Developer Cookbook*, section 'Introduction': the Python Console and the editor. https://docs.qgis.org/latest/en/docs/pyqgis_developer_cookbook/intro.html
2. QGIS Project, *QGIS User Guide*, section 'QGIS Python console'. https://docs.qgis.org/latest/en/docs/user_manual/plugins/python_console.html
3. QGIS Project, PyQGIS 4.2 documentation, class `QgsProject`. https://qgis.org/pyqgis/4.2/core/QgsProject.html
