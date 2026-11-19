# Checks written before the first calculation

Written on 19 November 2026, before any data was downloaded or processed. The date of the commit that adds this file is the proof that these rules came before the results. If a rule is changed later, the change and its reason are recorded below, and results are reported under both versions.

| What we check | How | What counts as a failure |
|---|---|---|
| Distances | A random sample of buildings near the lines and far from them, checked by eye on the PNOA orthophoto | On the orthophoto a building is clearly in a different distance band from the calculated one |
| Totals | Dwellings summed by parish against dwellings summed by building | The totals do not match exactly |
| Dwellings | Cadastre dwellings against the SADEI parish layer and the GEOSTAT 2021 population grid | Large discrepancies that we cannot explain |
| Travel time | Several routes worked out by hand, and an independent travel-time formula for fire engines | A time of a different order of magnitude |
| Portability | The same tool run on Cabrales without changing any code | The code has to be changed |
| Robustness | Recalculation with different distance-band boundaries and road speeds | The list of top-priority line sections changes completely |

Whatever a check shows is a result of the project, including a failure.

## Changes to these rules

- None yet.
