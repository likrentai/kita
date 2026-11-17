# Kita: Malaysia in 3.2 million dots

**Live page: https://likrentai.github.io/kita/**

**Case study: https://likrentai.github.io/kita/case-study.html** ([PDF](https://likrentai.github.io/kita/case-study.pdf))

"Kita" is Malay for "us". The 2020 census counted 32.4 million people living in Malaysia. This page draws them as 3.24 million dots, one for every ten people. Scrolling down the story moves the map and turns the dots into charts.

1. **The country in dots.** Half of everyone lives on about 2% of the land. The Klang Valley holds 28% of the people on 2.5% of the land. Sabah and Sarawak hold 18% of the people on 60% of the land.
2. **Who lives where.** The dots are coloured by six census groups: Malay, Other Bumiputera, Chinese, Indian, Other citizens and Non-citizens.
3. **The dots leave the map.** Every dot flies into an age pyramid, then into urban and rural blocks.
4. **First, a guess.** You draw how the 160 district median household incomes rise from the poorest district to the richest. Then the real curve and a beeswarm of all districts appear.
5. **Where do you stand?** Pick a district and type a monthly household income to see how many households in that state earn less.
6. **Explore.** Filter the dots by group, sex, urban or rural and age, and find any district by name.

The page works best on a desktop or laptop with a graphics card. On a phone the story sits in a sheet at the bottom of the screen.

## How it is made

Five notebooks in Python 3.14 build the data. The page itself is plain HTML with MapLibre GL JS and deck.gl, and it needs no build step.

| Notebook | What it does |
|---|---|
| `00datacheck.ipynb` | Downloads every source into `data/raw` and checks totals (32,447,385 people, 160 districts). |
| `01dots.ipynb` | Places the dots. People are counted for 613 small areas (600 state constituencies and 13 Federal Territory seats). Inside each area, dots fall into the Kontur 400 m hexagons in proportion to their population. Then a random point is picked inside each hexagon. Group, sex and age come from the published tables and are mixed at random among the area's dots; each district's urban count goes to its densest places first. |
| `02builddots.ipynb` | Writes the dots for the web as eight binary chunks (`docs/data/dots_00.bin` to `dots_07.bin`) and draws the whole-country images used below zoom 6.5. |
| `03buildplaces.ipynb` | Writes the town labels (`docs/data/towns.json`, from the hand-made `data/places/towns.csv`) and the 613 small areas with their census numbers (`docs/data/areas.geojson`). |
| `04buildincome.ipynb` | Writes district medians and state income percentiles from the Household Income Survey 2022 (`docs/data/income.json`). |

To rebuild, create an environment and install the packages in `requirements.txt`. Then run the notebooks in order from the repository folder. To view the page, run `python -m http.server 8000` inside `docs` and open http://localhost:8000.

## Data sources

- **Census 2020** by district, state constituency (DUN) and parliamentary seat, with boundaries: Department of Statistics Malaysia (DOSM), [dosm-malaysia/data-open](https://github.com/dosm-malaysia/data-open), under the licence in that repository.
- **Population by district** and **Household Income Survey 2022** (district medians, state percentiles): [OpenDOSM](https://open.dosm.gov.my), CC BY 4.0.
- **Kontur Population** for Malaysia (400 m H3 hexagons, 1 November 2023): Kontur, on the [Humanitarian Data Exchange](https://data.humdata.org/organization/kontur), CC BY 4.0. It is used only to decide where dots fall inside each area. The totals come from the census.
- **Basemap**: CARTO Dark Matter (no labels), using OpenStreetMap data (c) OpenStreetMap contributors.

## Limits

- Dots are modelled positions, not addresses. Within each small area the groups, ages and sexes are scattered at random among its dots, so the map shows differences between areas, never between streets or households.
- Sex and age by group, and urban or rural, are published by district, not by small area, so those attributes follow the district mix.
- District totals from the dots differ slightly from the census district tables (median difference 0.9%), because the dots are counted by state constituency and some constituencies cross district lines.
- Income figures are from 2022. Percentiles are published for states, not districts, so "Where do you stand?" compares a household with its whole state. Incomes have risen since 2022.
- The census counts everyone living in Malaysia, non-citizens included. The page says "people", not "Malaysians", for totals.

## Repository layout

```
00datacheck.ipynb ... 04buildincome.ipynb   data notebooks
data/places/towns.csv                        town labels (hand-made)
docs/index.html                              the page
docs/data/                                   data used by the page
requirements.txt                             Python packages for the notebooks
```

`data/raw` and `data/processed` are not in the repository. `00datacheck` and `01dots` download or rebuild them.

## Licence

The code is under the MIT licence (see `LICENSE`). The data files in `docs/data` are derived from the sources above and remain under their licences. Please credit DOSM, OpenDOSM and Kontur when you reuse them.
