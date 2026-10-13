# GW100: converged PBE/def2-QZVP restart wavefunctions for CP2K

Converged Kohn–Sham wavefunctions (`ALL_ELEC-RESTART.wfn`) for all 100 molecules of the GW100 benchmark set, computed with CP2K at the PBE level in the all-electron def2-QZVP basis (GAPW). They can be used as `SCF_GUESS RESTART` starting points for G0W0@PBE runs, so that each GW calculation starts from the same converged ground state without repeating the SCF.

## Contents

```
NN_name/
    ALL_ELEC-RESTART.wfn   converged PBE wavefunction (CP2K binary restart file)
    NN_name.xyz            geometry (GW100 set)
    NN_name.inp            CP2K input that produced the wavefunction
data/
    BASIS_def2_QZVP_RI_ALL basis sets (def2-QZVP orbital basis, RI-5Z auxiliary basis)
    POTENTIAL              all-electron potentials
MANIFEST.csv               per molecule: number of atoms, SCF steps, total energy,
                           PBE HOMO/LUMO, file size and SHA-256 of the .wfn
```

## Settings

| | |
|---|---|
| Functional | PBE |
| Orbital basis | def2-QZVP, all electrons (`POTENTIAL ALL`) |
| Auxiliary basis (RI) | RI-5Z |
| Method | GAPW, `EPS_DEFAULT 1.0E-14`, `EPS_PGF_ORB 1.0E-14` |
| Grid | `CUTOFF 800`, `REL_CUTOFF 80` |
| Cell | 27 × 27 × 27 Å, non-periodic, wavelet Poisson solver, coordinates centred |
| SCF convergence | `EPS_SCF 1.0E-7` |
| Project name | `ALL_ELEC` |

The exact input of every molecule is in its folder.

## Usage

A restart file is valid only for the same orbital basis and the same atom order. Use the `.xyz` and the basis file from this repository. The auxiliary (RI) basis does not enter the wavefunction, so any RI basis can be used with it.

Either copy the file next to an input with `PROJECT ALL_ELEC` and set

```
&SCF
  SCF_GUESS RESTART
&END SCF
```

or point to it explicitly:

```
&DFT
  WFN_RESTART_FILE_NAME /path/to/NN_name/ALL_ELEC-RESTART.wfn
  &SCF
    SCF_GUESS RESTART
  &END SCF
&END DFT
```

With the same settings, the SCF should converge within a few steps to the total energy listed in `MANIFEST.csv`.

## Note on the inputs

The `&PROPERTIES/&BANDSTRUCTURE/&GW` section of the inputs contains two keywords, `NPARAM_PADE` and `QP_START_DFT`, that come from a locally patched CP2K build and are not part of stock CP2K. Stock CP2K stops at them with "unknown keyword". They affect only the analytic continuation of the GW self-energy, not the SCF or the wavefunctions.

## Reference

M. J. van Setten et al., "GW100: Benchmarking G0W0 for Molecular Systems", J. Chem. Theory Comput. 11, 5665 (2015).
