# IC Design Flow Overview

Designing and implementing an Integrated Circuit (IC) is a **multi-stage process** that goes from a high-level description of the circuit behavior all the way to the physical layout delivered in **GDSII format**.  
This section provides an overview of the main steps in the **RTL-to-GDS flow**, highlighting the key tasks, tools, and outputs at each stage.

---

## 1. Specification

- Definition of functional requirements, performance metrics, and constraints.  
- For digital designs: clock frequency, power budget, area limits, I/O interfaces.  
- For analog/mixed-signal designs: gain, bandwidth, noise, PSRR, linearity, etc.  
- Output: a **clear set of design goals** that drive the rest of the flow.

---

## 2. RTL Design

- The circuit is described in **Register Transfer Level (RTL)** using **Verilog or VHDL**.  
- Focus on **functionality**, not physical implementation.  
- Coding guidelines ensure the design can be synthesized later.  
- Output: synthesizable RTL code.

---

## 3. RTL Simulation and Verification

- Functional correctness is validated using **testbenches**.  
- Tools like **Icarus Verilog, Verilator, or GHDL** can be used in open-source flows.  
- Verification methods:
  - Unit-level testbenches.
  - System-level simulations.
  - Assertions and coverage.  
- Output: verified RTL code.

---

## 4. Logic Synthesis

- Converts RTL into a **gate-level netlist** mapped to a target technology library (standard cells).  
- Tools (e.g., **Yosys** in open-source flows) ensure constraints like timing and area are met.  
- Output: gate-level netlist + synthesis reports.

---

## 5. Floorplanning

- Defines the **chip outline, power grid, and macro placement**.  
- IO pads, memo
