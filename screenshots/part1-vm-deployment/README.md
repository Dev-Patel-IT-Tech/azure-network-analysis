# Part 1: Virtual Machine Deployment

## Overview
Deployed two virtual machines in Microsoft Azure within 
the same Virtual Network and Subnet to enable direct 
private IP communication for network analysis.

## Configuration
- Resource Group: RG-Network-Activities (Canada Central)
- Windows 10 VM: windows-vm — Standard D2s v3 — Private IP 10.0.0.4
- Ubuntu 24.04 VM: linix-vm — Standard D2s v3 — Private IP 10.0.0.5
- Virtual Network: Lab01-Vnet/default — shared across both VMs

## Key Finding
Both VMs confirmed on the same VNet (Lab01-Vnet/default) 
with private IPs in the 10.0.0.0/24 range. This allows 
direct communication between the machines without 
traversing public internet infrastructure.

## Screenshots
![Resource Group](01-resource-group-created.png)
![Both VMs Running](02-03-both-vms-running.png)
![Windows VM VNet Confirmed](04A-windows-vm-vnet.png)
![Linux VM VNet Confirmed](04B-linux-vm-vnet.png)
