---
title: "Chapter 1: What exactly do we want to know?"
---

## Where we left off

The problem is clear: trees fall on power lines and on roads, and in the mountain municipalities of Asturias it happens every winter. I couldn't find an open tool that shows where people live next to the lines and how long a fire crew takes to reach them.

So it has to be built. I'll calculate it for the municipality of Lena, my test case, and check it on Cabrales. But before I open QGIS, I want to know exactly what it should produce. Otherwise I can calculate everything correctly and still calculate the wrong thing.

## Who the tool is for

I start with the people who'll use it. There are three kinds of user, and each needs something different.

1. **The distribution company.** It owns and maintains the lines: it does the tree work along the line routes, plans line patrols and decides where to send a crew before a storm. It needs a list of **line sections** with the most people living nearby and the longest response times. It plans its work by line, not by house.

2. **The fire service and 112.** When a conductor comes down and a fire starts, 112 sends the fire service, which is usually first on scene. It needs to know in advance **which homes are next to the lines and how many minutes it takes to reach them**, by day and by night.

3. **The municipality.** It draws up the emergency plan and grants planning permission. It needs **a picture of the whole area**: which parts of the municipality have homes next to the lines and far from the fire service.

**So the main requirement is: one calculation, three levels of output – buildings, line sections and area.**

## What they'll get

You give it the boundary of a municipality, and it returns:

1. **A layer of residential buildings.** For each building:
   - the distance to the nearest overhead line, and that line's voltage;
   - the number of dwellings – flats or houses;
   - the year it was built, so the municipality can see whether people are still building next to the lines;
   - the fire crew's travel time, by day and by night;
   - a priority class from 1 to 5.
2. **A layer of line sections.** For each section: how many dwellings are nearby, the travel time to the hardest-to-reach one, and its priority class.
3. **Tables by parish** – the parts a municipality is divided into: how many buildings and dwellings fall in each class.
4. **A map of priority zones** without individual houses, for publication and for the municipality.
5. **A map of how complete the power line data is:** where the open data can be trusted and where it has gaps.
6. **A run report:** what went in and which version of the tool did the calculation. Without that, the calculation can't be repeated, so it can't be checked either.

Everything is saved as GeoPackage and CSV, so any of the three users can open the results in their own GIS.

And a word on personal data from the start. The layer with every building is a working layer: it stays with whoever runs the tool – the company, the fire service or the municipality. The published maps and tables only show totals by area, and figures based on very few dwellings are suppressed, because they could identify real households.

## What does 'close to a line' mean?

Here's the first subtlety. Twenty metres from a 20 kV line and twenty metres from a 400 kV line aren't the same thing.

Spain's rules for high-voltage lines, ITC-LAT 07, set a minimum distance between the conductors and buildings, and the higher the voltage, the larger it is. The rule itself can't be checked with open data: it's about the conductor, which sags between supports and swings in the wind, and all we have is the line's centreline on a map. So from the rules I take the width of a **safety clearance zone** around the centreline. I'll go through the exact values in the chapter on voltage.

I measure distance from the building's footprint, not its centre: for a large building, the difference can be tens of metres. For each building I keep three values:

1. **Distance to the centreline.** This is a measurement.
2. **Half-width of the zone** for that line's voltage. This is the rule.
3. **Distance to the edge of the zone** – the first minus the second. If the building is inside the zone, it's negative: it shows how many metres the house sits past the edge.

To compare buildings near different lines, I use the third value. A building's nearest line is the one whose zone edge is closest.

For the tables, distance is split into **distance bands**: for example, 'inside the zone', 'within 25 m of the edge', '25–50 m' and so on. Splitting into just 'inside' and 'outside' is too crude: a house 1 metre from the edge and one 40 metres away would land in the same group. I'll choose the band limits once I've seen the data.

In open data, a line's centreline is offset from the line's true position. I'll measure how far off it is in the chapter on the lines. **A building in a close band isn't a breach of the rules. It's a candidate for a site check.**

I only include lines above 1 kV, which Spanish rules already class as high voltage. There's almost no open data on the low-voltage lines in villages, and I'm not going to pretend it's complete. If a line's voltage is missing from the data, I take the widest zone possible for that type of line and flag it on the data map: better to check one house too many than miss the one that matters.

**So I need the voltage of every line and a table of half-widths from the rules.**

## How many people live nearby?

Open data doesn't say how many people live in each building. The closest stand-in is **the number of dwellings** from the Spanish cadastre.

