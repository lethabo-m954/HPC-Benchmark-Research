# HemeLB

## 1. What is HemeLB?

HemeLB is an open-source software application designed to simulate blood flow through complex blood vessels using high-performance computing (HPC) systems.

It is specifically designed for simulating blood flow through complex and irregular geometries, such as human blood vessels.

HemeLB uses the **Lattice Boltzmann Method (LBM)** to calculate how fluid behaves inside the computational geometry.

Unlike traditional fluid simulations that may calculate the fluid across an entire rectangular or cubic region, HemeLB is designed to work efficiently with **sparse and complex geometries**. This means that it can focus computational resources on the areas where fluid is actually present.

---

## 2. What Scientific/Engineering Problem Does It Solve?

HemeLB helps bridge the gap between **high-performance computing, computational fluid dynamics, and clinical medicine**.

Medical scans can provide detailed 3D information about a patient's blood vessels. However, a medical scan mainly shows the structure and shape of the vessels. It does not directly show how blood is moving through those vessels.

HemeLB can use patient-specific vessel geometries to simulate blood flow and calculate physical quantities such as:

- Velocity
- Pressure
- Flow behaviour
- Wall-related quantities
- Other fluid-flow measurements

This can help researchers investigate blood-flow behaviour in complex vascular structures.

### The Scaling Problem

Accurately simulating blood flow requires a large number of calculations. A complex blood vessel may contain millions or billions of computational points.

Running these calculations on a single CPU would take a very long time.

HemeLB is therefore designed to run using **parallel computing**, where the simulation is divided between many CPU cores.

### The Engineering Challenge

When a simulation is divided between many CPU cores, the cores must communicate with each other.

This communication becomes particularly important when the computational domain is divided between different processes or compute nodes.

HemeLB uses **MPI (Message Passing Interface)** to allow these processes to communicate.

The Lattice Boltzmann Method is well suited to parallel computing because much of the calculation is local. However, information still needs to be exchanged between neighbouring computational regions.

---

## 3. Why Is HemeLB Used as an HPC Application/Benchmark?

HemeLB can be used as both a **scientific application** and an **HPC benchmark**.

It is useful for testing how well a computer system can perform a large-scale fluid simulation.

### 3.1 Strong Scaling

**Strong scaling** measures how the time required to solve a fixed-size problem changes when more processors are added.

For example:

```text
Same simulation
      ↓
1 CPU
      ↓
2 CPUs
      ↓
4 CPUs
      ↓
8 CPUs
      ↓
16 CPUs
```

Ideally, adding more processors should reduce the simulation time.

However, the improvement eventually becomes smaller because the processors need to communicate with each other.

HemeLB is useful for testing this because its simulations can involve a large number of computational points and MPI processes.

### 3.2 Testing HPC Communication

HemeLB requires communication between MPI processes.

When the simulation is distributed across multiple compute nodes, these processes must exchange information across the network.

This means HemeLB can be used to investigate:

- Network communication performance
- MPI performance
- Communication overhead
- CPU performance
- Memory performance
- Load balancing
- Scaling efficiency

### 3.3 Hardware and Software Optimisation

HemeLB can also be used to investigate how different hardware and software configurations affect performance.

Examples include:

- Number of CPU cores
- Memory bandwidth
- Network performance
- MPI configuration
- Compiler optimisations
- Process placement
- Domain partitioning

Benchmark results can help identify which part of an HPC system is limiting performance.

---

## 4. What Calculations Are Performed?

HemeLB uses the **Lattice Boltzmann Method (LBM)** to simulate fluid flow.

LBM represents the fluid using distribution functions on a computational lattice.

The simulation repeatedly performs two main steps:

1. **Collision**
2. **Streaming**

### 4.1 Collision

During the collision step, the distribution functions at a lattice site are updated according to the Lattice Boltzmann equations.

This represents the local interaction and relaxation of the fluid distributions.

