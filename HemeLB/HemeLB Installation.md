# HemeLB Installation, Compilation and Running Guide

This guide explains how to download, compile and run HemeLB on an HPC system.

## 1. Prerequisites

The HPC system already has the required HPC software installed, including OpenMPI, OpenBLAS, and other libraries used for HPC applications.

Check the required tools:

```bash
gcc --version
cmake --version
git --version
mpirun --version
```

---

## 2. Clone HemeLB

Download the HemeLB source code:

```bash
git clone https://github.com/hemelb-codes/hemelb.git
```

Move into the HemeLB directory:

```bash
cd hemelb
```

---

## 3. Compile HemeLB

Create a build directory:

```bash
mkdir build
cd build
```

Configure HemeLB using CMake:

```bash
cmake .. -DCMAKE_BUILD_TYPE=Release
```

Compile the source code:

```bash
make -j$(nproc)
```

---

## 4. Prepare the Simulation

Create a directory for the simulation:

```bash
mkdir -p ~/hemelb_runs/test_case
cd ~/hemelb_runs/test_case
```

A simulation directory contains the required input files:

```text
test_case/
├── input.xml
└── geometry.gmy
```

### Input Files

| File | Purpose |
|---|---|
| `input.xml` | Contains the simulation configuration |
| `geometry.gmy` | Contains the computational geometry |

---

## 5. Run HemeLB

Run HemeLB using MPI:

```bash
mpirun -n 4 hemelb -in input.xml -out ./output/
```

Where:

- `-n 4` specifies 4 MPI processes.
- `-in input.xml` specifies the input file.
- `-out ./output/` specifies where the simulation results are saved.

---

## 6. HemeLB Workflow

```text
Clone HemeLB
     ↓
Create build directory
     ↓
Configure with CMake
     ↓
Compile
     ↓
Prepare input.xml and geometry.gmy
     ↓
Run with MPI
     ↓
View output
```
