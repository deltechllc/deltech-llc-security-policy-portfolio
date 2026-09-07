<p align="center">
  <img src="../assets/deltech-logo.png" alt="DelTech LLC" width="220">
</p>

# Playbook: Distributed Denial of Service (DDoS)

**DelTech LLC**
**Document Owner:** Chief Information Security Officer (CISO)
**Version:** 1.0
**Effective Date:** July 29, 2026
**Last Updated:** July 29, 2026
**Review Cycle:** Annual, or after any SEV-1/SEV-2 DDoS event
**Classification:** Internal

---

## Framework Alignment

| Standard | Reference |
|---|---|
| NIST SP 800-61 Rev. 3 | Incident handling lifecycle |
| ISO/IEC 27001:2022 | Annex A.5.26 (Response), A.8.14 (Redundancy of Information Processing Facilities) |
| NIST CSF 2.0 | RESPOND (RS.MI), PROTECT (PR.IR — Technology Infrastructure Resilience) |
| MITRE ATT&CK | T1498 (Network Denial of Service), T1499 (Endpoint Denial of Service) |

This playbook inherits the roles, severity model, and lifecycle structure from the [Incident Response Playbook Framework](./00-ir-playbook-framework.md).

---

## 1. Scope

Covers volumetric, protocol, and application-layer denial-of-service events affecting DelTech's internet-facing connectivity at either site, the Cisco SD-WAN fabric, or resources hosted in the Azure VNet (reached via the site-to-site VPN Gateway). Excludes internal capacity/performance issues with no evidence of external attack traffic — those are an IT operations matter, not an incident.

---

## 2. Detection Sources

- **Cisco SD-WAN / firewall telemetry** — abnormal inbound traffic volume, connection-rate spikes, or link saturation at either site's internet edge.
- **Azure DDoS Protection (Standard tier, on the VNet)** — automated detection and mitigation telemetry for attacks targeting Azure-hosted resources (the replica domain controller and jump box).
- **Microsoft Sentinel** — correlates network telemetry with any accompanying alerts (DDoS is sometimes used as a smokescreen for a simultaneous intrusion attempt — treat concurrent alerts during a DDoS event as high-priority, not noise).
- **User/help desk reports** — reported outage or severe slowness of internet-facing services, VPN, or site-to-site connectivity.
- **ISP/upstream provider alerts** — DelTech's internet service provider may detect and notify before internal telemetry does, particularly for volumetric attacks upstream of the SD-WAN edge.

---

## 3. Initial Severity Guidance

| Situation | Initial Severity |
|---|---|
| Minor traffic anomaly, no service degradation | SEV-4 |
| Noticeable degradation to a single service, mitigated automatically (e.g., Azure DDoS Protection engaged) | SEV-3 |
| Sustained outage or severe degradation of internet connectivity at one site, or VPN/site-to-site link disrupted | SEV-2 |
| Full loss of internet connectivity at either site, or the attack coincides with signs of a concurrent intrusion attempt | SEV-1 |

---

## 4. Response Procedure

### 4.1 Preparation
- Confirm Azure DDoS Protection Standard is enabled and correctly scoped to the VNet's public IPs — the Basic (free) tier alone is not sufficient for an environment hosting a domain controller replica reachable via VPN Gateway.
- Maintain an up-to-date contact and escalation path with the internet service provider(s) at both Portland and Westbrook for upstream mitigation requests.
- Confirm Cisco SD-WAN edge devices have baseline rate-limiting/ACL capability configured and documented, ready to apply quickly under attack conditions.

### 4.2 Detection and Analysis
1. Confirm the traffic pattern is a genuine attack rather than a legitimate traffic surge or misconfiguration: check source IP diversity, protocol distribution, and destination ports/services targeted.
2. Query Sentinel for correlated network telemetry to characterize the attack (volumetric vs. protocol vs. application-layer):
   ```kql
   CommonSecurityLog
   | where DeviceVendor == "Cisco"
   | where TimeGenerated > ago(1h)
   | summarize RequestCount = count() by SourceIP, DestinationPort
   | order by RequestCount desc
   | take 50
   ```
3. If Azure resources are targeted, review Azure DDoS Protection's attack analytics (mitigation reports) for attack vector, volume, and whether automated mitigation is holding.
4. Determine whether the attack is affecting a single site, both sites, or specifically the site-to-site VPN Gateway path to Azure — this determines whether containment is a local network action or requires ISP-level engagement.
5. While the attack is being characterized, actively monitor for any concurrent alerts elsewhere in the environment (Falcon, Sentinel identity alerts) that could indicate the DDoS is cover for a simultaneous intrusion attempt.

### 4.3 Containment
**Short-term:**
- Apply rate-limiting or source-IP ACLs at the Cisco SD-WAN edge for identified attack sources, where the attack is small enough for local mitigation to be effective.
- For Azure-targeted attacks, confirm Azure DDoS Protection's automated mitigation is engaged; escalate to Microsoft support if the attack exceeds automated mitigation capacity.
- For large volumetric attacks exceeding local/ISP capacity, engage the upstream ISP to request scrubbing or blackholing of attack traffic as close to the source as possible.
- If the site-to-site VPN Gateway path is the target and cannot be stabilized, consider temporarily isolating the Azure VNet connection to protect on-premises operations at both sites, in coordination with IT Ops leadership.

**Long-term:**
- Work with the ISP on a standing DDoS mitigation/scrubbing service agreement if the environment sees recurring attacks.

### 4.4 Eradication
DDoS incidents typically don't have a persistent "presence" to eradicate the way malware does — eradication here means confirming attack traffic has fully stopped and any temporary mitigations (rate limits, blackholes) are appropriately scoped so they don't also block legitimate traffic indefinitely.

### 4.5 Recovery
- Gradually remove temporary mitigations (rate limits, ACLs) while monitoring for attack recurrence.
- Confirm normal service levels are restored at both sites and across the site-to-site VPN Gateway.

### 4.6 Post-Incident
- Document attack vector, volume, duration, and effectiveness of mitigations.
- Review whether Azure DDoS Protection or ISP-level mitigation response times met expectations; adjust the service tier or agreement if not.
- If the attack coincided with any other alerts, ensure that investigation is closed out separately under the relevant playbook (malware/endpoint or phishing) before closing the DDoS incident.

---

## 5. Communication Triggers

- SEV-1/SEV-2 DDoS events affecting customer-facing availability may warrant proactive communication to affected internal stakeholders (Sales, Customer Support) even without a data exposure component — Communications Lead determines internal messaging.
- If the DDoS is assessed as cover for a concurrent intrusion, communication triggers from the relevant playbook (ransomware, malware/endpoint) apply in parallel.

---

## 6. Quick-Reference Checklist

- [ ] Confirm genuine attack vs. legitimate surge/misconfiguration
- [ ] Characterize attack type (volumetric/protocol/application-layer) via Sentinel
- [ ] Check Azure DDoS Protection mitigation status if Azure resources targeted
- [ ] Determine affected scope (site, both sites, VPN Gateway path)
- [ ] Watch for concurrent alerts elsewhere (possible smokescreen)
- [ ] Apply local rate-limiting/ACLs; engage ISP for large volumetric attacks
- [ ] Gradually remove mitigations once traffic normalizes
- [ ] Document and review mitigation response time

---

## Version History

| Version | Date | Summary |
|---|---|---|
| 1.0 | July 29, 2026 | Initial publication |

---

*This document is part of DelTech LLC's Information Security Management System (ISMS) portfolio, developed in alignment with ISO/IEC 27001:2022 and the NIST Cybersecurity Framework 2.0.*
