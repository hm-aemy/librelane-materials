# OpenROAD - GDS Export

GDS export is the final stage of the physical implementation process.
After placement, routing, and parasitic extraction, the design is converted from the internal OpenDB database or DEF representation into a GDSII file, which is the standard format for tape-out and mask generation.

### Official documentation:

- OpenROAD GDS Export (write_gds)
- OpenROAD-flow-scripts GDS Export Tutorial
- OpenDB API Reference

## 1. Overview

The GDS export step consolidates all physical data —
cell placements, routing geometries, and technology layers — into a single GDSII file.

| Input    | Description                                      |
| -------- | ------------------------------------------------ |
| `*.lef`  | Technology and standard-cell layer definitions   |
| `*.def`  | Final placed and routed layout                   |
| `*.lib`  | Liberty timing library (optional for annotation) |
| `OpenDB` | Internal database built by OpenROAD              |

| Output       | Description                                |
| ------------ | ------------------------------------------ |
| `design.gds` | Final GDSII file for sign-off and tape-out |

## 2. Basic GDS Export Example
```
# Load tech and design data
read_lef tech.lef
read_lef stdcells.lef
read_def results/routed.def

# Write final GDSII layout
write_gds results/design.gds
```

This creates results/design.gds that can be opened in KLayout or Magic for verification and inspection.

👉 Reference example:
write_gds1.tcl

## 3. Including Macro GDS Files

If your design contains hard macros or IP blocks (e.g., SRAMs, PLLs),
their GDS files must be included before export to ensure proper hierarchy.
```
read_lef tech.lef
read_lef stdcells.lef
read_lef sram_macro.lef
read_def results/routed.def

# Load macro GDS
read_gds macros/sram_macro.gds

# Export complete chip layout
write_gds results/top_with_macros.gds
```

Important:

- The macro’s LEF and GDS must share consistent cell names.
- All included GDS files are merged into the final top_with_macros.gds.

## 4. Hierarchical vs. Flattened GDS

By default, OpenROAD exports a hierarchical GDS, preserving cell structure.
For smaller test chips, you can flatten the hierarchy:
```
write_gds -flatten results/design_flat.gds
```
| Mode             | Description                                                    |
| ---------------- | -------------------------------------------------------------- |
| **Hierarchical** | Recommended for large designs; smaller file size.              |
| **Flattened**    | Easier for inspection but much larger; used for simple blocks. |

## 5. Merging with Padframe or Top-Level

If your core design is part of a larger SoC or includes an IO ring, you can merge multiple GDS files using:
```
merge_gds -input {core.gds pad_ring.gds} -output results/chip_top.gds
```

This command combines layouts hierarchically under a new top cell.

## 6. Optional — Write OASIS Format

For modern tape-outs, OASIS format may be preferred due to smaller file sizes.
```
write_oas results/design.oas
```

OASIS files can be loaded directly into KLayout and many foundry tools.

## 7. Example Full Script
```
# === GDS Export ===
read_lef tech.lef
read_lef stdcells.lef
read_def results/routed.def
read_gds macros/sram_macro.gds

# Optional flatten
write_gds -flatten results/design_flat.gds

# Standard hierarchical export
write_gds results/design.gds
write_oas results/design.oas

# Save OpenDB snapshot
write_db results/final.odb
```
## 8. Outputs
| File              | Description                                    |
| ----------------- | ---------------------------------------------- |
| `design.gds`      | Final hierarchical GDSII layout                |
| `design_flat.gds` | Optional flattened layout                      |
| `design.oas`      | Optional OASIS layout                          |
| `final.odb`       | OpenDB snapshot for later analysis             |
| `gds_export.log`  | Export log (layer mapping and cells processed) |

## 9. Post-Export Verification

After generating the GDS file, verify it with KLayout or Magic before sign-off.

Example: Open GDS in KLayout
```
klayout design.gds &
```

Example: Run DRC/LVS
```
klayout -b -r drc_check.lydrc -rd layout=design.gds -rd report=drc.lyrdb
netgen -batch lvs "layout/design.spice top" "schematic/top.spice top" setup.tcl
```

Checks to perform before tape-out:

- DRC violations (width, spacing, enclosure)
- LVS comparison against schematic
- Antenna violations
- Layer naming consistency

## 10. Troubleshooting Tips
| Issue                 | Cause                  | Solution                                 |
| --------------------- | ---------------------- | ---------------------------------------- |
| Missing macros in GDS | Macro GDS not loaded   | Add `read_gds <macro>.gds` before export |
| Wrong layer mapping   | PDK layer map mismatch | Ensure correct `.map` or `.lef` from PDK |
| File too large        | Flattened export       | Use hierarchical mode or OASIS format    |
| LVS mismatch          | Cell name differences  | Verify symbol names in LEF and GDS       |
| DRC errors            | Routing incomplete     | Re-run detailed routing before export    |

## 11. Summary of Key Commands
| Command     | Description                         |
| ----------- | ----------------------------------- |
| `write_gds` | Export layout in GDSII format       |
| `write_oas` | Export layout in OASIS format       |
| `read_gds`  | Import macro or top-level GDS files |
| `merge_gds` | Combine multiple GDS files into one |
| `write_db`  | Save OpenDB snapshot for reuse      |

## 12. Next Step

After exporting the GDS, proceed to Verification and Sign-off, where you perform:

- DRC/LVS using KLayout or Magic
- Timing and parasitic re-validation
- Final tape-out packaging

👉 See also:
{doc}verification