# Traffic pollution CFD on an Eddy3D wind case (OpenFOAM 8)

Step-by-step workflow used in this project to add **road traffic pollution** as a passive scalar on top of an Eddy3D outdoor wind simulation, then probe results for Grasshopper / Rhino visualization.

**Stack:** Eddy3D case → blueCFD / OpenFOAM.org **8** → `simpleFoam` + `scalarTransport` function object → probes → Grasshopper point cloud.

**Case folder used here:** wind-direction subcase `230/` (same idea applies to other angle folders).

---

## 0. What you need before starting

1. A finished Eddy3D **mesh** and at least one wind-direction case (here: `230/`) with a converged or usable wind field (`U`, `p`, `k`, `ε`/`ω`, …).
2. OpenFOAM 8 via **blueCFD-Core** (this project uses `C:\Program Files\blueCFD-Core-2020\setvars_OF8.bat`).
3. Road emission regions as **axis-aligned bounding boxes** in the **same coordinate system as the mesh** (this case uses absolute RD-like metres, not a local 0-origin).
4. Optional: the Eddy3D pedestrian probe file `230/system/ttt` (same points will be reused for pollution).

**Important:** Do **not** let Eddy3D rewrite `230/system/*` while OpenFOAM is running. That previously deleted `fvOptions` / reverted `controlDict` mid-run and crashed the solver.

---

## 1. Overview of the method

| Step | Goal |
|------|------|
| A | Select road cells with `topoSet` → cellSet `roadEmissions` |
| B | Create scalar field `s` (pollutant concentration / relative concentration) |
| C | Enable `scalarTransport` in `controlDict` with an emission source on that cellSet |
| D | Add numerical settings for `s` in `fvSchemes` / `fvSolution` |
| E | Run `simpleFoam` (preferably with a **frozen or healthy** wind field) |
| F | Probe `s` at the same points as wind → convert for Grasshopper |

Pollution is a **passive scalar**: it is carried by the wind and diffused; it does not push the air. Emission rate `0.05` used here is a **placeholder** (relative map), not calibrated µg/m³.

---

## 2. Define road emission volumes (`topoSet`)

### 2.1 File

`230/system/topoSetDict`

### 2.2 Idea

OpenFOAM.org 8 does **not** support `selectionMode boxes` in `fvOptions`. Instead:

1. Build a **cellSet** from one or more boxes (`boxToCell`).
2. Point the emission source at that cellSet.

Several boxes → **same cellSet name**, first `action new`, then `action add`:

```cpp
actions
(
    {
        name    roadEmissions;
        type    cellSet;
        action  new;          // create
        source  boxToCell;
        sourceInfo
        {
            box (xmin ymin zmin) (xmax ymax zmax);
        }
    }
    {
        name    roadEmissions;
        type    cellSet;
        action  add;          // merge second road into same set
        source  boxToCell;
        sourceInfo
        {
            box (xmin ymin zmin) (xmax ymax zmax);
        }
    }
);
```

**Same name is correct** — it does *not* replace the first box; `add` appends cells.

### 2.3 Boxes used in this project

| Box | Min | Max | Rough orientation |
|-----|-----|-----|-------------------|
| 1 | `(119393.15, 483073.21, 0)` | `(119714.00, 483122.27, 1.5)` | E–W |
| 2 | `(119701.55, 483083.62, 0)` | `(119753.21, 483442.08, 1.5)` | N–S |

- Keep **z from ~0 to ~1.5 m** (road / exhaust band). A box with z = −1.5 → 0 sits under the ground and selects almost no fluid cells.
- Boxes are **axis-aligned**. Skewed roads may need a better cellSet later (e.g. from an STL).

### 2.4 Run topoSet

From the case root (Command Prompt):

```bat
Scripts\230_run_topoSet.bat
```

Or manually (after `setvars_OF8.bat`), from `230/`:

```bat
topoSet
```

**Success check:** log should show the cellSet growing, e.g. ~2510 cells after box1, ~6068 after adding box2.  
Set file: `mesh/constant/polyMesh/sets/roadEmissions` (via the `230/constant/polyMesh` symlink).

---

## 3. Create the pollutant field `s`

### 3.1 Files

- `230/0/s` — initial / boundary conditions  
- Also copy into any time folder you will start from (e.g. `230/10/s` or `230/120/s`) so the solver can read `s` at `startFrom latestTime`.

### 3.2 Recommended BCs (same idea as `s_reference` / `aoa`)

- Dimensions: `[0 0 0 0 0 0 0]` (relative concentration)
- `internalField uniform 0;`
- Inlet: `fixedValue uniform 0;`
- Outlet / ground / buildings / frontAndBack: `zeroGradient;`

Emission is **not** applied on patches; it is applied as a volumetric source on `roadEmissions` (next section).

