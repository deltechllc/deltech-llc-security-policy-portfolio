<p align="center">
  <img src="./assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Business Continuity & Disaster Recovery Policy

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO)
**Version:** 1.1
**Effective Date:** July 14, 2026
**Review Cycle:** Annual, or upon significant organizational/technical change, or following a DR activation
**Classification:** Internal — Restricted Distribution

---

## Framework Alignment

| Standard | Reference |
|---|---|
| ISO/IEC 27001:2022 | Annex A.5.29–A.5.30 (Information Security During Disruption, ICT Readiness for Business Continuity) |
| NIST CSF 2.0 | RECOVER (RC.RP — Recovery Planning, RC.IM — Improvements), PROTECT (PR.DS) |
| NIST SP 800-53 Rev. 5 | CP-2 (Contingency Plan), CP-9 (System Backup), CP-10 (System Recovery and Reconstitution) |
| NIST SP 800-34 Rev. 1 | Contingency Planning Guide for Federal Information Systems |
| NIST SP 800-82 Rev. 3 | OT/ICS-specific continuity and recovery considerations |

This policy should be read alongside [`00-security-architecture-overview.md`](./00-security-architecture-overview.md) and the [Incident Response Policy](./04-incident-response-policy.md), which governs the security-incident-driven trigger for many DR activations.

---

## 1. Purpose

This policy establishes DelTech LLC's approach to business continuity planning and disaster recovery (DR), ensuring that critical IT systems, the Azure-hosted environment, and — where technically supported — OT-adjacent business systems can be restored within defined timeframes following a disruptive event, minimizing impact to manufacturing operations and business functions.

## 2. Scope

This policy applies to:

- Corporate IT systems, servers, and applications across both sites
- The Azure-hosted environment (Azure replica domain controller, jump box, Sentinel infrastructure)
- The ERP system and other business-critical applications
- Business processes and departments dependent on these systems

The SCADA/ICS environment at the Manufacturing Site is addressed separately in Section 8, given its distinct recovery considerations (physical equipment, safety implications, and vendor-specific recovery procedures that fall outside standard IT DR tooling).

## 3. Business Impact Analysis (BIA)

DelTech maintains a Business Impact Analysis identifying, for each critical system and business process:

- **Recovery Time Objective (RTO):** the maximum acceptable time to restore the system/process following a disruption
- **Recovery Point Objective (RPO):** the maximum acceptable amount of data loss, measured in time

| System/Process | Criticality | RTO | RPO |
|---|---|---|---|
| ERP System | High | 4 hours | 15 minutes |
| Active Directory Domain Controller (on-prem and Azure replica) | High | 4 hours | 15 minutes |
| Email/Microsoft 365 | Medium | 8 hours | N/A (SaaS, Microsoft-managed) |
| File/Application Servers (Corporate VLAN) | Medium | 8 hours | 1 hour |
| Azure Jump Box | Medium | 8 hours | 1 hour |
| Guest VLAN Infrastructure | Low | 5 business days | N/A |

RTO/RPO targets are reviewed as part of the annual BIA update and adjusted based on changes to business criticality or system architecture.

## 4. Disaster Recovery Approach

### 4.1 Replication and Recovery Platform: Zerto

DelTech uses **Zerto** as its primary disaster recovery platform for continuous data protection and orchestrated failover of virtualized IT workloads, including:

