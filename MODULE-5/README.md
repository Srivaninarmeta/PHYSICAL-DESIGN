# Routing, Power Distribution & TritonRoute



</p>

<p align="center">
  <b>Routing • Design Rules • Power Distribution • Global Routing • Detailed Routing • TritonRoute</b>
</p>

<p align="center">
  Understanding the final physical-design stages of the SKY130 RTL-to-GDSII flow.
</p>

---

## 📚 Table of Contents

1. [🛣️ Routing in Physical Design](#1--routing-in-physical-design)
2. [🧩 Routing Challenges](#2--routing-challenges)
3. [🧭 Maze Routing](#3--maze-routing)
4. [🔍 Lee's Algorithm](#4--lees-algorithm)
5. [📐 Design Rule Check](#5--design-rule-check)
6. [⚡ Power Distribution Network](#6--power-distribution-network)
7. [🔋 Power Straps](#7--power-straps)
8. [🌐 Global Routing](#8--global-routing)
9. [🎯 Detailed Routing](#9--detailed-routing)
10. [🔄 Global vs Detailed Routing](#10--global-vs-detailed-routing)
11. [🛠️ TritonRoute](#11--tritonroute)
12. [🚀 TritonRoute Features](#12--tritonroute-features)
13. [🧠 Routing Algorithms & Topology](#13--routing-algorithms--topology)
14. [💻 Important Routing Commands](#14--important-routing-commands)
15. [🎯 Module 5 Key Takeaways](#15--module-5-key-takeaways)

---

# 1. 🛣️ Routing in Physical Design

Routing is one of the final and most important stages of physical design.

After placement and Clock Tree Synthesis, the design contains:

- Standard cells
- Macros
- Pins
- Clock network
- Power connections

However, these components still need physical metal connections.

Routing creates the required interconnections between the placed components.

### 🔹 Basic Flow

```text
Placement
    │
    ▼
Clock Tree Synthesis
    │
    ▼
Routing
    │
    ▼
Physical Verification
    │
    ▼
Final Layout
```

### 🔹 Routing connects

```text
Cell A ─────────────── Cell B
       Metal Interconnect
```

The router determines suitable paths through the available routing resources.

### 💡 Key Idea

> **Routing converts logical connections into physical metal interconnections.**

---

# 2. 🧩 Routing Challenges

Routing is not simply connecting two points with a straight line.

The router must consider several physical constraints.

### 🔹 Important Routing Constraints

- Available routing tracks
- Metal layers
- Existing wires
- Cell locations
- Pin locations
- Design rules
- Congestion
- Routing resources

A simplified routing problem can be represented as:

```text
             Destination
                  ●
                  │
          ┌───────┼────────┐
          │       │        │
          │   Obstacles    │
          │    █████       │
          │    █████       │
          │                │
          └────────────────┘
                  │
                  ●
               Source
```

The router must find a legal path while avoiding obstacles and respecting physical design rules.

### 💡 Key Idea

> **A good routing solution must satisfy connectivity as well as physical design constraints.**

---

# 3. 🧭 Maze Routing

Maze routing is a routing technique used to find a path between a source and destination while avoiding obstacles.

The routing area can be represented as a grid.

```text
┌───┬───┬───┬───┬───┐
│ S │   │   │   │   │
├───┼───┼───┼───┼───┤
│   │ █ │ █ │   │   │
├───┼───┼───┼───┼───┤
│   │   │ █ │   │   │
├───┼───┼───┼───┼───┤
│   │   │   │   │ D │
└───┴───┴───┴───┴───┘

S = Source
D = Destination
█ = Obstacle
```

The router searches through available locations until a connection is found.

### 🔹 General Process

```text
Source
  │
  ▼
Explore Available Locations
  │
  ▼
Avoid Obstacles
  │
  ▼
Reach Destination
  │
  ▼
Create Route
```

Maze routing is useful for understanding how routing algorithms search for paths through a constrained physical space.

---

# 4. 🔍 Lee's Algorithm

**Lee's algorithm** is a classical maze-routing algorithm.

It uses a grid-based search to find a path between two points.

### 🔹 Basic Concept

```text
Start
  │
  ▼
Assign Distance
  │
  ▼
Expand to Neighbouring Cells
  │
  ▼
Continue Expansion
  │
  ▼
Reach Destination
  │
  ▼
Trace Back the Path
```

The search expands through available grid locations.

A simplified example:

```text
┌───┬───┬───┬───┐
│ 0 │ 1 │ 2 │ 3 │
├───┼───┼───┼───┤
│ 1 │ █ │ 3 │ 4 │
├───┼───┼───┼───┤
│ 2 │ 3 │ 4 │ 5 │
├───┼───┼───┼───┤
│ 3 │ 4 │ 5 │ D │
└───┴───┴───┴───┘
```

### 🔹 Important Characteristics

- Grid-based routing
- Expands through neighbouring locations
- Avoids blocked locations
- Finds a valid path
- Can be used as a basis for maze-routing concepts

### 💡 Key Idea

> **Lee's algorithm searches the routing space systematically to find a path between two points.**

---

# 5. 📐 Design Rule Check

**Design Rule Check (DRC)** verifies whether the physical layout follows the manufacturing rules of the technology.

For the SKY130 technology, the layout must satisfy the required design rules.

### 🔹 Examples of Physical Rules

Design rules can relate to:

- Metal width
- Metal spacing
- Via placement
- Layer interactions
- Minimum dimensions
- Manufacturing constraints

A simplified example:

```text
Metal A
════════════════

<-- Required Spacing -->

Metal B
════════════════
```

If the spacing is too small:

```text
❌ DRC Violation
```

If the spacing satisfies the required rule:

```text
✅ DRC Clean
```

### 🔹 Why DRC is Important

DRC helps ensure that the generated physical layout is suitable according to the technology's manufacturing constraints.

### 💡 Key Idea

> **DRC checks whether the physical layout follows the technology design rules.**

---

# 6. ⚡ Power Distribution Network

A digital circuit contains a large number of cells that require power.

Therefore, power must be distributed throughout the chip using a dedicated power network.

This is called the **Power Distribution Network (PDN)**.

### 🔹 Simplified PDN

```text
                 VDD
                  │
        ══════════╪══════════
                  │
        ║         │         ║
        ║         │         ║
        ║       Cells       ║
        ║         │         ║
        ║         │         ║
        ══════════╪══════════
                  │
                 VSS
```

The power network distributes:

- VDD
- VSS / GND

throughout the design.

### 🔹 Purpose

The power network should provide reliable power connections to the cells across the chip.

### 💡 Key Idea

> **PDN provides the physical power and ground distribution required by the design.**

---

# 7. 🔋 Power Straps

Power straps are wide metal structures used to distribute power across the chip.

A simplified representation is:

```text
      VDD STRAP
════════════════════════════

│       │       │       │
│       │       │       │
│       │       │       │
│       │       │       │

════════════════════════════
      VSS STRAP
```

Power straps are part of the overall power distribution network.

### 🔹 Why Power Straps are Used

They help:

- Distribute power across the layout
- Connect different regions of the chip
- Provide low-resistance power paths
- Support the power requirements of standard cells

### 🔹 Power Distribution

```text
Power Source
     │
     ▼
Power Grid
     │
     ▼
Power Straps
     │
     ▼
Standard Cells
```

### 💡 Key Idea

> **Power straps form an important part of the physical power network across the chip.**

---

# 8. 🌐 Global Routing

Global routing is the stage where the router determines the **general paths** that connections should follow.

It does not necessarily determine every exact wire segment.

Instead, it plans how the connections should move through the available routing resources.

### 🔹 Concept

```text
Source
  │
  ▼
┌───────────────┐
│ Routing Area  │
│               │
│   ────────┐   │
│           │   │
│           └───┼──► Destination
│               │
└───────────────┘
```

### 🔹 Global Routing Considers

- Routing resources
- Congestion
- Routing regions
- Connectivity
- General path selection

### 💡 Key Idea

> **Global routing creates a high-level routing plan before exact physical wires are created.**

---

# 9. 🎯 Detailed Routing

Detailed routing follows the global-routing plan and determines the actual physical interconnects.

It deals with the exact routing resources available in the layout.

### 🔹 Detailed Routing

```text
Global Route
     │
     ▼
Exact Routing Tracks
     │
     ▼
Metal Segments
     │
     ▼
Vias
     │
     ▼
Physical Connection
```

The detailed router must obey the technology design rules while creating the final connections.

### 🔹 Detailed Routing Deals With

- Exact routing tracks
- Metal layers
- Via locations
- Wire segments
- Design-rule constraints
- Connectivity

### 💡 Key Idea

> **Detailed routing converts the global routing plan into actual physical wires and vias.**

---

# 10. 🔄 Global vs Detailed Routing

Global routing and detailed routing perform different tasks.

| Feature | Global Routing | Detailed Routing |
|:---|:---|:---|
| Purpose | Planning | Exact implementation |
| Path | General | Exact |
| Tracks | Approximate resources | Specific tracks |
| Wires | Not fully finalized | Physical wires |
| Vias | Not fully finalized | Actual vias |
| DRC | Preliminary consideration | Strictly checked |
| Output | Routing plan | Physical routing |

### 🔹 Overall Routing Flow

```text
                 Routing
                    │
           ┌────────┴────────┐
           ▼                 ▼
    Global Routing      Detailed Routing
           │                 │
           ▼                 ▼
   Routing Planning     Exact Wires
           │                 │
           └────────┬────────┘
                    ▼
             Final Layout
```

### 💡 Key Idea

> **Global routing plans the route, while detailed routing implements the exact physical route.**

---

# 11. 🛠️ TritonRoute

**TritonRoute** is a detailed-routing component used in the OpenROAD physical-design flow.

Its role is to create detailed physical connections after earlier physical-design stages have been completed.

### 🔹 Simplified Flow

```text
Placement
    │
    ▼
CTS
    │
    ▼
Global Routing
    │
    ▼
TritonRoute
    │
    ▼
Detailed Routing
    │
    ▼
Physical Verification
```

### 🔹 TritonRoute Works With

- Routing information
- Physical layout
- Routing constraints
- Design rules
- Routing resources

The detailed-routing stage produces the physical metal connections required to connect the design.

---

# 12. 🚀 TritonRoute Features

The module introduces different TritonRoute features and their role in detailed routing.

The important idea is that detailed routing must handle the physical constraints of the chip while producing legal connections.

### 🔹 Main Routing Requirements

```text
Connectivity
     +
Routing Resources
     +
Design Rules
     +
Physical Constraints
     ↓
Legal Detailed Routing
```

### 🔹 Routing Must Consider

- Metal layers
- Routing tracks
- Via locations
- Existing wires
- Obstacles
- Design rules
- Connectivity

### 💡 Key Idea

> **TritonRoute performs detailed routing while working within the physical and technology constraints of the design.**

---

# 13. 🧠 Routing Algorithms & Topology

Routing requires algorithms to determine suitable paths and topologies between connected pins.

A routing topology describes the structure used to connect multiple points.

### 🔹 Simple Topology

```text
             Pin A
               │
               │
Pin B ─────────┼──────── Pin C
               │
               │
             Pin D
```

The router needs to determine an efficient physical structure for the required connections.

### 🔹 Routing Algorithm

A routing algorithm determines:

```text
Where to route
      ↓
Which direction to take
      ↓
Which routing resources to use
      ↓
How to avoid obstacles
      ↓
How to satisfy design rules
```

### 🔹 Important Routing Goals

A routing solution should aim for:

- Valid connectivity
- Efficient routing
- Reduced congestion
- Design-rule compliance
- Suitable routing topology

### 💡 Key Idea

> **Routing algorithms determine how connections are physically constructed while respecting available resources and constraints.**

---

# 14. 💻 Important Routing Commands

The following commands are useful when working with the OpenLane/OpenROAD physical-design flow.

## 🔹 Run Routing

```tcl
run_routing
```

---

## 🔹 Generate Power Grid

```tcl
run_power_grid_generation
```

---

## 🔹 Run Antenna Check

```tcl
run_antenna_check
```

---

## 🔹 Run Magic DRC

```tcl
run_magic_drc
```

---

## 🔹 Run KLayout DRC

```tcl
run_klayout_drc
```

---

## 🔹 Run LVS

```tcl
run_lvs
```

---

## 🔹 Run Routing After Previous Stages

A simplified physical-design sequence is:

```text
run_synthesis
      ↓
run_floorplan
      ↓
run_placement
      ↓
run_cts
      ↓
run_routing
      ↓
DRC / LVS / Verification
```

### 🔹 Important Outputs to Observe

After routing and verification, important outputs include:

| Output | Purpose |
|:---|:---|
| Routed Layout | Final physical interconnections |
| Routing Report | Routing information |
| DRC Report | Design-rule violations |
| LVS Report | Layout vs schematic/netlist comparison |
| Antenna Report | Antenna-rule information |

---

# 15. 🎯Key Takeaways

Module 5 focuses on the final physical-design stages where logical connections are converted into physical interconnections.

### 🧠 Major Concepts Learned

- Routing creates physical metal connections between design elements.
- Routing must work within physical and technology constraints.
- Maze routing searches for paths through a constrained routing space.
- Lee's algorithm is a classical grid-based maze-routing algorithm.
- Design Rule Check verifies physical layout against technology rules.
- The Power Distribution Network distributes power and ground throughout the design.
- Power straps form an important part of the chip power network.
- Global routing creates a high-level routing plan.
- Detailed routing creates the exact physical wires and vias.
- Global routing and detailed routing perform different functions.
- TritonRoute is used for detailed routing in the OpenROAD flow.
- Routing algorithms determine suitable physical paths and topologies.
- Final routing must satisfy connectivity and physical constraints.
- DRC, LVS and antenna checks are important parts of physical verification.

---

# 🔄 Complete  Flow

```text
                  Placement
                      │
                      ▼
             Clock Tree Synthesis
                      │
                      ▼
             Power Distribution
                      │
                      ▼
               Global Routing
                      │
                      ▼
              Detailed Routing
                      │
                      ▼
                TritonRoute
                      │
                      ▼
              Routed Layout
                      │
            ┌─────────┼─────────┐
            ▼         ▼         ▼
           DRC       LVS      Antenna
            │         │         │
            └─────────┼─────────┘
                      ▼
             Physical Verification
                      │
                      ▼
                Final Layout
```

---

# 📊 Routing Flow — Quick Reference

| Stage | Main Purpose |
|:---|:---|
| **Placement** | Places cells physically |
| **CTS** | Builds the clock network |
| **PDN** | Distributes power and ground |
| **Global Routing** | Plans routing paths |
| **Detailed Routing** | Creates exact wires and vias |
| **TritonRoute** | Performs detailed routing |
| **DRC** | Checks design rules |
| **LVS** | Checks layout/netlist consistency |
| **Antenna Check** | Checks antenna-related issues |

---

# 🛠️ Tools Used

| Tool | Purpose |
|:---|:---|
| **OpenLane** | RTL-to-GDSII flow |
| **OpenROAD** | Physical design |
| **TritonRoute** | Detailed routing |
| **Magic** | Layout inspection and verification |
| **KLayout** | Layout viewing and DRC |
| **Yosys** | RTL synthesis |
| **OpenSTA** | Static Timing Analysis |
| **SKY130 PDK** | Technology/process information |
| **Linux** | VLSI design environment |

---

# 📌 Final Learning Flow

```text
             PHYSICAL DESIGN
                    │
                    ▼
                Placement
                    │
                    ▼
                   CTS
                    │
                    ▼
             Power Distribution
                    │
                    ▼
             Global Routing
                    │
                    ▼
            Detailed Routing
                    │
                    ▼
              TritonRoute
                    │
                    ▼
             Routed Layout
                    │
                    ▼
          Physical Verification
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
         DRC       LVS      Antenna
          │         │         │
          └─────────┼─────────┘
                    ▼
               Final Design
```

---

# 📝 Summary

The major learning progression of Module 5 was:

```text
Routing Concepts
       ↓
Maze Routing
       ↓
Lee's Algorithm
       ↓
Design Rule Check
       ↓
Power Distribution
       ↓
Power Straps
       ↓
Global Routing
       ↓
Detailed Routing
       ↓
Global vs Detailed Routing
       ↓
TritonRoute
       ↓
TritonRoute Features
       ↓
Routing Algorithms
       ↓
Routing Topology
       ↓
Physical Verification
       ↓
Final Layout
```

This represents the transition from **placed physical design to routed physical design**.

The major idea is:

```text
Logical Connectivity
        ↓
Physical Routing Plan
        ↓
Exact Metal Connections
        ↓
Physical Verification
        ↓
Final Layout
```

---

# 🏁 Conclusion
This  covers the routing and final physical-design concepts required to transform the placed design into a physically connected layout.

The learning starts with routing concepts and maze-routing algorithms and progresses through power distribution, global routing, detailed routing and TritonRoute.

Finally, physical verification techniques such as DRC, LVS and antenna checking help verify the generated layout.