---

## 4. Wire scalar transport + emission (`controlDict`)

### 4.1 File

`230/system/controlDict`

Eddy3D may restore a stock `controlDict`. If pollution settings disappear, put them back before running.

### 4.2 Recommended run controls

```cpp
application         simpleFoam;
startFrom           latestTime;   // continue from last written time
stopAt              endTime;
endTime             200;          // example
deltaT              1;
writeInterval       20;
runTimeModifiable   false;        // strongly recommended (see pitfalls)
```

### 4.3 `scalarTransport` function object (OpenFOAM 8)

Add under `functions { ... }`. Emission must be **nested** inside this block (not only in a standalone `system/fvOptions` for the main solver):

```cpp
scalarTransport
{
    type            scalarTransport;
    libs            ("libsolverFunctionObjects.so");
    enabled         true;
    writeControl    timeStep;
    writeInterval   20;
    field           s;

    // Turbulent diffusivity: D = alphaD*nu + alphaDt*nut
    alphaD          1;
    alphaDt         1;
    nCorr           1;

    fvOptions
    {
        roadEmissions
        {
            type            semiImplicitSource;   // OF.org 8 name
            active          true;

            selectionMode   cellSet;
            cellSet         roadEmissions;

            volumeMode      specific;   // Su per m^3 per s

            sources
            {
                s
                {
                    explicit    0.05;   // PLACEHOLDER — calibrate later
                    implicit    0;
                }
            }
        }
    }
}
```

Notes for OF.org 8 (blueCFD):

- Use `semiImplicitSource` and `sources { s { explicit ...; implicit ...; } }`.
- Do **not** use ESI-style `injectionRateSuSp` or `selectionMode boxes`.
- One source on the combined cellSet ⇒ **same emission rate on every selected cell** (both roads). Total mass injected still scales with **cell volume** (`volumeMode specific`).

Keep a minimal `230/system/fvOptions` file present (even if empty aside from the FoamFile header) so file watches do not crash if something probes that path. Real pollution sources live in `controlDict` as above.

---

## 5. Numerics for `s`

### 5.1 `230/system/fvSchemes`

In `divSchemes`:

```cpp
div(phi,s)    bounded Gauss upwind;
```

(Laplacian can rely on `default` if already set.)

### 5.2 `230/system/fvSolution`

Add a solver for `s` (same style as `aoa`), and under-relaxation, e.g.:

```cpp
s
{
    solver          PBiCGStab;
    preconditioner  DILU;
    tolerance       1e-12;
    relTol          0.1;
    minIter         1;
    maxIter         50;
}
```

```cpp
relaxationFactors
{
    fields    { s 0.7; /* ... */ }
    equations { s 0.7; /* ... */ }
}
```

---

## 6. Run the simulation

### 6.1 Preferred strategy (avoid turbulence blow-up)

Coupling wind + scalar for many extra iterations can **destabilize** `k`/`ε` (seen in this project: huge `bounding k` / `bounding epsilon`, `nut` explosion). That corrupts both wind and pollution.

**Better workflow:**

1. Keep a **good** wind solution (e.g. early time with healthy residuals, before blow-up).
2. Freeze or reuse that `U`/`phi`/`nut` (or only continue a short way).
3. Let `scalarTransport` develop `s` without wrecking turbulence.

This case’s first pollution run continued flow+scalar to time 200; late fields are **less trustworthy** for quantitative maps.

### 6.2 Start the run

From case root (cmd):

```bat
Scripts\230_run_sim.bat
```

Git Bash:

```bash
cmd //c Scripts/230_run_sim.bat
```

The script should call blueCFD `setvars_OF8.bat`, `cd` into `230`, and run `simpleFoam`.

### 6.3 Healthy log signs

- `Solving for s` each step under `scalarTransport write:`
- No fatal error about missing `fvOptions` / `s`
- Avoid long stretches of extreme `bounding k` / `bounding epsilon` with max ~1e8–1e12

---

## 7. Probe pollution for Grasshopper (same grid as wind)

Eddy3D wind comfort typically probes field `U` via `230/system/ttt` → `postProcessing/ttt/<time>/U`.

Pollution uses the **same probe coordinates**, field **`s`**.

### 7.1 Probe dictionary

Example: `230/system/ttt_s` (copy of `ttt` with):

```cpp
ttt_s
{
    type            probes;
    libs            ("libsampling.so");
    writeControl    writeTime;
    interpolationScheme cellPointFace;
    fields           (U s);   // or (s) only
    probeLocations   ( /* same points as ttt */ );
}
```

### 7.2 Run probes

```bat
Scripts\230_run_postprocess_s.bat
```

Output example:

`230/postProcessing/ttt_s/200/s`

