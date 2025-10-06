# Routing

**Routing** is the process of creating the **physical interconnections** between placed components inside a chip.  
After placement defines *where* each element sits, routing defines *how* signals, power, and clocks travel across metal layers.

---

## Why routing matters

- Determines **signal integrity**, **timing**, and **power distribution**.  
- Affects chip **area**, **congestion**, and **manufacturability**.  
- Errors in routing can cause shorts, opens, or parasitic effects that degrade performance.  

---

## Routing in digital design

- **Inputs**:  
  - Placed DEF from placement step.  
  - Design rules from the PDK (minimum width, spacing, via rules).  
  - Timing constraints and power requirements.  

- **Process**:  
  - **Global routing**: plans approximate paths for all nets.  
  - **Detailed routing**: finalizes exact wire shapes and via positions.  
  - **Post-route optimization**: fixes violations, re-balances timing, and closes DRC errors.  

- **Outputs**:  
  - Fully routed DEF and updated netlist.  
  - Reports for DRC violations, wire length, and timing closure.  

-```{seealso}
For practical implementation examples, see:
- {doc}`../digital_flows/openroad/flow_steps`
- {doc}`../digital_flows/librelane/flow_steps`

📺 **Video Resource – Routing Fundamentals (VSD YouTube)**  
[Routing Basics](https://www.youtube.com/watch?v=yS3TMVovX-4)
-```

---

## Routing in analog design

- **Focus**:  
  - Manual routing to ensure signal matching and symmetry.  
  - Shielding of sensitive nodes and differential pairs.  
  - Control of parasitic capacitance and resistance.  

- **Process**:  
  - Use of thick metal layers for power and high-current paths.  
  - Manual or assisted routing in layout tools (Magic, KLayout).  
  - Checking critical nets for length matching and noise coupling.  

- **Outputs**:  
  - Completed layout interconnects.  
  - DRC/LVS-clean layout ready for parasitic extraction.  

-```{seealso}
For schematic-driven analog layout routing, see:
- {doc}`../analog_flows/magic_layout`
- {doc}`../analog_flows/klayout_drc_lvs`
-```

---

## Mixed-signal considerations

- Isolation between analog and digital routing domains.  
- Use of guard rings and shielding to reduce substrate noise.  
- Separate power domains and supply routing.  

-```{seealso}
See {doc}`../mixed_signal/analog_on_top` for integration techniques.
-```

---

## Further Reading

- 🌍 [ASIC World – Routing Basics](https://asic-world.com/asic_routing.html)  
- 🌍 [OpenROAD Documentation – Routing](https://openroad.readthedocs.io/en/latest/user/Routing.html)  
- 📺 [VSD – Routing Basics (YouTube)](https://www.youtube.com/watch?v=yS3TMVovX-4)  
- 🌍 [KLayout Documentation](https://www.klayout.de/doc-qt5/about/about.html)  
- 🌍 [Magic VLSI User Guide](http://opencircuitdesign.com/magic/)  
