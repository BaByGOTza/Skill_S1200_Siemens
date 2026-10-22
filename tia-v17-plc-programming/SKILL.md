---
name: tia-v17-plc-programming
description: Create, modify, or review Siemens S7-1200/S7-1500 PLC programs for TIA Portal V17 using SCL, LAD, or FBD. Use when a request involves PLC blocks, PLC logic, SCL source, ladder networks, function block diagrams, DB interfaces, or TIA Portal compilation guidance.
---

# TIA Portal V17 PLC Programming

Use this skill for Siemens S7-1200/S7-1500 programming tasks targeting TIA Portal V17. Preserve the active project's existing block names, interfaces, tags, and conventions.

## Workflow

1. Inspect the active workspace for relevant PLC source, block interfaces, DB references, project instructions, and `Manual/` files. Read enough context to understand the existing behavior before proposing changes.
2. Establish the target CPU and firmware, block inputs and outputs, data types, expected behavior, and fault handling. If a missing detail can affect correctness or safety, ask a focused question before implementing; do not invent requirements.
3. Load only the relevant reference:
   - SCL source or template work: [SCL workflow](references/scl.md)
   - LAD/FBD logic design: [LAD/FBD workflow](references/lad-fbd.md)
   - PID control, PID technology-object, and analog PID requests: [PID control workflow](references/pid-control.md)
   - Documentation selection or hardware-specific facts: [source policy](references/source-policy.md)
4. Verify version- and CPU-specific claims against the TIA Portal V17 Information System/F1 and the manual for the target CPU/firmware.
5. Implement only the requested scope. Preserve established interfaces and types. If DB members change, update the matching TIA DB interface and `docs/db-settings/` reference in the same change.
6. Validate the result using the available project checks. Report assumptions, faults and edge cases addressed, checks run, and whether compilation was actually performed in the target TIA Portal.

## Boundaries

- Do not guess communication parameters, register mappings, device limits, or safety behavior. Cite the source manual section or page when using device-specific facts.
- Do not claim a TIA compile or hardware validation succeeded unless it was actually run.
- LAD/FBD deliverables are ordered network designs and TIA implementation instructions, not generated importable graphical block files.
- Do not copy vendor manuals into this skill repository.
- If the active workspace is the heat-calculation project containing `Data_Processing.scl`, treat it as the intended source for FC1. That source is currently empty: do not invent its interface, tags, or control behavior.
