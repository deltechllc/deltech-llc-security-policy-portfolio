<p align="center">
  <img src="../assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Incident Response Playbook Framework

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO)
**Version:** 1.0
**Effective Date:** July 29, 2026
**Last Updated:** July 29, 2026
**Review Cycle:** Annual, or after any SEV-1/SEV-2 incident or tabletop exercise
**Classification:** Internal

---

## Framework Alignment

| Standard | Reference |
|---|---|
| NIST SP 800-61 Rev. 3 | Incident Response Recommendations and Considerations (full lifecycle) |
| ISO/IEC 27001:2022 | Annex A.5.24 (Incident Management Planning & Preparation), A.5.25 (Assessment & Decision), A.5.26 (Response), A.5.27 (Learning from Incidents), A.5.28 (Evidence Collection) |
| NIST CSF 2.0 | RESPOND (RS.MA, RS.AN, RS.CO, RS.MI) and RECOVER (RC.RP, RC.CO) |
| NIST SP 800-53 Rev. 5 | IR-1 through IR-8 (Incident Response family) |

This document operationalizes [`04-incident-response-policy.md`](../04-incident-response-policy.md), which establishes the *policy* mandate for incident response. This playbook set defines the *procedures* — who does what, in what order, using which tools — during an actual event. It should be read alongside [`00-security-architecture-overview.md`](../00-security-architecture-overview.md) for environment context (sites, network segmentation, identity model, and security tooling referenced throughout).

This framework document applies to every playbook in this folder. Individual playbooks (phishing/BEC, ransomware, malware/endpoint, DDoS, SCADA/OT) inherit the roles, severity model, and lifecycle structure defined here and layer on incident-specific detection queries, containment steps, and communication triggers.

---

## 1. Purpose and Scope

This framework exists so that incident response at DelTech is executed consistently regardless of which analyst is on call, what time of day an incident is detected, or which system is affected. It covers cyber security incidents affecting DelTech's IT environment (Portland HQ, Westbrook manufacturing site, and the Azure VNet), and defines the boundary conditions under which OT/SCADA-specific handling (see [`05-scada-ot-playbook.md`](./05-scada-ot-playbook.md)) takes precedence over standard IT procedure.

Out of scope: physical security incidents without a cyber component, and HR/employee-relations matters that do not involve a system or data compromise (these are handled under separate HR policy).

---

## 2. Incident Response Lifecycle (NIST SP 800-61)

Every playbook in this set is structured around the same four phases. This shared structure is deliberate — it means an analyst who is fluent in the ransomware playbook can pick up the DDoS playbook mid-incident and immediately know where they are in the process.

### 2.1 Preparation
Readiness activities performed *before* an incident: maintaining detection content (Sentinel analytic rules, CrowdStrike Falcon prevention policies, Tenable scan schedules), keeping this playbook set current, running tabletop exercises, and ensuring the on-call rotation and escalation contacts are accurate.

### 2.2 Detection and Analysis
Identifying that an incident is occurring, scoping it, and assigning initial severity. Primary detection sources across the environment are Microsoft Sentinel (correlated alerts across both sites and the Azure VNet), CrowdStrike Falcon (endpoint detections across Portland, Westbrook, and Azure-hosted hosts), Tenable.io/Nessus (vulnerability context that informs likelihood and blast radius), Defender for Office 365 (email-borne threats), and Cisco SD-WAN/firewall logs (network-layer anomalies).

### 2.3 Containment, Eradication, and Recovery
Limiting damage (containment), removing the threat actor's foothold (eradication), and restoring normal operations (recovery). Containment is deliberately split into **short-term** (stop the bleeding — isolate a host, block a sender, drop malicious traffic) and **long-term** (sustainable fix while eradication/recovery work continues, e.g., temporary segmentation or credential resets) in each playbook.

### 2.4 Post-Incident Activity
Lessons-learned review, updating detection content and playbooks based on what was learned, and closing out any regulatory or contractual notification obligations.

---

## 3. Incident Severity Classification

Severity is assigned at initial triage and re-assessed as the incident evolves. Severity drives who is notified, how quickly, and whether the Incident Commander role is activated.

| Severity | Definition | Examples | Initial Response Time |
|---|---|---|---|
| **SEV-1 — Critical** | Confirmed compromise with active business impact, safety impact, or large-scale data exposure | Ransomware encryption in progress, confirmed SCADA/ICS network intrusion, confirmed exfiltration of customer/employee PII | Immediate (Incident Commander activated within 30 minutes) |
| **SEV-2 — High** | Confirmed compromise, contained or containable, limited scope | Single endpoint compromise with lateral movement attempts, successful BEC resulting in fraudulent wire attempt | Within 1 hour |
| **SEV-3 — Moderate** | Suspicious activity requiring investigation, no confirmed compromise | Phishing click without credential entry, isolated malware detection blocked by Falcon prevention | Within 4 business hours |
| **SEV-4 — Low** | Anomaly or policy violation with no indication of compromise | Single failed login anomaly, low-confidence Sentinel alert closed as benign | Next business day |

