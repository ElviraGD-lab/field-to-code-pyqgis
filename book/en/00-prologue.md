---
title: "Prologue: When the lights go out"
---

Before we write any code, we need to understand the problem: what happened, who it affects and what exactly we want to calculate. Otherwise, it's easy to do a correct calculation that answers the wrong question.

So we won't start with QGIS. We'll start with what happens when the power goes off.

## A rare kind of outage

**28 April 2025, 12:33.** The whole of Spain and Portugal lost power – the entire peninsula, all at once.

Trains and metros stopped. Traffic lights went dark. Cash machines and tills stopped working. By evening, the backup batteries at mobile masts had begun to run flat, and the mobile signal started to drop out.

Almost a year later, ENTSO-E, the association of Europe's transmission system operators, published its final report. It wasn't a storm, a fire or a fallen tree. There were several causes, and the main one was that the system couldn't hold its voltage. Generators tripped one after another, and within seconds the grid fell apart.[^1]

**An outage like this is rare.** It happened under clear skies and hit everyone at once.

**An ordinary outage works differently.** It comes with the weather, starts in one place and lasts longer. For the people it affects, it's more dangerous. **This book is about the ordinary outage.**

## An ordinary outage: Asturias, 2018

Let's look at two reports from Asturias.

**February 2018.** A snowstorm. Trees fall on power lines, slopes slide. Around 10,000 customers are left without power:

- about 6,000 EDP customers in Ibias, Tineo, Somiedo, Cangas del Narcea, Quirós and Laviana;
- about 2,500 Viesgo customers in Mieres, Aller and Lena;
- several hundred in Los Oscos and Grandas de Salime.[^2]

The 112 emergency service's report contains a sentence that matters most to us. The companies explain why repairs are slow: roads and mountain passes are blocked by snow, fallen trees and landslides. In other words, the crews are there, but they can't get to the fault.

**October 2018.** Wet snow. At the peak, around 15,000 homes are without power; by evening, around 13,500 still are. The municipalities are almost the same: Degaña, Ibias, Somiedo, Cangas del Narcea, Quirós, Teverga, Caso, Lena, Aller, Los Oscos. Trees fall on roads in 17 municipalities, and there are 169 incidents in all.[^3]

Let's put the two reports side by side. Two things happen in both:

1. **The storm brings down lines** – the power goes off.
2. **Trees and snow block the roads** – help can't get through.

And they happen in the same mountain municipalities.

**So in the mountains, the danger isn't just the fault itself. It's also how long it takes to reach it.**

## It isn't just Asturias

Could this be a local story? Let's check what happened in other European countries. We'll take three major storms and see what was learnt from each.

1. **Sweden, 2005, Storm Gudrun.** Around 250 million trees blown down. About 700,000 people without power, some for up to 35 days. Afterwards, Sweden began putting its distribution lines in forests underground.[^4]
2. **United Kingdom, 2021, Storm Arwen.** More than a million customers without power, about 40,000 of them for over three days and nearly 4,000 for over a week. Almost all the damage came from trees and branches. The regulator, Ofgem, found two things. First, the money for tree work had gone on meeting the rules, not on making the network able to withstand a storm. Second, the companies hadn't known in advance where their most vulnerable customers lived.[^5]
3. **Portugal, 2026, Storm Kristin.** More than a million customers without power, and over 6,000 km of distribution network affected. Two weeks later, about 30,000 customers were still without electricity. Repairing the network cost the company E-REDES about €110 million. That's roughly a fiftieth of the total damage, which was estimated at €5.3 billion. Only 26% of it was insured.[^6]

The European Environment Agency adds one more thing. About 6 in 10 power failures cause knock-on failures in other sectors, most often telecoms.[^6] First the lights go out, and then the phone people would use to call for help goes quiet.

**So this is a shared problem: trees, storms and a long way to reach the people who need help.**

## Why not put the wires underground

