# OpenROAD

OpenROAD is an open-source tool for digital physical implementation, providing a unified environment to perform layout-related tasks from floorplanning to sign-off.
Unlike a flow manager such as OpenROAD-flow-scripts or LibreLane, OpenROAD itself is a tool — an interactive and scriptable engine that executes each design step independently. The full documentation can be found in [here](1017).

### Overview

OpenROAD integrates multiple algorithms under one tool, allowing designers to perform physical design tasks in a modular way.
Each step can be run individually through Tcl commands or combined in larger automated flows.

| Step                     | Purpose                                               | Typical Script  |
| ------------------------ | ----------------------------------------------------- | --------------- |
| **Floorplanning**        | Define die/core dimensions, IO pins, and power rings. | `floorplan.tcl` |
| **Placement**            | Place and optimize standard cells.                    | `placement.tcl` |
| **CTS & Optimization**   | Build clock tree and fix timing violations.           | `cts.tcl`       |
| **Routing**              | Route all nets across metal layers.                   | `route.tcl`     |
| **Parasitic Extraction** | Extract RC parasitics for STA and sign-off.           | `rcx.tcl`       |
| **GDS Export**           | Write final layout to GDSII.                          | `write_gds.tcl` |
| **Verification**         | Check DRC, LVS, and timing closure.                   | `signoff.tcl`   |

## Example usage

OpenROAD can be launched in batch or interactive mode:

Batch mode
```
openroad -exit scripts/place_route.tcl
```
Interactive mode
```
openroad
% read_lef tech/merged.lef
% read_def results/floorplan.def
% place_design
% gui_show
```

## Integration within larger flows

OpenROAD can be used directly or as part of an automated pipeline:

- OpenROAD-flow-scripts (ORFS): provides Makefile-based automation around OpenROAD steps.
- LibreLane: wraps OpenROAD into a reproducible flow for Sky130 and other open PDKs.
- Custom flows: can source only the required scripts (e.g., placement + routing only).

## Available documentation

For each OpenROAD design stage:

- {doc}`floorplaning`
- {doc}`placement`
- {doc}`cts-optimization`
- {doc}`routing`
- {doc}`parasitic_extraction`
- {doc}`gds_export`
- {doc}`verification`

```{toctree}
:glob:
:hidden:

floorplaning.md
placement.md
cts-optimization.md
routing.md
parasitic_extraction.md
gds_export.md
verification.md
```