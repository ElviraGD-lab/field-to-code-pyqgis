---
title: "Lesson 1.2: How we calculate and how we check"
---

## Where we left off

In lesson 1.1, we decided who we're building the tool for, what we give it as input and what it produces. But the specification still has words we haven't defined yet: 'close to a line', 'how many people', 'how long it takes', 'what to inspect first'. For now, they're just words. A computer can't calculate them.

## What we want to know

The question for this lesson: **how do we turn each of these words into something we can calculate, and how do we then check that the result can be trusted?**

How will we know if we've got it wrong? If, for any of the words, we can't say what data and what method we'll use to get it, the specification isn't ready yet.

Let's go through the words one at a time.

## Step 1: What 'close to a line' means

Let's start with something simple. Is a home 20 metres from a 20 kV line just as close as a home 20 metres from a 400 kV line?

No. The higher the voltage, the further from the conductors buildings have to be. This is set out in the ITC-LAT 07 regulation: it gives a safe distance between the conductors and buildings, and that distance is different for each voltage.[^1] Around the line, this gives us a strip where there shouldn't be any buildings. Let's call it the **safety strip**.

Now a harder question: what do we measure this strip from? The regulation measures the distance from the **conductor that the wind has blown furthest out**, not from the middle of the line.[^2] The conductors hang on either side of the tower, and in the wind they swing out even further.

And what do we have? On the map, a line is just a single line: its centre line. We don't know the spacing between the conductors, or how far the wind swings them. So if we measure the strip from the centre line, it'll come out **narrower than the real one**. That's not a problem, as long as we say so honestly: our strip is a **lower bound**. The real strip is wider, and there may be more homes inside it.

So how do we compare homes near lines of different voltages? For each building, we store three numbers:

1. **The distance to the line's centre line.** This is what we measure.
2. **The half-width of the safety strip for that line's voltage.** We take this from the regulation.
3. **The distance to the edge of the strip.** The first number minus the second. If the home is inside the strip, the number is negative.

We compare buildings on the third number. That way, a home near a 20 kV line and a home near a 400 kV line can be compared fairly. We measure the distance from the building's wall, not from its centre: the danger starts at the wall.

**One more detail.** Splitting homes into just 'inside the strip' and 'outside' is too crude. A home a metre from the edge of the strip and a home forty metres away would end up in the same group. So we split the distance into several **bands**: inside the strip, and then several steps further out. We'll decide where to draw the boundaries between the steps once we've seen the data.

**Which lines we include.** High voltage only. Under Spanish regulations, that's anything above 1 kV.[^3] And what if a line's voltage isn't given in the data? Then we use the widest strip for it and flag that line on the completeness map. Better to check one home too many than to miss one that matters.

**So a home in the nearest band isn't a breach of the rules. It's a place worth checking.**

## Step 2: How many people live nearby

The open data doesn't tell us how many people live in each home. What's the closest thing we have? In the cadastre, every building has a **number of dwellings**: flats or separate houses.[^4]

But a dwelling isn't a person. Let's think about which way we'll be wrong:

- in villages with lots of empty houses and holiday homes, there are actually **fewer** people than dwellings;
- in care homes and hotels, there are **more** people.

So we don't trust the cadastre blindly. We check it against two more independent sources:

1. **The Asturian parish layer**, which gives the number of dwellings for each parish.[^5]
2. **The European population grid** from the 2021 census, with 1 km cells.[^6]

If all three sources roughly agree, we can trust the cadastre. If they don't, we work out why.

## Step 3: How long it takes help to arrive

To calculate travel time, we need two things: where crews set out from, and which roads they take.

1. **Where they set out from.** In Asturias, there are 19 fire stations run by SEPA, the Asturian emergency service.[^7] We didn't find a ready-made layer with their coordinates and operating hours, so we'll put the table together ourselves. At night, only stations that are staffed round the clock can send crews out. That's where the 'by day and by night' in our question comes from. If all the stations around Lena are staffed round the clock, the daytime and night-time figures will be the same – and that's an answer too.
2. **Which roads they take.** We set the speeds for a fire engine, not a car: on a winding mountain road, the difference is big. We connect each home to the nearest road a vehicle can drive on.