It's the first thing that comes to mind. The short answer: underground is more expensive and more complicated, and repairs take longer. The figures below are for 400 kV transmission lines.[^7]

1. **More expensive.** Underground cable costs €4–8 million per kilometre, and over €10 million on difficult terrain. An overhead line costs €0.6–1 million in the countryside and €1.5–2 million in the mountains.
2. **More complicated.** A long AC cable behaves like an enormous capacitor. Beyond roughly 30–35 km, it needs separate, expensive equipment, without which it won't work.
3. **Repairs take longer.** A broken overhead conductor can be seen from the road. If crews can get to it, it's fixed within hours. A damaged cable first has to be located, then dug up, and a new section fitted. That takes days or weeks.

Sweden showed that part of a network can go underground. But in mountains with steep slopes and narrow valleys, most lines will stay on towers for a long time yet.

**So the question isn't how to get rid of overhead lines. It's how to live alongside them and know where the risk is.**

## What makes a line dangerous for the people who live nearby

It's easy to mix up two different things here. Let's separate them.

1. **The danger from the line itself.** A conductor that touches a tree or comes down can set a forest alight. A live wire on the ground can kill. Both get more dangerous the closer a home is. A house 20 metres from a line and a house a kilometre away are different stories. The consequences can be costly. In California, fires started by power lines have become the central issue in utilities' plans.[^8] In Chile, a court ordered a company to compensate the victims of a fire that started on a poorly maintained line.[^9]
2. **Who loses power.** That doesn't depend on distance from the line. It depends on the layout of the network: who is connected 'downstream' of the fault. A house right under the line may keep its power, while a village three kilometres away loses it.

**We're dealing with the first:** who lives near the source of danger, and how quickly help can reach them. The second needs the network layout, and that isn't publicly available.

One more clarification. When people in Spain talk about 'homes near high-voltage lines', they usually mean electromagnetic fields and health. We don't cover that.

## What the regulation requires, and what it leaves out

Spain's regulation for high-voltage overhead lines is called ITC-LAT 07. It already requires vegetation near conductors to be managed. But the check it sets is geometric: does the tree reach the conductor or not.[^10]

The regulation says nothing about:

- where people live near the line;
- how many minutes it takes fire crews to get there;
- which section of line to inspect first.

**That's the difference between 'complying with the rules' and 'managing risk'.** A company that knows where its lines come closest to homes, and where help takes longest to arrive, can start its clearance work on those sections. And it can show that it knows the risk and is dealing with it.

The law is heading the same way. Spain is preparing a law on the protection and resilience of critical entities, which transposes European Directive 2022/2557. Energy facilities designated as critical will have to assess their risks, natural ones included, and have a resilience plan.[^11]

## What already exists

Before we build our own, let's see what others have already done.

1. **United States.** Utilities calculate risk as the probability of a fault multiplied by its consequences. They model the consequences from building density and vegetation on a grid of cells about 200 metres across. But these models are closed, commercial and built for Californian wildfires.[^8]
2. **Asturias.** In 2024, Red Eléctrica and the government of Asturias agreed to de-energise lines during fires, inspect line routes in fire-prone areas and share the fire danger index.[^12] The new forest fire plan for 2026–2030 calculates how long it takes to reach a fire in each district of Asturias. But it works from past fires, not from homes near power lines.[^13]

We looked for an open tool that answers two questions at once for any municipality: which homes stand close to overhead lines, and how long it takes help to reach them, by day and by night. **We didn't find one.**

## What we'll build

So we'll build it ourselves – in QGIS, with Python.

The tool takes the name of a municipality as its input. As its output, it gives:

- residential buildings and the distance from each one to the nearest line;
- how many dwellings those buildings contain;
- how many minutes it takes fire crews to reach each building, by day and by night;
- the sections of line where inspection should start;
- a map of where the open data on power lines can be trusted and where it has gaps.

We don't know yet what numbers we'll get. We'll only find out once we've written the code and it has done the calculations.

**What the tool doesn't claim:**

