# Floorplanning and Placement – PicoRV32A

## 1. Introduction

Floorplanning and placement are key stages in the **ASIC physical design flow**. After synthesis, the gate-level netlist must be transformed into a physical layout by defining the die and core dimensions, arranging I/O pins, planning power distribution, and determining where standard cells and fixed blocks will be placed.

The objective is to obtain a physically organized design that meets **area, timing, power, and routing requirements** while providing sufficient resources for later implementation stages.

### Physical Design Flow

```text
Synthesized Netlist
        ↓
   Floorplanning
        ↓
   Power Planning
        ↓
   Pin Placement
        ↓
     Placement
        ↓
Placement Optimization
        ↓
       CTS
        ↓
     Routing
        ↓
Timing Analysis

```

---

## 2. Core and Die

### Core

The **core** is the main internal region of the die where standard cells and other logic elements are placed and connected.

### Die

The **die** is the complete physical silicon area of the chip. It includes the core and the surrounding space needed for I/O, power distribution, routing, and other physical-design requirements.

```text
+--------------------------------+
|              DIE               |
|                                |
|      +------------------+      |
|      |                  |      |
|      |       CORE       |      |
|      |                  |      |
|      +------------------+      |
|                                |
+--------------------------------+

```

The final physical design is eventually fabricated on a silicon wafer.

---

## 3. Aspect Ratio and Utilization

### Aspect Ratio

**Aspect ratio** describes the geometric shape of the core or die by comparing its height with its width.

```text
Aspect Ratio = Height / Width

```

For example:

```text
Aspect Ratio = 1

```

indicates a square-shaped core.

An aspect ratio greater than or less than 1 results in a rectangular shape.

### Utilization

**Utilization** indicates how much of the available core area is occupied by placed cells.

```text
Utilization (%) =
(Cell Area / Core Area) × 100

```

Higher utilization can reduce the required physical area, but excessive utilization leaves less routing space, which can increase **congestion** and make timing closure more difficult.

---

## 4. Floorplanning

**Floorplanning** is one of the first major physical implementation steps after synthesis.

It establishes the overall physical organization of the design and provides the boundaries and regions needed by later stages.

### Major Floorplanning Decisions

- Core dimensions
- Die dimensions
- Aspect ratio
- Core utilization
- I/O locations
- Placement of large blocks
- Power distribution requirements
- Routing resources

A good floorplan can reduce congestion, keep related logic physically closer, shorten interconnects, and improve timing and routability.

---

## 5. Floorplan Configuration in OpenLane

OpenLane uses configuration variables to control important aspects of the floorplanning process.

Important parameters include:

```text
FP_CORE_UTIL
FP_ASPECT_RATIO
FP_SIZING
DIE_AREA
FP_IO_HMETAL
FP_IO_VMETAL
FP_IO_MODE
FP_PDN_VPITCH
FP_PDN_HPITCH

```

### Purpose of Important Variables

| VariablePurpose   |                                            |
| ----------------- | ------------------------------------------ |
| `FP_CORE_UTIL`    | Defines the target core utilization        |
| `FP_ASPECT_RATIO` | Controls the height-to-width ratio         |
| `FP_SIZING`       | Determines the floorplan sizing method     |
| `DIE_AREA`        | Specifies the die dimensions               |
| `FP_IO_HMETAL`    | Defines the horizontal metal layer for I/O |
| `FP_IO_VMETAL`    | Defines the vertical metal layer for I/O   |
| `FP_IO_MODE`      | Controls I/O placement configuration       |
| `FP_PDN_VPITCH`   | Sets vertical power-grid pitch             |
| `FP_PDN_HPITCH`   | Sets horizontal power-grid pitch           |

---

## 6. OpenLane Configuration

The OpenLane configuration file defines the design inputs and physical-design parameters required to run the flow.

Example:

```tcl
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) \
"./designs/picorv32a/src/picorv32a.v"

set ::env(SDC_FILE) \
"./designs/picorv32a/src/picorv32a.sdc"

set ::env(CLOCK_PERIOD) "5.000"

set ::env(CLOCK_PORT) "clk"

set ::env(CLOCK_NET) $::env(CLOCK_PORT)

```

### Configuration Parameters

- `DESIGN_NAME` – specifies the name of the design.
- `VERILOG_FILES` – specifies the RTL source file.
- `SDC_FILE` – specifies the timing constraint file.
- `CLOCK_PERIOD` – defines the clock period.
- `CLOCK_PORT` – identifies the clock input port.
- `CLOCK_NET` – identifies the clock network.

---

## 7. Pre-Placed Cells and Blocks

Some cells or blocks may require fixed physical locations before automated standard-cell placement begins.

These are commonly referred to as **pre-placed cells or blocks**.

Examples include:

- Memory blocks
- Large macros
- Clock-related cells
- Interface blocks
- Other fixed IP blocks

Pre-placement gives the tool fixed physical constraints around which the remaining standard cells can be distributed.

```text
+---------------------------+
| Standard Cells            |
|                           |
|     +-------------+       |
|     | Fixed Block |       |
|     +-------------+       |
|                           |
| Standard Cells            |
+---------------------------+

```

---

## 8. Power Planning

**Power planning** establishes the power distribution network (PDN) that delivers VDD and VSS to the cells throughout the design.

The primary power signals are:

```text
VDD → Power Supply
VSS → Ground

```

The power distribution network generally consists of:

```text
VDD / VSS
    ↓
Power Rings
    ↓
Power Straps
    ↓
Standard Cell Rails
    ↓
Logic Cells

```

A properly designed power network helps control:

- IR voltage drop
- Ground bounce
- Supply noise
- Power integrity problems

It also helps ensure that standard cells receive an adequate and stable power supply during operation.

---

## 9. Decoupling Capacitors

**Decoupling capacitors**, commonly called **decap cells**, help stabilize the local power supply by providing temporary stored charge.

When many cells switch at the same time, the sudden increase in current demand can produce temporary local supply-voltage variations.

A decoupling capacitor stores electrical charge and can provide local current during short switching transients.

```text
       VDD
        |
        +------+
        | Decap|
        | Cell |
        +------+
        |
     Circuit
        |
       VSS

```

### Benefits

- Reduces supply-voltage fluctuations
- Improves power integrity
- Helps reduce local noise
- Provides temporary local charge during switching activity

---

## 10. Pin Placement

**Pin placement** determines where the input and output pins are located around the die.

The location of pins directly affects routing distance, congestion, and timing.

Pins can generally be positioned along:

- Left side
- Right side
- Top side
- Bottom side

Clock-related pins require special attention because the clock network has a significant effect on timing.

Good pin placement helps:

- Reduce routing distance
- Reduce congestion
- Improve timing
- Simplify routing

---

## 11. Placement Blockages

A **placement blockage** is a physical region in which standard-cell placement is restricted or prohibited.

Blockages may be created to protect:

- Fixed macros
- Power structures
- Special routing regions
- Reserved physical areas

Example:

```text
+---------------------------+
| Standard Cells            |
|                           |
|     +-------------+       |
|     |   BLOCKED   |       |
|     |    AREA     |       |
|     +-------------+       |
|                           |
| Standard Cells            |
+---------------------------+

```

Placement blockages provide control over cell distribution and help prevent conflicts with macros, power structures, reserved regions, and routing requirements.

---

## 12. Standard Cell Placement

After floorplanning and power planning, the standard cells are assigned physical locations within the available core area.

The placement process aims to achieve:

- Shorter interconnects
- Lower congestion
- Better timing
- Legal cell positions
- Efficient area utilization
- Better connectivity between related cells

### Placement Flow

```text
Synthesized Netlist
        ↓
Global Placement
        ↓
Legalization
        ↓
Detailed Placement
        ↓
Placement Optimization

```

