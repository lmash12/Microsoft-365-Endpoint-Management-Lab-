# Azure Lab Architecture

## Overview

The lab combines traditional Windows Server infrastructure with a modern
cloud-managed Windows 11 endpoint.

The infrastructure is hosted in Azure South Africa North and uses two
separate Virtual Networks.

## Architecture

```text
                         Azure
                           │
                    Resource Group
                       Novexus
                           │
              ┌────────────┴────────────┐
              │                         │
        catenicus VNet          vnet-southafricanorth-1
         10.0.0.0/16                172.16.0.0/16
              │                         │
              │                         │
       10.0.1.0/24                172.16.0.0/24
              │                         │
              ▼                         ▼
      Novexusendpoint                vm-dc01
       Windows 11                  Windows Server
       Enterprise                  2025
              │                         │
        Entra ID /                  AD DS
          Intune                      DNS
                                     AD CS
