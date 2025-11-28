# Yosys

Yosys is an open-source tool for logic synthesis of Verilog and SystemVerilog designs.
It transforms RTL descriptions into gate-level netlists — either technology-independent or mapped to a specific standard-cell library (using Liberty files and the ABC engine).

## Typical uses

- RTL linting and structure checking.
- Generic synthesis (without technology mapping).
- Technology mapping using Liberty and ABC.
- Hierarchical synthesis for multi-module projects.
- Blackboxing of macros (e.g., SRAM, PLL).
- Report and artifact generation (Verilog, JSON, BLIF, EDIF).

## Inputs and Outputs

| Type        | Description                                                                                                          |
| ----------- | -------------------------------------------------------------------------------------------------------------------- |
| **Inputs**  | Verilog/SystemVerilog RTL, include files, `defines`, and standard-cell Liberty (`.lib`) files for technology mapping |
| **Outputs** | Gate-level Verilog, JSON/BLIF/EDIF netlists, synthesis reports (`stat`), and logs                                    |


## Project structure

```
design/
├─ rtl/           # RTL sources
├─ tech/          # Liberty (.lib) files
├─ scripts/       # Yosys synthesis scripts
└─ build/         # Generated netlists and reports
```

## Recipe 1 — Lint and RTL sanity check (pre-synthesis)

Goal: detect structural issues before synthesis.

scripts/lint.ys
```
# Read Verilog sources (-sv for SystemVerilog)
read_verilog -Irtl rtl/top.v rtl/mod_a.v rtl/mod_b.v

# Resolve hierarchy and check for missing top
hierarchy -auto-top -check

# Prepare design
proc; opt

# Structural checks
check -assert

# Print basic statistics
stat

# Export a generic JSON netlist for inspection
write_json build/design_generic.json
```
Run:
```
yosys -s scripts/lint.ys | tee build/lint.log
```
## Recipe 2 — Generic synthesis (no technology mapping)

Goal: generate a technology-independent logical netlist.

scripts/synth_generic.ys
```
read_verilog -Irtl rtl/top.v rtl/**/*.v
hierarchy -top top -check

# Frontend processing
proc; opt; fsm; opt; memory; opt

# Generic synthesis
synth -top top

# Reports and netlists
stat -tech
write_json build/top_generic.json
write_verilog -noattr build/top_generic.v
```

Use case: for early RTL validation, gate-level simulation, or as input to technology-agnostic analysis.

## Recipe 3 — Technology-mapped synthesis (Liberty + ABC)

Goal: produce a gate-level netlist mapped to a specific standard-cell library, ready for physical design.

scripts/synth_techmap.ys

```
# 1) Read RTL
read_verilog -Irtl rtl/top.v rtl/**/*.v
hierarchy -top top -check

# 2) Generic optimization
proc; opt; fsm; opt; memory; opt

# 3) Read technology library
read_liberty -lib tech/standardcells.lib

# 4) Map logic to cells using ABC
dfflibmap -liberty tech/standardcells.lib
abc -liberty tech/standardcells.lib

# 5) Clean and finalize
clean -purge
setundef -zero

# 6) Reports and outputs
stat -liberty tech/standardcells.lib
write_verilog -noattr build/top_gatelevel.v
write_json build/top_gatelevel.json
```
Run:
```
yosys -s scripts/synth_techmap.ys | tee build/synth.log
```

### Integration with OpenROAD:

- The resulting top_gatelevel.v becomes the input for OpenROAD’s floorplanning step.
- OpenROAD also needs the LEF and Liberty files for physical and timing analysis (via OpenSTA).

## Working with hierarchy and blackboxes

- Define the top module:
```
hierarchy -top top -check
```

- Mark a module as blackbox (e.g., SRAM/PLL):
```
read_verilog -lib rtl/sram_stub.v
```

- Preserve specific modules or nets:

    - In RTL:
    ```
    (* keep, dont_touch = "true" *) wire my_signal;
    ```

    - In Yosys: avoid flattening or optimization on critical modules.

## Handling multi-file and include paths

Example:
```
read_verilog -sv \
  -Irtl/includes \
  -D SYNTHESIS \
  rtl/top.sv rtl/core/**/*.sv
```

## Reports and debugging commands
| Command             | Purpose                                      |
| ------------------- | -------------------------------------------- |
| `stat`              | Print synthesis statistics                   |
| `check -assert`     | Ensure structural correctness                |
| `tee -o report.txt` | Save output of a command                     |
| `show`              | Visualize the synthesized netlist (Graphviz) |
| `write_json`        | Export internal representation for debugging |

## Practical tips

- Resets: prefer synchronous resets; asynchronous or mixed styles can confuse mapping.
- FSM / memory passes: keep them enabled (fsm, memory) before ABC mapping.
- Unknowns (x) handling: use setundef -zero if the downstream tools cannot handle undefined states.
- Attributes: use (* keep *) or (* dont_touch = "true" *) to preserve cells, debug logic, or blackboxes.
- Common warnings: messages like “wire has no driver” or “multiple drivers” are usually structural; rerun hierarchy -check and lint first.

## Minimal synthesis-to-PnR chain

1. Yosys → gate-level Verilog (top_gatelevel.v) mapped to .lib.
2. OpenROAD → placement, CTS, routing (using .lef + .lib).
3. OpenSTA → timing verification with SPEF/SDF.
4. Magic / KLayout / Netgen → physical sign-off (DRC/LVS).

## Further reading

- Yosys Documentation
- Clifford Wolf’s YosysHQ GitHub
- ASIC World – Logic Synthesis Basics
- LibreLane and OpenROAD-flow-scripts Integration