# LAMMPS Simulation of Oblique High-Energy Al Nanoparticle Impact onto a Frozen Al Substrate

<p align="center">
  <img src="https://img.shields.io/badge/LAMMPS-MD%20Simulation-blue?style=for-the-badge&logo=gnu&logoColor=white"/>
  <img src="https://img.shields.io/badge/Aluminum-Nanoparticles-orange?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Frozen-Substrate-silver?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Oblique-Impact%2030°-red?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/EAM-Al99.eam.alloy-purple?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/OVITO-Visualization-green?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey?style=for-the-badge"/>
</p>

<p align="center">
  A fully atomistic <b>molecular dynamics simulation of dual oblique Al nanoparticle high-velocity impact</b>
  onto a frozen FCC aluminum substrate using LAMMPS.
  Models two FCC aluminum nanoparticles (radius = 26 Å each) launched symmetrically at
  <b>30° from vertical</b> with a total speed of 25 Å/ps, capturing mid-air collision,
  fragment redirection, and high-velocity substrate impact via the
  <i>EAM/alloy potential (Al99.eam.alloy)</i>.
  Full per-atom stress tensors, von Mises stress, kinetic and potential energies are
  computed and written across all five phases: approach, collision, post-collision travel,
  substrate impact, and deposition.
</p>

<img width="1600" height="1200" alt="oblique collision" src="https://github.com/user-attachments/assets/62bbfca1-9750-42dc-bb71-1b30b668aebe" />

---

## Physics

The simulation captures the complete sequence of a dual nanoparticle oblique high-velocity impact at the atomistic scale:

- Two FCC aluminum nanoparticles (NP1 and NP2) modeled with the EAM/alloy interatomic potential (`Al99.eam.alloy`)
- NP1 launched from the left with velocity (+12.5, 0, −21.65) Å/ps; NP2 from the right at (−12.5, 0, −21.65) Å/ps
- Both nanoparticles share the same 25 Å/ps total speed at 30° oblique angle from vertical
- Mid-air collision between the two NPs generates a high-energy fragment cloud before substrate contact
- Substrate is fully frozen (`setforce 0 0 0`) — acts as a rigid target to isolate NP impact dynamics
- NPs evolve under NVE integration (no thermostat) — true ballistic, energy-conserving impact
- Per-atom stress tensor computed via `compute stress/atom NULL` at every phase
- Von Mises stress normalized by Al atomic volume (16.6 Å³ → factor 166,000) to yield GPa-scale output
- Expected stress range: substrate rest ≈ 0–1 GPa; impact crater zone ≈ 10–50 GPa

---

## Impact Regime

| Regime | Speed Range | This Simulation |
|--------|-------------|-----------------|
| Low-energy deposition (soft landing) | < 5 Å/ps | ❌ |
| Moderate impact (deformation without fragmentation) | 5–15 Å/ps | ❌ |
| High-energy impact (fragmentation + deep penetration) | 15–30 Å/ps | ✅ |
| Hypervelocity (full substrate penetration) | > 50 Å/ps | ❌ |
| This simulation total speed | 25 Å/ps | ✅ |

---

## Geometry

```
  Simulation box (units: Angstrom):

  z = 130  +------------------------------------------+  <- box top
           |                                          |
  z =  91  |  O          O    <- NP1 (left) &         |
           | /            \      NP2 (right), r = 26 Å|
           | v↓↙          v↓↘  <- oblique 30° impact  |
           |                                          |
  z =  20  +------------------------------------------+  <- substrate top
           |  ██████████████████████████████████████  |
  z =   0  +------------------------------------------+  <- substrate bottom / box floor

  Box XY:   0 to 120 Angstrom (periodic in x and y)
  Box Z:    0 to 130 Angstrom (shrink-wrap in z)
  NP1 center:  (32,  60, 65) Angstrom
  NP2 center:  (88,  60, 65) Angstrom
  Substrate:   120 × 120 × 20 Angstrom slab at z = 0–20
```

- **NP1** (left): FCC Al, center (32, 60, 65) Å, radius = 26 Å, velocity (+12.5, 0, −21.65) Å/ps
- **NP2** (right): FCC Al, center (88, 60, 65) Å, radius = 26 Å, velocity (−12.5, 0, −21.65) Å/ps
- **Substrate**: FCC Al slab, 120 × 120 × 20 Å, fully frozen
- **Boundary**: Periodic in x and y; shrink-wrap (s) in z

---

## Simulation Phases

