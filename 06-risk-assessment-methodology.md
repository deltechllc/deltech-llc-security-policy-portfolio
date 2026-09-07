<p align="center">
  <img src="./assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Risk Assessment Methodology

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
| ISO/IEC 27001:2022 | Clause 6.1.2 (Information Security Risk Assessment), Clause 8.2 (Information Security Risk Assessment — Operational), Annex A.5.7 (Threat Intelligence) |
| NIST CSF 2.0 | IDENTIFY (ID.RA — Risk Assessment, ID.AM — Asset Management) |
| NIST SP 800-30 Rev. 1 | Guide for Conducting Risk Assessments |
| NIST SP 800-82 Rev. 3 | OT/ICS-specific risk considerations |

This methodology should be read alongside [`00-security-architecture-overview.md`](./00-security-architecture-overview.md), which provides the asset inventory and environment context risk assessments are performed against.

---

## 1. Purpose

This document defines DelTech LLC's methodology for identifying, analyzing, and evaluating information security risk across its IT, OT/SCADA, and Azure-hosted environments, providing a consistent, repeatable approach that supports the risk-based commitments established in the Information Security Policy.

## 2. Scope

This methodology applies to risk assessments covering:

- Corporate IT systems, endpoints, and the Corporate/Management/Guest VLANs
- The SCADA/ICS environment at the Manufacturing Site
- The Azure-hosted environment
- Third-party/vendor relationships with access to DelTech systems or data
- New projects, systems, or significant changes to the environment described in the Security Architecture & Environment Overview

## 3. When Risk Assessments Are Performed

- **Annual comprehensive assessment** covering the full environment.
- **Targeted assessment** triggered by:
  - Introduction of a new system, application, or network segment
  - Significant change to the network architecture (e.g., a new site, new SD-WAN policy, new Azure subnet)
  - Onboarding of a new third-party vendor with system or data access
  - Following a Medium-severity-or-above security incident, as identified in the Incident Response Policy
  - Material change to applicable regulatory or contractual obligations

## 4. Risk Assessment Process

DelTech's risk assessment process follows four stages, consistent with NIST SP 800-30 guidance: **Asset & Scope Identification → Threat and Vulnerability Identification → Risk Analysis → Risk Evaluation and Treatment.**

### 4.1 Asset and Scope Identification

- Assets in scope are identified by referencing the Security Architecture & Environment Overview (network segments, IT systems, SCADA/ICS components, Azure resources) and any asset inventory maintained by IT Security.
- Each asset is assigned a criticality rating (Low/Medium/High) based on its role in business operations, data sensitivity, and dependency relationships (e.g., SCADA/ICS systems are rated High due to production impact; Guest VLAN infrastructure is typically rated Low).

### 4.2 Threat and Vulnerability Identification

- Threats are identified using a combination of internal knowledge, vendor advisories, threat intelligence feeds, and vulnerability scan data (Tenable.io, Microsoft Sentinel).
- OT-specific threats (e.g., threats targeting ICS protocols, legacy PLC vulnerabilities) are assessed with input from OT/Plant Engineering leadership, given their specialized knowledge of the manufacturing environment.
- Identified vulnerabilities are cross-referenced with the Vulnerability & Patch Management Policy's tracking of open findings and compensating controls.

### 4.3 Risk Analysis

Risk is analyzed using a **Likelihood × Impact** model:

| Likelihood | Description |
|---|---|
| **Low** | Unlikely to occur; no known active threat or exploit targeting this asset/vulnerability |
| **Medium** | Plausible; some evidence of relevant threat activity or exploit availability |
| **High** | Likely; active exploitation observed in the wild, or a known gap directly applicable to DelTech's environment |

| Impact | Description |
|---|---|
| **Low** | Minimal business disruption; no sensitive data involved |
| **Medium** | Moderate disruption to a business function; limited data exposure |
| **High** | Significant business disruption (e.g., production downtime at the Manufacturing Site), major data breach, or safety implication |

Risk rating is calculated as Likelihood × Impact, producing a combined risk level (Low / Medium / High / Critical) used to prioritize treatment.

### 4.4 Risk Evaluation and Treatment

For each identified risk, DelTech selects one of the following treatment options:

- **Mitigate** — Apply a control to reduce likelihood or impact (e.g., patch a vulnerability, add network segmentation, deploy a compensating control per the Vulnerability & Patch Management Policy)
- **Transfer** — Shift risk via insurance, contractual terms, or a managed third-party service
- **Accept** — Formally accept the risk where the cost of treatment exceeds the benefit, with CISO approval and documented justification
- **Avoid** — Discontinue the activity or system introducing the risk

All risk treatment decisions are documented in the risk register, including the rationale, owner, and target completion date for any mitigating action.

## 5. Risk Register

DelTech maintains a risk register recording, at minimum:

- Risk description and affected asset(s)/segment(s)
- Likelihood, impact, and resulting risk rating
- Treatment decision and current status
- Risk owner
- Date identified and date of next scheduled review

The risk register is reviewed by the CISO at least quarterly, with High and Critical risks reviewed monthly until resolved or formally accepted.

## 6. OT/SCADA-Specific Considerations

Given the availability-first risk profile of the SCADA/ICS environment described in the Security Architecture & Environment Overview:

- Impact ratings for SCADA/ICS risks weigh production downtime and safety implications heavily, in addition to confidentiality/integrity concerns.
- Risk treatment for SCADA/ICS findings frequently favors compensating controls over direct remediation (e.g., patching), consistent with the approach defined in the Vulnerability & Patch Management Policy, and is documented accordingly in the risk register.
- OT/Plant Engineering leadership is a required participant in any risk assessment scoped to the SCADA/ICS environment.

## 7. Roles and Responsibilities

| Role | Responsibility |
|---|---|
| **CISO** | Owns the risk assessment methodology and risk register; approves risk acceptance decisions |
| **IT Security Team** | Conducts risk assessments for IT and Azure environments; maintains the risk register |
| **OT/Plant Engineering Leads** | Participate in and provide subject-matter input for SCADA/ICS risk assessments |
| **Executive Leadership** | Reviews High/Critical risks and approves resource allocation for treatment |

## 8. Reporting

A summary of the risk register, including newly identified risks, changes in risk rating, and treatment progress, is presented to executive leadership at least quarterly, or immediately following identification of a Critical risk.

## 9. Review and Maintenance

This methodology is reviewed at least annually by the CISO and updated to reflect changes in the environment, threat landscape, or organizational risk tolerance.

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
