<p align="center">
  <img src="./assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# AI Acceptable Use & Governance Policy

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO)
**Version:** 1.1
**Effective Date:** July 14, 2026
**Review Cycle:** Annual, or upon significant change to approved AI tooling
**Classification:** Internal

---

## Framework Alignment

| Standard | Reference |
|---|---|
| ISO/IEC 27001:2022 | Annex A.5.10 (Acceptable Use of Information and Other Associated Assets), A.5.19–A.5.22 (Supplier/Third-Party Relationships), A.8.16 (Monitoring Activities) |
| ISO/IEC 42001:2023 | AI Management System — general principles referenced for governance of AI tool adoption and risk management |
| NIST AI Risk Management Framework (AI RMF 1.0) | GOVERN, MAP, MEASURE functions — referenced for AI-specific risk considerations |
| NIST CSF 2.0 | GOVERN (GV.PO, GV.SC — Supply Chain), PROTECT (PR.DS — Data Security) |

This policy should be read alongside the [Acceptable Use Policy](./03-acceptable-use-policy.md), which it supersedes with respect to AI tool use (Acceptable Use Policy, Section 8).

---

## 1. Purpose

This policy establishes DelTech LLC's requirements for the use of artificial intelligence (AI) tools and large language models (LLMs) by employees, contractors, and other authorized users, in order to manage data confidentiality, intellectual property, and third-party risk associated with AI tool adoption.

## 2. Scope

This policy applies to all DelTech employees, contractors, and other authorized users, and covers:

- Third-party AI applications and LLM-based tools (e.g., public generative AI chatbots, AI browser extensions, AI-powered SaaS features not natively part of DelTech's approved Microsoft 365 environment)
- Microsoft Copilot and other AI features embedded within DelTech's existing Microsoft 365 / Azure environment
- Any use of DelTech data — including confidential, proprietary, or customer data — as input to an AI tool

## 3. Policy Statement

### 3.1 Third-Party AI Applications and LLMs

Use of third-party AI applications or LLMs (including public generative AI tools not provided or sanctioned by DelTech IT) is **strictly prohibited without prior written approval from IT Security**. This includes, but is not limited to:

- Public generative AI chatbots and web-based LLM tools
- AI-powered browser extensions or plugins
- Third-party SaaS applications with embedded AI/LLM features not part of DelTech's approved technology stack
- AI coding assistants, AI note-taking tools, or AI meeting transcription tools not formally approved

No DelTech data — including confidential, proprietary, customer, or personally identifiable information — may be submitted to any unapproved third-party AI tool under any circumstances.

### 3.2 Microsoft Copilot

Microsoft Copilot, as an AI feature integrated within DelTech's existing Microsoft 365 and Azure environment, is treated differently from unapproved third-party AI tools due to its integration with DelTech's existing data governance and access controls. However, use of Microsoft Copilot is **limited to individuals who have received express approval from IT Security** prior to use.

Approved Copilot users must:

- Complete any AI-specific awareness training designated by IT Security prior to use
- Use Copilot only within its approved scope (e.g., approved Microsoft 365 applications), and not enable or use Copilot features that have not been formally reviewed and approved by IT Security
- Continue to observe DelTech's data classification and handling requirements when using Copilot-generated content, and independently verify AI-generated output before relying on it for business-critical decisions

### 3.3 Approval Process

- Requests to use Microsoft Copilot must be submitted to IT Security, specifying business justification and intended use case.
- Requests to use any third-party AI tool must be submitted to IT Security for formal risk review prior to any use, including review of the vendor's data handling, retention, and confidentiality practices.
- IT Security maintains a list of approved AI tools and approved Copilot users, reviewed at least annually.

## 4. Data Handling Requirements

- Confidential, proprietary, or customer data must never be submitted to an unapproved AI tool, consistent with Section 3.1.
- Even for approved tools (e.g., Microsoft Copilot), users must apply DelTech's data classification requirements and avoid submitting data beyond what is necessary and appropriate for the approved use case.
- AI-generated content used in business decisions, customer-facing communications, or technical documentation must be reviewed by a human for accuracy before use; AI output is not considered authoritative on its own.

## 5. SCADA/ICS and OT Environment

Use of AI tools, including Microsoft Copilot, in connection with the SCADA/ICS environment or OT engineering workstations described in the Security Architecture & Environment Overview is prohibited, consistent with the general restriction on non-essential software in that environment defined in the Acceptable Use Policy, Section 10.

## 6. Monitoring and Enforcement

- DelTech reserves the right to monitor use of AI tools on its systems and networks, consistent with the monitoring provisions of the Acceptable Use Policy.
- Use of an unapproved third-party AI tool, or use of Microsoft Copilot without express IT Security approval, is treated as a policy violation and may result in disciplinary action up to and including termination of employment or contract, consistent with the Acceptable Use Policy's enforcement provisions.
- Suspected policy violations, including submission of confidential data to an unapproved AI tool, should be reported to IT Security immediately and may be handled consistent with the Incident Response Policy depending on the nature and scope of data exposure.

## 7. Roles and Responsibilities

| Role | Responsibility |
|---|---|
| **CISO** | Owns this policy; approves the list of sanctioned AI tools and any policy exceptions |
| **IT Security Team** | Reviews and approves/denies AI tool requests and Copilot access requests; maintains the approved tool list; monitors for unauthorized AI tool use |
| **Department Managers** | Ensure staff awareness of this policy; support requests for legitimate business use cases |
| **All Employees & Contractors** | Comply with this policy; do not use unapproved AI tools or submit DelTech data to them |

## 8. Exceptions

Exceptions to this policy must be documented, risk-assessed, and formally approved by the CISO, consistent with the exception process defined in the Information Security Policy.

## 9. Review and Maintenance

This policy is reviewed at least annually, or upon significant change to DelTech's approved AI tooling or the broader AI risk landscape, and updated accordingly by the CISO.

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
