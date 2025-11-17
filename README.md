# DDNO

The **DDNO program** computes Difference Density Natural Orbitals and related quantities from pairs of electronic-state density matrices. It provides a unified framework for analyzing electron‑conserving and non‑conserving transitions, automatically performing DDNO, NIO, Attachment/Detachment, and Excitation‑Number analyses as appropriate. The program works directly with Fortran Array Files (FAFs) and supports orbital visualization through standard quantum‑chemistry tools.

---

## 1. Installation

### Requirements

* **Supported Fortran compilers**:

  * `gfortran`
  * `nvfortran`
    *(Other compilers are not supported.)*

* **MQCPack Fortran (f03) library**
  Available at: [https://github.com/MQCPack](https://github.com/MQCPack)

* **BLAS/LAPACK** libraries

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

## 2. Usage

### Basic command-line usage

DDNO requires **two Fortran Array Files (FAF)** as input:

```bash
ddno.exe initial.faf final.faf
```

* `initial.faf` — AO‑basis density matrix for the initial electronic state
* `final.faf` — AO‑basis density matrix for the final electronic state

DDNO will:

1. Read the two density matrices
2. Form the difference density ΔP
3. Compute DDNOs and their occupation‑change eigenvalues δ
4. Identify particle, hole, and relaxation orbitals
5. Compute excitation and relaxation numbers (nₓ, nᵣ)
6. Print results to standard output

### Optional third argument

If a third filename is provided:

```bash
ddno.exe initial.faf final.faf ddno_output.faf
```

DDNO writes computed orbitals and related data to `ddno_output.faf`, which can be converted to a Gaussian formatted checkpoint file for visualization (e.g., GaussView, iQMol):

```bash
unfchk ddno_output.faf ddno_output.fchk
```

You may then visualize the DDNOs using the viewer of your choice.

---

## 3. Overview

**DDNO** (Difference Density Natural Orbitals) is an orbital‑based analysis tool designed for interpreting electron‑conserving electronic transitions, including ΔSCF excited states, charge‑transfer processes, and state‑specific density differences. DDNO generalizes the Natural Ionization Orbital (NIO) model of electron detachment/attachment to processes where the total number of electrons remains fixed, providing a unified, density‑based picture of electronic rearrangement.

The DDNO model:

* Constructs the difference density
  ΔP = P_f − P_i
  between initial and final states.
* Performs a Löwdin natural orbital decomposition of the difference density.
* Produces orbitals that fall naturally into:

  * Particle (δ ≈ +1) orbitals
  * Hole (δ ≈ −1) orbitals
  * Relaxation (0 < |δ| < 1) orbital pairs
  * δ = 0 orbitals that do not contribute to the transition
* Decomposes the difference density into excitation and relaxation contributions:
  ΔP = X + R
* Computes excitation number (nₓ) and relaxation number (nᵣ), quantifying the many‑electron character of the transition.

In addition to DDNO and NIO analyses, the program also evaluates:

* **Attachment/Detachment model** (Head‑Gordon and co‑workers), which partitions ΔP into attachment and detachment densities and reports the **promotion number**.
* **Excitation Number model** (Gill and co‑workers), providing an alternative scalar measure of electron promotion.

These analyses are automatically computed when the required conditions (electron‑conserving transitions for A/D and excitation‑number models) are satisfied.

The program determines whether the initial and final states contain the same number of electrons:

* If the electron count **is conserved**, DDNO performs the general **DDNO analysis**.
* If the electron count **changes**, DDNO performs **NIO analysis**.

This unified behavior replaces earlier workflows that required separate programs (`nio.exe`) for non‑conserving transitions.

