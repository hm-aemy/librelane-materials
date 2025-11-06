# Course Structure and Teaching Methodologies

This section describes the structure of the *Teach-the-Teacher* program and the supported teaching methodologies for delivering open-source digital IC design education.

Currently, the **Problem-Based Learning (PBL)** methodology is the primary supported approach.  Future versions of this handbook may include additional models such as Project-Based Learning (PrBL) or Guided Laboratory Learning.

## 1. Teaching Methodologies Overview

| Methodology | Description | When to Use |
|:--|:--|:--|
| **Problem-Based Learning (PBL)** | Students learn by solving realistic problems that integrate multiple design concepts. Each module is framed around a design or verification challenge. | Recommended for workshops and advanced courses. |
| **Project-Based Learning (PrBL)** *(planned)* | Students complete a longer, open-ended project such as a full SoC or mixed-signal design. | For semester-long or capstone courses. |
| **Guided Laboratory Learning** *(planned)* | Instructor-led step-by-step exercises emphasizing tool familiarity. | For introductory training sessions. |

## 2. Implementation of Problem-Based Learning (PBL)

The **PBL methodology** organizes the course around a sequence of design problems that represent each stage of the IC design flow. Participants explore, research, and apply the relevant concepts using the handbook/documentation as their reference library.

### Structure of a PBL Module

Each module follows the same four-phase structure:

| Phase | Description | Instructor Role | Student Role |
|:--|:--|:--|:--|
| **1. Problem Introduction** | The instructor presents a clear design or verification problem with open-ended outcomes. | Facilitate discussion, clarify objectives. | Ask questions, analyze the problem. |
| **2. Exploration & Research** | Students search for solutions using the handbook, documentation, or experiments. | Provide hints or references only when needed. | Explore resources, test ideas, fail fast. |
| **3. Application & Prototyping** | Students implement and simulate their proposed solution using open tools. | Observe progress, encourage reflection. | Execute design steps, document findings. |
| **4. Discussion & Reflection** | The group reviews different approaches, identifies lessons learned, and connects results to theory. | Moderate discussion and summarize key takeaways. | Present findings, share feedback. |

## 3. Example of Course Progression under PBL

This represents a minimal course progression that covers the essential stages of the RTL-to-GDSII flow. As the program evolves, additional modules can be incorporated — for example, post-silicon testing, formal verification, or other advanced topics.

| Module | Problem Statement | Learning Goals | Expected Outcome | Related Concepts |
|:--|:--|:--|:--|:--|
| **Module 0 – Introduction** | *“How can a complete IC be implemented using only open tools?”* | Understand the open-source design ecosystem and abstraction levels. | Familiarity with PDKs, tools, and documentation structure. | {doc}`../concepts/pdk_basics`, {doc}`../concepts/design_abstraction` |
| **Module 1 – Digital System Design and Simulation** | *“How can we design and verify a digital system that meets a functional specification?”* | Apply digital design principles (combinational and sequential logic). Write and simulate RTL descriptions using Verilog and open-source simulators. | Functional RTL implementation and waveform verification using **Verilator** or **Icarus Verilog**. | {doc}`../concepts/specs`, {doc}`../concepts/simulation` |
| **Module 2 – Synthesis Challenge** | *“How does RTL become a gate-level implementation?”* | Use Yosys to synthesize and analyze netlists. | Synthesized netlist and synthesis report. | {doc}`../concepts/synthesis` |
| **Module 3 – Layout Challenge** | *“How can we physically implement and optimize our circuit?”* | Perform floorplanning, placement, and routing. | Routed DEF/GDS and timing report. | {doc}`../concepts/floorplaning`, {doc}`../concepts/placement`, {doc}`../concepts/routing` |
| **Module 4 – Verification Challenge** | *“How can we ensure the layout matches the schematic?”* | Run DRC/LVS and STA checks. | DRC/LVS-clean GDS file and verification summary. | {doc}`../concepts/parasitic_extraction`, {doc}`../concepts/verification` |
| **Module 5 – Integration Challenge** | *“How do analog and digital blocks interact in a mixed-signal system?”* | Understand interface constraints and integration flow. | Mixed-signal top-level design and report. | {doc}`../mixed_signal/analog_on_top` |

## 4. Instructor Guidelines for PBL Delivery

