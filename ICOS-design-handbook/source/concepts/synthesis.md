# Synthesis and Netlist Generation

**Synthesis** is the process of transforming a high-level circuit description into a **lower-level representation** that can be implemented physically.  
In digital design, synthesis refers to **mapping RTL code into a gate-level netlist** using a technology library.  
In analog design, there is no "logic synthesis," but **netlists are extracted** from schematics or layouts to represent the transistor-level circuit.

---

## Why synthesis (or netlist generation) matters

- It connects the **abstract design** (RTL or schematic) to the **physical design flow**.  
- It provides the first technology-dependent description of the design.  
- It allows estimation of **area, timing, and power** before layout.  
- It generates the **reference netlist** used for verification (LVS, STA).  

---

## Digital synthesis

- **Inputs**:  
  - RTL description (Verilog, VHDL).  
  - Timing constraints (e.g., clock frequency, input/output delays).  
  - Target technology library (standard cells, Liberty files).  

- **Process**:  
  - High-level logic is transformed into Boolean networks.  
  - Optimizations are applied (area, power, timing trade-offs).  
  - Mapping is performed to available gates in the library.  

- **Outputs**:  
  - **Gate-level netlist**: list of standard cells and interconnections.  
  - **Reports**: area usage, timing analysis, power estimates.  


```{seealso}
📺 [Digital Synthesis Basics – VSD YouTube Lecture](https://www.youtube.com/watch?v=TR3IMS_TYxw&list=PLtChGkQ0aIK-S0V_3RxgkGaZj8UgtjcyQ&index=3)  
This video provides an accessible introduction to **logic synthesis** and how RTL is transformed into a gate-level netlist.
```

```{seealso}
For step-by-step instructions on how synthesis is executed in open-source flows, see:  
- {doc}`../digital_flows/openroad/flow_steps`  
- {doc}`../digital_flows/librelane/flow_steps`
```

---

## Analog netlist extraction

- **Inputs**:  
  - Schematic-level description (transistors, resistors, capacitors).  
  - Device models from the PDK.  

- **Process**:  
  - The schematic is translated into a SPICE netlist.  
  - Each component is mapped to a device model with parameters.  

- **Outputs**:  
  - **SPICE netlist** used for simulation.  
  - Extracted netlist later used for LVS comparison.  

```{seealso}
For details on schematic-level netlist extraction, see:  
- {doc}`../analog_flows/xschem_intro`
```

---

## Mixed-Signal perspective

- Digital blocks → synthesized into gate-level netlists.  
- Analog blocks → extracted into transistor-level SPICE netlists.  
- Both netlists are integrated at the **top level** to form a complete mixed-signal system.  

```{seealso}
For integration of analog and digital netlists, see:  
- {doc}`../mixed_signal/analog_on_top`
```
