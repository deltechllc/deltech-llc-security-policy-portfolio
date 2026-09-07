<p align="center">
  <img src="./assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Security Architecture & Environment Overview

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO)
**Version:** 1.1
**Effective Date:** July 14, 2026
**Review Cycle:** Annual, or upon significant infrastructure change
**Classification:** Internal — Restricted Distribution
**Document Type:** Reference Document (supports all subordinate security policies)

---

## Framework Alignment

| Standard | Reference |
|---|---|
| ISO/IEC 27001:2022 | Annex A.5.9 (Inventory of Information and Other Associated Assets), A.8.20–A.8.22 (Network Security, Segregation, Filtering) |
| NIST CSF 2.0 | IDENTIFY (ID.AM — Asset Management), PROTECT (PR.AA, PR.DS, PR.IR) |
| NIST SP 800-53 Rev. 5 | CM-8 (System Component Inventory), SC-7 (Boundary Protection), AC-4 (Information Flow Enforcement) |
| NIST SP 800-82 Rev. 3 | Guide to Operational Technology (OT) Security — referenced for SCADA/ICS segmentation guidance |

---

## 1. Purpose

This document provides a single reference description of DelTech LLC's network architecture, IT and OT environments, and security technology stack. It exists to ensure consistency across all subordinate security policies — rather than re-describing the environment in each policy, subordinate documents (Access Control, Incident Response, Vulnerability & Patch Management, etc.) reference this document directly.

This is a reference document, not a policy in itself; it does not impose requirements but describes the environment those requirements apply to.

## 2. Company & Site Overview

DelTech LLC is a widget manufacturer headquartered in Portland, ME, operating two physical sites:

| Site | Function |
|---|---|
| **Corporate HQ (Portland, ME)** | Corporate offices, management, sales, finance, IT operations |
| **Manufacturing Site (Westbrook, ME)** | Widget production floor, SCADA/ICS-controlled manufacturing equipment, local warehousing/shipping |

Both sites host three common VLANs (Guest, Corporate, Management), and the Manufacturing Site additionally hosts a logically separate SCADA/ICS environment. The two sites are connected via a Cisco SD-WAN implementation, and both sites connect to an Azure virtual network via a site-to-site VPN Gateway for cloud-hosted services (see Section 3.5).

## 3. Network Architecture

### 3.1 Site-Level VLAN Structure

Each site is segmented into three standard VLANs, consistently defined across both locations:

| VLAN | Purpose | Typical Access |
|---|---|---|
| **Guest** | Visitor and non-corporate device internet access | Internet only; no access to internal resources |
| **Corporate** | End-user workstations, laptops, general business applications | Access to approved business systems (ERP, email, file shares); internet access via corporate proxy |
| **Management** | Out-of-band administrative access to network, server, and security infrastructure | Restricted to IT/Security admin accounts; MFA-enforced; no general user access |

### 3.2 SCADA/ICS Environment (Manufacturing Site Only)

The Manufacturing Site hosts a fourth environment — the SCADA/ICS network — which is **logically separate** from the Guest, Corporate, and Management VLANs. This environment controls production-line equipment and is treated as a distinct trust zone due to the operational sensitivity and legacy nature of many ICS components (see Section 4.2).

- No direct routing exists between the SCADA/ICS environment and the Guest or general Corporate VLAN.
- Any required data exchange between SCADA/ICS and IT systems (e.g., production data flowing to the ERP) passes through a controlled interface — see Section 6 (Data Flows).
- Management-plane access to SCADA/ICS devices is restricted to authorized OT/Plant Engineering personnel and routed through the Management VLAN with additional compensating controls (see the Access Control Policy).

### 3.3 Inter-Site Connectivity: Cisco SD-WAN

The Portland HQ and Manufacturing Site are connected via a **Cisco SD-WAN** implementation, providing:

- Encrypted site-to-site connectivity over the WAN transport
- Application-aware routing and path selection between sites
- Centralized policy enforcement for VLAN-to-VLAN traffic permitted across the WAN link
- A single point of policy administration for inter-site segmentation rules, in coordination with the Network Engineering Team

By default, VLANs of the same type are permitted to communicate across sites (e.g., Corporate-to-Corporate) where a legitimate business need exists; cross-VLAN-type traffic (e.g., Guest-to-Corporate, any traffic to/from SCADA/ICS) is denied by default and requires an explicit, documented exception.

### 3.4 Azure Network

