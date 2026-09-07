<p align="center">
  <img src="./assets/deltech-logo.png" alt="DelTech LLC" width="200">
</p>

# DelTech LLC — Information Security Policy Portfolio

## ⚠️ Disclaimer

**DelTech LLC is a fictional company.** This repository is a portfolio project created to demonstrate the ability to develop information security policy, standards, and architecture documentation aligned with **ISO/IEC 27001** and the **NIST Cybersecurity Framework (CSF)**.

All company names, network details, technology stacks, and identifying information in this repository are entirely fictional and created for demonstration purposes only. None of the documents in this repository:

- Describe a real organization, network, or environment
- Should be used as actual security guidance or a compliance template without independent legal and technical review
- Represent official guidance from ISO, NIST, or any other standards body

This project exists to showcase policy-writing, security architecture documentation, and framework mapping skills for a cybersecurity job search portfolio.

## Purpose

Cybersecurity roles increasingly require the ability to translate technical controls into clear, audience-appropriate documentation — not just implement tools. This project was built to demonstrate that skill set concretely, by producing a coherent set of security policies and reference architecture for a fictional mid-size manufacturing company, DelTech LLC.

Each document is explicitly mapped to relevant clauses/controls in ISO/IEC 27001:2022 and functions/categories in the NIST CSF 2.0 (and NIST SP 800-53 / 800-82 where relevant), so the reasoning behind each policy is traceable rather than generic.

## About DelTech LLC (Fictional)

DelTech LLC is a fictional widget manufacturer headquartered in Portland, ME, with a secondary manufacturing site in Westbrook, ME. The environment includes:

- Two physical sites connected via Cisco SD-WAN
- Standard Guest / Corporate / Management VLAN segmentation at each site
- A logically isolated SCADA/ICS environment at the manufacturing site
- An Azure-hosted environment (VPN-connected) supporting Microsoft Sentinel and a replica Active Directory domain controller, alongside an on-premises AD domain controller at Portland HQ
- Zerto-based disaster recovery for critical virtualized IT workloads, with Veeam providing scheduled VM backup and retention

Full environment details are documented in [`00-security-architecture-overview.md`](./00-security-architecture-overview.md), which serves as the canonical reference for all other documents in this repository.

## Repository Contents

| Document | Description |
|---|---|
| [`00-security-architecture-overview.md`](./00-security-architecture-overview.md) | Reference architecture: network topology, IT/OT environment, security tool stack, data flows |
| [`01-information-security-policy.md`](./01-information-security-policy.md) | Top-level Information Security Policy governing the ISMS |
| [`02-access-control-policy.md`](./02-access-control-policy.md) | Access control requirements across VLANs, SCADA/ICS, and the Azure environment |
| [`03-acceptable-use-policy.md`](./03-acceptable-use-policy.md) | Acceptable and prohibited use of DelTech systems, devices, and data |
| [`04-incident-response-policy.md`](./04-incident-response-policy.md) | Incident detection, response, and recovery procedures across IT, OT/SCADA, and Azure environments |
| [`05-vulnerability-patch-management-policy.md`](./05-vulnerability-patch-management-policy.md) | Vulnerability scanning and patch management, differentiated across IT, SCADA/ICS, and Azure environments |
| [`06-risk-assessment-methodology.md`](./06-risk-assessment-methodology.md) | Risk assessment process, risk register, and treatment methodology |
| [`07-control-mapping-matrix.md`](./07-control-mapping-matrix.md) | Consolidated ISO 27001 / NIST CSF / NIST 800-53 control mapping across all policies |
| [`08-business-continuity-dr-policy.md`](./08-business-continuity-dr-policy.md) | Business continuity and disaster recovery, including Zerto-based replication and failover, and SCADA/ICS continuity considerations |
| [`09-ai-acceptable-use-policy.md`](./09-ai-acceptable-use-policy.md) | Governance of AI tool and LLM use, including third-party AI approval requirements and restricted Microsoft Copilot access |
| [`LICENSE.md`](./LICENSE.md) | CC BY 4.0 License governing reuse of this repository's content |

## Framework Alignment

Each document includes a framework alignment table mapping its content to:

- **ISO/IEC 27001:2022** — relevant clauses and Annex A controls
- **NIST Cybersecurity Framework 2.0** — relevant Functions/Categories
- **NIST SP 800-53 Rev. 5** — relevant control families, where applicable
- **NIST SP 800-82 Rev. 3** — OT/ICS-specific guidance, where applicable

## About This Project

This portfolio was created by John Delea as part of a cybersecurity job search, alongside a companion hands-on lab project demonstrating detection engineering with Microsoft Sentinel/KQL. Together, they're intended to show both the policy/governance and technical detection sides of security work.

Feedback and questions are welcome — feel free to reach out via [LinkedIn](https://www.linkedin.com/in/john-delea-645ab096).

## License

This project is licensed under [CC BY 4.0](./LICENSE.md) — you're welcome to reuse, adapt, or build on the structure and content of this repository, including commercially, as long as you provide attribution. Please don't represent it as documentation for a real organization.
