<p align="center">
  <img src="../assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Playbook: Phishing & Business Email Compromise (BEC)

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO)
**Version:** 1.0
**Effective Date:** July 29, 2026
**Last Updated:** July 29, 2026
**Review Cycle:** Annual, or after any SEV-1/SEV-2 phishing/BEC incident
**Classification:** Internal

---

## Framework Alignment

| Standard | Reference |
|---|---|
| NIST SP 800-61 Rev. 3 | Incident handling lifecycle (Preparation, Detection & Analysis, Containment/Eradication/Recovery, Post-Incident) |
| ISO/IEC 27001:2022 | Annex A.5.25 (Assessment & Decision), A.5.26 (Response), A.8.23 (Web Filtering) |
| NIST CSF 2.0 | RESPOND (RS.AN, RS.MI), PROTECT (PR.AT) |
| MITRE ATT&CK | T1566 (Phishing), T1566.002 (Spearphishing Link), T1078 (Valid Accounts) — for BEC that leads to account takeover |

This playbook inherits the roles, severity model, and lifecycle structure from the [Incident Response Playbook Framework](./00-ir-playbook-framework.md). Read that document first if you haven't already.

---

## 1. Scope

Covers phishing emails (credential harvesting, malicious attachments, malicious links) and Business Email Compromise (fraudulent wire/payment requests, vendor invoice fraud, or an attacker operating from a compromised or spoofed mailbox). Applies to both Portland HQ and Westbrook staff, since email is a single tenant across both sites (Microsoft 365 / Entra ID).

---

## 2. Detection Sources

- **Defender for Office 365** — Safe Links/Safe Attachments detonation results, anti-phishing policy verdicts, quarantine notifications.
- **User reports** — the "Report Phishing" button (Outlook add-in) is the highest-volume and often fastest detection source; treat every user report as worth triaging even if most turn out benign.
- **Microsoft Sentinel** — `EmailEvents` and `EmailUrlInfo` tables (via the Microsoft 365 Defender connector), plus the built-in analytic rule for mass-forwarding or suspicious inbox rule creation (a common BEC persistence technique).
- **Entra ID sign-in logs** — impossible-travel or unfamiliar-sign-in-properties alerts that follow a phishing click, indicating credential use.

---

## 3. Initial Severity Guidance

| Situation | Initial Severity |
|---|---|
| Phishing email reported/quarantined, no evidence of click | SEV-4 |
| User clicked link or opened attachment, no credentials entered, EDR shows no execution | SEV-3 |
| Credentials entered on a phishing page | SEV-2 (escalate to SEV-1 if the account has privileged/finance access) |
| Confirmed BEC — attacker sending from or through a compromised mailbox, especially with a fraudulent payment request in progress | SEV-1 |

---

## 4. Response Procedure

### 4.1 Preparation
- Maintain Defender for Office 365 anti-phishing, anti-spoofing (SPF/DKIM/DMARC enforcement), and Safe Links/Safe Attachments policies.
- Keep the Sentinel analytic rule for suspicious inbox rules (forwarding to external domains, auto-delete rules) enabled and tuned.
- Run periodic phishing simulation/awareness training per the Acceptable Use Policy.

### 4.2 Detection and Analysis
1. Pull the full message headers and body from the Defender for Office 365 Explorer (Threat Explorer) or the user-submitted report.
2. In Sentinel, query for other recipients of the same message and any subsequent activity from those accounts:
   ```kql
   EmailEvents
   | where NetworkMessageId == "<message-id>"
   | project Timestamp, RecipientEmailAddress, Subject, SenderFromAddress, ThreatTypes
   ```
3. If a link was clicked, check Entra ID sign-in logs for that user in the following window for anomalous sign-in properties (new device, new location, atypical travel):
   ```kql
   SigninLogs
   | where UserPrincipalName == "<user-upn>"
   | where TimeGenerated > ago(24h)
   | project TimeGenerated, AppDisplayName, IPAddress, Location, ConditionalAccessStatus, RiskLevelDuringSignIn
   ```
4. For suspected BEC, check for newly created or modified inbox rules — a classic persistence mechanism attackers use to hide their tracks (auto-forwarding, auto-deleting replies):
   ```kql
   OfficeActivity
   | where OfficeWorkload == "Exchange"
   | where Operation in ("New-InboxRule", "Set-InboxRule")
   | where TimeGenerated > ago(7d)
   ```
5. If a malicious attachment executed on an endpoint, pivot to CrowdStrike Falcon and follow the [Malware / Endpoint Compromise playbook](./03-malware-endpoint-playbook.md) from this point forward.

### 4.3 Containment
**Short-term:**
- Purge the malicious message from all mailboxes it was delivered to. For a handful of mailboxes, Defender for Office 365 Threat Explorer's "Soft delete" / "Hard delete" action is fastest. For organization-wide or precisely-scoped removal — and for an auditable record of exactly what was searched and removed — use the [Malicious Email Recall & Purge playbook](./06-email-recall-purge-playbook.md).
- Block the sending domain/address at the mail gateway.
- If credentials were entered: force a password reset and revoke all active sessions for the affected account in Entra ID (`Revoke-AzureADUserAllRefreshToken` or the Entra admin center "Revoke sessions" action).
- If a fraudulent payment is in progress or was completed: notify Finance/Accounts Payable **immediately** — for wire fraud, minutes matter for any chance of a bank recall.
- Remove any malicious inbox rules discovered during analysis.

**Long-term:**
- Require MFA re-registration for the affected account.
- Review and, if warranted, tighten Conditional Access policies for external mail-forwarding and legacy authentication protocols (which bypass MFA).

### 4.4 Eradication
- Confirm no persistence mechanism remains (inbox rules, OAuth app grants — check Entra ID "Enterprise applications" for any suspicious app consented to by the user around the time of compromise).
- Revoke any OAuth tokens/app consents granted without IT Security approval.

### 4.5 Recovery
- Restore normal mail flow; confirm the user can authenticate normally with MFA.
- If a payment was fraudulently redirected, Finance/Legal lead recovery efforts (bank recall request, insurance claim) — outside this playbook's technical scope.

### 4.6 Post-Incident
- Log the incident, including the lure type, delivery method, and how detection occurred (user report vs. automated).
- If the message bypassed Defender for Office 365 filtering, submit it to Microsoft as a false negative and tune anti-phishing policies.
- Feed the sender domain/IOCs into Sentinel watchlists and CrowdStrike Falcon custom IOC management for organization-wide blocking.

---

## 5. Communication Triggers

- **Confirmed BEC with attempted or completed fraudulent payment** — notify Finance leadership and Legal/Compliance immediately per the framework's escalation table.
- **Compromised account with access to customer or employee PII** — notify Legal/Compliance to begin the breach-notification assessment.

---

## 6. Quick-Reference Checklist

- [ ] Pull headers/body, identify all recipients
- [ ] Purge message from all mailboxes
- [ ] Block sender domain at gateway
- [ ] Check sign-in logs for credential misuse
- [ ] Check for malicious inbox rules / OAuth grants
- [ ] Reset password + revoke sessions if credentials entered
- [ ] Notify Finance immediately if payment fraud is in progress
- [ ] Submit IOCs to Sentinel watchlist / Falcon custom IOCs
- [ ] Document and close

---

## Version History

| Version | Date | Summary |
|---|---|---|
| 1.0 | July 29, 2026 | Initial publication |

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
