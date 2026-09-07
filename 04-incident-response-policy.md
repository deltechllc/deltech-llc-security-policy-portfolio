<p align="center">
  <img src="./assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Incident Response Policy

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO)
**Version:** 1.1
**Effective Date:** July 14, 2026
**Review Cycle:** Annual, or upon significant organizational/technical change, or following a major incident
**Classification:** Internal — Restricted Distribution

---

## Framework Alignment

| Standard | Reference |
|---|---|
| ISO/IEC 27001:2022 | Annex A.5.24–A.5.28 (Information Security Incident Management) |
| NIST CSF 2.0 | RESPOND (RS.MA, RS.AN, RS.CO, RS.MI), RECOVER (RC.RP) |
| NIST SP 800-53 Rev. 5 | IR-4 (Incident Handling), IR-6 (Incident Reporting), IR-8 (Incident Response Plan) |
| NIST SP 800-61 Rev. 2 | Computer Security Incident Handling Guide |
| NIST SP 800-82 Rev. 3 | OT/ICS-specific incident response considerations |

This policy should be read alongside [`00-security-architecture-overview.md`](./00-security-architecture-overview.md) and the [Access Control Policy](./02-access-control-policy.md).

---

## 1. Purpose

This policy establishes DelTech LLC's approach to detecting, responding to, and recovering from information security incidents affecting its IT, OT/SCADA, and Azure-hosted environments, minimizing business impact and supporting continual improvement through post-incident review.

## 2. Scope

This policy applies to any suspected or confirmed information security incident affecting:

- Corporate IT systems, endpoints, and the Corporate/Management VLANs at either site
- The SCADA/ICS environment at the Manufacturing Site
- The Azure-hosted environment (replica domain controller, jump box, Sentinel infrastructure)
- The ERP system, Microsoft 365, and other business applications
- Data handled by DelTech, regardless of where it resides

## 3. Definitions

- **Security Event:** Any observed occurrence in a system or network indicating a possible security-relevant condition.
- **Security Incident:** A confirmed or strongly suspected violation of security policy, unauthorized access, or compromise of confidentiality, integrity, or availability of DelTech systems or data.
- **Major Incident:** An incident with significant business impact, such as production downtime at the Manufacturing Site, confirmed data breach, ransomware, or compromise of privileged accounts.

## 4. Incident Response Team (IRT)

| Role | Responsibility |
|---|---|
| **CISO** | Overall incident response authority; declares Major Incidents; approves external communication |
| **IT Security Team / SOC Analyst function** | Triage, investigation, containment; primary users of Microsoft Sentinel and CrowdStrike Falcon during response |
| **Network Engineering Team** | Executes network-level containment (VLAN isolation, SD-WAN policy changes, Azure NSG updates) |
| **OT/Plant Engineering Leads** | Coordinate any response actions affecting the SCADA/ICS environment; assess production impact |
| **IT Leadership** | Coordinates business continuity and communication with affected departments |
| **Legal/HR (as needed)** | Advises on regulatory notification obligations and personnel matters |

## 5. Incident Response Lifecycle

DelTech's incident response process follows the NIST SP 800-61 lifecycle: **Preparation → Detection & Analysis → Containment, Eradication & Recovery → Post-Incident Activity.**

### 5.1 Preparation

- Microsoft Sentinel serves as the primary detection and alerting platform, ingesting telemetry from CrowdStrike Falcon, Entra ID, network devices, and — where feasible — SCADA/ICS monitoring infrastructure (see architecture overview, Section 6).
- The IRT maintains up-to-date contact information, escalation paths, and access to response tooling independent of potentially compromised systems (e.g., out-of-band communication method).
- Tabletop exercises are conducted at least annually, including at least one scenario involving the SCADA/ICS environment given its distinct operational risk profile.

### 5.2 Detection and Analysis

- Security events are triaged based on alerts from Microsoft Sentinel, CrowdStrike Falcon, and manual reports from staff (see Acceptable Use Policy, Section 3.4 on reporting).
- Analysts assess scope, affected systems/segments, and likely severity, referencing the network segmentation model to determine which VLANs, the SCADA/ICS environment, or the Azure environment may be involved.
- Incidents are classified by severity (Low / Medium / High / Major) to determine response urgency and required escalation.

### 5.3 Containment, Eradication, and Recovery

