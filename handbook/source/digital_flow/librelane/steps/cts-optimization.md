# Step V — Clock Tree Synthesis (CTS) and Optimization

The **Clock Tree Synthesis (CTS)** and **Optimization** stage focuses on building a balanced clock network and refining timing across the entire design.  
Its goal is to minimize **clock skew**, **insertion delay**, and **timing violations**, ensuring reliable synchronization of all sequential elements before routing begins.

During this phase, LibreLane uses **OpenROAD** to insert clock buffers, generate the clock tree topology, and perform post-CTS timing-driven optimizations.

---

## Tools Involved

This stage uses:

- **[OpenROAD](../../tools/openroad.md)** — performs CTS, static timing analysis (STA), and timing-driven resizing/repair of the design.  
- **[Checker](../../tools/checker.md)** *(optional)* — validates timing consistency between stages and reports violations.

---

## Sequence of Steps

LibreLane executes the following sub-steps during CTS and optimization:

34. **OpenROAD.CTS** — generates the clock tree network by inserting buffers and balancing clock paths.  
35. **OpenROAD.STAMidPNR-1** — performs static timing analysis after CTS to measure skew and delay.  
36. **OpenROAD.ResizerTimingPostCTS** — resizes and buffers cells to fix setup/hold violations and improve timing.  
37. **OpenROAD.STAMidPNR-2** — re-analyzes the design after optimization to confirm timing closure.

---

## Configuration Overview

CTS and optimization parameters are defined in `config.json`.  
An example configuration block is:

```json
{
  "CTS_BUFFER_LIST": ["clkbuf_1", "clkbuf_2", "clkbuf_4"],
  "CTS_ROOT_BUFFER": "clkbuf_4",
  "CTS_TARGET_SKEW": 0.1,
  "CTS_MAX_CAP": 0.05,
  "CTS_MAX_TRANSITION": 0.2,
  "CTS_CLK_NET": "clk",
  "CTS_REPORTS_DIR": "reports/cts"
}
```
Explanation:

- CTS_BUFFER_LIST — list of available clock buffer cells for the tree.
- CTS_ROOT_BUFFER — defines the top-level clock buffer driving the tree.
- CTS_TARGET_SKEW — target maximum skew (in ns) between clock endpoints.
- CTS_MAX_CAP / CTS_MAX_TRANSITION — design constraints for capacitive load and transition.
- CTS_CLK_NET — specifies the clock net name.
- CTS_REPORTS_DIR — path for CTS logs and timing reports.

## Example: Running Only the CTS Step

You can run only the CTS and optimization stage using:
```
librelane run -d my_design -f cts-optimization
```

LibreLane will:

1. Read the placed DEF from the previous stage.
2. Build the clock tree using OpenROAD’s CTS engine.
3. Run STA to evaluate skew and latency.
4. Resize and buffer critical paths for improved timing.
5. Generate updated DEF and timing reports for the next stage.

Results are saved under:
```
results/cts-optimization/
```

To open the post-CTS layout in the GUI:
```
openroad -gui results/cts-optimization/my_design.def
```
## Expected Outputs

- Post-CTS DEF file: results/cts-optimization/&lt;design&gt;.def
- Updated Verilog netlist: results/cts-optimization/&lt;design&gt;.v
- CTS reports: reports/cts-optimization/cts_report.rpt
- Timing reports: reports/cts-optimization/sta_post_cts.rpt
- OpenROAD logs: logs/cts-optimization/openroad.log

## Common Issues and Checks

- Clock skew too high: adjust CTS_TARGET_SKEW or buffer list to balance the tree.
- Unconnected flip-flops: verify CTS_CLK_NET matches the actual clock port name.
- Timing violations persist: refine cell sizes or constraints, then re-run synthesis or placement if necessary.
- Buffer overuse: reduce available buffers or tweak OpenROAD CTS tuning parameters to limit insertion.

Review the generated reports to ensure:

- Skew < target
- No excessive insertion delay
- Clean STA summary with no setup/hold violations.