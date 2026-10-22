# S7-1200 PID Ladder Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Extend `tia-v17-plc-programming` with reusable S7-1200/TIA Portal V17 LAD guidance for PID, deeply covering `PID_Compact` first.

**Architecture:** Add a focused PID reference page under the existing skill and route PID requests to it from the skill entry point. Cross-link the existing LAD/FBD workflow rather than duplicating PID procedure; keep Siemens PDFs outside GitHub and cite official sources and manual pages.

**Tech Stack:** Markdown Agent Skill files, Siemens S7-1200 System Manual, TIA Portal V17 Information System/F1, Git.

## Global Constraints

- The guidance must work across S7-1200 projects without assuming a particular CPU, firmware, signal module, I/O address, sensor, actuator, tag, process range, or safety behavior.
- These findings are baseline guidance, not proof that every option or version is available for a specific TIA Portal V17 installation or S7-1200 CPU/firmware. The skill must instruct users to verify version-specific behavior against TIA Portal V17 Information System/F1 and the exact CPU/module manual.
- It must not invent raw analog ranges, engineering-unit bounds, wiring, sampling times, tuning values, or safety responses.
- The user provided a private Google Drive folder containing Siemens manuals, which is accessible for research in the current environment. Treat it as a private reference library, not a distributable artifact. Do not include the user's Drive path or folder URL in the public repository. Instead, instruct each project to use its own `Manual/` folder or a user-configured manual library, and ask for the matching manual if it is not accessible.
- Do not add vendor PDFs to the GitHub repository; store official links, document identifiers, section/page references, and concise original summaries instead.
- `PID_Compact` is the only deeply specified controller in the initial scope; `PID_3Step` and `PID_Temp` remain clearly distinguished future extensions.
- Do not claim a TIA Portal compile or hardware test unless actually performed.

---

### Task 1: Add PID_Compact reference

**Files:**
- Create: `tia-v17-plc-programming/references/pid-control.md`

**Interfaces:**
- Consumes: project/manual discovery and source precedence from `SKILL.md` and `references/source-policy.md`.
- Produces: a focused PID workflow linked by the skill entry point in Task 2.

- [ ] **Step 1: Draft the reference with explicit project discovery gates**

Create `references/pid-control.md` with these sections:

1. Scope and source requirements: S7-1200/TIA V17; discover CPU, firmware, instruction/technology-object version, module, sensor, actuator, units, tag/interface, and the project's own manuals.
2. Controller choice: distinguish `PID_Compact` from `PID_3Step` and `PID_Temp`; detail only `PID_Compact` and label the other controllers as future coverage.
3. Technology object and instruction: explain that the TIA object holds loop configuration, and that the block call/instance DB are created and configured in TIA according to the actual project/version.
4. Analog signal path: establish whether `Input`/`Input_PER` and `Output`/`Output_PER` are supported and appropriate; explain scaling and validation with `NORM_X`/`SCALE_X` only when justified by the selected I/O path and source manual.
5. Ordered LAD design: define purpose, confirmed conditions, block call/pins, tag connections, behavior, and TIA steps for each requested network; identify cyclic execution and sample-time requirements from the target documentation.
6. Modes, limits, tuning, diagnostics, interlocks, faults, and recovery: specify questions and checks required before producing concrete network behavior; do not supply guessed tuning or fail-safe values.
7. Validation and commissioning checklist: compile and simulate/test in the target TIA version when available; distinguish documentation guidance from actual compile/hardware evidence.

Cite the local system manual as baseline only: A5E02486680-AL V4.2.3 (08/2018), section 10.2 page 574, section 10.2.1 page 576, section 10.2.5 page 608, and section 8.7.5 pages 291–293. Link the official Siemens/TIA references. State that exact feature and range claims require the installed TIA V17/CPU/module documentation.

- [ ] **Step 2: Review source claims and boundaries**

Check each manual-specific statement against the cited page and revise or remove unsupported statements. Ensure the document:

- Does not recommend calling PID from OB1 where the cited target documentation requires constant cyclic execution.
- Does not give a raw analog range, fixed scaling limits, PID tuning constants, tag names, wiring, or output state without project evidence.
- Does not treat the user's private Google Drive path or folder URL as a universal/public skill path.
- Does not imply `PID_Compact`, `PID_3Step`, and `PID_Temp` are interchangeable or safety-rated.

- [ ] **Step 3: Check Markdown whitespace**

Run from the repository root:

```powershell
git diff --check
```

Expected: no output and exit code `0`.

### Task 2: Route PID requests into the reference

**Files:**
- Modify: `tia-v17-plc-programming/SKILL.md`
- Modify: `tia-v17-plc-programming/references/lad-fbd.md`

**Interfaces:**
- Consumes: `references/pid-control.md` created in Task 1.
- Produces: an explicit route from PID/analog PID/technology-object requests to the focused reference, while preserving existing SCL and general LAD/FBD flows.

- [ ] **Step 1: Add PID routing to the skill entry point**

Add a workflow bullet in `SKILL.md` directing PID control, PID technology-object, and analog PID requests to `[PID control workflow](references/pid-control.md)`. Leave existing general LAD/FBD and SCL routing intact.

- [ ] **Step 2: Cross-link the general LAD/FBD workflow**

Add a concise note in `references/lad-fbd.md` directing PID-specific control and commissioning requests to `[PID control workflow](pid-control.md)`. Do not duplicate the detailed PID instructions there.

- [ ] **Step 3: Verify the routing targets exist**

Run from the repository root:

```powershell
$skill = Get-Content -Raw 'tia-v17-plc-programming\SKILL.md'
$lad = Get-Content -Raw 'tia-v17-plc-programming\references\lad-fbd.md'
$pidPath = 'tia-v17-plc-programming\references\pid-control.md'
if ($skill -notmatch 'references/pid-control\.md' -or
    $lad -notmatch 'pid-control\.md' -or
    -not (Test-Path $pidPath)) {
    throw 'PID reference routing is incomplete.'
}
```

Expected: no output and exit code `0`.

### Task 3: Validate the skill package and evidence

**Files:**
- Verify: `tia-v17-plc-programming/SKILL.md`
- Verify: `tia-v17-plc-programming/references/lad-fbd.md`
- Verify: `tia-v17-plc-programming/references/pid-control.md`
- Verify: `tia-v17-plc-programming/references/source-policy.md`
- Verify: repository contains no added Siemens PDF

**Interfaces:**
- Consumes: all documentation changes from Tasks 1 and 2.
- Produces: a checked, internally linked skill package with honest validation status.

- [ ] **Step 1: Check every relative Markdown link in the touched skill files**

Run from the repository root:

```powershell
$files = @(
    'tia-v17-plc-programming\SKILL.md',
    'tia-v17-plc-programming\references\lad-fbd.md',
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

Expected: no output and exit code `0`; all internal links resolve and official Siemens links remain external URLs.

- [ ] **Step 2: Check diff and repository scope**

Run:

```powershell
git diff origin/main...HEAD --check
git status --short
git diff --stat origin/main...HEAD
git diff --name-only origin/main...HEAD
git diff --name-only origin/main...HEAD -- '*.pdf'
```

Expected: no whitespace errors or uncommitted changes. The branch diff includes only the approved design spec and plan plus the PID reference and its two routing/cross-reference files. The final command prints no paths.

- [ ] **Step 3: Report validation accurately**

State that the documentation structure and links were checked. State that TIA Portal V17 compilation and hardware commissioning were not run unless direct evidence becomes available during execution.
