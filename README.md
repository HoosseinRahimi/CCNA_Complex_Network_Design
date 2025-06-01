# CCNA Enterprise Network Design & Implementation with Advanced Security

## Overview
This project meticulously details the design, configuration, and comprehensive security implementation of a multi-area enterprise network. It thoroughly leverages a wide array of Cisco CCNA concepts to establish robust connectivity, enforce stringent security measures, and ensure efficient resource management across diverse network segments, specifically focusing on **Area 0 (Backbone), Area 16, and Area 17**.

The primary goal was to create a fully functional, highly secure, and scalable network environment, emphasizing practical implementation of complex protocols and features, advanced subnetting, and demonstrating proficiency in critical networking and security tasks. This project achieved a perfect score of 100/100 in the CCNA course at Tehran Institute of Technology (MFT).

## Key Features & Technologies Implemented

This project showcases a comprehensive understanding and practical application of the following networking concepts and technologies:

### Routing & Addressing
* **Multi-Area OSPF:**
    * **Area 0 (Backbone):** All LAN segments and Point-to-Point (PTP) links within Area 0 are precisely subnetted and configured within Area 0.
    * **Area 16 & Area 17:** Specific LAN and PTP networks in these areas are assigned their respective Area IDs, with meticulous subnetting to accommodate the exact number of required LANs (e.g., using `/27` for LANs) and PTP links (using `/30`).
    * **Point-to-Point Links:** Dedicated `/30` subnets from the allocated Area network ranges are used for inter-router communication (e.g., for R11-R5, R1-R2 links). **No dummy addresses are used.**
    * **R5 & R11 Link:** This critical link is configured as a Point-to-Point OSPF connection within **Area 0**, ensuring correct routing between the backbone and other areas.
* **Inter-VLAN Routing (Router-on-a-Stick):** Implemented on the Layer 3 Switch in Area 0 (Part 1 of the topology) for efficient communication between different VLANs. The link between the Layer 2 switches and the Layer 3 switch is configured as a PTP link.
* **Default Route:** A default route is configured on the OSPF network, directing traffic towards the network exit point.
* **DHCP Services:**
    * Configured on designated routers (e.g., R5, R11) as DHCP servers for specific VLANs.
    * **Area 0 DHCP:** DHCP services are active for relevant VLANs in Area 0.
    * **Area 17 DHCP:** R5 is configured as the DHCP server for LANs in Area 17, ensuring clients in Area 17 receive IPs from R5 and **not** from Area 0's DHCP server.
    * **Area 16 DHCP:** DHCP is configured to provide IP addresses for specific VLANs. Each system is designed to receive only one DHCP lease, based on its VLAN.

### Switching & VLANs
* **VLAN Configuration & Subnetting:** Precise VLAN segmentation and corresponding IP subnetting across all switches (SW1, SW3, SW7, SW4, SW5, SW6) based on the network design. Each switch is configured to only recognize and manage its relevant VLANs.
* **Trunking:**
    * R1 and R2 are connected to SW1 via trunk links in Area 17.
    * VLAN configurations and trunking are correctly implemented between switches in Area 16.
* **Spanning Tree Protocol (STP):**
    * **Area 17:** STP is active and correctly configured to prevent loops while ensuring path redundancy.
    * **Area 16:** STP is active and meticulously configured between switches to prevent loops, specifically managed to avoid blocking active forwarding paths.

### Security Implementation
* **Port Security:**
    * **SW7 (Area 0 - connected to Hub):**
        * Configured to ensure authorized traffic always passes, while unauthorized traffic is blocked.
        * Dynamically learns MAC addresses (`sticky`) and stores them in the running configuration.
        * Maximum of **3 MAC addresses** allowed per port.
    * **Area 16 Switches (SW4, SW5, SW6):**
        * Configured for port security to allow only **one dynamically learned MAC address** per port (`sticky`).
        * If another device replaces the learned MAC, the port is designed to be blocked.
* **DHCP Snooping:**
    * **SW3 (Area 0):** Enabled specifically for VLANs pertinent to Area 0.
    * Trusted/untrusted ports are configured to prevent rogue DHCP servers; only the designated DHCP source port is trusted.