- Header lines `# Probe i (x y z)` are **locations** (comments).
- The last data line is: `time  s0 s1 s2 ...` (scalars, not vectors).

Warnings `Did not find location ... Skipping location` are normal for probes outside the mesh.

### 7.3 Convert for Grasshopper

```bat
python Scripts/probe_s_to_gh.py
```

Produces (example):

`230/postProcessing/ttt_s/200/s_gh.txt` — one number per line (invalid / outside-mesh probes → `0`).

Optional helpers (if generated):

- `points_gh.txt` — `x y z` per line (same order)
- `s_as_vectors_gh.txt` — fake `(s, 0, 0)` only if a GH component insists on vectors

### 7.4 Grasshopper / Rhino cloud

| Wind map | Pollution map |
|----------|----------------|
| Points + **vectors** `U` | Points + **scalars** `s` |
| Color by speed / comfort | Color by concentration |

Use the **same point list order**. Do not expect the stock Eddy3D “probe wind” component to read `s` automatically — feed `s_gh.txt` (or Python parse of `s`) into your cloud colour.

**Colour scale tip:** one road can be much stronger than another. If the legend max is set by the hot road, the weaker road can look “empty” even when mean `s` is clearly > 0. Inspect each road’s points separately or tighten the legend.

---

## 8. Interpreting “one road visible, one not”

In this project, probes over both emission boxes had **non-zero** `s`, but road 2 was much stronger (higher mean/max). Causes can include:

1. **Wind direction** — plume leaves one road corridor and concentrates along another.
2. **Axis-aligned box vs real street** — fewer fluid cells on one road.
3. **`volumeMode specific`** — wider/taller selected volume injects more total mass at the same rate per m³.
4. **Turbulence blow-up** — late-time mixing/advection can exaggerate asymmetry; prefer a frozen good wind for fairer maps.
5. **Display scale** — strong hotspot dominates the colour bar.

Shared cellSet name (`roadEmissions` with `new` + `add`) is **not** a bug.

---

## 9. Scripts in this repo

| Script | Purpose |
|--------|---------|
| `Scripts/230_run_topoSet.bat` | Build `roadEmissions` cellSet |
| `Scripts/230_run_sim.bat` | Run `simpleFoam` in `230/` |
| `Scripts/230_run_postprocess_s.bat` | Probe `ttt_s` (or `ttt_s2`) at latest time |
| `Scripts/probe_s_to_gh.py` | Convert probe `s` → `s_gh.txt` for GH |
| `Scripts/230_run_postprocess_U.bat` | Existing Eddy3D-style wind probe (`ttt`) |

Always run `.bat` files from **cmd**, or `cmd //c Scripts\...` from Git Bash (`\` escapes in bash).

---

## 10. Checklist (copy for a new angle / new case)

- [ ] Mesh coords match emission box coords  
- [ ] `topoSetDict` boxes have sensible z (e.g. 0–1.5 m)  
- [ ] `topoSet` → non-empty `roadEmissions`  
- [ ] `0/s` (+ start-time folder) with correct patch names  
- [ ] `controlDict`: `scalarTransport` + nested `semiImplicitSource` on `cellSet`  
- [ ] `fvSchemes` / `fvSolution` entries for `s`  
- [ ] `runTimeModifiable false`; Eddy3D not overwriting `system/` during the run  
- [ ] Prefer healthy / frozen wind before long scalar iteration  
- [ ] Log shows `Solving for s`  
- [ ] `postProcess -func ttt_s` → `s_gh.txt` → GH cloud  

---

## 11. Pitfalls seen in this project

1. **Eddy3D / file sync** restored stock `controlDict` and removed `fvOptions` while the solver ran → fatal error. Keep `runTimeModifiable false` and avoid touching the case in Eddy3D during a run.  
2. **Wrong OF dialect** (`boxes`, `injectionRateSuSp`) fails on OpenFOAM.org 8.  
3. **Emission under ground** (negative z max) → empty or useless cellSet for that box.  
4. **Coupled long run** → `k`/`ε` explosion → unreliable `U` and `s`.  
5. **ParaView** shows only buildings if you do not load internal mesh + field `s` + correct time — GH probe cloud is often enough for maps.  
6. Placeholder **`explicit 0.05`** is for pattern studies only until calibrated to traffic emission data.

---

## 12. Suggested next improvements

- Freeze wind from a good time directory; solve only scalar transport for pollution maps.  
- Calibrate `explicit` from traffic flow × emission factors ÷ source volume.  
- Replace AABB roads with road-surface / corridor cellSets if boxes miss the street.  
- Multi-direction: repeat per angle folder (`0/`, `45/`, …) like Eddy3D wind, then combine in GH if needed.

---

*Document based on the workflow developed for*  
`3_SimpleWindAnalysis_new_meshManualRebuild_3` *(wind folder `230`)*.
