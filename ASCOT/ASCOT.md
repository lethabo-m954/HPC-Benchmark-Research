# ASCOT (Adaptive Self-Correction Chain-of-Thought)

## 1. What is ASCOT
ASCOT also known as ASCOT5 is a parallel physics simulation code used to model nuclear fusion reactors. This application was developed by Aalto University and VTT in Finland.

## 2. What is ASCOT used for?
ASCOT is used to simulate and predict how high-energy particles behave inside of nuclear fusion reactors. 

When researchers test or design confinement reactors like tokamaks, they must ensure the superheated plasma doesn't destroy the machine. This is where ASCOT comes in. It is deployed in supercomputers to calculate those risks.

Specifically it is used for
~ HPC Hardware Benchmarking - The code is structurally highly parallelized - written using a hybrid mix of MPI, OPENMPI and OpenACC GPU offloadling.
~ It is designed to use thousands of CPU cores across a massive cluster. In the HPC community, it is mostly used as a benchmark to stress test raw scaling limits and memory architecture pf the next generation supercomputers
Simulating Collisions (Neoclassical Transport)- ASCOT calculates how fast moving particles collide with each other and with the background plasma. This information helps scientists understand how heat and energy disperse over time.

## 3 ASCOT's Dependencies
In order to build and ASCOT Fusion Simulation Code on high-performance platforms, your system environment requires certain dependencies.

### 1. Build Tools and System Core
Because the primary physics simulation engine is highly optimized and within in C Language, it will require the following compilers:
~ C Compiler: GCC, Clang, or Intel icc/icx compiler suites.
~ Make: For handling build scripts.
~ zlib: Compression library required for general data utilities.
### 2. Math and Data Libraries

ASCOT handles a lot of physics datasets, robust array computing and self-describing file formats

~ HDF5: Crucial, as ASCOT5 reads all simulation input parameters and outputs particle tracking histories explicitly in the HDF5 format.
~ BLAS / LAPACK: Essential for linear algebra and high-speed multi-dimensional array calculations.
### 3. Parallelization & HPC Scaling
If you will be running the benchmark on a clustered supercomputer, you will leverage different parallelization dependencies
~ OpenMP- Required for shared-memory multithreading across many-core CPUs
~ MPI - : Such as OpenMPI, MPICH, or Intel-MPI. Required to scale the simulation across multiple nodes on an HPC cluster.


