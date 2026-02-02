# Step II — Synthesis

The **Synthesis** stage transforms the RTL design (usually written in Verilog) into a **gate-level netlist** that uses cells from the target standard-cell library.  
This step ensures that the design is functionally equivalent to the RTL description and that it adheres to the logical and timing constraints defined by the user.

## Tools Involved

This stage primarily uses two tools:

- **[Yosys](../../tools/yosys.md)** — responsible for logic synthesis, technology mapping, and generation of the Verilog and JSON netlists.  
- **[OpenROAD](../../tools/openroad.md)** — performs pre-layout timing checks to ensure constraint consistency before physical design starts.

LibreLane wraps these tools through configurable steps, allowing full customization via the `config.json` file.

## Sequence of Steps

During synthesis, LibreLane executes the following sub-steps:

1. **Yosys.JsonHeader** — generates a JSON header with the design hierarchy.  
2. **Yosys.Synthesis** — runs the Yosys synthesis script to produce the gate-level netlist.  
3. **Checker.YosysUnmappedCells** — verifies that all logic elements have been mapped to standard cells.  
4. **Checker.YosysSynthChecks** — runs logical consistency checks.  
5. **Checker.NetlistAssignStatements** — ensures the synthesized netlist has no residual “assign” constructs.  
6. **OpenROAD.CheckSDCFiles** — validates that timing constraint files (SDC) are consistent.  
7. **OpenROAD.CheckMacroInstances** — ensures macro instances are correctly defined.  
8. **OpenROAD.STAPrePNR** — performs static timing analysis before physical design.

## Configuration Overview

You can control the synthesis process through the project’s main configuration file, typically named `config.json`.  
The relevant parameters include:

```json
{
  "DESIGN_NAME": "my_design",
  "TOP_MODULE": "top",
  "CLOCK_PORT": "clk",
  "CLOCK_PERIOD": 10.0,
  "SYNTH_TOOL": "yosys",
  "SYNTH_STRATEGY": "AREA 0",
  "TECH": "sky130hd"
}
```

The "SYNTH_STRATEGY" and "TECH" parameters determine which synthesis script and standard-cell library are used.
For advanced setups, custom Yosys synthesis scripts can be specified using "SYNTH_SCRIPT".

## Example: Running Only the Synthesis Step

To execute only the synthesis step within the LibreLane flow, you can use the following command:

```
librelane run -d my_design -f synthesis
```

This command will:

1. Load the project configuration.
2. Run Yosys for RTL-to-gate synthesis.
3. Execute all synthesis checkers.
4. Perform timing checks using OpenROAD.

After completion, the generated gate-level netlist can be found at:

```
results/synthesis/my_design.synthesis.v
```

Additional reports are available in:

```
reports/synthesis/
```

You can inspect synthesis logs, timing reports, and unmapped-cell summaries there.

## Expected Outputs

- Gate-level netlist: results/synthesis/&lt;design&gt;.synthesis.v
- JSON netlist: results/synthesis/&lt;design&gt;.json
- Yosys log: logs/synthesis/yosys.log
- Timing reports (OpenROAD): reports/synthesis/sta_pre_pnr.rpt

## Common Issues and Checks

- Ensure all module ports are properly declared and match the top-level Verilog.
- Check that your .lib and .lef files for the target technology are accessible.
- If warnings about unmapped cells appear, review your synthesis library path and mapping file.
- Review yosys.log and sta_pre_pnr.rpt for early timing violations.