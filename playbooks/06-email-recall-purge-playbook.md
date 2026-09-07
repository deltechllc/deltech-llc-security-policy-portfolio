<p align="center">
  <img src="../assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Playbook: Malicious Email Recall & Purge (Exchange Online)

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO)
**Version:** 1.0
**Effective Date:** July 29, 2026
**Last Updated:** July 29, 2026
**Review Cycle:** Annual, or after any incident where this procedure is exercised
**Classification:** Internal

---

## Framework Alignment

| Standard | Reference |
|---|---|
| NIST SP 800-61 Rev. 3 | Containment (removing attacker-delivered artifacts from the environment) |
| ISO/IEC 27001:2022 | Annex A.5.26 (Response), A.8.23 (Web Filtering) |
| NIST CSF 2.0 | RESPOND (RS.MI — Mitigation) |
| MITRE ATT&CK | T1566 (Phishing), T1566.001/.002 (Spearphishing Attachment/Link) |

This playbook inherits the roles, severity model, and lifecycle structure from the [Incident Response Playbook Framework](./00-ir-playbook-framework.md). It is a **supporting procedure** for the [Phishing / Business Email Compromise playbook](./01-phishing-bec-playbook.md) — use this document for the specific mechanics of locating and removing a malicious message from every mailbox it reached, once that playbook's analysis steps have identified the message to act on.

---

## 1. Scope

Covers organization-wide search and removal of a malicious email from Exchange Online mailboxes at DelTech (single Microsoft 365 tenant covering both Portland HQ and Westbrook staff). Applies whether the message was flagged by Defender for Office 365, reported by a user, or identified during incident investigation after the fact — including cases where the message has already been read, forwarded, or moved to another folder by the recipient.

This is written around the **Microsoft Purview / Security & Compliance PowerShell** method rather than the Defender for Office 365 Threat Explorer GUI "Remove email" button, because the PowerShell method gives precise control over the search query, a mandatory preview-before-purge step, and an auditable record of exactly what was searched and removed — all of which matter for post-incident documentation and, if it ever comes to it, for defending the action in front of Legal/Compliance or an auditor.

---

## 2. Prerequisites

- **Permissions:** the executing analyst's account must be a member of the **eDiscovery Manager** role group (or hold the **Search and Purge** role directly) in the Microsoft Purview compliance portal. This is a privileged, sensitive permission — per the [Access Control Policy](../02-access-control-policy.md), it should be assigned only to designated incident responders, not held broadly.
- **Module:** the **Exchange Online Management** PowerShell module (`ExchangeOnlineManagement`), which provides `Connect-IPPSSession` for connecting to Security & Compliance PowerShell.
- **Identifying information** for the message to remove: sender address, subject line, and ideally the `NetworkMessageId` or `InternetMessageId` pulled from Defender for Office 365 Threat Explorer during the analysis phase of the Phishing/BEC playbook — the more specific the identifier, the less risk of an overly broad search catching unrelated legitimate mail.

---

## 3. Procedure

### 3.1 Connect to Security & Compliance PowerShell

```powershell
Install-Module -Name ExchangeOnlineManagement -Scope CurrentUser
Import-Module ExchangeOnlineManagement
Connect-IPPSSession -UserPrincipalName analyst@deltech.com
```

`Connect-IPPSSession` (not `Connect-ExchangeOnline`) is what's needed here — it connects to the Security & Compliance endpoint where compliance search and purge cmdlets live, rather than the standard Exchange Online management endpoint.

### 3.2 Create a Compliance Search

Build the search as narrowly as the available identifiers allow. Prefer matching on `InternetMessageId` when it's available (from the message headers or Threat Explorer) — it uniquely identifies the message and avoids any ambiguity:

```powershell
New-ComplianceSearch -Name "IR-2026-0729-Phishing-Purge" `
  -ExchangeLocation All `
  -ContentMatchQuery '(Subject:"Urgent: Invoice Payment Required") AND (From:"billing@malicious-domain.example")'

Start-ComplianceSearch -Identity "IR-2026-0729-Phishing-Purge"
```

If you have the `InternetMessageId`, use it instead for a precise match:

