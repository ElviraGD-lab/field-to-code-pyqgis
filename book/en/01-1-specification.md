---
title: "Lesson 1.1: The specification"
---

## Where we left off

In the prologue, we saw the same picture in two reports from 2018: a storm brings down power lines, trees and snow block the roads, and help can't get through. We looked for an open tool that shows, for any municipality, which homes stand close to overhead lines and how long it takes help to reach them. We didn't find one.

So we're building it ourselves. But if we open QGIS and start calculating straight away, it's easy to get every number right and still answer the wrong question. That's why the first step has no code.

## What we want to find out in this lesson

The main question of this lesson: what should the tool produce so that someone can make a decision from its results?

To answer it, we need to decide five things:

1. who will use the tool;
2. what we need to give it as input;
3. what it produces as output;
4. what assumptions we're making;
5. how we'll check the result.

How will we know if we've got it wrong? If we can't say what a user would do with a particular output, that output doesn't belong. And if a user is missing something they need to make a decision, we've left it out.

Engineers call a document like this a **specification**. In essence, it's an agreement with ourselves: what we're building, and how we'll know when we've built it.

## Who the tool is for

The tool has three users, and each of them needs something different.

1. **The distribution company.** It's responsible for the lines: it clears trees along the routes, plans line patrols and decides where to send crews before a storm. It plans its work by line, not by home. What it needs is a list of line sections: where the most homes are nearby, and where it takes longest to get to.
2. **Fire crews and the 112 emergency service.** If a conductor comes down and a fire starts, fire crews are usually the first to arrive. They need to know in advance which homes stand near the lines, and how many minutes it takes to reach them by day and by night.
3. **The municipality.** It writes the emergency plan and issues building permits. It needs the picture across its whole area: which parts of the municipality have homes near the lines and far from the fire stations.

**So there's one calculation, but the results are needed at three levels: buildings, line sections and the area as a whole.**

## What goes in and what comes out

The input:

- the municipal boundary;
- a power line layer (open data by default; a company may have its own);
- a table of fire stations;
- travel speeds by road class;
- the distance band boundaries and the priority table.

Everything that depends on the place is set on the input side. Nothing should be left in the code that only holds true for Lena.

The output:

1. **A layer of residential buildings.** For each building:
   - the distance to the nearest line, and that line's voltage;
   - the number of dwellings in the building;
   - the year it was built;
   - the fire crews' travel time, by day and by night;
   - the priority class.
2. **A layer of line sections:** how many homes are near each section, how long it takes to reach the furthest of them, and the priority class.
3. **A table by parish.** A parish is a subdivision of a municipality that Asturias uses for its statistics.
4. **A priority-zone raster** on a regular grid, with no individual homes.
5. **A map of how complete the line data is:** where we can trust the open data, and where it has gaps.
6. **A run report:** what data went in, which version it was, and which version of the tool did the calculation. Without it, the calculation can't be repeated, and so it can't be checked either.

All of this is saved as GeoPackage and CSV, so every user can open the results in their own GIS or in a spreadsheet.

**About private data.** The layer with every individual home stays with whoever runs the tool. Anything published will only show totals by area. If a figure is based on just a few homes, we suppress it.

## What 'close to a line' means

Twenty metres from a 20 kV line and twenty metres from a 400 kV line are two different things. Spain's regulation for high-voltage overhead lines, ITC-LAT 07, sets a safe distance between the conductors and buildings: the higher the voltage, the greater the distance.[^1]

There's an important subtlety here. The regulation measures this distance from the conductor that the wind has blown furthest out, not from the centre line.[^2] All we have, though, is the centre line on the map. We don't know the spacing between the conductors, or how far the wind swings them. So a safety strip measured from the centre line will come out narrower than the real one. It's a lower bound, and that's exactly what we call it.

For each building, we store three numbers:

1. **The distance to the line's centre line.** This is our measurement.
2. **The half-width of the safety strip for that line's voltage.** This comes from the regulation.
3. **The distance to the edge of the strip.** The first number minus the second. If the home is inside the strip, the number is negative.

We compare buildings on the third number. That way, a home near a 20 kV line and a home near a 400 kV line can be compared fairly. We measure the distance from the building's outline, not from its centre.

For the tables, we split the distances into bands: inside the strip, and then several steps further out. Splitting into just 'inside' and 'outside' is too crude: a home a metre from the edge of the strip and a home forty metres away would end up in the same group. We'll choose the band boundaries once we've seen the data.

