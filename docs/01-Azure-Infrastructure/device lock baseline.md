# Windows Device Lock Baseline

## Overview

This document records the configuration and verification of the Windows device inactivity-lock baseline deployed to the `Novexusendpoint` Windows 11 endpoint through Microsoft Intune.

The policy sets an inactivity limit of 900 seconds (15 minutes).

## Environment

| Item               | Value                                       |
| ------------------ | ------------------------------------------- |
| Endpoint           | `Novexusendpoint`                           |
| Operating system   | Windows 11 Enterprise                       |
| Management         | Microsoft Intune                            |
| Policy name        | `Novexus - Windows Device Lock Baseline`    |
| Policy type        | Settings catalog                            |
| Configured setting | Interactive logon: Machine inactivity limit |
| Inactivity limit   | 900 seconds (15 minutes)                    |
| Scope tag          | Default                                     |

## Configuration

The Intune Settings Catalog policy was configured with:

* **Setting:** Interactive logon: Machine inactivity limit
* **Value:** 900 seconds
* **Assignment:** novexus windows device

The policy was created and saved in Microsoft Intune.

## Deployment Verification

After the policy was created, the endpoint was synchronized from Windows:

**Settings → Accounts → Access work or school → Connected account → Info → Sync**

The Intune policy report subsequently showed **Success**.

## Local Configuration Verification

The following PowerShell command was run on `Novexusendpoint` to check the local inactivity-timeout registry value:

```powershell
Get-ItemProperty `
  'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System' `
  -Name InactivityTimeoutSecs `
  -ErrorAction SilentlyContinue
```

### Result

```text
InactivityTimeoutSecs : 900
```

The local registry value matches the configured 900-second limit.

## Expected Behavior

After 15 minutes of inactivity, Windows is expected to lock the interactive session.

The user must unlock the session using an existing sign-in method for the account. This policy does not create or change the account password.

## Idle-Lock Functional Test

**Status:** Passed

Test procedure:

1. Leave the endpoint signed in and idle.
2. Start a 15-minute timer.
3. Avoid interacting with the endpoint during the test.
4. Check whether Windows locks after the inactivity period.
5. Attempt to unlock using the existing account sign-in method.

Record the observed result below.

| Test item                        | Result  |
| -------------------------------- | ------- |
| Test date/time                   | 09:37 - 09:52 |
| Endpoint locked after inactivity | passed |
| Unlock successful                | passed |


## Evidence

The following evidence has been collected:

* Intune policy deployment status: Success
* PowerShell output showing `InactivityTimeoutSecs : 900`

No screenshot is required if the Intune report and PowerShell output are retained as evidence.

## Summary

The device-lock policy was successfully deployed according to the Intune report, and the local registry value was verified as 900 seconds.

The actual 15-minute idle-lock behavior remains pending functional testing.
