# HemeLB Installation, Compilation and Running Guide

This guide explains how to install the required dependencies, download HemeLB, compile the source code, and prepare HemeLB to run.

## 1. Install Dependencies
Before installing HemeLB, update the system and install the required development tools and libraries
### Update the system
Run:
```bash
sudo apt update
```

### Install required dependencies
```bash
sudo apt install -y build-essential cmake git \
    libboost-all-dev libtinyxml2-dev \
    libparmetis-dev libmetis-dev \
    libopenmpi-dev openmpi-bin
```
Executable:
```bash
HemeLB/build/bin/hemelb
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
| `libmetis-dev` | Provides METIS development libraries |
| `libopenmpi-dev` | Provides OpenMPI development files |
| `openmpi-bin` | Provides MPI executables such as `mpirun` |

---
## 2. Clone HemeLB
```bash
git clone https://github.com/UCL/HemeLB.git
```
Move into the HemeLB directory:
```bash
cd HemeLB
```
### What does the above command do?
"git clone" command downloads the HemeLB source code from the GitHub repository.
"cd HemeLB" command moves you into the directory containing the downloaded HemeLB  code.

## 3.Compile HemeLB

Once HemeLB has been downloaded, create a separate directory for the comiled files

### Create the build directory
```bash
mkdir build && cd build
```

Keeping the build files separate from the source code makes the project easier to manage.

### Configure the build with CMake

```bash
cmake .. -DCMAKE_BUILD_TYPE=Release
```
### Compile HemeLB

```bash
make -j$(nproc)
```

After `make` finishes, check that the compilation completed successfully.

```bash
ls
```

Look for the HemeLB executable and other generated build files.

---
