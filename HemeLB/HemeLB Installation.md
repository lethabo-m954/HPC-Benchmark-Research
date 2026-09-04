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

A simulation directory contains the required input files:

```text
test_case/
├── input.xml
├── geometry.gmy
└── velocity.dat
```

### `velocity.dat`

The `.dat` file contains the velocity profile used by the simulation. It defines the velocity at specific points in time.

Example:

```text
# time(s)   velocity(m/s)
0.0         0.05
0.1         0.10
0.2         0.15
```

The first column represents **time in seconds (s)**, and the second column represents **velocity in meters per second (m/s)**.

> **Note:** Modify the velocity profile to match your experiment. All units must be in SI units, including **seconds (s)** for time and **meters per second (m/s)** for velocity.

### `input.xml`

The `input.xml` file contains the configuration settings for the HemeLB simulation.

The values in this file should be modified according to the requirements of the experiment.

Example:

```xml
<hemelbsettings>
  <geometry>geometry.gmy</geometry>
  <initialconditions>
    <velocity>0.1</velocity>
  </initialconditions>
  <simulation>
    <timesteps>10000</timesteps>
    <outputfrequency>100</outputfrequency>
  </simulation>
  <analysis>
    <extractor type="Velocity" location="all" />
    <extractor type="Pressure" location="all" />
  </analysis>
</hemelbsettings>
```
> **Note:** Modify the following settings according to your experiment:
>
> 1. Change `<geometry>` to point to your `.gmy` file.
> 2. Adjust `<timesteps>` for the required simulation length.
> 3. Add or remove `<extractor>` entries to control what data is written out.


### Simulation Files

| File | Purpose |
|---|---|
| `input.xml` | Contains the configuration settings for the HemeLB simulation. |
| `geometry.gmy` | Contains the computational geometry used by the simulation. |
| `velocity.dat` | Contains the velocity profile, with time and velocity values. |

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
