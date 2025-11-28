# OpenROAD - Routing

Routing connects all placed standard cells, macros, and pins according to the logical netlist.
It is performed in two main stages: global routing and detailed routing, which progressively refine the physical paths of interconnections.
In OpenROAD, these tasks are handled by FastRoute (global) and TritonRoute (detailed).

### Official documentation:

- FastRoute (Global Routing) README
- TritonRoute (Detailed Routing) README
- OpenROAD-flow-scripts Routing Tutorial

## 1. Overview of Routing Flow
| Stage                | Engine                | Description                                                                          |
| -------------------- | --------------------- | ------------------------------------------------------------------------------------ |
| **Global Routing**   | *FastRoute*           | Estimates wire paths and assigns routing regions for each net.                       |
| **Track Assignment** | *TritonRoute-prepass* | Assigns nets to specific routing tracks on each metal layer.                         |
| **Detailed Routing** | *TritonRoute*         | Creates exact wire geometries, vias, and layer transitions, ensuring DRC compliance. |

## 2. Basic Routing Example
```
# Read the design
read_lef tech.lef
read_lef stdcells.lef
read_def results/cts.def

# Perform global and detailed routing
route_design
```

The route_design command executes global routing, track assignment, and detailed routing sequentially.

Equivalent manual sequence:
```
global_route
detailed_route
```

👉 Reference example:
route1.tcl

## 3. Global Routing (FastRoute)

During global routing, OpenROAD computes approximate paths between pins, divides the chip into routing grids, and estimates wire congestion.

Example
```
global_route \
  -guide_file results/guides.guide \
  -overflow_iterations 150 \
  -congestion_iterations 50
```

Options

| Option                   | Description                                      |
| ------------------------ | ------------------------------------------------ |
| `-guide_file`            | File storing routing guides for detailed routing |
| `-overflow_iterations`   | Number of iterations to resolve congestion       |
| `-congestion_iterations` | Iterations to refine congested regions           |


Output: guides.guide — routing guide file defining regions for each net.

👉 More details:
FastRoute documentation

## 4. Detailed Routing (TritonRoute)

Detailed routing creates actual metal paths and vias that connect all nets, following the guides produced by FastRoute.
It also performs DRC checks and reports violations.

Example
```
detailed_route \
  -output_drc results/drc.rpt \
  -output_maze results/route.maze
```

Options

| Option         | Description                                |
| -------------- | ------------------------------------------ |
| `-output_drc`  | Saves the DRC violation report             |
| `-output_maze` | Saves the routing maze graph for debugging |
| `-verbose`     | Enables detailed logging of route progress |

👉 Reference:
TritonRoute README

## 5. Antenna and Via Checks

After detailed routing, OpenROAD can automatically fix antenna violations (caused by long wires connected to gates) and verify via rules.
```
repair_antennas
check_antennas
```

Optionally run after routing:
```
detailed_route -output_drc results/drc_final.rpt
```
## 6. Example: Full Routing Script
```
read_lef tech.lef
read_lef stdcells.lef
read_def results/optimized.def

# === Routing ===
global_route -guide_file results/guides.guide
detailed_route -output_drc results/drc.rpt

# === Post-routing checks ===
repair_antennas
check_antennas

# Export routed design
write_def results/routed.def
write_guides results/guides.guide
write_sdc results/final.sdc
```
## 7. Visualization and Debugging

Launch GUI mode to view routing layers and congestion maps:
```
openroad -gui
% read_def results/routed.def
% gui::show_routing_layers
% gui::show_congestion_heatmap
```

Tips:

- Toggle routing layers using Layer Visibility Panel.
- Use “Heatmap → Routing Congestion” to inspect critical regions.
- Export screenshots for documentation or analysis.

## 8. Outputs
| File           | Description                                |
| -------------- | ------------------------------------------ |
| `routed.def`   | Fully routed layout ready for extraction   |
| `guides.guide` | Routing guides from global router          |
| `drc.rpt`      | DRC violations report from detailed router |
| `antenna.rpt`  | Optional antenna violation report          |
| `routed.odb`   | OpenDB snapshot after routing              |

## 9. Troubleshooting Common Issues
| Issue                  | Cause                                 | Mitigation                                          |
| ---------------------- | ------------------------------------- | --------------------------------------------------- |
| **Unrouted nets**      | Overlaps or missing guides            | Re-run `global_route` with more overflow iterations |
| **High congestion**    | Density too high                      | Reduce utilization or adjust macro spacing          |
| **DRC errors**         | Layer constraints or pin obstructions | Tune routing layers or fix pin locations            |
| **Antenna violations** | Long metal wires                      | Use `repair_antennas`                               |

## 10. Summary of Key Commands
| Command           | Description                                |
| ----------------- | ------------------------------------------ |
| `route_design`    | Run full routing sequence                  |
| `global_route`    | Perform global routing using FastRoute     |
| `detailed_route`  | Perform detailed routing using TritonRoute |
| `repair_antennas` | Fix antenna-related violations             |
| `check_antennas`  | Report antenna issues                      |
| `write_guides`    | Export routing guides                      |
| `write_def`       | Save routed DEF                            |

## 11. Next Step

After routing is completed and the layout is DRC-clean, proceed to Parasitic Extraction to generate the RC networks used for post-layout timing verification and sign-off.

👉 See also:
{doc}parasitic_extraction