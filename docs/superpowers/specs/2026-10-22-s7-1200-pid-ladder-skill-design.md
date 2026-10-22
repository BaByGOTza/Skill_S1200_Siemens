# S7-1200 PID Ladder Skill Design

## Goal

Extend the existing `tia-v17-plc-programming` skill with reusable, project-independent guidance for configuring and implementing Siemens PID control in TIA Portal V17 LAD. Start with `PID_Compact`, while establishing a clear path to cover `PID_3Step` and `PID_Temp` later.

The guidance must work across S7-1200 projects without assuming a particular CPU, firmware, signal module, I/O address, sensor, actuator, tag, process range, or safety behavior.

## User problem

The existing skill gives a general LAD/FBD workflow but does not explain PID technology objects, cyclic execution, analog signal preparation, controller modes, commissioning, or the different purposes of Siemens PID instructions. The user wants to create a Ladder-focused skill extension, beginning with PID, that can also be reused in other S7-1200 projects.

## Scope

### Initial coverage: PID_Compact

The new reference will explain a requirements-first LAD workflow for:

1. Identifying the actual CPU, firmware, TIA Portal version, installed instruction/technology-object version, sensor/transmitter, analog module, and actuator from the active project and its manuals.
2. Selecting and creating the `PID_Compact` technology object and its associated instruction/instance data in TIA Portal.
3. Configuring the controller and arranging the LAD call and supporting networks, including the correct cyclic execution arrangement and confirmed sample-time behavior.
4. Preparing and validating the process value and output path, including when scaling is needed and the use of analog peripheral inputs/outputs only when supported and confirmed.
5. Documenting setpoint, controller direction, limits, manual/automatic transitions, tuning, error/warning handling, interlocks, and recovery requirements.
6. Providing a project-specific validation and commissioning checklist without claiming that a generated network design is an importable TIA project or a verified PLC compile.

### Planned extensions

The same reference may later add separate, clearly distinguished guidance for:

- `PID_3Step`, for suitable motor-actuated/discrete open-close control applications.
- `PID_Temp`, for temperature-control applications.

Do not present these instructions as interchangeable with `PID_Compact`; determine suitability from the application, selected CPU/firmware, TIA version, and Siemens documentation.

## Research findings and source constraints

The local Siemens S7-1200 System Manual, document A5E02486680-AL, version 4.2.3 (08/2018), describes:

- `PID_Compact` as a controller for continuous input/output variables, `PID_3Step` for motor-actuated devices such as valves with discrete open/close signals, and `PID_Temp` for temperature-control requirements (section 10.2, page 574).
- PID instructions as requiring execution at constant intervals, preferably in a cyclic OB, and not in the main program cycle OB (section 10.2, page 574).
- The technology object as holding loop settings and insertion of a unique instance DB for each PID instruction (section 10.2.1, page 576).
- `PID_Compact` configuration options for selecting `Input` versus analog `Input_PER`, and `Output` versus analog `Output_PER`; use of the peripheral process value requires scaling (section 10.2.5, page 608).
- `NORM_X` and `SCALE_X` as instructions documented in section 8.7.5, pages 291–293.

These findings are baseline guidance, not proof that every option or version is available for a specific TIA Portal V17 installation or S7-1200 CPU/firmware. The skill must instruct users to verify version-specific behavior against TIA Portal V17 Information System/F1 and the exact CPU/module manual. It must not invent raw analog ranges, engineering-unit bounds, wiring, sampling times, tuning values, or safety responses.

Reference links:

- TIA Portal V17 Information System: https://docs.tia.siemens.cloud/r/en-us/v17
- Siemens S7-1200 programmable controller documentation entry: https://support.industry.siemens.com/cs/document/109759862/simatic-s7-s7-1200-programmable-controller
- Siemens S7-1200/S7-1500 programming guideline: https://support.industry.siemens.com/cs/document/81318674/programming-guideline-for-s7-1200-s7-1500

The complete Siemens PDFs remain in the relevant local PLC project's `Manual/` folder. Do not add vendor PDFs to the GitHub repository; store official links, document identifiers, section/page references, and concise original summaries instead.

## Proposed repository changes

- Add `tia-v17-plc-programming/references/pid-control.md` as the focused PID reference, beginning with `PID_Compact`.
- Update `tia-v17-plc-programming/SKILL.md` to route PID, analog PID, and PID technology-object requests to the new reference.
- Add a short cross-reference from `references/lad-fbd.md` to the PID reference rather than duplicating PID-specific procedure there.
- Update `references/source-policy.md` only if needed to make the PID-specific Siemens source requirements explicit.
- Keep the skill installable through the existing README instructions; do not create a separate skill package or duplicate installation workflow.

## Safety and correctness boundaries

- Treat each active project's source, interfaces, I/O configuration, DB references, manual set, and existing conventions as authoritative for that project.
- Ask for missing details whenever they affect signal conversion, controller direction, operation mode, interlocks, output behavior, fault handling, or commissioning safety.
- Keep ordinary process PID distinct from safety-rated functions. Do not replace independent safety measures with a standard PID controller.
- Describe LAD output as ordered, buildable network instructions; do not imply generated graphical source or a validated TIA project.
- If the project has no native TIA files or compiler available, clearly list required manual configuration and state that TIA compilation was not run.
- Preserve the existing workspace-specific `Data_Processing.scl` boundary only for that named heat-calculation project; do not apply it to unrelated projects.

## Acceptance criteria

1. The PID reference is reusable across S7-1200/TIA Portal V17 projects and discovers each project's target hardware and manuals before giving device-specific values.
2. It clearly distinguishes technology-object setup, LAD instruction/network design, analog signal conditioning, controller configuration, fault handling, and validation.
3. PID_Compact is the only deeply specified controller in the initial scope; PID_3Step and PID_Temp are explicitly future extensions with no assumptions of equivalence.
4. Every version- or device-specific claim is linked to an appropriate Siemens source, and no raw range, tag, I/O address, or control response is guessed.
5. No Siemens vendor PDF is added to GitHub.
6. Skill frontmatter, internal links, and routing instructions remain valid; no TIA compile or hardware test is claimed unless actually performed.
