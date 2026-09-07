<p align="center">
  <img src="./assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Information Security Policy

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
| ISO/IEC 27001:2022 | Clause 5.2 (Policy), Annex A.5.1 (Policies for Information Security) |
| NIST CSF 2.0 | GOVERN (GV.PO), IDENTIFY (ID.GV) |
| NIST SP 800-53 Rev. 5 | PM-1 (Information Security Program Plan), PL-1 (Policy and Procedures) |

---

## 1. Purpose

This policy establishes DelTech LLC's (DelTech) commitment to protecting the confidentiality, integrity, and availability of its information assets, including corporate IT systems, enterprise resource planning (ERP) platforms, and the operational technology (OT) / SCADA environment supporting widget manufacturing operations at the company's manufacturing site.

It defines the framework under which all subordinate security policies, standards, and procedures are developed, approved, and maintained.

## 2. Scope

This policy applies to:

- All DelTech employees, contractors, temporary staff, and third parties with access to DelTech systems, networks, facilities, or data
- All information assets owned, leased, or operated by DelTech, including corporate IT infrastructure, cloud services, enterprise resource planning (ERP) systems, the Azure-hosted environment, and the OT/ICS/SCADA environment supporting manufacturing operations
- All network segments across both DelTech sites, including the Guest, Corporate, and Management VLANs present at each site, the logically separate SCADA/ICS environment at the manufacturing site, and the Azure Virtual Network connected via site-to-site VPN Gateway
- All locations where DelTech conducts business, including the corporate headquarters (Portland, ME) and the secondary manufacturing site, connected via a Cisco SD-WAN implementation

## 3. Policy Statement

DelTech is committed to protecting information assets from threats, whether internal or external, deliberate or accidental, to ensure business continuity, minimize business risk, and maintain the trust of employees, customers, and partners.

DelTech will:

1. Establish an Information Security Management System (ISMS) aligned with ISO/IEC 27001, applying a risk-based approach to identifying, assessing, and treating information security risks.
2. Define roles, responsibilities, and accountability for information security at all levels of the organization.
3. Ensure all subordinate policies (Access Control, Incident Response, Acceptable Use, Vulnerability & Patch Management, Risk Assessment, etc.) are developed in support of this policy and reviewed on a regular cycle.
4. Provide appropriate resources, training, and awareness programs to support the ISMS.
5. Address the unique risk profile of converged IT/OT environments, recognizing that the SCADA/ICS environment may require compensating controls where traditional IT controls (e.g., frequent patching) are not operationally feasible, and maintain logical separation between the SCADA/ICS environment and the Guest, Corporate, and Management VLANs at each site.
6. Maintain defined network segmentation across the Guest, Corporate, and Management VLANs at both sites and the SD-WAN links connecting them, with access controls appropriate to the trust level of each segment.
7. Comply with applicable legal, regulatory, and contractual obligations related to information security and data protection.
8. Monitor, measure, and continually improve the ISMS through internal audits, management review, and corrective action processes.

## 4. Roles and Responsibilities

| Role | Responsibility |
|---|---|
| **Board / Executive Leadership** | Ultimate accountability for information security governance; approves this policy and allocates resources |
| **Chief Information Security Officer (CISO)** | Owns the ISMS; develops and maintains security policy; reports risk posture to leadership |
| **IT Security Team** | Implements and monitors technical controls; manages security tooling (SIEM, EDR, vulnerability management) |
| **OT/Plant Engineering Leads** | Coordinate security requirements for the SCADA/ICS environment with IT Security; manage compensating controls for legacy OT equipment; maintain logical separation from IT VLANs |
| **Network Engineering Team** | Manages VLAN segmentation (Guest, Corporate, Management) and the Cisco SD-WAN implementation connecting both sites; enforces inter-VLAN access controls |
| **Department Managers** | Ensure staff compliance with security policies within their teams |
| **All Employees & Contractors** | Comply with security policies; report suspected incidents promptly |
| **Internal Audit** | Independently verifies ISMS effectiveness and compliance |

## 5. Risk Management Approach

DelTech employs a risk-based approach to information security, consistent with ISO/IEC 27001 Clause 6.1 and NIST CSF IDENTIFY function. Risk assessments are conducted at least annually and upon significant changes to the IT/OT environment (e.g., new plant systems, mergers, major vendor changes). See the **Risk Assessment Methodology** document for detailed procedures.

## 6. Policy Framework

This policy sits at the top of DelTech's information security documentation hierarchy. The following subordinate policies operationalize this policy's commitments:

- Access Control Policy
- Acceptable Use Policy
- Incident Response Policy
- Vulnerability & Patch Management Policy
- Risk Assessment Methodology

Where a conflict exists between this policy and a subordinate document, this policy takes precedence.

## 7. Compliance and Enforcement

Violations of this policy or subordinate security policies may result in disciplinary action up to and including termination of employment or contract, consistent with DelTech's HR policies and applicable law. Suspected violations should be reported to the IT Security Team or via DelTech's confidential reporting channel.

## 8. Exceptions

Exceptions to this policy must be documented, risk-assessed, and formally approved by the CISO. Exceptions are time-bound and subject to periodic review.

## 9. Review and Maintenance

This policy is reviewed at least annually by the CISO and updated as needed to reflect changes in the threat landscape, business operations, regulatory requirements, or lessons learned from incidents and audits.

---

*This document is part of DelTech's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