- On-premises domain controller virtualized infrastructure (Portland HQ)
- ERP application and database servers
- File/application servers at the Corporate VLAN
- The Azure-hosted replica domain controller and jump box (via Zerto's Azure-integrated replication capability)

Zerto provides continuous, journal-based replication rather than traditional periodic backups for in-scope workloads, supporting the aggressive RPO targets (as low as 15 minutes) defined in Section 3 for the most critical systems. Zerto is used specifically for **failover** — restoring service availability quickly after an infrastructure-level disruption — and is distinct from the longer-term retention backup function described in Section 4.2.

### 4.2 Backup Platform: Veeam

DelTech uses **Veeam Backup & Replication** as its primary VM backup platform, providing scheduled, image-level backups and longer-term retention for virtualized IT workloads across both sites and the Azure environment. Veeam serves a complementary role to Zerto:

- **Zerto** provides near-continuous replication and rapid failover for the most critical, low-RPO workloads (Section 4.1), oriented toward minimizing downtime during an active disruption.
- **Veeam** provides scheduled backups with defined retention periods, supporting point-in-time restore, long-term archival, and recovery from scenarios where a specific historical restore point (rather than near-real-time failover) is required — for example, restoring an individual file, VM, or database to a state from several days or weeks prior.

Veeam backups are stored on-premises with replication to a secondary location for redundancy, and cover the full virtualized server estate, including systems not in scope for Zerto replication. Backup jobs are scheduled at a frequency appropriate to each system's criticality (daily for High/Medium-criticality systems, weekly for Low-criticality systems), with retention periods defined per DelTech's data retention requirements.

Backup integrity is validated through periodic test restores, performed at least quarterly for High-criticality systems.

### 4.3 Replication Topology

- Primary production workloads at the Portland HQ are replicated via Zerto to a secondary recovery target. Where feasible, this includes cross-site replication to the Manufacturing Site's IT infrastructure and/or replication into the Azure environment, providing geographic diversity consistent with the site-to-site VPN Gateway connectivity described in the Security Architecture & Environment Overview.
- Zerto's journal-based recovery allows point-in-time recovery within a defined checkpoint window, supporting recovery from both infrastructure failure and logical corruption events (e.g., ransomware), not solely full-site outages.
- Failover and failover testing are orchestrated through Zerto's recovery plans, which define boot order, network mapping, and validation steps for in-scope systems.

### 4.4 Backup Coverage for Non-Zerto-Protected Systems

Systems not in scope for Zerto replication (e.g., lower-criticality file shares, Guest VLAN infrastructure) are protected through Veeam's standard scheduled backup processes described in Section 4.2, with backup job success validated on a regular schedule by the IT Security Team.

## 5. Disaster Recovery Testing

- **Zerto failover tests** are conducted at least semi-annually using Zerto's non-disruptive test failover capability, which validates recoverability without impacting production systems.
- **Veeam restore tests** are conducted at least quarterly for High-criticality systems, as described in Section 4.2, to confirm backup integrity and restorability.
- **Full DR exercises**, including a coordinated tabletop exercise involving IT, OT/Plant Engineering, and business stakeholders, are conducted at least annually.
- Test results, including actual recovery time achieved versus RTO targets, are documented and reviewed by the CISO, with any gaps tracked to remediation.

## 6. Activation and Roles

| Role | Responsibility |
|---|---|
| **CISO** | Declares a formal DR activation; approves failover to recovery environment |
| **IT Security Team** | Executes Zerto failover/recovery plans; validates system integrity post-recovery |
| **Network Engineering Team** | Adjusts network configuration (VLAN, SD-WAN, Azure NSG) as needed to support failover connectivity |
| **OT/Plant Engineering Leads** | Coordinate any business continuity actions affecting manufacturing operations |
| **IT Leadership** | Communicates status to affected business units and executive leadership during activation |

DR activation may be triggered independently or in conjunction with the Incident Response Policy, where a security incident (e.g., ransomware) is the cause of the disruption.

## 7. Communication During Disruption

- Internal stakeholders are notified promptly upon DR activation, with regular status updates until services are restored.
- Customer-facing communication, where warranted by the scope or duration of a disruption, is approved by the CISO and executive leadership before release, consistent with the communication approach defined in the Incident Response Policy.

## 8. SCADA/ICS Continuity Considerations

The SCADA/ICS environment at the Manufacturing Site is not managed through Zerto or standard IT DR tooling, given its reliance on physical production equipment and specialized control systems. Continuity planning for this environment instead relies on:

- Vendor-supported recovery procedures for PLCs, HMIs, and other ICS components
- Documented manual/fallback operating procedures where feasible, maintained by OT/Plant Engineering
- Configuration backups for ICS devices, stored securely and tested periodically for restorability
- Close coordination with OT/Plant Engineering leadership during any event affecting production systems, consistent with the incident coordination approach defined in the Incident Response Policy

Recovery time for SCADA/ICS disruptions is highly dependent on the nature of the failure (e.g., a controller replacement versus a software-level issue) and is addressed on a case-by-case basis rather than a fixed RTO, though DelTech aims to minimize production downtime as a top priority in any such event.

## 9. Exceptions

Exceptions to this policy must be documented, risk-assessed, and formally approved by the CISO, consistent with the exception process defined in the Information Security Policy.

## 10. Review and Maintenance

This policy, along with the associated Business Impact Analysis and Zerto recovery plans, is reviewed at least annually, following any DR activation or major test, and updated to reflect changes in business criticality, system architecture, or the technology environment.

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
