# Step VII — Parasitic Extraction

The **Parasitic Extraction** stage characterizes the real electrical behavior of the routed design by calculating parasitic resistances (R) and capacitances (C) present in interconnects and vias.  
This information is essential for accurate **timing**, **power**, and **signal integrity** analysis before tapeout.

LibreLane performs parasitic extraction and subsequent analysis using **OpenROAD**, which integrates the **OpenRCX** extraction engine and the internal **IR drop** reporting system.

---

## Tools Involved

This stage mainly uses:

- **[OpenROAD](../../tools/openroad.md)** — runs the *OpenRCX* module for parasitic extraction, static timing analysis, and IR drop evaluation.  
- **[Checker](../../tools/checker.md)** *(optional)* — validates extraction reports and ensures data consistency across tools.

---

## Sequence of Steps

The following sub-steps are executed during parasitic extraction:

56. **OpenROAD.RCX** — extracts parasitic R and C values for interconnects and creates a SPEF file.  
57. **OpenROAD.STAPostPNR** — performs static timing analysis using extracted parasitics (post-layout STA).  
58. **OpenROAD.IRDropReport** — computes voltage drops across the power distribution network and generates an IR drop report.

---

## Configuration Overview

Extraction behavior and analysis parameters are defined in the `config.json` file.  
A typical configuration block looks like this:

```json
{
  "RCX_CORNER": "typical",
  "RCX_USE_COUPLING_CAP": true,
  "RCX_REPORT_DETAIL": "summary",
  "IRDROP_ANALYSIS_ENABLE": true,
  "IRDROP_NETS": ["VDD", "VSS"],
  "IRDROP_MAX_DROP": 0.05
}
```

Explanation:

- RCX_CORNER — selects the process corner for extraction (e.g., typical, slow, fast).
- RCX_USE_COUPLING_CAP — enables coupling capacitance extraction for signal integrity.
- RCX_REPORT_DETAIL — controls report verbosity (summary or full).
- IRDROP_ANALYSIS_ENABLE — enables IR drop estimation and reporting.
- IRDROP_NETS — specifies the power nets to be analyzed.
- IRDROP_MAX_DROP — defines the maximum acceptable voltage drop (in volts).

## Example: Running Only the Parasitic Extraction Step

To perform only parasitic extraction and analysis:
```
librelane run -d my_design -f parasitic-extraction
```

This command will:

1. Load the fully routed design.
2. Extract parasitics using the OpenRCX engine.
3. Run post-layout static timing analysis (STA).
4. Generate IR drop and timing reports.
5. Produce SPEF and RC data for later verification or signoff.

You can visualize extracted parasitics within OpenROAD using:
```
openroad -gui results/parasitic-extraction/my_design.def
```
## Expected Outputs

- SPEF file: results/parasitic-extraction/&lt;design&gt;.spef
- Post-layout timing report: reports/parasitic-extraction/sta_post_pnr.rpt
- IR drop report: reports/parasitic-extraction/ir_drop.rpt
- OpenROAD log: logs/parasitic-extraction/openroad.log

These outputs are used directly in the final verification and signoff stages.

## Common Issues and Checks

- Missing parasitic data: ensure that metal layer definitions (tech.lef) and extraction corners are correctly configured.
- Incorrect RC scaling: verify that RCX_CORNER matches the target PVT corner.
- High IR drop values: review PDN configuration and ensure sufficient via density and strap width.
- Timing degradation: expected after RC extraction — check sta_post_pnr.rpt and re-optimize if necessary.
- Large SPEF files: use "RCX_REPORT_DETAIL": "summary" to limit file size.