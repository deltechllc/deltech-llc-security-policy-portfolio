<p align="center">
  <img src="./assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Access Control Policy

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO)
**Version:** 1.1
**Effective Date:** July 14, 2026
**Review Cycle:** Annual, or upon significant organizational/technical change
**Classification:** Internal

---

## Framework Alignment

| Standard | Reference |
|---|---|
| ISO/IEC 27001:2022 | Annex A.5.15–A.5.18 (Access Control, Access Rights), A.8.2–A.8.5 (Privileged Access, Authentication) |
| NIST CSF 2.0 | PROTECT (PR.AA — Identity Management, Authentication, and Access Control) |
| NIST SP 800-53 Rev. 5 | AC-2 (Account Management), AC-3 (Access Enforcement), AC-6 (Least Privilege), IA-2 (Identification and Authentication) |
| NIST SP 800-82 Rev. 3 | Access control guidance for OT/ICS environments |

This policy should be read alongside [`00-security-architecture-overview.md`](./00-security-architecture-overview.md), which defines the network segments, VLANs, and Azure environment referenced throughout.

---

## 1. Purpose

This policy defines the requirements for granting, managing, and revoking access to DelTech LLC's information systems, network segments, and data, ensuring that access is granted based on business need, least privilege, and appropriate authentication strength.

## 2. Scope

This policy applies to all DelTech employees, contractors, and third parties who access:

- Corporate IT systems and the Corporate VLAN at either site
- The Management VLAN and any administrative/privileged functions
- The Guest VLAN
- The SCADA/ICS environment at the Manufacturing Site
- The Azure-hosted environment (replica domain controller, jump box, Sentinel infrastructure)
- The ERP system and other business applications

## 3. Access Control Principles

DelTech access control is governed by the following core principles:

1. **Least Privilege** — Users are granted the minimum level of access necessary to perform their job function, and no more.
2. **Need-to-Know** — Access to data is limited to individuals with a legitimate business requirement to view or modify it.
3. **Segregation of Duties** — Where feasible, no single individual holds end-to-end control over a critical process (e.g., financial transactions, privileged account provisioning) without independent review.
4. **Default Deny** — Access to network segments, systems, and data is denied by default and must be explicitly granted, consistent with the segmentation model defined in the architecture overview.
5. **Periodic Review** — All access rights are reviewed on a regular cycle (see Section 8) to identify and remove unnecessary or stale access.

## 4. Identity and Authentication

### 4.1 Identity Provider

DelTech operates a hybrid identity model. An on-premises Active Directory domain controller at the Portland HQ (Management VLAN) is authoritative for on-premises resource authentication, and synchronizes with **Microsoft Entra ID** via Microsoft Entra Connect. Microsoft Entra ID serves as the identity provider for cloud and SaaS resources, the Azure environment, and integrated business applications (ERP, Microsoft 365), allowing users to authenticate to both on-premises and cloud resources with a single set of credentials.

Administrative access to the on-premises domain controller is restricted to designated IT/Security administrators via the Management VLAN, consistent with Section 5.

### 4.2 Multi-Factor Authentication (MFA)

MFA is required for:

- All access to the Management VLAN
- All administrative/privileged accounts, including access to the Azure jump box
- All remote access to corporate systems from outside DelTech's on-premises network
- All access to the Microsoft Sentinel workspace and underlying Azure resources
- All Entra ID user sign-ins, enforced via conditional access policy

### 4.3 Password and Credential Requirements

- Passwords must meet DelTech's minimum complexity and length requirements as configured in Entra ID password policy.
- Shared or generic accounts are prohibited except where technically unavoidable (e.g., certain OT/ICS service accounts), in which case such accounts must be documented, access-restricted, and subject to compensating controls (e.g., enhanced logging, physical access restriction).
- Default credentials on any system, including SCADA/ICS devices and network equipment, must be changed prior to production deployment.

## 5. Network Segment Access Control

Access to each network segment is governed as follows, consistent with the segmentation model defined in the Security Architecture & Environment Overview:

| Segment | Who Has Access | Authentication Requirement |
|---|---|---|
| **Guest VLAN** | Visitors, non-corporate devices | Captive portal; no access to internal resources |
| **Corporate VLAN** | Employees and authorized contractors | Entra ID authentication; MFA for remote access |
| **Management VLAN** | IT/Security administrators, Network Engineering | MFA required; access restricted to named admin accounts |
| **SCADA/ICS Environment** | Authorized OT/Plant Engineering personnel only | Access routed through Management VLAN; MFA required; session logging enabled; no general IT staff access without documented, time-bound exception |
| **Azure VNet** | IT/Security administrators via jump box only | MFA required; no direct RDP/SSH from the internet; all sessions logged |

Cross-segment access (e.g., an IT admin needing SCADA/ICS access) requires a documented, time-bound exception approved by both the CISO and OT/Plant Engineering leadership, consistent with the Security Architecture & Environment Overview's default-deny posture for cross-VLAN-type traffic.

## 6. Privileged Access Management

- Privileged accounts (Domain Admin, Azure global admin, network device admin, SCADA/ICS engineering accounts) are limited to the minimum number of individuals necessary.
- Privileged access to the Azure environment is limited to the designated jump box; standing administrative access is not granted to routine user accounts.
- Privileged account activity is logged and forwarded to Microsoft Sentinel for monitoring and alerting.
- Use of privileged accounts for routine, non-administrative tasks (e.g., email, web browsing) is prohibited; administrators must use a separate standard account for such activity.

## 7. Access Provisioning and De-provisioning

### 7.1 Provisioning

- Access requests must be approved by the requestor's manager and, for privileged or SCADA/ICS access, by the CISO or OT/Plant Engineering leadership as applicable.
- Access is provisioned according to standard role-based templates where possible, to support consistency and ease of review.

### 7.2 De-provisioning

- Access must be revoked immediately upon termination of employment or contract.
- Access must be reviewed and adjusted promptly upon role change (e.g., transfer between departments).
- Accounts inactive for more than 90 days are disabled pending manager confirmation of continued need.

## 8. Access Review

- **Standard user access** is reviewed at least annually by department managers.
- **Privileged access** (Management VLAN, Azure admin, SCADA/ICS engineering accounts) is reviewed at least quarterly by the CISO.
- **Service and shared accounts** are reviewed at least annually, with justification documented for continued use.

Review findings, including any removed or modified access, are documented and retained consistent with DelTech's record retention requirements.

## 9. Remote Access

- Remote access to corporate systems requires MFA and is permitted only through DelTech's approved VPN client, **Cisco Secure Client** (the current, rebranded version of Cisco AnyConnect), consistent with DelTech's Cisco network infrastructure.
- Use of any VPN client other than Cisco Secure Client for remote access to DelTech systems is prohibited without prior approval from IT Security.
- Remote access to the SCADA/ICS environment is not permitted except under a documented, time-bound exception approved by the CISO and OT/Plant Engineering leadership, and must be routed through the Management VLAN with session logging enabled.

## 10. Exceptions

Exceptions to this policy must be documented, risk-assessed, and formally approved by the CISO. Exceptions are time-bound and subject to periodic review, consistent with the exception process defined in the Information Security Policy.

## 11. Compliance and Enforcement

Violations of this policy may result in disciplinary action up to and including termination of employment or contract, and may result in immediate suspension of access pending investigation.

## 12. Review and Maintenance

This policy is reviewed at least annually by the CISO, and updated as needed to reflect changes to the network architecture, identity infrastructure, or regulatory requirements.

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
