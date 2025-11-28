# OpenROAD - Placement

Placement arranges all standard cells inside the defined core area, minimizing total wire length, congestion, and timing violations.
In OpenROAD, placement is performed in multiple stages — global, legalization, and detailed — using the integrated placement engines RePlAce and OpenDP.

### Official documentation:

- OpenROAD Placement (RePlAce) README
- OpenROAD Detailed Placement (OpenDP) README
- OpenROAD-flow-scripts Placement Tutorial

## 1. Overview of Placement Flow

Placement in OpenROAD is executed in three main phases:

| Phase                  | Tool                      | Description                                                                  |
| ---------------------- | ------------------------- | ---------------------------------------------------------------------------- |
| **Global placement**   | *RePlAce*                 | Distributes cells to minimize wire length and congestion (overlaps allowed). |
| **Legalization**       | *OpenDP*                  | Snaps cells to legal rows and removes overlaps.                              |
| **Detailed placement** | *OpenDP / RePlAce refine* | Final local adjustments for better timing and routability.                   |

## 2. Basic Placement Example
```
# Read design and tech data
read_lef tech.lef
read_lef stdcells.lef
read_def results/floorplan.def

# Perform placement
place_design
```

The place_design command automatically performs global placement, legalization, and detailed placement in sequence.

Equivalent manual execution:
```
global_placement
detailed_placement
```

👉 Reference script:
place1.tcl

## 3. Global Placement

During global placement, OpenROAD minimizes the total estimated wire length and balances cell density using RePlAce.

Example:
```
# Global placement with specific density and iterations
global_placement \
  -density 0.7 \
  -init_density_penalty 8.0 \
  -pad_left 2.0 -pad_right 2.0
```

Options

| Option                   | Description                                     |
| ------------------------ | ----------------------------------------------- |
| `-density`               | Target density (0–1). Higher = tighter packing. |
| `-pad_left / -pad_right` | Adds whitespace for routability.                |
| `-init_density_penalty`  | Adjusts spreading strength.                     |

👉 More examples:
RePlAce documentation

## 4. Legalization

Legalization aligns all cells onto legal row sites, removes overlaps, and respects blockages or halos defined during floorplanning.

Example:
```
# Legalize placement
detailed_placement
```

To restrict legalization to specific regions:
```
detailed_placement -region {100 100 800 800}
```

👉 Reference module:
OpenDP (Detailed Placement)

## 5. Incremental and Timing-Driven Placement

After initial placement, the design can be re-optimized to improve timing or congestion before CTS.

Example:
```
# Incremental placement to fix timing
resizer::repair_timing -incremental
```

Alternatively:
```
global_placement -timing_driven
```
## 6. Filler, Tap, and Endcap Cells

To maintain uniform well connections and avoid DRC errors, OpenROAD can insert additional filler and tap cells after placement.
```
tapcell -distance 14
filler_placement {FILLCELL_X1 FILLCELL_X2 FILLCELL_X4}
```

👉 Documentation:
Tapcell Insertion

## 7. Example: Full Placement Script
```
read_lef tech.lef
read_lef stdcells.lef
read_def results/floorplan.def

# === Placement ===
place_design

# or step-by-step:
global_placement -density 0.65
detailed_placement
tapcell -distance 14
filler_placement {FILLCELL_X1 FILLCELL_X2 FILLCELL_X4}

write_def results/placement.def
```
## 8. Outputs
| File               | Description                           |
| ------------------ | ------------------------------------- |
| `placement.def`    | DEF file with placed standard cells   |
| `placement.log`    | Log file with placement statistics    |
| `placement.odb`    | OpenDB database snapshot              |
| `report_place.txt` | Optional timing and congestion report |

## 9. Visualization (Optional)

Launch OpenROAD GUI to inspect cell distribution and density heatmaps:
```
openroad -gui
% read_def results/placement.def
% gui::show_density_heatmap
```

You can toggle display layers (e.g., macro outlines, congestion maps) for debugging routability.

## 10. Summary of Key Commands
| Command              | Description                                 |
| -------------------- | ------------------------------------------- |
| `place_design`       | Perform full placement flow automatically   |
| `global_placement`   | Analytical placement (wirelength & density) |
| `detailed_placement` | Legalization and local refinement           |
| `tapcell`            | Insert well and endcap cells                |
| `filler_placement`   | Fill remaining empty sites                  |

## 11. Next Step

After placement is complete, proceed with Clock Tree Synthesis (CTS), where buffers are added to distribute the clock signal evenly across the design.

👉 See also:
{doc}cts_and_optimization