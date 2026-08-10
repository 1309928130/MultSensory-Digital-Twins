# Sunlight / solar access assessment (Rhino + Ladybug)

Workflow used in this project to evaluate **sunlight** (solar access / radiation / shade) with **Ladybug** in Rhino / Grasshopper, for outdoor comfort and multisensory UX mapping.

**Stack:** Rhino + Ladybug (+ Grasshopper) → solar / sun-vector analysis → maps or probe values → UX metric (**comfort of sunlight**, with wind in the framework diagram).

Framework context: [OVERALL_FRAMEWORK.md](OVERALL_FRAMEWORK.md).

---

## 0. What you need before starting

1. 3D design geometry (buildings / streets / optional trees or canopies) in Rhino.  
2. <!-- TODO: Rhino + Grasshopper + Ladybug (+ Honeybee if used) versions -->  
3. Weather / location file: <!-- TODO: EPW path, city, timezone -->  
4. Analysis period: <!-- TODO: e.g. summer afternoon, annual, specific hours -->  
5. Evaluation surfaces or points: <!-- TODO: street grid, pedestrian probes, façade samples -->  

---

## 1. Overview of the method

| Step | Goal |
|------|------|
| A | Import / align design geometry with location CRS / north |
| B | Load EPW (or equivalent) and set analysis period |
| C | Run Ladybug sunlight / radiation / shade analysis |
| D | Sample results at pedestrian-relevant locations |
| E | Derive comfort / access indicators; export for synthesis |

**Exact Ladybug components used in this project:** <!-- TODO: e.g. Ladybug_Sunpath, Radiation Analysis, Direct Sun Hours, … -->

---

## 2. Geometry and context

### 2.1 Rhino / design inputs

| Item | Location / name | Notes |
|------|-----------------|-------|
| Buildings / streets | <!-- TODO --> | Prefer same design data as wind / visual models |
| Context buildings (outside design) | <!-- TODO --> | Affects shade |
| Ground / analysis mesh | <!-- TODO --> | |
| North / project location | <!-- TODO --> | |

### 2.2 Units and coordinates

<!-- TODO -->

---

## 3. Ladybug setup

### 3.1 Climate and time

| Setting | Value |
|---------|-------|
| EPW / location | <!-- TODO --> |
| Analysis period(s) | <!-- TODO --> |
| Time step | <!-- TODO --> |

### 3.2 Analysis type(s)

Mark what this project actually computes:

| Analysis | Used? | Notes |
|----------|-------|-------|
| Direct sun hours | <!-- yes/no --> | |
| Cumulative radiation (Wh/m²) | <!-- yes/no --> | |
| Shade / shadow study | <!-- yes/no --> | |
| UTCI or other thermal comfort | <!-- yes/no --> | If coupled with wind / MRT |
| Other | <!-- TODO --> | |

### 3.3 Run procedure

```text
<!-- TODO: GH definition name, key component graph, bake / export steps -->
```

---

## 4. Sampling and export

| Item | Path / script | Notes |
|------|---------------|-------|
| Analysis grid / points | <!-- TODO --> | Align with wind / pollution probes if possible |
| Raw Ladybug output | <!-- TODO --> | |
| Export for Grasshopper / Python | <!-- TODO --> | |
| File handed to synthesis | <!-- TODO --> | |

---

## 5. Metrics for UX synthesis

Framework label: **comfort of sunlight, wind** (shared box with wind).

| Metric | Definition | Thresholds / comfort rule | Notes |
|--------|------------|---------------------------|-------|
| Sun access / hours | <!-- TODO --> | <!-- TODO --> | |
| Radiation on street | <!-- TODO --> | <!-- TODO --> | |
| Combined sun–wind comfort | <!-- TODO --> | <!-- TODO --> | How Ladybug + Eddy3D results are merged |

**Output handed to synthesis script:** <!-- TODO -->

---

## 6. Scripts / GH definitions in this project

| Asset | Purpose |
|-------|---------|
| <!-- TODO --> | |
| <!-- TODO --> | |

---

## 7. Checklist (new case / new design)

- [ ] Geometry and north / location correct  
- [ ] EPW matches the case city  
- [ ] Analysis period documented and justified  
- [ ] Context shading included if relevant  
- [ ] Sample points comparable to other environmental layers  
- [ ] Comfort map exported for synthesis  

---

## 8. Pitfalls

1. <!-- TODO -->  
2. Mismatch between Ladybug analysis grid and Eddy3D probe points → hard to overlay.  
3. <!-- TODO -->  

---

## 9. Suggested next improvements

- Document joint sun–wind comfort rule used in the synthesis step.  
- <!-- TODO -->  

---

*Draft scaffold. Fill from the Ladybug / Grasshopper definitions used for this case study.*
