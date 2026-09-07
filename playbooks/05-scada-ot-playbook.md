<p align="center">
  <img src="../assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Playbook: SCADA / OT Incident (Westbrook Manufacturing Site)

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO), jointly with Westbrook Plant Engineering
**Version:** 1.0
**Effective Date:** July 29, 2026
**Last Updated:** July 29, 2026
**Review Cycle:** Annual, or after any SCADA/OT incident or tabletop exercise
**Classification:** Internal

---

## Framework Alignment

| Standard | Reference |
|---|---|
| NIST SP 800-82 Rev. 3 | Guide to Operational Technology (OT) Security |
| NIST SP 800-61 Rev. 3 | Incident handling lifecycle (adapted for OT safety constraints) |
| ISO/IEC 27001:2022 | Annex A.5.26 (Response), A.8.22 (Segregation of Networks) |
| NIST CSF 2.0 | RESPOND (RS.MI), GOVERN (GV.OC — Organizational Context, including safety) |
| MITRE ATT&CK for ICS | T0816 (Device Restart/Shutdown), T0836 (Modify Parameter), T0800 (Activate Firmware Update Mode) |

This playbook inherits the roles, severity model, and lifecycle structure from the [Incident Response Playbook Framework](./00-ir-playbook-framework.md), but **overrides standard IT containment procedure wherever the two conflict.** Where any other playbook in this set would normally direct an IT action against a SCADA/ICS-adjacent system, this playbook's authority structure takes precedence.

---

## 1. Scope and Governing Principle

Covers any suspected or confirmed cyber security incident touching the logically isolated SCADA/ICS environment at the Westbrook manufacturing site, or the IT/OT boundary that separates it from the corporate network.

**Governing principle: safety and physical process integrity take precedence over data confidentiality, system availability, and forensic completeness, in that order.** No IT action — isolation, patching, host restart, forensic collection — is taken against an OT/ICS-adjacent system without explicit approval from the OT/Plant Engineering Lead. A wrong containment action on a live industrial process can cause physical harm; a wrong or delayed IT containment action generally cannot. This asymmetry is the reason this playbook exists as its own document rather than a section of the general playbooks.

---

## 2. Detection Sources

- **OT network monitoring** at the IT/OT boundary — anomalous traffic crossing the segmentation point between the corporate network and the SCADA/ICS environment (this boundary should see effectively zero unplanned traffic given the logical isolation design).
- **Microsoft Sentinel** — ingesting IT/OT boundary firewall and switch logs; any east-west traffic attempt from the corporate VLANs toward the ICS segment outside of an approved maintenance window is a high-priority alert.
- **CrowdStrike Falcon** — deployed only on IT-managed systems at the IT/OT boundary (jump hosts, engineering workstations with dual connectivity), never on OT/ICS controllers or HMIs themselves, which typically cannot support standard EDR agents.
- **Plant engineering observation** — unexpected physical process behavior, HMI anomalies, or unplanned control system alarms reported by operators, which may be the *first* indication of a cyber-physical event before any IT telemetry shows anything.

---

## 3. Initial Severity Guidance

Any confirmed or credibly suspected incident touching the SCADA/ICS environment is a **minimum SEV-2**, per the framework, regardless of apparent scope — the potential safety and production impact outweighs normal severity scoping logic. It is escalated to **SEV-1** immediately if there is any indication of unauthorized control-system parameter changes, unexplained equipment behavior, or if the corporate/IT side of the intrusion has confirmed access toward the ICS boundary.

---

## 4. Response Procedure

### 4.1 Preparation
- Maintain the logical isolation between the SCADA/ICS environment and the corporate VLANs (Guest/Corporate/Management) as the primary preventive control — this playbook assumes that boundary is intact going in.
- Maintain a joint IT Security / Plant Engineering contact list and confirm it during each annual tabletop exercise.
- Ensure the OT/Plant Engineering Lead and their designated alternate are always reachable — this playbook cannot proceed past initial triage without that role's sign-off.

