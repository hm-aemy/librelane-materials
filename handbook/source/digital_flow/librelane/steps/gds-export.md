# Step VIII — GDS Export

The **GDS Export** stage generates the final **GDSII** and **LEF** layout files that represent the physical design ready for tapeout.  
This stage ensures that the layout is geometrically consistent, that all metal layers and vias are correctly defined, and that the exported files match across different layout tools.  

LibreLane performs this step using both **Magic** and **KLayout**, providing redundancy and cross-validation through XOR checks to ensure identical geometry between exports.

## Tools Involved

This stage uses the following tools:

- **[Magic](../../tools/magic.md)** — performs GDSII stream-out, LEF generation, and basic design integrity checks.  
- **[KLayout](../../tools/klayout.md)** — performs an independent GDSII export and XOR comparison with Magic’s output to validate equivalence.  
- **[Odb](../../tools/odb.md)** — provides final design property checks prior to export (antenna, layer connectivity).  
- **[Checker](../../tools/checker.md)** — verifies consistency between Magic and KLayout outputs.

## Sequence of Steps

LibreLane executes the following sub-steps for GDS export:

59. **Magic.StreamOut** — exports the final layout to GDSII format using Magic.  
60. **KLayout.StreamOut** — performs a second independent GDS export using KLayout.  
61. **Magic.WriteLEF** — generates the final LEF abstract for integration in higher-level designs.  
62. **Odb.CheckDesignAntennaProperties** — checks antenna-related properties before final export.  
63. **KLayout.XOR** — performs XOR comparison between Magic and KLayout GDS outputs.  
64. **Checker.XOR** — validates XOR results and reports mismatches if any.

## Configuration Overview

Configuration options for GDS export are defined in `config.json`.  
A typical configuration block looks like this:

```json
{
  "GDS_EXPORT_TOOL": "magic",
  "GDS_EXPORT_LAYER_MAP": "tech/sky130A/libs.tech/magic/sky130A.magicrc",
  "GDS_EXPORT_KLAYOUT_LMAP": "tech/sky130A/libs.tech/klayout/sky130A.lyp",
  "LEF_WRITE_ENABLE": true,
  "XOR_CHECK_ENABLE": true,
  "XOR_TOLERANCE": 0.001
}
```

Explanation:

 - GDS_EXPORT_TOOL — defines the main export tool (magic or klayout).
- GDS_EXPORT_LAYER_MAP — specifies the Magic layer mapping file.
- GDS_EXPORT_KLAYOUT_LMAP — specifies the KLayout layer properties file.
- LEF_WRITE_ENABLE — enables or disables LEF abstract generation.
- XOR_CHECK_ENABLE — enables post-export XOR comparison.
- XOR_TOLERANCE — defines allowable geometric deviation (in microns).

## Example: Running Only the GDS Export Step

You can perform only the GDS export stage using:
```
librelane run -d my_design -f gds-export
```

This command will:

1. Load the fully routed design database.
2. Generate GDS and LEF files using Magic and KLayout.
3. Run XOR comparison between both exports.
4. Produce final signoff-ready layout files under the results directory.

Results are stored in:
```
results/gds-export/
```

You can open the exported layout in KLayout for inspection:
```
klayout results/gds-export/my_design.gds
```
## Expected Outputs

- Final GDSII layout: results/gds-export/&lt;design&gt;.gds
- Final LEF abstract: results/gds-export/&lt;design&gt;.lef
- XOR report: reports/gds-export/xor_report.rpt
- Magic log: logs/gds-export/magic.log
- KLayout log: logs/gds-export/klayout.log

## Common Issues and Checks

- Layer mismatches: ensure that both Magic and KLayout use the correct layer map files for the target PDK.
- Missing vias or fills: verify that the routing fill and via generation were completed before export.
- XOR differences: check xor_report.rpt — small mismatches may occur due to numerical rounding but should be below the defined tolerance.
- LEF abstraction errors: if macro boundaries are incorrect, re-run Magic.WriteLEF and verify LEF rules in the tech file.
- GDS viewer issues: some tools may require layer map adjustments; use the .lyp file provided by the PDK.