* **Access Control Lists (ACLs):**
    * **Area 16 - VLAN 6:** Clients in VLAN 6 are prevented from accessing DNS services.
    * **Area 16 - DHCP Client Restriction:** All clients in Area 16 are configured to obtain IP addresses only from the designated DHCP server (either router or server).
    * **Area 0 - SW7 Access Control:** Only clients located in Area 0 are permitted to Telnet and SSH to SW7.
    * **Area 16 - SW1 Access Control:** Computers in Area 16 are permitted to Telnet only to SW1, and explicitly prevented from SSHing to SW1.
    * **Area 17 - DHCP Server Exclusion:** ACLs are configured to ensure Area 17 clients **do not** receive IP addresses from the DHCP server in Area 0.
    * **R5 (Border Router between Area 0 and Area 17):** ACLs are meticulously configured to permit only DHCP traffic to pass between these two areas, enforcing strict security at the boundary.
    * **General SSH & Telnet Control:** Appropriate ACLs are written to restrict SSH and Telnet access to devices, ensuring only authorized access is permitted.
    * **General DNS Access:** ACLs manage DNS traffic access as per design.

## Network Topology
Below is the comprehensive network diagram for this project, illustrating the intricate design and security zones:

![Network Topology Diagram]([network_topology.png](https://github.com/HoosseinRahimi/CCNA_Complex_Network_Design/blob/main/CCNA_Complex_Network_Design/network_topology.png))

## Project Goals & Challenges
* **Goal 1:** Achieve full end-to-end connectivity across all VLANs and OSPF areas, with precise subnetting.
* **Goal 2:** Implement and verify robust DHCP services across all designated LANs and VLANs, with specific source restrictions.
* **Goal 3:** Secure the network using comprehensive ACLs, Port Security, and DHCP Snooping, enforcing granular access control.
* **Goal 4:** Ensure proper STP operation in relevant areas without creating path disruptions, maintaining redundancy.
* **Challenge 1: Complex Multi-Area OSPF with Precise Subnetting:** Accurately segmenting the network into Area 0, Area 16, and Area 17, implementing correct PTP links, and ensuring seamless routing principles between them, especially on the ABR (R5), while strictly adhering to subnetting requirements.
* **Solution 1:** Meticulous planning of network addresses, precise calculation of subnets for each LAN and PTP link, and careful configuration of OSPF process IDs, area assignments, and network statements on all routers.
* **Challenge 2: Layer 3 Switch Inter-VLAN Routing & PTP Link:** Implementing router-on-a-stick on a Layer 3 switch in Area 0 and configuring a PTP link between Layer 2 and Layer 3 devices while maintaining proper VLAN and routing integrity.
* **Solution 2:** Careful configuration of VLAN interfaces (SVIs) on the Layer 3 switch, proper encapsulation on trunk ports, and precise `/30` subnetting for the PTP link.
* **Challenge 3: Integrated & Granular Security Implementation:** Combining Port Security (with dynamic sticky MAC and limit), DHCP Snooping (with trusted/untrusted ports), and a multitude of specific ACLs (for DHCP source control, SSH/Telnet access, DNS restriction, and inter-area traffic) to create a robust security posture without hindering legitimate traffic.
* **Solution 3:** Incremental testing of each security feature, careful crafting of ACL rules (implicit deny consideration), and detailed verification using `show` commands (`show port security`, `show ip dhcp snooping binding`, `show access-lists`) to ensure expected behavior and identify any unintended blocking.
* **Challenge 4: STP in Multi-Switch Environments with Active Path Preservation:** Configuring STP in Area 16 and Area 17 to prevent loops and ensure redundancy, while actively verifying that it does not block essential active paths required for full connectivity.
* **Solution 4:** Strategic placement of root bridges, careful adjustment of port costs and priorities where necessary, and continuous monitoring with `show spanning-tree` commands to ensure optimal paths.

## Verification & Testing
Extensive verification was performed throughout the project lifecycle to ensure all functionalities and security policies operate as intended:
* `ping` and `traceroute` commands were used to confirm end-to-end connectivity and path verification across all VLANs and OSPF areas.
* `show ip route`, `show ip ospf neighbor`, `show ip ospf database`, `show vlan brief`, `show interfaces trunk`, `show ip dhcp binding`, `show ip dhcp snooping binding`, `show ip interface brief`, and `show access-lists` commands were used to verify routing, VLANs, DHCP, DHCP Snooping, and ACL configurations.
* `show port security`, `show mac address-table`, `show mac address-table secure` were extensively used to confirm port security operation, dynamically learned MACs, and port blocking.
* Device reachability via SSH and Telnet was tested from authorized and unauthorized clients as per ACL rules, and DNS resolution was confirmed for authorized segments.

## Future Enhancements
* Implement IPv6 addressing alongside IPv4 across all areas.
* Introduce Quality of Service (QoS) policies for critical voice/video traffic.
* Explore advanced security features like Zone-Based Firewall (ZBFW) or IPS/IDS.
* Integrate a network monitoring system (e.g., SNMP) for proactive management.

---
*Completed as part of CCNA coursework at Tehran Institute of Technology (MFT) (مجتمع فنی تهران)*
*Score: 100/100*