| Phase | Run Steps | Simulated Time | Dump Frequency | Description |
|-------|-----------|----------------|----------------|-------------|
| Approach | 100 | 0.05 ps | every 20 steps | NPs descend; mid-air collision at ~step 160 |
| Collision + Ejecta | 500 | 0.25 ps | every 5 steps | High-resolution capture of NP–NP impact and fragmentation |
| Post-Collision Travel | 2,000 | 1.00 ps | every 20 steps | Fragment cloud propagates toward substrate |
| Substrate Impact | 500 | 0.25 ps | every 5 steps | High-resolution capture of crater formation |
| Deposition + Settling | 5,000 | 2.50 ps | every 50 steps | Fragments spread, cool (dynamically), and deposit |
| **Total** | **8,100** | **4.05 ps** | — | — |

---

## Key Event Timeline

| Event | Approx. Step | Phase |
|-------|-------------|-------|
| Minimization complete | pre-run | Initialize |
| NP–NP mid-air collision | ~160 | Approach/Collision |
| Maximum von Mises stress at collision | ~165 | Collision |
| Fragment cloud separates | ~350 | Collision/Ejecta |
| First fragment contacts substrate | ~2,400 | Post-collision travel |
| Crater formation peak stress | ~2,450–2,500 | Substrate impact |
| Deposition complete | ~8,100 | Settling |

---

## Material Parameters

### Nanoparticle Parameters (NP1 & NP2)

| Parameter | Symbol | Value | Unit |
|-----------|--------|-------|------|
| Crystal structure | — | FCC | — |
| Lattice constant | a | 4.05 | Angstrom |
| Atomic mass | m | 26.9815 | g/mol |
| Nanoparticle radius | R | 26 | Angstrom |
| NP1 center | — | (32, 60, 65) | Angstrom |
| NP2 center | — | (88, 60, 65) | Angstrom |
| Total impact speed | v | 25 | Å/ps |
| Impact angle from vertical | θ | 30 | degrees |
| Horizontal velocity (vx) | vx | ±12.5 | Å/ps |
| Vertical velocity (vz) | vz | −21.65 | Å/ps |
| Integration | — | NVE | — |

### Substrate Parameters

| Parameter | Symbol | Value | Unit |
|-----------|--------|-------|------|
| Crystal structure | — | FCC | — |
| Lattice constant | a | 4.05 | Angstrom |
| Dimensions (x × y × z) | — | 120 × 120 × 20 | Angstrom |
| Constraint | — | Fully frozen (`setforce 0 0 0`) | — |
| Atom type | — | 1 (same as NPs) | — |

### Interatomic Potential

| Parameter | Value |
|-----------|-------|
| Potential file | Al99.eam.alloy |
| Potential style | EAM/alloy |
| Al mapping | Type 1 → Al |
| pair_coeff | `* * Al99.eam.alloy Al` |

### Boundary Conditions

| Group | Condition | Description |
|-------|-----------|-------------|
| substrate | `setforce 0 0 0` + `velocity set 0 0 0` | Fully frozen; acts as rigid target |
| nps (NP1 + NP2) | NVE | Ballistic; no thermostat — conserves impact energy |
| X, Y | Periodic | Bulk-like lateral behavior |
| Z | Shrink-wrap (s) | Box adapts to atom positions |

---

## Group Definitions

```
substrate       <- All atoms in region z = 0–20 Å (frozen slab)
np1_atoms       <- All atoms in sphere centered at (32, 60, 65), r = 26 Å
np2_atoms       <- All atoms in sphere centered at (88, 60, 65), r = 26 Å
nps             <- Union of np1_atoms and np2_atoms (NVE group)
free            <- All non-substrate atoms (nps; available for further fixes)
```

---

## Governing Equations

### Oblique Impact Velocity Decomposition

```
Total speed: v_total = 25 Å/ps
Impact angle from vertical: θ = 30°

Horizontal component:  vx = ±v_total * sin(θ) = ±25 * sin(30°) = ±12.5 Å/ps
Vertical component:    vz = -v_total * cos(θ) = -25 * cos(30°) = -21.65 Å/ps

NP1: velocity = (+12.5,  0, -21.65) Å/ps  (converging inward)
NP2: velocity = (-12.5,  0, -21.65) Å/ps  (converging inward)
```

### NP–NP Collision Timing (Approximate)

```
Initial horizontal separation between NP centers: Δx = 88 - 32 = 56 Å
Closing speed:  Δvx = 12.5 - (-12.5) = 25 Å/ps
Time to contact: t ≈ (Δx - 2R) / Δvx = (56 - 52) / 25 = 0.16 ps → step ~320

Note: timestep = 0.0005 ps → 0.16 ps / 0.0005 = 320 steps
(collision captured during Phase 1 + Phase 2 combined)
```

