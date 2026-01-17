# Changelog — AD Lab Network Stabilization

**Date:** 2026-01-17

**Scope:** Cisco SG L3 switch + AD/DHCP/DNS lab network

**Status:** Applied and verified working

**Impact:** Stabilized inter-VLAN routing, DHCP, DNS, and internet access for AD lab

---

## Summary

Reverted experimental VLAN / multi-router topology and restored a **clean Layer-3 AD lab design** using the Cisco SG switch as the L3 core. Removed conflicting routing, DHCP relay, and VRRP configurations that caused nondeterministic behavior. Result is a stable, predictable AD lab with double-NAT internet access.

---

## Network Design (Post-Change)

### Architecture

* **Cisco SG switch** acts as **Layer-3 core**
* **SVIs present for VLANs 10 / 20 / 30 / 40**
* **AD/DHCP/DNS server:** `192.168.1.100`
* **Default route:** `192.168.1.1` (upstream router)
* **Double NAT:** lab → upstream router → ISP (acceptable for lab)

### VLAN Roles

| VLAN | Name        | Subnet          | Gateway      |
| ---- | ----------- | --------------- | ------------ |
| 10   | Server      | 192.168.10.0/24 | 192.168.10.1 |
| 20   | Workstation | 192.168.20.0/24 | 192.168.20.1 |
| 30   | Admin       | 192.168.30.0/24 | 192.168.30.1 |
| 40   | PenTest     | 192.168.40.0/24 | 192.168.40.1 |
| 1    | Management  | 192.168.1.0/24  | 192.168.1.1  |

---

## Changes Made

### 1. Removed Conflicting / Experimental Configuration

* Reverted all partially implemented:

  * Transit VLANs
  * VRRP configuration
  * Bastion / VLAN100 routing experiments
* Returned to a **single, consistent routing model**

---

### 2. Standardized Switch as the Only L3 Gateway for Lab VLANs

* Confirmed **SVIs exist only on the switch** for:

  * VLAN 10
  * VLAN 20
  * VLAN 30
  * VLAN 40
* Ensured **exactly one default gateway per VLAN**
* Normalized gateway IPs to `.1` where applicable

---

### 3. Cleaned Up DHCP Relay Configuration

**Before:**

* Multiple `ip helper-address` statements
* Per-VLAN relay mixed with global relay
* Self-referencing relay entries

**After:**

* Single DHCP relay target:

  * `192.168.1.100` (AD/DHCP/DNS server)
* DHCP relay enabled **only where needed**
* Removed all redundant and conflicting helper entries

Result:

* Predictable DHCP behavior
* Correct gateway and DNS assignment
* No cross-VLAN leakage

---

### 4. Verified and Corrected Trunk Configuration

* Explicitly set trunk mode on uplink:

  * `gi1/1/1` trunks VLANs 10/20/30/40
* Ensured access ports are explicitly assigned to correct VLANs
* Eliminated ambiguous port modes

---

### 5. Verified Routing and Internet Access

* Switch routing table shows:

  * Connected routes for VLANs 10/20/30/40
  * Default route → `192.168.1.1`
* Hosts in all lab VLANs:

  * Obtain DHCP leases
  * Resolve DNS via AD
  * Reach internet successfully

---

## Validation Tests Performed

### From VLAN 20 Host

* `ping 192.168.20.1` 
* `ping 192.168.1.100` 
* `ping 1.1.1.1` 
* `nslookup google.com` 

### AD Functionality

* DHCP scopes issuing correct:

  * Gateway
  * DNS
  * Domain options
* No intermittent authentication or name resolution issues observed

---

## Known Limitations (Accepted)

* **Double NAT** remains in place (lab → router → ISP)
* No bastion or VLAN100 separation at this stage
* ISP router limitations unchanged

These are acceptable and intentional for the current lab scope.

---

## Next Planned Changes (Not Yet Applied)

* Reintroduce **VLAN 100 (home / ISP LAN)** as L2 only
* Add **bastion VLAN** with tightly scoped access to:

  * Desktop
  * Internet
* Enforce access control via switch ACLs or downstream router rules
* Maintain current AD lab baseline unchanged during expansion

---

## Rollback Plan

If issues arise:
1. Restore saved running-config from this baseline
2. Verify:

   * SVIs
   * DHCP relay target
   * Default route
3. Confirm AD/DHCP/DNS server availability
