# DelTech LLC — Incident Response Playbooks

This folder contains a companion set of tactical incident response playbooks for the [DelTech LLC information security policy portfolio](../README.md). Where the root policy set establishes governance and requirements, these documents operationalize the [Incident Response Policy](../04-incident-response-policy.md) into step-by-step procedures aligned with NIST SP 800-61 Rev. 3, ISO/IEC 27001:2022 Annex A, and NIST CSF 2.0.

Each playbook assumes familiarity with the [Security Architecture & Environment Overview](../00-security-architecture-overview.md) and cross-references it rather than re-describing the environment.

## Documents

| Document | Version | Description |
|---|---|---|
| [`00-ir-playbook-framework.md`](./00-ir-playbook-framework.md) | 1.0 | Master framework: NIST 800-61 lifecycle, severity classification, roles/responsibilities, communication triggers, and the playbook index |
| [`01-phishing-bec-playbook.md`](./01-phishing-bec-playbook.md) | 1.0 | Phishing and Business Email Compromise — Defender for Office 365 and Sentinel-driven triage |
| [`02-ransomware-playbook.md`](./02-ransomware-playbook.md) | 1.0 | Ransomware — CrowdStrike Falcon containment, Veeam/Zerto recovery coordination |
| [`03-malware-endpoint-playbook.md`](./03-malware-endpoint-playbook.md) | 1.0 | General malware and endpoint compromise — Falcon/RTR-driven investigation and containment |
| [`04-ddos-playbook.md`](./04-ddos-playbook.md) | 1.0 | DDoS — Cisco SD-WAN edge and Azure DDoS Protection response |
| [`05-scada-ot-playbook.md`](./05-scada-ot-playbook.md) | 1.0 | SCADA/OT incident handling for the Westbrook manufacturing site — safety-first, joint IT/Plant Engineering authority model, aligned to NIST SP 800-82 |
| [`06-email-recall-purge-playbook.md`](./06-email-recall-purge-playbook.md) | 1.0 | Supporting procedure for the Phishing/BEC playbook — organization-wide email search and purge using Security & Compliance PowerShell |

All documents are at Version 1.0, last updated July 29, 2026.

## Design notes

- Every playbook shares the same NIST 800-61 phase structure (Preparation → Detection & Analysis → Containment/Eradication/Recovery → Post-Incident) so an analyst can move between playbooks without relearning the format.
- Detection and containment steps are written around the tools actually in use in the DelTech environment (Microsoft Sentinel/KQL, CrowdStrike Falcon, Tenable.io/Nessus, Defender for Office 365, Veeam, Zerto) rather than generic tool-agnostic language.
- The SCADA/OT playbook is intentionally the odd one out: it overrides standard IT containment authority wherever the two would conflict, reflecting that safety and physical process integrity take priority over data confidentiality, availability, or forensic completeness in an OT context.
- Each document carries its own version number, "Last Updated" date, and version history table, tracked independently from the root policy set — playbooks are expected to be revised individually as detection content and tooling evolve, whereas the policy documents tend to move in lockstep at a single version.
