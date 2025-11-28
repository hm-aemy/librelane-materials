# OpenROAD - Floorplanning

Floorplanning defines the physical boundaries of the chip’s core and die areas, sets the positions for I/O pins, macros, and creates the foundation for power delivery and later placement/routing stages (more information about floorplanning in {doc}`../../concepts/floorplaning`). In OpenROAD, this is done interactively or through scripts using commands such as init_floorplan, place_pins, and pdngen.

### Official documentation:

- [OpenROAD IFP (Floorplan) README](https://openroad.readthedocs.io/en/latest/main/src/ifp/README.html)
- [OpenROAD-flow-scripts Floorplanning Tutorial](https://openroad-flow-scripts.readthedocs.io/en/latest/tutorials/FlowTutorial.html#floorplanning)

## 1. Basic Floorplan Initialization

The init_floorplan command creates the initial die and core regions.
```
# Example: Define floorplan size and core offsets
init_floorplan \
  -die_area {0 0 1000 1000} \
  -core_area {100 100 900 900}
```
Alternatively, you can specify core utilization and aspect ratio instead of absolute coordinates:
```
init_floorplan \
  -utilization 30 \
  -aspect_ratio 1.0 \
  -core_space 10
```

Reference script:
[init_floorplan1.tcl](https://github.com/The-OpenROAD-Project/OpenROAD/blob/master/src/ifp/test/init_floorplan1.tcl)

```{tip}
There are severals test files you can explore in [here](https://github.com/The-OpenROAD-Project/OpenROAD/tree/master/src/ifp/test). try them looking at the results.
```

## 2. Power Grid Generation

After defining the floorplan, Pdngen is used to create the Power Distribution Network (PDN).
It generates metal rings, straps, and vias to deliver stable power to all cells.
```
# Example: Create a core power grid
pdngen pdn.tcl
```

pdn.tcl contains technology-specific parameters like strap width, pitch, and metal layers.

### Example (core_grid_snap.tcl):
```
define_pdn_grid -name core_grid -voltage_domains {core}
add_pdn_stripe -layer metal1 -width 0.48 -pitch 10 -nets {VDD VSS}
add_pdn_ring -layers {metal4 metal5} -widths {2.0 2.0} -spacings {1.0 1.0} -nets {VDD VSS}
```

Reference script: [core_grid_snap.tcl](https://github.com/The-OpenROAD-Project/OpenROAD/blob/master/src/pdn/test/core_grid_snap.tcl)

```{tip}
There are severals test files you can explore in [here](https://github.com/The-OpenROAD-Project/OpenROAD/tree/master/src/pdn/test). try them looking at the results.
```


## 3. Macro Placement

Macros (e.g., SRAMs, PLLs, large IPs) must be placed before standard cells.
Use the macro_placement command to control spacing and halos.
```
# Example: place macros with halo
macro_placement -halo {0.5 0.5}
```

This command ensures macros are separated by 0.5 µm horizontally and vertically, leaving routing resources available.

Example visualization:
```
# Load the example and view results in GUI
cd src/mpl/test/
openroad -gui
source fixed_macros.tcl
```

Reference script: [fixed_macros.tcl](https://github.com/The-OpenROAD-Project/OpenROAD/blob/master/src/mpl/test/fixed_macros.tcl)

```{tip}
There are severals test files you can explore in [here](https://github.com/The-OpenROAD-Project/OpenROAD/tree/master/src/mpl/test). try them looking at the results.
```

## 4. Summary of Key Floorplanning Commands
| Command            | Description                         |
| ------------------ | ----------------------------------- |
| `init_floorplan`   | Define die/core size or utilization |
| `place_pins`       | Automatic pin placement             |
| `macro_placement`  | Place macros and set halo/spacing   |
| `tapcell`          | Insert well and endcap cells        |
| `pdngen`           | Create power grid                   |
| `filler_placement` | Fill empty gaps between cells       |

## 5. Example: Minimal Tcl Floorplan Script
```
read_lef sky130_fd_sc_hd.tlef
read_lef sky130_fd_sc_hd_merged.lef
read_def my_design.def

init_floorplan -utilization 30 -aspect_ratio 1.0 -core_space 10
place_pins -random
macro_placement -halo {0.5 0.5}
pdngen pdn.tcl
tapcell -distance 14
filler_placement {FILLCELL_X1 FILLCELL_X2 FILLCELL_X4}
write_def results/floorplan.def
```
## 6. Typical Outputs
| File            | Description                                      |
| --------------- | ------------------------------------------------ |
| `floorplan.def` | Floorplan DEF file with die/core regions defined |
| `floorplan.odb` | OpenDB database snapshot                         |
| `floorplan.log` | Execution log of floorplan stage                 |
| `pdn.log`       | PDN generation and IR drop analysis log          |
