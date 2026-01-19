# Enterprise Lab — Personal Network & AD Environment

## Overview

This repository documents a personal, continuously evolving **enterprise-style lab environment** built to gain hands-on experience with real-world networking, Active Directory, routing, segmentation, and operational tradeoffs. The lab is actively used day-to-day, not just simulated, and changes are tracked over time as design decisions are tested, validated, or rolled back.

The primary goals of this project are:
- To build practical experience with enterprise concepts in a home environment
- To document decisions, failures, and tradeoffs in an ops-log style
- To provide reference material others can learn from or adapt
- To support blog posts that walk through specific implementations and lessons learned

This is **not** a single-purpose demo or a frozen “perfect” design. It is a living lab.

![Current Network Diagram](/network-20261701.png)

---

## Design Philosophy

This lab is guided by a few core principles:

- **Stability over cleverness**  
  Experimental configurations are tested, documented, and removed if they introduce nondeterministic behavior.

- **Physical path awareness matters**  
  Performance-critical traffic is routed intentionally based on actual throughput and latency, not just logical topology.

- **Consumer gear is edge-only**  
  Consumer routers are treated strictly as NAT/DHCP edges, not as routing cores.

- **VLANs and subnets are tools, not defaults**  
  Segmentation is introduced only when it provides clear operational value.

- **Overlay networking preferred for admin access**  
  Tailscale is used for bastion access and cloud connectivity instead of exposing services to the LAN.

- **Document what actually happened**  
  The changelog reflects real changes, reversions, and outcomes — not idealized designs.

- **Operational issue tracking**  
  Incidents, outages, and unexpected behavior are documented using Jira ITSM to maintain an ops-oriented workflow alongside the changelog.

---


## High-Level Architecture

At a high level, the environment consists of:

- A **Cisco SG500X** acting as a Layer-3 switch for lab VLANs
- A **TP-Link Archer** acting as the internal LAN DHCP/NAT edge
- A **T-Mobile 5G gateway** providing high-throughput WAN access
- An **Active Directory lab** with multiple routed VLANs
- Multiple Linux and Windows hosts placed intentionally based on role
- Cloud-hosted servers connected via Tailscale

The environment supports both:
- **Isolated experimentation** (AD lab, VLAN routing, security tooling)
- **Daily-use workloads** (desktop, bastion, media, admin access)

---

## Core Components

### Networking
- **Cisco SG500X-48P-K9**
  - Layer-3 core for lab VLANs
  - Inter-VLAN routing
  - DHCP relay for lab subnets
- **TP-Link Archer**
  - DHCP + NAT for `192.168.1.0/24`
  - Internal LAN edge
- **T-Mobile 5G Gateway**
  - High-throughput WAN
  - Separate upstream subnet for performance-critical hosts

### Active Directory Lab
- **Windows Server 2016**
  - Domain Controller
  - DNS
  - DHCP for lab VLANs
- **Windows 11**
  - Domain-joined lab clients

### Linux Hosts
- **AlmaLinux (Headless Bastion)**
  - Placed on the T-Mobile subnet
  - Used for administrative access and control-plane tasks
  - Primary jump host for cloud servers via Tailscale
- **AlmaLinux (GUI Workstation)**
  - Placed on the internal LAN
  - Used for administration, testing, and lab-adjacent workloads

### Desktop
- **Windows 10**
  - Primary daily-use system
  - Routed through the SG500X to the T-Mobile gateway to centralize connectivity while preserving throughput

### Cloud Integration
Connected via **Tailscale**:
- Vultr Kali Linux (security testing)
- Vultr Debian (web services)
- Oracle Cloud Rocky Linux

---

## What This Repo Is (and Is Not)

### This repo **is**:
- A technical ops journal
- A learning and portfolio artifact
- A reference for enterprise-style design decisions in constrained environments
- A companion to blog posts and walkthroughs

### This repo **is not**:
- A one-click reproducible lab
- A full configuration dump
- A security guide with sensitive details
- A static or “final” architecture

Tutorials and walkthroughs may be added, but exact IPs, secrets, and production-sensitive details are intentionally omitted.

---

## Repository Structure (Current)

```
/
├── README.md
└── CHANGELOG.md
```


Additional documentation and materials will be added over time as the lab evolves.

---

## Change Tracking

All meaningful changes to the environment are documented in `CHANGELOG.md`, including:
- Architectural decisions
- Experiments that were reverted
- Stability fixes
- Role and traffic-path changes

The changelog is treated as an **ops log**, not marketing documentation.

---

## Future Work

Planned or likely future work includes:
- Additional documentation and diagrams
- Wi-Fi network segmentation for IoT and miscellaneous devices
- Expanded security tooling and monitoring
- Continued refinement aligned with Network+ and security studies

---

## Background

This lab supports ongoing professional development, including:
- CompTIA Security+
- TestOut Security Pro
- Network+ (in progress)
- Active participation in TryHackMe

---

## License / Use

This repository is public for learning and reference.  
Feel free to adapt ideas or structures, but expect differences based on hardware, ISPs, and constraints.

---


