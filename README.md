# DDNO

## 1. Project Overview

**DDNO** (Difference Density Natural Orbitals) is a modern orbital analysis tool designed for interpreting **electron-conserving electronic transitions**, including ΔSCF excited states, charge-transfer processes, and state-specific density differences. DDNO generalizes the **Natural Ionization Orbital (NIO)** model of electron detachment/attachment to processes where the total number of electrons remains fixed, providing a unified, density-based picture of electronic rearrangement.

The DDNO model:

* Constructs the **difference density**
  ΔP = P_f − P_i
  between initial and final states.
* Performs a **Löwdin natural orbital decomposition** of the difference density.
* Produces orbitals that fall naturally into:

  * **Particle (δ ≈ +1)** orbitals
  * **Hole (δ ≈ −1)** orbitals
  * **Relaxation (0 < |δ| < 1)** orbital pairs
  * **δ = 0** orbitals that do not contribute to the transition
* Decomposes the difference density into **excitation** and **relaxation** contributions:
  ΔP = X + R
* Computes **excitation number** (n_x) and **relaxation number** (n_r), which quantify the many-electron character of the transition.

This repository contains the complete DDNO implementation (`ddno.exe`). The program automatically detects whether the initial and final states have the same number of electrons:

* If the electron count **is conserved**, DDNO performs the general **DDNO analysis**.
* If the electron count **changes**, the program automatically performs an **NIO analysis** following established methods.

This unified behavior replaces earlier workflows that required separate programs (`nio.exe`) for non-conserving transitions.

---

## 2. Installation

### Requirements

* **Supported Fortran compilers**:

  * `gfortran`
  * `nvfortran`
    *(Other compilers are not supported.)*

* **MQCPack Fortran (f03) library**
  Available at: [https://github.com/MQCPack](https://github.com/MQCPack)

* BLAS/LAPACK libraries

### Build Instructions

```bash
git clone https://github.com/HratchianGroup/ddno
cd ddno
make
```

This produces the main executable:

```
ddno.exe
```

---

## 3. Usage

### Basic command-line usage

DDNO requires **two Fortran Array Files (FAF)** as input:

```bash
ddno.exe initial.faf final.faf
```

* `initial.faf` — AO-basis density matrix for the initial electronic state
* `final.faf` — AO-basis density matrix for the final electronic state

DDNO will:

1. Read the two density matrices
2. Form the difference density ΔP
3. Compute DDNOs and their occupation-change eigenvalues δ
4. Identify particle, hole, and relaxation orbitals
5. Compute excitation and relaxation numbers (n_x, n_r)
6. Print results to standard output

### Optional third argument

If a third filename is provided:

```bash
ddno.exe initial.faf final.faf ddno_output.faf
```

DDNO writes the computed orbitals and associated data to `ddno_output.faf`, which can be converted to a Gaussian formatted checkpoint file for visualization using GaussView, iQMol, or any program that reads Gaussian `.fchk` files.

Example:

```bash
unfchk ddno_output.faf ddno_output.fchk
```

You may then visualize the DDNOs using the viewer of your choice.
