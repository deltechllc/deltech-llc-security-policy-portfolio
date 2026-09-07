<p align="center">
  <img src="../assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Playbook: Ransomware

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO)
**Version:** 1.0
**Effective Date:** July 29, 2026
**Last Updated:** July 29, 2026
**Review Cycle:** Annual, or after any ransomware incident or related tabletop exercise
**Classification:** Internal

---

## Framework Alignment

| Standard | Reference |
|---|---|
| NIST SP 800-61 Rev. 3 | Incident handling lifecycle |
| ISO/IEC 27001:2022 | Annex A.5.26 (Response), A.5.29 (Information Security During Disruption), A.5.30 (ICT Readiness for Business Continuity) |
| NIST CSF 2.0 | RESPOND (RS.MI), RECOVER (RC.RP) |
| MITRE ATT&CK | T1486 (Data Encrypted for Impact), T1490 (Inhibit System Recovery), T1489 (Service Stop) |

This playbook inherits the roles, severity model, and lifecycle structure from the [Incident Response Playbook Framework](./00-ir-playbook-framework.md), and works in tandem with the [Business Continuity & Disaster Recovery Policy](../08-business-continuity-disaster-recovery-policy.md) for restore procedures via Veeam (VM backup) and Zerto (DR replication).

---

## 1. Scope

Covers ransomware affecting servers, workstations, or the Azure VNet at either site. Ransomware affecting or originating near the Westbrook SCADA/ICS boundary triggers the [SCADA/OT playbook](./05-scada-ot-playbook.md) in parallel — IT and OT/Plant Engineering coordinate, but OT retains sole authority over ICS-adjacent systems.

---

## 2. Detection Sources

- **CrowdStrike Falcon** — ransomware behavioral prevention alerts, mass file-modification/rename detections, shadow-copy deletion attempts (`vssadmin delete shadows`, a near-universal ransomware precursor).
- **Microsoft Sentinel** — correlated alerts across endpoint, identity, and network signals; a spike in file-modification events combined with anomalous authentication is a strong early indicator.
- **Veeam / Zerto** — backup job failures or unexpected changes to backup repositories can indicate an attacker attempting to disable recovery capability before detonating ransomware — treat backup infrastructure alerts as a leading indicator, not just an operational nuisance.
- **User/help desk reports** — ransom notes appearing on desktops or shared drives, files becoming inaccessible.

---

## 3. Initial Severity Guidance

Ransomware is presumed **SEV-1** the moment encryption activity or a credible precursor (mass shadow-copy deletion, backup-repository tampering) is confirmed on any system — even a single endpoint — because of the propagation speed and business-continuity implications. Severity is only stepped down after the Incident Commander confirms the activity was isolated and contained.

---

## 4. Response Procedure

### 4.1 Preparation
- Maintain CrowdStrike Falcon prevention policies with ransomware-specific behavioral protections enabled (not detection-only).
- Verify Veeam backup immutability/air-gap settings and Zerto replication health on a recurring schedule — backups an attacker can reach and delete are not backups.
- Maintain an offline, current copy of this playbook and key contact information — assume email/chat may be unavailable during a live incident.

### 4.2 Detection and Analysis
1. In CrowdStrike Falcon, identify patient zero and scope: pull the process tree for the triggering detection and identify parent process, command line, and any lateral movement (RDP, PsExec, WMI) from that host.
2. In Sentinel, correlate the affected host(s) against sign-in activity to identify whether a compromised account (rather than just a compromised endpoint) is driving the spread:
   ```kql
   DeviceProcessEvents
   | where DeviceName in ("<affected-hosts>")
   | where Timestamp > ago(48h)
   | where FileName in~ ("vssadmin.exe","wbadmin.exe","bcdedit.exe")
   | project Timestamp, DeviceName, AccountName, ProcessCommandLine
   ```
3. Identify scope of encryption: which hosts, which shares, whether backup repositories were touched.
4. Check Veeam/Zerto logs for any deletion, disabling, or configuration changes to backup jobs or replication in the preceding 30 days — attackers often disable backups days before detonating.

### 4.3 Containment
**Short-term (within minutes of confirmation):**
- Network-isolate every affected host immediately via CrowdStrike Falcon's "Network Contain" action — this keeps the host reachable to Falcon for investigation while cutting it off from the rest of the network.
- Disable the compromised account(s) in Entra ID and revoke active sessions.
- If backup infrastructure integrity is in question, disconnect Veeam/Zerto repositories from the network path an attacker could reach, preserving whatever backup generations remain intact.
- Do **not** power off affected hosts if memory forensics may be needed — network isolation, not shutdown, is the default first action.

**Long-term:**
- Segment-level isolation (VLAN ACL changes on the Cisco SD-WAN fabric) if containment can't keep pace with spread across multiple hosts.
- Force password resets organization-wide if a privileged/domain account was confirmed compromised.

### 4.4 Eradication
- Rebuild affected hosts from known-good images rather than attempting to "clean" a ransomware-encrypted or attacker-controlled host.
- Confirm the initial access vector (phishing, exposed RDP, exploited vulnerability — cross-reference Tenable scan history for the affected hosts) and close it before reconnecting anything to the network.
- Rotate any credentials that were present on or accessible from compromised hosts, including service accounts.

### 4.5 Recovery
- Restore from Veeam (VM backup) or Zerto (DR replication) per the Business Continuity & Disaster Recovery Policy, using the most recent backup confirmed to predate the intrusion.
- Bring systems back online in stages (domain controllers and identity infrastructure first, then core business systems, then general workstations), validating each stage with CrowdStrike Falcon and a targeted Tenable scan before proceeding to the next.
- Do not pay a ransom or engage with threat actors without explicit Executive Sponsor and Legal/Compliance authorization — this decision sits above the Incident Commander's authority.

### 4.6 Post-Incident
- Full timeline reconstruction: initial access → lateral movement → encryption trigger → containment.
- Update Falcon prevention policies and Sentinel analytic rules based on the specific TTPs observed.
- Review whether backup immutability settings need strengthening.
- Conduct a lessons-learned session within 10 business days.

---

## 5. Communication Triggers

- Declare SEV-1 immediately per the framework — Executive Sponsor, Legal/Compliance, and IT Ops leadership are notified without delay.
- If customer or employee PII resided on affected systems, Legal/Compliance begins the breach-notification assessment in parallel with containment (this does not wait for eradication to complete).
- Any consideration of ransom payment goes to Executive Sponsor and Legal/Compliance only — never a unilateral technical decision.
- Consider law enforcement notification (FBI IC3 / local field office) per Legal/Compliance guidance — ransomware is frequently linked to organized criminal activity.

---

## 6. Quick-Reference Checklist

- [ ] Declare SEV-1 on first confirmed sign of encryption/precursor activity
- [ ] Network-isolate affected hosts via Falcon (do not power off)
- [ ] Disable compromised accounts, revoke sessions
- [ ] Verify backup repository integrity (Veeam/Zerto)
- [ ] Identify initial access vector before rebuilding
- [ ] Rebuild from known-good images — do not attempt to clean encrypted hosts
- [ ] Restore in stages, validating each with Falcon + Tenable before proceeding
- [ ] No ransom decision without Executive Sponsor + Legal sign-off
- [ ] Post-incident timeline and detection tuning

---

## Version History

| Version | Date | Summary |
|---|---|---|
| 1.0 | July 29, 2026 | Initial publication |

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
