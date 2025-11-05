# Problem-Based Modules

This section presents a set of **problem-based learning (PBL) modules** that correspond to the stages of the open-source digital IC design flow.  
Each module focuses on a concrete design problem, encouraging participants to **explore**, **experiment**, and **apply** concepts introduced in the *Concepts* and *Course Structure* sections.

## Problem 0 – Introduction to the Open Design Ecosystem

### Problem Statement
*“How can we design and fabricate a complete integrated circuit using only open-source tools and PDKs?”*

### Learning Goals
- Understand the open-source EDA ecosystem (tools, communities, and PDKs).  
- Identify the stages of the RTL-to-GDSII flow and their interdependencies.  
- Set up the working environment (Docker, Nix, or WSL2).  

### Hints & Resources

- **Work in progress...**

### Expected Deliverables
- Verified tool installation and environment setup log.  
- Screenshot or description of a successful example run (e.g., `openroad -version` or `yosys -V`).  

### Reflection Questions
- What are the main design flow stages?  
- What advantages does open-source design offer to education and research?  

## Problem 1 – Digital System Design and Simulation

### Problem Statement
*“How can we design and verify a digital system that meets a functional specification?”*

### Learning Goals
- Apply digital design principles (combinational and sequential logic).  
- Write and simulate RTL using Verilog and open-source simulators.  
- Interpret simulation waveforms and debug design behavior.  

### Hints & Resources
- **Work in progress...**

### Expected Deliverables
- RTL code of the designed system (e.g., counter, ALU, FSM or advanced system).  
- Testbench verifying correct functionality.  
- Simulation waveform (`.vcd`) demonstrating proper behavior.

### Reflection Questions
- What types of errors did you encounter during simulation?  
- How do you ensure that the testbench covers all expected scenarios?

## Problem 2 – Synthesis and Netlist Analysis

### Problem Statement
*“How does our high-level RTL description become a gate-level implementation?”*

### Learning Goals
- Perform synthesis using **Yosys**.  
- Analyze synthesis reports and identify logic optimizations.  
- Interpret the generated gate-level netlist.  

### Hints & Resources
- **Work in progress...**

### Expected Deliverables
- Gate-level Verilog netlist (`.v`).  
- Synthesis log and report (area, cell count, warnings).  
- Screenshot or diagram comparing RTL vs. netlist structure.

### Reflection Questions
- What types of optimizations did Yosys perform?  
- How can synthesis constraints affect timing?  
- What are the main differences between your RTL and netlist versions?

## Problem 3 – Physical Implementation

### Problem Statement
*“How can we transform our gate-level netlist into a physical layout that meets design rules?”*

### Learning Goals
- Understand floorplanning, placement, and routing fundamentals.  
- Use **OpenROAD** or **LibreLane** to implement the design.  
- Identify congestion, timing, or routing issues during layout generation.  

### Hints & Resources
- **Work in progress...**

### Expected Deliverables
- Implemented design DEF and GDS files.  
- Layout screenshots at different stages (placement, routing).  
- Short timing and area summary report.

### Reflection Questions
- What trade-offs exist between area, timing, and routing complexity?  
- Which step of physical design was most challenging and why?  
- How could you improve the power distribution network in future iterations?

## Problem 4 – Verification and Sign-off

### Problem Statement
*“How can we ensure that our final layout is correct, manufacturable, and functionally equivalent to our schematic?”*

### Learning Goals
- Perform DRC and LVS checks using **Magic** and **KLayout**.  
- Validate electrical and logical equivalence with **Netgen** or **Yosys EQY**.  
- Understand the importance of parasitic extraction and timing analysis (STA).  

### Hints & Resources
- **Work in progress...**

### Expected Deliverables
- DRC and LVS reports showing a clean design.  
- Extracted parasitic netlist and timing report.  
- Short summary describing verification results.

### Reflection Questions
- What are the most common DRC violations?  
- How can parasitic effects influence timing?  
- What differences did you observe between schematic and layout netlists?

## Problem 5 (Optional) – Integration and Mixed-Signal Extension

### Problem Statement
*“How can we combine a digital core and an analog block into a functional mixed-signal system?”*

### Learning Goals
- Integrate digital and analog sub-blocks at the top level.  
- Manage pin alignment, power domains, and interface signals.  
- Perform top-level verification before final GDS export.  

### Hints & Resources
- **Work in progress...**

### Expected Deliverables
- Top-level mixed-signal GDS and schematic hierarchy diagram.  
- Documentation of integration constraints (supply levels, IO).  
- Verification report showing proper interconnection between domains.

### Reflection Questions
- What integration issues arise when combining analog and digital domains?  
- How can you verify signal integrity across domains?  
- How could you generalize your integration flow for future projects?

> 💡 *Each problem is designed to be open-ended — instructors are encouraged to adjust difficulty and scope depending on audience background and available time.*
