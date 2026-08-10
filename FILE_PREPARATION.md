# File requirements for design alternatives

For each **design proposals or alternative**, we need the following files for assessment.

<hr style="border: none; border-top: 3px solid currentColor; margin: 1.5em 0;" />

## 1. Macro pedestrian flow simulation

Aspects involved: road networks, building quantities, building functions, transport nodes, parking space, and areas of green/sport parks. The required data include 2D lines of building profiles and Center lines of new roads are needed:

### 1.1 2D lines of building profiles (Figure 1)

Provide 2D building profile lines with **attribute user text** documenting:

- `origin_type`
- `GFA`

The example in Figure 1 is in the default Rhino format (`.3dm`). If you do not use Rhino, any common 2D format is fine (e.g. `dwg`, `dxf`, `shp`). Document the building information (`attribute user text`, `origin_type`, `GFA`) in a logical way for that format.

Our pedestrian model snaps each building’s information to its nearest road links. Therefore, for large buildings, to make the simulation more accurate, we suggest separating them into **sub-buildings**.

In Figure 1:

- Blue line = large building profile
- Yellow box lines = sub-buildings (information is stored on these)
- Example: whole building = 40 000 m² → each sub-building = 10 000 m²

**Allowed values of `origin_type`:**

`home`, `work`, `education`, `shopping`, `leisure` (assembly venues, hospitality, parks), `sport`, `healthcare`, `other`, `car_parking`, `bicycle_parking`, `motorcycle_parking`, `train_station`, `subway_station`, `tram_stop`, `bus_stop`

![Figure 1: Rhino Attribute User Text with origin_type and GFA on building profile lines](./file_prep_figure_1.png)

*Figure 1 — 2D building profiles with `origin_type` and `GFA` (Attribute User Text)*

### 1.2 Center lines of new roads (Figure 2)

![Figure 2: 3D massing with road centerlines](./file_prep_figure_2.png)

*Figure 2 — center lines of new roads*

<hr style="border: none; border-top: 3px solid currentColor; margin: 1.5em 0;" />

## 2. Sunlight, wind, pollution, and heat analysis

3D building geometry is involved.

- **3D buildings** — As abstract or detailed as the buildings in Figure 2. Rhino (`.3dm`) is preferred; SketchUp or other common formats are also fine, as long as they can be imported into Rhino.

<hr style="border: none; border-top: 3px solid currentColor; margin: 1.5em 0;" />

## 3. Visual assessment

Provide the following on **separate layers** in 3D files:

- Walkable areas (e.g. sidewalks, squares)
- Trees
- 3D buildings