### EAM/Alloy Potential Energy

```
E_i = F_alpha(sum_{j ≠ i} rho_alpha_beta(r_ij)) + 0.5 * sum_{j ≠ i} phi_alpha_beta(r_ij)

where:
  F_alpha = embedding energy function
  rho     = electron density contribution at site i
  phi     = pair interaction
```

### Per-Atom Stress Tensor (LAMMPS units: bar · Å³)

```
sigma_ab = (1/V_i) * [ -m_i * v_ia * v_ib + 0.5 * sum_j (r_iab * f_ijb) ]
```

### Von Mises Stress (in-script variable, output in GPa)

```
sigma_VM = sqrt( 0.5 * [(s11-s22)^2 + (s22-s33)^2 + (s33-s11)^2
                         + 6*(s12^2 + s23^2 + s13^2)] ) / 166000

Normalization factor 166,000 = Al atomic volume (16.6 Å³) × unit conversion
(bar·Å³ → GPa: 1 bar·Å³ = 0.1 MPa·Å³; 166000 bar·Å³ per atom → ~1 GPa scale)
```

### Hydrostatic Pressure

```
P = -(s11 + s22 + s33) / 3

Compression: P > 0  (impact crater center)
Tension:     P < 0  (crater rim / spallation zone)
```

---

## Output Files

| File | Dump Frequency | Content |
|------|---------------|---------|
| `impact_frozen.lammpstrj` | Variable by phase (5–50 steps) | id, type, x, y, z, vx, vy, vz, stress tensor (6 components), ke/atom, pe/atom, vonMises |
| `impact_frozen_final.data` | 1 frame (end of run) | Final atomic positions and velocities (LAMMPS data format) |

### Dump Column Reference

| Column | Quantity | Units |
|--------|----------|-------|
| id | Atom ID | — |
| type | Atom type | — |
| x, y, z | Position | Å |
| vx, vy, vz | Velocity | Å/ps |
| c_peratom_stress[1–6] | Stress tensor (xx, yy, zz, xy, xz, yz) | bar·Å³ |
| c_ke_atom | Per-atom kinetic energy | eV |
| c_pe_atom | Per-atom potential energy | eV |
| v_vonmises | Von Mises stress | GPa |

---

## Repository Structure

```
al_nanoparticle_impact/
|
|-- in.impact_frozen.lammps          # Main LAMMPS simulation script
|-- Al99.eam.alloy                   # EAM potential file (required)
|-- README.md                        # This file
|
|-- outputs/                         # Generated on run
    |-- impact_frozen.lammpstrj      # Full trajectory (all phases, variable dump rate)
    |-- impact_frozen_final.data     # Final atomic configuration
```

---

## How to Run

### Requirements

- LAMMPS (any recent version with MANYBODY package): https://www.lammps.org
- EAM potential file `Al99.eam.alloy` (place in same directory as script)
- OVITO for trajectory visualization: https://www.ovito.org

### Step 1 — Place potential file

```bash
ls Al99.eam.alloy   # must be in same directory as script
```

### Step 2 — Run the simulation

```bash
lmp -in in.impact_frozen.lammps
```

Or in parallel with MPI:

```bash
mpirun -np 4 lmp -in in.impact_frozen.lammps
```

### Step 3 — Visualize in OVITO

1. Open OVITO → `File > Load File` → select `impact_frozen.lammpstrj`
2. Add modifier: **Color Coding** → set property to `vonMises` to map stress
3. Set color map range: 0 GPa (blue) → 50 GPa (red) for vivid impact contrast
4. Add modifier: **Displacement Vectors** to visualize atom motion during collision
5. Step through frames — note the phase boundaries where dump frequency changes
6. Use **Slice** modifier to view cross-section of the impact crater
7. Add modifier: **Common Neighbor Analysis** to observe FCC → amorphous at crater rim

### Von Mises Expression for OVITO Compute Property (if recomputing manually)

```
sqrt(0.5*(
  (c_peratom_stress_1 - c_peratom_stress_2)^2 +
  (c_peratom_stress_2 - c_peratom_stress_3)^2 +
  (c_peratom_stress_1 - c_peratom_stress_3)^2 +
  6*(c_peratom_stress_4^2 + c_peratom_stress_5^2 + c_peratom_stress_6^2)
)) / 166000
```

Name the output property `vonMises`.

---

## What to Look for in Results

### Mid-Air NP–NP Collision (Phase 2 — steps 100–600)

