# TIA Portal V17 PLC Programming Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build and validate a reusable Agent Skill for TIA Portal V17 SCL and LAD/FBD PLC programming, distributed from `BaByGOTza/Skill_S1200_Siemens`.

**Architecture:** Keep the skill in `tia-v17-plc-programming/` with a concise `SKILL.md` entry point and focused reference files for SCL, LAD/FBD, and source selection. Add a repository README with installation instructions for VS Code personal skills; link to Siemens sources instead of redistributing vendor PDFs.

**Tech Stack:** Markdown, Agent Skills `SKILL.md` YAML frontmatter, Windows PowerShell, Git.

## Global Constraints

- This project targets Siemens TIA Portal V17 and S7-1200/S7-1500 PLC work, including Structured Control Language (SCL) and vendor reference manuals.
- Treat `Data_Processing.scl` as the intended source for the `Data_Processing` FC1 block. It is currently empty; do not invent its interface, tags, or control behavior.
- Before implementing PLC logic, establish the target CPU and firmware, block inputs and outputs, data types, expected behavior, and fault handling. Ask for missing requirements when they affect correctness or safety.
- Use SCL syntax supported by TIA Portal V17 and the target CPU. Preserve established block names and interfaces, use explicit types, and avoid implicit assumptions about scan-cycle timing or process state.
- For SCL copy-body templates intended for an existing TIA block editor, write `VAR_INPUT`, `VAR_OUTPUT`, `VAR_IN_OUT`, and `VAR_TEMP` declarations as `//` comments and state that they must be created in the TIA Block interface. Keep declarations active only when producing a complete importable SCL source; distinguish that from a copy-body template.
- In SCL state machines, wrap each `CASE` branch in a named `REGION Step <state>: <description>` / `END_REGION` so TIA Portal's navigation tree lists the process steps. Keep the region title aligned with the branch's actual behavior.
- Do not confuse a manual's document revision (for example, V1.6) with the TIA Portal version. Treat older S7-1200/S7-1500 programming guidelines as general guidance; verify version-specific features against TIA Portal V17 documentation or compiler results.
- Use the PDFs in `Manual/` for device-specific facts. Do not guess communication parameters, register mappings, limits, or safety behavior; identify the manual section or page when relying on it.
- Whenever a PLC logic change adds, removes, or changes a DB member, update the corresponding TIA DB interface and its `docs/db-settings/` reference in the same change. Keep DB tags, types, defaults, and source-code references consistent; list any required manual TIA edits when no native project is available.
- When reporting a PLC change, summarize the behavior and assumptions, and state whether compilation in the target TIA Portal was verified. Do not claim a successful PLC build unless it was actually run.
- Do not copy Siemens vendor PDFs or their substantial text into this public repository.
- LAD/FBD output is a network design and implementation guide, not a claim of generating an importable graphical block file.

---

## File map

- Create `tia-v17-plc-programming/SKILL.md` as the skill's trigger and routing entry point.
- Create `tia-v17-plc-programming/references/scl.md` for SCL source-form, interface, state-machine, and verification rules.
- Create `tia-v17-plc-programming/references/lad-fbd.md` for network-by-network graphic-language design and TIA implementation instructions.
- Create `tia-v17-plc-programming/references/source-policy.md` for Siemens V17, CPU manual, guideline, and project-manual precedence.
- Create `README.md` for the repository purpose and personal-skill installation/update steps.

### Task 1: Create skill entry point

**Files:**
- Create: `tia-v17-plc-programming/SKILL.md`

**Interfaces:**
- Consumes: the reviewed design at `docs/superpowers/specs/2026-10-21-tia-v17-plc-programming-skill-design.md`.
- Produces: skill name `tia-v17-plc-programming`, a clear description that triggers for Siemens S7-1200/S7-1500 TIA Portal V17 PLC programming requests, and relative links to the three reference files.

- [ ] **Step 1: Create the skill directory**

Run from `C:\Application\Skill_S1200_Siemens`:

