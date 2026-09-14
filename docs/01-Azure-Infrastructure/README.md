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

The `catenicus` virtual network is used for the Windows 11 endpoint environment.

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
## 2. vnet-southafricanorth-1

This virtual network contains the existing Windows Server infrastructure.

Property	Configuration
VNet	vnet-southafricanorth-1
Region	South Africa North
Subnet	snet-southafricanorth-1
Subnet address	172.16.0.0/24

Note: The VNet address space will be documented after it has been verified from the Azure configuration.

The existing server is connected to:

vnet-southafricanorth-1
└── snet-southafricanorth-1
    └── 172.16.0.0/24
        └── vm-dc01
Network Relationship

The two virtual networks are currently not peered.

catenicus
10.0.0.0/16
    │
    └── snet-southafricanorth-2
        10.0.1.0/24
            │
            └── Novexusendpoint


vnet-southafricanorth-1
    │
    └── snet-southafricanorth-1
        172.16.0.0/24
            │
            └── vm-dc01

There is currently no direct VNet peering connection between catenicus and vnet-southafricanorth-1.

Any future connectivity between these networks will be treated as a deliberate architectural change and documented accordingly.
