# SCL Workflow

Use this reference when creating, modifying, or reviewing Siemens SCL for a TIA Portal project.

## Choose the source form first

Confirm whether the user needs:

- **Complete importable source:** a complete block source with active declarations and the correct block wrapper for the known FC, FB, or other block.
- **Copy-body template:** executable statements to paste into an existing TIA block editor, without the block wrapper or active interface declarations.

Do not silently switch forms. If the destination block or its existing interface is unclear, inspect the project or ask the user.

For a copy-body template:

- Show `VAR_INPUT`, `VAR_OUTPUT`, `VAR_IN_OUT`, and `VAR_TEMP` declarations only as `//` comments.
- State that these members must be created in the TIA block interface and tell the user to copy only the executable body into the existing block.
- Do not include a `FUNCTION`, `FUNCTION_BLOCK`, `BEGIN`, or end wrapper as though it were part of the body.

For complete importable source, keep declarations active and include a block wrapper only when the block type, name, interface, and required attributes are known. Never fill unknown interface members with guessed values.

## Design and write the logic

- Inspect the existing source and preserve block names, interface members, PLC types, symbolic tags, and established conventions.
- Use explicit PLC data types. Make conversions, bounds, initialization, retained state, and output assignment behavior clear where the requirements call for them.
- Ask for the target CPU and firmware, block interface, data types, expected behavior, and fault handling when any missing information could change correctness or safety. Do not assume scan-cycle timing, edge behavior, actuator state, or process recovery.
- In state machines, wrap every `CASE` branch in a named `REGION Step <state>: <description>` / `END_REGION`. The region title must describe the actual branch behavior.
- Make timer instances, call conditions, reset behavior, and timer preset units explicit when timers are part of the requested design. Do not invent timer DB names or retained-state requirements.
- Initialize and assign outputs consistently for every execution path that the requested behavior defines. Surface invalid assumptions or unsupported requirements rather than returning plausible-looking placeholder logic.
- Preserve fault handling and interlocks specified by the project. Do not claim standard PLC code is a safety-rated function.

## Keep interfaces and documentation aligned

If a logic change adds, removes, or changes a DB member:

1. Update the corresponding TIA DB interface.
2. Update the matching `docs/db-settings/` reference.
3. Keep tag name, type, default, and source-code references consistent.
4. If no native TIA project is available, list the manual TIA interface changes the user must make.

Do not change unrelated PLC blocks or project files to work around missing requirements.

## Verify and report

- Check syntax and instruction availability in the TIA Portal V17 Information System/F1 and against the selected CPU/firmware.
- Run available repository checks, but do not present them as a substitute for TIA compilation.
- State whether the code is a complete importable source or a copy-body template.
- Report assumptions, changed behavior, fault handling, checks performed, and whether the target TIA Portal compiler was actually run.
- Never claim successful PLC compilation or hardware validation without direct evidence.

For projects containing `Data_Processing.scl` as the intended FC1 source, follow the workspace-specific boundary in `SKILL.md`: do not invent the currently empty block interface, tags, or control behavior.
