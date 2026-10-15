# VSDBabySoC Physical Design and Static Timing Analysis

This directory documents the physical-design and timing-analysis work
performed on **VSDBabySoC** using the **SKY130 HD** platform and
OpenROAD/OpenSTA tools.

The evidence is organized by stage, from pre-layout static timing
analysis (STA) through floorplanning, placement, clock-tree synthesis
(CTS), routing, and post-route STA.

> **Evidence note:** Screenshots document the tool views and results
> visible at the time they were captured. A stage completing does not,
> by itself, prove timing closure, complete design-rule-check (DRC)
> closure, layout-versus-schematic (LVS) closure, or signoff readiness.

## Directory Structure

``` text
physical_design/
├── README.md
├── pre_layout_sta/
├── floorplanning/
├── placement/
├── cts/
├── routing/
└── post_route_sta/
```

Image links below assume that the screenshots are stored in the stage
folders shown above and retain the filenames shown in the project
screenshot, with a `.png` extension. If an uploaded image uses a
different extension, update that link to match the exact filename in
GitHub.

------------------------------------------------------------------------

## 1. Pre-Layout Static Timing Analysis

Pre-layout STA evaluates timing using the timing constraints, mapped
design, and library timing models available before physical
implementation. The evidence below covers the 18 logical Liberty corners
used in this analysis.

### 1.1 Corner execution

![Pre-layout STA: 18-corner
execution](pre_layout_sta/pre_layout_sta_18_corner_execution.png)

*The terminal output shows the multi-corner STA run and the discovered
Liberty libraries. The screenshot also shows a permission-denied message
when writing a report, so successful execution should be distinguished
from successful report-file creation for every corner.*

### 1.2 Corner metrics

![Pre-layout STA: 18-corner
metrics](pre_layout_sta/pre_layout_sta_18_corner_metrics.png)

*Corner-wise metrics collected during the pre-layout analysis.*

### 1.3 Corner completion

![Pre-layout STA: corner
completion](pre_layout_sta/pre_layout_sta_corner_completion.png)

*Terminal evidence showing the reported completion status of the
corner-analysis script. Review the accompanying logs and output files
when interpreting the run status.*

### 1.4 CSV results

![Pre-layout STA: CSV
results](pre_layout_sta/pre_layout_sta_csv_results.png)

*CSV output listing corner-wise hold WNS/TNS and setup WNS/TNS. In the
captured data, several slow-process corners have negative setup slack
and strongly negative setup TNS values. These values should be
interpreted using the exact report definitions and constraints.*

### 1.5 Hold timing path

![Pre-layout STA: hold timing
path](pre_layout_sta/pre_layout_sta_hold_timing_path.png)

*Example minimum-delay timing path, including the startpoint, endpoint,
clock path assumptions, data arrival time, data required time, and
reported slack.*

### 1.6 Setup timing path

![Pre-layout STA: setup timing
path](pre_layout_sta/pre_layout_sta_setup_timing_path.png)

*Example maximum-delay timing path through combinational cells between
sequential elements. The path illustrates how cell and interconnect
delays contribute to data arrival time.*

### 1.7 Setup slack summary

![Pre-layout STA: setup slack
summary](pre_layout_sta/pre_layout_sta_setup_slack_summary.png)

*Summary of setup and hold WNS/TNS metrics reported by the pre-layout
analysis. Check the source reports before treating any zero value as
proof that no violations exist; report coverage and constraints matter.*

### 1.8 WNS/TNS charts

![Pre-layout STA: WNS and TNS
charts](pre_layout_sta/pre_layout_sta_wns_tns_charts.png)

*Visual comparison of timing metrics across the evaluated corners.
Negative setup slack or negative setup TNS indicates timing violations
under the constraints used for that analysis.*

------------------------------------------------------------------------

## 2. Floorplanning

Floorplanning establishes the initial die/core organization and the
placement of macros and other floorplan elements. The screenshot below
documents the OpenROAD GUI view captured during the floorplanning stage.

![Floorplanning in OpenROAD
GUI](floorplanning/floorplanning_openroad_gui.png)

*OpenROAD floorplanning view. The screenshot includes a timing chart and
reports 17 unconstrained pins; this should be investigated when
assessing timing-constraint coverage. The view alone does not establish
that every floorplanning check passed.*

------------------------------------------------------------------------

## 3. Placement

Placement assigns locations to standard cells while considering legal
placement sites, timing, congestion, and wirelength. The following
screenshots document the global-placement view and a standard-cell
placement view.

### 3.1 Global placement

![Global placement in OpenROAD
GUI](placement/global_placement_openroad_gui.png)

*OpenROAD GUI view during the global-placement stage. The displayed
layout is evidence of the placement-stage view, not a standalone proof
of placement quality or timing closure.*

### 3.2 Standard-cell placement layout

![Standard-cell placement
layout](placement/standard_cell_placement_layout.png)

*Layout view showing standard-cell placement. Use the corresponding
placement reports for quantitative metrics such as utilization,
displacement, congestion, and timing.*

