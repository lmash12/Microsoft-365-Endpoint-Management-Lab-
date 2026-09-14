# Azure Infrastructure

## Overview

This section documents the Azure infrastructure used as the foundation for the Microsoft 365 Endpoint Management & Security Lab.

The environment is hosted in the **South Africa North** Azure region and uses a dedicated resource group named `Novexus`.

The infrastructure currently contains both:

- Traditional Windows Server infrastructure
- A Windows 11 endpoint intended for modern cloud-based management through Microsoft Entra ID and Microsoft Intune

The Azure infrastructure provides the underlying compute, networking, storage, and security components required for the later endpoint-management phases of the project.

---

## Resource Group

| Property | Configuration |
|---|---|
| Resource group | `Novexus` |
| Azure region | South Africa North |
| Subscription | Azure subscription 1 |
| Environment | Lab |

The `Novexus` resource group is used to organize the resources associated with this lab.

---

# Virtual Network Architecture

The lab currently uses two separate Azure Virtual Networks.

There is currently **no VNet peering configured** between these networks.

## 1. `catenicus`

The `catenicus` virtual network contains the Windows 11 endpoint subnet used by `Novexusendpoint`.

| Property | Configuration |
|---|---|
| VNet | `catenicus` |
| Address space | `10.0.0.0/16` |
| Region | South Africa North |

### Subnets

| Subnet | Address range | Purpose |
|---|---|---|
| `default` | `10.0.0.0/24` | Existing subnet |
| `snet-southafricanorth-2` | `10.0.1.0/24` | Windows 11 endpoint subnet |

The Windows 11 endpoint is connected to:

```text
catenicus
└── snet-southafricanorth-2
    └── 10.0.1.0/24
        └── Novexusendpoint
```

---

## 2. `vnet-southafricanorth-1`

This virtual network contains the existing Windows Server infrastructure.

| Property | Configuration |
|---|---|
| VNet | `vnet-southafricanorth-1` |
| Address space | `172.16.0.0/16` |
| Region | South Africa North |
| Subnet | `snet-southafricanorth-1` |
| Subnet address | `172.16.0.0/24` |

The existing server is connected to:

```text
vnet-southafricanorth-1
└── snet-southafricanorth-1
    └── 172.16.0.0/24
        └── vm-dc01
```

---

## Network Relationship

The two virtual networks are currently **not peered**.

```text
catenicus
10.0.0.0/16
    │
    ├── default
    │   10.0.0.0/24
    │
    └── snet-southafricanorth-2
        10.0.1.0/24
            │
            └── Novexusendpoint


vnet-southafricanorth-1
172.16.0.0/16
    │
    └── snet-southafricanorth-1
        172.16.0.0/24
            │
            └── vm-dc01
```

There is currently no direct VNet peering connection between `catenicus` and `vnet-southafricanorth-1`.

Any future connectivity between these networks will be treated as a deliberate architectural change and documented accordingly.

---

# Virtual Machines

## `vm-dc01`

`vm-dc01` is the existing Windows Server infrastructure server used by the lab.

| Property | Configuration |
|---|---|
| VM name | `vm-dc01` |
| Operating system | Windows Server 2025 |
| Azure region | South Africa North |
| Virtual network | `vnet-southafricanorth-1` |
| Subnet | `snet-southafricanorth-1` |
| Private IP | `172.16.0.4` |
| Public IP | Configured |
| Network security group | `vm-dc01-nsg` |

### Server roles and services

The server provides several traditional Windows infrastructure services for the lab:

- Active Directory Domain Services (AD DS)
- DNS
- Active Directory Certificate Services (AD CS)
- IIS
- File services
- Group Policy management

The Active Directory domain used by the existing infrastructure is:

```text
novexus.local
```

This server represents the traditional Windows Server side of the lab environment.

---

## `Novexusendpoint`

`Novexusendpoint` is the Windows 11 endpoint created specifically for the modern endpoint-management portion of the lab.

| Property | Configuration |
|---|---|
| VM name | `Novexusendpoint` |
| Operating system | Windows 11 Enterprise |
| Version | 25H2 |
| Architecture | x64 |
| VM size | Standard B2as_v2 |
| vCPU | 2 |
| Memory | 8 GiB |
| OS disk | Standard SSD |
| Virtual network | `catenicus` |
| Subnet | `snet-southafricanorth-2` |
| Private IP | `10.0.1.4` |
| Public IP | None |
| Public inbound ports | None |
| Auto-shutdown | 22:00 |
| Security type | Standard |

