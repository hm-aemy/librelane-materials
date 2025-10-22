# Librelane Flow

[LibreLane is a powerful and versatile infrastructure library that enables the construction of digital ASIC physical implementation flows based on open-source and commercial EDA tools. It includes a reference flow (Classic) that is constructed entirely using open-source EDA tools –abstracting their behavior and allowing the user to configure them using a single file (See Figure 1). LibreLane also supports extending or modifying flows using Python scripts and utilities.](https://librelane.readthedocs.io/en/latest/getting_started/newcomers/index.html)

![asda](fig/librelane_flow.png "asdasd")

## Librelane Classic Steps

```
Librelane-Flow/
├── RTL-Linting
│   ├─ 1. Verilator.Lint
│   ├─ 2. Checker.LintTimingConstructs
│   ├─ 3. Checker.LintErrors
│   └─ 4. Checker.LintWarning

├── synthesis_netlists.md
│   ├─ 5. Yosys.JsonHeader  
│   ├─ 6. Yosys.Synthesis  
│   ├─ 7. Checker.YosysUnmapedCells  
│   ├─ 8. Checker.YosysSynthChecks  
│   ├─ 9. Checker.NetlistAssignStatements  
│   ├─ 10. OpenROAD.CheckSDCFiles  
│   ├─ 11. OpenROAD.CheckMacroInstances  
│   └─ 12. OpenROAD.STAPrePNR  
│      → Converts RTL into a gate-level netlist using Yosys and validates timing before P&R.

├── floorplanning.md
│   ├─ 13. OpenROAD.Floorplan  
│   ├─ 14. Odb.CheckMacroAntennaProperties  
│   ├─ 15. Odb.SetPowerConnections  
│   ├─ 16. Odb.ManualMacroPlacement  
│   ├─ 17. OpenROAD.CutRows  
│   ├─ 18. OpenROAD.TapEndCapInsertion  
│   ├─ 19. Odb.AddPDNObstructions  
│   ├─ 20. OpenROAD.GeneratePDN  
│   ├─ 21. Odb.RemovePDNObstructions  
│   └─ 22. Odb.AddRoutingObstructions  
│      → Defines die/core areas, macro placement, and power delivery network (PDN) planning.

├── placement.md
│   ├─ 23. OpenROAD.GlobalPlacementSkipIO  
│   ├─ 24. OpenROAD.IOPlacement  
│   ├─ 25. Odb.CustomIOPlacement  
│   ├─ 26. Odb.ApplyDEFTemplate  
│   ├─ 27. OpenROAD.GlobalPlacement  
│   ├─ 28. Odb.WriteVerilogHeader  
│   ├─ 29. Checker.PowerGridViolations  
│   ├─ 30. OpenROAD.STAMidPNR  
│   ├─ 31. OpenROAD.RepairDesignPostGPL  
│   ├─ 32. Odb.ManualGlobalPlacement  
│   └─ 33. OpenROAD.DetailedPlacement  
│      → Places standard cells and macros, repairs timing and congestion before CTS.

├── (digital-only) CTS & Optimization
│   ├─ 34. OpenROAD.CTS  
│   ├─ 35–37. STA/Resizer optimization passes  
│      → Builds the clock tree and balances timing across the design.

├── routing.md
│   ├─ 38. OpenROAD.GlobalRouting  
│   ├─ 39. OpenROAD.CheckAntennas  
│   ├─ 40. OpenROAD.RepairDesignPostGRT  
│   ├─ 41–43. Odb and OpenROAD diode/antenna repair  
│   ├─ 44. OpenROAD.ResizerTimingPostGRT  
│   ├─ 45. OpenROAD.STAMidPNR-3  
│   ├─ 46. OpenROAD.DetailedRouting  
│   ├─ 47–55. Final routing checks and metal fill insertion  
│      → Connects all nets physically, fixes violations, and closes timing.

├── parasitics_extraction.md
│   └─ 56. OpenROAD.RCX  
│      → Extracts parasitic RCs from the routed layout for accurate timing and post-layout analysis.

├── verification.md
│   ├─ 57. OpenROAD.STAPostPNR  
│   ├─ 58. OpenROAD.IRDropReport  
│   ├─ 65–78. Physical signoff and electrical checks (Magic/KLayout/Netgen/Checkers)  
│      → Performs timing sign-off, DRC/LVS/ERC, and manufacturability validation.

├── gds_export.md
│   ├─ 59. Magic.StreamOut  
│   ├─ 60. Klayout.StreamOut  
│   ├─ 61. Magic.WriteLEF  
│   ├─ 62. Odb.CheckDesignAntennaProperties  
│   ├─ 63. Klayout.XOR  
│   └─ 64. Checker.XOR  
│      → Streams out final layout to GDSII and verifies top-level equivalence.

└── pdk_basics.md
    └─ (Underpins all steps)
       → Provides technology files, rules, and device models for Yosys, OpenROAD, Magic, and KLayout.
```

```{toctree}
:glob:
:hidden:

installation.md
running.md