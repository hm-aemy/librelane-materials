# Floorplanning

**Floorplanning** is the process of defining the **physical organization** of a chip before detailed placement and routing.  
It sets the foundation for power distribution, signal flow, and overall chip performance.

---

## Why floorplanning matters

- Establishes the **chip outline** and core area.  
- Defines the position of **I/O pads, macros, and memory blocks**.  
- Ensures a robust **power delivery network (PDN)**.  
- Affects performance, congestion, and manufacturability.  

---

## Floorplanning in digital design

- **Inputs**:  
  - Gate-level netlist.  
  - Library files (standard cells, LEF).  
  - Timing and power constraints.  

- **Process**:  
  - Define die/core dimensions.  
  - Place I/O pins and macros (SRAMs, PLLs, etc.).  
  - Insert power rings and initial PDN.  
  - Reserve routing channels and keep-out zones.  

- **Outputs**:  
  - Floorplan DEF/LEF files.  
  - Reports for area utilization and congestion estimates.  

```{seealso}

📺 **Video Series – Floorplanning Basics (VSD YouTube)**  
- [Part 1](https://www.youtube.com/watch?v=Z1Cxbn5LOYg)  
- [Part 2](https://www.youtube.com/watch?v=iKGxOVbwi40)  
- [Part 3](https://www.youtube.com/watch?v=YcwY1PH31qg)  
- [Part 4](https://www.youtube.com/watch?v=AW6OwZxY7VY)  
- [Part 5](https://www.youtube.com/watch?v=IG0EWRcDkl8)  
- [Part 6](https://www.youtube.com/watch?v=3n_v_1HRlzM)  
- [Part 7](https://www.youtube.com/watch?v=HVeSgMS8-SU) 

For detailed steps on digital floorplanning, see:  
- {doc}`../digital_flows/openroad/flow_steps`  
- {doc}`../digital_flows/librelane/flow_steps`
```

---

## Floorplanning in analog design

- **Focus**: transistor arrays, current mirrors, matched pairs.  
- **Process**:  
  - Define symmetry and common-centroid structures.  
  - Reserve guard rings and isolation wells.  
  - Plan routing paths for sensitive nodes.  

- **Outputs**:  
  - Schematic-driven layout templates.  
  - Analog floorplan sketches (often manual).  

```{seealso}
For schematic-driven analog layout, see:  
- {doc}`../analog_flows/magic_layout`  
- {doc}`../analog_flows/klayout_drc_lvs`
```
---

## Mixed-signal considerations

- Separation of digital and analog domains to reduce noise coupling.  
- Dedicated power grids for analog and digital blocks.  
- Shielding of sensitive analog traces from switching digital nets.  

```{seealso}
For mixed-signal integration, see:  
- {doc}`../mixed_signal/analog_on_top`
```

---


## Further Reading

- 🌍 [ASIC World – Floorplanning Basics](https://asic-world.com/asic_floorplan.html)  
- 🌍 [OpenROAD Documentation – Floorplan](https://openroad-flow-scripts.readthedocs.io/en/latest/floorplan.html)  
- 📺 [VSD – Floorplanning Basics (YouTube)](https://www.youtube.com/watch?v=3t9JykdpHgQ)  
- 🌍 [KLayout Documentation](https://www.klayout.de/doc-qt5/about/about.html)  
