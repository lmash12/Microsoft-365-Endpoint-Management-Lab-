# Windows Device Restrictions

## Overview

This document records a removable-storage restriction configured in Microsoft Intune for the `Novexusendpoint` Windows 11 lab endpoint.

## Policy Configuration

| Item | Value |
|---|---|
| Policy name | `Novexus - Windows Device Restrictions` |
| Policy type | Settings catalog |
| Category | Administrative Templates → Removable Storage Access |
| Setting | Removable Disks: Deny write access (User) |
| Configured value | Enabled |
| Assignment group | `novexus windows device` |
| Scope tag | Default |

Only this setting was configured. Other settings were left Not configured.

## Deployment Result

The Intune report for `Novexusendpoint` showed:

| Status | Count |
|---|---:|
| Pending | 0 |
| Not applicable | 1 |
| Success | 0 |
| Error | 0 |
| Conflict | 0 |

## Investigation

The selected setting is user-scoped, while the policy is assigned to a device group. The endpoint has been using a local Windows account.

These details may be relevant to the Not applicable result, but the report does not establish the exact cause. The endpoint being an Azure virtual machine is not, by itself, proof that the setting is unsupported.

## Verification and Limitations

The policy is configured as Enabled, but Intune reported Not applicable for the endpoint.

Removable-storage write protection has not been verified as enforced. No functional write-blocking test has been performed.

## Deployment Result

The Intune report for `Novexusendpoint` initially showed
Not applicable. On 2026-09-19, the status changed to Pending.

The policy has not yet been confirmed as successfully applied.

| Latest status | Count |
|---|---:|
| Pending | 1 |
| Not applicable | 0 |
| Success | 0 |
| Error | 0 |
| Conflict | 0 |

## Next Steps

Check back after restarting the virtual machine
Revisit this setting if the lab later includes a suitable Entra user sign-in and user-group assignment. Confirm applicability and test the actual behavior before documenting the restriction as enforced.
