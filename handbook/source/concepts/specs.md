# Specifications and Requirements

Designing an integrated circuit (IC) always begins with a set of specifications. These specifications describe what the circuit must achieve in terms of functionality, performance, power, and physical constraints. They are the foundation of the entire design flow, guiding decisions from the first schematic to the final layout (GDSII).

## Why specifications matter

- They define the **design goals** before any line of RTL code or schematic is drawn.  
- They serve as a **contract** between system architects, designers, and verification engineers.  
- They allow **objective evaluation** of the final IC: pass/fail is decided based on the specs.  

## Typical specifications in digital design

- **Clock frequency / timing**: maximum operating frequency, setup and hold margins.  
- **Area**: maximum silicon footprint allowed.  
- **Power consumption**: dynamic and leakage power budgets.  
- **I/O and interfaces**: voltage levels, supported protocols (UART, SPI, I²C, etc.).  
- **Reliability**: operating temperature, ESD protection, lifetime targets.  

## Typical specifications in analog design

- **Gain and bandwidth**: small-signal gain, unity-gain frequency.  
- **Noise**: thermal, flicker, or integrated noise requirements.  
- **Linearity**: Total Harmonic Distortion (THD), Intermodulation Distortion (IMD).  
- **PSRR and CMRR**: power supply rejection and common-mode rejection.  
- **Accuracy**: offset voltage, bias currents, reference stability.  
- **Output swing / headroom**: limits set by technology and supply voltage.  

## From specs to constraints

In practice, specifications are translated into constraints used by tools and designers:

- Digital flows → timing constraints (SDC files), power targets, floorplan area.  
- Analog flows → bias conditions, device sizing rules, performance corners.  

These constraints ensure that the automated flow ({doc}`synthesis <synthesis>`, {doc}`placement <placement>` & {doc}`routing <routing>`, {doc}`simulation <simulation>`) is always evaluated against the original design goals.

## Key takeaway

Specifications are not optional: they are the starting point and the final checkpoint of any IC design. Well-defined specs make the difference between a successful tapeout and endless iterations.