------------------------------------------------------------------------

## 4. Clock-Tree Synthesis (CTS)

CTS builds the clock distribution network for sequential elements. The
resulting clock paths affect insertion delay, skew, and setup/hold
timing.

![Clock-tree synthesis in OpenROAD
GUI](cts/clock_tree_synthesis_openroad_gui.png)

*OpenROAD GUI view captured during CTS. Clock latency, skew, buffer
counts, and post-CTS timing should be taken from the associated CTS
reports rather than inferred from the screenshot alone.*

------------------------------------------------------------------------

## 5. Routing

Routing implements signal interconnect using the available metal layers
and vias. The images below are visual evidence of routing/layout views.

### 5.1 Routing layout view 1

![Routing layout view 1](routing/routing_layout_view_1.png)

*Routing/layout view captured during physical implementation.*

### 5.2 Routing layout view 2

![Routing layout view 2](routing/routing_layout_view_2.png)

*Additional routing/layout view. The image alone does not establish
detailed-routing completion or DRC cleanliness.*

**Known limitation:** The available routing DRC report contains entries
marked `Short`, including entries involving DAC-related nets. These
violations must be reviewed and resolved or explicitly documented before
claiming DRC-clean routing. Do not describe the design as DRC-clean,
LVS-clean, or signoff-ready without corresponding verification evidence.

The existence of macro GDS files does not, by itself, prove that a
merged top-level VSDBabySoC GDS was generated.

------------------------------------------------------------------------

## 6. Post-Route Static Timing Analysis

Post-route STA evaluates timing after routing, using the timing setup
and parasitic information available to the analysis. The post-route
analysis documented here covers **16 evaluated corners**. This corner
set is distinct from the 18 logical Liberty corners used in the
pre-layout analysis; the two sets should not be assumed identical.

### 6.1 Sixteen-corner completion

![Post-route STA: 16-corner
completion](post_route_sta/post_route_sta_16_corner_completion.png)

*Terminal output reporting 16 completed runs and zero failed runs for
the post-route STA script. This indicates the script's reported
execution status; it does not mean that every corner met setup and hold
timing requirements.*

### 6.2 Corner slack results

![Post-route STA: corner slack
results](post_route_sta/post_route_sta_corner_slack_results.png)

*Corner-wise worst-slack results. Confirm the report's definitions of
`worst slack max` and `worst slack min` before labeling them as setup or
hold metrics.*

### 6.3 Setup TNS chart

![Post-route STA: setup
TNS](post_route_sta/post_route_sta_setup_tns.png)

*Setup total negative slack (TNS) visualization across the evaluated
corners. Strongly negative TNS values indicate accumulated setup timing
violations under the analyzed constraints.*

### 6.4 Setup WNS chart

![Post-route STA: setup
WNS](post_route_sta/post_route_sta_setup_wns.png)

*Setup worst negative slack (WNS) visualization across the evaluated
corners. Negative WNS indicates at least one setup path violates its
required timing; non-negative WNS alone does not establish complete
signoff.*

------------------------------------------------------------------------

## 7. Interpretation and Limitations

-   **Pre-layout STA:** The evidence shows analysis across 18 logical
    Liberty corners. The captured outputs include negative setup
    slack/TNS for some corners and a report-writing permission warning.
-   **Physical implementation:** Floorplanning, placement, CTS, and
    routing are represented by GUI screenshots. Use stage reports and
    logs for quantitative claims.
-   **Post-route STA:** The captured terminal output reports 16
    completed runs and zero failed runs. Timing closure must be
    evaluated from the actual per-corner setup/hold reports.
-   **Corner sets:** The 18-corner pre-layout analysis and 16-corner
    post-route analysis are separate datasets. Compare them only after
    confirming their libraries, constraints, parasitic assumptions, and
    metric definitions.
-   **Routing integrity:** The available routing DRC report includes
    `Short` violations. Do not claim DRC closure unless a subsequent
    report verifies that these have been resolved.
-   **GDS/LVS status:** These screenshots do not prove top-level GDS
    stream-out or LVS closure. Claim either only when supported by the
    corresponding artifacts and verification reports.
-   **Unconstrained paths:** Investigate unconstrained pins or paths
    before relying on STA summaries as comprehensive timing evidence.

## 8. Evidence Inventory

  -----------------------------------------------------------------------
  Stage                               Evidence
  ----------------------------------- -----------------------------------
  Pre-layout STA                      18-corner execution, corner
                                      metrics, completion output, CSV
                                      results, hold/setup paths, setup
                                      slack summary, WNS/TNS charts

  Floorplanning                       OpenROAD floorplanning GUI

  Placement                           Global-placement GUI and
                                      standard-cell placement layout

  CTS                                 Clock-tree synthesis GUI

  Routing                             Two routing/layout views

  Post-route STA                      16-corner completion, corner slack
                                      results, setup TNS chart, setup WNS
                                      chart
  -----------------------------------------------------------------------