The calculation is mainly local because information from neighbouring lattice sites is not required for the basic collision calculation.

### 4.2 Streaming

During the streaming step, the distribution functions move between neighbouring lattice sites according to their directions.

This means that information from one computational location is transferred to another.

When different MPI processes are responsible for different parts of the simulation, information may need to be exchanged between processes.

This is where communication between CPU cores and compute nodes becomes important.

### 4.3 Repeated Calculations

The collision and streaming steps are repeated for many simulation time steps.

For example:

```text
Initial conditions
       ↓
Collision
       ↓
Streaming
       ↓
Collision
       ↓
Streaming
       ↓
     ...
       ↓
Final simulation state
```

A large simulation may require thousands or millions of time steps.

---

## 5. What Metrics Are Important?

The most important HPC performance metrics when working with HemeLB include:

- MLUPS
- Runtime
- Strong scaling
- Scaling efficiency
- Load balance
- Memory bandwidth
- Communication performance
- I/O performance

### 5.1 MLUPS

**MLUPS** stands for:

**Million Lattice Updates Per Second**

It measures how many millions of lattice sites are processed per second.

A higher MLUPS generally means that the simulation is being processed faster.

For example:

```text
100 MLUPS
```

means approximately 100 million lattice updates are performed per second.

MLUPS is useful when comparing HemeLB performance between different systems or configurations.

### 5.2 Runtime

Runtime is the amount of time required to complete the simulation or benchmark.

A lower runtime generally means better performance for the same problem.

### 5.3 Scaling Efficiency

Scaling efficiency measures how effectively additional processors improve performance.

For example, if doubling the number of processors nearly halves the runtime, the scaling is good.

A simplified strong-scaling efficiency can be calculated as:

```text
Scaling Efficiency =
(T1 / Tp) / p × 100%
```

where:

- `T1` = runtime using one processor/process
- `Tp` = runtime using `p` processors/processes
- `p` = number of processors/processes

Perfect scaling would result in an efficiency close to **100%**.

### 5.4 Load Balance

Load balance describes how evenly the computational work is distributed between MPI processes.

For example:

```text
Process 1 → 25% of the work
Process 2 → 25% of the work
Process 3 → 25% of the work
Process 4 → 25% of the work
```

This is well balanced.

If one process has significantly more work:

```text
Process 1 → 15%
Process 2 → 15%
Process 3 → 15%
Process 4 → 55%
```

the other processes may spend time waiting for the process with more work to finish.

Poor load balance can reduce overall performance.

### 5.5 Memory Bandwidth

Memory bandwidth measures how quickly data can be transferred between the CPU and system memory.

HemeLB performs many memory accesses during the simulation.

Therefore, memory performance can have a significant effect on overall performance.

### 5.6 Communication Performance

MPI processes must exchange information during the simulation.

Communication performance depends on factors such as:

- Network latency
- Network bandwidth
- Number of MPI processes
- Number of compute nodes
- Process placement
- Amount of data exchanged

Poor communication performance can reduce scaling efficiency.

### 5.7 I/O Performance

I/O refers to reading and writing data.

A simulation may produce a large amount of output data.

Writing output too frequently can increase the total runtime.

For performance benchmarking, output frequency should therefore be considered carefully.

---

## 6. What Makes HemeLB Useful, and What Are Its Limitations?

### 6.1 Advantages

#### Sparse Geometry

HemeLB is designed for complex geometries such as blood vessels.

Blood-vessel geometries can contain large regions where there is no fluid.

HemeLB can therefore work efficiently with sparse computational domains instead of treating the entire bounding region as fluid.

#### Parallel Processing

HemeLB uses MPI to distribute the simulation across multiple processes.

This allows simulations to run across:

- Multiple CPU cores
- Multiple compute nodes
- Large HPC systems

#### Patient-Specific Geometry

HemeLB can be used with complex vessel geometries obtained from medical data.

