# abr-asbr-multiarea-ospf
An enterprise-grade Multi-Area OSPFv2 Network Topology designed to simulate an organization's central headquarters connected to multi-region branch offices and an external ISP provider.

An enterprise-grade Multi-Area OSPFv2 Network Topology designed to simulate an organization's central headquarters connected to multi-region branch offices and an external ISP provider.This project demonstrates advanced Layer 3 routing protocols, Area Border Router (ABR) summary propagation, Autonomous System Boundary Router (ASBR) external route redistribution, and multi-access DR/BDR election dynamics.🌐 Quick Overview & Network Specs🟢 Architecture & Topology: Multi-Area OSPF Enterprise Network featuring a central Backbone (Area 0), non-backbone operational subnets (Area 1 & Area 2), and an isolated ISP External Transit Domain.🔵 Core Routing Protocols: OSPFv2 dynamic routing with manually configured Router IDs, custom wildcard masks, and sub-second convergence parameters.🟣 Inter-Area Communication (ABRs): Router R1 and Router R2 act as Area Border Routers, synthesizing intra-area topology into Type 3 Summary LSAs (O IA in the routing table).🔴 External Redistribution (ASBR): Router R3 operates as the Autonomous System Boundary Router, taking static/ISP default routes and injecting them domain-wide via Type 5 External LSAs (O E2 in the routing table).🟡 Layer 2 Transit Infrastructure: Multi-access backbone interconnect via Switch0 (123.0.0.0/29), demonstrating Designated Router (DR) / Backup Designated Router (BDR) election handling.🟠 High-Availability & Redundancy: Secondary inter-router point-to-point transit link (192.168.12.0/30) between ABRs to prevent Area 0 split-brain scenarios and ensure path redundancy.🗺️ Topology Diagram Summary                      +---------------------------------+
                      |     Area 0 (Backbone Domain)    |
                      |                                 |
 [ Area 1 ]           |   +----+  192.168.12.0/30 +----+|            [ Area 2 ]
+-----------+         |   | R1 |------------------| R2 ||          +-----------+
| Loopback  |---(ABR)-+---|2911|                  |2911|+- - (ABR) -| Loopback  |
|  1.1.1.1  |         |   +----+                  +----+|          |  2.2.2.2  |
+-----------+         |     \                    /      |          +-----------+
                      |      \                  /       |
                      |       +----------------+        |
                      |       | Switch0 (2960) |        |
                      |       | 123.0.0.0/29   |        |
                      |       +----------------+        |
                      |               |                 |
                      |             +----+              |
                      |             | R3 | (3.3.3.3)    |
                      +-------------|2911|--------------+
                                    +----+ (ASBR)
                                      |
                              203.0.113.0/30 (External Link)
                                      |
                                    +----+
                                    |ISP | Loopback: 8.8.8.8
                                    +----+
📐 Network Addressing & Area AllocationsDeviceRoleRouter IDInterface / LinkSubnet / IP AddressOSPF AreaLSA Types OriginatedR1ABR1.1.1.1Loopback0Gig0/0 (p2p)Gig0/1 (Switch0)1.1.1.1/32192.168.12.1/30123.0.0.1/29Area 1Area 0Area 0Type 1, Type 3R2ABR2.2.2.2Loopback0Gig0/0 (p2p)Gig0/1 (Switch0)2.2.2.2/32192.168.12.2/30123.0.0.2/29Area 2Area 0Area 0Type 1, Type 3R3ASBR3.3.3.3Loopback0Gig0/1 (Switch0)Gig0/0 (ISP Transit)3.3.3.3/32123.0.0.3/29203.0.113.1/30Area 0Area 0External (Static)Type 1, Type 4, Type 5ISPEdge Router8.8.8.8Loopback0Gig0/08.8.8.8/32203.0.113.2/30Out of AreaN/A (Static Route to R3)💡 Key Technical Insights & What This Lab Demonstrates🔍 LSA Hierarchy Analysis: Proves full mastery of the OSPF Link-State Database (LSDB) by auditing Type 1 (Router), Type 2 (Network), Type 3 (Summary), and Type 5 (External) LSAs across area boundaries.⚡ Traffic Optimization & Isolation: Demonstrates how multi-area segmentation limits Link-State Advertisement (LSA) flooding, reduces CPU/RAM overhead on routers, and isolates localized link flaps.🛠️ Real-World Verification Skills: Accompanied by full command-line audit logs showcasing show ip ospf neighbor, show ip ospf database, show ip route ospf, and end-to-end traceroute ICMP verification.🛡️ Edge Security & Routing Hygiene: Outlines proper redistribution policies at the ASBR edge to prevent routing loops and ensure controlled external network access.🛠️ Verification & Command Audit Logs1. Neighbor Adjacency AuditVerify full 2-WAY and FULL adjacency states across the multi-access and point-to-point links:R1# show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:34    192.168.12.2    GigabitEthernet0/0
2.2.2.2           1   FULL/BDR        00:00:38    123.0.0.2       GigabitEthernet0/1
3.3.3.3           1   FULL/DR         00:00:31    123.0.0.3       GigabitEthernet0/1
2. OSPF Database Structure (LSDB)Viewing inter-area and external prefix advertisements on R1:R1# show ip ospf database

            OSPF Router with ID (1.1.1.1) (Process ID 1)

		Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
1.1.1.1         1.1.1.1         450         0x80000003 0x002B1A 3
2.2.2.2         2.2.2.2         412         0x80000003 0x001F2C 3
3.3.3.3         3.3.3.3         398         0x80000002 0x0041B8 2

		Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
2.2.2.2         2.2.2.2         380         0x80000001 0x00A1A2

		Type-5 AS External Link States

Link ID         ADV Router      Age         Seq#       Checksum Tag
0.0.0.0         3.3.3.3         210         0x80000001 0x00E2B1 0
8.8.8.8         3.3.3.3         210         0x80000001 0x009F1C 0
3. Routing Table VerificationVerification of Inter-Area (O IA) and External (O E2) routes across the network:R1# show ip route ospf

      2.0.0.0/32 is subnetted, 1 subnets
O IA     2.2.2.2 [110/2] via 192.168.12.2, 00:12:44, GigabitEthernet0/0
      8.0.0.0/32 is subnetted, 1 subnets
O E2     8.8.8.8 [110/20] via 123.0.0.3, 00:08:15, GigabitEthernet0/1
O*E2  0.0.0.0/0 [110/1] via 123.0.0.3, 00:08:15, GigabitEthernet0/1
🚀 How to Run / Replicate This LabOpen Cisco Packet Tracer (v8.0 or higher).Load the topology using three Cisco 2911 Routers (R1, R2, R3), one Cisco 2960 Switch (Switch0), and an ISP edge router.Configure interfaces and IP addresses as specified in the Addressing Table.Apply OSPF process parameters (router ospf 1) with designated router IDs and network commands.Configure default static route on R3 pointing to ISP and execute redistribute static subnets or default-information originate.
