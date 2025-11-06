# Step VI — Routing

The **Routing** stage connects all the placed standard cells, macros, and I/O pins using metal interconnects that comply with the design rules of the target technology.  
This step transforms the abstract placement into a **fully routed layout**, ready for parasitic extraction and verification.  

Routing is divided into **global routing** (approximate wire assignment), **detailed routing** (precise wire geometry), and **post-routing checks and repairs** such as antenna rule fixing and metal fill insertion.

## Tools Involved

This stage primarily uses:

- **[OpenROAD](../../tools/openroad.md)** — performs global and detailed routing, timing-driven optimizations, and antenna repairs.  
- **[Odb](../../tools/odb.md)** — handles database-level modifications such as adding or removing obstructions, diode insertion, and wirelength reporting.  
- **[Checker](../../tools/checker.md)** *(optional)* — validates routing connectivity and DRC compliance.

LibreLane coordinates these tools to ensure design-rule correctness and timing preservation throughout the routing process.

## Sequence of Steps

The routing stage in LibreLane executes the following sub-steps:

38. **OpenROAD.GlobalRouting** — generates a coarse routing guide for each net.  
39. **OpenROAD.CheckAntennas** — identifies potential antenna violations before detailed routing.  
40. **OpenROAD.RepairDesignPostGRT** — performs minor optimizations after global routing.  
41. **Odb.DiodesOnPorts** — inserts antenna diodes on ports if required.  
42. **Odb.HeuristicDiodeInsertion** — adds diodes based on heuristics for antenna protection.  
43. **OpenROAD.RepairAntennas** — fixes detected antenna violations by rerouting or adding diodes.  
44. **OpenROAD.ResizerTimingPostGRT** — resizes cells to fix post-routing timing issues.  
45. **OpenROAD.STAMidPNR-3** — performs mid-flow static timing analysis after routing adjustments.  
46. **OpenROAD.DetailedRouting** — performs full detailed routing with DRC-compliant geometries.  
47. **Odb.RemoveRoutingObstructions** — removes temporary blockages used during routing.  
48. **OpenROAD.CheckAntennas-1** — performs a final antenna rule check.  
49. **Checker.TrDRC** — runs top-level design rule checks.  
50. **Odb.ReportDisconnectedPins** — reports any pins left unconnected.  
51. **Checker.DisconnectedPins** — validates pin connectivity.  
52. **Odb.ReportWireLength** — generates wirelength statistics.  
53. **Checker.WireLength** — verifies expected wirelength and congestion metrics.  
54. **OpenROAD.FillInsertion** — performs metal fill insertion for uniform density.  
55. **Odb.CellFrequencyTables** — generates cell usage and density summaries.

## Configuration Overview

Routing behavior is defined in the project’s `config.json`.  
A typical configuration includes:

```json
{
  "ROUTING_LAYERS": "met1-met5",
  "ROUTING_STRATEGY": "timing_driven",
  "ROUTING_DIODE_INSERTION": true,
  "ROUTING_ANTENNA_REPAIR": true,
  "ROUTING_FILL_INSERTION": true,
  "ROUTING_CONGESTION_EFFORT": "high",
  "ROUTING_DRC_MAX_VIOLATIONS": 0
}
```

Explanation:

- ROUTING_LAYERS — defines the routing metal stack range.
- ROUTING_STRATEGY — sets the optimization target (timing_driven or wirelength_driven).
- ROUTING_DIODE_INSERTION — enables antenna diode insertion.
- ROUTING_FILL_INSERTION — enables dummy fill generation for metal density.
- ROUTING_DRC_MAX_VIOLATIONS — defines the acceptable number of DRC errors (usually 0).

LibreLane automatically translates these settings into OpenROAD routing commands and Odb scripts for post-routing operations.

## Example: Running Only the Routing Step

To execute only the routing stage within LibreLane:
```
librelane run -d my_design -f routing
```

This will:

1. Load the placed and clocked design from previous stages.
2. Perform global routing using OpenROAD’s FastRoute engine.
3. Execute detailed routing with TritonRoute.
4. Repair antenna and timing violations.
5. Insert metal fill shapes to ensure density compliance.

You can visualize the routed design using:
```
openroad -gui results/routing/my_design.def
```

All generated logs and reports will appear under:
```
results/routing/
reports/routing/
logs/routing/
```
## Expected Outputs

- Routed DEF: results/routing/<design>.def
- Routed GDS (optional): results/routing/<design>.gds
- Antenna reports: reports/routing/antenna.rpt
- Wirelength statistics: reports/routing/wirelength.rpt
- DRC summary: reports/routing/drc_summary.rpt
- Log files: logs/routing/openroad.log

## Common Issues and Checks

- Routing congestion: lower placement density or enable congestion-driven routing (ROUTING_CONGESTION_EFFORT = "high").
- Antenna violations: verify that diode cells exist in the standard-cell library and that antenna repair is enabled.
- Unconnected pins: check pin naming consistency between macro LEF and top-level netlist.
- DRC errors after fill insertion: reduce fill density or adjust layer constraints in the technology file.
- Timing regressions: re-run OpenROAD.ResizerTimingPostGRT or slightly relax constraints if margins are small.