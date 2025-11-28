# Secure-Multisite-BGP-MPLS-Backbone-with-VPN-Services
Build a multi-site secure enterprise backbone using BGP and MPLS VPN technologies across datacenters and branches With my team on the DEPI graduation project in the Huawei Networks Adminstration track . 
Network Documentation

1️⃣ Access Layer
Device: ACCESS-DEPT1

VLANs:

VLAN 10

Interfaces:

GigabitEthernet0/0/1

Type: Access

VLAN: 10

2️⃣ Aggregation Layer
Device: AGG-BR1 & AGG-BR2

VLANs:

10, 20, 30, 40

Interfaces:

GigabitEthernet0/0/0 → Trunk, VLANs 10, 20, 30, 40

GigabitEthernet0/0/1 → Trunk, VLANs 10, 20, 30, 40

GigabitEthernet0/0/2 → Trunk, VLANs 10, 20, 30, 40

Routing Protocol: IS-IS Level-1

AGG-BR1 NET: 49.0002.0000.0000.0005.00

AGG-BR2 NET: 49.0002.0000.0000.0006.00

IS-IS enabled on all trunk interfaces

Device: AGG-HQ1 & AGG-HQ2

VLANs:

10, 20, 30, 40

Interfaces:

GigabitEthernet0/0/0 → Trunk, VLANs 10, 20, 30, 40

GigabitEthernet0/0/1 → Trunk, VLANs 10, 20, 30, 40

GigabitEthernet0/0/2 → Trunk, VLANs 10, 20, 30, 40

Routing Protocol: IS-IS Level-1

AGG-HQ1 NET: 49.0001.0000.0000.0003.00

AGG-HQ2 NET: 49.0001.0000.0000.0004.00

IS-IS enabled on all trunk interfaces

3️⃣ Core / Border Routers
Device: BR-R2

Interfaces:

G0/0/0 → 172.16.2.1/30 (towards ISP)

G0/0/1 → 10.2.2.1/24 (LAN/DHCP)

G0/0/2 → 198.51.100.2/24 (IPSec VPN)

Routing:

Static: 0.0.0.0/0 → 172.16.2.2

Static: 198.51.100.1/32 → 198.51.100.254

BGP:

AS 200

Peer: 172.16.2.2 AS 65000

Networks advertised: 10.2.2.0/24

DHCP:

Pool: BR-POOL → 10.2.2.0/24, GW 10.2.2.1, DNS 8.8.8.8, Excluded 10.2.2.1–10.2.2.20

Interface G0/0/1 uses global DHCP

IPSec VPN:

ACL 3000 → Permit 10.2.2.0 → 10.1.1.0

ISAKMP pre-shared key → YourPSK

Policy → ESP-AES + SHA HMAC

Device: HQ-R1

Interfaces:

G0/0/0 → 172.16.1.1/30 (towards ISP)

G0/0/1 → 10.1.1.1/24 (LAN/DHCP)

G0/0/2 → 198.51.100.1/24 (IPSec VPN)

Routing:

Static: 0.0.0.0/0 → 172.16.1.2

Static: 198.51.100.2/32 → 198.51.100.254

BGP:

AS 100

Peer: 172.16.1.2 AS 65000

Networks advertised: 10.1.1.0/24

DHCP:

Pool: HQ-POOL → 10.1.1.0/24, GW 10.1.1.1, DNS 8.8.8.8, Excluded 10.1.1.1–10.1.1.20

Interface G0/0/1 uses global DHCP

IPSec VPN:

ACL 3000 → Permit 10.1.1.0 → 10.2.2.0

ISAKMP pre-shared key → YourPSK

Policy → ESP-AES + SHA HMAC

4️⃣ ISP / Internet Layer
Device: ISB-ISP

Loopback: 1.1.1.1/32

Interfaces:

G0/0/0 → 172.16.1.2/30

G0/0/1 → 172.16.2.2/30

G0/0/2 → 203.0.113.1/24

BGP:

AS 65000

Peer 172.16.1.1 AS 100

Peer 172.16.2.1 AS 200

Advertised network: 203.0.113.0/24

Default route: 0.0.0.0/0 → 203.0.113.254

Device: ISP-R

Loopback: 2.2.2.2/32

Interfaces:

G0/0/0 → 172.16.1.1/30

G0/0/1 → 172.16.2.1/30

G0/0/2 → 203.0.113.1/24

BGP:

AS 64512

Peer 172.16.1.2 AS 100

Peer 172.16.2.2 AS 200

Networks advertised: learned from CE routers

Default route: 0.0.0.0/0 → next-hop 203.0.113.254

5️⃣ CE (Customer Edge) VLAN Routing
Device: BR-CE

Interfaces:

G0/0/0 → 172.16.2.2/30

G0/0/1 → Subinterfaces:

0/0/1.10 → 10.2.1.1/24

0/0/1.20 → 10.2.2.1/24

Loopback0 → 10.2.255.254/32

Routing:

BGP AS 200

Peer 172.16.2.1 AS 64512

Networks: 10.2.1.0/24, 10.2.2.0/24, 10.2.255.254/32

IS-IS Level-1 enabled

DHCP: Enabled on subinterfaces for internal VLANs

Device: HQ-CE

Interfaces:

G0/0/0 → 172.16.1.2/30

G0/0/1 → Subinterfaces:

0/0/1.10 → 10.1.1.1/24

0/0/1.20 → 10.1.2.1/24

Loopback0 → 10.1.255.254/32

Routing:

BGP AS 100

Peer 172.16.1.1 AS 64512

Networks: 10.1.1.0/24, 10.1.2.0/24, 10.1.255.254/32

IS-IS Level-1 enabled

DHCP: Enabled on subinterfaces for internal VLANs

6️⃣ Network Design Notes

Topology Overview:

Access → Aggregation → CE → ISP → Internet

Routing Protocols:

CE-CE: BGP (external with ISP)

Aggregation layer: IS-IS Level-1

VLAN Design:

VLAN 10–40 in aggregation/HQ/BR switches

Access layer has VLAN 10 assigned

VPN/IPSec:

CE routers have IPSec tunnels configured to connect across untrusted network (198.51.100.x)

DHCP:

CE routers provide DHCP for internal VLANs

Loopbacks:

CE routers have loopbacks used as BGP router-IDs and for management
