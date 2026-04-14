# Azure Network Protocol Analysis

Two-VM environment deployed in Microsoft Azure to capture 
and analyze live network traffic across five protocols using 
Wireshark, with real-time NSG firewall rule testing.

Built to develop hands-on understanding of how ICMP, SSH, 
DHCP, DNS, and RDP behave at the packet level - protocols 
that come up daily in IT support troubleshooting.

## Environment

- Resource Group: RG-Network-Activities (Canada Central)
- Windows 10 VM (windows-vm) - Private IP: 10.0.0.4
- Ubuntu 24.04 VM (linix-vm) - Private IP: 10.0.0.5
- Virtual Network: Lab01-Vnet/default (shared across both VMs)
- Tools: Microsoft Azure, Wireshark, PowerShell, NSG

---

## VM Deployment

Two virtual machines deployed on the same Virtual Network 
and Subnet so they can communicate directly using private 
IP addresses without traversing the public internet.

Confirmed on the Networking tab of each VM that both show 
Lab01-Vnet/default before starting any analysis. This is 
a critical check - if the VMs were on different networks, 
the private IP pings would fail and troubleshooting would 
begin in the wrong place.

![Resource Group Created](images/01-resource-group-created.png)

![Both VMs Running in Same Resource Group](images/02-03-both-vms-running.png)

![Windows VM Virtual Network Confirmed](images/04A-windows-vm-vnet.png)

![Linux VM Virtual Network Confirmed](images/04B-linux-vm-vnet.png)

---

## ICMP Analysis

Connected to windows-vm via Remote Desktop, installed 
Wireshark, and captured ICMP traffic between both VMs 
and toward an external address.

- Applied icmp filter in Wireshark
- Pinged linix-vm at 10.0.0.5 from windows-vm at 10.0.0.4
- Observed Echo Request and Echo Reply packets in real time
- Pinged www.google.com to confirm external routing
- 0 percent packet loss on both tests
- VM-to-VM latency: 1-20ms
- External ping resolved to 142.251.157.119 at 8-9ms

In real IT support, ping is usually the first tool used 
to confirm whether a device is reachable. Watching the 
request go out and the reply come back in Wireshark 
shows exactly what is happening at the packet level 
when a user says they cannot reach another machine.

![ICMP Ping Between VMs - Request and Reply Visible](images/07-wireshark-icmp-ping-ubuntu.png)

![ICMP Ping to External Address - Google.com](images/08-wireshark-icmp-ping-google.png)

---

## NSG Firewall Configuration

Configured an Azure Network Security Group inbound rule 
to block ICMP traffic to the Ubuntu VM and watched the 
real-time impact on both Wireshark and the command line.

- Started continuous ping: ping -t 10.0.0.5
- Confirmed replies in Wireshark and PowerShell
- Opened linix-vm-nsg and added inbound rule:
  Priority 290 - Protocol ICMP - Action Deny
- Wireshark immediately showed no response found
- PowerShell showed Request timed out on every line
- Deleted the rule - replies resumed within seconds

The rule took effect without restarting the VM. Priority 
290 placed it above the default allow rules, which is 
why traffic stopped the instant it was applied.

This is the same logic used when a user loses access to 
a service and the firewall is suspected. Checking NSG 
inbound rules and testing with a live ping tells you 
immediately whether the firewall is the problem.

![Continuous Ping Running Before Rule](images/09-perpetual-ping-before-block.png)

![NSG Inbound Deny Rule Applied](images/10-nsg-deny-rule-created.png)

![ICMP Traffic Blocked - No Response Found in Wireshark](images/11-icmp-traffic-blocked.png)

![ICMP Traffic Restored After Rule Removal](images/12-icmp-traffic-resumed.png)

---

## SSH Analysis

SSH'd into linix-vm from windows-vm using PowerShell 
and ran Linux commands to confirm remote access.

- Command: ssh labuser@10.0.0.5
- Authenticated with username and password
- Ran: id, hostname, pwd, ls, whoami
- Confirmed: user labuser, hostname linix-vm, 
  directory /home/labuser

