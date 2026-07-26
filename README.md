GROMACS 2026 Protein-Ligand MD Pipeline (Google Colab)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/nishattt90/gromacs-2026-protein-ligand-colab/blob/main/)

A modernized, cloud-native implementation of Justin Lemkul's classic GROMACS Protein-Ligand tutorial, updated specifically for GROMACS 2026 running inside Google Colab.

Why This Project?

Justin Lemkul's GROMACS tutorial is the gold standard for learning protein-ligand MD simulations. However, running it on modern cloud platforms like Google Colab often fails due to:
Python 3.11+ Compatibility: Legacy `cgenff_charmm2gmx` scripts fail due to deprecated `networkx` methods.
Obsolete GROMACS Syntax: Modern GROMACS 2026 rejects older `.mdp` directives (e.g., `ns_type`).

This pipeline patches these compatibility issues out-of-the-box for a seamless execution environment.
 How to Run
Click the Open in Colab badge at the top of this page.
Upload your prepared files (`.pdb` protein, `.pdb` ligand, and CGenFF `.str` stream file).
Run the cells sequentially through Energy Minimization, Equilibration, and Production MD.
Pipeline Summary
| Step | What Happens | Output |
| :--- | :--- | :--- |
| **1. Target Prep** | Clean protein and parametrize ligand with CGenFF | `nsp2_processed.gro`, `ber.itp`, `ber.prm` |
| **2. Topology Merge** | Combine coordinates and include parameter files | `complex.gro`, `topol.top` |
| **3. Solvation & Ions** | Set up dodecahedron box, add TIP3P water & neutral Na+/Cl- | `solv_ions.gro` |
| **4. Relaxation** | Energy Minimization followed by NVT and NPT equilibration | `nvt.gro`, `npt.gro` |
| **5. Production MD** | Run main simulation trajectory and analyze system stability | `md_noPBC.xtc`, `rmsd.xvg` |
Step-by-Step Overview
Step 1: Input File Preparation
- Protein (`.pdb`): Cleaned structure without crystal waters or alternate conformers.
- Ligand (`.str` & `.pdb`): Matching coordinate and topology files generated via [CGenFF Server](https://cgenff.umaryland.edu/).

 Step 2: Protein Topology Generation
- Convert protein structure to GROMACS format using CHARMM36 force field parameters (`gmx pdb2gmx`).

Step 3: CGenFF Topology Conversion (Python 3 Patch)
- Automatic patching of `cgenff_charmm2gmx_py3.py` to bypass legacy NetworkX version checks in Python 3.11+.

Step 4: Complex Topology Integration
- Merge `nsp2_processed.gro` and `ber.gro` into `complex.gro`.
- Update `topol.top` to include `#include "ber.prm"`, `#include "ber.itp"`, and add the ligand entry under `[ molecules ]`.

Step 5: Solvation & Charge Neutralization
- Construct dodecahedron box (`1.0 nm` edge clearance).
- Solvate with TIP3P water and neutralize system net charge with Na+/Cl- ions using `gmx genion`.

Step 6: Two-Phase Equilibration
- Energy Minimization Emin>1000 kJ/mol/nm
- 100 ps NVT (Constant Temperature, 300 K)
- 100 ps NPT (Constant Pressure, 1.0 bar) with position restraints on heavy atoms.

Step 7: Production Simulation & Analysis
- Execute production MD trajectory.
- Correct Periodic Boundary Conditions (PBC) with `gmx trjconv` and calculate RMSD/RMSF metrics.

License
Distributed under the MIT License.