Any incident touching the Westbrook SCADA/ICS environment is treated as a **minimum SEV-2** regardless of apparent scope, per the SCADA/OT playbook.

---

## 4. Roles and Responsibilities

| Role | Responsibility | Typically Filled By |
|---|---|---|
| **Incident Commander (IC)** | Owns the incident end-to-end for SEV-1/SEV-2 events: decision authority on containment actions, coordinates workstreams, is the single point of truth for status | CISO or delegated senior security analyst |
| **Security Analyst (on-call)** | First responder; performs triage in Sentinel/Falcon/Tenable, executes containment actions, documents the timeline | SOC/security analyst on rotation |
| **IT Operations** | Executes infrastructure-level actions (network isolation, DNS/firewall changes, restore from Veeam/Zerto) at the direction of the IC | IT Ops/network engineering |
| **OT/Plant Engineering Lead** | Sole authority on any action affecting the Westbrook SCADA/ICS environment; must approve before IT touches OT-adjacent systems | Westbrook plant engineering manager |
| **Communications Lead** | Manages internal stakeholder updates and, if activated, external/regulatory communication drafting | Designated by CISO, typically with Legal input |
| **Legal/Compliance** | Determines breach notification obligations (state law, contractual, regulatory) and privilege considerations | General Counsel or outside counsel |
| **Executive Sponsor** | Informed at SEV-1/SEV-2; makes business-impact decisions (e.g., paying a ransom, disclosure timing) that exceed IC authority | CEO or designated executive |

For SEV-3/SEV-4 incidents, the on-call Security Analyst typically handles the incident solo and only escalates to the IC if scope grows.

---

## 5. Communication and Escalation

| Trigger | Notify | Timing |
|---|---|---|
| Any SEV-1 declared | IC, Executive Sponsor, Legal/Compliance, IT Ops leadership | Immediately |
| Any SEV-2 declared | IC, IT Ops leadership | Within 1 hour |
| Confirmed or suspected exposure of employee/customer personal data | Legal/Compliance | Immediately — Maine's breach notification statute (10 M.R.S. § 1348) and applicable state laws where affected individuals reside impose notification deadlines that Legal must track from the moment exposure is suspected |
| Incident touches Westbrook SCADA/ICS | OT/Plant Engineering Lead | Immediately, before any containment action is taken on OT-adjacent systems |
| Incident with potential law enforcement / regulatory reporting interest (e.g., ransomware, suspected nation-state activity) | Legal/Compliance, Executive Sponsor | Before any external communication or ransom-related action |

All communication decisions and legal notification determinations are made by Legal/Compliance and the Executive Sponsor — this playbook set governs the technical response, not the disclosure decision.

---

## 6. Playbook Index

| Playbook | Primary Trigger Sources | Companion Document |
|---|---|---|
| [Phishing / Business Email Compromise](./01-phishing-bec-playbook.md) | Defender for Office 365, user reports, Sentinel `EmailEvents` | — |
| [Ransomware](./02-ransomware-playbook.md) | CrowdStrike Falcon, Sentinel, Veeam/Zerto backup alerts | Business Continuity & Disaster Recovery Policy |
| [Malware / Endpoint Compromise](./03-malware-endpoint-playbook.md) | CrowdStrike Falcon, Sentinel, Tenable | — |
| [DDoS](./04-ddos-playbook.md) | Cisco SD-WAN, Azure DDoS Protection, Sentinel | — |
| [SCADA / OT Incident](./05-scada-ot-playbook.md) | OT network monitoring, Sentinel, Falcon (IT/OT boundary only) | Business Continuity & Disaster Recovery Policy |
| [Malicious Email Recall & Purge](./06-email-recall-purge-playbook.md) | Supporting procedure — invoked from the Phishing/BEC playbook | Phishing / Business Email Compromise playbook |

---

## 7. Evidence Handling and Chain of Custody

All analysts follow baseline evidence-handling discipline regardless of incident type: preserve original artifacts (memory captures, disk images, log exports) before remediation where feasible, record who collected what and when, and store evidence in the designated restricted-access case folder. This satisfies ISO/IEC 27001:2022 Annex A.5.28 and supports any downstream legal or regulatory process.

---

## 8. Playbook Maintenance

Each playbook is reviewed annually and after every SEV-1/SEV-2 incident of that type, incorporating lessons learned. Tabletop exercises are run at least twice per year, rotating through incident types, with at least one exercise per year specifically covering the SCADA/OT playbook given the safety stakes at Westbrook.

---

## 9. Review and Maintenance

This framework is reviewed at least annually by the CISO, and updated as needed to reflect changes to the network architecture, identity infrastructure, security tooling, or regulatory requirements.

---

## Version History

| Version | Date | Summary |
|---|---|---|
| 1.0 | July 29, 2026 | Initial publication |

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