**Which lines we include.** High voltage only: under Spanish regulations, that's anything above 1 kV.[^3] If a line's voltage isn't given in the data, we use the widest strip for it and flag that line on the completeness map. Better to check one home too many than to miss one that matters.

**A home in the nearest band isn't a breach of the rules. It's a place worth checking.**

## How many people live nearby

The open data doesn't tell us how many people live in each home. The closest thing to it is the number of dwellings in the cadastre.[^4]

It isn't exact, and we already know which way it's off:

- in villages with lots of empty houses and holiday homes, there are actually fewer people;
- in care homes and hotels, there are more people than dwellings.

So we don't trust the cadastre blindly. We check it against two independent sources:

1. **The Asturian parish layer**, which gives the number of dwellings for each parish.[^5]
2. **The European population grid** from the 2021 census, with 1 km cells.[^6]

## How long it takes help to arrive

We need two things: where crews set out from, and which roads they take.

1. **Fire stations.** SEPA, the Asturian emergency service, has 19 of them.[^7] We didn't find a ready-made layer with their coordinates and opening hours, so we'll put the table together ourselves. At night, crews can only set out from stations that are staffed round the clock. If all the stations around Lena are, the daytime and night-time figures will be the same – and that's an answer too.
2. **Roads.** We set the speeds for a fire engine, not a car: on a winding mountain road, the difference is big. We connect each home to the nearest road a vehicle can drive on.

**The boundary trap.** The station that serves Lena may sit in the next municipality, and the fastest route may run through a neighbour too. If we clip the roads exactly to the boundary, the times will be wrong, and the software won't show a single error. So we take the homes and lines inside the municipality, and the roads and fire stations with a margin around it.

An important limitation: we only calculate driving time on clear roads. No call handling, no time to turn out, no snow and no fallen trees. The real time is always longer, especially in a storm. **Our time is a lower bound.**

## Where to start inspecting

Once every home has a distance and a time, the company needs to know where to start. For that, we use a priority matrix:

- along one side, the distance band: close, medium, far;
- along the other, the travel time band: quick, medium, long;
- where they meet, a class from 1 (inspect first) to 5.

Why a table rather than a weighted formula? With a formula, we can't see where the result came from. With a table, an engineer sees straight away why a section got class 1: for example, the homes are inside the strip and it takes a long time to get there. The table is kept in a separate file, and the company can adapt it to its own needs without touching the code.

**The matrix isn't the probability of a fault. It shows how bad things will be if one happens.**

## How we'll check the result

A result that can't be checked is no use to a company. That's why we write down the checking rules now, before the first calculation, and save them to the project repository. The date they were saved proves the rules came before the result. Otherwise, it's very tempting to bend the checks to fit whatever we get.

| What we check | How | What counts as a failure |
|---|---|---|
| Distances | a random sample of buildings near the lines and far from them, checked by eye on orthophotos | on the orthophoto, a home is clearly in a different band from the calculated one |
| Totals | dwellings summed by parish against dwellings summed by building | the totals don't match exactly |
| Dwellings | the cadastre against the parish layer and the population grid | large discrepancies that we can't explain |
| Travel time | a few routes worked out by hand, and an independent formula for fire engines | a time of a different order of magnitude |
| Portability | the same tool run on Cabrales without changing a single line of code | we have to change the code |
| Robustness | recalculating with different band boundaries and speeds | the list of top-priority sections changes completely |

**Whatever a check shows is a result of the work on the project, even if we don't like it.**

## What the tool doesn't do

- It doesn't check compliance with the regulation: that needs the real conductor sag.
- It doesn't work out who loses power: that needs the network layout, and that isn't publicly available.
- It doesn't assess electromagnetic fields.
- It doesn't claim that every line in the data is overhead: some may be underground.

## Stages of the work

The specification gives us the order of work on the project. Each stage is a chapter of its own:

1. The municipal boundary and our first script.
2. Power lines.
3. Voltage and the safety strip.
4. Can we trust the line data?
5. Residential buildings.
6. Who lives near the lines.
7. How quickly help arrives.
8. Where to start inspecting.
9. Rasters.
10. From script to tool.
11. Testing the tool.
12. The result for the company: a QGIS plugin.

:::{admonition} Your data
For our own municipality, we replace these inputs:

- the municipal boundary;
- the table of fire stations: we take the coordinates and opening hours from the public pages of our own emergency service;
- the line layer, if we have our own.

In Spain, the cadastre is the same for every region except Navarre and the Basque Country, which have cadastres of their own. And we check the speeds for each road class against our own terrain.
:::

