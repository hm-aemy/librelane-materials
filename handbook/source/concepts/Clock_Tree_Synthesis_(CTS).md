# Clock Tree Synthesis (CTS)

**Clock Tree Synthesis (CTS)** is the process of designing and inserting the **clock distribution network** that delivers the clock signal from its source to all sequential elements in a chip.  
Its primary goal is to ensure that the clock reaches all registers **with controlled delay and minimal skew**, while respecting power and signal integrity constraints.

CTS is a critical step between **placement** and **routing**, as it introduces new cells (buffers, inverters) and wires that strongly influence timing and power.

## Why CTS matters

- Ensures **synchronous operation** across the entire design.  
- Controls **clock skew** and **clock latency**, which directly affect timing margins.  
- Impacts **setup and hold timing** on all sequential paths.  
- Influences **power consumption**, since the clock network toggles every cycle.  

## Key objectives of CTS

- **Minimize skew**  
  Reduce the difference in clock arrival time between registers.

- **Control latency**  
  Maintain predictable clock delay from source to sinks.

- **Balance clock paths**  
  Ensure similar electrical characteristics (length, load) across branches.

- **Limit clock power**  
  Avoid unnecessary buffering and excessive wire capacitance.

## Typical CTS inputs

- Placed design (cell locations fixed).  
- Clock definition (period, source, waveform).  
- Available clock buffer and inverter cells.  
- Technology constraints (metal layers, spacing, via rules).  

## Clock tree structure

A clock network is typically built as a **tree topology**, consisting of:

- **Clock source** — PLL, pad, or internal generator.  
- **Root buffer** — first stage driving the clock network.  
- **Intermediate buffers** — balance delay and fanout.  
- **Leaf buffers** — directly drive registers.  
- **Clock sinks** — flip-flops, latches, memory elements.  

Common topologies include:
- H-tree  
- Balanced tree  
- Spine-based distribution  

## CTS vs. data path optimization

CTS is distinct from data-path optimization:

| Aspect | Clock Network | Data Network |
|------|---------------|--------------|
| Switching activity | Always toggles | Data-dependent |
| Timing goal | Minimize skew | Meet setup/hold |
| Topology | Tree-like | Arbitrary |
| Optimization focus | Balance and symmetry | Delay reduction |

## Interaction with timing

CTS directly affects timing closure:

- **Setup timing** depends on clock latency differences between launch and capture.  
- **Hold timing** can be violated if clock paths are unbalanced.  
- CTS may intentionally introduce **useful skew** to improve setup margins.  

As a result, CTS is usually followed by **timing-driven optimization** to fix violations introduced by the clock network.

## CTS in the design flow

CTS typically occurs:

1. After **placement**, when register locations are known.  
2. Before **routing**, so clock wires are treated as first-class nets.  
3. Before final timing optimization and sign-off.  

It may be repeated iteratively if major timing or congestion issues are detected later.

## Outputs of CTS

- Inserted clock buffers and inverters.  
- Clock routing topology (abstract or physical).  
- Updated timing characteristics (skew, latency).  
- Reports summarizing clock quality metrics.  

## Summary

CTS transforms a single clock source into a **balanced and reliable distribution network**.  
It is essential for achieving timing closure and ensuring correct synchronous behavior, especially in large or high-frequency designs.

While implementation details vary across tools, the **fundamental goals and principles of CTS remain the same** across all digital IC design flows.

Additional Material: https://ocdcpro.github.io/educator-portal/material_browser.html?topic=digital&stage=cts