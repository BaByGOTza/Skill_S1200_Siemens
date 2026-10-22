# Siemens Manual Library Workflow Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Make the Siemens PLC skill inventory and read every accessible document in the configured manual libraries before PLC work, while grounding technical claims only in sources applicable to the target hardware.

**Architecture:** Add a focused manual-library workflow reference and route the skill entry point and source policy through it. Store the user's private Drive location in a user-local file outside the repository; the public skill documents only the generic configuration location and behavior.

**Tech Stack:** Markdown Agent Skill files, VS Code Copilot skill folder, PowerShell, Google Drive/browser access, PDF text extraction/OCR where available, Git.

## Global Constraints

- For each PLC task, enumerate all files and nested folders in each accessible library; do not assume the present file list is permanent.
- Read the full content of every accessible document before implementation. For PDFs, extract searchable text and process all pages; if a document is scanned, use OCR when available.
- If any file cannot be opened, extracted, or fully processed, report the exact file and limitation. Do not claim the library was fully read; ask the user to provide an accessible copy or access.
- After reading all documents, use only facts from manuals that apply to the identified CPU, firmware, module, sensor, actuator, or other target device.
- Cite device-specific claims to the correct document and section/page. Continue to verify TIA Portal V17 language/editor/instruction behavior in the V17 Information System/F1 and the configured hardware manual.
- The Drive folder URL is private user configuration and must never be embedded in the public skill repository.
- Keep the private URL in `~/.copilot/siemens-manual-library.local.md` (on Windows: `%USERPROFILE%\.copilot\siemens-manual-library.local.md`), a user-local, untracked configuration file. The public skill may document this generic configuration location, but not the private value. The skill reads this file when it exists.
- Do not copy Siemens PDFs or substantial manual text into the public GitHub repository. The library remains in the user's Drive or local/project manual folder.
- Do not claim successful source reading, TIA Portal compilation, simulation, or hardware verification without evidence.

---

### Task 1: Document the manual-library workflow

**Files:**
- Create: `tia-v17-plc-programming/references/manual-library.md`

**Interfaces:**
- Consumes: The approved requirements in `docs/superpowers/specs/2026-10-22-siemens-manual-library-workflow-design.md`.
- Produces: A reusable procedure for the entry point and source policy in Task 2.

- [ ] **Step 1: Define discovery and configuration**

Write a dedicated reference that directs the agent to:

1. Inspect the active project’s `Manual/` folder.
2. Read `~/.copilot/siemens-manual-library.local.md` (`%USERPROFILE%\.copilot\siemens-manual-library.local.md` on Windows) if it exists; treat it as private configuration, never reproduce its URL in public output or tracked files.
3. If no local configuration exists, use a library URL/path supplied in the current conversation or project instructions; otherwise ask the user for it.
4. Recursively inventory all accessible folders and documents, including formats the agent must read using available text extraction or OCR.

- [ ] **Step 2: Define full-read evidence and failure behavior**

Require the agent to read all pages/content in each accessible document before implementation. For each file, record a per-task status of `fully read`, `partially read`, `not readable`, or `not accessible`, plus the reason when incomplete. Define that partial previews, filenames, search snippets, or table-of-contents-only reads do not count as a full read. When an extraction exceeds context limits, process it in chunks and track page/section coverage; if complete coverage cannot be achieved, state that limitation and ask for an accessible or smaller source copy.

- [ ] **Step 3: Define applicability and citations**

Explain that complete library reading does not make every manual applicable. Select facts only from documents matching the target CPU, firmware, module, sensor, actuator, or device; keep product families distinct. Require document title/identifier and section/page for device-specific facts, and preserve TIA V17 Information System/F1 as the authority for version-specific editor, instruction, and syntax behavior.

- [ ] **Step 4: Check the new reference**

From the repository root, run:

```powershell
git diff --check
```

Expected: no output and exit code `0`.

### Task 2: Integrate library discovery into the skill

**Files:**
- Modify: `tia-v17-plc-programming/SKILL.md`
- Modify: `tia-v17-plc-programming/references/source-policy.md`

**Interfaces:**
- Consumes: `references/manual-library.md` from Task 1.
- Produces: A mandatory manual-discovery/read stage before PLC implementation and consistent source-precedence rules.

- [ ] **Step 1: Route the skill workflow through manual-library.md**

Update `SKILL.md` workflow to require reading the manual-library procedure before implementation and add `[Manual library workflow](references/manual-library.md)` to the reference routes. Preserve the existing SCL, LAD/FBD, PID, V17, CPU, and heat-calculation project boundaries.

- [ ] **Step 2: Clarify source policy**

Update `references/source-policy.md` to distinguish:

- Library coverage: inventory and read all accessible documents.
- Evidence applicability: use only documents matching the identified target.
- Reporting: state per-file read coverage and request inaccessible manuals.

Retain the existing precedence and do not add the private Drive URL or PDF files.

- [ ] **Step 3: Verify the route and local-config wording**

Run from the repository root:

```powershell
$skill = Get-Content -Raw 'tia-v17-plc-programming\SKILL.md'
$policy = Get-Content -Raw 'tia-v17-plc-programming\references\source-policy.md'
$manual = Get-Content -Raw 'tia-v17-plc-programming\references\manual-library.md'
if ($skill -notmatch 'references/manual-library\.md' -or
    $policy -notmatch 'manual-library\.md' -or
    $manual -notmatch 'siemens-manual-library\.local\.md') {
    throw 'Manual-library workflow routing or local config instructions are incomplete.'
}
```

