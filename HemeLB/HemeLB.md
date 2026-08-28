# HemeLB
## 1. What is HemeLB?
HemeLB is an open-source software that was designed to simulate blood flow through complex blood vessels using powerful supercomputers. It starts by modeling how blood moves through the human body. Blood vessels have a lot of empty space. HemeLB is specifically built to ignore those empty areas and focus only on the blood fluid.
## 2. What scientific/engineering problem does it solve?
HemeLB is used to bridge a big gap that is withing high-performance computer engineering and clinical medicine.

In the past, neurosurgeons and cardiologist could view static 3D shapes from scans but they could not look inside a patient's body to see how blood was moving in real time. With HemeLB, it solves the problem by calculating the precise patient-specific fluid physics inside the human anatomy within a fast timeframe.
This was an breakthrough due to the following reasons:
Simulating bllod flow accurately requires dividing a medical scan into millions or billions of microscopic 3D pixels. Simulating a single human's heartbeat requires massive computational capacity
- **The Scaling Problem**Most software would slow down or crash when it attempted to split a single calculation across thousands of computer chips due to the lack of communication
- **The Engineering Breakthrough** Since HemeLB utilizes collision-and-streaming fluid steps, it showed strong scaling capabilities. This allows the simulator to be broken up and solved across lots of CPU cores without choking due to data transfer bottlenecks. 
## 3. Why is the application/benchmark used?
In HPC, HememeLB is used as a production application and a supercomputing benchmark.

In supercomputing "string scaling" measures how fast a computer can solve a fixed-size problem when you add more processors.

### 1. To Test "Strong Scaling" to push Limits

- **The Benchmark Value** The Lattice Boltzmann Method relies on local physics calculations, which is based on collisions happening inside localized pixel clusters.
- **The Test* Computer scientists use this benchmark to see at what point a supercomputer's network will choke on communication. They test using up to 300 000+ CPU cores at once to stress-test high-speed interconnect networks.

 ### 2. To Optimise Hardware and Software Customisation
  When a new supercomputer is built, the default settings are rarely optimal

  - **The Benchmark Value**  Running the HemeLB Carpentries Benchmark Suite generates an automated report.txt file outlining the precise time spent on execution vs. data writing.
  - **The Test** Engineers use this data to identify hidden system bottlenecks, balance the workload across compute nodes that do not have the same hardware specifications, and figure out the exact optimal compiler options for that specific machine.

## 4. What calculations are performed?
HemeLB does not treat blood as a smooth, continuous liquid, but billions of tiny virtual fluid particles moving through a grid of microscopic 3D pixels.

For a single heartbeat to be simulated, the supercomputer needs to run two basic calculations repeatedly for millions of times for every single voxel.

### 1. Local Math

In this process, the supercomputer calculates what happens when the fluid particles crash into each other inside a single pixel.

- **The Calculation**. The software calculates the speed, direction and pressure of the particles in that specific spot. This math is easy for supercomputers because the math is local. It doesn't need to ask the processor for information to solve it.

### 2. Moving Math

After the particles collide with each, the particles need to move to the next pixel.

- **The Calculation** The software calculates the neighboring pixels the particles should move into based on the direction they are traveling
This calculation may be hard for supercomputers because if a particle needs to move to a pixel that is being handled by a different computer chip, the supercomputer has to physically send the data across its internal network wires
## 5. What metrics are important?
## 6. What makes the benchmark useful, and what are its limitations?
## 7. What to focus on when running the benchmark/application?
## 8. Where is the benchmark used in the real world?
## 9. Advantages and Limitations
