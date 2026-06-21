---
description: Guidelines for implementing localization.
applyTo: "source/**/*.ps1"
version: 1.0.0
---

# Localization Guidelines

## Requirements

- Localize all `Write-Debug`, `Write-Verbose`, `Write-Error`, `Write-Warning`, and
  `$PSCmdlet.ThrowTerminatingError()` messages; never use hardcoded strings
- Assume `$script:localizedData` is available

## String Files

- MOF-based resources:
  `source/DSCResources/DSC_<ResourceName>/en-US/DSC_<ResourceName>.strings.psd1`
- Class-based resources: `source/en-US/<ClassName>.strings.psd1`
- Module-level strings: `source/en-US/<ModuleName>.strings.psd1`

## Key Naming Patterns

- Format: `Verb_FunctionName_Action` (underscore separators),
  e.g. `Get_TimeZone_GettingCurrentState`

## String Format

```powershell
ConvertFrom-StringData @'
    KeyName = Message with {0} placeholder. (PREFIX0001)
'@
```

## String IDs

- Format: `(PREFIX####)`
- PREFIX: First letter of each word in class or function name
  (e.g. `Get-TargetResource` for `DSC_TimeZone` → `GTR`, `PSResourceRepository` → `PRR`)
- Number: Sequential from 0001

## Usage

```powershell
Write-Verbose -Message ($script:localizedData.KeyName -f $value1)
```