### 4.2 Detection and Analysis
1. **Immediately notify the OT/Plant Engineering Lead** — this happens in parallel with, not after, initial IT triage. This is the one deviation from the standard framework's escalation timing: for this playbook, OT notification is simultaneous with detection, not gated behind severity confirmation.
2. IT Security analyzes activity strictly on the IT side of the boundary (jump hosts, engineering workstations, boundary firewall/switch logs) using Sentinel and Falcon as with any other playbook.
3. **IT Security does not access, query, or take any action on OT/ICS controllers, HMIs, or the isolated ICS network segment itself.** Any investigation on that side of the boundary is performed by, or under the direct supervision of, Plant Engineering, potentially with IT Security providing remote guidance only.
4. Jointly assess: is this an IT-side event that was stopped at the boundary (most likely outcome given the isolation design), or is there evidence of actual boundary crossing?

### 4.3 Containment
**Short-term:**
- If the event is confirmed contained to the IT side of the boundary: apply standard IT containment (network isolation via Falcon, account disablement) per the [Malware/Endpoint](./03-malware-endpoint-playbook.md) or [Ransomware](./02-ransomware-playbook.md) playbook as appropriate — no OT approval needed for actions that stay entirely on the IT side.
- If there is any credible evidence of boundary crossing: **the OT/Plant Engineering Lead determines the containment approach for the OT side**, which may include manual/physical isolation of specific control system components, and IT Security supports only as directed.
- IT Security may recommend severing the IT/OT boundary connection entirely as a precaution while assessment continues — this recommendation is made to the OT/Plant Engineering Lead, who authorizes it considering active production/safety implications of doing so.

**Long-term:**
- Any change to the IT/OT segmentation architecture itself (firewall rules, allowed protocols at the boundary) is a joint IT Security/Plant Engineering decision, documented as a change to the [Security Architecture & Environment Overview](../00-security-architecture-overview.md).

### 4.4 Eradication
- On the IT side: standard eradication as in other playbooks.
- On the OT side: eradication actions (firmware verification, controller reset, HMI rebuild) are performed exclusively by Plant Engineering or vendor field service, following the equipment manufacturer's procedures — not by IT Security.

### 4.5 Recovery
- The OT/Plant Engineering Lead determines when it is safe to restore any severed IT/OT connectivity or resume normal control system operation — this is a safety sign-off, not a technical "all clear" from IT Security alone.
- IT Security confirms the IT side of the boundary is clean (Falcon, Tenable scan of boundary-adjacent systems) before that connectivity is restored.

### 4.6 Post-Incident
- Joint IT Security/Plant Engineering lessons-learned session, given the cross-functional nature of any real event here.
- Reassess the IT/OT boundary architecture and monitoring coverage in light of what was learned.
- If the incident had any potential safety implication, Plant Engineering leads any required internal safety reporting in parallel with the security post-incident review.

---

## 5. Communication Triggers

- OT/Plant Engineering Lead notified simultaneously with initial detection, not after severity confirmation (see 4.2).
- Executive Sponsor and Legal/Compliance notified immediately given the minimum-SEV-2 default, per the framework.
- If there is any actual or suspected safety impact, plant safety reporting obligations run in parallel with the security incident process, led by Plant Engineering, and are not subordinate to it.
- Consider CISA/ICS-CERT reporting for confirmed ICS-targeting activity, per Legal/Compliance guidance — this is a decision for Legal/Compliance and the Executive Sponsor, not a default technical action.

---

## 6. Quick-Reference Checklist

- [ ] Notify OT/Plant Engineering Lead **immediately and in parallel** with initial detection
- [ ] IT Security investigates only the IT side of the boundary
- [ ] No IT action on OT/ICS systems without explicit Plant Engineering approval
- [ ] Jointly assess whether boundary crossing occurred
- [ ] OT/Plant Engineering Lead directs any OT-side containment
- [ ] IT Security may recommend, but Plant Engineering authorizes, severing the IT/OT link
- [ ] Recovery requires Plant Engineering safety sign-off, not just a technical clean bill
- [ ] Joint post-incident review; reassess boundary architecture

---

## Version History

| Version | Date | Summary |
|---|---|---|
| 1.0 | July 29, 2026 | Initial publication |

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
