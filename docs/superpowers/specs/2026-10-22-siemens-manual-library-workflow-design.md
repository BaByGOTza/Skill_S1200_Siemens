# Siemens Manual Library Workflow Design

## Goal

Update the Siemens PLC programming skill so that it inventories and reads every accessible document in a user-configured Siemens manual library before PLC work, while basing each technical claim only on manuals applicable to the target CPU, module, or device.

## User problem

The skill currently directs the agent to inspect the active project's `Manual/` folder but does not state that it must inspect the complete user-provided Siemens documentation library. The user wants the skill to use the full contents of their private Google Drive manual folder as research material for PLC tasks.

The currently shared folder snapshot contains three PDF manuals: an S7-1200 System Manual, the S7-1200/S7-1500 Programming Guideline V16, and an HR-X series PROFINET communication guide. The library may change, so the workflow must enumerate its live contents rather than hardcode this inventory.

## Approved reading behavior

For each PLC task:

1. Locate the active project's own `Manual/` folder and the user-configured Siemens manual library, if available.
2. Enumerate all files and nested folders in each accessible library; do not assume the present file list is permanent.
3. Read the full content of every accessible document before implementation. For PDFs, extract searchable text and process all pages; if a document is scanned, use OCR when available. Keep a per-task inventory with each document's name, type, and read status.
4. If any file cannot be opened, extracted, or fully processed, report the exact file and limitation. Do not claim the library was fully read; ask the user to provide an accessible copy or access.
5. After reading all documents, use only facts from manuals that apply to the identified CPU, firmware, module, sensor, actuator, or other target device. Reading an unrelated manual does not make its facts applicable.
6. Cite device-specific claims to the correct document and section/page. Continue to verify TIA Portal V17 language/editor/instruction behavior in the V17 Information System/F1 and the configured hardware manual.

This workflow records full-document coverage but does not require placing entire manual contents in the conversation or public repository. A concise per-task catalog and page/section references provide traceability.

## Privacy and portability

- The Drive folder URL is private user configuration and must never be embedded in the public skill repository.
- Keep the private URL in `~/.copilot/siemens-manual-library.local.md` (on Windows: `%USERPROFILE%\.copilot\siemens-manual-library.local.md`), a user-local, untracked configuration file. The public skill may document this generic configuration location, but not the private value. The skill reads this file when it exists.
- If the private local configuration is absent, use a library link or path supplied in the current conversation or project instructions. If none is accessible, ask the user for the source; do not silently skip it or claim it was read.
- Do not copy Siemens PDFs or substantial manual text into the public GitHub repository. The library remains in the user's Drive or local/project manual folder.
- A different user can point the skill to a different library and use it with their own S7-1200 projects. Do not assume every user has the same Drive, files, or hardware.

## Proposed repository changes

- Add a focused `references/manual-library.md` describing discovery, recursive inventory, full-document read/OCR requirements, per-task coverage status, source applicability, and inaccessible-file handling.
- Update `SKILL.md` to require completing the manual-library inventory/read step before PLC implementation and to route source selection to the new reference.
- Update `references/source-policy.md` to distinguish library coverage (read all accessible docs) from applicability (cite only target-relevant docs) and restate the privacy/no-PDF boundary.
- Keep the user's private Drive URL and any credential out of tracked files. Provide `~/.copilot/siemens-manual-library.local.md` separately, outside the repository.
- Avoid changing PID/LAD logic details unrelated to document discovery and use.

## Error and limitation handling

- Distinguish among folder unavailable, permission denied, file omitted from listing, unsupported file type, extraction failure, OCR failure, and content too large to process fully.
- State which documents were fully read, partially read, or not read, and why.
- Do not fabricate a successful source check when any required document is unavailable.
- If the library contains manuals for multiple product lines, keep facts attributed to the correct product; do not generalize HR-X, S7-1200, and other manuals across device families.

## Acceptance criteria

1. The workflow requires recursive inventory and full-document reading of every accessible file in the configured manual library and the active project's `Manual/` folder.
2. It reports per-file read status and explicitly asks for access or a copy when a required document cannot be fully read.
3. It separates “read all documents” from “use only applicable evidence” and requires exact document/section/page citations for device-specific claims.
4. It preserves TIA V17/F1 verification, CPU/firmware-specific source precedence, and all existing PLC correctness/safety boundaries.
5. No private Drive URL, credential, or Siemens PDF is committed to the public repository.
6. The generic skill remains usable by other projects/users with their own configured manual libraries, and no unsupported successful-read or compilation claims are made.
