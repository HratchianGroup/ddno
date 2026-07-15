# DDNO

The **DDNO program** computes Difference Density Natural Orbitals and related quantities from pairs of electronic-state density matrices. It supersedes the earlier **NIO** repository ([https://github.com/HratchianGroup/nio](https://github.com/HratchianGroup/niorep)) and incorporates its functionality under the same MIT License.

DDNO provides a unified framework for analyzing electron-conserving and non-conserving transitions, automatically performing DDNO, NIO, Attachment/Detachment, and Excitation-Number analyses as appropriate. The program works directly with Fortran Array Files (FAFs) and supports orbital visualization through standard quantum-chemistry tools.

---

## 1. Installation

### Requirements

* **Supported Fortran compilers (Other compilers are not supported.):**

  * `gfortran`
  * `nvfortran`

* **MQCPack Fortran (f03) library**
  Available at: [https://github.com/MQCPack](https://github.com/MQCPack)
  The installed MQCPack compiler flavor must match the compiler used to build DDNO.

* **BLAS/LAPACK** libraries

### Build Instructions

```bash
git clone https://github.com/HratchianGroup/ddno
cd ddno
export mqcinstall=/path/to/mqcPack
make
```

This produces the main executable:

```
ddno.exe
```

The makefile defaults to `nvfortran`; switch `FC` to `gfortran` near the top of
the makefile if that is your local compiler. DDNO currently calls the MQCPack
version check with its `newerThan*` components set to 26.7.1 at runtime.

---

## 2. Usage

### Basic command-line usage

DDNO requires **two Fortran Array Files (FAF)** as input:

```bash
ddno.exe initial.faf final.faf
```

* `initial.faf` — AO‑basis density matrix for the initial electronic state
* `final.faf` — AO‑basis density matrix for the final electronic state

The two FAFs must describe the same molecule in the same AO basis. DDNO checks
that the atom count, basis dimensions, basis-use dimensions, and coordinates
match before proceeding.

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
unfchk -faf ddno_output.faf ddno_output.chk
formchk ddno_output.chk ddno_output.fchk
```

You may then visualize the DDNOs using GaussView, iQMol, or another standard visualizer of your choice.

The output FAF contains reordered DDNO orbitals, orbital-energy-like
occupation-change eigenvalues, the relevant copied basis/density data needed
for visualization, an eight-field integer DDNO metadata block, and separate
spin-resolved particle/hole coefficient arrays when those blocks are nonempty.

### Example inputs and regression cases

The `GTests/` directory contains small Gaussian input examples that were used to
validate the program and exercise the DDNO/NIO workflows. A few representative
cases are:

* `GTests/test001a.gjf` and `GTests/test001b.gjf`
* `GTests/test002.gjf`, `GTests/test002a.gjf`, and `GTests/test002b.gjf`
* `GTests/test101a.gjf`, `GTests/test101b.gjf`, and `GTests/test101b-corbs.gjf`

These files are a good starting point if you want to generate FAF inputs with
Gaussian before running `ddno.exe`.

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

These density-based analyses are evaluated for every calculation that is
successfully classified as DDNO or NIO detachment. Transition-dipole and
oscillator-strength quantities are the later, DDNO-only branch.

The program classifies the calculation from the numbers of near-`+1` and
near-`-1` occupation-change eigenvalues:

* Equal particle and hole counts select the general **DDNO analysis**.
* Fewer particles than holes select **NIO detachment analysis**.

Electron-attachment cases with more particles than holes are not currently
classified as NIO and terminate at the ambiguity check.

This unified behavior replaces earlier workflows that required separate programs (`nio.exe`) for non-conserving transitions.

For electron-conserving jobs, DDNO also reports the transition dipole, dipole
strength, and oscillator-strength-related quantities. Those terms are not
computed for NIO-style detachment jobs.

---

## 4. References

If you use DDNO or NIO analyses in published work, please cite the appropriate foundational references:

* **DDNO Model** (Difference Density Natural Orbitals):
  *A. J. Bovill, A. Abou Taka, H. Harb, and H. P. Hratchian.*
  *Excitation/Relaxation Analysis of Electronic Transitions Using Difference Density Natural Orbitals.*
  ChemRxiv preprint available at [https://doi.org/10.26434/chemrxiv-2025-d34wd-v2](https://doi.org/10.26434/chemrxiv-2025-d34wd-v2).

* **Natural Ionization Orbitals (NIOs):**

  * *L. M. Thompson, H. Harb, and H. P. Hratchian.*
    *Natural Ionization Orbitals for interpreting electron detachment processes.*
    J. Chem. Phys. **144**, 204117 (2016).

  * *H. Harb and H. P. Hratchian.*
    *ΔSCF Dyson Orbitals and Pole Strengths From Natural Ionization Orbitals.*
    J. Chem. Phys. **154**, 084104 (2021).

* **Attachment/Detachment (A/D) Model:**

  * *M. Head‑Gordon, A. M. Graña, D. Maurice, and C. A. White.*
    *Analysis of electronic transitions as the difference of electron attachment and detachment densities.*
    J. Phys. Chem. **99**, 14261–14270 (1995).

* **Excitation Number Model:**

  * *G. M. Barca, A. T. B. Gilbert, and P. M. W. Gill.*
    *Excitation number: Characterizing multiply excited states.*
    J. Chem. Theory Comput. **14**, 9–13 (2018).

---

## 5. License

This project is distributed under the **MIT License**.
The full license text is available at:

[https://github.com/HratchianGroup/ddno/blob/main/LICENSE](https://github.com/HratchianGroup/ddno/blob/main/LICENSE)
