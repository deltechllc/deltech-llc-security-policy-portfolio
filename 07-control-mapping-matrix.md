<p align="center">
  <img src="./assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Control Mapping Matrix

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO)
**Version:** 1.1
**Effective Date:** July 14, 2026
**Classification:** Internal
**Document Type:** Reference Document

---

## Purpose

This matrix provides a consolidated, at-a-glance view of how DelTech LLC's information security policy set maps to **ISO/IEC 27001:2022**, the **NIST Cybersecurity Framework (CSF) 2.0**, **NIST SP 800-53 Rev. 5**, and — where applicable — **NIST SP 800-30** and **NIST SP 800-82**. It is intended as a quick-reference index; the authoritative framework alignment for each topic is documented in the "Framework Alignment" table at the top of the corresponding policy.

## Matrix

| ISO/IEC 27001:2022 Reference | NIST CSF 2.0 Function/Category | NIST SP 800-53 Rev. 5 | Governing Document |
|---|---|---|---|
| Clause 5.2 (Policy), Annex A.5.1 (Policies for Information Security) | GOVERN (GV.PO), IDENTIFY (ID.GV) | PM-1, PL-1 | [Information Security Policy](./01-information-security-policy.md) |
| Annex A.5.9 (Inventory of Assets), A.8.20–A.8.22 (Network Security, Segregation, Filtering) | IDENTIFY (ID.AM), PROTECT (PR.AA, PR.DS, PR.IR) | CM-8, SC-7, AC-4 | [Security Architecture & Environment Overview](./00-security-architecture-overview.md) |
| Annex A.5.15–A.5.18 (Access Control, Access Rights), A.8.2–A.8.5 (Privileged Access, Authentication) | PROTECT (PR.AA) | AC-2, AC-3, AC-6, IA-2 | [Access Control Policy](./02-access-control-policy.md) |
| Annex A.5.10 (Acceptable Use), A.5.14 (Information Transfer), A.8.1 (User Endpoint Devices) | PROTECT (PR.AT, PR.DS) | PL-4, AC-20 | [Acceptable Use Policy](./03-acceptable-use-policy.md) |
| Annex A.5.24–A.5.28 (Information Security Incident Management) | RESPOND (RS.MA, RS.AN, RS.CO, RS.MI), RECOVER (RC.RP) | IR-4, IR-6, IR-8 | [Incident Response Policy](./04-incident-response-policy.md) |
| Annex A.8.8 (Management of Technical Vulnerabilities), A.8.32 (Change Management) | IDENTIFY (ID.RA), PROTECT (PR.IP, PR.MA) | RA-5, SI-2, CM-3 | [Vulnerability & Patch Management Policy](./05-vulnerability-patch-management-policy.md) |
| Clause 6.1.2 / 8.2 (Information Security Risk Assessment), Annex A.5.7 (Threat Intelligence) | IDENTIFY (ID.RA, ID.AM) | — (see NIST SP 800-30 Rev. 1) | [Risk Assessment Methodology](./06-risk-assessment-methodology.md) |
| Annex A.5.29–A.5.30 (Information Security During Disruption, ICT Readiness for Business Continuity) | RECOVER (RC.RP, RC.IM), PROTECT (PR.DS) | CP-2, CP-9, CP-10 | [Business Continuity & Disaster Recovery Policy](./08-business-continuity-dr-policy.md) |
| Annex A.5.10 (Acceptable Use), A.5.19–A.5.22 (Supplier/Third-Party Relationships), A.8.16 (Monitoring Activities) | GOVERN (GV.PO, GV.SC), PROTECT (PR.DS) | — (see ISO/IEC 42001:2023, NIST AI RMF 1.0) | [AI Acceptable Use & Governance Policy](./09-ai-acceptable-use-policy.md) |

## Supplementary Standards Referenced

| Standard | Applies To | Referenced In |
|---|---|---|
| NIST SP 800-61 Rev. 2 (Computer Security Incident Handling Guide) | Incident response lifecycle | Incident Response Policy |
| NIST SP 800-82 Rev. 3 (Guide to OT Security) | SCADA/ICS-specific controls | Security Architecture & Environment Overview, Access Control Policy, Incident Response Policy, Vulnerability & Patch Management Policy, Risk Assessment Methodology |
| NIST SP 800-30 Rev. 1 (Guide for Conducting Risk Assessments) | Risk assessment methodology | Risk Assessment Methodology |
| NIST SP 800-34 Rev. 1 (Contingency Planning Guide) | Business continuity and DR planning | Business Continuity & Disaster Recovery Policy |
| ISO/IEC 42001:2023 (AI Management System) | AI governance principles | AI Acceptable Use & Governance Policy |
| NIST AI Risk Management Framework (AI RMF 1.0) | AI-specific risk considerations | AI Acceptable Use & Governance Policy |

## NIST CSF 2.0 Function Coverage Summary

| Function | Covered By |
|---|---|
| **GOVERN** | Information Security Policy, AI Acceptable Use & Governance Policy |
| **IDENTIFY** | Security Architecture & Environment Overview, Vulnerability & Patch Management Policy, Risk Assessment Methodology |
| **PROTECT** | Access Control Policy, Acceptable Use Policy, Vulnerability & Patch Management Policy, Security Architecture & Environment Overview |
| **DETECT** | Incident Response Policy (via Microsoft Sentinel detection, referenced in Section 5.2), Vulnerability & Patch Management Policy (scanning) |
| **RESPOND** | Incident Response Policy |
| **RECOVER** | Incident Response Policy, Business Continuity & Disaster Recovery Policy |

## Notes on Use

- This matrix reflects the policy set as of the Effective Date above. As additional documents are added to the portfolio (e.g., Data Classification, Business Continuity/DR, Third-Party Risk Management), this matrix should be updated accordingly.
- Control references are drawn directly from the "Framework Alignment" section of each source document; if the two ever diverge due to future edits, the source document is authoritative.

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
