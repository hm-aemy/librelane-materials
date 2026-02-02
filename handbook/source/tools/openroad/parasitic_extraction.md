# OpenROAD - Parasitic Extraction

Parasitic extraction (PEX) calculates the resistance (R) and capacitance (C) of routed interconnects.
These parasitics directly impact signal delay and must be included for accurate Static Timing Analysis (STA) and power estimation.
In OpenROAD, extraction is performed with the integrated OpenRCX engine.

### Official documentation:

- OpenRCX (Parasitic Extraction) README
- OpenROAD-flow-scripts Extraction Tutorial

## 1. Overview

OpenRCX reads the routed design and computes parasitic RC values for all nets based on the technology’s metal stack definitions.
The extracted data is exported as a SPEF (Standard Parasitic Exchange Format) file, used later by OpenSTA for timing verification.

| Step | Task                           | Output                |
| ---- | ------------------------------ | --------------------- |
| 1    | Load technology and routed DEF | Input geometry        |
| 2    | Run RC extraction with OpenRCX | Internal RC network   |
| 3    | Write results in SPEF format   | design.spef         |
| 4    | Use SPEF in STA                | Back-annotated timing |

## 2. Basic Extraction Example

# Load technology and design data
read_lef tech.lef
read_lef stdcells.lef
read_def results/routed.def

# Run parasitic extraction
extract_parasitics -ext_model_file rcx_rules.json

# Write parasitic data to SPEF
write_spef results/design.spef


This process extracts all RC networks from the routed layout using parameters defined in the RCX model file (rcx_rules.json or .ext.rc), which describes the resistance and capacitance per metal layer.

## 3. RC Model Configuration

The extraction model is defined by the PDK and determines layer thickness, spacing, and permittivity parameters.

Example of rcx_rules.json

{
  "version": "1.0",
  "layers": {
    "metal1": { "r": 0.09, "c": 0.18 },
    "metal2": { "r": 0.06, "c": 0.14 },
    "metal3": { "r": 0.04, "c": 0.11 }
  }
}


👉 The actual file is provided by your open PDK under:
$PDK_ROOT/$pdk/libs.tech/openroad/rcx/

Tip: Always use the PDK’s official RCX file — do not edit it unless you are characterizing a new technology.

## 4. Advanced Example with SPEF Compression and Timing Annotation

    # Extract parasitics
    extract_parasitics -ext_model_file tech/rcx_rules.json

    # Write full or compressed SPEF
    write_spef results/design_full.spef
    write_spef -compress results/design_compressed.spef

    # Back-annotate timing
    read_spef results/design_full.spef
    report_timing -path_delay min_max

Options for extract_parasitics:

| Option             | Description                                      |
| ------------------ | ------------------------------------------------ |
| -ext_model_file  | Path to RC model file                            |
| -coupling_cap    | Include coupling capacitances                    |
| -no_coupling_cap | Extract only ground caps                         |
| -corner          | Specify PVT corner for extraction (if supported) |

## 5. SPEF and SDF Outputs
| File          | Description                                             |
| ------------- | ------------------------------------------------------- |
| design.spef | Full parasitic network (R and C per net)                |
| design.sdf  | Optional delay annotation for simulation                |
| rcx.log     | Extraction log (runtime, nets processed, layer summary) |


OpenROAD can also write an SDF (Standard Delay Format) file for post-layout gate-level simulation:

write_sdf results/design.sdf

## 6. Using SPEF with OpenSTA (Timing Verification)

After parasitic extraction, you can perform timing analysis with OpenSTA to check the impact of interconnect delays.

# Load design and timing constraints
read_liberty tech.lib
read_verilog results/top_gatelevel.v
read_def results/routed.def
read_spef results/design.spef
read_sdc constraints.sdc

# Run STA
report_timing
report_worst_slack


👉 Reference module:
OpenSTA Integration Guide

## 7. Example Full Script

read_lef tech.lef
read_lef stdcells.lef
read_def results/routed.def

# === Parasitic Extraction ===
extract_parasitics -ext_model_file tech/rcx_rules.json
write_spef results/design.spef
write_sdf results/design.sdf

# === Timing Validation (optional) ===
read_liberty tech.lib
read_spef results/design.spef
report_timing

## 8. Visualization in GUI (optional)

Launch OpenROAD GUI to view parasitic distributions per net:

openroad -gui
% read_def results/routed.def
% read_spef results/design.spef
% gui::highlight_net -name CLK


You can visualize extracted nets, check RC segments, and inspect timing-critical paths.

## 9. Outputs Summary
| Output           | Description                                |
| ---------------- | ------------------------------------------ |
| design.spef    | RC parasitic network                       |
| design.sdf     | Delay annotation for gate-level simulation |
| rcx.log        | Extraction log                             |
| sta_timing.rpt | Optional post-layout STA report            |

## 10. Troubleshooting Tips
| Issue               | Cause                             | Solution                                  |
| ------------------- | --------------------------------- | ----------------------------------------- |
| Missing parasitics  | DEF not fully routed              | Ensure routing completed with TritonRoute |
| Incorrect RC values | Wrong or missing rcx_rules.json | Use correct PDK RC model                  |
| Large SPEF file     | Coupling capacitances included    | Use -no_coupling_cap or compression     |
| Timing mismatch     | Not reloading SPEF before STA     | Always re-run STA after extraction        |

## 11. Summary of Key Commands
| Command              | Description                             |
| -------------------- | --------------------------------------- |
| extract_parasitics | Run OpenRCX extraction                  |
| write_spef         | Write parasitic data to SPEF format     |
| write_sdf          | Generate SDF for post-layout simulation |
| read_spef          | Import SPEF into STA                    |
| report_timing      | Run timing check with parasitics        |

## 12. Next Step

Once parasitic extraction is complete, proceed with Verification, where DRC, LVS, and final timing checks confirm that the design is manufacturable and functionally correct.

👉 See also:
{doc}verification