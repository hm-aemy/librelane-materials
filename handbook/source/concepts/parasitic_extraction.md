# Parasitics Extraction

After routing is completed, the next step is to **extract parasitics** — the unwanted resistances, capacitances, and couplings that arise from the physical layout.  
These parasitics can significantly affect timing, gain, noise, and overall circuit behavior, especially at deep-submicron scales.

---

## Why parasitic extraction matters

- Real interconnects are **not ideal** — they introduce resistance and capacitance.  
- Post-layout simulation and timing analysis require **accurate parasitic data**.  
- Extraction bridges the gap between **layout geometry** and **electrical performance**.  
- Enables **post-layout verification**: DRC, LVS, STA, and analog re-simulation.  

---

## Parasitics in digital design

- **Purpose:**  
  - Model RC delays introduced by interconnect wires and vias.  
  - Enable realistic timing and signal integrity checks.  

- **Process:**  
  - Tools read the routed DEF and LEF to calculate interconnect resistance and capacitance.  
  - Results are back-annotated into timing models or simulation files.  

- **Common formats:**  
  - **SPEF (Standard Parasitic Exchange Format)** — used for STA and power analysis.  
  - **SDF (Standard Delay Format)** — used for gate-level timing simulation.  
  - **DSPF** — detailed RC network for high-accuracy analysis.  

- **Outputs:**  
  - Parasitic RC files (`.spef`, `.sdf`, `.dspf`).  
  - Updated timing reports including RC effects.  

---

## Parasitics in analog design

- **Purpose:**  
  - Capture parasitic capacitances, resistances, and couplings from layout geometry.  
  - Evaluate their impact on performance (gain, phase margin, noise, etc.).  

- **Process:**  
  - Extraction tools analyze the final GDS/stream file and generate an **RC-annotated SPICE netlist**.  
  - Each interconnect segment becomes an RC element with accurate geometry-based values.  

- **Outputs:**  
  - Extracted SPICE netlist used for **post-layout simulations**.  
  - Enables accurate matching between schematic and layout behavior.  

---

## Mixed-signal considerations

- Coupling between digital and analog nets can inject **substrate noise**.  
- Mixed-signal extraction includes **substrate and well models** for isolation analysis.  
- Ensures analog performance remains stable under switching activity.  

---

## Typical flow

1. Routing completed → GDS/DEF generated.  
2. Extraction performed → RC networks generated.  
3. Parasitics back-annotated → updated netlist or timing file.  
4. Post-layout verification → simulation or STA.  

---

## Further Reading

- 🌍 [OpenROAD Documentation – Parasitic Extraction (OpenRCX)](https://openroad.readthedocs.io/en/latest/user/OpenRCX.html)  
- 🌍 [Magic VLSI – Extraction Manual](http://opencircuitdesign.com/magic/extract.html)  
- 🌍 [KLayout xRC Extraction Documentation](https://www.klayout.de/xrc.html)  
- 🌍 [ASIC World – Post-Layout Simulation](https://asic-world.com/)  
- 📺 [VSD – Post-Layout Parasitic Extraction (YouTube)](https://www.youtube.com/watch?v=8VlnTtnqNnA)  