**There's a boundary trap here.** The fire station that serves Lena may sit in the next municipality. And the fastest route may run through a neighbour too. If we clip the roads exactly to Lena's boundary, the times will be wrong, and the software won't show a single error.

**So we take the homes and lines inside the municipality, and the roads and fire stations with a margin around it.**

And one important limitation. We only calculate driving time, on clear roads. No call handling, no time to turn out, no snow and no fallen trees. The real time is always longer, especially in a storm. **Our travel time is a lower bound: help definitely won't arrive any faster.**

## Step 4: Where to start inspecting

Let's say every home already has a distance and a travel time. The company needs to decide where to start. How do we get one decision out of two numbers?

We could come up with a weighted formula: so much for distance, so much for time. But with a formula, we can't see why a section came first. So we do something simpler: a table, the **urgency matrix**:

- along one side, the distance band: close, medium, far;
- along the other, the travel time band: quick, medium, long;
- where they meet, a class from 1 (inspect first) to 5.

With a table like this, an engineer sees straight away why a section got class 1: for example, the homes are inside the strip and it takes a long time to get there. The table is kept in a separate file. The company can adapt it to its own needs without touching the code.

And let's be clear straight away: **the matrix isn't the probability of a fault. It shows how bad things will be if one happens.**

## Step 5: How we'll check the result

A result that can't be checked is no use to a company. So we need to know in advance how we'll check it.

The key word here is 'in advance'. There's a strong temptation to calculate first and then choose a check the result will pass. To stop that happening, we write down the checking rules **now**, before the first calculation, and save them to the project repository. The date they were saved will show that the rules came before the result.

| What we check | How we check it | What counts as a failure |
|---|---|---|
| Distances | a random sample of buildings near the lines and far from them, checked by eye on orthophotos | on the orthophoto, a home is clearly in a different band from the calculated one |
| Totals | dwellings summed by parish against dwellings summed by building | the totals don't match exactly |
| Dwellings | the cadastre against the parish layer and the population grid | large discrepancies that we can't explain |
| Travel time | a few routes worked out by hand, and an independent formula for fire engines | a time of a different order of magnitude |
| Portability | the same tool, without changing a single line of code, on another municipality: Cabrales | we have to change the code |
| Robustness | recalculating with different band boundaries and speeds | the list of sections to inspect first changes completely |

**Whatever a check shows is a result of the work on the project, even if we don't like it.**

## What the tool doesn't do

Let's be honest and write this down too, so we don't promise more than we deliver later on:

- it doesn't check whether the regulation is being met: that needs the real conductor sag;
- it doesn't work out who loses power: that needs the network layout, and that isn't publicly available;
- it doesn't assess electromagnetic fields;
- it doesn't claim that every line in the data is overhead: some may be underground.

## What we found

Let's compare this with the question for the lesson. Every word in the specification can now be calculated:

| Word | How we calculate it |
|---|---|
| 'close to a line' | the distance to the edge of the safety strip set by the regulation, in bands |
| 'how many people' | the number of dwellings from the cadastre, with two checks |
| 'how long it takes' | a fire engine's travel time on clear roads: a lower bound |
| 'what to inspect first' | the urgency class from the matrix |

And we have checking rules, written down before the calculation. **The specification is ready.**

It gives us the order of work. Each chapter is one stage:

2. Introduction to Python and PyQGIS.
3. The municipal boundary and our first script.
4. Power lines.
5. Voltage and the safety strip.
6. Can we trust the line data?
7. Residential buildings.
8. Who lives near the lines.
9. How quickly help arrives.
10. Where to start inspecting.
11. Rasters.
12. From script to tool.
13. Testing the tool.
14. The result for the company: a QGIS plugin.

## Your data

With our own data, we usually have to change:

- the speeds for each road class, to suit our own terrain;
- the boundaries of the distance and time bands;
- the urgency matrix, if the company has its own rules for what to inspect first.

These are all inputs, so there's no need to change the code.

## Summary

### New terms

- **Safety strip:** the zone around a line where, under the regulation, there should be no buildings. Example: ours is measured from the centre line, so it's a lower bound.
- **Distance to the edge of the strip:** the distance to the centre line minus the half-width of the strip. Example: a negative number means the home is inside the strip.
- **Distance band:** a step of distance from the edge of the strip. Example: 'inside the strip'.
- **Lower bound:** a value the real quantity can't be smaller than. Example: our travel time.
- **Urgency matrix:** a 'distance band × time band → class' table. Example: a home inside the strip, a long way to drive – class 1.

