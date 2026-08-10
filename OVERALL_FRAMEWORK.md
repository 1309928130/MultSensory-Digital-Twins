# Multisensory user experience assessment — overall framework

High-level description of how urban data, simulation models, and UX metrics connect in this project.

**Figure:** [framework_overall.png](framework_overall.png)

---

## 1. Purpose

Assess **multisensory pedestrian / street-level user experience** in a design or case-study area by combining:

- pedestrian demand and movement,
- visual quality of the streetscape,
- outdoor environmental conditions (sunlight, wind, noise, pollution),

into comparable **maps / indicators** that can be overlaid and synthesised.

<!-- TODO: 1–2 sentences on case study (e.g. Amsterdam Zuidas), research questions, and intended users of the framework. -->

---

## 2. Three columns (as in the diagram)

```text
  Data  →  Models / software  →  Metrics / indicators of user experience
```

| Column | Role |
|--------|------|
| **Data** | Urban infrastructure, traffic, and 3D design inputs |
| **Models — software** | Pedestrian + environmental simulations (with custom bridges) |
| **Metrics / indicators** | Quantities used to describe UX; most are synthesised by a shared Python overlay step |

Custom pieces developed in this project (per diagram footer):

- Python scripts
- Grasshopper scripts
- User guidance / manual editing steps

---

## 3. Data (inputs)

| Data group | Sources (from diagram) | Used by | Notes / TODO |
|------------|------------------------|---------|--------------|
| Urban infrastructure | Road networks (OSM); buildings (OSM + BAG); amenities (OSM); national travel survey (ODiN) | Macroscopic pedestrian model | <!-- fill paths, CRS, year --> |
| Traffic data | OmniTRANS | Microscopic pedestrian model (and pollution / exposure chain) | <!-- fill --> |
| Design data | 3D buildings and streets | Visual perception; sunlight; wind; noise; pollution | Rhino / design files |

**Coordinate systems / units:** <!-- TODO -->

**Case folders / shared naming:** <!-- TODO -->

---

## 4. Models and software

### 4.1 Pedestrian chain

| Model | Software / scripts | Inputs | Outputs (diagram) |
|-------|-------------------|--------|-------------------|
| **(Macroscopic) pedestrian model** | Python script | Urban infrastructure (+ ODiN) | Flow volume; travel demand → also feeds macro→micro |
| **MacroPed → SUMO** | Python conversion script | Macro outputs | Demand / network for SUMO |
| **(Microscopic) pedestrian model** | MassMotion **or** SUMO | Macro conversion + traffic data | Travel time; detailed flow / trajectories |
| Vehicle–human interaction | SUMO *(crossed out on diagram — not active)* | — | — |

See: [MACROPED_WORKFLOW.md](MACROPED_WORKFLOW.md).  
Micro workflow: <!-- TODO link when written -->

### 4.2 Visual perception

| Model | Software | Inputs | Output |
|-------|----------|--------|--------|
| Pedestrian visual perception | Rhino + Python | Design 3D + MassMotion trajectories | Street visual quality |

<!-- TODO: VISUAL_PERCEPTION_WORKFLOW.md -->

### 4.3 Environmental models

| Model | Software | Notes | UX metric |
|-------|----------|-------|-----------|
| **Sunlight** | Rhino + Ladybug | [SUNLIGHT_WORKFLOW.md](SUNLIGHT_WORKFLOW.md) | Comfort of sunlight (with wind) |
| **Wind** | Rhino + Eddy3D | [WIND_WORKFLOW.md](WIND_WORKFLOW.md) | Comfort of sunlight / wind |
| **Noise** | Rhino + Pachyderm | User guidance | Pollution exposure *(grouped with air pollution on diagram)* |
| **Pollution** | Rhino + Eddy3D (+ OpenFOAM / Python) | [POLLUTION_WORKFLOW.md](POLLUTION_WORKFLOW.md); Python + user guidance | Pollution exposure |

QGIS, Python, Grasshopper, and manual reading/editing appear as **bridges** between data and models (see diagram arrows).

---

## 5. Metrics / indicators of user experience

Most indicators feed a shared **Python script for overlapping / synthesising the mappings** (dashed box on the diagram).

| Indicator | Primary model(s) | In synthesis box? | Doc |
|-----------|------------------|-------------------|-----|
| Flow volume; travel demand | Macroscopic ped | Yes | [MACROPED_WORKFLOW.md](MACROPED_WORKFLOW.md) |
| Travel time | Microscopic ped | Yes | <!-- TODO --> |
| Crowd density | Micro *(crossed out)* | — | Not used |
| Street visual quality | Visual perception | Yes | <!-- TODO --> |
| Comfort of sunlight, wind | Sunlight + wind | Yes | [SUNLIGHT](SUNLIGHT_WORKFLOW.md) / [WIND](WIND_WORKFLOW.md) |
| Pollution exposure | Noise + pollution | Yes | [POLLUTION](POLLUTION_WORKFLOW.md); noise TBD |
| Flow on links / detailed trajectories | Microscopic ped | No (separate detailed ped data) | <!-- TODO --> |

**Synthesis script location / name:** <!-- TODO -->

**How overlays are defined (grid, pedestrian probes, link aggregation, weights):** <!-- TODO -->

---

## 6. Recommended reading order

1. This document + [framework_overall.png](framework_overall.png)  
2. Pedestrian demand: [MACROPED_WORKFLOW.md](MACROPED_WORKFLOW.md)  
3. Environment: [WIND_WORKFLOW.md](WIND_WORKFLOW.md) → [POLLUTION_WORKFLOW.md](POLLUTION_WORKFLOW.md) (pollution builds on wind) and [SUNLIGHT_WORKFLOW.md](SUNLIGHT_WORKFLOW.md) in parallel  
4. Micro / visual / noise / synthesis when those docs exist  

---

## 7. Open items

- [ ] Case study boundary and design scenarios  
- [ ] Naming of shared probe points / grids across wind, sun, pollution  
- [ ] Definition of “comfort” thresholds (wind, sun)  
- [ ] Weighting or multi-criteria rule in the synthesis script  
- [ ] Which micro tool is default for a given scenario (MassMotion vs SUMO)  
- [ ] Status of crossed-out diagram elements (vehicle–human; crowd density)  

---

*Draft scaffold aligned with `framework_overall.png`. Fill TODOs as workflows are documented.*
