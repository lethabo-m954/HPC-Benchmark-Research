# HemeLB Installation, Compilation and Running Guide

This guide explains how to install the required dependencies, download HemeLB, compile the source code, and run a HemeLB simulation on an HPC system.

## 1. Update the System 

Update the system package list(On all nodes):

```bash
sudo apt update
```


## 2. Install HemeLB Dependencies

Install the basic development tools and libraries required to build HemeLB:

```bash
sudo apt install -y build-essential cmake git \
    libboost-all-dev libtinyxml2-dev libtinyxml-dev \
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
| `libtinyxml-dev` | Provides XML v1 parsing functionality (Required by HemeLB sub-modules) |
| `libparmetis-dev` | Provides ParMETIS development libraries |
| `libmetis-dev` | Provides METIS development libraries. |

---

OpenMPI and OpenBLAS were already installed as part of the HPC environment and therefore do not need to be installed again.

---

## 3. Clone HemeLB

Download the HemeLB source code (Compute 1):

Ensure your compiler environment is active
```bash
module load gcc/system
```
Download and build HemeLB
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
cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=~/hemelb/install
```

Compile HemeLB:
```bash
make -j$(nproc)
```
Once the compilation completes successfully, run the install step to gather all binaries into that clean path:
```bash
make install

```
### Verify your compilation
'''bash
ls -l ~/hemelb/build/hemelb-prefix/src/hemelb-build/
```

---

## 5. Prepare the Simulation

Create a directory for the simulation (On your headnode):

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
<?xml version="1.0"?>
<hemelbsettings version="5">
  <simulation>
    <step_length units="s" value="1e-5"/>
    <steps units="lattice" value="50000"/>
    <stresstype value="1"/>
    <voxel_size units="m" value="5e-5"/>
    <origin units="m" value="(0.0,0.0,0.0)"/>
  </simulation>
  
  <geometry>
    <datafile path="large_cylinder.gmy"/>
  </geometry>
<initialconditions>
    <pressure>
      <uniform units="mmHg" value="0.0"/>
    </pressure>
  </initialconditions>

  <inlets>
    <inlet>
      <condition type="pressure" subtype="cosine">
        <amplitude value="0" units="mmHg" />
        <mean value="0" units="mmHg" />
        <phase value="0" units="rad" />
        <period value="1" units="s" />
      </condition>
      <normal units="dimensionless" value="(0.0,0.0,1.0)" />
      <position units="m" value="(0.0,0.0,-2.4e-5)" />
    </inlet>
  </inlets>

  <outlets>
    <outlet>
      <condition type="pressure" subtype="cosine">
        <amplitude value="0" units="mmHg" />
        <mean value="0" units="mmHg" />
        <phase value="0" units="rad" />
        <period value="1" units="s" />
      </condition>
      <normal units="dimensionless" value="(0.0,0.0,-1.0)" />
      <position units="m" value="(0.0,0.0,2.4e-5)" />
    </outlet>
  </outlets>

  <visualisation>
    <centre units="m" value="(0.0,0.0,0.0)" />
    <orientation>
      <longitude units="deg" value="45.0" />
      <latitude units="deg" value="45.0" />
    </orientation>
    <display brightness="0.03" zoom="1.0" />
    <range>
      <maxvelocity units="m/s" value="0.1" />
      <maxstress units="Pa" value="0.1" />
    </range>
  </visualisation>
  
<initialconditions>
    <pressure>
      <uniform units="mmHg" value="0.0" />
    </pressure>
  </initialconditions>

  <monitoring>
    <incompressibility/>
  </monitoring>

  <properties>
    <property type="Velocity">
      <geometry type="All"/>
      <output path="Extracted/velocity.dat"/>
      <period value="100"/>
    </property>
    <property type="Pressure">
      <geometry type="All"/>
      <output path="Extracted/pressure.dat"/>
      <period value="100"/>
    </property>
  </properties>
</hemelbsettings>
```

---
> **Note:** Modify the following settings according to your experiment:
>
> 1. Ensure `<datafile path=...>` points exactly to your `.gmy` file name.
> 2. Ensure `<hemelbsettings version="5">` is declared at the root tag.
> 3. Clean out the target output folder before running, as HemeLB will abort execution if the destination folder already exists.

### `geometry.gmy`

The `geometry.gmy` file contains the computational geometry used by the HemeLB simulation.

The `.gmy` file is normally **not modified manually**. It is generated from the required geometry and then referenced in the `input.xml` file.

## 6. Run HemeLB
Before running the simulation, you must configure your cluster shortcuts and host specifications (**On the Headnode**).

### Step A: Create a System Shortcut for HemeLB
Instead of typing the long internal build path, link the binary to a clean home file target:
```bash
ln -s /home/ubuntu/hemelb/build/hemelb-prefix/src/hemelb-build/hemelb ~/hemelb_exe
```

### Step B: Configure your MPI Cluster Hostfile
Create or edit your local cluster host profile mapping available computing blocks:
```bash
nano ~/hosts
```
Paste your resource framework layout inside:
```text
10.100.0.10 slots=2
10.100.0.11 slots=4
10.100.0.12 slots=4
```

To run the simulation across multi-node infrastructure, you must use your compiled OpenMPI pathways and pass tracking variables ('x') to cleanly bypass PMIx system validation roadblocks (Run from the headnode)

> 1. Always remove any existing output directory before starting a sun
```bash
rm -rf ./output
```
> 2. Run the simulation targeting your compute nodes directly

'''bash
/home/ubuntu/opt/openmpi/bin/mpirun \
  -x PATH=/home/ubuntu/opt/openmpi/bin:$PATH \
  -x LD_LIBRARY_PATH=/home/ubuntu/opt/openmpi/lib:/home/ubuntu/opt/openblas/lib:$LD_LIBRARY_PATH \
  --host 10.100.0.11:4,10.100.0.12:4 \
  -n 8 \
  /home/ubuntu/hemelb/build/hemelb-prefix/src/hemelb-build/hemelb \
  -in input.xml -out ./output/
```
## 7. Post-Processing: Extracting Simulation Data

HemeLB writes fluid mechanics data dynamically into heavily compressed binary archive streams inside your `output/` directory. To unpack these files into human-readable tables, ensure **python3-numpy** is installed and invoke the Python extraction tool (**On the Headnode**):


# 1. Ensure NumPy is active on the system
```bash
sudo apt install -y python3-numpy
```
# 2. Extract raw data logs into clean text dump tracking lists
```bash
PYTHONPATH=~/hemelb/python-tools python3 -m hlb.converters.ExtractedPropertyTextDump ./output/
```
Once this script finishes processing, individual tracking files will populate inside the `./output/Extracted/` subdirectory.
---

## 8. Visualize Results with ParaView
Copy the output file
Open the results in **ParaView:**
1. Open Parview.
2. Select **File** and go to **Open**
3. Select the HemeLB output file.
4. Click **Apply**
5. Select **Velocity** or **Pressure** to view the results.
6. Use the 3D controls to rotate and zoom.
7. Use **Play** to view changes over time if multiple time steps were generated

### Results to Analyse:
1. **Velocity-** how fast the blood is moving.
2. **Pressure-** how pressure changes through the geometry
3. **Flow patterns-** how the blood moves through the geometry.
4. **Time changes-** how velocity and pressure change during the simulation

## 9. HemeLB Workflow

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
