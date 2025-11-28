# Secure-Multisite-BGP-MPLS-Backbone-with-VPN-Services
Build a multi-site secure enterprise backbone using BGP and MPLS VPN technologies across datacenters and branches With my team on the DEPI graduation project in the Huawei Networks Adminstration track .  
Network Documentation

1️⃣ Customer Edge Routers (CE)
1.1 BR-CE (Branch Customer Edge Router) 📍

Role: Branch-side router facing the ISP (PE equivalent)

Timezone: Cairo (UTC+2)

Loopback: 10.2.255.254/32 (Router ID for IS-IS/BGP)

Interfaces & Addressing:

Gi0/0/0: 172.16.2.2/30 → Link to ISP-R

Gi0/0/1: Physical interface only, sub-interfaces used

Gi0/0/1.10: 10.2.1.1/24 → VLAN 10 L3 interface

Gi0/0/1.20: 10.2.2.1/24 → VLAN 20 L3 interface

Routing & Protocols:

IS-IS (Process 1) enabled on Gi0/0/0 and Gi0/0/1, NET: 49.0002.0000.0000.0002.00

BGP (AS 200) neighbor: 172.16.2.1 (ISP-R in AS 64512), advertised networks: 10.2.1.0/24, 10.2.2.0/24, 10.2.255.254/32

1.2 HQ-CE (Headquarters Customer Edge Router) 🏢

Role: HQ-side router facing ISP (PE equivalent)

Timezone: Cairo (UTC+2)

Loopback: 10.1.255.254/32 (Router ID for IS-IS/BGP)

Interfaces & Addressing:

Gi0/0/0: 172.16.1.2/30 → Link to ISP-R

Gi0/0/1: Physical interface only, sub-interfaces used

Gi0/0/1.10: 10.1.1.1/24 → VLAN 10 L3 interface

Gi0/0/1.20: 10.1.2.1/24 → VLAN 20 L3 interface

Routing & Protocols:

IS-IS (Process 1) enabled on Gi0/0/0 and Gi0/0/1, NET: 49.0001.0000.0000.0001.00

BGP (AS 100) neighbor: 172.16.1.1 (ISP-R in AS 64512), advertised networks: 10.1.1.0/24, 10.1.2.0/24, 10.1.255.254/32

2️⃣ Site-to-Site VPN Routers 🛡️
2.1 HQ-R1 (Headquarters Internal Router)

Interfaces & Addressing:

Gi0/0/0: 172.16.1.1/30 → Link to HQ-CE

Gi0/0/1: 10.1.1.1/24 → LAN/DHCP segment

Gi0/0/2: 198.51.100.1/24 → VPN outside interface to BR-R2

Routing:

Static route: 198.51.100.2/32 → 198.51.100.254

Default route: 0.0.0.0/0 → 172.16.1.2 (towards HQ-CE/ISP)

IS-IS Level-1: NET 49.0001.0000.0000.0001.00

BGP (AS 100): neighbor 172.16.1.2 (HQ-CE) in AS 65000, advertised network: 10.1.1.0/24

IPSec VPN:

ACL 3000: permits 10.1.1.0/24 → 10.2.2.0/24

IKE Policy 10: PSK, AES-128, SHA-1, DH Group 2

IPSec Policy: uses ACL 3000, peer 198.51.100.2

DHCP: Pool HQ-POOL → 10.1.1.0/24, gateway 10.1.1.1, DNS 8.8.8.8, excluded 10.1.1.1-10.1.1.20

2.2 BR-R2 (Branch Internal Router)

Interfaces & Addressing:

Gi0/0/0: 172.16.2.1/30 → Link to BR-CE

Gi0/0/1: 10.2.2.1/24 → LAN/DHCP segment

Gi0/0/2: 198.51.100.2/24 → VPN outside interface to HQ-R1

Routing:

Static route: 198.51.100.1/32 → 198.51.100.254

Default route: 0.0.0.0/0 → 172.16.2.2 (towards BR-CE/ISP)

IS-IS Level-1: NET 49.0002.0000.0000.0002.00

BGP (AS 200): neighbor 172.16.2.2 (BR-CE) in AS 65000, advertised network: 10.2.2.0/24

IPSec VPN:

ACL 3000: permits 10.2.2.0/24 → 10.1.1.0/24

IKE Policy 10: PSK, AES-128, SHA-1, DH Group 2

IPSec Policy: uses ACL 3000, peer 198.51.100.1

DHCP: Pool BR-POOL → 10.2.2.0/24, gateway 10.2.2.1, DNS 8.8.8.8, excluded 10.2.2.1-10.2.2.20

3️⃣ Access & Aggregation Layer Switches 🔄
3.1 Aggregation Switches (AGG-HQ1, AGG-HQ2)

Role: Layer 2/Aggregation switching layer at HQ

VLANs: 10, 20, 30, 40

IS-IS Level-1: enabled on Gi0/0/0, Gi0/0/1, Gi0/0/2

AGG-HQ1 NET: 49.0001.0000.0000.0003.00

AGG-HQ2 NET: 49.0001.0000.0000.0004.00

Interface Configuration: trunk ports allowing VLANs 10–40

3.2 Access Switch (ACCESS-DEPT1)

Role: Access layer switch for end-user devices

VLANs: 10

Interface Configuration: Gi0/0/1 → access port VLAN 10

4️⃣ Key Takeaways & Operational Notes

Dual Routing Domains:

IS-IS for internal routing

BGP for CE ↔ ISP connectivity

VPN Topology:

IPSec VPN between HQ-R1 ↔ BR-R2 over 198.51.100.0/24

Protects 10.1.1.0/24 ↔ 10.2.2.0/24

VLAN Tagging:

802.1Q used on CE routers and aggregation switches

Inconsistencies:

BGP AS numbers on HQ-R1/BR-R2 (65000) differ from HQ-CE/BR-CE (64512)

Suggests missing PE or configuration mismatch