It isn't an exact measure, and it's important to know which way it's wrong:
- in villages with lots of empty or holiday homes, it **overstates** the number of residents;
- in care homes and hotels, it **understates** it.

The national census (INE) shows what share of the dwellings in each area are main residences. With that, I'll estimate how much the count overstates in Lena's villages.

**The number of dwellings is a stand-in, and I'll know how far off it is.**

## How long does the fire crew take?

There are two things to know: where the crews leave from and which way they go.

1. **Fire stations.** I couldn't find an open layer, so I'll build the table myself: where each station is, which municipalities it covers and whether it's crewed overnight. 112 dispatches crews by area, so the station nearest on the map isn't necessarily the one that's sent.
2. **Roads.** I take them from OpenStreetMap. I set a speed for each road type – for a fire engine, not a car: on a winding mountain road, it makes a big difference. Each building is snapped to the nearest drivable road. If that's too far away, or if the only way there is a track, the building is flagged separately.

At night the time can change if a station isn't crewed and the crew has to come from one further away. I don't yet know whether the stations around Lena are crewed 24/7. I'll find out when I get to the fire stations. If they all are, the night-time figure will match the daytime one, and that's an answer too.

Fire stations are the default starting points. The company can put its own depots in their place, and the same calculation will show how long its crews take to reach the buildings along each section.

And let me be clear now: **I only calculate travel time on clear roads.** It doesn't include call handling or turnout time, and it doesn't include snow or fallen trees either. The real time is always longer, and in a storm longest of all.

Another trap is the boundary. The station that covers Lena may be in the next municipality, and the fastest route may run through it. If I cut the roads exactly at the boundary, the time will come out wrong and the program won't say a thing.

**So I take buildings and lines inside the municipality, but roads and fire stations with a margin of several kilometres around it.**

## Where do we start?

Every building now has a distance and a time. That isn't enough for the company: it needs to know where to start.

For that I use a **priority matrix**. On one axis is the distance band, on the other the travel-time band, and where they cross is a class from 1 (check first) to 5. For example:

| | up to 15 min | 15–30 min | over 30 min |
|---|---|---|---|
| **inside the zone** | 2 | 1 | 1 |
| **within 25 m of the edge** | 3 | 2 | 1 |
| **25–50 m** | 4 | 3 | 2 |
| **over 50 m** | 5 | 5 | 4 |

These numbers are only an example. I'll choose the real bands and classes in the chapter on priority.

Bear in mind that this isn't the probability of a fault. The matrix shows something else: how bad it would be if one happened. And why a matrix rather than one weighted formula? Because a formula hides what the result is made of. With the matrix, an engineer can see at once why a section is class 1: for example, there are houses inside the zone, and it takes more than 30 minutes to get there. The matrix is stored in a separate table, so the company can adapt it to its own rules without touching the code.

I build the line sections like this:
1. **A section is the span between two supports** where the data includes them, so the company can find it in its own asset register. Where there are no supports, the line is cut into sections of a fixed length.
2. **Each building is assigned to a single section** – the nearest one, by the same zone-edge rule.
3. **A section's class** is the most urgent class among its buildings, that is, the lowest number. Within the same class, sections are ranked by number of dwellings.

**The company gets a ranked list of sections, and every place on it can be explained.**

## Can we trust the result?

A result that can't be checked is no use to the company. So checking is built into every step:

1. **Lines from two sources.** I compare Spain's national topographic database (BTN) with OpenStreetMap. Where they disagree, the data is less reliable, and that goes on the data map.
2. **By eye, on aerial photos.** I check two samples against the PNOA orthophotos. The first is buildings in the close bands: is there really a house, and does a line run past it? The second is buildings far from every known line: could there be a line nearby that isn't in either source?
3. **Nothing has gone missing.** The total number of dwellings by parish must match the total by building. If it doesn't, some buildings have fallen through the gaps between parish boundaries, or been counted twice.
4. **Travel time by hand.** For a few buildings I plot the route myself and compare it with the calculation.
5. **Sensitivity.** I rerun the prioritisation with different band limits and different speeds. The sections that stay in class 1 under any reasonable settings are the most solid conclusions.
6. **Another municipality.** I run the same tool on Cabrales without changing a single line of code. If Cabrales needs the code changed, something somewhere only works for Lena.

**The most dangerous error is the one that doesn't raise an error.** If a line that really exists is missing from the data, the houses next to it will look 'safe' and the program won't notice. That's why I check how complete the line data is before I calculate any distances from it.

