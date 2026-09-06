# PHYSICAL-DESIGN
# PicoRV32A – RTL to GDSII Using OpenLane

A practical VLSI implementation project demonstrating how the **PicoRV32A RISC-V processor** can be taken from Verilog RTL through synthesis, physical design, timing analysis, verification, and final GDSII generation using **OpenLane** and the **Sky130 PDK**.

The objective is to understand the complete digital ASIC implementation flow and the relationship between logical design, physical implementation, timing closure, and layout verification.

---

## 1. Project Overview

ASIC physical design transforms an RTL description into a technology-specific physical layout. Each stage progressively adds implementation details while maintaining the intended functionality of the design.

The design used in this project is **PicoRV32A**, a compact RISC-V processor core implemented in Verilog.

The complete implementation flow is:

```text
RTL Design
    ↓
Synthesis
    ↓
Floorplanning
    ↓
Power Planning
    ↓
Placement
    ↓
Clock Tree Synthesis
    ↓
Routing
    ↓
Static Timing Analysis
    ↓
Physical Verification
    ↓
GDSII

---

## 2. Design Used – PicoRV32A

PicoRV32A is a small RISC-V processor core designed for resource-efficient hardware implementations.

Its Verilog RTL describes the datapath, control logic, registers, instruction handling, and interfaces required for processor operation.

The design includes:

* Arithmetic and logic operations
* Registers
* Flip-flops
* Multiplexers
* Control logic
* Instruction decoding
* Data-path logic
* Memory interface logic

The RTL is the starting representation of the design and is supplied to the synthesis stage for conversion into technology-mapped logic.

---

## 3. Technology – SKY130 PDK

A **Process Design Kit (PDK)** provides the technology files, standard-cell information, physical rules, and timing data required by EDA tools.

The Sky130 PDK provides information such as:

* Standard-cell libraries
* Technology layers
* Design rules
* Timing models
* Physical abstracts
* Cell dimensions
* Process information
* Layout rules

This project uses the **SkyWater SKY130** technology.

The PDK allows the physical-design tools to map the RTL design to technology-specific standard cells and metal layers.

---

## 4. OpenLane Flow

**OpenLane** is an open-source RTL-to-GDSII flow that coordinates synthesis, floorplanning, placement, clock-tree synthesis, routing, and signoff-oriented checks.

It integrates several open-source EDA tools and automates different stages of ASIC implementation.

The general OpenLane flow is:

```text
RTL
 ↓
Logic Synthesis
 ↓
Floorplanning
 ↓
Placement
 ↓
CTS
 ↓
Routing
 ↓
Signoff
 ↓
GDSII
```

Using OpenLane makes it easier to reproduce the implementation flow through configuration files and automated tool execution.

---

## 5. RTL Design

RTL stands for **Register Transfer Level**.

RTL describes how data moves between registers and how combinational and sequential logic operates.

For the PicoRV32A project, the Verilog RTL acts as the starting point of the physical-design flow.

```text
Verilog RTL
     ↓
Synthesis
     ↓
Gate-Level Netlist
```

The RTL contains the logical description of the processor before technology mapping.

---

## 6. Logic Synthesis

Logic synthesis converts the Verilog RTL into a **technology-mapped gate-level netlist** while applying the constraints and libraries associated with the target technology.

During synthesis, the RTL is analyzed and mapped to cells available in the Sky130 standard-cell library.

```text
RTL
 ↓
Logic Synthesis
 ↓
Technology-Mapped Netlist
```

The resulting netlist can contain cells such as:

* AND gates
* OR gates
* NAND gates
* NOR gates
* Inverters
* Buffers
* Multiplexers
* Flip-flops
* Logic gates

---

## 7. Gate-Level Netlist

A netlist represents the logical structure of the synthesized circuit.

It describes:

* Standard cells used in the design
* Connections between cells
* Input and output ports
* Sequential elements
* Combinational logic

The RTL is therefore transformed into a technology-mapped representation.

```text
RTL
 ↓
Synthesis
 ↓
Standard Cells
 ↓
Cell Connections
 ↓
