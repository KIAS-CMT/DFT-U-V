# Self-Consistent DFT+U+V Using the Extended ACBN0 Functional

This repository provides a DFT+U+V patch for [Quantum Espresso](https://www.quantum-espresso.org/) version 7.3, enabling `pw.x` to calculate on-site *U* and inter-site *V* parameters self-consistently using the extended ACBN0 (Agapito-Curtarolo-Buongiorno Nardelli) functional.

The original idea for the self-consistent on-site U parameters was adopted from the following paper:
- Luis A. Agapito, Stefano Curtarolo, and Marco Buongiorno Nardelli, *Reformulation of DFT+𝑈 as a Pseudohybrid Hubbard Density Functional for Accelerated Materials Discovery*, Phys. Rev. X **5**, 011006 (2015).

When you use this patch file to perform first-principles calculations with self-consistent extended Hubbard interactions and then, obtain results, cite following two papers:

[1] S.-H. Lee and Y.-W. Son, *First-principles approach with a pseudohybrid density functional for extended Hubbard interactions*, Phys. Rev. Research **2**, 0434210 (2020)

[2] W. Yang, S.-H. Jhi, S.-H. Lee, and Y.-W. Son, *Ab initio study of lattice dynamics of group IV semiconductors using pseudohbyrid functionals for extended Hubbard interactions*, Phys. Rev. B **104**, 104313 (2021).

If you use a subroutine for spin-orbit interaction with extended Hubbard interactions, cite the following one paper together with two papers above. 

[3] W. Yang and Y.-W. Son, *Effects of self-consistent extended Hubbard interactions and spin-orbit couplings on energy bands of semiconductors and topological insulators*, Phys. Rev. B **110**, 155133 (2024).

## Quantum ESPRESSO Patch  

Below is a step-by-step guide on how to use the modified code

### Installation

1. Download Quantum Espresso v7.3 (not v7.3.1) from [gitlab page](https://gitlab.com/QEF/q-e/-/releases/qe-7.3).

2. Move "**qe-7.3_ehub_uv.diff**" into q-e-qe-7.3 folder.

3. Run “**patch -p1 < qe-7.3_ehub_uv.diff**”.

4. Install QE (**make pw pp**).

### Changes in `pw.x`

A spin polarized calculation (nspin=2) is **only** available for collinear calculations. 

For noncollinear calculations with SOC, ”noncolin = .true. and lspinorb = .true.”

We have introduced the following inputs in `pw.x`:
- `lda_plus_v = .true.` (default: .false.)

  Set 'lda_plus_v = .true.' to perform DFT+*U*+*V* calculations with the extended ACBN0 functional.

- `acbn0_type = 2` (default: 1)

  '1': original ACBN0 (for *U* only), '2': extended ACBN0 (for *U* and *V*)
  (We actively develop in the case of 'acbn0_type = 2' only. If you want to perform DFT+*U* calculation, set ‘ehub_nn_distance = 0.0’ and ‘acbn0_type = 2’)

- `ehub_nn_distance = 2.4` (default: 3.0 Å)

  Specify a cut-off inter-atomic disance d<sub>IJ</sub> defined in our paper [1][Phys. Rev. Research **2**, 0434210 (2020)]

- `ehub_conv_thr = 1.0D-8` (default: 1.0D-8 Ry)

  Convergence threshold on Hubbard energy *U* & *V* (a.u) for ACBN0 calculation.

- `lacbn0 = .true.` (default: .false.)

  Set 'lacbn0 = .true.' to calculate eACBN0 energy for self-consistent Hubbard parameters.

- `ehub_mixing = 0.8` (default: 0.7)

  We use a simple linear mixing. [U<sub>new</sub> = (1 – ehub_mixing)*ΔU + U<sub>old</sub>]

- `ehub_l_choice(a,b) = 1` (default: 0)

  a: a type of atom in atomic species card (INTEGER)
  
  b: orbital index (INTEGER, 1: s orbital, 2: p orbital, 3: d orbital, f-orbital is not available yet.)
  
  DFT+U+V calculation for the b orbital of and atom with type a.

- `remove_ehub_u(a,b) = 0` (default: 1)

   *U* for the b orbital of an atom with type a is forced to 0.

Following inputs for fixing Hubbard parameters in `pw.x`:

Once the Hubbard parameter is determined, `lacbn0` is not required.

- `read_ehub_uv_file = .true.` (default: .false.)

  Read the existing ‘ehub_uv.txt’ in '$outdir/$prefix.save/'.

- `read_ehub_ns_file = .false.` (default: .false.)

  Read the existing ‘occupation_uv.txt’ in '$outdir/$prefix.save/'.

## Examples

Four examples are included in this repository.

1. scf calculation of DFT+*U*+*V* using eACBN0 for Ni

   - The neighbors considered in the calculation are printed out in the output under the table title "List of the nearest neighbors for inter-site interactions V".

     - The notation "atom 1: **A**, atom 2: **B**, *l m n*" indicates that atom **A** and atom **B** are neighbor, with **A** in home unit cell and **B** in the cell at **r** = *l* * **a**<sub>1</sub> +*m* * **a**<sub>2</sub> + *n* * **a**<sub>3</sub> (where **a**<sub>1</sub>, **a**<sub>2</sub>, and **a**<sub>3</sub> are the lattice vectors).

     - With `lacbn0 = .true.`, calculated *U* and *V* values are updated and printed out at each SCF step.

   - [ A, B, C, D ][*l*, *m*, *n*]

     - Atom **A** (in home unit cell)
     - Atom **B** (in the cell at **r** =[*l*, *m*, *n*])
  
     - **C**: Total number of corrected orbitals of atom **A** (e.g: if you choose s and p orbitals, **C** becomes 4)
     - **D**: Total number of corrected orbitals of atom **B**

     - **A** = **B** and *l*= *m*= *n*= 0 indicate on-site *U* matrix
     - Others indicate inter-site *V* matrix
       
2. vc-relax calculaton of DFT+*U*+*V* using eACBN0 for GaAs

3. scf calculation of DFT+*U*+*V* using the fixed occupation number for MnO

   - First, run `MnO.gga.in`, then run `MnO.acbn0.in`.
  
4. scf calculation of noncollinear DFT+*U*+*V* using eACBN0 for GaAs

    - Fully relativistic pseudopotentials are used to include spin-orbit coupling effects.
  
## Licence

This software is released as free software: you may copy, distribute, and/or modify it under the terms of the GNU General Public License published by the Free Software Foundation ([link](https://www.gnu.org/licenses)), either version 2 of the License or any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

Copyright (C) 2025 W. Yang, S.-H. Lee, and Y.-W. Son
   
   
