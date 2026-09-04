# HemeLB Installation, Compilation and Running Guide

This guide explains how to install the required dependencies, download HemeLB, compile the source code, and run a HemeLB simulation on an HPC system.

## 1. Update the System

Update the system package list:

```bash
sudo apt update
```

---

## 2. Install HemeLB Dependencies

Install the basic development tools and libraries required to build HemeLB:

```bash
sudo apt install -y build-essential cmake git \
    libboost-all-dev libtinyxml2-dev \
    libparmetis-dev libmetis-dev
```

OpenMPI and OpenBLAS were already installed as part of the HPC environment and therefore do not need to be installed again.

Check that the required software is available:

```bash
gcc --version
cmake --version
git --version
mpirun --version
```

---

## 3. Clone HemeLB

Download the HemeLB source code:

```bash
git clone https://github.com/hemelb-codes/hemelb.git
```

Move into the HemeLB directory:

```bash
cd hemelb
```

---

## 4. Compile HemeLB

Create a build directory:

```bash
mkdir build
cd build
```

Configure the build:

```bash
cmake .. -DCMAKE_BUILD_TYPE=Release
```

Compile HemeLB:

```bash
make -j$(nproc)
```

---

## 5. Prepare the Simulation

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

| File | Purpose |
|---|---|
| `input.xml` | Contains the simulation configuration |
| `geometry.gmy` | Contains the computational geometry |

---

## 6. Run HemeLB

Run HemeLB using MPI:

```bash
mpirun -n 4 hemelb -in input.xml -out ./output/
```

Where:

- `-n 4` specifies 4 MPI processes.
- `-in input.xml` specifies the simulation input file.
- `-out ./output/` specifies the output directory.

---

## 7. HemeLB Workflow

```text
Update system
     ↓
Install dependencies
     ↓
Clone HemeLB
     ↓
Configure with CMake
     ↓
Compile HemeLB
     ↓
Prepare simulation files
     ↓
Run with MPI
     ↓
View output
```