### Key points

- Every word in the specification has to be turned into something we can calculate.
- We compare homes near lines of different voltages by the distance to the edge of the strip.
- A strip measured from the centre line and travel times on clear roads are both estimates that make things look better than they really are. We say so openly.
- We take the roads and fire stations with a margin beyond the municipal boundary.
- We write down the checking rules before the calculation.

## Questions for an engineer

1. **Why do we compare homes by the distance to the edge of the strip, and not simply by the distance to the line?**

:::{dropdown} Answer
Lines of different voltages have safety strips of different widths. 20 metres from a 20 kV line may already be outside the strip, while 20 metres from a 400 kV line is still inside it. The distance to the edge of the strip takes the voltage into account, so homes become comparable.
:::

2. **Why don't we clip the roads exactly to the municipal boundary?**

:::{dropdown} Answer
The nearest fire station and the fastest route may lie beyond the boundary. A clipped road network will give the wrong times, and the software won't show a single error.
:::

3. **Why are the checking rules saved to the repository before the calculation?**

:::{dropdown} Answer
The date they were saved proves the rules came before the result. That protects us from the temptation to bend the checks to fit whatever we get.
:::

## Exercises

1. **Recall.** Which three numbers do we store for each building so we can compare homes near lines of different voltages? [Answer](#en-sol-1-2-1)

2. **Fill the gap.** What are the missing words? 'We take the homes and lines inside the municipality, and the roads and fire stations ______.' [Answer](#en-sol-1-2-2)

3. **Back to lesson 1.1.** Why do we give the tool everything that depends on the place as input? [Answer](#en-sol-1-2-3)

## The open question

The specification is ready, and before any calculation, we need the area itself: the municipal boundary. How do we get it in QGIS with code rather than with the mouse? And what is code, anyway – what is it made of? That's where chapter 2 begins: the introduction to Python and PyQGIS.

---

## Notes and sources

[^1]: [BOE, Royal Decree 223/2008, ITC-LAT 07, section 5.12.2](https://www.boe.es/buscar/pdf/2008/BOE-A-2008-5269-consolidado.pdf) (in Spanish). Accessed 19 November 2026.
[^2]: Ibid.: the distance Dadd + Del = 3.3 + Del, with a minimum of 5 m, is measured from the conductors at their maximum swing. See also [J. Andreu, 'Distancias eléctricas de seguridad en LAAT', FEMPA, 2016](https://www.fempa.es/wp-content/uploads/oldfiles/documentos/20160203111250_Jornada%20distancias%20electricas%20020216_Jorge%20Andreu.pdf) (in Spanish). Accessed 19 November 2026.
[^3]: [BOE, Royal Decree 223/2008, ITC-LAT 01](https://www.boe.es/buscar/pdf/2008/BOE-A-2008-5269-consolidado.pdf) (in Spanish): any nominal voltage above 1 kV is high voltage. Accessed 19 November 2026.
[^4]: [Spanish Directorate General for the Cadastre, INSPIRE buildings](https://www.catastro.hacienda.gob.es/webinspire/index.html): attributes currentUse, numberOfDwellings and dateOfConstruction; see also [IDEE, JIIDE 2017](https://www.idee.es/resources/presentaciones/JIIDE17/Apresentacoes_JIIDE2017_Conjunto_datos_edificios_BU_en_la_Direccion_General_del_Catastro_Pablo_Gomez_Dominguez.pdf) (in Spanish). Accessed 19 November 2026.
[^5]: [SITPA-IDEAS, Government of Asturias: administrative units](https://sig.asturias.es/servicios/rest/services/UnidadesAdministrativas/MapServer/layers): parish layer with SADEI data. Accessed 19 November 2026.
[^6]: [Eurostat GISCO, GEOSTAT 2021 population grid](https://ec.europa.eu/eurostat/en/web/gisco/geodata/population-distribution/population-grids). Accessed 19 November 2026.
[^7]: [112 Asturias, SEPA fire station network](https://www.112asturias.es/parques-bomberos) (in Spanish). Accessed 19 November 2026.
