# Documentation — Multisensory Digital Twins

Workflow notes for the **multisensory user experience assessment** framework used in this project (urban digital twin + environmental / pedestrian models).

Python scripts, Grasshopper scripts, and user guidance bridging the tools are developed in this project.

---

## Documents


| Document                                       | Topic                                             | Status               |
| ---------------------------------------------- | ------------------------------------------------- | -------------------- |
| [OVERALL_FRAMEWORK.md](OVERALL_FRAMEWORK.md)   | End-to-end framework (data → models → UX metrics) | Draft                |
| [MACROPED_WORKFLOW.md](MACROPED_WORKFLOW.md)   | Macroscopic pedestrian flow prediction            | Draft / placeholders |
| [WIND_WORKFLOW.md](WIND_WORKFLOW.md)           | Outdoor wind assessment (Rhino + Eddy3D)          | Draft / placeholders |
| [SUNLIGHT_WORKFLOW.md](SUNLIGHT_WORKFLOW.md)   | Sunlight / solar access (Rhino + Ladybug)         | Draft / placeholders |
| [POLLUTION_WORKFLOW.md](POLLUTION_WORKFLOW.md) | Traffic pollution CFD on an Eddy3D wind case      | Filled (OpenFOAM 8)  |




### Planned / not yet written


| Component (from framework diagram) | Suggested doc name              | Notes                      |
| ---------------------------------- | ------------------------------- | -------------------------- |
| Microscopic pedestrian model       | `MICROPED_WORKFLOW.md`          | MassMotion and/or SUMO     |
| MacroPed → SUMO conversion         | (section in micro or separate)  | Python script              |
| Pedestrian visual perception       | `VISUAL_PERCEPTION_WORKFLOW.md` | Rhino + Python             |
| Noise model                        | `NOISE_WORKFLOW.md`             | Rhino + Pachyderm          |
| Metric synthesis / overlay         | `SYNTHESIS_WORKFLOW.md`         | Python overlapping of maps |


---



## Related figure

- [framework_overall.png](framework_overall.png) — overall data / model / metrics diagram

