# PDK Basics

A **Process Design Kit (PDK)** is the collection of data, models, and rules that describe a specific semiconductor fabrication technology.  
It acts as the **bridge between circuit design and manufacturing**, defining how transistors, resistors, interconnects, and all physical layers are implemented.

Without a PDK, no layout, simulation, or verification can accurately reflect how a chip will behave in silicon.

---

## Why the PDK matters

- Provides all **physical and electrical parameters** of the process.  
- Ensures **design compatibility** with the foundry’s fabrication rules.  
- Enables **simulation**, **layout**, **DRC/LVS**, and **parasitic extraction**.  
- Defines **naming conventions** and **layer maps** shared across tools.  

In open-source flows, PDKs are what make reproducible, transparent chip design possible.

---

## Main components of a PDK

| Category | Description | Typical File Types |
|:--|:--|:--|
| **Technology files** | Define layers, masks, and design rules. | `.tech`, `.lyp`, `.lef`, `.map` |
| **Device models** | SPICE models describing transistor and passive behavior. | `.spice`, `.model` |
| **Standard cell libraries** | Pre-built logic gates with timing/power data. | `.lib`, `.lef`, `.gds` |
| **DRC/LVS rules** | Files used by verification tools. | `.drc`, `.lvs`, `.tcl` |
| **Parasitic extraction rules** | Define how RC values are computed. | `.ext`, `.xrc`, `.spef` |
| **IO and pad cells** | Interfaces between core logic and chip pins. | `.gds`, `.lef`, `.lib` |
| **Documentation** | Design guidelines and layer references. | `.pdf`, `.html` |

---

## Open-source PDK examples

| PDK | Technology Node | Maintainers | Typical Use |
|:--|:--:|:--|:--|
| **Skywater SKY130** | 130 nm | Google & Efabless | General-purpose analog/digital designs. |
| **GF180MCU** | 180 nm | GlobalFoundries & Efabless | Mixed-signal, higher-voltage applications. |
| **IHP SG13G2** | 130 nm BiCMOS | IHP Microelectronics | RF and analog/mixed-signal designs. |

Each open PDK includes its own set of models, technology files, and libraries that integrate with open-source EDA tools like **OpenROAD**, **Magic**, **KLayout**, **Ngspice**, and **Xschem**.

---

## How PDKs connect to the flow

- **Front-end design** (RTL or schematic): uses transistor or cell models for simulation.  
- **Physical design**: uses layer definitions and DRC rules for layout.  
- **Verification**: uses LVS/DRC rule decks and extraction parameters.  
- **Export**: uses layer maps for GDS stream-out.

This makes the PDK the **common foundation** across all design steps — from the very first schematic to the final GDSII file.

---

## PDK installation and access

Open-source PDKs can be installed through Git repositories or Docker images.

Examples:
```bash
# Skywater PDK
git clone https://github.com/google/skywater-pdk

# GF180MCU PDK
git clone https://github.com/efabless/gf180mcu-pdk

# IHP SG13G2 PDK
git clone https://github.com/IHP-GmbH/IHP-Open-PDK
