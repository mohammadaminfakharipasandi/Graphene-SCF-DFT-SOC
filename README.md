# SCF-DFT Calculation of a Graphene √3×√3 R30° Supercell with Spin–Orbit Coupling

A self-consistent-field (SCF) density-functional theory calculation of a 6-atom √3×√3 R30° graphene supercell, performed with **Quantum ESPRESSO** and including a fully relativistic (non-collinear, spin–orbit-coupled) treatment.

## Overview

This repository contains the input and full output log for a plane-wave pseudopotential DFT calculation of monolayer graphene, using a fully relativistic ultrasoft pseudopotential together with `noncolin`/`lspinorb` to include spin–orbit coupling (SOC) explicitly, rather than relying on the usual scalar-relativistic approximation.

## Computational details

| Parameter | Value |
|---|---|
| Code | Quantum ESPRESSO v7.5 (`pw.x`) |
| Exchange–correlation | LDA (Perdew–Zunger) |
| Pseudopotential | `C.rel-pz-n-rrkjus_psl.0.1.UPF` — fully relativistic, ultrasoft, PSlibrary v1.0.0 |
| Relativistic treatment | Non-collinear + spin–orbit coupling (`noncolin=.true.`, `lspinorb=.true.`) |
| Plane-wave cutoff | 65 Ry (wavefunctions) / 260 Ry (charge density) |
| k-point mesh | 8×8×8 Monkhorst–Pack, shifted (1 1 1) → 208 points in the irreducible set |
| Smearing | Gaussian, width 0.02 Ry |
| SCF convergence threshold | 1×10⁻⁹ Ry |

## Structure

A √3×√3 R30° supercell of graphene: 6 carbon atoms in a single planar (z = 0) layer.

- In-plane lattice parameters: a = b = 4.26 Å, γ = 120° (`ibrav = 14`, `cosAB = -0.5`)
- Out-of-plane cell length: c = 8.52 Å, used as vacuum spacing to isolate the 2D layer
- C–C bond length: 1.42 Å

## Results

- SCF converged in **21 iterations** (final estimated accuracy 5.2×10⁻¹⁰ Ry, below the 1×10⁻⁹ Ry threshold)
- Total energy: **−71.79149839 Ry**
- Fermi energy: **−0.0660 eV**
- Total force: 0.000596 Ry/bohr (atoms effectively at their equilibrium positions)
- Pressure: −19.11 kbar

## Notes and limitations

- This is a single-point SCF run, not a full geometry/cell relaxation (`vc-relax`); the residual pressure above indicates the cell is close to, but not exactly at, its equilibrium volume.
- The vacuum spacing (8.52 Å) is on the low side for completely decoupling periodic images of an isolated 2D layer. For properties sensitive to inter-layer coupling, a larger vacuum (≥ 15 Å) is recommended.
- Only 4 symmetry operations (point group C₂ᵥ) were detected for this cell, well below the symmetry a √3×√3 R30° graphene supercell should ideally have. This is most likely due to small rounding in the manually specified Cartesian atomic coordinates relative to the exact lattice vectors, and it noticeably increases the computational cost, since far fewer k-points can be reduced by symmetry. Regenerating the atomic positions directly from the lattice vectors (or applying `nosym`-free symmetrization) should recover the full hexagonal symmetry and speed up future runs.
- Carbon's intrinsic spin–orbit coupling is very weak, so SOC-induced splittings in pristine graphene are expected to be small; this calculation primarily serves as a template for a fully relativistic QE workflow.

## Reproducing this calculation

```bash
mpirun -np 4 pw.x -npool 4 -in sample01.pw.in | tee sample.pw.out
```

The pseudopotential `C.rel-pz-n-rrkjus_psl.0.1.UPF` is not included in this repository; it can be downloaded from [PSlibrary](https://pseudopotentials.quantum-espresso.org/legacy_tables) and placed in `./pseudo/`.

## Files

- `sample01.pw.in` — Quantum ESPRESSO input file
- `sample.pw.out` — full Quantum ESPRESSO output log
- `README.md` — this file
- `LICENSE` — license for the input file, output data, and documentation
- `.gitignore` — excludes regenerable run artifacts

## How to cite
Mohammadamin Fakharipasandi, "SCF-DFT Calculation of a Graphene √3×√3 R30° 
Supercell with Spin–Orbit Coupling", GitHub repository, 2026.

## References

- P. Giannozzi et al., *J. Phys.: Condens. Matter* **21**, 395502 (2009)
- P. Giannozzi et al., *J. Phys.: Condens. Matter* **29**, 465901 (2017)
- P. Giannozzi et al., *J. Chem. Phys.* **152**, 154105 (2020)

## License

The input file, output data, and documentation in this repository are licensed under [CC BY 4.0](LICENSE). Quantum ESPRESSO, the software used to produce these results, is separate open-source software licensed under the GNU GPL — see [quantum-espresso.org](https://www.quantum-espresso.org) for its own terms.
