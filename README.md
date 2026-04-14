# Azure Network Protocol Analysis

Hands-on Azure project deploying a multi-VM environment 
and analyzing live network traffic using Wireshark across 
five core protocols ICMP, SSH, DHCP, DNS, and RDP
with real-time Azure NSG firewall configuration and testing.

## Environment

- Microsoft Azure-Resource Group: RG-Network-Activities
- Windows 10 VM (windows-vm)-Private IP: 10.0.0.4
- Ubuntu 24.04 VM (linix-vm)-Private IP: 10.0.0.5
- Both VMs on the same Virtual Network: Lab01-Vnet/default
- Region: Canada Central

## Project Sections

| Section | Tools Used | Key Activity |
|---|---|---|
| [VM Deployment](screenshots/part1-vm-deployment/) | Azure Portal | Deployed two VMs on shared VNet |
| [ICMP Analysis](screenshots/part2-icmp/) | Wireshark + ping | Observed request and reply packets |
| [NSG Firewall](screenshots/part3-nsg-firewall/) | Azure NSG + Wireshark | Blocked and restored ICMP in real time |
| [SSH Analysis](screenshots/part3-ssh/) | Wireshark + PowerShell | SSH session to Ubuntu VM |
| [DHCP Analysis](screenshots/part3-dhcp/) | Wireshark + ipconfig | Captured full DORA sequence |
| [DNS Analysis](screenshots/part3-dns/) | Wireshark + nslookup | Resolved google.com and disney.com |
| [RDP Analysis](screenshots/part3-rdp/) | Wireshark | Continuous stream on tcp.port 3389 |
| [Decommission](screenshots/decommission/) | Azure Portal | Deleted all resources after completion |

## Technologies Used
Microsoft Azure - Windows 10 - Ubuntu 24.04 - Wireshark - 
PowerShell-NSG-VNet-ICMP-SSH-DHCP-DNS-RDP-TCP/IP
