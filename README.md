# Phase 1 & 2 FortiGate Dual WAN Connectivity over MPLS and ISP Infrastructure

## 📌 Project Overview
This project simulates a highly available, enterprise-grade Dual-WAN infrastructure connecting corporate branch offices (`CUST-A SITE 1` and `CUST-A SITE 2`). The architecture validates a primary private service provider backbone alongside a backup broadband internet connection managed by edge FortiGate firewalls to guarantee continuous business uptime.

---

## 🛠️ Technologies & Protocols
- **Security & Edge Management:** FortiGate Firewall Integration, Deep Security Inspection
- **Core Provider Infrastructure:** MPLS LDP (Label Distribution Protocol), MP-BGP (Multiprotocol BGP)
- **Routing Protocols:** OSPF (Area 0 Core, Area 2 Customer Sites, Area 99 ISP Core), Static Routing
- **Redundancy & Failover:** Administrative Distance Manipulation, IPsec VPN Failover Architecture

---

## 📐 Network Architecture & Components

### 1. Primary WAN: MPLS-BGP Provider Cloud
Simulates a Tier-1 telecommunications provider infrastructure delivering private layer 3 transport across the core.
* **Autonomous System:** BGP AS 65400
* **Core Core Routing:** OSPF Area 0 provides underlying IGP reachability between provider routers.
- **Label Distribution:** MPLS LDP distributes core transport labels.
- **Customer Route Exchange:** MP-BGP configured between Provider Edge (PE) routers to exchange isolated customer VPN paths privately.
- **Nodes Involved:** Customer Edge (CE) Routers, Provider Edge (PE) Routers, and Core Provider (P) Routers.

### 2. Secondary WAN: Internet Broadband Simulation
Simulates a public internet transport layer acting as a cost-effective standby.
- **ISP Backhaul:** Handled via simulated ISP-1, INTERNET, and ISP-2 routers.
- **ISP Core Routing:** Dynamically exchanges global paths using OSPF Area 99.
- **Edge Handoff:** FortiGate firewalls leverage static default routing directed to their respective ISP gateways.

### 3. Edge Security & Dynamic Routing Integration
- **Site 1:** `CUST-A SITE 1` operating internally on OSPF Area 2 (Router ID: `40.40.40.40`).
- **Site 2:** `CUST-A SITE 2` operating internally on OSPF Area 2 (Router ID: `30.30.30.30`).
- FortiGate firewalls run dynamic routing natively toward the private CE routers and manage traffic filtering entering/leaving the corporate perimeter.

---

## 🔄 Traffic Engineering & Failover Mechanism
To establish an automatic standby path without causing asymmetric routing or packet duplication:
1. **Primary Path:** Traffic natively forwards through the high-performance MPLS private link.
2. **Floating Static Route:** A backup static default route is configured on the FortiGate towards the ISP gateway with an elevated **Administrative Distance of 200**.
3. **Failover Execution:** When the primary private circuit encounters a major link failure, the FortiGate instantly drops the primary route and injects the floating static route into the active routing table. This immediately triggers an encrypted IPsec VPN tunnel across the public internet to secure end-to-end branch communication.

---

## 📊 Verification & Testing Results

### End-to-End Connectivity Validation
Both paths were stress-tested and validated for deterministic behavior:
* **Internet WAN Stability:** OSPF neighbor adjacencies established smoothly between ISP edge nodes; 0% packet loss during baseline testing.
* **MPLS Core Stability:** MPLS labels correctly generated and exchanged; MP-BGP sessions active.

### Traceroute Inspections (MPLS Data Path Verification)

#### Site 2 to Site 1 Traceroute:
```text
Router# traceroute 172.16.1.2
Tracing the route to 172.16.1.2
1 172.16.2.1 1 msec
2 192.168.4.3 2 msec
3 192.168.2.3 2 msec
4 20.0.0.2 [MPLS: Labels 16/20 Exp 0] 4 msec
5 192.168.1.1 [MPLS: Label 28 Exp 0] 2 msec
6 192.168.1.4 5 msec
7 192.168.3.2 4 msec
8 172.16.1.2 5 msec