## What data do we need?

| What | Why | Where to get it in Spain |
|---|---|---|
| Municipal boundary | To clip buildings and lines | National Geographic Institute (IGN) |
| Overhead lines and supports | The hazard; line sections | BTN (IGN) and OpenStreetMap |
| Line voltage | Width of the safety clearance zone | OpenStreetMap |
| Residential buildings | Where people live, and how many | Spanish cadastre, INSPIRE buildings dataset |
| Fire stations | Where help comes from | A table I build myself |
| Roads | The route help takes | OpenStreetMap |
| Parishes | Totals by area | Principality of Asturias mapping service |
| Census | Share of main residences | INE |
| Orthophotos | Checking by eye | PNOA |

All of this data is open and free. I'll find out how complete it is as I go.

:::{admonition} Your data
Lines, supports and roads from OpenStreetMap exist for every country, but lower-voltage lines often have no voltage tag – check that first. Not every country publishes buildings with the number of dwellings. In Spain they're in the cadastre, except in Navarre and the Basque Country, which have their own. You'll almost always have to compile the fire-station table yourself. Use your own country's clearance rules: in the half-width table you only need to change the values. And choose a projected coordinate system in metres for your area; for Asturias it's EPSG:25830.
:::

## In what order do we work?

The data itself sets the order: distance can't be calculated without lines and buildings, time can't be calculated without stations and roads, and priority needs both. Each stage is a chapter.

**Getting ready**

2. Python in QGIS: the first script
3. Municipal boundary and coordinate system

**Input data**

4. Overhead lines from two sources
5. Line voltage and the safety clearance zone
6. How complete the line data is
7. Residential buildings from the cadastre

**Who lives next to the lines**

8. Distance from buildings to lines
9. Homes next to the lines by parish

**How fast help arrives**

10. Fire stations and response areas
11. Roads as a routing network
12. Travel time by day and by night

**What to check first**

13. Priority of buildings and line sections
14. How sensitive the result is

**The finished tool**

15. From script to Processing algorithm
16. Testing on another municipality: Cabrales
17. Maps and report for the company
18. QGIS plugin and version 1.0

In the end we won't have a script but a **QGIS plugin**: you install it from a ZIP file, and our algorithm appears in the Processing Toolbox. That's how tools are usually rolled out in a company.

## What the tool doesn't do

It doesn't assess **where a fault is more likely**. For that you'd need to know what kind of forest grows along the route and how it behaves in a storm, and that's a separate, large piece of work. The tool answers a different question: if a fault happens, who's nearby and how long it takes to reach them.

I set out the other limits – regulatory compliance, power cuts and electromagnetic fields – in the prologue, and they still apply.

## Summary

**New terms**
- **Overhead line:** a line whose conductors hang from supports.
- **Dwelling:** a flat or a house.
- **Safety clearance zone:** a band around a line's centreline whose width depends on the voltage.
- **Distance band:** a range of distances between a building and the edge of the zone, for example 25–50 m.
- **Travel time:** how long it takes to drive from the fire station to the building on clear roads.
- **Priority matrix:** a table in which the crossing of a distance band and a travel-time band gives a class from 1 to 5.

**Key points**
- Distance is compared with the edge of the safety clearance zone, because the width of the zone depends on the voltage.
- The most dangerous error is a missing line: the program won't spot it, so I check the data before calculating.

## Questions for an engineer

1. A building is 30 m from the centreline of a 20 kV line and 35 m from the centreline of a 400 kV line. Which is its nearest line?

:::{dropdown} Answer
The one whose zone edge is closer. The 400 kV line's zone is wider, so its edge can be closer even though the centreline is further away. If I measured to the centreline, I could assign the building to the lower-voltage line.
:::

2. Why isn't it enough to check only the buildings next to known lines on the aerial photos?

:::{dropdown} Answer
Because that sample already starts from the lines that are in the data. If a line isn't in either source, the buildings next to it don't make it into the sample, and the check won't find it. A missing line can only be found by also looking where, according to the data, there are no lines.
:::

3. Why is a section's class the most urgent of its buildings' classes, rather than the average?

:::{dropdown} Answer
Because a single house inside the zone that takes 40 minutes to reach is already a reason to check the section, however many houses further from the line share the section. An average would dilute that house among the others, and the section would look safer than it is.
:::

## The open question

We have the plan, we know what data we need and in what order to work. Time to open QGIS. Where is Python inside it, and how do we give it its first command?