- a home close to a line isn't a breach of the rules, just a place worth checking;
- we calculate travel time on clear roads, and in a real storm it'll be longer;
- we don't assess electromagnetic fields.

We'll work through the example of the municipality of Lena, which appears in both 2018 reports. The same path works for any municipality, in any country with power lines, homes and roads. Wherever we need to plug in our own data, we'll point it out.

## How we'll learn

We know QGIS, but we've never written code. So we'll start Python from scratch, right inside QGIS, and learn only what our task needs.

Each step of the project is a lesson:

1. first, we decide what we want to get;
2. then we write the code and go through it line by line;
3. then we look at what we've got and check whether we can trust it.

There'll be mistakes too. There's no learning, and no engineering, without them.

**This book isn't about PyQGIS for its own sake. It's about using it to solve a real problem.**

---

## Notes and sources

[^1]: [ENTSO-E, final report on the incident of 28 April 2025, March 2026 – as reported by pv magazine, 23 March 2026](https://www.pv-magazine.com/2026/03/23/entso-e-report-finds-systemic-failures-behind-2025-iberian-blackout/).
[^2]: [112 Asturias – coordination of power and telecoms restoration, February 2018](https://www.112asturias.es/112-asturias-coordinara-actuaciones-para-restablecimiento-suministro-electrico-telefonico).
[^3]: [RTPA, 'Sin luz en hogares y negocios', 28 October 2018](https://www.rtpa.es/noticias-asturias:Sin-luz-en-hogares-y-negocios_111540715885.html).
[^4]: [The Irish Times, 15 February 2025](https://www.irishtimes.com/environment/2025/02/15/ella-mcsweeney-after-storm-eowyn-ireland-must-do-what-sweden-did-20-years-ago).
[^5]: [Ofgem, *Final report on the review into the networks' response to Storm Arwen*, June 2022](https://www.ofgem.gov.uk/sites/default/files/2022-06/Final%20report%20on%20the%20review%20into%20the%20networks'%20response%20to%20Storm%20Arwen.pdf).
[^6]: [EEA, *Europe's critical infrastructure under climate stress: lessons from 2026*](https://www.eea.europa.eu/en/analysis/publications/europes-critical-infrastructure-under-climate-stress-lessons-from-2026).
[^7]: [European Commission, report on undergrounding solutions, 2008](https://ec.europa.eu/ten/energy/coordinators/doc/2008_03_18_report_undergrounding_solutions_es.pdf).
[^8]: [E3 – *Review of PG&E's 2021 Wildfire Distribution Risk Model*](https://www.ethree.com/wp-content/uploads/2022/01/E3_Review_of_PGEs_2021_Wildfire_Distribution_Risk_Model.pdf).
[^9]: [CNN Chile – CGE, 2019 forest fire](https://www.cnnchile.com/pais/cge-debera-pagar-400-millones-a-vecinos-de-navidad-por-responsabilidades-en-incendio-forestal-de-2019/).
[^10]: [BOE – Royal Decree 223/2008, ITC-LAT 07, §5.12.1](https://www.boe.es/buscar/pdf/2008/BOE-A-2008-5269-consolidado.pdf).
[^11]: [ECIJA – *Proyecto de Ley de Protección y Resiliencia de Entidades Críticas* (Draft Law on the Protection and Resilience of Critical Entities), April 2026](https://www.ecija.com/actualidad-insights/proyecto-de-ley-de-proteccion-y-resiliencia-de-entidades-criticas-transposicion-de-la-directiva-ue-2022-2557-cer/).
[^12]: [Red Eléctrica – meeting with the Principality of Asturias, 18 April 2024](https://ree.es/sites/default/files/2024-06/NP_Reuni%C3%B3n%20Principado%20Asturias_OK_18_abril_24.pdf).
[^13]: [Principality of Asturias – EPLIFA 2026–2030](https://www.asturias.es/documents/d/guest/eplifa_2026-2030-con-anejos_03062026).
