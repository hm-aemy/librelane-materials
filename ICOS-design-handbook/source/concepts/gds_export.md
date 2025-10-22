# GDSII Export

**GDSII export** is the final step in the integrated circuit (IC) design flow.  
It produces the **layout file** that will be sent for fabrication, representing all the layers, geometries, and masks of the chip.

The GDSII (Graphic Data System II) format is the **industry standard** for exchanging layout data between design tools and foundries.

---

## Why GDS export matters

- It’s the **final representation of the chip** used by the fabrication process.  
- Ensures that every device, wire, and via has been correctly implemented and verified.  
- Enables mask generation for each layer of the semiconductor process.  
- Used for post-layout visualization, inspection, and archiving.  

---

## The GDSII format

- **Structure:** binary format containing layer numbers, polygons, paths, and hierarchy.  
- **Contents:**  
  - All physical geometries (active, poly, metal layers, vias, wells, etc.)  
  - Text labels and annotations  
  - Cell hierarchy definitions  
- **File extension:** `.gds` or `.gdsii`  
- **Alternative formats:** OASIS (`.oas`) — more compact and modern version of GDSII.

---

## Digital GDS export

- **Inputs:**  
  - Routed and verified DEF/LEF files.  
  - Technology files defining layers and mapping to GDS numbers.  

- **Process:**  
  - Layout database is streamed out from the digital flow environment (e.g., OpenROAD).  
  - Layer mapping files convert internal names to foundry-specific layers.  
  - Final GDS is validated with DRC/LVS before tape-out.  

- **Outputs:**  
  - `design.gds` — final chip layout.  
  - `design.lef` — abstract view for integration in analog/mixed-signal contexts.  

-```{seealso}
For digital GDS generation and streaming examples, see:
- {doc}`../digital_flows/openroad/flow_steps`
- {doc}`../digital_flows/librelane/flow_steps`
-```

---

## Analog GDS export

- **Inputs:**  
  - Layout generated in Magic, KLayout, or similar tool.  
  - Verified DRC/LVS-clean layout database.  

- **Process:**  
  - Stream out layout using the foundry’s layer map file.  
  - Verify hierarchy and layer assignments before export.  
  - Optionally merge analog and digital GDS blocks for full-chip integration.  

- **Outputs:**  
  - `block_name.gds` — ready for top-level assembly or tape-out.  

-```{seealso}
For analog layout and export workflows, see:
- {doc}`../analog_flows/magic_layout`
- {doc}`../analog_flows/klayout_drc_lvs`
-```

---

## Mixed-signal integration

When analog and digital blocks are designed separately, their GDS files must be merged.

- **Hierarchical integration:** top-level cell references sub-blocks (analog, digital, IO pads).  
- **Power domain consistency:** align supply names, ESD protection layers, and guard structures.  
- **Verification:** run top-level DRC/LVS before final submission.  

-```{seealso}
See {doc}`../mixed_signal/analog_on_top` for details on mixed-signal GDS integration.
-```

---

## Tape-out checklist

Before sending the GDS to fabrication:
1. All **DRC/LVS/ERC** checks pass.  
2. **GDS layer map** matches foundry definitions.  
3. **Pin names and coordinates** verified against pad frame or IO ring.  
4. **Antenna and density checks** completed.  
5. **Documentation and reports** archived with the GDS file.  

---

## Further Reading

- [GDSII File Format Specification](https://en.wikipedia.org/wiki/GDSII)  
- [OpenROAD Documentation – GDS Stream-Out](https://openroad.readthedocs.io/en/latest/user/StreamOut.html)  
- [Magic VLSI – GDSII Export Guide](http://opencircuitdesign.com/magic/gdsii.html)  
- [KLayout – Reading and Writing GDS Files](https://www.klayout.de/doc-qt5/manual/gds.html)  
- [VSD – GDSII and Tape-out Basics (YouTube)](https://www.youtube.com/watch?v=W2_GvS6hQl4)  
