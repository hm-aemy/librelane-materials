# Placement

**Placement** is the step where the components of a circuit (standard cells in digital, or devices in analog) are arranged within the chip area.  
A good placement ensures efficient routing, minimizes delay, and improves overall performance.

### Why placement matters

- Directly affects **timing, power, and area**.  
- Influences **routing congestion** and manufacturability.  
- For analog, placement is critical for **matching, symmetry, and noise isolation**.  

## Placement in digital design

- **Inputs**:  
  - Floorplan (die/core dimensions, macros, IO positions).  
  - Gate-level netlist.  
  - Timing and congestion constraints.  

- **Process**:  
  - Standard cells are placed into predefined rows.  
  - Placement is optimized for wire length, delay, and congestion.  
  - Incremental refinement improves critical path timing.  

- **Outputs**:  
  - Placed DEF file.  
  - Reports for wire length, congestion, and timing impact.  

### Global and Detailed placement

![asda](fig/global_pl.jpg "asdasd")
*Schematic view of an OTA designed in Xschem.*

![asda](fig/detailed_pl.jpg "asdasd")
*Schematic view of an OTA designed in Xschem.*

```{seealso}
For practical details, see:
- {doc}`../digital_flows/openroad/flow_steps`  
- {doc}`../digital_flows/librelane/flow_steps`  

**Video Resource – Placement Basics (VSD YouTube)**  
[Placement Fundamentals](https://www.youtube.com/watch?v=hVT8pRgr5zg)
```

## Placement in analog design

- **Focus**:  
  - Device matching (current mirrors, differential pairs).  
  - Symmetry and common-centroid layouts.  
  - Minimizing parasitics and coupling.  

- **Process**:  
  - Manual arrangement of devices based on schematic intent.  
  - Use of guard rings, dummy devices, and shielding.  

- **Outputs**:  
  - Layout drafts with critical device placement.  
  - Guidelines for later routing.  

-```{seealso}
For examples of schematic-driven analog layout, see:
- {doc}`../analog_flows/magic_layout`
-```

## Mixed-signal considerations

- Isolation between noisy digital blocks and sensitive analog sections.  
- Placement of decoupling capacitors and power domains.  
- Ensuring short interconnects for analog critical paths.  

-```{seealso}
See {doc}`../mixed_signal/analog_on_top` for integration guidelines.
-```

## Further Reading

- [ASIC World – Placement Basics](https://asic-world.com/asic_placement.html)  
- [OpenROAD Documentation – Placement](https://openroad.readthedocs.io/en/latest/user/Placement.html)  
- [VSD – Placement Lecture (YouTube)](https://www.youtube.com/watch?v=hVT8pRgr5zg)  
- [KLayout Documentation](https://www.klayout.de/doc-qt5/about/about.html)  
