# Xschem

Xschem is an open-source schematic capture tool widely used in analog, digital, and mixed-signal design flows.
It allows you to create hierarchical schematics, define circuit parameters, and export simulation and layout netlists compatible with open-source tools such as Ngspice, Xyce, and OpenROAD.

## Key features

- Hierarchical schematic design with symbol-based blocks.
- Parameterized device instances (transistors, resistors, etc.).
- SPICE and Verilog netlist generation.
- Integration with Ngspice and Xyce for circuit simulation.
- Layout verification interface for Magic and KLayout (LVS-ready).
- Netlisting options for mixed-signal co-design (SPICE + Verilog-A/AMS).
- Library and symbol management with technology-specific .sym files.

## Project organization
```
design/
├─ xschem/
│  ├─ symbols/         # Custom symbols (.sym)
│  ├─ schematics/      # Schematic sources (.sch)
│  ├─ netlists/        # Generated netlists (.spice, .v)
│  ├─ tests/           # Testbenches for simulations
│  └─ xschemrc         # Configuration file (library paths, colors, etc.)
└─ magic/
   └─ layout.mag       # Layout counterpart for LVS
```
## Running Xschem

Xschem can be used interactively via its GUI or in batch mode to automatically generate netlists.

### Launch the GUI
```
xschem &
```

### Generate a SPICE netlist (batch mode)
```
xschem -n -r -x -q -s schematics/opamp.sch -o netlists/opamp.spice
```

### Flags:

| Option | Meaning                         |
| ------ | ------------------------------- |
| `-n`   | no GUI                          |
| `-r`   | recursive (include subcircuits) |
| `-x`   | extract hierarchy               |
| `-q`   | quiet mode                      |
| `-s`   | schematic path                  |
| `-o`   | output path                     |



### Netlisting modes

| Mode                | Output format   | Typical use                                    |
| ------------------- | --------------- | ---------------------------------------------- |
| **SPICE netlist**   | `.spice`        | Analog simulation in Ngspice/Xyce              |
| **Verilog netlist** | `.v`            | Digital co-simulation or PnR                   |
| **Mixed netlist**   | `.spice` + `.v` | Mixed-signal simulations (SPICE + Verilog-AMS) |

### Example: generating a Verilog netlist for OpenROAD
```
xschem -n -r -x -q -o netlists/top.v schematics/top.sch
```

This netlist can then be canonicalized (removing power pins, adding (* keep *)) before synthesis or PnR.

## Symbol hierarchy and connectivity

- Each schematic symbol (.sym) defines pins, attributes, and optional parameters (e.g., W, L, model).
- Hierarchical blocks are created by referencing .sym files in upper-level schematics.
- Parameters can be overridden at the instance level using the Properties dialog.
- Global nets (e.g., vdd!, gnd!) are resolved automatically across hierarchy.

Tip: keep your symbol names consistent with layout devices (important for LVS).

## Simulation setup (with Ngspice)

Create a testbench schematic, e.g. tests/opamp_tb.sch, and define .tran, .ac, or .dc statements.

To simulate directly from Xschem:

1. Go to Simulation → Run Ngspice.
2. Check output in the Xschem terminal window.
3. View results in Ngspice GUI or external viewers (e.g., GTKWave with .raw export).

Example of .sim block inside schematic:
```
*.control
tran 1u 2m
plot v(out) v(in)
.endc
.end
```

## Layout and LVS integration

Xschem works together with Magic or KLayout for layout-versus-schematic (LVS) checks.

### Export schematic netlist for LVS
```
xschem -n -r -x -q -o netlists/opamp_lvs.spice schematics/opamp.sch
```

### Run LVS (with Netgen)
```
netgen -batch lvs "layout/opamp.spice opamp" "netlists/opamp_lvs.spice opamp" \
  $PDK_ROOT/sky130A/libs.tech/netgen/sky130_setup.tcl
```

## Example workflow: from schematic to layout
| Step               | Tool        | Output                  |
| ------------------ | ----------- | ----------------------- |
| Schematic capture  | **Xschem**  | `.sch`                  |
| Netlist generation | **Xschem**  | `.spice` / `.v`         |
| Layout design      | **Magic**   | `.mag`                  |
| LVS check          | **Netgen**  | Report (match/mismatch) |
| DRC check          | **KLayout** | Report                  |
| Simulation         | **Ngspice** | Waveforms               |

## Tips and best practices

- Keep a consistent pin naming between schematic and layout.
- Use flat hierarchy for LVS if needed:
xschem -r -x -q -o flat_netlist.spice schematic.sch.
- Annotate parameters directly in the symbol (e.g., W=1u L=0.5u).
- Use global labels (vdd!, gnd!) for power networks.
- Keep your xschemrc up to date with library paths for your PDK.
- For digital co-design: export Verilog and verify in Yosys/OpenROAD.

## Integration with other tools
| Tool               | Integration purpose                              |
| ------------------ | ------------------------------------------------ |
| **Ngspice / Xyce** | Analog simulation                                |
| **Yosys**          | Netlist verification or gate-level co-simulation |
| **Magic**          | Layout editing and DRC                           |
| **KLayout**        | Visualization and DRC                            |
| **Netgen**         | LVS comparison                                   |
| **OpenROAD**       | Digital PnR (via Verilog netlist export)         |

## Further reading