SSH encrypts all traffic between client and server. 
Every keystroke and response travels as an encrypted 
packet. This is the protocol IT teams use to remotely 
access and manage Linux servers. If an SSH connection 
fails during troubleshooting, checking whether port 22 
is open in the firewall rules is the first step - which 
connects directly to the NSG configuration above.

![SSH Session Active on Ubuntu VM](images/13-ssh-session-active.png)

![SSH Session with Wireshark Capture Running](images/14-wireshark-ssh-traffic.png)

---

## DHCP Analysis

Ran ipconfig /renew on windows-vm and captured the full 
DHCP exchange in Wireshark.

- Applied dhcp filter in Wireshark
- Confirmed no DHCP traffic before running command
- Opened PowerShell as Administrator
- Ran: ipconfig /renew
- Captured the full sequence as it played out

DORA sequence captured:
1. DHCP Release - client released existing IP lease
2. DHCP Discover - client broadcast to locate DHCP server
3. DHCP Offer - server at 168.63.129.16 offered an address
4. DHCP Request - client requested the offered address
5. DHCP ACK - server confirmed and assigned 10.0.0.4

Full sequence completed in under a second.

ipconfig /renew is one of the most common first responses 
when a user calls saying they have no network connection. 
Watching this in Wireshark shows what is actually happening 
during that command. If Discover goes out but no Offer 
comes back, the DHCP server is not responding. If Offer 
comes back but no ACK, the server accepted the request 
but something failed at confirmation. Knowing which step 
failed changes where you look next.

![Wireshark Before ipconfig renew - No Traffic](images/15-wireshark-dhcp-before-renew.png)

![Full DORA Sequence Captured in Wireshark](images/16-wireshark-dhcp-traffic.png)

---

## DNS Analysis

Used nslookup to query two external domains from 
windows-vm and captured DNS traffic in Wireshark.

- Applied dns filter in Wireshark
- Ran: nslookup google.com
- Ran: nslookup disney.com
- Watched query and response packets for each

google.com resolved to 142.250.139.138 and several 
other addresses including IPv6 range 2607:f8b0:4023:1804.
disney.com resolved to 130.211.198.204.
DNS server used: 168.63.129.16 (Azure internal resolver).

DNS is the most common cause of connectivity complaints 
that are not actually connectivity problems. When a 
user says they cannot access a website but can ping 
an IP address directly, DNS is almost always the issue. 
Running nslookup shows whether the DNS server is 
responding and returning a valid address. No response 
from nslookup means the DNS server is unreachable or 
misconfigured, not the network itself.

![DNS Query and Response for google.com](images/17-wireshark-dns-google.png)

![DNS Query and Response for disney.com](images/18-wireshark-dns-disney.png)

---

## RDP Analysis

Filtered Wireshark for tcp.port == 3389 during an 
active Remote Desktop session with zero user activity.

- Applied filter: tcp.port == 3389
- No mouse movement, no keyboard input
- Watched the stream for 10 seconds
- Result: over 45,000 packets captured, all TLSv1.3

RDP streams the entire desktop display continuously 
to the client. Every screen refresh and background 
process generates encrypted packets regardless of 
what the user is doing. It does not wait for input 
the way ICMP or DNS do.

This explains why RDP feels slow on a congested network 
even when the user is not actively doing anything. It 
also explains why IT teams reduce display quality and 
color depth when connecting to remote machines over 
low-bandwidth links. The stream never stops, so 
bandwidth management matters throughout the session.

![RDP Continuous Traffic on tcp.port 3389](images/19-wireshark-rdp-nonstop.png)

---

## Resource Decommission

All Azure resources deleted after the project was 
complete to prevent ongoing compute costs.

Deleted resource group RG-Network-Activities which 
removed all 11 dependent resources at once - both VMs, 
Virtual Network, NSG, public IPs, network interfaces, 
and OS disks - in a single operation.

![Resource Group Deletion Confirmation](images/21-resource-group-deleting.png)