Gate-Level Netlist
```

The synthesized netlist becomes the logical input for floorplanning and subsequent physical implementation stages.

---

## 8. Floorplanning

Floorplanning establishes the physical boundaries and initial organization of the chip core before detailed placement and routing.

It determines the basic physical dimensions and organization of the design.

The floorplan defines:

* Core area
* Die area
* Standard-cell region
* I/O locations
* Placement boundaries
* Initial physical organization

A suitable floorplan is important because it affects placement, routing congestion, timing, and overall chip area.

---

## 9. Power Distribution Network

The Power Distribution Network (PDN) distributes VDD and VSS across the core so that standard cells receive reliable power connections.

The power network consists of structures such as:

* VDD rails
* VSS rails
* Power rings
* Power straps
* Standard-cell power connections

The objective is to distribute power reliably across the complete core area.

```text
VDD
 │
 ├── Power Ring
 │
 ├── Power Straps
 │
 └── Standard Cell Rails

VSS
 │
 ├── Power Ring
 │
 ├── Power Straps
 │
 └── Standard Cell Rails
```

---

## 10. Placement

Placement assigns legal physical locations to the standard cells while balancing timing, congestion, density, and interconnect length.

The placement process generally consists of:

### Global Placement

Global placement determines approximate locations for the cells while considering factors such as:

* Wire length
* Timing
* Cell density
* Congestion

### Detailed Placement

Detailed placement moves the cells into legal locations according to the placement rules.

A good placement helps to:

* Reduce routing congestion
* Reduce interconnect length
* Improve timing
* Simplify routing
* Improve area utilization

---

## 11. Clock Tree Synthesis

**Clock Tree Synthesis (CTS)** builds the clock distribution network between the clock source and sequential elements.

A clock signal must reach the sequential elements of the design with controlled delay.

CTS inserts clock buffers and builds a suitable clock network.

```text
             Clock
               |
            Buffer
           /      \
       Buffer    Buffer
       /   \      /   \
      FF1  FF2   FF3  FF4
```

The main objectives of CTS include:

* Reducing clock skew
* Controlling clock latency
* Providing sufficient clock drive
* Ensuring proper clock distribution

Clock-tree quality has a direct effect on timing performance.

---

## 12. Routing

Routing creates the physical metal and via connections between cells while following the routing resources and technology design rules.

The routing process determines the physical paths for signals, clocks, and other connections.

The major routing stages are:

### Global Routing

Global routing determines the approximate routing paths and available routing resources.

### Detailed Routing

Detailed routing creates the actual metal-layer connections while following the technology design rules.

```text
Placed Cells
     ↓
Global Routing
     ↓
Detailed Routing
     ↓
Connected Physical Design
```

---

## 13. Static Timing Analysis

**Static Timing Analysis (STA)** evaluates timing paths in the implemented design to determine whether setup, hold, and other timing requirements are satisfied.

STA analyzes timing paths without requiring functional simulation of every possible input combination.

Important timing parameters include:

* Clock period
* Cell delay
* Net delay
* Setup time
* Hold time
* Clock skew
* Slew
* Slack

### Slack

Slack indicates the timing margin of a path.

```text
Positive Slack
      ↓
Timing Requirement Satisfied

Negative Slack
      ↓
Timing Violation
```

OpenSTA is used for static timing analysis in the open-source flow.

---

## 14. Physical Verification

After routing, the physical database is checked to ensure that the layout follows manufacturing rules and represents the intended circuit.

Important physical verification checks include:

### DRC

**Design Rule Check (DRC)** verifies that the layout follows the manufacturing rules defined by the technology.

It checks issues related to:

* Minimum spacing
* Minimum width
* Layer rules
* Via rules
* Metal geometry

### LVS

**Layout Versus Schematic (LVS)** verifies whether the physical layout corresponds to the intended circuit/netlist.

The goal is to ensure that the implemented layout represents the correct circuit.

---

## 15. Signoff and GDSII

Signoff consolidates the final timing and physical checks before the layout is delivered as a GDSII database.

The design is evaluated for:

* Timing
* Routing correctness
* Design-rule compliance
* Layout consistency
* Power connectivity
* Netlist consistency

A successful implementation can produce the final **GDSII** layout.

```text
Physical Design
      ↓
Verification
      ↓
Signoff
      ↓