```powershell
New-Item -ItemType Directory -Force -Path 'tia-v17-plc-programming\references' | Out-Null
```

Expected: the directory exists and no tracked file is overwritten.

- [ ] **Step 2: Write the skill frontmatter and routing workflow**

Start the file with YAML frontmatter containing:

```yaml
---
name: tia-v17-plc-programming
description: Create, modify, or review Siemens S7-1200/S7-1500 PLC programs for TIA Portal V17 using SCL, LAD, or FBD. Use when a request involves PLC blocks, PLC logic, SCL source, ladder networks, function block diagrams, DB interfaces, or TIA Portal compilation guidance.
---
```

In the body, define this workflow in order:

1. Inspect the active workspace for PLC source, block interface, DB references, existing conventions, and `Manual/`.
2. Identify CPU and firmware, block interface and data types, requested behavior, and fault handling. Ask a focused clarification when missing information could change correctness or safety.
3. Select and read only the relevant reference files:
   - SCL source or template work: `references/scl.md`
   - LAD/FBD logic design: `references/lad-fbd.md`
   - source selection or hardware-specific facts: `references/source-policy.md`
4. Verify version- and CPU-specific claims against Siemens V17 Information System/F1 and the target CPU manual.
5. Implement only the requested scope, preserve established names/interfaces, and update DB interface documentation whenever DB members change.
6. Report assumptions and validation performed; state explicitly whether TIA Portal compilation was actually run.

State that LAD/FBD results are network designs and TIA implementation steps, not importable graphical source. For the originating heat-calculation workspace only, preserve the rule that the empty `Data_Processing.scl` interface and behavior must not be invented.

- [ ] **Step 3: Check the entry point's links and required triggers**

Run:

```powershell
$path = 'tia-v17-plc-programming\SKILL.md'
$text = Get-Content -Raw $path
if ($text -notmatch '(?s)\A---\r?\nname: tia-v17-plc-programming\r?\ndescription: .+\r?\n---') { throw 'Invalid skill frontmatter' }
foreach ($ref in @('references/scl.md', 'references/lad-fbd.md', 'references/source-policy.md')) {
    if ($text -notmatch [regex]::Escape($ref)) { throw "Missing skill routing link: $ref" }
}
'SKILL.md frontmatter and routing links: PASS'
```

Expected: `SKILL.md frontmatter and routing links: PASS`.

- [ ] **Step 4: Commit the entry point**

```powershell
git add -- tia-v17-plc-programming/SKILL.md
git commit -m "feat: add TIA PLC programming skill entry point" -m "Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>"
```

Expected: one commit containing only the skill entry point.

### Task 2: Add SCL and source-selection references

**Files:**
- Create: `tia-v17-plc-programming/references/scl.md`
- Create: `tia-v17-plc-programming/references/source-policy.md`

**Interfaces:**
- Consumes: the entry-point links established in Task 1.
- Produces: precise SCL output requirements and a source-precedence policy shared by later skill use.

- [ ] **Step 1: Write the SCL reference**

Include all of the following sections and rules:

- Decide whether the user needs a complete importable SCL source or a copy-body template for an existing TIA block.
- For copy-body templates, write `VAR_INPUT`, `VAR_OUTPUT`, `VAR_IN_OUT`, and `VAR_TEMP` declarations as `//` comments and explicitly tell the user to create these members in the TIA block interface. For complete importable source, keep the declarations active and include the correct block wrapper.
- Use explicit PLC types and preserve existing block names, interfaces, and symbolic tags. Do not invent interface members, DB tags, or behavior when source requirements are missing.
- For state machines, put every `CASE` branch inside a named `REGION Step <state>: <description>` / `END_REGION`, with the description matching the branch behavior.
- Avoid assumed scan-cycle timing and implicit process state. Make timer instances, reset behavior, edge detection, and retained state explicit where requirements establish them.
- If DB members change, require synchronized TIA DB interface and `docs/db-settings/` updates.
- Validate syntax against the V17 Information System/F1 and the target CPU; never claim compile success without TIA compilation.
- For the originating workspace, state that `Data_Processing.scl` is intended for FC1 but currently empty; ask for its interface/behavior instead of inventing them.