The endpoint is intentionally configured without a public IP address or public inbound ports.

The intended management model is:

```text
Microsoft Entra ID
        +
Microsoft Intune
        │
        ▼
Novexusendpoint
Windows 11 Enterprise
```

The endpoint is intended to remain **cloud-managed rather than being immediately joined to the existing `novexus.local` Active Directory domain**.

---

# Network Security

## Windows 11 Endpoint

The Windows 11 endpoint uses a private network configuration.

Current configuration:

- Public IP: **None**
- Public inbound ports: **None**
- Dedicated endpoint subnet
- No load balancer
- Accelerated networking disabled
- NIC deletion enabled when the VM is deleted

This reduces the endpoint's direct exposure to the public internet.

Remote management and connectivity will be addressed later in the lab without unnecessarily exposing the endpoint through a public IP.

---

# Network Segmentation

The endpoint and traditional server infrastructure are currently separated into different VNets.

```text
Azure
│
└── Novexus
    │
    ├── catenicus
    │   │
    │   ├── default
    │   │   └── 10.0.0.0/24
    │   │
    │   └── snet-southafricanorth-2
    │       └── 10.0.1.0/24
    │           └── Novexusendpoint
    │
    └── vnet-southafricanorth-1
        │
        └── snet-southafricanorth-1
            └── 172.16.0.0/24
                └── vm-dc01
```

There is currently no VNet peering between the two networks.

Any future connectivity between the networks will be treated as a deliberate architectural change.

---

# Cost Management

Because this lab is being operated as a development and testing environment, unnecessary Azure resources are avoided where possible.

Current cost-management measures include:

- Using the `Standard B2as_v2` VM size for the Windows 11 endpoint
- Using Standard SSD storage
- No public IP on the Windows 11 endpoint
- No load balancer
- No Azure Backup
- No Site Recovery
- No unnecessary monitoring services
- Auto-shutdown configured for the endpoint at 22:00
- Deallocating resources when they are no longer required

# Deployment Troubleshooting

The initial deployment of `Novexusendpoint` failed during network interface creation.

## Error

Azure returned:

```text
InvalidResourceReference
```

The network interface referenced:

```text
catenicus/subnets/snet-southafricanorth-2
```

However, the subnet did not exist at the time of the original deployment.

## Investigation

The Azure resource group deployment history was inspected using Azure PowerShell:

```powershell
Get-AzResourceGroupDeployment -ResourceGroupName "Novexus" |
Sort-Object Timestamp -Descending |
Select-Object -First 10 DeploymentName, ProvisioningState, Timestamp
```

The failed deployment was identified as:

```text
CreateVirtualMachine-Novexusendpoint
```

The failed deployment operation was then inspected using:

```powershell
Get-AzResourceGroupDeploymentOperation `
  -ResourceGroupName "Novexus" `
  -DeploymentName "CreateVirtualMachine-Novexusendpoint" |
Where-Object ProvisioningState -eq "Failed" |
Format-List ProvisioningState, StatusMessage, TargetResource
```

The operation returned:

```text
Code: InvalidResourceReference
```

and confirmed that the referenced subnet could not be found.

## Resolution

The `catenicus` VNet was inspected through:

**Azure Portal → Virtual Networks → catenicus → Subnets**

Only the following subnet existed at that time:

```text
default
10.0.0.0/24
```

The required endpoint subnet was then created:

```text
Subnet: snet-southafricanorth-2
Address range: 10.0.1.0/24
```

The Windows 11 VM was subsequently deployed again using the existing subnet.

## Result

The second deployment completed successfully.

The VM initially reported that the Azure VM Agent was not ready. After allowing the VM to complete initialization and refreshing the Azure portal, the VM Agent status became healthy.

---

# Current Infrastructure Status

| Component | Status |
|---|---|
| Azure resource group | ✅ Operational |
| `vm-dc01` | ✅ Running |
| `catenicus` VNet | ✅ Operational |
| `snet-southafricanorth-2` | ✅ Created |
| `Novexusendpoint` | ✅ Running |
| Windows 11 VM Agent | ✅ Ready |
| VNet peering | Not configured |
| Microsoft Entra ID enrollment | ⏳ Planned |
| Microsoft Intune enrollment | ⏳ Planned |

