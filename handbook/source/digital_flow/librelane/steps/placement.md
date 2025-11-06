# Step IV — Placement

The **Placement** stage determines the exact physical locations of all standard cells, I/O pins, and macros within the floorplan previously defined.  
This process has two phases: **global placement**, which finds an approximate optimal distribution, and **detailed placement**, which fine-tunes cell positions to meet design rules and timing constraints.  
Proper placement directly affects the quality of results (QoR) in routing, timing closure, and overall chip performance.

## Tools Involved

This stage mainly uses:

- **[OpenROAD](../../tools/openroad.md)** — performs both global and detailed placement, I/O pin assignment, legalization, and timing-driven optimizations.  
- **[Odb](../../tools/odb.md)** — handles layout database modifications such as I/O template application, custom macro positioning, and reporting utilities.

LibreLane uses a sequence of placement steps that combine OpenROAD algorithms with Odb adjustments and design checks.

## Sequence of Steps

The following sub-steps are executed during placement:

23. **OpenROAD.GlobalPlacementSkipIO** — performs initial placement excluding I/O cells.  
24. **OpenROAD.IOPlacement** — places I/O pins according to constraints or templates.  
25. **Odb.CustomIOPlacement** — applies user-defined I/O pin arrangements if provided.  
26. **Odb.ApplyDEFTemplate** — merges DEF-based placement templates (optional).  
27. **OpenROAD.GlobalPlacement** — runs the global placement engine optimizing wirelength and congestion.  
28. **Odb.WriteVerilogHeader** — generates a Verilog header for post-placement verification.  
29. **Checker.PowerGridViolations** — ensures the placed cells comply with power grid connectivity rules.  
30. **OpenROAD.STAMidPNR** — runs static timing analysis to validate placement timing.  
31. **OpenROAD.RepairDesignPostGPL** — performs timing-driven design repairs after global placement.  
32. **Odb.ManualGlobalPlacement** — applies manual corrections or additional placement scripts.  
33. **OpenROAD.DetailedPlacement** — finalizes placement by aligning cells to legal sites and minimizing overlaps.

## Configuration Overview

The placement process is controlled through parameters in the `config.json` file:

```json
{
  "PL_RANDOM_INITIAL_PLACEMENT": false,
  "PL_TARGET_DENSITY": 0.7,
  "PL_IO_PLACEMENT_MODE": "automatic",
  "PL_SKIP_IO": false,
  "PL_MACRO_PLACE_FILE": "macros.def"
}
```
Key parameters:

- PL_TARGET_DENSITY — sets the desired standard-cell density target for placement.
- PL_IO_PLACEMENT_MODE — controls automatic or manual I/O pin placement.
- PL_MACRO_PLACE_FILE — optional file defining macro positions in DEF format.
- PL_RANDOM_INITIAL_PLACEMENT — can be set to true for randomized seed placement (useful for exploration).

LibreLane passes these options to OpenROAD placement scripts, allowing fully automated or partially user-guided flows.

## Example: Running Only the Placement Step

To run only the placement stage within LibreLane:
```
librelane run -d my_design -f placement
```

This will:

1. Load the floorplan from the previous stage.
2. Perform global placement, I/O placement, and legalization.
3. Run timing checks and design repairs.
4. Save a placed DEF file for the next stage (CTS).

Results are stored in:
```
results/placement/
```

To visualize the placement interactively in OpenROAD:
```
openroad -gui results/placement/my_design.def
```
## Expected Outputs

- DEF file: results/placement/&lt;design&gt;.def
- Verilog header: results/placement/&lt;design&gt;.vh
- Timing report: reports/placement/sta_mid_pnr.rpt
- Logs: logs/placement/openroad.log
- Checker reports: reports/placement/power_grid_violations.rpt

## Common Issues and Checks

- If the placement density is too high, routing congestion will appear in later stages; lower PL_TARGET_DENSITY.
- Check that macros and blockages do not overlap placement rows.
- For designs with many I/O pins, review io.def or use Odb.CustomIOPlacement for control.
- Always verify the timing report (sta_mid_pnr.rpt) for early setup/hold issues before proceeding to CTS.