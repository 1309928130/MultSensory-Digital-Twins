# Outdoor wind assessment (Rhino + Eddy3D)

Workflow used in this project to evaluate **outdoor wind** (and wind comfort–related fields) with **Eddy3D** in Rhino / Grasshopper, producing probe / map outputs for UX metrics and for downstream pollution CFD.

**Stack:** Rhino + Eddy3D → OpenFOAM (via Eddy3D) → wind field (`U`, …) → probes / post-processing → Grasshopper visualisation (and optional comfort indicators).

**Related:** pollution is layered on a finished wind case — see [POLLUTION_WORKFLOW.md](POLLUTION_WORKFLOW.md). Framework context: [OVERALL_FRAMEWORK.md](OVERALL_FRAMEWORK.md).

---



## 0. What you need before starting

1. 3D design geometry (buildings / streets) in Rhino, consistent with the case CRS / units.
2. 
3. Wind climate / EPW or statistical wind rose inputs: 
4. Pedestrian evaluation height / probe strategy: 

**Important (from pollution workflow):** do **not** let Eddy3D rewrite `system/` while a custom OpenFOAM run is in progress. Prefer finishing wind in Eddy3D before heavy custom `controlDict` edits.

---



## 1. Overview of the method


| Step | Goal                                                             |
| ---- | ---------------------------------------------------------------- |
| A    | Prepare / clean 3D geometry for CFD domain                       |
| B    | Set up Eddy3D outdoor wind case (mesh + ABL / inlet)             |
| C    | Run wind directions of interest (angle subfolders)               |
| D    | Check convergence / usability of `U` (and turbulence fields)     |
| E    | Probe wind at pedestrian points; export for GH / comfort metrics |
| F    | (Optional) Feed same case / probes into pollution or synthesis   |


Wind comfort metrics used in the framework diagram: **comfort of sunlight, wind** (joint indicator box — exact combination rule TBD).

---



## 2. Geometry and domain



### 2.1 Rhino / design inputs


| Item                     | Location / name | Notes                                        |
| ------------------------ | --------------- | -------------------------------------------- |
| Buildings / streets      |                 | Same design data as sunlight / visual models |
| Ground / terrain         |                 |                                              |
| Domain extent / blockage |                 |                                              |




### 2.2 Units and coordinates



---



## 3. Eddy3D wind setup



### 3.1 Mesh


| Setting                   | Value / notes |
| ------------------------- | ------------- |
| Mesh type / refinement    |               |
| Case folder example       |               |
| Wind-direction subfolders |               |




### 3.2 Boundary conditions / wind climate


| Item                          | Notes |
| ----------------------------- | ----- |
| Reference wind speed / height |       |
| Directions simulated          |       |
| Turbulence model              |       |
| Solver                        |       |




### 3.3 Run procedure

```text
<!-- TODO: Grasshopper / Eddy3D click-path or batch commands -->
```

---



## 4. Probing and post-processing

Eddy3D wind comfort workflows typically probe field `U` (e.g. via `system/ttt` → `postProcessing/ttt/<time>/U`). See also `Scripts/230_run_postprocess_U.bat` mentioned in the pollution doc.


| Item                   | Path / script | Notes                                           |
| ---------------------- | ------------- | ----------------------------------------------- |
| Probe point list       |               | Prefer **same points** as pollution / synthesis |
| Post-process U         |               |                                                 |
| Export for Grasshopper |               |                                                 |
| Comfort criterion      |               |                                                 |


---



## 5. Metrics for UX synthesis


| Metric                          | Definition | Thresholds | Notes                       |
| ------------------------------- | ---------- | ---------- | --------------------------- |
| Pedestrian-level wind speed     |            |            |                             |
| Wind comfort category           |            |            |                             |
| Direction-weighted / annual map |            |            | How directions are combined |


**Output handed to synthesis script:** 

---



## 6. Scripts / GH definitions in this project


| Asset | Purpose |
| ----- | ------- |
|       |         |
|       |         |


---



## 7. Checklist (new case / new design)

- [ ] Geometry watertight / CFD-ready at pedestrian scale  
- [ ] Domain and mesh acceptable for target streets  
- [ ] Wind climate / directions documented  
- [ ] Each direction folder has usable `U`  
- [ ] Probe points match other environmental layers  
- [ ] Comfort map exported and named for synthesis  
- [ ] If pollution follows: freeze wind / protect `system/` from Eddy3D overwrite  

---



## 8. Pitfalls

1. Eddy3D sync overwriting custom OpenFOAM files (see pollution workflow).
2. 

---



## 9. Suggested next improvements

- 
- Align probe grids across wind, sun, pollution, noise.  
- Document direction-weighting for annual comfort.

---

*Draft scaffold. Fill from the Eddy3D wind cases used alongside the pollution workflow.*