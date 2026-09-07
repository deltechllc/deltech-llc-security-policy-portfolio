<p align="center">
  <img src="./assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Acceptable Use Policy

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
| ISO/IEC 27001:2022 | Annex A.5.10 (Acceptable Use of Information and Other Associated Assets), A.5.14 (Information Transfer), A.8.1 (User Endpoint Devices) |
| NIST CSF 2.0 | PROTECT (PR.AT — Awareness and Training, PR.DS — Data Security) |
| NIST SP 800-53 Rev. 5 | PL-4 (Rules of Behavior), AC-20 (Use of External Systems) |

This policy should be read alongside [`00-security-architecture-overview.md`](./00-security-architecture-overview.md), the [Access Control Policy](./02-access-control-policy.md), and the [AI Acceptable Use & Governance Policy](./09-ai-acceptable-use-policy.md).

---

## 1. Purpose

This policy defines acceptable and unacceptable use of DelTech LLC's information systems, devices, network, and data by employees, contractors, and other authorized users, in order to protect DelTech's assets, reputation, and legal standing.

## 2. Scope

This policy applies to all individuals granted access to DelTech systems, including:

- Corporate-issued laptops, workstations, and mobile devices
- The Corporate and Management VLANs at either site
- Email, Microsoft 365, and other collaboration platforms
- The ERP system and other business applications
- The Azure-hosted environment
- Personal devices used to access DelTech resources, where permitted

This policy does not apply to the Guest VLAN, which is governed by separate visitor network terms displayed at the captive portal.

## 3. General Acceptable Use

Users are expected to:

1. Use DelTech systems and data primarily for legitimate business purposes.
2. Protect their credentials and never share passwords, MFA codes, or access tokens with anyone, including coworkers or IT staff.
3. Lock or log off workstations when unattended.
4. Report suspected security incidents, phishing attempts, or policy violations promptly to the IT Security Team, consistent with the Incident Response Policy.
5. Apply security updates and patches promptly when prompted by managed endpoint tooling (Microsoft Intune), and not disable or circumvent endpoint security agents (CrowdStrike Falcon, Intune compliance policies).
6. Use only approved software and cloud services for business purposes; unauthorized software installation on corporate endpoints is prohibited without IT Security approval.

## 4. Limited Personal Use

Limited, incidental personal use of DelTech systems (e.g., checking personal email during a break) is permitted provided it does not:

- Interfere with job performance
- Consume significant network or storage resources
- Violate any other provision of this policy
- Involve illegal activity or inappropriate content

DelTech reserves the right to restrict or revoke personal use privileges at its discretion.

## 5. Prohibited Activities

The following activities are strictly prohibited on DelTech systems and networks:

- Accessing, storing, or transmitting illegal, discriminatory, harassing, or offensive content
- Attempting to access systems, data, or network segments beyond one's authorized scope (including attempting to access the SCADA/ICS environment or Management VLAN without authorization)
- Installing unauthorized software, browser extensions, or remote access tools on corporate endpoints
- Connecting personal or unauthorized removable media (USB drives, external storage) to corporate endpoints or, especially, any SCADA/ICS-connected equipment, without prior IT Security approval
- Bypassing or disabling security controls, including endpoint protection, network segmentation, or MFA
- Sharing confidential company information, credentials, or access tokens with unauthorized individuals
- Using DelTech systems to engage in unauthorized cryptocurrency mining, torrenting, or similar resource-intensive or legally risky activity
- Connecting unauthorized personal devices to the Corporate or Management VLANs

## 6. Email and Collaboration Tools

- Users must exercise caution with email attachments and links, particularly from unknown or unexpected senders, and report suspected phishing to IT Security.
- Company email and Microsoft 365 accounts should be used for business communication; forwarding company email to personal accounts is prohibited without documented business justification and IT Security approval.
- Sensitive or confidential information should only be shared via approved channels, consistent with data classification and handling requirements.

## 7. Mobile and Remote Devices

- Corporate-issued mobile devices and laptops must be enrolled in Microsoft Intune and maintain compliance with configured security baselines (encryption, screen lock, patch level).
- Remote access to corporate systems must use DelTech's approved VPN client, Cisco Secure Client (formerly AnyConnect), consistent with the Access Control Policy; use of other VPN clients or unauthorized remote access tools is prohibited.
- Lost or stolen devices must be reported to IT Security immediately so the device can be remotely locked or wiped.
- Use of personal devices to access DelTech email or business applications is permitted only through approved, managed configurations (e.g., Intune mobile application management).

## 8. Use of AI Tools and External Services

Use of AI tools and large language models (LLMs), including Microsoft Copilot and any third-party AI applications, is governed by the dedicated [AI Acceptable Use & Governance Policy](./09-ai-acceptable-use-policy.md). In summary:

- Use of third-party AI applications or LLMs is strictly prohibited without prior written approval from IT Security.
- Use of Microsoft Copilot is limited to individuals who have received express approval from IT Security.
- Confidential, proprietary, or customer data must never be submitted to an unapproved AI tool.

See the AI Acceptable Use & Governance Policy for full requirements.

## 9. Monitoring

DelTech reserves the right to monitor use of its systems and networks, including email, endpoint activity, and network traffic, for security and operational purposes, consistent with applicable law. Users should have no expectation of privacy when using DelTech systems for any purpose, including limited personal use.

## 10. OT/SCADA-Specific Provisions

Given the operational sensitivity of the SCADA/ICS environment described in the Security Architecture & Environment Overview:

- Only authorized OT/Plant Engineering personnel may access SCADA/ICS systems, consistent with the Access Control Policy.
- Removable media use on any SCADA/ICS-connected system requires prior approval and, where used, must be scanned on an isolated system before connection.
- No general-purpose internet browsing, email, or non-essential software is permitted on SCADA/ICS engineering workstations.

## 11. Compliance and Enforcement

Violations of this policy may result in disciplinary action up to and including termination of employment or contract, and may involve legal action where warranted. Suspected violations should be reported to the IT Security Team or via DelTech's confidential reporting channel.

## 12. Exceptions

Exceptions to this policy must be documented, risk-assessed, and formally approved by the CISO, consistent with the exception process defined in the Information Security Policy.

## 13. Review and Maintenance

This policy is reviewed at least annually by the CISO and updated as needed to reflect changes in technology, business practices, or the threat landscape.

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