Expected: no output and exit code `0`.

### Task 3: Create the private local library setting and validate package

**Files:**
- Create locally, outside Git: `%USERPROFILE%\.copilot\siemens-manual-library.local.md`
- Verify: `tia-v17-plc-programming/SKILL.md`
- Verify: `tia-v17-plc-programming/references/manual-library.md`
- Verify: `tia-v17-plc-programming/references/source-policy.md`
- Verify: `tia-v17-plc-programming/references/pid-control.md`

**Interfaces:**
- Consumes: Documentation workflow from Tasks 1 and 2.
- Produces: Private per-user Drive configuration and a privacy-checked, internally linked skill package.

- [ ] **Step 1: Create private config using a prompt, not a tracked literal**

Run in PowerShell on the user's machine:

```powershell
$path = Join-Path $HOME '.copilot\siemens-manual-library.local.md'
$directory = Split-Path $path
New-Item -ItemType Directory -Force -Path $directory | Out-Null
$url = Read-Host 'Paste the private Siemens manuals Google Drive folder URL'
if ($url -notmatch '^https://drive\.google\.com/drive/') {
    throw 'Expected an HTTPS Google Drive folder URL.'
}
"Siemens manual library: $url" | Set-Content -LiteralPath $path -Encoding utf8
```

Expected: the private configuration file exists outside the repository; its contents are never printed, staged, or committed.

- [ ] **Step 2: Verify the config path without exposing its contents**

Run:

```powershell
$path = Join-Path $HOME '.copilot\siemens-manual-library.local.md'
if (-not (Test-Path -LiteralPath $path)) {
    throw 'Private Siemens manual-library configuration was not created.'
}
Write-Output 'Private manual-library config exists; contents intentionally not displayed.'
```

Expected: the confirmation line only; do not use `Get-Content` on this private file in a way that prints the URL.

- [ ] **Step 3: Verify full reading against the currently configured library**

Using the Drive URL in the private config, enumerate the live folder contents and nested folders. For every current file, read the entire document using the browser's document view or local text extraction; process scanned pages with OCR if available. Record the filename, document type, page count when applicable, and one of `fully read`, `partially read`, `not readable`, or `not accessible` in a local scratch report outside the repository. Do not save manual text or the private Drive URL to tracked files. If any file is inaccessible or only partially read, identify it and ask for an accessible copy before claiming the full-library check passed.

Expected: every currently listed document has full-content coverage recorded, or a specific access/extraction limitation is reported and resolved before proceeding. Use the current live inventory rather than assuming an earlier file count.

- [ ] **Step 4: Check relative Markdown links in the skill package**

Run from the repository root:

```powershell
$files = @(
    'tia-v17-plc-programming\SKILL.md',
    'tia-v17-plc-programming\references\source-policy.md',
    'tia-v17-plc-programming\references\manual-library.md',
    'tia-v17-plc-programming\references\pid-control.md'
)
$broken = foreach ($file in $files) {
    $content = Get-Content -Raw $file
    foreach ($match in [regex]::Matches($content, '\[[^\]]+\]\(([^)]+)\)')) {
        $target = $match.Groups[1].Value.Trim().Trim('<', '>')
        if ($target -match '^https?://') { continue }
        $target = ($target -split '#', 2)[0]
        if ([string]::IsNullOrWhiteSpace($target)) { continue }
        $resolved = Join-Path (Split-Path $file) ($target -replace '/', '\')
        if (-not (Test-Path $resolved)) { "$file -> $target" }
    }
}
if ($broken) {
    $broken
    throw 'Broken relative Markdown links found.'
}
```

Expected: no output and exit code `0`.

- [ ] **Step 5: Check diff scope, private-data exclusion, and PDF exclusion**

Run:

```powershell
$base = 'db26f082f72dc82de6d79e0ccd08122a6d4a9a7b'
git diff $base...HEAD --check
git status --short
$expected = @(
    'docs/superpowers/plans/2026-10-22-siemens-manual-library.md',
    'docs/superpowers/specs/2026-10-22-siemens-manual-library-workflow-design.md',
    'tia-v17-plc-programming/SKILL.md',
    'tia-v17-plc-programming/references/manual-library.md',
    'tia-v17-plc-programming/references/source-policy.md'
)
$changed = @(git diff --name-only $base...HEAD)
$unexpected = Compare-Object -ReferenceObject $expected -DifferenceObject $changed
if ($unexpected) {
    $unexpected
    throw 'Unexpected tracked files in the manual-library change.'
}
git diff --name-only $base...HEAD -- '*.pdf'
$privateMatches = git diff --unified=0 $base...HEAD | Select-String 'https?://drive\.google\.com/drive/|AIza[0-9A-Za-z_-]{30,}'
if ($privateMatches) {
    $privateMatches
    throw 'A private Drive URL or credential pattern appears in the branch diff.'
}
```

Expected: no whitespace errors or uncommitted changes; the branch contains exactly the five listed plan/spec/skill files; no PDF, local config file, private Drive URL, or credential appears in the branch diff.

- [ ] **Step 6: Report source coverage honestly**

State that documentation and links were checked. For any real task using the library, report each file’s read coverage and applicability. Do not claim full-library reading unless every enumerated file was processed. Do not claim TIA compilation or hardware validation unless performed.
