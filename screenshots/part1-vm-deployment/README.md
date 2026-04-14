# Part 1 - VM Deployment

Two virtual machines deployed in Microsoft Azure on the 
same Virtual Network so they can communicate directly 
over private IP addresses.

## Configuration

- Resource Group: RG-Network-Activities - Canada Central
- Windows 10 VM: windows-vm - Standard D2s v3 - IP 10.0.0.4
- Ubuntu 24.04 VM: linix-vm - Standard D2s v3 - IP 10.0.0.5
- Virtual Network: Lab01-Vnet/default - same on both VMs

## Why the VNet Matters

Both VMs need to be on the same Virtual Network and Subnet 
to communicate using private IP addresses. This was 
confirmed on the Networking tab of each VM before 
starting the protocol analysis.

## Screenshots

![Resource Group Created](01-resource-group-created.png)
![Both VMs Running](02-03-both-vms-running.png)
![Windows VM VNet](04A-windows-vm-vnet.png)
![Linux VM VNet](04B-linux-vm-vnet.png)
