# PowerShell Endpoint Inventory Script

## Objective

Deploy a PowerShell script through Microsoft Intune to collect basic Windows endpoint information and verify successful execution on the managed device.

## Configuration

- **Script name:** Novexusendpoint Inventory
- **Script file:** `endpoint-inventory.ps1`
- **Platform:** Windows 10 and later
- **Run using logged-on credentials:** No
- **Enforce script signature check:** No
- **Run script in 64-bit PowerShell Host:** Yes
- **Assignment:** `novexus windows device`
- **Target device:** `Novexusendpoint`

## Script Function

The PowerShell script:

1. Creates the directory `C:\ProgramData\Novexus` if it does not already exist.
2. Collects basic information using Windows CIM queries.
3. Writes the collected information to:

`C:\ProgramData\Novexus\endpoint-inventory.txt`

## Verification

The Intune script report initially showed no device status. After an Intune device sync, the script was subsequently reported as:

- **Succeeded:** 1
- **Error:** 0

The endpoint was then checked directly, and the output file was found at:

`C:\ProgramData\Novexus\endpoint-inventory.txt`

The file contained:

```text
Computer Name: Novexusendpoint
Manufacturer: Microsoft Corporation
Model: Virtual Machine
OS: Microsoft Windows 11 Enterprise
OS Version: 10.0.26200
Last Boot: 09/19/2026 06:48:49
Collection Time: 2026-09-19 12:28:45
