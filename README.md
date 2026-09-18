# Microsoft 365 Endpoint Management & Security Lab

## Overview

This project is a hands-on Microsoft 365 endpoint management and security lab designed to simulate the management of Windows endpoints in a small business environment.

The lab combines traditional Windows Server infrastructure with modern cloud-based endpoint management using Microsoft Entra ID and Microsoft Intune.

The objective is to gain practical experience with identity, endpoint enrollment, device configuration, application deployment, compliance, security controls, monitoring, and troubleshooting.

---

## Project Objectives

The lab is designed to demonstrate practical experience with:

- Microsoft Entra ID
- Microsoft Intune
- Windows 11 endpoint management
- Windows device enrollment
- Application deployment
- Device configuration policies
- Compliance policies
- Endpoint security policies
- Microsoft Defender integration
- PowerShell administration
- Azure infrastructure
- Troubleshooting and documentation

---

## Lab Architecture

The environment currently consists of an Azure-based infrastructure containing a Windows Server environment and a Windows 11 endpoint.

### Current architecture

```text
           Microsoft Azure
              │
       Resource Group
          Novexus
              │
      ┌───────┴──────────────────────┐
      │                              │
      ▼                              ▼
catenicus VNet             vnet-southafricanorth-1
10.0.0.0/16                       │
      │                            ▼
      │                    snet-southafricanorth-1
      │                         172.16.0.0/24
      │                            │
      │                            ▼
      │                         vm-dc01
      │                    Windows Server 2025
      │                            │
      │                     ┌──────┼──────┐
      │                     │      │      │
      │                   AD DS   DNS   AD CS
      │
      ├── default
      │   10.0.0.0/24
      │
      └── snet-southafricanorth-2
          10.0.1.0/24
               │
               ▼
        Novexusendpoint
          Windows 11
        Enterprise 25H2
               │
        Target: Entra ID
             + Intune
-Network connectivity: catenicus and vnet-southafricanorth-1 are currently separate VNets with no VNet peering configured.

```
## Microsoft Defender Antivirus Baseline

### Objective

Apply and verify Microsoft Defender Antivirus settings on the managed Windows endpoint using Microsoft Intune.

### Policy

* **Policy name:** Novexus - Defender Antivirus Baseline
* **Target device:** Novexusendpoint
* **Deployment status:** Success
* **Reported errors:** 0
* **Reported conflicts:** 0

### Endpoint Verification

The following PowerShell commands were used to verify Defender Antivirus status:

```powershell
Get-MpComputerStatus |
Select-Object AMServiceEnabled,
              AntivirusEnabled,
              RealTimeProtectionEnabled,
              AMRunningMode
```

Results confirmed that the Defender service, antivirus, and real-time protection were enabled, with the antivirus running in Normal mode.

```powershell
Get-MpPreference |
Select-Object DisableRealtimeMonitoring,
              MAPSReporting,
              EnableNetworkProtection
```

Real-time monitoring was not disabled. MAPS reporting was set to `2`, while network protection was set to `0`.

### Result

Intune reported successful deployment of the Defender Antivirus baseline to Novexusendpoint. Endpoint checks confirmed that Defender Antivirus and real-time protection were enabled.

### Evidence

* Intune policy deployment status
* Intune device-level policy report