- **Containment** actions may include isolating affected endpoints via CrowdStrike Falcon, restricting or disabling compromised accounts in Entra ID, adjusting Cisco SD-WAN policies to isolate a VLAN or site, or restricting Azure NSG rules.
- **SCADA/ICS-specific containment:** Given the availability-first risk profile of the manufacturing environment, any containment action affecting SCADA/ICS systems (e.g., network isolation, device shutdown) must be coordinated with OT/Plant Engineering leadership before execution, balancing security containment against production impact. Where full isolation would halt production, compensating controls (enhanced monitoring, restricted access) may be used as an interim measure pending CISO and OT leadership joint approval for further action.
- **Eradication** involves removing the root cause (malware, unauthorized access, misconfiguration) and validating systems are clean before restoration.
- **Recovery** involves restoring affected systems to normal operation, with monitoring for recurrence, and validating data integrity, particularly for ERP and production data flows.

### 5.4 Post-Incident Activity

- A post-incident review is conducted for all Medium-severity incidents and above, documenting root cause, response timeline, and lessons learned.
- Identified control gaps are tracked to remediation, and this policy or related documents (Access Control, Vulnerability & Patch Management) are updated as needed.
- Major Incidents require a formal after-action report presented to executive leadership.

## 6. Incident Classification and Response Time Targets

### 6.1 Classification Criteria

Incident severity is determined using a combination of factors, consistent with the functional impact, information impact, and recoverability categories defined in **NIST SP 800-61 Rev. 2**, and the impact-assessment approach required for evaluating information security events under **ISO/IEC 27001:2022 Annex A.5.25** (Assessment and Decision on Information Security Events):

| Factor | Description |
|---|---|
| **Functional Impact** | The degree to which the incident affects DelTech's ability to operate — ranging from no impact on business functions to a full loss of a critical service (e.g., production downtime at the Manufacturing Site) |
| **Information Impact** | Whether confidentiality, integrity, or availability of data has been compromised, and the sensitivity of the data involved (e.g., general business data vs. customer/proprietary data) |
| **Scope** | The number and criticality of systems, network segments, or sites affected (e.g., a single endpoint vs. an entire VLAN, or IT vs. SCADA/ICS) |
| **Recoverability** | The effort, time, and resources required to restore affected systems to normal operation, and whether the root cause can be readily contained |

These factors are assessed together, rather than any single factor automatically determining severity — for example, an incident with limited scope but high information impact (e.g., a small-scale but confirmed exposure of customer data) may still be classified as High or Major.

### 6.2 Severity Levels and Response Time Targets

| Severity | Functional Impact | Information Impact | Example | Initial Response Target |
|---|---|---|---|---|
| **Low** | None to minimal; no disruption to business operations | None; no confirmed compromise of confidentiality, integrity, or availability | Isolated phishing attempt, no compromise confirmed | Within 1 business day |
| **Medium** | Limited; a single system or small user group affected, contained without broader disruption | Limited; confirmed compromise restricted to a single low-to-medium-sensitivity system | Malware detected and contained on a single endpoint | Within 4 hours |
| **High** | Significant; a network segment or multiple systems affected, or a privileged account compromised | Significant; confirmed unauthorized access to systems handling sensitive business data | Confirmed unauthorized access to Corporate or Management VLAN | Within 1 hour |
| **Major** | Severe; loss of a critical business function, production downtime, or organization-wide impact | Severe; confirmed breach of sensitive/regulated data, or loss of data integrity with safety implications | Suspected compromise of SCADA/ICS environment, ransomware, confirmed data breach | Immediate (on-call escalation) |

Severity classification is reassessed as an incident investigation progresses; an incident may be re-classified to a higher or lower severity as functional impact, information impact, scope, and recoverability become clearer.

## 7. Reporting Requirements

- All DelTech staff must report suspected security incidents immediately to the IT Security Team, consistent with the Acceptable Use Policy.
- The CISO is responsible for determining whether an incident triggers external notification obligations (e.g., regulatory, contractual, customer notification), in coordination with Legal.
- Incidents involving the SCADA/ICS environment are reported to OT/Plant Engineering leadership in parallel with the IT Security Team, given the potential production impact.

## 8. Communication

- Internal communication during a Major Incident is coordinated by the CISO to ensure consistent, accurate information and to avoid premature or unauthorized disclosure.
- External communication (customers, regulators, media) is approved by the CISO and Legal/executive leadership before release.

## 9. Exceptions

Exceptions to this policy must be documented, risk-assessed, and formally approved by the CISO, consistent with the exception process defined in the Information Security Policy.

## 10. Review and Maintenance

This policy is reviewed at least annually, following any Major Incident, and updated to reflect lessons learned, changes to the environment, or evolving regulatory requirements.

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
