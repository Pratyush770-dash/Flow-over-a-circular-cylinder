# Flow Over a Circular Cylinder: Drag and Wake at Re = 40 and 100

2D water flow past a circular cylinder in ANSYS Fluent. Drag coefficient is checked against textbook values, and the wake is followed from steady (Re = 40) to vortex shedding (Re = 100).

The cylinder is the standard bluff body benchmark, and bluff body wakes matter for underwater vehicles (appendages, sonar domes, control surfaces). It makes a sensible first case before moving to shapes with no published reference data.

## Contents
- [What was done](#what-was-done)
- [Software](#software)
- [Reproducing the results](#reproducing-the-results)
- [Results](#results)
- [Method notes](#method-notes)
- [Limitations](#limitations)
- [Repository structure](#repository-structure)
- [Author](#author)

## What was done

- 2D laminar water flow past a cylinder with D = 0.1 m, at **Re = 40** (steady) and **Re = 100** (transient)
- Re set through inlet velocity only, geometry and fluid unchanged
- Cd and Cl tracked as coefficients through Report Definitions, Cd compared with textbook values
- Vortex shedding at Re = 100, with the shedding period taken from the lift history, giving St of about 0.17
- Three early mistakes sorted out: k-omega SST left switched on in a laminar flow, a residual criterion that stopped the run at 34 iterations, and a force report that gave drag in newtons instead of Cd
- A small perturbation was needed at Re = 100, since the symmetric wake took far too long to go unstable by itself

## Software

* ANSYS DesignModeler / SpaceClaim for geometry
* ANSYS Meshing, single global element size, no inflation
* ANSYS Fluent 2026 R1 Student
* Fluent report plots and contours for post-processing

## Reproducing the results

1. Workbench: add Fluid Flow (Fluent). Set the geometry analysis type to **2D** before opening it
2. Sketch a rectangle from x = -1 to 3 m and y = -1 to 1 m, plus a 0.1 m circle at the origin. Make a surface from the rectangle and subtract the circle
3. Name the edges `inlet`, `outlet`, `top`, `bottom`, `cylinder`
4. Mesh with a global element size of 0.007 m
5. Fluent: planar 2D, pressure-based, **laminar**, water-liquid (998.2 kg/m3, 0.001003 Pa.s)
6. Boundaries: velocity inlet (speeds below), pressure outlet at 0 Pa gauge, `top` and `bottom` as symmetry, `cylinder` as no-slip wall
7. Reference values: area 0.1 m2, length 0.1 m, density 998.2, velocity equal to the inlet velocity
8. Drag and Lift report definitions on `cylinder`, output type **coefficient**, drag vector (1, 0), lift vector (0, 1)
9. **Re = 40:** steady, residual criterion 1e-6. The default 1e-3 stops the run too early
10. **Re = 100:** transient. 5 s steps for 1200 steps, then a small y-velocity perturbation and 10 s steps for another 1000. Average only after the lift amplitude stops growing

## Results

| Re | Inlet velocity (m/s) | Flow type |
|---|---|---|
| 40 | 4.02e-4 | Steady |
| 100 | 1.005e-3 | Transient |

| Re | Cd (this study) | Cd (textbook, approx.) | St (this study) | St (reference) |
|---|---|---|---|---|
| 40 | about 1.6 | 1.5 to 1.6 | n/a | n/a |
| 100 | 1.3 to 1.4 | 1.3 to 1.4 | about 0.17 | about 0.16 to 0.17 |

Cd values were read off the report plots. Check them against the console output before quoting them anywhere.

At Re = 40 the wake is steady and symmetric, lift sits near zero, and Cd comes out slightly high, at the top edge of the textbook range.

At Re = 100 a staggered vortex street forms. Lift oscillates with an amplitude near 0.3 and a period of about 590 s, and Cd stays between 1.3 and 1.4.

Contours, Cd and Cl histories and residuals for both cases are in `results/`.

## Method notes

- **Domain:** 10D upstream, 30D downstream, 10D each side. Blockage 5%.
- **Mesh:** global size 0.007 m, about 14 elements across the diameter. Nothing at the wall or in the wake.
- **Solver:** pressure-based and laminar. Steady at Re = 40, transient at Re = 100.
- **Coefficients:** Cd and Cl need the reference values entered by hand. Skip that and Cd is wrong.
- **Triggering shedding:** over the first 6000 s at Re = 100, lift only grew to about 0.02. The perturbation at 6000 s started the shedding, and the spike it leaves in the histories is not physical.

## Limitations

The mesh is coarse. One global size, no mesh independence study, so there is no telling how much of the small Cd overshoot at Re = 40 comes from the mesh and how much from the 5% blockage. Probably some of each.

Only two Reynolds numbers were run, so the Cd comparison is two points and not a curve. Re = 1000 was dropped for time. A 2D laminar run there would not be reliable anyway, as the real wake is three-dimensional.

Cd and St still need to be taken from exported data and averaged over the last several shedding cycles.

## Repository structure

```
├── README.md
├── report/
│   └── Cylinder_Flow_CFD_Report.docx
├── geometry/
├── mesh/
├── results/
│   ├── contours/
│   ├── force_histories/
│   └── residuals/
└── case_files/
```

## Author

**Pratyush Dash**

B.Tech Chemical Engineering, KIIT University, Bhubaneswar