This makes it useful for research involving realistic blood-flow structures.

#### HPC Benchmarking

HemeLB can be used to investigate:

- CPU performance
- Memory performance
- MPI communication
- Network performance
- Scaling
- Load balancing

### 6.2 Limitations

HemeLB is primarily designed around the **Lattice Boltzmann Method** and blood-flow related computational fluid dynamics.

Therefore, its performance does not represent how an HPC system will perform for every type of scientific application.

Different applications may have very different:

- Memory requirements
- Communication patterns
- Mathematical operations
- I/O requirements

Another limitation is that HemeLB simulations can generate significant amounts of output data.

If data is written too frequently, I/O can become a performance bottleneck.

---

# 7. Software and Hardware Requirements

HemeLB requires both software and suitable HPC hardware.

## 7.1 Software Requirements

The main software components required to build and run HemeLB include:

| Software / Library | Purpose |
|---|---|
| **Git** | Downloads the HemeLB source code. |
| **CMake** | Configures the HemeLB build. |
| **C/C++ compiler** | Compiles the HemeLB source code. |
| **MPI / OpenMPI** | Allows parallel HemeLB processes to communicate. |
| **Boost** | Provides required C++ libraries. |
| **TinyXML2** | Provides XML parsing functionality. |
| **METIS** | Provides graph partitioning functionality. |
| **ParMETIS** | Provides parallel graph partitioning. |
| **OpenBLAS** | Provides optimized numerical routines where required by the build/environment. |

> **Note:** OpenMPI and OpenBLAS may already be installed on an HPC system. Check the environment before installing them again.

## 7.2 Hardware Requirements

HemeLB is designed for HPC systems and benefits from:

- Multiple CPU cores
- Sufficient system memory
- High memory bandwidth
- Fast network interconnects
- Fast storage
- Multiple compute nodes for large simulations

The most suitable hardware depends on the size of the simulation and the number of MPI processes being used.

---

# 8. Dependencies and Libraries

The following libraries are important when building HemeLB.

### `build-essential`

Provides essential development tools required to compile software.

This normally includes tools such as:

- GCC
- G++
- Make

### `cmake`

CMake is used to configure the HemeLB source code before compilation.

It determines how the source code should be built and generates the required build files.

### `git`

Git is used to download the HemeLB source code from its repository.

### Boost

Boost is a collection of C++ libraries.

HemeLB requires Boost libraries for parts of its C++ implementation.

### TinyXML2

TinyXML2 is an XML parsing library.

It allows software to read and process XML configuration files.

HemeLB uses XML configuration information for simulation settings.

### METIS

METIS is a graph partitioning library.

Partitioning is important because the computational domain needs to be divided into sections that can be assigned to different processes.

### ParMETIS

ParMETIS provides parallel graph partitioning functionality.

It is particularly useful when the computational domain needs to be distributed across multiple MPI processes.

### MPI / OpenMPI

MPI stands for **Message Passing Interface**.

MPI allows different processes to communicate with one another.

HemeLB uses MPI so that different parts of the simulation can run in parallel.

For an HPC system, MPI communication can occur:

```text
CPU Core
   ↕
MPI Process
   ↕
Network
   ↕
MPI Process
   ↕
CPU Core
```

---

# 9. Important HemeLB Files

A HemeLB simulation uses several important files.

The main files to understand are:

| File | Purpose |
|---|---|
| `input.xml` | Contains the simulation configuration. |
| `geometry.gmy` | Contains the computational geometry. |
| `velocity.dat` | Contains velocity profile data when used by the simulation. |
| Output files | Contain the data generated by the simulation. |
| Log files | Contain information about the simulation and its execution. |

---

## 9.1 `input.xml`

The `input.xml` file contains the configuration settings for the HemeLB simulation.

It tells HemeLB important information about how the simulation should be performed.

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

> **Note:** The exact XML structure and available parameters depend on the HemeLB version and simulation configuration. The example above should be treated as a template for the setup being documented.