## Summary

### New concepts

- **Specification:** an agreement about who we're building the tool for, what we need to give it as input, what results it produces, what assumptions we're making and how we'll check it. Example: the six outputs of our tool.
- **Safety strip:** the zone around a line where, under the regulation, there should be no buildings. Example: ours is measured from the centre line, so it's a lower bound.
- **Distance band:** a step of distance from the edge of the strip. Example: 'inside the strip'.
- **Priority matrix:** a 'distance × time → class' table. Example: a home inside the strip, a long way to drive – class 1.
- **Lower bound:** a value the real quantity can't be smaller than. Example: our travel time.

### Key points

- First the specification, then the code.
- The results are needed at three levels: buildings, line sections and the area as a whole.
- Everything that depends on the place is set as input, not in the code.
- A strip measured from the centre line and travel times on clear roads are both estimates that make things look better than they really are. We say so openly.
- We write down the checking rules before the calculation.

## Questions for an engineer

1. **Why don't we clip the roads exactly to the municipal boundary?**

:::{dropdown} Answer
The nearest fire station and the fastest route may lie beyond the boundary. A clipped network will give the wrong times, and the software won't show a single error.
:::

2. **The company asks for a single list of homes, sorted by distance to the line. What will it be missing?**

:::{dropdown} Answer
It plans its work by line section, not by home. And it needs the travel time: a home near a line close to a fire station is less urgent than the same home an hour's drive away. That's why the result is line sections with a class from the matrix.
:::

3. **Why are the checking rules saved to the repository before the calculation?**

:::{dropdown} Answer
The date they were saved proves the rules came before the result. That protects us from the temptation to bend the checks to fit whatever we get.
:::

## Exercises

1. **Recall.** Which three numbers do we store for each building so we can compare homes near lines of different voltages? [Answer](#en-sol-1-1-1)

2. **Fill the gap.** What are the missing words? 'We take the homes and lines inside the municipality, and the roads and fire stations ______.' [Answer](#en-sol-1-1-2)

3. **Back to the prologue.** What happened in the same mountain municipalities in both 2018 reports? [Answer](#en-sol-1-1-3)

## Open question

The specification is ready. Before any calculation, we need the area itself: the municipal boundary. How do we get it in QGIS with code rather than with the mouse? And what is Python inside QGIS, anyway? That's where the next lesson begins.

## Notes and sources

[^1]: [BOE, Royal Decree 223/2008, ITC-LAT 07, section 5.12.2](https://www.boe.es/buscar/pdf/2008/BOE-A-2008-5269-consolidado.pdf) (in Spanish). Accessed 19 November 2026.
[^2]: Ibid.: the distance Dadd + Del = 3.3 + Del, with a minimum of 5 m, is measured from the conductors at their maximum swing. See also [J. Andreu, 'Distancias eléctricas de seguridad en LAAT', FEMPA, 2016](https://www.fempa.es/wp-content/uploads/oldfiles/documentos/20160203111250_Jornada%20distancias%20electricas%20020216_Jorge%20Andreu.pdf) (in Spanish). Accessed 19 November 2026.
[^3]: [BOE, Royal Decree 223/2008, ITC-LAT 01](https://www.boe.es/buscar/pdf/2008/BOE-A-2008-5269-consolidado.pdf) (in Spanish): any nominal voltage above 1 kV is high voltage. Accessed 19 November 2026.
[^4]: [Spanish Directorate General for the Cadastre, INSPIRE buildings](https://www.catastro.hacienda.gob.es/webinspire/index.html): attributes currentUse, numberOfDwellings and dateOfConstruction; see also [IDEE, JIIDE 2017](https://www.idee.es/resources/presentaciones/JIIDE17/Apresentacoes_JIIDE2017_Conjunto_datos_edificios_BU_en_la_Direccion_General_del_Catastro_Pablo_Gomez_Dominguez.pdf) (in Spanish). Accessed 19 November 2026.
[^5]: [SITPA-IDEAS, Government of Asturias: administrative units](https://sig.asturias.es/servicios/rest/services/UnidadesAdministrativas/MapServer/layers): parish layer with SADEI data. Accessed 19 November 2026.
[^6]: [Eurostat GISCO, GEOSTAT 2021 population grid](https://ec.europa.eu/eurostat/en/web/gisco/geodata/population-distribution/population-grids). Accessed 19 November 2026.
[^7]: [112 Asturias, SEPA fire station network](https://www.112asturias.es/parques-bomberos) (in Spanish). Accessed 19 November 2026.
