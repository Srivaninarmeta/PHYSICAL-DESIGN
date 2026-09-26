# Timing Analysis & Clock Tree Synthesis

<p align="center">
  <b>Timing Characterization • STA • Clock Distribution • Signal Integrity</b>
</p>

<p align="center">
  A practical exploration of standard-cell timing, delay characterization, setup and hold checks,
  clock uncertainty, CTS, skew, crosstalk and post-CTS timing using the SKY130-based physical design flow.
</p>

---

## 📚 Table of Contents

1. [⏱️ Timing Characterization](#1--timing-characterization)
2. [📊 Cell Delay Tables](#2--cell-delay-tables)
3. [⚡ Slew and Capacitive Load](#3--slew-and-capacitive-load)
4. [🕐 Setup Analysis](#4--setup-analysis)
5. [🔒 Hold Analysis](#5--hold-analysis)
6. [⚠️ Clock Jitter and Uncertainty](#6--clock-jitter-and-uncertainty)
7. [🌳 Clock Tree Synthesis](#7--clock-tree-synthesis)
8. [📐 Skew and Clock Latency](#8--skew-and-clock-latency)
9. [🔊 Crosstalk and Signal Integrity](#9--crosstalk-and-signal-integrity)
10. [🛡️ Clock Shielding](#10--clock-shielding)
11. [🔄 Ideal and Propagated Clock](#11--ideal-and-propagated-clock)
12. [🔬 Static Timing Analysis with OpenSTA](#12--static-timing-analysis-with-opensta)
13. [📈 WNS and TNS](#13--wns-and-tns)
14. [💻 OpenLane and OpenROAD Commands](#14--openlane-and-openroad-commands)
15. [🎯 Day 4 Learning Summary](#15--day-4-learning-summary)

---

# 1. ⏱️ Timing Characterization

Timing characterization describes how a standard cell behaves when its input signal and output loading conditions change.

A logic cell cannot be assigned one universal propagation delay. Its delay depends on factors such as the incoming signal transition and the capacitance connected to its output.

### 🔹 Basic Concept

```text
             Input Slew
                 │
                 ▼
          ┌─────────────┐
          │ Standard    │
          │    Cell     │
          └──────┬──────┘
                 │
                 ▼
          Propagation Delay
                 ▲
                 │
           Output Load
```

The cell timing behaviour can be represented as:

**Cell Delay = f(Input Slew, Output Capacitance)**

### 🔹 Important Parameters

| Parameter       | Meaning                                                       |
| :-------------- | :------------------------------------------------------------ |
| **Input Slew**  | Speed at which the input waveform changes                     |
| **Output Load** | Capacitance presented to the cell output                      |
| **Cell Delay**  | Time required for the cell output to respond                  |
| **Transition**  | Time taken by a signal to move between defined voltage levels |

### 🔹 Why Timing Characterization is Required?

Physical-design tools need timing information for standard cells while evaluating thousands or millions of timing paths.

Performing transistor-level analysis for every path would be impractical. Therefore, cells are characterized beforehand and their timing behaviour is stored in technology libraries.

These libraries are consumed by:

* RTL synthesis
* Static Timing Analysis
* Placement optimization
* Routing optimization
* Timing closure tools

### 💡 Key Idea

> **The propagation delay of a standard cell varies with both input slew and output loading.**

When the input becomes slower or the output capacitance becomes larger, the cell generally takes longer to produce the required output transition.

---

# 2. 📊 Cell Delay Tables

Standard-cell libraries commonly represent timing behaviour using lookup tables.

These tables contain characterized delay values for multiple combinations of input transition and output capacitance.

### 🔹 Simplified Delay Table

| Input Slew ↓ / Output Load → |          Low |       Medium |         High |
| :--------------------------- | -----------: | -----------: | -----------: |
| **Fast**                     |  Small Delay |  Small Delay | Medium Delay |
| **Medium**                   |  Small Delay | Medium Delay |  Large Delay |
| **Slow**                     | Medium Delay |  Large Delay |  Large Delay |

### 🔹 How the Table is Used

```text
Input Slew
     │
     ▼
┌──────────────┐
│ Timing       │
│ Lookup Table │
└──────┬───────┘
       ▲
       │
Output Capacitance
```

During STA, the timing engine determines the current slew and load of a cell and uses the characterized library information to estimate the corresponding delay.

### 💡 Key Idea

> **Cell delay generally increases when both input transition time and output capacitance increase.**

This allows timing tools to perform fast timing calculations without repeatedly running detailed circuit simulations.

---

# 3. ⚡ Slew and Capacitive Load

Input slew and output capacitance are two major contributors to standard-cell delay.

## 🔹 Input Slew

Input slew describes the speed of an electrical transition at the input of a cell.

```text
Voltage
  │
  │              ┌────────
  │             /
  │            /
  │           /
  │──────────┘
  │
  └──────────────────────► Time
```

A steep waveform corresponds to a faster transition, while a gradual waveform represents a slower transition.

Poor input transition can propagate through logic and negatively influence downstream timing.

### 🔹 Output Load

The output load is the effective capacitance that a driver has to charge and discharge.

```text
             Driver Cell
                  │
                  ▼
            ┌──────────┐
            │  Load C  │
            └──────────┘
```

As the capacitive load becomes larger, more time is required for the output voltage to transition.

### 🔹 Relationship

```text
Faster Slew
Lower Load
     │
     ▼
Smaller Delay
```

```text
Slower Slew
Higher Load
     │
     ▼
Larger Delay
```

### 📌 Summary

| Condition             | Expected Delay |
| :-------------------- | :------------: |
| Fast Slew + Low Load  |       Low      |
| Fast Slew + High Load |    Moderate    |
| Slow Slew + Low Load  |    Moderate    |
| Slow Slew + High Load |      High      |

> **Both transition quality and output loading must be considered when estimating cell delay.**

---

# 4. 🕐 Setup Analysis

Setup timing verifies that data reaches the receiving flip-flop sufficiently before the active capture clock edge.

A normal sequential path contains a launch element, combinational circuitry and a capture element.

```text
Launch Flip-Flop
       │
       ▼
Logic / Interconnect
       │
       ▼
Capture Flip-Flop
```

The launched data must travel through the complete data path within the time permitted by the clock period and the setup requirement.

### 🔹 Setup Requirement

For a simplified ideal-clock case:

```text
Data Path Delay < Clock Period - Setup Time
```

When clock uncertainty is considered:

```text
Usable Timing Window
=
Clock Period
- Setup Time
- Clock Uncertainty
```

### 🔹 Setup Slack

```text
Setup Slack
=
Required Arrival Time
-
Actual Arrival Time
<img width="787" height="807" alt="image" src="https://github.com/user-attachments/assets/98167490-3bb3-43de-8f3f-9f9a911d6d5e" />

```

### 🔹 Timing Condition

```text
Setup Slack > 0
        │
        ▼
   Setup Met
```

```text
Setup Slack < 0
        │
        ▼
  Setup Violation
```

### 🔹 Example

Assume:

```text
Clock Period = 1 ns
Setup Time   = 0.10 ns
Uncertainty  = 0.05 ns
```

The usable data window becomes:

```text
1 - 0.10 - 0.05
= 0.85 ns
```

Hence, the data path must satisfy the available 0.85 ns timing window.

### 💡 Key Idea

> **Setup analysis determines whether the data path can complete before the receiving clock edge.**

---

# 5. 🔒 Hold Analysis

Hold analysis checks whether the data input of a capture flip-flop remains stable for the required duration immediately after the active clock edge.

```text
                 Hold Interval
                      │
                      ▼
Clock ────────────────┼────────────
                      │
Data  ────────────────┴────────────
                      │
                Data remains stable
```

The data path must not become active too quickly after the capture edge.

### 🔹 Hold Requirement

For an ideal clock:

```text
Data Path Delay > Hold Time
```

For a practical clock network:

```text
Launch Clock Delay + Data Delay
>
Hold Time + Capture Clock Delay
```

Where:

* **Data Delay** = Minimum propagation delay through the data path
* **Launch Clock Delay** = Clock delay reaching the launching element
* **Capture Clock Delay** = Clock delay reaching the receiving element
* **Hold Time** = Minimum time for which data must remain stable

### 🔹 Hold Slack

```text
Hold Slack
=
Arrival Time
-
Required Time
```

### 🔹 Timing Condition

```text
Hold Slack > 0
        │
        ▼
     Hold Met
```

```text
Hold Slack < 0
        │
        ▼
   Hold Violation
```

### 🔹 Setup vs Hold

| Setup                                | Hold                                            |
| :----------------------------------- | :---------------------------------------------- |
| Detects late-arriving data           | Detects early-arriving data                     |
| Maximum-delay analysis               | Minimum-delay analysis                          |
| Concerned with the next capture edge | Concerned with the immediate post-edge interval |
| Uses setup requirement               | Uses hold requirement                           |

### 💡 Key Idea

> **Hold analysis makes sure that newly launched data does not reach the capture element prematurely.**

---

# 6. ⚠️ Clock Jitter and Uncertainty

Clock signals in real systems are affected by variations in the exact location of their edges.

The deviation of an actual clock edge from its expected position is commonly described as **clock jitter**.

```text
Expected Edge
      │
      ▼
──────┼────────
   ← Variation →
```

The edge can occur slightly earlier or later than the nominal position.

## 🔹 Clock Uncertainty

Clock uncertainty is timing margin reserved for variations associated with the clock.

It may account for effects such as:

* Clock jitter
* Clock variation
* Clock distribution uncertainty
* Other clock-related margins

### 🔹 Effect on Setup Timing

```text
Available Timing
=
Clock Period
- Setup Time
- Clock Uncertainty
```

Therefore:

```text
Clock Uncertainty ↑
        │
        ▼
Available Margin ↓
```

### 🔹 Why is it Important?

A timing engine cannot assume that every clock edge will occur at exactly the ideal location.

Adding uncertainty gives the design an additional safety margin during timing verification.

### 💡 Key Idea

> **Clock uncertainty reserves timing margin for variations in clock arrival.**

---

# 7. 🌳 Clock Tree Synthesis

Clock Tree Synthesis creates the physical network that distributes the clock signal from its source to sequential elements throughout the design.

A single clock source may have to drive a very large number of flip-flops. A direct connection would create excessive fanout and uneven arrival times.

CTS introduces a structured network of buffers and interconnects.

### 🔹 Basic Clock Distribution

```text
                    Clock Source
                         │
                      Buffer
                         │
                  ┌──────┴──────┐
                  │             │
               Buffer        Buffer
                  │             │
             ┌────┴────┐    ┌───┴────┐
             │         │    │        │
            FF1       FF2  FF3      FF4
```

### 🔹 Objectives of CTS

The clock-tree stage aims to:

* Control clock skew
* Manage clock latency
* Reduce excessive fanout
* Improve clock transition
* Distribute clock load
* Maintain clock quality
* Produce a usable physical clock network

### 🔹 TritonCTS

Within the OpenLane/OpenROAD environment, **TritonCTS** performs Clock Tree Synthesis.

A typical command is:

```tcl
run_cts
```

### 💡 Key Idea

> **CTS transforms the logical clock connection into a physical distribution network designed to provide controlled and balanced clock arrival.**

---

# 8. 📐 Skew and Clock Latency

## 🔹 Clock Skew

Clock skew refers to the difference between clock arrival times at two sequential elements.

```text
                  Clock Source
                       │
                 ┌─────┴─────┐
                 │           │
                FF1         FF2
                 │           │
                t₁           t₂
```

The skew can be expressed as:

```text
Clock Skew = t₂ - t₁
```

In a perfectly balanced network:

```text
Clock Skew ≈ 0
```

### 🔹 Why Skew Matters?

Clock arrival differences can influence:

* Setup margin
* Hold margin
* Timing closure
* Sequential circuit operation

---

## 🔹 Clock Latency

Clock latency represents the propagation time required for a clock signal to travel from its source to a sequential endpoint.

A simplified representation is:

```text
Clock Latency
=
Buffer Delay
+
Interconnect Delay
+
Parasitic Effects
```

### 🔹 Clock Network

```text
Clock Source
     │
     ▼
  Buffer
     │
     ▼
  Interconnect
     │
     ▼
  Buffer
     │
     ▼
 Flip-Flop
```

### 💡 Key Idea

> **Clock latency describes how long the clock takes to reach an endpoint, while skew describes the difference between clock arrival times.**

---

# 9. 🔊 Crosstalk and Signal Integrity

Crosstalk occurs when neighbouring interconnects electrically interact through parasitic coupling.

```text
Aggressor Wire
══════════════════════════
          ↕
    Coupling Capacitance
          ↕
Victim Wire
══════════════════════════
```

A changing signal on the aggressor can introduce an unwanted disturbance on the victim net.

### 🔹 Effects of Crosstalk

Possible effects include:

* Noise generation
* Unwanted transitions
* Delay changes
* Slew degradation
* Timing variation
* Clock disturbances
* Signal integrity issues

### 🔹 Crosstalk and Delay

```text
Nominal Delay
      +
Coupling Effect
      │
      ▼
Effective Delay
```

This is especially significant for timing-critical nets.

### 🔹 Signal Integrity

Signal integrity focuses on ensuring that signals maintain acceptable electrical behaviour while travelling through the physical interconnect.

Important considerations include:

* Coupling capacitance
* Crosstalk
* Noise
* Slew degradation
* Delay variation

### 💡 Key Idea

> **Electrical interaction between nearby wires can alter both waveform quality and timing behaviour.**

---
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/31e41083-66ef-4b88-8a95-c0dc7cf76493" />

# 10. 🛡️ Clock Shielding

Clock networks require careful physical routing because clock signals influence a large number of sequential elements.

One method used to protect sensitive clock routes is **shielding**.

### 🔹 Simplified Structure

```text
       Shield          Clock          Shield
══════════════       ═══════       ══════════════
      GND               CLK              GND
```

A shield is normally connected to a stable reference such as:

```text
VDD
or
GND
```

### 🔹 Benefits

Clock shielding can help to:

* Lower unwanted coupling
* Reduce crosstalk
* Limit noise injection
* Protect sensitive clock transitions
* Improve clock signal quality

### 💡 Key Idea

> **Shielding provides electrical isolation around sensitive routes and helps protect important clock signals from nearby switching activity.**

---

# 11. 🔄 Ideal and Propagated Clock

Timing analysis changes after Clock Tree Synthesis.

Before CTS, the clock is generally treated as an ideal signal. After CTS, the physical clock network can be included in timing calculations.

## 🔹 Ideal Clock

Before CTS, the clock can be represented without modelling the detailed physical distribution network.

```text
                 Clock
                   │
             ┌─────┴─────┐
             │           │
            FF1         FF2
```

The actual clock-tree buffer and wire delays are not yet represented.

This makes ideal-clock analysis useful during earlier stages of physical design.

---

## 🔹 Propagated / Real Clock

After CTS, the clock passes through physical buffers and interconnect.

```text
                 Clock
                   │
                 Buffer
                   │
                  Wire
                   │
              ┌────┴────┐
           Buffer      Buffer
             │            │
            FF1          FF2
```

Timing analysis can now include:

* Clock buffer delays
* Interconnect delays
* Clock latency
* Clock skew
* Parasitic effects

### 🔹 Comparison

| Feature         | Ideal Clock |   Propagated Clock  |
| :-------------- | :---------: | :-----------------: |
| Clock Buffers   |      ❌      |          ✅          |
| Wire Delay      |      ❌      |          ✅          |
| Clock Latency   |  Idealized  |       Included      |
| Clock Skew      |  Idealized  |       Physical      |
| RC Effects      |      ❌      |          ✅          |
| Timing Accuracy | Preliminary | More representative |

### 🔄 Transition

```text
Ideal Clock
     │
     ▼
Pre-CTS Analysis
     │
     ▼
Clock Tree Synthesis
     │
     ▼
Propagated Clock
     │
     ▼
Post-CTS Analysis
```

### 💡 Key Idea

> **Propagated-clock analysis reflects the physical behaviour of the implemented clock network more closely than ideal-clock analysis.**

---

# 12. 🔬 Static Timing Analysis with OpenSTA

**Static Timing Analysis (STA)** evaluates timing paths mathematically without requiring functional simulation vectors.

OpenSTA can examine the timing relationships between sequential elements and determine whether timing constraints are satisfied.

### 🔹 Typical Timing Path

```text
Launch Flip-Flop
       │
       ▼
Combinational Logic
       │
       ▼
Capture Flip-Flop
```

OpenSTA can report values such as:

* Arrival time
* Required time
* Slack
* Setup violations
* Hold violations
* Clock information
* Critical timing paths

### 🔹 Slack

A simplified timing relationship is:

```text
Slack
=
Required Time
-
Arrival Time
```

### 🔹 Timing Result

```text
Positive Slack
      │
      ▼
 Timing Met
```

```text
Zero Slack
      │
      ▼
 Timing Limit Reached
```

```text
Negative Slack
      │
      ▼
 Timing Violation
```

### 🔹 Propagated Clock

After CTS, timing analysis can use the physical clock network.

Consequently, the analysis can account for:

* Clock buffer delay
* Clock interconnect delay
* Clock latency
* Clock skew

### 💡 Key Idea

> **OpenSTA evaluates whether the implemented design satisfies its timing constraints by analyzing its timing paths and clock relationships.**

---

# 13. 📈 WNS and TNS

Two commonly used indicators for evaluating timing results are:

* **WNS — Worst Negative Slack**
* **TNS — Total Negative Slack**

---

## 🔹 WNS — Worst Negative Slack

WNS identifies the most negative slack value among the analyzed timing paths.

```text
WNS = Minimum Slack
```

### Example

```text
Path 1 → +0.20 ns
Path 2 → -0.05 ns
Path 3 → -0.15 ns
```

The worst slack is:

```text
WNS = -0.15 ns
```

A negative WNS means at least one analyzed path has negative timing margin.

---

## 🔹 TNS — Total Negative Slack

TNS represents the combined negative slack of paths that violate the timing requirement.

```text
TNS = Sum of Negative Slack Values
```

### Example

```text
Path 1 → -0.05 ns
Path 2 → -0.10 ns
Path 3 → -0.15 ns
```

Therefore:

```text
TNS = -0.30 ns
```

### 🔹 Ideal Timing Condition

```text
WNS ≥ 0
```

```text
TNS = 0
```

This means that the analyzed timing paths do not contain negative slack.

### 💡 Key Idea

> **WNS identifies the most severe timing margin, whereas TNS represents the combined negative timing margin across violating paths.**

---

# 14. 💻 OpenLane and OpenROAD Commands

The following commands are useful for examining different stages of the physical-design flow.

## 🔹 Run Synthesis

```tcl
run_synthesis
```

---

## 🔹 Run Floorplanning

```tcl
run_floorplan
```

---

## 🔹 Run Placement

```tcl
run_placement
```

---

## 🔹 Execute CTS

```tcl
run_cts
```

---

## 🔹 Display Synthesis Strategy

```tcl
echo $::env(SYNTH_STRATEGY)
```

---

## 🔹 Enable Synthesis Buffering

```tcl
set ::env(SYNTH_BUFFERING) 1
```

---

## 🔹 Enable Synthesis Cell Sizing

```tcl
set ::env(SYNTH_SIZING) 1
```

---

## 🔹 Launch OpenROAD

```bash
openroad
```

---

## 🔹 Generate Detailed Timing Report

```tcl
report_checks -path_delay min_max \
-format full_clock_expanded \
-digits 4
```

---

## 🔹 Examine Setup Clock Skew

```tcl
report_clock_skew -setup
```

---

## 🔹 Examine Hold Clock Skew

```tcl
report_clock_skew -hold
```

### 🔹 Important Timing Values

When inspecting an STA report, the following values are useful:

| Parameter         | What it tells you                             |
| :---------------- | :-------------------------------------------- |
| **Arrival Time**  | Time at which the signal reaches the endpoint |
| **Required Time** | Time boundary that the signal must satisfy    |
| **Slack**         | Remaining timing margin                       |
| **Data Delay**    | Delay accumulated along the data path         |
| **Clock Delay**   | Delay associated with clock propagation       |
| **Clock Skew**    | Difference between clock arrival times        |
| **WNS**           | Most negative timing slack                    |
| **TNS**           | Combined negative slack                       |

---

# 15. 🎯 Key Takeaways

### 🧠 Major Concepts Learned

* Standard-cell timing changes according to **input slew and output capacitance**.
* Timing libraries contain characterized information for estimating cell behaviour.
* Delay lookup tables relate transition and load conditions to cell delay.
* Setup analysis verifies that data does not arrive beyond the capture requirement.
* Hold analysis verifies that data does not arrive too soon.
* Clock jitter represents variation in the position of clock edges.
* Clock uncertainty reserves additional margin for clock-related variations.
* CTS builds a physical clock distribution network.
* TritonCTS is used to construct the clock tree in the OpenROAD-based flow.
* Clock skew is the difference in arrival time between clock endpoints.
* Clock latency describes the propagation time of the clock network.
* Crosstalk results from electrical coupling between neighbouring interconnects.
* Signal integrity analysis considers unwanted electrical disturbances.
* Clock shielding can reduce coupling from neighbouring routes.
* Ideal-clock analysis is useful before physical clock-tree implementation.
* Propagated-clock analysis incorporates the implemented clock network.
* OpenSTA performs static timing calculations.
* WNS indicates the most negative timing slack.
* TNS represents the accumulated negative slack.
* Both setup and hold requirements are considered during timing closure.

---

## 🔄 Complete Flow

```text
             Cell Timing Models
                    │
                    ▼
              Delay Tables
                    │
                    ▼
          Slew + Load Calculation
                    │
                    ▼
            Pre-CTS Timing
                    │
                    ▼
                Placement
                    │
                    ▼
          Clock Tree Synthesis
               (TritonCTS)
                    │
                    ▼
           Skew / Latency Check
                    │
                    ▼
        Crosstalk / SI Analysis
                    │
                    ▼
          Propagated Clock STA
                    │
              ┌─────┴─────┐
              ▼           ▼
            Setup        Hold
              │           │
              └─────┬─────┘
                    ▼
             Timing Reports
                    │
                    ▼
                 WNS/TNS
                    │
                    ▼
              Timing Closure
```

---

## 📊 Setup vs Hold — Quick Reference

| Feature           |         Setup        |         Hold        |
| :---------------- | :------------------: | :-----------------: |
| Main Check        |   Data arrives late  |  Data arrives early |
| Timing Type       |     Maximum delay    |    Minimum delay    |
| Related Parameter |      Setup Time      |      Hold Time      |
| Uncertainty       |   Setup Uncertainty  |   Hold Uncertainty  |
| Violation         | Negative Setup Slack | Negative Hold Slack |
| Goal              |       Slack ≥ 0      |      Slack ≥ 0      |

---

## 📊 Ideal Clock vs Propagated Clock

| Feature         | Ideal Clock |   Propagated Clock  |
| :-------------- | :---------: | :-----------------: |
| CTS             |  Before CTS |      After CTS      |
| Clock Network   |  Abstracted |       Physical      |
| Buffers         |   Ignored   |       Included      |
| Wire Delay      |   Ignored   |       Included      |
| Skew            |  Idealized  |      Calculated     |
| Latency         |  Idealized  |       Included      |
| RC Effects      |   Ignored   |       Included      |
| Timing Accuracy | Preliminary | More representative |

---

## 🛠️ Tools Used

| Tool           | Purpose                                     |
| :------------- | :------------------------------------------ |
| **OpenLane**   | Automated RTL-to-GDSII implementation flow  |
| **Yosys**      | RTL synthesis and logic processing          |
| **OpenROAD**   | Physical implementation and optimization    |
| **OpenSTA**    | Static Timing Analysis                      |
| **TritonCTS**  | Clock Tree Synthesis                        |
| **SKY130 PDK** | Technology and standard-cell information    |
| **Magic**      | Layout inspection and physical verification |
| **Linux**      | Design and EDA environment                  |

---

## 📌 Final Summary

The main progression of Day 4 can be represented as:

```text
Cell Timing Characterization
       ↓
Delay Lookup Tables
       ↓
Input Slew + Output Capacitance
       ↓
Setup / Hold Verification
       ↓
Clock Jitter and Uncertainty
       ↓
Clock Tree Construction
       ↓
Clock Skew and Latency
       ↓
Crosstalk Effects
       ↓
Clock Shielding
       ↓
Ideal Clock Analysis
       ↓
Propagated Clock Analysis
       ↓
OpenSTA
       ↓
WNS / TNS
       ↓
Timing Closure
```

Day 4 connects standard-cell timing information with the physical clock network used in an ASIC implementation.

Once physical implementation begins, timing is influenced by several components:

```text
Cell Delay
    +
Interconnect Delay
    +
Clock Network Delay
    +
Clock Skew
    +
Parasitic Effects
    +
Crosstalk
    ↓
Physical Timing Behaviour
```

---

# 🏁 Conclusion

The study focused on how timing is evaluated as a digital design moves through the physical implementation flow.

The concepts progressed from standard-cell timing characterization and delay lookup tables to setup and hold verification, clock uncertainty, clock-tree construction, skew, latency, crosstalk and propagated-clock analysis.

The complete relationship can be summarized as:

```text
                    TIMING
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
          DATA PATH          CLOCK PATH
             │                   │
             ▼                   ▼
       Cell + Wire          CTS + Buffers
          Delay              + Interconnect
             │                   │
             └─────────┬─────────┘
                       ▼
                    OpenSTA
                       │
                ┌──────┴──────┐
                ▼             ▼
              Setup          Hold
                │             │
                └──────┬──────┘
                       ▼
                 Timing Closure
```

### 📸 OpenSTA Timing Report

The following section shows the timing report obtained after performing static timing analysis with OpenSTA.

<p align="center">
  <img src="opensta_timing_report.png" width="850">
</p>

### 🔍 Timing Result

| Parameter          |          Value |
| :----------------- | -------------: |
| Clock Period       | **12.0000 ns** |
| Data Arrival Time  |  **4.9962 ns** |
| Data Required Time |  **9.6000 ns** |
| Slack              |  **4.6038 ns** |
| Timing Status      |      ✅ **MET** |

### 🧠 Observation

The reported timing margin is:

```text
4.6038 ns
```

The positive slack indicates that the analyzed path has remaining timing margin with respect to the reported requirement.

```text
Slack > 0
   ↓
Positive Timing Margin
   ↓
✅ MET
```

This OpenSTA result demonstrates that the analyzed timing path meets the specified timing constraint.
