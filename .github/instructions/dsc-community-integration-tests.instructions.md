---
description: Guidelines for implementing integration tests for DSC resources.
applyTo: "tests/[iI]ntegration/**/*.[iI]ntegration.[tT]ests.ps1"
version: 1.0.0
---

# Integration Tests Guidelines

## Requirements

- Location: `tests/Integration/<ResourceName>.Integration.Tests.ps1`
- No mocking — real environment only
- Cover all scenarios and code paths
- Use `Get-ComputerName` for computer names in CI
- Avoid `ExpectedMessage` for `Should -Throw` assertions
- Run integration tests in CI only unless explicitly instructed otherwise
- Call commands with `-Force` where applicable (avoids prompting)
- Use `-ErrorAction 'Stop'` so failures surface immediately

## Required Setup Block

```powershell
# Suppressing this rule because Script Analyzer does not understand Pester's syntax.
[System.Diagnostics.CodeAnalysis.SuppressMessageAttribute('PSUseDeclaredVarsMoreThanAssignments', '')]
param ()

BeforeDiscovery {
    try
    {
        if (-not (Get-Module -Name 'DscResource.Test'))
        {
            # Assumes dependencies have been resolved, so if this module is not available, run 'noop' task.
            if (-not (Get-Module -Name 'DscResource.Test' -ListAvailable))
            {
                # Redirect all streams to $null, except the error stream (stream 2)
                & "$PSScriptRoot/../../build.ps1" -Tasks 'noop' 2>&1 4>&1 5>&1 6>&1 > $null
            }

            # If the dependencies have not been resolved, this will throw an error.
            Import-Module -Name 'DscResource.Test' -Force -ErrorAction 'Stop'
        }
    }
    catch [System.IO.FileNotFoundException]
    {
        throw 'DscResource.Test module dependency not found. Please run ".\build.ps1 -ResolveDependency -Tasks build" first.'
    }

    $script:dscModuleName = '<ModuleName>'
    $script:dscResourceName = 'DSC_<ResourceName>'

    $script:skipIntegrationTests = $false
}

BeforeAll {
    $script:dscModuleName = '<ModuleName>'
    $script:dscResourceName = 'DSC_<ResourceName>'

    $script:testEnvironment = Initialize-TestEnvironment `
        -DSCModuleName $script:dscModuleName `
        -DSCResourceName $script:dscResourceName `
        -ResourceType 'Mof' `
        -TestType 'Integration'

    Import-Module -Name (Join-Path -Path $PSScriptRoot -ChildPath '..\TestHelpers\CommonTestHelper.psm1')
}

AfterAll {
    Restore-TestEnvironment -TestEnvironment $script:testEnvironment
}
```
