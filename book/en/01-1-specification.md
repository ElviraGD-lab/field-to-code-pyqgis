---
title: "Lesson 1.1: Who the tool is for and what it should produce"
---

## Where we left off

In the prologue, we saw the same picture in two reports from 2018. A storm brings down power lines, trees and snow block the roads, and help can't get through. We looked for an open tool that shows, for any municipality, which homes stand close to the lines and how long it takes to reach them. We didn't find one.

So we're building it ourselves. But where do we start?

Our first instinct is to open QGIS and start loading data. But then it's easy to get every number right and still answer the wrong question. That's why the first lesson has no code. **First, we agree on exactly what we're building.**

## What we want to know

The question for this lesson: **what should the tool produce so that someone can make a decision from its results?**

How will we know if we've got it wrong?

- If we can't say what a user would do with a particular output, that output doesn't belong.
- If a user is missing something they need to make a decision, we've left it out.

Engineers call a document like this a **specification**. In essence, it's an agreement with ourselves: what we're building, and how we'll know when we've built it. In this lesson, we write the first half of it.

## Step 1: Who will use the tool

Let's think about who actually needs an answer to our question. If we picture a winter storm in the mountains, three players come to mind straight away.

1. **The distribution company.** It's responsible for the lines: it clears trees along the routes, plans line patrols and decides where to send crews before a storm. It thinks in terms of lines, not homes. It needs to know **which line sections to inspect first**.
2. **Fire crews and the 112 emergency service.** If a conductor comes down and a fire starts, fire crews are usually the first to arrive. They need to know in advance **which homes stand near the lines, and how many minutes it takes to reach them** by day and by night.
3. **The municipality.** It writes the emergency plan and issues building permits. It needs **the picture across its whole area**: which parts of the municipality have homes near the lines and far from the fire crews.

Here's something interesting. They all share the same question, but each of them looks at it differently.

**So there's one calculation, but the results are needed at three levels: individual buildings, line sections and the area as a whole.**

## Step 2: What we give the tool as input

Now let's think about what the tool needs to work out the answer. We'll go through our question word by word.

- **'Which homes…'**: we need buildings. We'll take them from the open cadastre.
- **'…close to the lines…'**: we need a power line layer. By default, that's open data. But a company may have its own, more accurate layer, and the tool should accept it.
- **'…in this municipality…'**: we need the municipal boundary.
- **'…how long it takes fire crews…'**: we need fire stations and roads. And a speed for each road class too: a fire engine travels differently on a main road and on a winding mountain road.
- **'…what to inspect first…'**: we need a rule for turning distance and time into urgency. We'll look at it in lesson 1.2.

The tool can download the buildings and roads itself. But we have to give it the municipal boundary, the table of fire stations, the speeds and the urgency rule.

There's one important rule here. **Everything that depends on the place, we give as input; we don't write it inside the code.** Why?

Let's say we wrote a speed of 40 km/h into the code, because it suits Lena. Then someone runs the tool in a flat municipality. The code quietly calculates with the same speed, and the times come out wrong. The software won't show any error. But if the speed is an input, it's visible, and it's easy to change.

**So nothing should be left in the code that only holds true for Lena.**

## Step 3: What the tool produces as output

Now let's go back to our three users and decide what each of them will get. At the same time, we'll check each output: what would someone do with it?

1. **A layer of residential buildings.** For each building:
   - the distance to the nearest line, and that line's voltage;
   - the number of dwellings in the building;
   - the year it was built;
   - the fire crews' travel time, by day and by night;
   - the urgency class.

   *Who uses it:* fire crews and 112, to know which homes take longest to reach.
2. **A layer of line sections.** We divide the lines into sections of equal length. For each section: how many homes are nearby, how long it takes to reach the furthest of them, and the urgency class.

   *Who uses it:* the company, to decide where to start clearing.
3. **A table by parish.** A parish is a subdivision of a municipality that Asturias uses for its statistics.

   *Who uses it:* the municipality, to see which parts of its area have a problem.
4. **An urgency-zone map.** This is a raster on a regular grid, with no individual homes.

   *Who uses it:* the municipality, and anyone who needs the whole picture. Individual homes can't be seen on it.
5. **A map of how complete the line data is.** Where we can trust the open data on power lines, and where it has gaps.

   *Who uses it:* everyone. If there are no lines in the data somewhere, that doesn't mean there are none in reality.