Watch NP1 (left) and NP2 (right) converge symmetrically. At contact (~step 320), the two FCC nanoparticles partially merge and fragment. Von Mises stress peaks sharply at the collision interface. The symmetric inward horizontal velocities produce a forward-compressed ejecta cone directed toward the substrate.

### Fragment Cloud Propagation (Phase 3 — steps 600–2600)

The ejecta cloud spreads in a cone pattern toward the substrate. Kinetic energy redistributes among the fragment cloud. Per-atom KE (`c_ke_atom`) shows a broad distribution — some atoms decelerate, others are accelerated by the collision dynamics.

### Substrate Impact and Crater Formation (Phase 4 — steps 2600–3100)

Fragment cluster strikes the frozen substrate. Von Mises stress in the substrate directly below impact rises to 10–50 GPa. Because the substrate is frozen, deformation is not permitted — all energy is reflected back into the NP fragments, producing a realistic rigid-wall impact signature. The crater shape is asymmetric due to the oblique angle.

### Deposition and Settling (Phase 5 — steps 3100–8100)

Fragments lose kinetic energy through repeated interactions with the substrate surface and each other. Per-atom potential energy (`c_pe_atom`) rises for atoms that deposit at non-equilibrium surface sites. The final configuration (`impact_frozen_final.data`) captures the deposited film morphology.

---

## Common Errors and Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| `Cannot open potential file Al99.eam.alloy` | Potential file not found | Place `Al99.eam.alloy` in run directory |
| `-nan` in potential energy at start | Atom overlap after `create_atoms` | Re-run `minimize` with tighter tolerances; check lattice constant vs box size |
| `Lost atoms` during impact phase | Atoms ejected beyond shrink-wrap z-boundary | Add `fix walls` or enlarge box z-range to 200+ Å |
| All substrate atoms show non-zero forces | `fix setforce` applied after first `run` | Ensure `fix 1 substrate setforce 0 0 0` is set before `run 100` |
| NPs show zero velocity | Velocity command applied before group definition | Define all groups before `velocity` commands |
| `v_vonmises` all zeros in dump | Variable defined before compute — order matters | Ensure `compute peratom_stress` is defined before `variable vonmises` |
| Dump file extremely large | Too-frequent dump during settling phase | Phase 5 already uses `dump_modify every 50`; reduce further if needed |

---

## Extending the Model

| Extension | What to Change |
|-----------|----------------|
| Higher impact speed | Increase total speed from 25 to 50 Å/ps; adjust vx/vz accordingly |
| Different impact angle | Change θ; recompute `vx = v*sin(θ)`, `vz = -v*cos(θ)` |
| Deformable substrate | Remove `fix 1 substrate setforce 0 0 0`; add NVT thermostat to substrate |
| Add thermostat to NPs | Replace `fix 2 nps nve` with `fix 2 nps nvt temp 300 300 0.01` |
| Triple NP collision | Add NP3 region and velocity; adjust box geometry |
| Larger NPs (~50 Å radius) | Change sphere radius in region commands; update box z to 200 Å |
| Cu substrate | Change substrate lattice to `fcc 3.615`; use CuAl EAM potential |
| Track crater depth over time | Add `compute chunk/atom` in z-slabs on substrate atoms |
| Radial distribution function post-impact | Add `compute rdf all rdf 100 1 1` after Phase 5 |

---

## Citation

If you use this code in your research, please cite:

```bibtex
@software{mishra_2026_al_nanoparticle_impact,
  author    = {Mishra, Akshansh},
  title     = {LAMMPS Simulation of Oblique High-Energy Al Nanoparticle
               Impact onto a Frozen Al Substrate},
  year      = {2026},
  publisher = {Zenodo},
  doi       = {10.5281/zenodo.20340985},
  url       = {https://doi.org/10.5281/zenodo.20340985}
}
```

Plain text citation:

> Mishra, A. (2026). *LAMMPS Simulation of Oblique High-Energy Al Nanoparticle Impact onto a Frozen Al Substrate*. Zenodo. https://doi.org/10.5281/zenodo.20340985

---

## Author

**Akshansh Mishra**
GitHub: https://github.com/akshansh11

---

## License

<p>
<a rel="license" href="http://creativecommons.org/licenses/by/4.0/">
<img alt="Creative Commons Licence" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png"/>
</a>
<br/>
This work is licensed under a
<a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>.
</p>

You are free to:

- **Share** — copy and redistribute the material in any medium or format
- **Adapt** — remix, transform, and build upon the material for any purpose, including commercially

Under the following terms:

- **Attribution** — You must give appropriate credit to Akshansh Mishra and provide a link to this repository

Copyright 2026 Akshansh Mishra.