DelTech maintains a single Azure Virtual Network (VNet), connected to both on-premises sites via a **site-to-site VPN Gateway**. The Azure environment is treated as a logical extension of the Management/Corporate trust boundary and is subject to the same segmentation principles as the on-premises VLANs.

The Azure VNet is segmented into subnets by function:

| Subnet | Purpose |
|---|---|
| **Management Subnet** | Azure replica domain controller and a hardened jump box used for administrative access into the Azure environment |
| **Monitoring Subnet** | Supporting infrastructure for the Microsoft Sentinel workspace (log ingestion, data collection endpoints) |
| **Gateway Subnet** | Reserved for the VPN Gateway connecting Azure to the Portland HQ and Manufacturing Site |

Network Security Groups (NSGs) are applied at the subnet level to restrict traffic between subnets and from on-premises networks, consistent with the default-deny approach used for on-premises VLAN-to-VLAN traffic. Administrative access to Azure VMs is restricted to the jump box in the Management Subnet; direct RDP/SSH exposure to the internet is not permitted.

Key Azure-hosted virtual machines:

| VM | Role |
|---|---|
| Domain Controller (Azure Replica) | Extends the on-premises Active Directory domain into Azure via standard AD replication, providing directory services redundancy for Azure-hosted resources; the on-premises domain controller at Portland HQ remains authoritative |
| Jump Box | Sole administrative access point into the Azure VNet; MFA-enforced, session-logged |

The Microsoft Sentinel workspace itself is a cloud-native (SaaS) service and is not VM-hosted, but its supporting log collection infrastructure resides in the Monitoring Subnet described above.

### 3.5 High-Level Network Diagram (Logical)

```
                       ┌──────────────────────────┐
                       │   Cisco SD-WAN Fabric     │
                       │  (encrypted site link)    │
                       └─────────────┬─────────────┘
             ┌─────────────────────────────────────────┐
             │                                          │
  ┌──────────▼──────────┐                  ┌──────────▼──────────┐
  │     Portland HQ      │                  │  Manufacturing Site  │
  │                       │                  │  (Westbrook, ME)      │
  ├───────────────────────┤                  ├───────────────────────┤
  │  VLAN: Guest          │                  │  VLAN: Guest          │
  │  VLAN: Corporate      │                  │  VLAN: Corporate      │
  │  VLAN: Management     │                  │  VLAN: Management     │
  │   (incl. on-prem AD    │                  │  SCADA/ICS (isolated) │
  │    Domain Controller)  │                  │                        │
  └───────────┬────────────┘                  └───────────┬────────────┘
              │                                            │
              │            Site-to-Site VPN Gateway         │
              └───────────────────────┬────────────────────┘
                                       │
                          ┌────────────▼─────────────┐
                          │      Azure VNet           │
                          ├────────────────────────────┤
                          │  Gateway Subnet            │
                          │  Management Subnet         │
                          │   (AD Replica DC, Jump    │
                          │    Box)                    │
                          │  Monitoring Subnet         │
                          │   (Sentinel log collection)│
                          └────────────────────────────┘
```

## 4. IT and OT Environment

### 4.1 IT Environment (Both Sites)

DelTech operates a **hybrid identity model**: an on-premises Active Directory domain controller, located at the Portland HQ (Management VLAN), serves as the authoritative directory for on-premises resources and synchronizes with Microsoft Entra ID via Microsoft Entra Connect (formerly Azure AD Connect). This allows users to authenticate to both on-premises resources (via the on-prem domain controller) and cloud/SaaS resources (via Entra ID) using a single set of credentials.

| Function | Platform |
|---|---|
| On-Premises Directory Services | Active Directory Domain Controller (Portland HQ, Management VLAN) — authoritative for on-prem resource authentication |
| Cloud Identity & Access Management | Microsoft Entra ID (conditional access, MFA) — synchronized from on-prem AD via Microsoft Entra Connect |
| Endpoint Management | Microsoft Intune |
| Productivity/Collaboration | Microsoft 365 (Exchange Online, SharePoint, Teams) |
| Enterprise Resource Planning | ERP system (production, inventory, finance) |
| Endpoint Operating Systems | Windows 10/11 (corporate fleet) |

### 4.2 OT/Manufacturing Environment (Manufacturing Site)

The SCADA/ICS environment controls widget production line equipment. Key characteristics relevant to security planning:

- A mix of modern and legacy programmable logic controllers (PLCs) and human-machine interfaces (HMIs), some of which cannot be patched on the same cadence as IT assets without risking production downtime.
- Availability is prioritized over confidentiality in this environment, consistent with typical ICS risk profiles — an outage on the production floor has direct, immediate business impact.
- Compensating controls (network isolation, strict access control, monitoring) are used in place of traditional IT controls (frequent patching, standard endpoint agents) where those controls aren't operationally feasible. See the Vulnerability & Patch Management Policy for the OT-specific patching approach.

## 5. Security Technology Stack

| Category | Tool/Platform | Notes |
|---|---|---|
| SIEM | Microsoft Sentinel | Cloud-native SaaS SIEM hosted in the DelTech Azure VNet's Monitoring Subnet (log collection infrastructure); central log aggregation and detection across IT environment; SCADA/ICS telemetry integrated via a dedicated collector where feasible |
| Endpoint Detection & Response (EDR) | CrowdStrike Falcon | Deployed across the Corporate VLAN endpoint fleet at both sites |
| Vulnerability Management | Tenable.io / Nessus | Scheduled scanning of IT assets; OT assets scanned via passive/out-of-band methods to avoid disrupting production systems |
| On-Premises Directory Services | Active Directory Domain Controller (Portland HQ) | Authoritative directory for on-premises resource authentication; synchronizes to Microsoft Entra ID via Microsoft Entra Connect; replicated to an Azure-hosted replica DC for redundancy |
| Identity & Conditional Access | Microsoft Entra ID | MFA enforcement, conditional access policies, privileged access controls; synchronized from on-premises AD |
| Endpoint Management | Microsoft Intune | Device compliance policies, configuration baselines |
| Network/SD-WAN | Cisco SD-WAN | Site-to-site connectivity, inter-VLAN policy enforcement |
| Remote Access VPN Client | Cisco Secure Client (formerly AnyConnect) | Client VPN software for approved remote user access to corporate systems, consistent with DelTech's Cisco network infrastructure |
| Email/Collaboration Security | Microsoft 365 (Defender for Office 365) | Phishing/malware protection for email and collaboration platforms |
| Cloud Infrastructure | Azure Virtual Network | Hosts the Azure replica domain controller, administrative jump box, and Sentinel log collection infrastructure; connected to both on-prem sites via site-to-site VPN Gateway |
| Disaster Recovery / Replication | Zerto | Continuous, journal-based replication and orchestrated failover for critical virtualized IT workloads (ERP, on-premises/Azure domain controllers, file/application servers); see Business Continuity & Disaster Recovery Policy |
| VM Backup | Veeam Backup & Replication | Scheduled, image-level backup and retention for the virtualized server estate across both sites and Azure; complements Zerto's failover-oriented replication; see Business Continuity & Disaster Recovery Policy |

## 6. Data Flows Between IT and OT Environments

Legitimate data exchange between the SCADA/ICS environment and IT systems is limited to specific, documented flows rather than open connectivity:

| Data Flow | Source | Destination | Control |
|---|---|---|---|
| Production/output data | SCADA/ICS historian | ERP system | One-way data diode or tightly firewalled interface; no inbound connectivity from IT to SCADA/ICS permitted |
| OT security telemetry | SCADA/ICS network monitoring | SIEM (Microsoft Sentinel) | Read-only forwarding via dedicated collector; no control-plane access |
| Engineering/maintenance access | Authorized OT engineering workstation | SCADA/ICS management interface | Routed through Management VLAN; MFA and session logging required; see Access Control Policy |
| Log forwarding | On-premises endpoints, network devices, security tools | Microsoft Sentinel (Azure Monitoring Subnet) | One-way telemetry forwarding over the site-to-site VPN Gateway; no inbound connectivity from Azure to on-prem VLANs beyond the designated jump box path |
| Administrative access | Authorized IT admin (via Management VLAN) | Azure jump box | MFA-enforced; sole entry point into the Azure VNet; session logged and monitored |

Any new data flow between IT and OT environments must be documented, risk-assessed, and approved by the CISO in coordination with OT/Plant Engineering leadership before implementation.

## 7. Maintenance of This Document

This document is reviewed at least annually by the CISO and Network Engineering Team, and updated immediately following any material change to network topology, site count, or the security technology stack, to ensure subordinate policies remain accurate by reference.

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0. It serves as the canonical environment reference for all subordinate security policies.*
