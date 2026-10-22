# PID Control Workflow

Use this reference to design a project-specific PID workflow in LAD for an S7-1200 and TIA Portal V17. This is a requirements and implementation guide, not a ready-to-download block or a substitute for the active project's configuration.

## Scope and source requirements

Before specifying networks, inspect the active project and establish:

- Exact CPU order number, firmware, and configured device.
- Installed TIA Portal version/update and the available PID instruction and technology-object versions. Select the version actually available to the project; a PID_Compact V2.3 entry seen in one environment is not a universal version claim.
- The target PID instruction, existing technology object and instance DB (if any), hosting block/OB, and the project’s current LAD conventions.
- The process variable and setpoint, their data types and engineering units, source module/channel, actuator/output path, and any existing tag or block interface definitions.
- Inspect the active project's `Manual/` folder or configured manual library for applicable project, CPU, and module manuals, plus the TIA Portal V17 Information System/F1 entry for the selected instruction. If the matching manual is unavailable or inaccessible, ask the user to provide it before relying on device-specific claims.

Use the source precedence in [source-policy.md](source-policy.md). The local S7-1200 system manual cited below is a 2018 baseline, not proof that a feature, limit, pin, or behavior applies to every V17 installation, CPU, firmware, or module. Verify exact feature and range claims against the installed TIA V17 help and the manuals for the configured hardware.

## Choose the controller

The S7-1200 system manual distinguishes the instructions by application: `PID_Compact` is for processes with continuous input and output variables; `PID_3Step` is for motor-actuated devices such as valves requiring discrete open/close signals; and `PID_Temp` addresses temperature-control requirements. These descriptions are baseline guidance, not a substitute for checking availability and behavior in the target project (A5E02486680-AL, V4.2.3, 08/2018, §10.2, p. 574).

This reference covers `PID_Compact` only. `PID_3Step` and `PID_Temp` are future coverage; do not treat these controllers as interchangeable. Do not infer safety-rated behavior from a PID instruction.

## Technology object and instruction

The technology object holds the loop configuration. In the cited manual, inserting the instruction in STEP 7 creates its associated technology object and instance DB; each PID instruction has its own unique instance DB (A5E02486680-AL, §10.2.1, p. 576). In the actual project, use TIA to insert or inspect the instruction and confirm the generated/associated object, DB, available pins, and configuration against the selected TIA, CPU, and instruction version. Do not invent an instance name or assume an object/DB already exists.

Document the concrete block call only after those details are confirmed:

- Host block and execution context.
- Exact instruction and version, technology object, and instance DB.
- Actual interface pins and data types displayed by the target TIA project.
- Confirmed signal/tag connections, required operating mode, and any configured limits.

## Analog signal path

Choose the input and output path from the real module/channel configuration and the selected instruction help. The cited manual describes a choice between `Input` and `Input_PER` for the process value, and between `Output` and `Output_PER` for the output; it describes the peripheral variants as direct analog-module paths. It also says the process value must be scaled when using `Input_PER` (A5E02486680-AL, §10.2.5, p. 608). Confirm the exact semantics, representation, data types, and compatibility for the target project before using either path.

Use `NORM_X` and/or `SCALE_X` only if the selected path requires a conversion and the module manual establishes its raw representation and engineering endpoints. The cited baseline defines `NORM_X` as mapping a value from a specified range to 0.0–1.0 and `SCALE_X` as mapping a normalized value to specified minimum and maximum values (A5E02486680-AL, §8.7.5, pp. 291–293). Its analog example is not a universal module range. Do not copy example raw counts, limits, units, or scaling constants into a project without its module and signal evidence. Check input bounds, invalid values, output conversion, and the instructions’ status/error handling.

## Ordered LAD design

For each network the user requests, write a design entry with:

1. **Network number and purpose** — state what this network does in the confirmed process sequence.
2. **Execution and conditions** — identify the host block/OB, expected call interval, and only confirmed enable, mode, or permissive conditions.
3. **Block call and pins** — name the verified instruction and instance; list only actual pins required by the design and their project data types.
4. **Tag connections** — use confirmed project tags and signal paths; identify any justified conversion and its sourced limits.
5. **Behavior and assumptions** — describe normal operation, mode/limit effects, scan or timing assumptions, and the expected fault/recovery path.
6. **TIA steps** — say where to insert or inspect the call, how to connect the confirmed pins, and which instruction/technology-object settings to verify.

