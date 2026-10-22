# Siemens Manual Library Workflow

Use this workflow before implementing PLC logic. It establishes document coverage, not source applicability: read every accessible document, then rely only on evidence that matches the target hardware and task.

## Discover the sources

1. Inspect the active project's `Manual/` folder, including nested folders.
2. Check for the private, user-local configuration file `~/.copilot/siemens-manual-library.local.md` (`%USERPROFILE%\.copilot\siemens-manual-library.local.md` on Windows). If it exists, use it to locate the manual library. Treat its contents as private configuration: never copy its URL, credentials, or other private values into tracked files or public output.
3. If no local configuration exists, use a library URL or path supplied in the current conversation or project instructions. If no source is available, ask the user for the library location; do not silently skip the library or imply it was checked.

Keep source locations and any access details private. Do not copy vendor manuals or substantial manual text into the public skill repository.

## Inventory and read every accessible document

Recursively enumerate folders and documents in both the project `Manual/` folder and the configured library. Inventory current contents rather than relying on a remembered or hardcoded file list. Include all accessible document formats. Use available text extraction for digital documents and OCR for scanned or image-only content; follow nested folders and account for files that cannot be listed or opened.

Before implementation, read all pages and content in each accessible document. A filename, partial preview, search snippet, abstract, or table of contents alone is not a full read. When the extracted content exceeds context limits, process it in chunks and track page or section coverage until the entire document is covered.

Maintain a per-task inventory with each file's name or identifier, type, and one of these exact statuses:

- `fully read` — all document content was read.
- `partially read` — some content was read, but full coverage was not achieved.
- `not readable` — the file was available, but its content could not be read or extracted.
- `not accessible` — the file or its contents could not be accessed.

For every status other than `fully read`, record the specific reason and, when relevant, pages or sections covered and missing. Distinguish access, listing, unsupported-format, extraction, OCR, and context/coverage limitations. If complete coverage cannot be achieved, state that the library was not fully read and ask the user for an accessible copy, a supported format, or a smaller source copy. Do not present a partial read as complete.

## Select applicable evidence and cite it

Reading the complete library does not make every document applicable. Identify the target CPU and firmware, modules, sensors, actuators, and other relevant devices; use device-specific facts only from documents that match the target. Keep product families distinct and do not transfer limits, behavior, or configuration details from one family to another.

For device-specific claims, cite the document title or identifier and the relevant section and/or page. Preserve source specificity: use the exact target CPU/firmware manual for hardware facts, and the TIA Portal V17 Information System/F1 for V17 editor, instruction, and language-syntax behavior. Verify version-specific behavior against both the TIA Portal V17 Information System/F1 and the manual for the configured target hardware. A manual's document revision is not the TIA Portal version. If the target or applicable source is unclear, or sources conflict, report the uncertainty and request the missing hardware or documentation details rather than guessing.
