# Librelane Flow

LibreLane is a powerful and versatile infrastructure library that enables the construction of digital ASIC physical implementation flows based on open-source and commercial EDA tools. It includes a reference flow (Classic) that is constructed entirely using open-source EDA tools –abstracting their behavior and allowing the user to configure them using a single file (See Figure 1). LibreLane also supports extending or modifying flows using Python scripts and utilities. See the [official documentation](https://librelane.readthedocs.io/en/latest/getting_started/newcomers/index.html)

![asda](fig/librelane_flow.png "asdasd")

## Librelane Classic Stepsd

Each of the following sections corresponds to one major stage of the LibreLane Classic flow. Click on each stage title to explore its specific configuration, tools, and outputs.

### {doc}`steps/rtl-linting`

    1. Verilator.Lint
    2. Checker.LintTimingConstructs
    3. Checker.LintErrors
    4. Checker.LintWarning

### {doc}`steps/synthesis`

    5. Yosys.JsonHeader  
    6. Yosys.Synthesis  
    7. Checker.YosysUnmapedCells  
    8. Checker.YosysSynthChecks  
    9. Checker.NetlistAssignStatements  
    10. OpenROAD.CheckSDCFiles  
    11. OpenROAD.CheckMacroInstances  
    12. OpenROAD.STAPrePNR  

### {doc}`steps/floorplaning`

    13. OpenROAD.Floorplan  
    14. Odb.CheckMacroAntennaProperties  
    15. Odb.SetPowerConnections  
    16. Odb.ManualMacroPlacement  
    17. OpenROAD.CutRows  
    18. OpenROAD.TapEndCapInsertion  
    19. Odb.AddPDNObstructions  
    20. OpenROAD.GeneratePDN  
    21. Odb.RemovePDNObstructions  
    22. Odb.AddRoutingObstructions  

### {doc}`steps/placement`

    23. OpenROAD.GlobalPlacementSkipIO  
    24. OpenROAD.IOPlacement  
    25. Odb.CustomIOPlacement  
    26. Odb.ApplyDEFTemplate  
    27. OpenROAD.GlobalPlacement  
    28. Odb.WriteVerilogHeader  
    29. Checker.PowerGridViolations  
    30. OpenROAD.STAMidPNR  
    31. OpenROAD.RepairDesignPostGPL  
    32. Odb.ManualGlobalPlacement  
    33. OpenROAD.DetailedPlacement  

### {doc}`steps/cts-optimization`

    34. OpenROAD.CTS  
    35. OpenROAD.STAMidPNR-1
    36. OpenROAD.ResizerTimingPostCTS
    37. OpenROAD.STAMidPNR-2

### {doc}`steps/routing`

    38. OpenROAD.GlobalRouting  
    39. OpenROAD.CheckAntennas  
    40. OpenROAD.RepairDesignPostGRT  
    41. Odb.DiodesOnPorts
    42. Odb.HeuristicDiodeInsertion
    43. OpenROAD.RepairAntennas
    44. OpenROAD.ResizerTimingPostGRT  
    45. OpenROAD.STAMidPNR-3  
    46. OpenROAD.DetailedRouting  
    47. Odb.RemoveRoutingObstructions
    48. OpenROAD.CheckAntennas-1
    49. Checker.TrDRC
    50. Odb.ReportDisconnectedPins
    51. Checker.DisconnectedPins
    52. Odb.ReportWireLength
    53. Checker.WireLength
    54. OpenROAD.FillInsertion
    55. Odb.CellFrequencyTables

### {doc}`steps/parasitic-extraction`

    56. OpenROAD.RCX  
    57. OpenROAD.STAPostPNR  
    58. OpenROAD.IRDropReport  

### {doc}`steps/gds-export`
    
    59. Magic.StreamOut
    60. KLayout.StreamOut
    61. Magic.WriteLEF
    62. Odb.CheckDesignAntennaProperties
    63. KLayout.XOR
    64. Checker.XOR

### {doc}`steps/verification`

    65. Magic.DRC
    66. KLayout.DRC
    67. Checker.MagicDRC
    68. Checker.KLayoutDRC
    69. Magic.SpiceExtraction
    70. Checker.IllegalOverlap
    71. Netgen.LVS
    72. Checker.LVS
    73. Yosys.EQY
    74. Checker.SetupViolations
    75. Checker.HoldViolations
    76. Checker.MaxSlewViolations
    77. Checker.MaxCapViolations
    78. Misc.ReportManufacturability

```{toctree}
:glob:
:hidden:

installation.md
running.md