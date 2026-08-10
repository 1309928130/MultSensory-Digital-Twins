# Macroscopic pedestrian flow prediction

Workflow for the **(macroscopic) pedestrian model**: demand generation and network assignment that produce **flow volume** and **travel demand** indicators, and that feed the microscopic chain (MacroPed → SUMO / MassMotion).

**Stack:** Urban data (OSM, BAG, amenities, ODiN) → Python macroscopic model (`PedModel` / `main.py`) → link flows & demand outputs → (optional) conversion to micro simulation.

Framework context: [OVERALL_FRAMEWORK.md](OVERALL_FRAMEWORK.md).  
Repo overview: project `README.md` (Macroscopic Pedestrian Model — Amsterdam Zuidas case).

---

## 0. What you need before starting

1. Python environment for this repo: <!-- TODO: env file / requirements -->  
2. Input data under `input/` (and any LFS assets): road network, buildings, amenities, ODiN-derived rates.  
3. <!-- TODO: QGIS / preprocessing steps if run outside Python -->  
4. Optional: OmniTRANS / traffic inputs are **not** required for the pure macro demand–assignment path (they appear on the micro side of the framework diagram).  

---

## 1. Overview of the method

| Step | Goal |
|------|------|
| A | Build / load walking network and land-use / amenity context |
| B | Process ODiN (or survey) into trip rates by OD type and hour |
| C | Generate trips (demand) |
| D | Assign trips to the network (e.g. shortest-path / BPR-style costs) |
| E | Export flow volume, travel demand, and diagnostics |
| F | (Optional) Convert macro outputs for SUMO / MassMotion |

| Framework output | Meaning in this project |
|------------------|-------------------------|
| Flow volume; travel demand | Link / OD level demand and assigned flows |
| Input to micro | Via **(Macro)PedModel-to-SUMO** Python script |

---

## 2. Data inputs

Aligned with the overall diagram (**urban infrastructure data**).

| Layer | Source | Role | Path / notes |
|-------|--------|------|--------------|
| Road / walking network | OSM | Graph for assignment | <!-- TODO --> |
| Buildings | OSM + BAG | Attractors / generators / land use | <!-- TODO --> |
| Amenities | OSM | Destination types | <!-- TODO --> |
| National travel survey | ODiN | Trip rates by purpose / hour | <!-- TODO --> |

**CRS / units:** <!-- TODO -->  
**Case extent (e.g. Zuidas):** <!-- TODO -->

Further notes on flow behaviour (directional asymmetry, zero hours, etc.): see repo `FLOW_EXPLANATIONS.md`.

---

## 3. Model pipeline (Python)

High-level stages — fill with notebook cell / module names as needed:

| Stage | Module / entry | Description |
|-------|----------------|-------------|
| Network construction | <!-- TODO: modules/... --> | |
| Building / amenity processing | <!-- TODO --> | |
| ODiN → trip rates | <!-- TODO --> | |
| Trip generation | <!-- TODO --> | |
| Trip assignment | <!-- TODO: e.g. modules/trip_assignment.py --> | |
| Calibration / sensors | <!-- TODO: e.g. modules/calibration_sensor*.py --> | |
| Visualisation | <!-- TODO: modules/trip_assignment_viz.py --> | |

**Main entry:** `main.py` (and/or `X_script_run.sh`).

### 3.1 Run procedure

```bash
# TODO: exact command(s)
# e.g. bash X_script_run.sh
# or: python main.py ...
```

### 3.2 Key parameters

| Parameter | Typical value | Meaning |
|-----------|---------------|---------|
| <!-- TODO --> | | |
| <!-- TODO --> | | |

---

## 4. Outputs

| Output | Format / path | Used for |
|--------|---------------|----------|
| Edge / link flows (by hour) | <!-- TODO --> | Flow volume maps; calibration |
| OD / travel demand | <!-- TODO --> | Demand indicator; micro conversion |
| Visualisations | <!-- TODO --> | Reporting |
| Macro → SUMO inputs | <!-- TODO --> | Microscopic model |

**UX metric handoff:** flow volume and travel demand enter the **Python synthesis / overlay** script (dashed box on the framework diagram). Exact file contract: <!-- TODO -->

---

## 5. Bridge to microscopic models

| Step | Script | Notes |
|------|--------|-------|
| (Macro)PedModel → SUMO | <!-- TODO: script path --> | Diagram: Python conversion |
| MassMotion demand / trajectories | <!-- TODO --> | Visual perception uses MassMotion trajectories |

Micro workflow doc: <!-- TODO: MICROPED_WORKFLOW.md -->

---

## 6. Scripts in this repo (macro-relevant)

| Asset | Purpose |
|-------|---------|
| `main.py` | Main model pipeline |
| `modules/` | Model components |
| `X_script_run.sh` | Run script |
| <!-- TODO: MacroPed-to-SUMO script --> | Micro handoff |

---

## 7. Checklist (new scenario / new year)

- [ ] Network and buildings cover the study area  
- [ ] ODiN (or rates) processed for the intended period  
- [ ] Assignment completed; flows inspected for odd zeros / asymmetries  
- [ ] Calibration against sensors (if applicable) documented  
- [ ] Exports named for synthesis and for micro conversion  
- [ ] Parameters and random seeds recorded  

---

## 8. Pitfalls

1. Directed network: opposite directions can differ strongly — see `FLOW_EXPLANATIONS.md`.  
2. Zero flows for some hour–link pairs can be data-driven (no ODiN trips) or routing-driven.  
3. <!-- TODO: project-specific pitfalls -->  

---

## 9. Suggested next improvements

- <!-- TODO -->  
- Document the MacroPed→SUMO conversion in one place.  
- Freeze the export schema expected by the synthesis script.  

---

*Draft scaffold. Fill with concrete paths, parameters, and conversion scripts from the PedModel pipeline.*