- [ ] **Step 2: Write the source policy reference**

Set this precedence:

1. Exact target CPU/firmware manual for hardware limits, behavior, and device-specific facts.
2. TIA Portal V17 Information System/F1 for V17 SCL, LAD/FBD, block-editor, and instruction syntax/availability.
3. The active project's `Manual/` PDFs and interface/DB documentation for project-specific devices and conventions.
4. Siemens S7-1200/S7-1500 Programming Guideline V16 for general programming guidance only, never as proof that a feature is supported in V17.

Include these source links:

- `https://docs.tia.siemens.cloud/r/en-us/v17`
- `https://support.industry.siemens.com/cs/document/109759862/simatic-s7-s7-1200-programmable-controller`
- `https://support.industry.siemens.com/cs/document/81318674/programming-guideline-for-s7-1200-s7-1500`

Require the skill to identify the manual section/page when it uses device-specific facts, not guess communication parameters, register mappings, limits, or safety behavior, and not copy vendor PDFs or substantial manual text into the public repository.

- [ ] **Step 3: Check SCL and source policy coverage**

Run:

```powershell
$files = @('tia-v17-plc-programming\references\scl.md', 'tia-v17-plc-programming\references\source-policy.md')
foreach ($file in $files) { if (-not (Test-Path $file)) { throw "Missing reference: $file" } }
$scl = Get-Content -Raw $files[0]
$policy = Get-Content -Raw $files[1]
foreach ($term in @('copy-body', 'VAR_INPUT', 'REGION Step', 'Data_Processing.scl', 'docs/db-settings')) {
    if ($scl -notmatch [regex]::Escape($term)) { throw "SCL rule missing: $term" }
}
foreach ($term in @('V17 Information System', 'V16', '109759862', 'Do not copy')) {
    if ($policy -notmatch [regex]::Escape($term)) { throw "Source policy rule missing: $term" }
}
'SCL and source policy coverage: PASS'
```

Expected: `SCL and source policy coverage: PASS`.

- [ ] **Step 4: Commit both references**

```powershell
git add -- tia-v17-plc-programming/references/scl.md tia-v17-plc-programming/references/source-policy.md
git commit -m "docs: define SCL and Siemens source policies" -m "Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>"
```

Expected: one commit containing the two reviewed reference files.

### Task 3: Add LAD/FBD design reference

**Files:**
- Create: `tia-v17-plc-programming/references/lad-fbd.md`

**Interfaces:**
- Consumes: the source policy from Task 2.
- Produces: a consistent network-design format for LAD and FBD requests.

- [ ] **Step 1: Write the LAD/FBD workflow**

Require the skill to:

- Establish the target CPU/firmware and block interface before specifying CPU-dependent blocks or pins.
- Break the requested behavior into ordered networks. For every network, identify its purpose, contacts/conditions or function blocks, exact pins and values, output/coil/result, and relevant scan/state assumptions.
- Explain required TIA Portal editor steps, including any block/interface or DB members that must be created manually.
- Preserve existing tags and block interfaces; identify missing tag names/types as questions rather than silently inventing them.
- Include reset/fault paths and expected outputs only when requirements establish them; do not guess safety behavior.
- If the same logic is also delivered as SCL, keep behavior consistent and label the SCL and graphic representations separately.
- Describe the output as an implementation design, never as generated importable LAD/FBD source.
- Apply the same DB documentation and real-compilation reporting rules as SCL.

- [ ] **Step 2: Verify every required LAD/FBD design element**

Run:

```powershell
$path = 'tia-v17-plc-programming\references\lad-fbd.md'
$text = Get-Content -Raw $path
foreach ($term in @('network', 'CPU', 'firmware', 'pin', 'reset', 'fault', 'implementation design', 'importable')) {
    if ($text -notmatch [regex]::Escape($term)) { throw "LAD/FBD requirement missing: $term" }
}
'LAD/FBD reference coverage: PASS'
```

