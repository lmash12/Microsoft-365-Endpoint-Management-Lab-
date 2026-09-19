# Windows Update Ring

## Overview

This document records the Windows Update ring configured in Microsoft Intune for the `Novexusendpoint` Windows 11 lab endpoint.

## Configuration

**Policy name:** `Novexus - Windows Update Ring`

### Update settings

| Setting                                                 | Configured value |
| ------------------------------------------------------- | ---------------- |
| Microsoft product updates                               | Allow            |
| Windows drivers                                         | Allow            |
| Quality update deferral                                 | 0 days           |
| Feature update deferral                                 | 7 days           |
| Upgrade Windows 10 devices to Latest Windows 11 release | No               |
| Feature update uninstall period                         | 10 days          |
| Enable pre-release builds                               | Not Configured   |

### User experience settings

| Setting                     | Configured value                             |
| --------------------------- | -------------------------------------------- |
| Automatic update behavior   | Auto install and restart at maintenance time |
| Active hours                | 8 AM – 6 PM                                  |
| Pause Windows updates       | Disabled                                     |
| Check for Windows updates   | Enabled                                      |
| Update notifications        | Default Windows Update notifications         |
| Use deadline settings       | Allow                                        |
| Feature update deadline     | 14 days                                      |
| Quality update deadline     | 7 days                                       |
| Grace period                | 2 days                                       |
| Auto reboot before deadline | No                                           |

## Assignment and Deployment

The policy was created in Microsoft Intune and assigned to the lab endpoint's device group.

**Reported status:** Success

The Intune report showed Success for `Novexusendpoint`.

## Verification and Limitations

The Intune deployment status confirms that the policy was reported as successfully applied. This does not independently confirm that a particular Windows update has downloaded, installed, or restarted the device.

No Windows Update installation or restart test is recorded in this document.

## Summary

The Windows Update ring was created and reported as successfully deployed to the lab endpoint.