### Global Placement

Global placement determines approximate locations for cells while optimizing wire length and congestion.

### Legalization

Legalization moves cells into valid positions according to the physical placement rules.

### Detailed Placement

Detailed placement performs local adjustments to improve the quality of the placement.

---

## 13. Placement Optimization

After initial placement, optimization is performed to improve timing, congestion, wire length, and overall placement quality.

The tool considers parameters such as:

- Wire length
- Capacitance
- Delay
- Congestion
- Setup timing
- Hold timing
- Cell density

Depending on the violations and optimization targets, the tool may resize cells, move cells, or insert buffers.

### Buffer and Repeater Insertion

Long interconnects can introduce significant delay and signal degradation.

Buffers or repeaters can be inserted along long paths:

```text
Source Cell
     |
     | Long Wire
     |
   Buffer
     |
     | Long Wire
     |
Destination Cell

```

These buffers help improve signal integrity and reduce the impact of long interconnects.

---

## 14. Placement Statistics

After placement, OpenLane reports statistics that help evaluate the quality of the physical implementation.

Example placement results:

```text
Total Instances      : 21699
Fixed Instances      : 6354
Nets                 : 15449
Design Area          : 420473.3 um²
Utilization          : 36%
Utilization Padded   : 55%
Rows                 : 238

```

### Important Placement Metrics

| ParameterDescription |                                           |
| -------------------- | ----------------------------------------- |
| Total Instances      | Total number of cell instances            |
| Fixed Instances      | Number of instances with fixed locations  |
| Nets                 | Number of electrical connections          |
| Design Area          | Physical area occupied by the design      |
| Utilization          | Percentage of available area occupied     |
| Utilization Padded   | Utilization considering placement padding |
| Rows                 | Number of standard-cell placement rows    |
| Wire Length          | Estimated interconnect length             |
| Displacement         | Movement of cells during optimization     |

These metrics help identify possible issues related to **area, congestion, placement quality, and timing**.

---

## 15. Floorplanning and Placement Results

### Floorplanning Result

The floorplanning stage establishes the physical boundaries of the design and defines the regions available for cells and other physical structures.

### Placement Result

After placement, standard cells are distributed within the core while considering timing, congestion, connectivity, and legal placement constraints.

---

## 16. Key Learnings

Through the floorplanning and placement stage of the PicoRV32A design, the following concepts were studied:

```text
Core
Die
Aspect Ratio
Utilization
Floorplanning
Floorplan Configuration
Pre-Placed Cells
Power Planning
Decoupling Capacitors
Pin Placement
Placement Blockages
Standard Cell Placement
Placement Optimization
Placement Statistics

```

The main purpose of this stage is to transform the synthesized logical design into a physically organized implementation that can proceed to clock-tree synthesis and routing.

---

## 17. Physical Design Flow Covered

The overall flow completed so far is:

```text
RTL
 ↓
Synthesis
 ↓
Gate-Level Netlist
 ↓
Floorplanning
 ↓
Power Planning
 ↓
Pin Placement
 ↓
Standard Cell Placement
 ↓
Placement Optimization

```

The next major stages of the physical design flow are:

```text
Clock Tree Synthesis (CTS)
        ↓
Routing
        ↓
Parasitic Extraction
        ↓
Static Timing Analysis
        ↓
Physical Verification
        ↓
Final Layout

```

---

## 18. Conclusion

Floorplanning and placement are essential steps in converting a synthesized netlist into a physically organized and implementable chip layout.

Floorplanning establishes the physical organization of the design, while placement assigns locations to standard cells. Power planning, pin placement, blockages, and placement optimization further improve the physical implementation and prepare the design for subsequent stages.

The PicoRV32A design has progressed from the synthesized netlist to a physically organized implementation, providing the foundation for the next stages of **Clock Tree Synthesis (CTS), routing, parasitic extraction, timing analysis, and physical verification**.

