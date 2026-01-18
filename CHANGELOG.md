# Changelog — Network Topology Expansion & Role Separation

**Date:** 2026-01-17

**Scope:** Cisco SG L3 switch, TP-Link Archer, AD/DHCP/DNS lab, dual-upstream host placement

**Status:** Applied and verified working

**Impact:** Preserved stable AD lab routing and services while introducing a dual-upstream model for performance-critical and internal hosts without altering baseline lab behavior

---

## Summary

The AD lab baseline was preserved without functional changes: 
- Cisco SG500x continues to operate as the Layer-3 core routing lab VLANs
- AD Domain Controller remains dual-homed and authoritative for lab DHCP
- TP-Link Archer remains the DHCP/NAT edge for the internal LAN.

A dual-upstream host model was introduced, placing a headless Alma bastion on the T-Mobile subnet for independent, high-throughput access while keeping a GUI Alma workstation on the internal LAN. 

The desktop, previously connected directly to the T-Mobile gateway, was moved behind the SG500x switch while retaining the same upstream path, centralizing physical connectivity without impacting WAN performance and preserving a stable, predictable AD lab environment.

---

### Baseline
- Cisco SG500x operating as L3 core for lab VLANs
- TP-Link Archer providing DHCP + NAT for 192.168.1.0/24
- AD DC dual-homed via SG500x:
  - 192.168.1.100 (LAN / management)
  - 192.168.20.50 (lab services)
- Lab VLANs (10/20/30/40) routed by SG500x
- DHCP relay to AD DC for lab subnet
- DHCP Authority:
  - Archer is authoritative for 192.168.1.0/24
  - AD DC is authoritative for lab VLAN subnets:
    - 192.168.10.0/24
    - 192.168.20.0/24
    - 192.168.30.0/24
    - 192.168.40.0/24
- Default route from SG500x → TP-Link Archer

### Added
- Deployed **two AlmaLinux systems** with distinct roles and upstream connectivity:
  - **Headless Alma Bastion**
    - Subnet: 192.168.12.0/24
    - DHCP from T-Mobile 5G gateway
    - Co-resident with primary desktop
    - Access via Tailscale
  - **GUI Alma Workstation**
    - Subnet: 192.168.1.0/24
    - DHCP from TP-Link Archer
    - Connected to internal LAN via SG500x

### Changed
- Introduced dual-upstream network design:
  - Performance-critical hosts (desktop + bastion) bypass Archer and lab routing
  - Internal admin and lab-adjacent hosts remain behind Archer NAT
- Preserved existing lab routing model:
  - SG500x continues inter-VLAN routing
  - DHCP relay unchanged for lab VLANs


### Notes
- Bastion is intentionally unreachable from LAN; access provided via overlay networking
- Physical path separation favored over logical segmentation to reduce complexity
- AD lab baseline restored and validated prior to applying changes
