# TIA Portal V17 PLC Programming Skill

This repository distributes the [`tia-v17-plc-programming`](tia-v17-plc-programming/SKILL.md) Agent Skill for Siemens S7-1200/S7-1500 projects targeting TIA Portal V17. It provides SCL source guidance and network-by-network LAD/FBD designs.

## Install in VS Code Copilot

VS Code discovers personal Copilot skills from `%USERPROFILE%\.copilot\skills\` on Windows. In PowerShell, run:

```powershell
$source = Read-Host 'Enter the full path to the tia-v17-plc-programming folder in your clone'
$target = Join-Path $HOME '.copilot\skills\tia-v17-plc-programming'
New-Item -ItemType Directory -Force -Path $target | Out-Null
Copy-Item -Path (Join-Path $source '*') -Destination $target -Recurse -Force
```

The source path must point to the `tia-v17-plc-programming` folder that contains `SKILL.md` and `references`, not just the repository root.

## Update

Pull the latest changes in your clone, then run the same PowerShell commands again. They copy the current skill files into your personal skill directory and overwrite older versions. Start a new VS Code agent session or refresh the current one if the skill is not discovered.

## Documentation sources

The skill links to Siemens documentation and expects users to consult the manuals in the active PLC project's `Manual/` directory. Siemens PDFs are not bundled or redistributed in this repository.
