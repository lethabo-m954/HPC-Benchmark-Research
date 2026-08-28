# ASCOT (Adaptive Self-Correction Chain-of-Thought)

## 1. What is ASCOT
ASCOT also known as ASCOT5 is a parallel physics simulation code used to model nuclear fusion reactors. This application was developed by Aalto University and VTT in Finland.

## 2. What is ASCOT used for?
ASCOT is used to simulate and predict how high-energy particles behave inside of nuclear fusion reactors. 

When researchers test or design confinement reactors like tokamaks, they must ensure the superheated plasma doesn't destroy the machine. This is where ASCOT comes in. It is deployed in supercomputers to calculate those risks.

Specifically it is used for
- **HPC Hardware Benchmarking** - The code is structurally highly parallelized - written using a hybrid mix of MPI, OPENMPI and OpenACC GPU offloadling.

It is designed to use thousands of CPU cores across a massive cluster. In the HPC community, it is mostly used as a benchmark to stress test raw scaling limits and memory architecture pf the next generation supercomputers

- **Simulating Collisions (Neoclassical Transport)-** ASCOT calculates how fast moving particles collide with each other and with the background plasma. This information helps scientists understand how heat and energy disperse over time.

## 3 ASCOT's Dependencies
In order to build and ASCOT Fusion Simulation Code on high-performance platforms, your system environment requires certain dependencies.

### 1. Build Tools and System Core
Because the primary physics simulation engine is highly optimized and within in C Language, it will require the following compilers:

- C Compiler: GCC, Clang, or Intel icc/icx compiler suites.

- Make: For handling build scripts.

- zlib: Compression library required for general data utilities.
### 2. Math and Data Libraries

ASCOT handles a lot of physics datasets, robust array computing and self-describing file formats 
- HDF5: Crucial, as ASCOT5 reads all simulation input parameters and outputs particle tracking histories explicitly in the HDF5 format.
- BLAS / LAPACK: Essential for linear algebra and high-speed multi-dimensional array calculations.
### 3. Parallelization & HPC Scaling
If you will be running the benchmark on a clustered supercomputer, you will leverage different parallelization dependencies:
- OpenMP- Required for shared-memory multithreading across many-core CPUs
- MPI - : Such as OpenMPI, MPICH, or Intel-MPI. Required to scale the simulation across multiple nodes on an HPC cluster.

## 4. When is ASCOT used?
In HPC, ASCOT5 is used to test and evaluate the multi-core CPU processing efficiency of a supercomputer hardware. It also plays a role in fusion plasma physics as it serves as an orbit-following Monte Carlo code to model fast ions, their impurities and runaway electrons
## 5. What goes into the calculation?
ASCOTS's calculation goes into simulating the time evolution of the distribution function of minority particle species.

Because this application operates as a **test-particle Monte Carlo code**, the calculation loops through millions of independent "marker" particles.

### 1. The Data Inputs Required
Before any calculations start, the user gives the software a structured set of variables, which will typically be in a Python interface called a5py.

- **Magnetic Field Grid:** This will be 3D magnetic components that map out the tokamak or stellarator geometry
- **Plasma Profile:** This is density, temperature and composition profiles of the background electrons and majority ion species
- **Initial Particle States:** This is the starting positions, velocity coordinates, mass and charge of the particles that are being tracked

### 2. The Core Physics Solvers

Once initiated, ASCOT will calculate the particle behavior at every finite time step by using two solutions:
- **Equation of Motion (Orbit Tracing):** The code will integrate differential equations to trace paths. The code will either evaluate the actual spiral path around the magnetic lines or it will evaluate the simplified average path of the particle.
- **Fokker-Planck Coulomb Collisions:** This will calculate the slowing-down process when the fats particles will collide with, scatter off, and deposit energy into the background plasma.

### 3. The Calculated Output
The computation will track these particles until they slow down completely. After that, it will undergo a nuclear reaction, or escape. This will result in the following:

- Phase-Space Distributions - 1D-6D density distributions of the fast ions
- Neoclassical Transport Coefficients - This is the rate of particle diffusion across the magnetic fields
- Wall Loads - This is the exact location, quantity and heat flux energy of particles slamming into the 3D reactor wall
- 
## 6. What to focus on when running the benchmark?
When doing HPC benchmarking and you decide to run the ASCOT5 benchmark, your focus should be on how efficiently the software scales the hardware and how quickly it completes its physics. The primary goal should be to maximize throughput and eliminate hardware bottlenecks.

- **MPI and OpenMP Scaling:** This benchmark divides particles across different compute nodes using  Message Passing Interface (MPI) then across individual CPU cores by using OpenMP Threads. Your job should be to balance the number of particles per thread so that all the cores finish their work at the exact same time without stalling.
- **Memory and I/O Bottlenecks** While core calculation happens in the processor's cache, loading the large 3D magnetic grids and writing out the final HDF5 particle particle data can cause delays. Your job is to focus on optimizing the file read/write speeds on the cluster storage
- **Compiler Optimization Flags:**  Since the code relies heavily on integrating mathematical equations of motion, make sure that you use the best **vectorization and math library flags** during compilation to wring out maximum hardware performance.

## 7. Advantages
- ASCOTS uses all the main processors and graphics card at the same time to work much faster.
- It groups millions of particles together so that they can be calculated at the same time instead of them being calculated individually.
- It is built for today's supercomputers, making it much faster the older version called ASCOTR4.
- This application allows you to choose between fast, simple path or a slow hyper-detailed path for the particles

## 8. Limitations
- Running this application requires you to know coding skills such as Python and computer terminal commands to set up the data.
- Getting the code to talk to the computer hardware can be frustrating and difficult to set up on a new supercomputer
- To get fast and accurate results, you will need expensive supercomputers.
- This application requires you to have a lot of space because it creates massive data files  that can quickly fill up your computer storage.
- In only simulates a small number of fast particles. It cannot simulate the main background plasma itself




