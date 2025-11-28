# Step IX — Verification

The **Verification** stage ensures that the final layout and netlists are **correct, consistent, and manufacturable**.  
It validates that the physical layout adheres to fabrication rules, that the extracted netlist matches the intended schematic, and that timing, connectivity, and electrical specifications are all within acceptable limits.

This step combines **physical verification** (DRC/LVS), **logical equivalence checking**, and **manufacturability analysis** to guarantee a clean, signoff-ready design.

## Tools Involved

This stage integrates several tools and checkers:

- **[Magic](../../tools/magic.md)** — performs *Design Rule Check (DRC)* and layout-to-SPICE extraction.  
- **[KLayout](../../tools/klayout.md)** — executes DRC with the PDK’s rule deck and cross-verifies against Magic.  
- **[Netgen](../../tools/netgen.md)** — performs *Layout Versus Schematic (LVS)* comparison between extracted and synthesized netlists.  
- **[Yosys](../../tools/yosys.md)** — executes logical equivalence checking (*EQY*) between RTL and gate-level netlists.  
- **[Checker](../../tools/checker.md)** — aggregates and analyzes all verification results, detecting violations and summarizing signoff metrics.

## Sequence of Steps

LibreLane performs the following sub-steps during verification:

65. **Magic.DRC** — runs a full DRC using Magic’s internal rule deck.  
66. **KLayout.DRC** — executes a second DRC using KLayout for independent validation.  
67. **Checker.MagicDRC** — parses and summarizes Magic DRC results.  
68. **Checker.KLayoutDRC** — parses and summarizes KLayout DRC results.  
69. **Magic.SpiceExtraction** — extracts a SPICE netlist from the final layout.  
70. **Checker.IllegalOverlap** — checks for illegal overlaps or missing layers.  
71. **Netgen.LVS** — performs layout vs schematic (LVS) comparison.  
72. **Checker.LVS** — interprets LVS results and reports connectivity mismatches.  
73. **Yosys.EQY** — performs logical equivalence checking between RTL and synthesized netlists.  
74. **Checker.SetupViolations** — detects setup timing violations.  
75. **Checker.HoldViolations** — detects hold timing violations.  
76. **Checker.MaxSlewViolations** — reports excessive signal transition times.  
77. **Checker.MaxCapViolations** — reports excessive load capacitance.  
78. **Misc.ReportManufacturability** — generates a manufacturability and readiness summary report.

## Configuration Overview

Verification behavior is configured via entries in `config.json`.  
A typical block might look like this:

```json
{
  "VERIFICATION_RUN_DRC": true,
  "VERIFICATION_RUN_LVS": true,
  "VERIFICATION_RUN_EQY": true,
  "VERIFICATION_RULESET": "default",
  "VERIFICATION_DRC_TOLERANCE": 0.001,
  "LVS_TOP_MODULE": "top",
  "EQY_IGNORE_SIGNALS": ["scan_en", "test_mode"]
}
```

Explanation:

- VERIFICATION_RUN_DRC/LVS/EQY — enable or disable specific verification types.
- VERIFICATION_RULESET — selects a rule deck variant (e.g., “default”, “strict”).
- VERIFICATION_DRC_TOLERANCE — sets the geometric tolerance in microns for DRC comparisons.
- LVS_TOP_MODULE — defines the top module for LVS comparison.
- EQY_IGNORE_SIGNALS — lists signals to ignore during equivalence checking.

## Example: Running Only the Verification Step

To execute only the verification stage:
```
librelane run -d my_design -f verification
```

This will:

1. Run DRC with both Magic and KLayout.
2. Extract the layout netlist with Magic.
3. Compare layout and schematic netlists using Netgen.
4. Perform logical equivalence checking using Yosys EQY.
5. Aggregate timing and manufacturability reports.

After completion, reports are generated in:
```
reports/verification/
```

You can inspect Magic’s DRC results interactively:
```
magic -d XR -T <techfile> results/gds-export/my_design.gds
```

Or visualize LVS mismatches in KLayout using its LVS viewer.

## Expected Outputs

- DRC reports:

    - reports/verification/magic_drc.rpt
    - reports/verification/klayout_drc.rpt

- LVS report: reports/verification/lvs_report.rpt
- EQY report: reports/verification/eqy_report.rpt
- Timing reports: reports/verification/setup_hold.rpt
- Manufacturability summary: reports/verification/manufacturability.rpt
- Logs:

    - logs/verification/magic.log
    - logs/verification/klayout.log
    - logs/verification/netgen.log
    - logs/verification/yosys.log

## Common Issues and Checks

- DRC violations: verify layer rules in Magic and KLayout; small differences may arise from tool-specific rule decks.
- LVS mismatches: confirm that the extracted netlist uses the same naming convention as the schematic.
- EQY mismatches: check ignored signals and ensure synthesis didn’t optimize away functional paths.
- Timing violations: ensure proper constraints in SDC and re-run STA if necessary.
- Manufacturability issues: review the summary report — look for density, enclosure, or antenna warnings.

All violations must be cleared before the design can proceed to fabrication.