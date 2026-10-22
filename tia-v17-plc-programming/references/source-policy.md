# Siemens Documentation and Source Policy

Use the most specific authoritative source available. Verify version-specific syntax and hardware-specific facts separately; a CPU manual is not a substitute for the TIA Portal programming help, and a programming guideline is not a CPU manual.

## Source precedence

1. **Target CPU and firmware manual:** use for that device's features, limits, behavior, and firmware restrictions.
2. **TIA Portal V17 Information System/F1:** use for V17 SCL, LAD/FBD, editor, instruction, and language feature syntax or availability.
3. **Active project sources and local manuals:** inspect the project's `Manual/` directory, existing PLC sources, block interfaces, DB documentation, and project instructions for device facts and established conventions.
4. **Siemens S7-1200/S7-1500 Programming Guideline V16:** use as general programming guidance only. Its document revision is not the TIA Portal version and does not prove that a feature is supported in TIA Portal V17 or a particular CPU/firmware.

## Library coverage, applicability, and reporting

- **Coverage:** follow the [manual library workflow](manual-library.md) to inventory and read every accessible document in the active project's `Manual/` folder and configured library before implementation. Track read coverage per file; a partial read is not a full read.
- **Applicability:** complete library coverage does not make every document evidence for the task. Use only documents that match the identified target CPU, firmware, relevant devices, and task. Do not transfer device-specific facts across product families.
- **Reporting:** state per-file read coverage and identify files that were partial, unreadable, or inaccessible, including the reason when known. If a manual is inaccessible and full coverage cannot be achieved, say the library was not fully read and ask the user for an accessible copy or source.

## Reference links

- [TIA Portal V17 Information System](https://docs.tia.siemens.cloud/r/en-us/v17)
- [SIMATIC S7-1200 programmable controller, Siemens document entry 109759862](https://support.industry.siemens.com/cs/document/109759862/simatic-s7-s7-1200-programmable-controller)
- [Programming guideline for S7-1200/S7-1500, Siemens document entry 81318674](https://support.industry.siemens.com/cs/document/81318674/programming-guideline-for-s7-1200-s7-1500)

The CPU manual in the active project may be newer or more specific than the linked S7-1200 manual. Check the exact model and firmware edition before relying on it.

## Evidence and safe use

- Cite the source manual section or page for device-specific facts when available.
- Do not guess communication parameters, register mappings, address ranges, limits, or safety behavior.
- If sources are inaccessible or conflict, say so and ask for the relevant manual, CPU/firmware, or TIA compiler result. Do not present uncertain behavior as confirmed.
- Use the V17 Information System/F1 for syntax or feature support; use compiler results only when they were actually produced by the target TIA Portal version and CPU configuration.
- Do not copy Siemens vendor PDFs or substantial manual text into this public repository. Link to the official Siemens source and use project-local licensed manuals when available.