Order the design so the controller call is executed in the configured cyclic context, and any signal preparation or output handling is placed only where the actual interface and project architecture require it. The cited S7-1200 manual says to execute the PID instruction at constant intervals of its sampling time, preferably in a cyclic OB, and specifically says not to execute it in the main program-cycle OB such as OB 1. It explains that the sampling time is the interval between output calculations (A5E02486680-AL, §10.2, p. 574). Follow the target CPU/TIA documentation for the actual execution block and interval; do not schedule a PID call in OB1 or assume a scan-cycle value.

Do not turn this outline into concrete network behavior until the required process conditions, signals, tags, interfaces, modes, and fault handling have been established. Where a fact is unknown, ask for it or mark the network as blocked on that requirement rather than supplying a guessed value.

## Modes, limits, tuning, diagnostics, interlocks, faults, and recovery

Resolve these project-specific requirements before assigning parameters or detailing mode/interlock networks:

- Which operating modes are required, how are they selected, and what transitions, manual operation, restart behavior, or reset behavior are expected?
- What process-value and output limits are required, and what source/manual justifies them? What must the process do when a limit is reached or a signal is invalid?
- What tuning method is authorized, by whom, and under what operating conditions? Confirm tuning controls and parameters in the selected instruction version; do not invent gains, tuning values, or tuning procedures.
- Which input/module diagnostics and instruction status/error signals must be monitored? What faults must inhibit operation, alarm, or require acknowledgement?
- Which permissives and interlocks are required, where are they implemented, and what are their priority and reset/recovery rules?
- What output state and recovery action are required for CPU restart, controller inactive state, sensor/module fault, actuator fault, or loss of permissive? Confirm the behavior from project requirements and target documentation; never infer that a controller output is a safe actuator state.

The 2018 manual's PID_Compact configuration discussion says configured process-value limits affect controller activity and output when the process value is outside its range, and describes a restart option (A5E02486680-AL, §10.2.5, p. 608). Treat this only as baseline behavior: verify exact target-version behavior and limits, and separately establish the application's safe response.

## Validation and commissioning checklist

- Confirm the CPU, firmware, configured modules, instruction version, technology object, instance DB, tags, and data types match the project evidence.
- Review the LAD network order, call context, constant execution interval, signal scaling, bounds, modes, interlocks, diagnostics, and recovery behavior against the project requirements and target documentation.
- Compile the affected block and PLC program in the target TIA Portal version; record warnings and resolve relevant pin/type/configuration issues.
- Simulate or test normal operation, mode transitions, boundaries, invalid inputs, interlock trips, instruction/module diagnostics, restart, and recovery using approved project test conditions.
- Before hardware commissioning, review the output path and actuator response with the responsible controls/safety process; do not use unverified fail-safe values.
- Report separately what was documented, what was compiled/simulated in TIA, and what was verified on hardware. Do not claim compilation or commissioning unless actually performed.

## Sources

- Siemens, *S7-1200 Programmable Controller System Manual*, A5E02486680-AL, V4.2.3 (08/2018): §10.2, p. 574; §10.2.1, p. 576; §10.2.5, p. 608; §8.7.5, pp. 291–293. Used as a baseline only; check the exact target CPU, module, firmware, and instruction documentation.
- [TIA Portal V17 Information System](https://docs.tia.siemens.cloud/r/en-us/v17) — verify instruction/editor availability and behavior in the target installation.
- [SIMATIC S7-1200 programmable controller documentation, Siemens document entry 109759862](https://support.industry.siemens.com/cs/document/109759862/simatic-s7-s7-1200-programmable-controller) — select the manual edition for the configured CPU and firmware.
- [S7-1200/S7-1500 programming guideline, Siemens document entry 81318674](https://support.industry.siemens.com/cs/document/81318674/programming-guideline-for-s7-1200-s7-1500) — general guidance only, not proof of V17 or CPU-specific feature support.