GDSII
```

---

## 16. OpenLane Execution

OpenLane can be executed using its flow scripts and configuration files.

For an interactive flow, the command can be started using:

```bash
./flow.tcl -interactive
```

Inside the interactive environment, the required OpenLane flow commands can be executed according to the installed OpenLane version.

For example, a design can be prepared using:

```tcl
prep -design picorv32a
```

The synthesis stage can then be started using the command supported by the installed OpenLane version.

> **Note:** OpenLane commands and configuration syntax may differ between OpenLane releases. The commands should therefore match the version installed in the VSDIAT environment.

---

## 17. Project Directory Structure

A typical OpenLane project can contain a structure similar to:

```text
openlane/
│
├── designs/
│   └── picorv32a/
│       ├── config.tcl
│       ├── src/
│       │   └── *.v
│       └── runs/
│
├── flow.tcl
├── scripts/
└── configuration files
```

The design source files contain the RTL, while the configuration file specifies important implementation parameters.

The `runs` directory contains the results generated during the physical-design flow.

---

## 18. Design Metrics

The synthesis and implementation flow generates different design statistics.

Important parameters include:

| Parameter   | Description                               |
| ----------- | ----------------------------------------- |
| Total Cells | Number of cells in the synthesized design |
| Flip-Flops  | Number of sequential storage elements     |
| Wires       | Number of logical connections             |
| Wire Bits   | Number of bits represented by the wires   |
| Area        | Physical area occupied by the design      |
| Utilization | Percentage of core area occupied by cells |
| Timing      | Timing performance of the design          |

---

## 19. Flip-Flop Ratio

The flip-flop ratio represents the percentage of flip-flops relative to the total number of cells.

### Formula

```text
Flip-Flop Ratio =
( Number of Flip-Flops / Total Number of Cells ) × 100
```

The ratio should be calculated from the actual synthesis report generated for the selected run. Using the report values ensures that the README reflects the current implementation rather than a fixed example.

---

## 20. Complete RTL-to-GDSII Flow

The complete flow followed in this project is:

```text
                    RTL
                     ↓
                 Synthesis
                     ↓
              Gate-Level Netlist
                     ↓
               Floorplanning
                     ↓
             Power Distribution
                     ↓
                 Placement
                     ↓
          Clock Tree Synthesis
                     ↓
                  Routing
                     ↓
            Static Timing Analysis
                     ↓
            Physical Verification
                     ↓
                  Signoff
                     ↓
                   GDSII
```

---

## 21. Tools and Technologies

| Tool / Technology | Purpose                           |
| ----------------- | --------------------------------- |
| PicoRV32A         | RISC-V processor RTL design       |
| OpenLane          | RTL-to-GDSII physical-design flow |
| Yosys             | RTL synthesis                     |
| OpenROAD          | Physical implementation           |
| OpenSTA           | Static timing analysis            |
| Sky130 PDK        | Semiconductor technology          |
| Magic             | Layout and physical verification  |
| Netgen            | LVS verification                  |
| GDSII             | Final physical layout format      |

---

## 22. Key Learning Outcomes

This project provides practical understanding of the ASIC physical-design process.

The major concepts learned are:

* RTL design
* RISC-V processor architecture
* Process Design Kit
* Logic synthesis
* Gate-level netlist
* Standard cells
* Floorplanning
* Power distribution
* Placement
* Clock Tree Synthesis
* Global routing
* Detailed routing
* Static Timing Analysis
* Setup and hold timing
* Slack
* DRC
* LVS
* Signoff
* GDSII generation

---

## 23. Project Outcome

The PicoRV32A RTL design is taken through the major stages of the ASIC implementation flow using open-source EDA tools.

The project demonstrates how:

```text
Verilog RTL
     ↓
Synthesized Netlist
     ↓
Physical Implementation
     ↓
Timing Analysis
     ↓
Physical Verification
     ↓
Final Layout
```

The final objective is to understand how a digital processor described using RTL can be transformed into a physical chip layout using the **Sky130 technology and OpenLane flow**.

---

## 24. Conclusion

This project demonstrates the complete **RTL-to-GDSII ASIC physical-design flow** using PicoRV32A.

The implementation begins with Verilog RTL and progresses through synthesis, floorplanning, power planning, placement, CTS, routing, timing analysis, and physical verification.

The project provides practical exposure to open-source VLSI tools and helps establish an understanding of the complete digital ASIC implementation process.

```text
RTL
 ↓
Synthesis
 ↓
Netlist
 ↓
Floorplan
 ↓
Power Planning
 ↓
Placement
 ↓
CTS
 ↓
Routing
 ↓
STA
 ↓
Physical Verification
 ↓
Signoff
 ↓
GDSII
```

### Final Takeaway

**RTL → Netlist → Physical Design → Verification → GDSII**

This represents the fundamental flow used to transform a digital design into a manufacturable physical layout.

```
```

