# Step III — Floorplanning

The **Floorplanning** stage defines the **physical structure** of the chip.  
It establishes the layout boundaries, placement blockages, I/O pin positions, power distribution network (PDN), and macro placements that will guide all subsequent physical implementation stages.  

A well-defined floorplan ensures that routing, timing, and power delivery can be achieved efficiently in later steps.

## Tools Involved

This stage mainly uses the following tools:

- **[OpenROAD](../../tools/openroad.md)** — performs floorplan creation, row cutting, tap/endcap insertion, PDN generation, and related physical checks.  
- **[Odb](../../tools/odb.md)** — manages database manipulations such as macro placement, PDN obstruction handling, and routing blockage definitions.

LibreLane automates the floorplanning sequence through a series of scripted steps, ensuring consistency and reproducibility across different technologies.

## Sequence of Steps

During the floorplanning stage, LibreLane executes the following sub-steps:

13. **OpenROAD.Floorplan** — creates the initial floorplan (core area, die size, I/O ring).  
14. **Odb.CheckMacroAntennaProperties** — verifies antenna-related parameters of macros.  
15. **Odb.SetPowerConnections** — connects macros and standard cells to power rails.  
16. **Odb.ManualMacroPlacement** — applies user-defined or scripted macro placements.  
17. **OpenROAD.CutRows** — removes placement rows under macros and obstructions.  
18. **OpenROAD.TapEndCapInsertion** — inserts well-taps and endcap cells to ensure substrate continuity.  
19. **Odb.AddPDNObstructions** — prevents routing over critical PDN regions.  
20. **OpenROAD.GeneratePDN** — creates the power distribution network (VDD/VSS grid).  
21. **Odb.RemovePDNObstructions** — restores access to allowed routing regions.  
22. **Odb.AddRoutingObstructions** — defines global routing blockages before placement.

## Configuration Overview

The floorplanning behavior is controlled via entries in the project’s `config.json`.  
Typical parameters include:

```json
{
  "CORE_UTILIZATION": 50,
  "CORE_MARGIN": 10,
  "FP_ASPECT_RATIO": 1.0,
  "FP_IO_MODE": "macro",
  "FP_TAPCELL_DIST": 14.0,
  "FP_PDN_ENABLE": true,
  "FP_PDN_CFG": "pdn.tcl"
}
```

## Key fields:

- CORE_UTILIZATION — defines the target area density for standard cells.
- CORE_MARGIN — specifies margins between core and die edges.
- FP_ASPECT_RATIO — controls die aspect ratio.
- FP_TAPCELL_DIST — sets spacing between tapcells.
- FP_PDN_CFG — points to a PDN generation script or template.

Custom macro placements can be specified using DEF templates or manual placement TCL files referenced through configuration variables.

## Example: Running Only the Floorplanning Step

To execute only the floorplanning stage of the flow:

```
librelane run -d my_design -f floorplanning
```

LibreLane will:

1. Generate the initial core and die dimensions based on configuration.
2. Place macros and define I/O ring boundaries.
3. Insert well-taps, endcaps, and power grid.
4. Produce DEF and LEF files describing the layout constraints.

The main outputs are stored in:

```
results/floorplanning/
```

and can be visualized in the GUI mode of OpenROAD:
```
openroad -gui results/floorplanning/my_design.def
```
## Expected Outputs

- DEF file: results/floorplanning/&lt;design&gt;.def
- LEF file: results/floorplanning/&lt;design&gt;.lef
- PDN reports: reports/floorplanning/pdn.log
- Log files: logs/floorplanning/openroad.log

## Common Issues and Checks

- Ensure that all macros used in the design have corresponding .lef abstracts.
- Verify that power and ground nets (VDD, VSS) are correctly declared in the configuration.
- If the design shows insufficient whitespace or overlap warnings, adjust CORE_UTILIZATION or CORE_MARGIN.
- Check PDN generation logs to confirm that all layers were routed and connected.