### Important Parameters

#### `<geometry>`

```xml
<geometry>geometry.gmy</geometry>
```

Specifies the geometry file that will be used by the simulation.

The `.gmy` file contains the computational geometry.

If a different geometry is required, the geometry entry must point to the appropriate `.gmy` file.

#### `<velocity>`

```xml
<velocity>0.1</velocity>
```

Specifies the velocity value used by the example initial-condition configuration.

The meaning and units of this value depend on how the simulation is configured.

#### `<timesteps>`

```xml
<timesteps>10000</timesteps>
```

Specifies the number of simulation time steps.

Increasing the number of time steps generally increases the amount of simulated time and therefore increases the amount of computation required.

#### `<outputfrequency>`

```xml
<outputfrequency>100</outputfrequency>
```

Controls how frequently simulation output is generated.

A smaller output frequency means data is written more frequently.

A larger output frequency means data is written less frequently.

Writing output too frequently can increase I/O overhead.

#### `<extractor>`

Example:

```xml
<extractor type="Velocity" location="all" />
```

An extractor controls what simulation information is collected and written as output.

For example:

```xml
<extractor type="Velocity" location="all" />
```

requests velocity information.

Another example is:

```xml
<extractor type="Pressure" location="all" />
```

which requests pressure information.

> **Note:** Add or remove `<extractor>` entries depending on what information needs to be collected from the simulation.

### What Should Be Modified?

When preparing `input.xml` for an experiment:

1. Change `<geometry>` to point to your `.gmy` file.
2. Adjust `<timesteps>` for the required simulation length.
3. Adjust simulation parameters according to the experiment.
4. Add or remove `<extractor>` entries to control what data is written out.
5. Check that the required input files are available in the simulation directory.

---

# 10. `velocity.dat`

The `.dat` file can contain a velocity profile used by the simulation.

The velocity profile defines how velocity changes over time.

Example:

```text
# time(s)   velocity(m/s)
0.0         0.05
0.1         0.10
0.2         0.15
```

The first column represents:

**Time in seconds (s)**

The second column represents:

**Velocity in meters per second (m/s)**

### How to Interpret the File

For example:

```text
0.0    0.05
```

means that at:

```text
Time = 0.0 seconds
```

the velocity is:

```text
Velocity = 0.05 m/s
```

The next row:

```text
0.1    0.10
```

means that at:

```text
Time = 0.1 seconds
```

the velocity is:

```text
Velocity = 0.10 m/s
```

### Important Note

> **Modify the velocity profile to match your experiment. All units must be in SI units, including seconds (s) for time and meters per second (m/s) for velocity.**

The `.dat` file should contain values that are consistent with the physical experiment being simulated.

---

# 11. `geometry.gmy`

The `geometry.gmy` file contains the computational geometry used by the HemeLB simulation.

The geometry represents the physical region through which the fluid is being simulated.

For example, the geometry could represent a complex blood-vessel structure.

The `.gmy` file is normally **not modified manually**.

It is generated from the required geometry and then referenced by the `input.xml` file.

For example:

```xml
<geometry>geometry.gmy</geometry>
```

This tells HemeLB which geometry file to use.

> **Note:** If a different geometry is required, generate a new `.gmy` file from the required geometry and update the `<geometry>` entry in `input.xml` to point to the new file.

---

# 12. Understanding the Simulation Workflow

The general HemeLB workflow is:

```text
Prepare geometry
       ↓
Generate/obtain .gmy file
       ↓
Prepare velocity/profile data
       ↓
Create input.xml
       ↓
Configure simulation parameters
       ↓
Start HemeLB
       ↓
MPI distributes the simulation
       ↓
HemeLB performs LBM calculations
       ↓
Extractors collect requested data
       ↓
Output files are generated
       ↓
Analyse results
       ↓
Visualise results
```

The important idea is:

**Input files control the simulation → HemeLB performs the calculations → output files contain the results.**

---

# 13. Running HemeLB

Once HemeLB has been compiled and the simulation files have been prepared, the simulation can be run using MPI.

A typical MPI execution has the following structure:

```bash
mpirun -n 4 hemelb -in input.xml -out ./output/
```

### Command Explanation

| Command / Option | Purpose |
|---|---|
| `mpirun` | Starts the application using MPI. |
| `-n 4` | Requests 4 MPI processes. |
| `hemelb` | Runs the HemeLB executable. |
| `-in input.xml` | Specifies the input configuration file. |
| `-out ./output/` | Specifies the output directory. |

> **Important:** The exact command-line options depend on the HemeLB version and build being used. The execution command should therefore be checked against the executable and version installed on the HPC system.

---

# 14. Understanding HemeLB Output

HemeLB produces output based on the simulation configuration and the extractors that have been enabled.

The output can contain information such as:

- Velocity
- Pressure
- Flow information
- Simulation data
- Performance information
- Timing information

The exact output files depend on the simulation configuration.

### Output Frequency

The output frequency determines how often data is written.

For example:

```xml
<outputfrequency>100</outputfrequency>
```

means that output is generated at the configured frequency associated with the simulation settings.

Writing data more frequently produces more detailed output but can increase:

- File size
- Storage requirements
- I/O time
- Overall simulation runtime

Therefore, output frequency should be chosen carefully.

---

# 15. How to Interpret Simulation Results

When analysing HemeLB results, there are two main questions to ask:

### 15.1 What Happened to the Fluid?

Scientific results can be used to investigate:

- Velocity
- Pressure
- Flow patterns
- Changes in velocity
- Changes in pressure
- Flow through different regions of the geometry

For example, velocity data can be used to identify regions where blood is moving faster or slower.

Pressure data can be used to investigate how pressure changes throughout the geometry.

### 15.2 How Well Did the Computer Run the Simulation?

HPC performance results can be used to investigate:

- Runtime
- MLUPS
- Scaling efficiency
- Load balance
- Memory performance
- MPI communication
- I/O performance

These measurements are important when HemeLB is being used as an HPC benchmark.

---

# 16. Visualising HemeLB Output

Simulation output can be processed and visualised to make the results easier to understand.

A visualisation workflow can be represented as:

```text
HemeLB Simulation
       ↓
Output Files
       ↓
Extract Required Data
       ↓
Visualisation Software
       ↓
Velocity / Pressure / Flow Visualisation
```

Visualisation can help show:

- Velocity distribution
- Pressure distribution
- Flow through vessels
- Regions of high or low velocity
- Changes in flow through complex geometries

The visualisation software should be selected based on the output format produced by the specific HemeLB configuration.

When preparing a benchmark, avoid generating unnecessary visualisation data because additional output can increase I/O overhead.

---

# 17. Important Things to Focus on When Running HemeLB

### 17.1 Use Appropriate Domain Partitioning

The blood-vessel geometry is irregular.

A poor partitioning strategy can cause some MPI processes to receive much more work than others.

Good partitioning helps distribute the workload evenly.

### 17.2 Monitor Load Balance

After running a simulation, check the timing and performance information.

If one process takes significantly longer than the others, this may indicate an imbalance in the workload.

Poor load balance can result in other processes waiting for the slower process.

### 17.3 Monitor Memory Performance

HemeLB performs many memory operations.

Therefore, memory bandwidth can have a significant effect on performance.

When comparing HPC systems, consider:

- Memory bandwidth
- Number of memory channels
- Memory architecture
- Available memory capacity

### 17.4 Monitor MPI Communication

As the number of MPI processes increases, communication between processes becomes increasingly important.

Consider:

- Network bandwidth
- Network latency
- Number of compute nodes
- MPI process placement
- Communication overhead

### 17.5 Control Output

Writing large amounts of data can affect benchmark performance.

