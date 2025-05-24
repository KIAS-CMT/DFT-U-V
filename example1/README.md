scf calculation of DFT+*U*+*V* using eACBN0 for Ni

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
