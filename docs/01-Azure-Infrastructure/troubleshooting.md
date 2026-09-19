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

## BitLocker Compliance Reporting Issue

### Issue

The `Novexusendpoint` Windows 11 device was reported as noncompliant by the Intune `baseline compliance` policy, with BitLocker identified as the failing setting.

### Investigation

Local BitLocker checks showed that the OS volume was fully encrypted and protection was enabled:

* Encryption percentage: 100%
* Volume status: Fully Encrypted
* Protection status: On

Windows MDM event logs also contained BitLocker CSP warnings:

* Event ID 2900: OS volume reported as noncompliant.
* Event ID 2914: OS drive reported as not protected.

These warnings were recorded earlier in the troubleshooting process.

### Resolution and Verification

The VM was shut down and started again.

After the restart, the Intune per-setting report showed Secure Boot, Code Integrity, and BitLocker as compliant.

The device-level report for `Novexusendpoint` subsequently showed:

* Policy compliance status: Compliant
* Compliant devices: 1
* Noncompliant devices: 0
* Total devices: 1

### Outcome

The device-level compliance report confirmed that `Novexusendpoint` was compliant with the `baseline compliance` policy.

The exact reason the reported status changed after the VM restart was not independently established.

### Evidence

* Intune per-setting compliance report
* Intune device-level compliance report
* Windows BitLocker status checks