```powershell
New-ComplianceSearch -Name "IR-2026-0729-Phishing-Purge" `
  -ExchangeLocation All `
  -ContentMatchQuery 'InternetMessageId:"<abc123@malicious-domain.example>"'
```

Naming convention: `IR-<incident-date>-<short-description>-Purge`, matching the incident's case folder name for traceability, per the framework's evidence-handling guidance.

### 3.3 Review Results Before Purging — Do Not Skip This Step

```powershell
Get-ComplianceSearch -Identity "IR-2026-0729-Phishing-Purge" | Format-List
```

Check the `Items` count and, if the number is higher or lower than expected given how many recipients were identified during analysis, stop and refine the `-ContentMatchQuery` before proceeding. A search that's broader than intended (e.g., matching on subject line alone, catching a legitimate email that happens to share wording) will purge mail that shouldn't be touched. **This preview step is the main safeguard against an overly broad purge — treat it as mandatory, not optional, regardless of time pressure.**

### 3.4 Execute the Purge

DelTech's default is **`SoftDelete`**, which moves the message to each recipient's Recoverable Items folder rather than permanently destroying it — this preserves the ability to reverse the action or produce the message later if Legal/Compliance needs it, while still removing it from the user's visible mailbox.

```powershell
New-ComplianceSearchAction -SearchName "IR-2026-0729-Phishing-Purge" -Purge -PurgeType SoftDelete
```

`HardDelete` (permanent, unrecoverable removal) is reserved for cases where the Incident Commander has explicitly authorized it — for example, a message containing highly sensitive exfiltrated data that must not remain recoverable anywhere. Do not use `HardDelete` as a default.

```powershell
# Only with explicit Incident Commander authorization:
New-ComplianceSearchAction -SearchName "IR-2026-0729-Phishing-Purge" -Purge -PurgeType HardDelete
```

### 3.5 Confirm Completion

```powershell
Get-ComplianceSearchAction -Identity "IR-2026-0729-Phishing-Purge_Purge" | Format-List Status,Results
```

The `Results` property lists a per-mailbox success/failure count. Investigate and re-run for any mailbox reported as failed — a failure usually means the mailbox was inaccessible at execution time (e.g., a litigation hold conflict or a temporarily unlicensed account) rather than that the message wasn't there.

---

## 4. Rollback / Recovery of a Mistaken Purge

Because DelTech's default is `SoftDelete`, a mistaken or overly broad purge is generally recoverable within the mailbox's deleted-item retention window:

- Affected users can recover the message themselves from **Recoverable Items → Deletions** (Outlook's "Recover Deleted Items" feature) if the retention window hasn't expired.
- If self-service recovery isn't sufficient at scale, an administrator can use `New-MailboxSearch` or a targeted compliance search scoped to `Recoverable Items` to locate and restore the purged message(s) to the original mailbox.
- There is no equivalent rollback for `HardDelete` — this is the core reason its use requires explicit Incident Commander sign-off rather than being an analyst's default choice.

---

## 5. Post-Action Documentation

Record in the incident case file: the exact `ContentMatchQuery` used, the compliance search name, the item count previewed before purge, the purge type (`SoftDelete`/`HardDelete`) and authorization for it if `HardDelete`, and the final per-mailbox results. This is the auditable record referenced in Section 1 and satisfies the evidence-handling expectations in the [Incident Response Playbook Framework](./00-ir-playbook-framework.md).

---

## 6. Quick-Reference Checklist

- [ ] Confirm eDiscovery Manager / Search and Purge permissions before starting
- [ ] Gather the most specific identifier available (`InternetMessageId` preferred over subject/sender alone)
- [ ] `Connect-IPPSSession`
- [ ] `New-ComplianceSearch` with a narrow `-ContentMatchQuery`, then `Start-ComplianceSearch`
- [ ] **Preview item count with `Get-ComplianceSearch` before purging — do not skip**
- [ ] `New-ComplianceSearchAction -Purge -PurgeType SoftDelete` (default) — `HardDelete` only with Incident Commander authorization
- [ ] Confirm per-mailbox results with `Get-ComplianceSearchAction`
- [ ] Document query, item count, purge type, and results in the incident case file

---

## Version History

| Version | Date | Summary |
|---|---|---|
| 1.0 | July 29, 2026 | Initial publication |

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
