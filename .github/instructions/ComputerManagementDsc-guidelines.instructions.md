---
description: ComputerManagementDsc-specific guidelines for AI development.
applyTo: "**"
---

# ComputerManagementDsc Requirements

## Build & Test Workflow Requirements

- Never use VS Code tasks; always use PowerShell scripts via terminal from the repository root.
- Setup build and test environment (once per `pwsh` session): `./build.ps1 -Tasks noop`
- Build project before running tests: `./build.ps1 -Tasks build`
- Run all unit tests: `Invoke-Pester -Path 'tests/Unit' -Output Detailed`
- Run a specific MOF resource test:
  `Invoke-Pester -Path 'tests/Unit/DSC_<ResourceName>.Tests.ps1' -Output Detailed`
- Run a specific class resource test:
  `Invoke-Pester -Path 'tests/Unit/Classes/<ClassName>.Tests.ps1' -Output Detailed`
- Never run integration tests locally.

## Naming

- MOF-based resources: `DSC_<ResourceName>` prefix on all files and exported functions
  (e.g. `DSC_TimeZone.psm1`, `Get-TargetResource` is exported via `*-TargetResource`)
- Class-based resources: PascalCase class name; file prefix `<N>.<ClassName>.ps1` where
  `N` is the dependency group number (e.g. `020.PSResourceRepository.ps1`)

## Resource Type Selection

- Implement new resources as class-based (`source/Classes/`) unless MOF-based is required
  (e.g. WMF 4.0 support).

## Tests

- MOF unit tests: `tests/Unit/DSC_<ResourceName>.Tests.ps1`
- Class unit tests: `tests/Unit/Classes/<ClassName>.Tests.ps1`
- Integration tests: `tests/Integration/` (flat)
- Run unit tests in a new `pwsh` session after changing class-based resources.
