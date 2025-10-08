# Office Network Design

## Hardware and Service Table

| Device/Service | Count | Deployment Type | Purpose/Notes |
| :-- | :-- | :-- | :-- |
| Layer 3 Managed Switch | 1 (2 for HA) | Hardware | VLAN routing; size for all ports needed |
| Advanced Firewall | 1 (2 for HA) | Hardware/VM | Network security, VLAN policy, optional VPN/DHCP host |
| WiFi Access Points | 2–3 | Hardware | Full coverage, per-SSID VLAN isolation |
| DHCP/IPAM Server | 1 (2 for HA) | VM/Firewall/Server | Central IP assignment \& reservations |
| WireGuard VPN Gateway | 1 | VM/Firewall/Hardware | Secure remote access |
| UPS Battery Backup | 1–2 | Hardware | Main rack/edge device coverage |
| Patch Panel/Cabling | 1+ per rack | Hardware | Clean, scalable cable management |


***

## Subnet and VLAN Assignment

| VLAN | Group/Usage | Example Network | Assignable IPs | Purpose |
| :-- | :-- | :-- | :-- | :-- |
| 10 | Core Services | 10.10.0.0/25 | 126 | Servers, NAS, printers, lab infra |
| 20 | Employee Devices | 10.10.1.0/24 | 254 | PCs, laptops, phones, TVs, daily devices |
| 30 | Lab Devices/IoT/SBC | 10.10.2.0/25 | 126 | Raspberry Pi, lab instruments, smart kit |
| 40 | VM/Containers | 10.10.3.0/24 | 254 | Docker, Proxmox, experimental images |
| 50 | Guests/Non-employee IoT | 10.10.4.0/25 | 126 | Visitors, unmanaged IoT, guest WiFi SSID |
| 99 | Management/Network infra | 10.10.10.0/27 | 30 | Switches, APs, out-of-band management |
| * | VPN Remote Users | 10.10.254.0/27 | 30 | WireGuard/IPsec endpoint range |

- Adjust subnet sizes as needed for future growth.
- All subnets are routed and isolated by the Layer 3 switch and firewall policy.

***

## ASCII Network Topology Diagram

<pre>
+-------------------+
|     Internet      |
+-------------------+
          |
+------------------------+
|  Firewall/Router       |
+-----------+------------+
            |
+-----------------------+
|  Layer 3 Switch      |
+-----------+----------+
     |     |     |     |
     |     |     |     +--- Patch Panel -- Physical Ports
     |     |     +--------- WiFi 7 APs (2–3 units)
     |     +--------------- Core Services (VLAN 10: 10.10.0.0/25)
     +--------------------- Employee Devices (VLAN 20: 10.10.1.0/24)
           +--------------- Lab Devices (VLAN 30: 10.10.2.0/25)
           +--------------- VM/Containers (VLAN 40: 10.10.3.0/24)
           +--------------- Guest IoT (VLAN 50: 10.10.4.0/25)
           +--------------- Management (VLAN 99: 10.10.10.0/27)
           +--------------- WireGuard VPN (10.10.254.0/27)
           +--------------- DHCP/IPAM Server
           +--------------- UPS
</pre>

***

## Key Principles

- Clear VLANs for each functional group, routed and firewalled for security and ease of troubleshooting.
- All IP ranges use RFC1918 private space and allow for both dynamic (DHCP) and static assignment.
- DHCP/IPAM can centrally manage all address assignments and static reservations.
- The network can grow by allocating larger subnets or additional VLANs as needed.