- Before starting Module 1, the instructor may ask students to conduct a brief research exercise on the design they wish to implement. Alternatively, the instructor can provide a predefined example design to ensure a common starting point.
- Start each module with a short briefing outlining the problem, goals, and constraints — avoid presenting solutions.  
- Encourage self-directed learning: students should navigate the handbook, explore tool documentation, and experiment independently.  
- Promote teamwork: each group can handle the same problem with slightly different parameters or target specs.  
- End every module with a reflection session: ask what worked, what didn’t, and what they would change next time.  
- Document everything: solutions, mistakes, and insights become future teaching material.

## 5. Timeline Examples

### A. 3-Day Bootcamp (Condensed)

A fast-paced introduction to the complete RTL-to-GDSII flow. Designed for participants with prior digital design experience who want to explore the open-source flow hands-on.

| Module | Day | Focus | Core Problem |
|:--|:--|:--|:--|
| **Module 0-1-2** | **Day 1** | Introduction + Digital Design + Simulation + synthesis| Understand the open-source ecosystem. Design, simulate and synthesize a simple digital system |
| **Module 3** | **Day 2** | Synthesis + Floorplanning + Placement + Routing| Synthesize the RTL design and prepare a physical layout. |
| **Module 4-5** | **Day 3** | Padring + Verification | Generate the top-level layout (including IO padring), export the GDS, and perform DRC/LVS checks. |

```{note}
In this timeline, it is recommended that the instructor provides a predefined simple design for implementation, so that students can focus on learning the flow rather than creating their own design. Example projects and a detailed instructor checklist are currently under development.
```

> *Outcome:* Participants obtain a DRC-clean GDSII of a small digital block and understand the basics of the open-source design flow.

### B. 5-Day Extended Workshop

A more balanced schedule that allows for deeper exploration, tool familiarization, and group reflection. Recommended for new instructors or students with limited previous exposure to open-source design.

| Module | Day | Focus | Core Problem |
|:--|:--|:--|:--|
| **Module 0** | **Day 1** | Overview + Environment Setup | Install and configure the toolchain (LibreLane or OpenROAD). Review the RTL-to-GDS flow and open PDKs. |
| **Module 1-2** | **Day 2** | Digital Design + Simulation + Synthesis | Write, simulate and synthesize an RTL design using **Verilator** or **Icarus Verilog**. |
| **Module 3** | **Day 3** | Floorplanning + placement | Do first steps in the layout implementation. |
| **Module 3** | **Day 4** | Routing + Padring | Implement routing and padring, analyze timing and congestion. |
| **Module 4–5** | **Day 5** | Verification + GDS Export | perform DRC/LVS in **Magic/KLayout**, and stream out the final GDS. |

```{note}
In this timeline, it is recommended that the instructor provides a list of possible basic designs, while allowing students to propose their own ideas if they wish. It is the instructor’s responsibility to ensure that the selected design remains simple and feasible within the workshop scope. It is also encouraged to have students give a short presentation at the end of the module, followed by instructor feedback.
```

> *Outcome:* Participants produce a verified layout and gain the ability to reproduce and implement their own designs.

#### C. 2-Week Educator Workshop (10 Days)

A comprehensive program designed for educators and teaching assistants.  
Combines problem-based design tasks with teaching reflection sessions.

| Week | Focus | Core Problems & Activities |
|:--|:--|:--|
| **Week 1** | Digital Design Fundamentals | - Review the design flow and PDK setup.<br>- Develop and simulate simple RTL blocks.<br>- Synthesize designs and discuss timing vs. area trade-offs. |
| **Week 2** | Physical Implementation & Teaching Practice | - Perform floorplanning, placement, routing, and verification.<br>- Export the final GDS.<br>- Prepare and present a mini teaching session (“teach-back”) explaining one stage of the flow. |


### D. Semester Course (8–10 weeks)
| Week | Focus | Problem |
|:--|:--|:--|
| 1–2 | Simulation & RTL design | Verify functional correctness of counter or ALU. |
| 3–4 | Synthesis | Optimize logic for area/timing. |
| 5–7 | Layout & Optimization | Produce DRC/LVS-clean layout. |
| 8–10 | Sign-off & Mixed-Signal | Integrate digital core with analog macro. |

## 6. Future Extensions

In future iterations, additional methodologies may be integrated:

- **Project-Based Learning (PrBL):** long-term team projects such as designing a full SoC.  
- **Guided Laboratory Learning:** instructor-led sessions for early-stage learners.  
- **Flipped Classroom:** pre-recorded lectures followed by in-class problem solving.
