# Azure Infrastructure Troubleshooting

## Windows 11 VM Deployment Failure

### Problem

The initial deployment of `Novexusendpoint` failed during VM creation.

Azure reported:

```text
InvalidResourceReference

catenicus/subnets/snet-southafricanorth-2 referenced by resource
.../networkInterfaces/novexusendpoint501 was not found.

The VM deployment therefore could not create the network interface because
the referenced subnet did not exist.

Investigation

The failed deployment was reviewed using Azure PowerShell:

Get-AzResourceGroupDeployment -ResourceGroupName "Novexus" |
Sort-Object Timestamp -Descending |
Select-Object -First 10 DeploymentName, ProvisioningState, Timestamp

The deployment appeared as:

CreateVirtualMachine-Novexusendpoint    Failed

The failed deployment operations were then inspected:

Get-AzResourceGroupDeploymentOperation `
  -ResourceGroupName "Novexus" `
  -DeploymentName "CreateVirtualMachine-Novexusendpoint" |
Where-Object ProvisioningState -eq "Failed" |
Format-List ProvisioningState, StatusMessage, TargetResource

This confirmed that the deployment was referencing a missing subnet.

Root Cause

The VM configuration referenced:

catenicus/snet-southafricanorth-2

However, the catenicus VNet initially contained only the default
subnet.

The required endpoint subnet had not been created before the VM
deployment.

Resolution

The missing subnet was created:

Subnet:  snet-southafricanorth-2
CIDR:    10.0.1.0/24
VNet:    catenicus

The Windows 11 VM was then deployed again using the existing subnet.

Result

The second deployment completed successfully.

Current endpoint networking:

VNet:       catenicus
Subnet:     snet-southafricanorth-2
Private IP: 10.0.1.4
Public IP:  None

The endpoint is now running successfully and remains isolated from
direct public inbound access.

Lesson Learned

Azure VM deployments depend on the referenced networking resources
already existing. Network architecture should therefore be validated
before deploying dependent resources.

The failure also demonstrated the value of checking
deployment operations rather than relying only on the high-level
deployment status.