Expected: `LAD/FBD reference coverage: PASS`.

- [ ] **Step 3: Commit the LAD/FBD reference**

```powershell
git add -- tia-v17-plc-programming/references/lad-fbd.md
git commit -m "docs: define LAD and FBD network design workflow" -m "Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>"
```

Expected: one commit containing the LAD/FBD reference only.

### Task 4: Document installation and validate the skill package

**Files:**
- Create: `README.md`
- Validate: `tia-v17-plc-programming/SKILL.md`
- Validate: `tia-v17-plc-programming/references/scl.md`
- Validate: `tia-v17-plc-programming/references/lad-fbd.md`
- Validate: `tia-v17-plc-programming/references/source-policy.md`

**Interfaces:**
- Consumes: all files produced in Tasks 1-3.
- Produces: install/update instructions and a clean, self-contained skill package ready for distribution from the repository.

- [ ] **Step 1: Write the repository README**

Describe the repository as a distributable TIA Portal V17 PLC programming skill. Give these exact PowerShell commands for personal installation and updates:

```powershell
$source = Read-Host 'Enter the full path to your Skill_S1200_Siemens clone'
$target = Join-Path $HOME '.copilot\skills\tia-v17-plc-programming'
New-Item -ItemType Directory -Force -Path $target | Out-Null
Copy-Item -Path (Join-Path $source '*') -Destination $target -Recurse -Force
```

Explain that `$source` must point to the `tia-v17-plc-programming` folder inside the clone; rerunning the same command updates the installed skill. Tell the user to restart or refresh the VS Code agent session if the skill is not discovered. Link to the skill entry point and state that the Siemens PDFs are intentionally not bundled.

- [ ] **Step 2: Validate files, relative references, and whitespace**

Run from the repository root:

```powershell
$required = @(
    'README.md',
    'tia-v17-plc-programming\SKILL.md',
    'tia-v17-plc-programming\references\scl.md',
    'tia-v17-plc-programming\references\lad-fbd.md',
    'tia-v17-plc-programming\references\source-policy.md'
)
$missing = $required | Where-Object { -not (Test-Path $_) }
if ($missing) { throw "Missing required skill files: $($missing -join ', ')" }
$skill = Get-Content -Raw 'tia-v17-plc-programming\SKILL.md'
if ($skill -notmatch '(?s)\A---\r?\nname: tia-v17-plc-programming\r?\ndescription: .+\r?\n---') { throw 'Invalid SKILL.md frontmatter' }
foreach ($ref in @('references/scl.md', 'references/lad-fbd.md', 'references/source-policy.md')) {
    if (-not (Test-Path (Join-Path 'tia-v17-plc-programming' $ref))) { throw "Broken relative skill reference: $ref" }
}
$vendorFiles = Get-ChildItem -File -Recurse | Where-Object { $_.Extension -eq '.pdf' }
if ($vendorFiles) { throw "Do not bundle vendor PDFs: $($vendorFiles.FullName -join ', ')" }
git diff --check
if ($LASTEXITCODE -ne 0) { throw 'Whitespace check failed' }
'Skill package validation: PASS'
```

Expected: `Skill package validation: PASS` and no PDF files in the repository.

- [ ] **Step 3: Review content against the approved design**

Confirm manually that the references agree on CPU/firmware clarification, V17-versus-V16 source authority, DB interface/documentation consistency, no invented `Data_Processing` interface, no unsupported LAD/FBD import claim, and honest TIA compile status. Do not claim TIA compilation as part of this documentation-only task.

- [ ] **Step 4: Commit the README and final validation fixes**

```powershell
git add -- README.md tia-v17-plc-programming
git commit -m "docs: document installation for TIA PLC skill" -m "Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>"
git status --short --branch
```

Expected: README and skill files committed, and no uncommitted changes remain.
