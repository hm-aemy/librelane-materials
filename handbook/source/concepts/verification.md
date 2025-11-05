# Verification

Verification ensures that a design is correct, consistent, and manufacturable.
It confirms that the physical layout and electrical behavior match the intended specifications and that the circuit will function reliably in silicon.
It bridges the gap between design intent and silicon reality, validating correctness at every stage of the design flow.

Verification spans multiple domains — logical, physical, timing, and statistical — and happens throughout the entire IC design process.

## Types of Verification

### Functional verification (pre-layout)
Checks whether the design behaves as expected from a functional perspective.
- Simulate RTL using testbenches.
- Validate logical operations and state transitions.
- Identify design bugs early in the flow.
- May include assertion-based or formal verification to complement simulation.

Typical tools: Icarus Verilog, Verilator, GHDL.

### Structural verification (post-synthesis)
Ensures that synthesis did not change the circuit’s logic.

- Gate-level simulation with real timing data.
- Equivalence checking between RTL and synthesized netlists.
- Verifies inserted cells (buffers, tie cells, etc.) behave correctly.
- Detects missing or mis-optimized logic.

Outputs: gate-level reports, functional equivalence logs.

### Physical verification (post-layout)
Confirms that the physical layout adheres to fabrication and connectivity rules.

- DRC (Design Rule Check): verifies geometry and spacing rules.
- LVS (Layout vs. Schematic): ensures layout connectivity matches schematic/netlist.
- ERC (Electrical Rule Check): checks for unconnected pins, shorts, or voltage violations.
- PEX (Parasitic Extraction): extracts RC elements for post-layout timing or analog simulation.

Outputs: clean DRC/LVS reports and GDS ready for tape-out.

### Timing verification (digital)
Guarantees that all timing constraints are satisfied across the chip.

- STA (Static Timing Analysis): computes signal arrival times and verifies setup/hold margins.
- Detects critical paths, clock-skew issues, and violations after routing.
- Uses extracted parasitic data (SPEF/SDF) for accurate post-layout timing closure.

Outputs: timing reports, slack summaries, sign-off timing views.

### Corner & Monte Carlo (analog)
Validates circuit performance under process, voltage, and temperature variations.

- **Corner analysis:** simulates design at fast/slow device corners and temperature extremes.  
- **Monte Carlo analysis:** adds random device variations to estimate yield and performance spread.  
- Ensures biasing, gain, and stability remain within spec.

Outputs: performance distributions and yield estimations.

## Digital vs. Analog focus

| Aspect | Digital Design | Analog Design |
|:--|:--|:--|
| **Primary Goals** | Functional and timing correctness | Accuracy, stability, yield |
| **Verification Flow** | Automated STA, DRC/LVS pipelines | Manual or semi-automated DRC/LVS + SPICE validation |
| **Key Tools** | OpenROAD, OpenRCX, Netgen | Magic, KLayout, Ngspice |
| **Common Reports** | Timing, area, power, DRC/LVS summary | Gain/phase margin, corners, noise analysis |


## Outputs

- Clean DRC/LVS/ERC/PEX reports confirming physical correctness.
- Timing sign-off reports verifying clock and data path performance.
- Simulation logs and coverage data validating functional behavior.
- Corner and Monte Carlo results quantifying yield and variation.

## Key Verification Domains

Verification occurs continuously across the design flow:

- Front-end domain: RTL simulation, logic equivalence, and functional correctness.
- Back-end domain: DRC, LVS, parasitic extraction, and timing sign-off.
- Statistical domain: process variation and yield estimation.
- Cross-domain checks: consistency between schematic, layout, and extracted simulation data.

## See Also

```{seealso}
- {doc}`../digital_flows/openroad/flow_steps`  
- {doc}`../digital_flows/librelane/flow_steps`  
- {doc}`../analog_flows/magic_layout`  
- {doc}`../analog_flows/klayout_drc_lvs`  
```

## Further Reading

