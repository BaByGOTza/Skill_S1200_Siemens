# TIA Portal V17 PLC Programming Skill Design

## Goal

Create a reusable personal Agent Skill for writing and modifying Siemens PLC
programs for TIA Portal V17. The skill covers SCL source generation and
network-by-network LAD/FBD design for S7-1200/S7-1500 projects.

## Location and structure

Publish the skill in this repository under
`tia-v17-plc-programming/`:

- `SKILL.md` defines when the skill applies, its workflow, and core constraints.
- `references/scl.md` defines SCL source/template conventions.
- `references/lad-fbd.md` defines the expected LAD/FBD network-design output.
- `references/source-policy.md` defines how to use Siemens documentation and
  project manuals.
- `README.md` explains installation and updating the skill in the user's
  personal VS Code Copilot skill directory.

The repository is the distribution source. Do not copy vendor PDFs into this
public repository; link to Siemens sources and use the relevant project's
local `Manual/` files when available.

## Workflow and behavior

Before proposing PLC logic changes, inspect the relevant project sources,
interfaces, DB references, and manuals. Establish the CPU and firmware, block
interface and data types, expected behavior, and fault handling. Ask for
missing details when they affect correctness or safety; do not invent them.
For the originating heat-calculation project, `Data_Processing.scl` is the
intended source for FC1, but its interface and behavior must not be invented.

Use the TIA Portal V17 Information System/F1 and target CPU documentation to
verify version- and device-specific behavior. The existing S7-1200/S7-1500
Programming Guideline V16 is general guidance only and must not be mistaken
for proof of V17 syntax support. Do not guess device-specific communication,
register, limit, or safety details.

For SCL, distinguish complete importable source from a copy-body template for
an existing block editor. In copy-body templates, show interface declarations
as comments and state that the declarations must be created in the TIA block
interface. Wrap every `CASE` branch in a named `REGION Step <state>:
<description>` / `END_REGION`.

For LAD/FBD, provide ordered networks with contacts/conditions, blocks,
parameters, pin connections, and implementation steps in TIA Portal. Do not
claim to generate an importable graphical block file.

When a logic change adds, removes, or changes DB members, keep the TIA DB
interface and matching `docs/db-settings/` reference consistent. Keep changes
focused, preserve existing names and interfaces, and report assumptions,
validation performed, and whether compilation was actually verified in the
target TIA Portal.

## Sources

- TIA Portal V17 Information System:
  <https://docs.tia.siemens.cloud/r/en-us/v17>
- Siemens S7-1200 System Manual, document ID 109759862:
  <https://support.industry.siemens.com/cs/document/109759862/simatic-s7-s7-1200-programmable-controller>
- Siemens S7-1200/S7-1500 Programming Guideline:
  <https://support.industry.siemens.com/cs/document/81318674/programming-guideline-for-s7-1200-s7-1500>

The target CPU's current manual and the active project's `Manual/` directory
take precedence for model- and firmware-specific facts.

## Validation

Check Agent Skill YAML frontmatter, required directory/file layout, relative
reference links, trigger clarity, and consistency with this design. Review all
guidance against the listed version/source policy. Documentation checks do not
replace compiling or testing a PLC program in TIA Portal.

## Out of scope

- Redistributing vendor manuals or copying their contents into the public repo.
- Claiming TIA compilation or hardware validation without actually performing
  it.
- Changing the originating PLC project's SCL or DB interfaces as part of
  creating the skill.