6. **A run report.** What data went in, which version it was, and which version of the tool did the calculation.

   *Who uses it:* anyone who wants to check or repeat the calculation. Without the report, six months from now nobody will be able to say which data we used.

Every output is needed by someone; there's nothing extra. We save everything in two formats. GeoPackage can be opened in any GIS, and CSV in any spreadsheet.

## Step 4: What to do about private data

The buildings layer shows specific homes. That's private information, and we can't simply put it on the internet.

So we agree on this:

- the layer with every individual home stays with whoever runs the tool;
- anything published shows only totals by area;
- if a figure is based on just a few homes, we suppress it, because it could be used to identify a specific home.

## What we found

Let's compare this with what we wanted to know.

We have the first half of the specification:

- **three users**, and what each of them needs;
- **the inputs:** the boundary, the lines, the fire stations, the speeds and the urgency rule. Everything that depends on the place is set as input;
- **six outputs**, and for each one it's clear who uses it and why;
- **a rule for private data.**

So far, the 'extra or missing' check passes: every output has a user.

But the specification still has a lot of words we haven't defined yet. What does 'close to a line' mean? How do we find out how many people live in a home? How do we calculate travel time? How do we turn distance and time into urgency? And how do we check that the tool can be trusted? That's the second half of the specification, and the next lesson.

## Your data

For our own municipality, we replace these inputs:

- the municipal boundary;
- the table of fire stations: we can take the coordinates and operating hours from the public pages of our own emergency service;
- the line layer, if we have our own;
- the speeds for each road class: we check whether they suit our own terrain.

The tool takes the buildings from the cadastre. In Spain, the cadastre is the same for every region except Navarre and the Basque Country, which have cadastres of their own.

## Summary

### New terms

- **Specification:** an agreement about who we're building the tool for, what we give it as input, what it produces and how we'll check the result. Example: in this lesson, we wrote down three users, the inputs and six outputs.
- **Input:** what the tool receives from the user. Example: the municipal boundary.
- **Output:** what the tool produces. Example: the layer of residential buildings.
- **Parish:** a subdivision of a municipality that Asturias uses for its statistics. Example: the table by parish.

### Key points

- First the specification, then the code.
- One calculation, three levels of output: buildings, line sections and the area as a whole.
- Everything that depends on the place, we give as input; we don't write it in the code.
- Every output must have a user.
- Anything published shows only totals by area.

## Questions for an engineer

1. **Why don't we write the fire engine's speed straight into the code, if we already know it for Lena?**

:::{dropdown} Answer
In another municipality, the speed may be different. If it's written into the code, the tool will quietly calculate the wrong time and show no error at all. If the speed is an input, it's visible, and it's easy to change.
:::

2. **The company asks for a single list of homes, sorted by distance to the line. What will it be missing?**

:::{dropdown} Answer
The company plans its work by line section, not by home. And it needs the travel time: a home by a line close to a fire station is less urgent than the same home an hour's drive away. That's why, for the company, we make a layer of line sections with an urgency class.
:::

3. **Why do we need a run report, if the result is already on our disk?**

:::{dropdown} Answer
Data changes: the cadastre gets updated, and services move. Six months from now, the same run could give different numbers. The report shows which data and which version of the tool we used. Without it, the result can be neither repeated nor checked.
:::

## Exercises

1. **Recall.** Who are the tool's three users? [Answer](#en-sol-1-1-1)

2. **Fill the gap.** What are the missing words? 'Everything that depends on the place, we give ______; we don't write it ______.' [Answer](#en-sol-1-1-2)

3. **Back to the prologue.** What two things happened in both 2018 reports? [Answer](#en-sol-1-1-3)

## The open question

We've written 'close to a line' many times. But is 20 metres from a 20 kV line the same as 20 metres from a 400 kV line? And what do we even measure the distance from? That's where lesson 1.2 begins.

## Notes and sources

- [Spanish Directorate General for the Cadastre, INSPIRE buildings](https://www.catastro.hacienda.gob.es/webinspire/index.html): buildings with their use, number of dwellings and year of construction; Navarre and the Basque Country keep their own cadastres.
- [SITPA-IDEAS, Government of Asturias: administrative units](https://sig.asturias.es/servicios/rest/services/UnidadesAdministrativas/MapServer/layers): parish layer based on SADEI data.
