# LAD and FBD Workflow

Use this reference when the user asks for ladder logic (LAD) or function block diagram (FBD) logic in TIA Portal.

## Gather requirements before designing

- Inspect the active project's blocks, tags, DB references, and conventions before proposing networks.
- Establish the CPU and firmware, block interface, signal types, expected sequence, and fault/reset behavior when they affect the design.
- Ask for missing tag names, types, reset behavior, or process states instead of inventing them.
- Check CPU-specific block availability, pin types, and language behavior in the TIA Portal V17 Information System/F1 and target CPU manual.

## Deliver an ordered network design

Describe the result as an **implementation design** for the user to build and verify in the TIA Portal editor. Do not claim that the response is an importable graphical source file.

For each network, provide:

1. **Network number and purpose** aligned with its place in the requested sequence.
2. **LAD layout:** contacts and conditions from left to right, branch/parallel logic, block calls, coils or outputs, and the exact tags involved.
3. **FBD layout:** function blocks, signal/data flow, exact input and output pins, tag connections, and parameter values. Name instance DBs only when confirmed by the project or requirements.
4. **Behavior:** describe when the network becomes true, the resulting state/output, and any relevant scan, edge, timer, or reset assumptions established by the requirements.
5. **TIA implementation steps:** identify the block and network to open, elements to place, parameters/pins to set, and interface/DB members that must already exist or be created.

Use a compact layout such as:

| Network | Purpose | Conditions and blocks | Connections/parameters | Result |
|---|---|---|---|---|
| 1 | User-specific purpose | Confirmed conditions and block names | Confirmed pins and values | Specified output or state |

Replace this outline with the actual requested design; do not fill unknown names or values with invented examples.

## Preserve behavior and safety boundaries

- Keep tags, block interfaces, DB members, and existing network behavior consistent with the project.
- Make timer, edge-detection, latch, and reset semantics explicit only when the design requirements establish them.
- Identify the fault path and expected recovery behavior. If either is unspecified and affects correctness or safety, ask before finalizing the network design.
- Do not imply ordinary CPU LAD/FBD logic is a safety-rated function or substitutes for an independent safety circuit.
- If both SCL and LAD/FBD are requested, describe equivalent behavior in both and call out any representation-specific differences.
- If a DB member changes, require the matching TIA DB interface and `docs/db-settings/` reference to change together.

## Verify and report

- Check instruction/block syntax, availability, and pin compatibility against TIA Portal V17 and the target CPU/firmware.
- Provide a validation checklist the user can carry out in TIA Portal: compile the affected block, compile the PLC program, and test specified normal/fault transitions before hardware commissioning.
- Report assumptions, missing project details, and checks performed. State whether TIA Portal compilation was actually run; never claim compilation or hardware validation without evidence.
