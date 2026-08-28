# DFTB (Density Functional Tight Binding)
## 1. What is DFTB
Density Functional Tight-Binding(DFTB) is a fast computer simulation method that is derived for the 
Density Functional Theory (DFT). It is used to calculate and simulate the properties and behaviour of atoms, molecules and materials. 
It bridges the gap between highly accurate but slow methods like the standard DFT and fast but less accurate classical molecular dynamics by using the precalculated parameters

## 2. What is DFTB used for?
This quantum simulation method is used in the fields of physics, chemistry and material science to simulate the behaviour of materials and chemical reactions at the atomic scale.

Other applications that the DFTB is common in is the following:
-**Nanotechnology:** Simulating the structure and electrical properties of carbon nanotubes, 2D materials
-**Materials Science:** Studying defects in semiconductors materials, surface reactions in catalysts and the developments of polymers and batteries
Biochemistry: Modeling large biomolecules like DNA and proteins to understand how they interact with specific drugs
-**Chemical Reactions:** Simulating complex real-time processes and combustion reactions over a long time frame
## 3. When is DFTB used?
DFTB is used when a simulation needs quantum mechanical accuracy but the standard Density Functional Theory (DFT) is too slow or computationally expensive to handle the job.

Certain conditions may be:
- **Large Atomic Systems:** When a simulation involves hundreds to a thousand of atoms, it may take weeks or even months to use your standard DFT. 
- **Long Timescales:** When running molecular dynamics simulations that require checking the movement of atoms over thousands of sequential time steps to observe a reaction or phase change
- **High-Throughput Screening:** When researchers need to rapidly scan thousands of different candidate material or chemical structures to find the best option before doing a slow high-precision testing
- **Limited Computational Budgets:** When the budget for supercomputer access or high-end hardware is limited, DFTB is the best option to run complex calculations on standard laboratory workstations

## 4. What goes into a DFTB calculation?
In order for you to run a DFTB calculation, you will need two primary inputs: the physical structure of your system and a set of pre-calculated parameters that define how the atoms interact

### 1. The Atomic Structure
- **Atomic Coordinates:** This is the exact position that every atom in 3D space
- **Chemical Composition:** This is the types of elements that are present
- **Boundary Conditions:** Whether the system is a single isolated molecule or a repeating single structure

### 2. Slater-Koster Parameter
Unlike your standard DFT, which calculates the electron interactions from the beginning, DFTB relies on pre calculated data tables which are called Slater-Koster files(.skf)

These files contain pre-computed info about how pairs of elements interact with each other
This can be download the parameter set for the specific elements in your systems from repositories

### 3. Calculation Setting(The input file)
In this case, you provide instructions to the DFTB software specifying:
- **The task type-** this specifies whether you want a single energy calculations, a structural optimizations or a molecular dynamics run
- **Change handling:** Here you specify if you want self-consistent charges such as SCC-DFTB, which is necessary if your system has polar bonds or shifting electrical charges
- **Total Charge/Spin:** This is the overall electrical charge of the molecule and its electronic spin state

## 5. What happens during a DFTB calculation?
During a DFTB calculation, the software will executes a sequence of mathematical approximations to solve something known as the Schrodinger equation and determine the energy and electronics properties of your system.

### 1. Initialization and Parameter Loading
The software starts by reading the atomic coordinates given and identifies the elements that are presents
**Loading Slater-Koster Tables:** The software looks for the pre-calculated files for every pair of the elements in your system. Instead of the system calculating complex electronic integrals from scratch, the just simply extracts those values from your file based on the distance between your atoms
### 2. Constructing the Hamiltonian Matrix
Setting up the Quantum Framework: The software builds a simplified Hamiltonian matrix which represents the total energy operator of the system.
**Applying the Tight-Binding Approximation-** The software assumes that the electrons remain tightly bound to their host atoms. Because of this, it will only calculate interactions between the atom and its closest neighbours, treating the distance atoms as having zero electronic overlap. This skills helps to reduce the size of the math problem and also reduces the amount of time involved in the process.
### 3. The Self-Consistent Charge (SCC) Loop
If your system has polar bonds like the bond in water, charge shift between atom. The software resolves this using an iterative loop:
- The loop makes an initial guess of the atomic charges
- The loop computes the electrostatic repulsion or attraction caused by these charges
- The loop adjusts the matrix, re-calculates the charges and repeats this loop until the charges stop lopping. This process is called **reaching convergence**
### 4. Evaluating Total Energy and Forces
Once the electronic structure is solved, the software calculates the following
Total Energy, which is the final quantum mechanical energy of the current atomic layout
Forces, which will be the direction and strength of the push or pull effect on every individual atom
### 5. Updating the Structure (The final Output)
Depending on the settings of your system, the software finishes the calculations by moving the atoms based on the force that has been calculated
- **Geometry Optimization:** It moves the atoms slightly to lower the forces experienced and repeats the whole process again until the molecule reaches it most stable and relaxed shape
- **Molecular Dynamics:** This applied Newton's Laws of Motion to advance the atoms forward in time by a tiny fraction of a second, simulating how the molecules vibrate and react in real-time

## 6. What to focus on when running the benchmark
When running this benchmark, the primary goal is to find the "sweet spot" where the software runs as fast as possible without wasting expensive computational resources

Because DFTB is lighter than the standard DFT, its performance bottlenecks are unique. You should focus your benchmark on the follwing:
### 1. Parallel Scalability (CPU Cores vs Nodes)
With DFTB, the mathematical matrices are smaller which can lead to communication overhead between separate server nodes quickly slowing down. You should focus on how well the software scales within a single nodes vs multiple nodes. DFTB performs best when kept on a single node with many cores or a very small number of tightly connected nodes
### 2. Memory (RAM), Bandwidth and Size
DFTB is memory intensive.
If you are running Self-Consistent Charge(SCC) calculations, the software repeatedly reads and writes matrix data. The performance will depend on the memory bandwidth of your architecture
Ensure the benchmark tracks the peak memory usage (MaxRSS) to ensure large systems that can have more than 10 000 atoms do not run out of RAM  on the standard compute nodes
### 3. File I/O performance
DFTB relies on external Slater-Koster parameter tables. Depending on how the code was compiled and the system size, Input/Output can cause hidden bottlenecks
At the beginning of the job, the software must read these files. If you are working on a big parallel run where hundreds of tasks tru to read the same file at the same time from a shared parallel file system, it can cause a temporary I/O bottleneck

## 7. Advantages
The main advantage of DFTB is that it provides a high quality, quantum mechanical description of molecules and materials at a cheaper price than the standard DFT.
Other advantages include:
- **Massive Speed:** DFTB is 100 to 1000 times faster than the standard DFT. turning simulations that would take weeks to months into calculations take take minutes to hours.
- **Larger System Sizes:** It allows researchers to simulate a system that contain thousands of atom that would be impossible to run with your traditional quantum chemistry methos
- **Extended Timescales:** Because each individual calculation is fast, it allows molecular dynamics simulations to run for a longer period of time. This allows scientists and researchers to watch the chemical reactions and physical transitions unfold in real time