For pure performance testing:

- Avoid unnecessary extractors.
- Avoid unnecessarily frequent output.
- Use an appropriate output frequency.
- Make sure sufficient storage is available.

This helps prevent I/O from dominating the benchmark results.

---

# 18. Benchmarking HemeLB

When using HemeLB as an HPC benchmark, keep the simulation problem consistent when comparing systems.

For example, when comparing two systems:

```text
System A
   ↓
Same geometry
Same input parameters
Same number of timesteps
Same output configuration
   ↓
Performance Result
```

and:

```text
System B
   ↓
Same geometry
Same input parameters
Same number of timesteps
Same output configuration
   ↓
Performance Result
```

This allows the performance results to be compared fairly.

Important measurements include:

| Metric | What it tells you |
|---|---|
| **Runtime** | How long the simulation takes. |
| **MLUPS** | How many million lattice updates are performed per second. |
| **Scaling efficiency** | How effectively performance improves when more processors are used. |
| **Load balance** | How evenly work is distributed between processes. |
| **Memory performance** | How efficiently the system moves data through memory. |
| **Communication performance** | How efficiently MPI processes communicate. |
| **I/O performance** | How efficiently simulation data is written to storage. |

---

# 19. Common Parameters to Check Before Running

Before starting a simulation, check:

### Geometry

```text
Is the correct .gmy file being used?
```

### Velocity

```text
Is the velocity profile appropriate for the experiment?
```

### Units

```text
Are the values using the required SI units?
```

### Timesteps

```text
Is the number of timesteps sufficient for the required simulation?
```

### Output Frequency

```text
Is data being written too frequently?
```

### Extractors

```text
Are the required quantities being extracted?
```

### MPI Processes

```text
Is the number of MPI processes appropriate for the available hardware?
```

### Storage

```text
Is enough storage available for the expected output?
```

---

# 20. HemeLB Files at a Glance

| File | Main Purpose | Normally Modified? |
|---|---|---|
| `input.xml` | Configures the simulation. | Yes |
| `velocity.dat` | Provides velocity profile data when used. | Yes |
| `geometry.gmy` | Contains the computational geometry. | No, normally generated instead |
| Output files | Contain simulation results. | No, generated by HemeLB |
| Log files | Contain execution and performance information. | No, generated by HemeLB |

---

# 21. Summary

HemeLB is an HPC application designed to simulate blood flow through complex and patient-specific vascular geometries using the **Lattice Boltzmann Method**.

Its main strengths include:

- Efficient handling of complex and sparse geometries
- Parallel execution using MPI
- Large-scale HPC capability
- Patient-specific blood-flow simulation
- Use as an HPC performance benchmark

The most important areas to understand when working with HemeLB are:

1. **Dependencies** – The libraries and software required to build and run HemeLB.
2. **MPI** – Used to distribute the simulation across multiple processes and compute nodes.
3. **`input.xml`** – Controls the simulation configuration.
4. **`.dat` files** – Can contain velocity profile information used by the simulation.
5. **`.gmy` files** – Contain the computational geometry.
6. **Output files** – Contain the scientific and performance information produced by the simulation.
7. **Visualisation** – Allows simulation results such as velocity and pressure to be examined.
8. **MLUPS and runtime** – Important measurements of computational performance.
9. **Scaling efficiency** – Shows how effectively HemeLB uses additional processors.
10. **Load balance** – Shows how evenly computational work is distributed.
11. **Memory and communication performance** – Important factors affecting HPC performance.
12. **I/O** – Excessive output can reduce benchmark performance.

The overall process is:

```text
Install dependencies
       ↓
Build HemeLB
       ↓
Prepare geometry
       ↓
Prepare input.xml
       ↓
Prepare required .dat data
       ↓
Run with MPI
       ↓
Generate output
       ↓
Analyse results
       ↓
Visualise results
       ↓
Evaluate HPC performance
```
