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
### What are these dependencies?

| Package | Purpose |
|---|---|
| `build-essential` | Provides essential tools for compiling software |
| `cmake` | Configures the HemeLB build |
| `git` | Downloads the HemeLB source code |
| `libboost-all-dev` | Provides Boost C++ libraries |
| `libtinyxml2-dev` | Provides XML parsing functionality |
| `libparmetis-dev` | Provides ParMETIS development libraries |

---

OpenMPI and OpenBLAS were already installed as part of the HPC environment and therefore do not need to be installed again.

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

A simulation directory contains the files required to configure and run a HemeLB simulation.

For example:

```text
test_case/
├── simulation.dat
├── input.xml
└── geometry.gmy
```

### Simulation Files

| File | Purpose |
|---|---|
| `simulation.dat` | Contains the parameters and settings required for the simulation. |
| `input.xml` | Contains the configuration information used by the simulation. |
| `geometry.gmy` | Contains the computational geometry used by HemeLB. |

### `.dat` File

The `.dat` file is a simulation input file. It contains parameters and settings that define how the HemeLB simulation should be performed.

The `.dat` file must be available in the appropriate simulation directory before running the simulation.

Copy the `.dat` file into the simulation directory:

```bash
cp /path/to/simulation.dat ~/hemelb_runs/test_case/
```

Replace `/path/to/simulation.dat` with the actual location of the `.dat` file.

Check that the file has been copied successfully:

```bash
ls
```

The `ls` command lists the files in the current directory. The `.dat` file should appear in the list.

> **Note:** The exact files required for a simulation depend on the HemeLB version and the simulation setup being used.

---

## 6. Run HemeLB

Once HemeLB has been compiled and the required simulation files have been prepared, the simulation can be run using MPI.

Example:

```bash
mpirun -n 4 hemelb -in input.xml -out ./output/
```

### Command Explanation

| Command/Option | Purpose |
|---|---|
| `mpirun` | Starts the HemeLB program using MPI for parallel execution. |
| `-n 4` | Specifies that 4 MPI processes should be used. |
| `hemelb` | Runs the HemeLB executable. |
| `-in input.xml` | Specifies the input configuration file. |
| `-out ./output/` | Specifies the directory where the simulation output should be stored. |

MPI allows the HemeLB simulation to run across multiple processes at the same time. This is important for HPC systems because simulations can be distributed across multiple CPU cores or compute nodes.

---

## 7. HemeLB Workflow

The general HemeLB workflow is:

```text
Update system
     ↓
Install dependencies
     ↓
Clone HemeLB
     ↓
Create build directory
     ↓
Configure with CMake
     ↓
Compile HemeLB
     ↓
Prepare simulation directory
     ↓
Add simulation files
     ↓
Prepare .dat file
     ↓
Run HemeLB with MPI
     ↓
View simulation output
```

## Summary

The main steps for installing and running HemeLB are:

1. Update the system package list.
2. Install the required HemeLB dependencies.
3. Clone the HemeLB source code from GitHub.
4. Create a build directory.
5. Configure HemeLB using CMake.
6. Compile HemeLB using `make`.
7. Create a directory for the simulation.
8. Prepare the required simulation files.
9. Place the required `.dat` file in the simulation directory.
10. Run HemeLB using MPI.
11. Check the simulation output.
