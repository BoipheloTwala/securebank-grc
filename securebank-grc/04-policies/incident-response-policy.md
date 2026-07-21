# SecureBank — Incident Response Policy

**Document ID:** GRC-POL-003  
**Version:** 1.0  
**Effective Date:** 21 July 2026  
**Owner:** CISO  
**Approved By:** CISO  
**Next Review:** 21 July 2027  
**Classification:** Internal  
**Scope:** All SecureBank systems, personnel, and third parties processing SecureBank data.

---

## 1. Purpose

This policy establishes SecureBank's approach to preparing for, detecting, containing, eradicating, and recovering from security incidents. It defines roles, responsibilities, severity levels, response timelines, and escalation paths to minimise the impact of security events on customers, operations, and regulatory standing.

---

## 2. Definitions

| Term | Definition |
|------|-----------|
| **Security Event** | Any observable occurrence that may indicate a threat to the confidentiality, integrity, or availability of SecureBank systems or data. |
| **Security Incident** | A confirmed or suspected security event that has a negative impact or potential negative impact on SecureBank. |
| **Data Breach** | An incident involving the unauthorised access to, or disclosure of, personal information (triggers POPIA Section 22 notification requirements). |
| **Critical Incident** | An incident causing significant service disruption, data loss, or regulatory reporting obligation. |

---

## 3. Incident Classification

| Severity | Score | Examples | Response SLA |
|----------|-------|---------|-------------|
| **P1 — Critical** | Residual risk ≥ 15 | Active data breach; ransomware deployment; production database compromised; mass account takeover | 15 minutes initial response; 1-hour containment target |
| **P2 — High** | Residual risk 10–14 | Confirmed unauthorised access; privilege escalation in production; DDoS causing outage | 30 minutes initial response; 4-hour containment target |
| **P3 — Medium** | Residual risk 5–9 | Brute force attack (no successful login); phishing attempt; single account compromise | 2 hours initial response; 24-hour containment target |
| **P4 — Low** | Residual risk 1–4 | Policy violation; failed port scan; isolated malware detection on non-critical system | 8 hours initial response; 72-hour containment target |

---

## 4. Incident Response Team

| Role | Name / Title | Responsibilities | Contact |
|------|-------------|-----------------|---------|
| **Incident Commander** | CISO | Declares severity; coordinates response; owns communication | CISO direct line |
| **Technical Lead** | IT Director | Leads technical investigation and containment | IT Director |
| **SOC Analyst** | SOC Lead | Monitors alerts; initial triage; evidence collection | SOC hotline |
| **Communications Lead** | DPO / Legal | Manages internal and external communications, regulatory notifications | DPO |
| **Executive Sponsor** | CEO | Notified for P1 incidents; approves public statements | CEO |

---

## 5. Incident Response Phases

### Phase 1 — Preparation

**Objective:** Ensure the organisation is ready to respond before an incident occurs.

- [ ] Incident response playbooks maintained and tested (SOC lab — P001, P002, P003).
- [ ] On-call roster defined and published.
- [ ] Communication channels (secure out-of-band channel) pre-established.
- [ ] Evidence collection tools and procedures documented.
- [ ] Contact list for regulatory bodies (Information Regulator, SARB) maintained.

---

### Phase 2 — Detection and Analysis

**Objective:** Identify and confirm that a security incident has occurred.

**Detection Sources:**

| Source | Tool |
|--------|------|
| Application alerts | Winston logger + Wazuh SIEM |
| Failed authentication spikes | Rate limiter alerts; `securebank-soc-lab` Wazuh Rule 100002 |
| Unusual transaction patterns | Audit log review |
| External report | Customer complaint; third-party notification |
| Vulnerability disclosure | Dependabot; Semgrep; researcher report |

**Analysis Steps:**

1. Collect alert details: source IP, target, timestamps, affected accounts.
2. Correlate with audit logs (`GET /api/v1/admin/audit-logs`).
3. Classify the incident severity using the table in Section 3.
4. Declare the incident formally — assign an incident ID (`INC-YYYY-MM-DD-NNN`).
5. Notify the Incident Response Team within the P1/P2/P3 SLA.

---

### Phase 3 — Containment

**Objective:** Limit the damage and prevent the incident from spreading.

**Short-Term Containment (immediate):**

| Action | Method |
|--------|--------|
| Lock compromised user account | `PATCH /api/v1/admin/users/:id/deactivate` |
| Revoke all active sessions | Invalidate refresh tokens in database |
| Block source IP | Firewall / rate limiter configuration |
| Isolate affected container | `docker stop <container>` |
| Preserve evidence | Snapshot container filesystem before any changes |

**Long-Term Containment:**

- Deploy patched or hardened replacement systems.
- Implement additional monitoring on affected systems.
- Enforce re-authentication for all active sessions if token compromise is suspected.

---

### Phase 4 — Eradication

**Objective:** Remove the root cause of the incident.

1. Identify and remove malware, backdoors, or unauthorised accounts.
2. Patch the exploited vulnerability (apply fix, merge PR, deploy to production).
3. Rotate all potentially compromised credentials: JWT secrets, database passwords, API keys.
4. Verify that the attack vector is fully closed before proceeding to recovery.

---

### Phase 5 — Recovery

**Objective:** Restore affected systems to normal operation.

1. Restore from last known good backup if data was modified or destroyed.
2. Deploy patched application version through CI/CD pipeline.
3. Monitor closely for 48 hours post-recovery for signs of re-compromise.
4. Validate that all services are operating normally and audit logs are intact.
5. Communicate service restoration to affected users.

---

### Phase 6 — Post-Incident Review

**Objective:** Learn from the incident to prevent recurrence.

**Mandatory for all P1 and P2 incidents. Recommended for P3.**

| Activity | Deadline |
|----------|----------|
| Post-incident review meeting | Within 5 business days of resolution |
| Incident report drafted (INC report template) | Within 10 business days of resolution |
| Risk register updated with new/changed risks | Within 15 business days |
| Remediation actions tracked in GRC platform | Ongoing |

---

## 6. Communication Plan

### Internal Communications

| Audience | Trigger | Medium | Owner |
|----------|---------|--------|-------|
| Incident Response Team | All incidents | Secure messaging + direct call | SOC Lead |
| CEO / Board | P1 incidents | Phone + email | CISO |
| All staff | If phishing campaign targets employees | Company-wide email | CISO |

### External / Regulatory Communications

| Audience | Trigger | Deadline | Owner |
|----------|---------|----------|-------|
| Information Regulator (POPIA Section 22) | Data breach involving personal information | 72 hours from discovery | DPO |
| Affected data subjects | Data breach where risk of harm is high | As soon as reasonably practicable | DPO |
| SARB / Financial Intelligence Centre | Incidents affecting payment systems | Per SARB GUIDANCE NOTE 3 | CISO + Legal |
| Media | Only if public disclosure is legally required | Approved by CEO | Communications Lead |

---

## 7. Evidence Handling

- Evidence must be collected using forensically sound methods.
- A chain of custody document must be maintained for all evidence.
- Evidence must not be modified; work on forensic copies only.
- Evidence must be retained for a minimum of 3 years.

---

## 8. Testing

| Activity | Frequency | Owner |
|----------|-----------|-------|
| Tabletop incident response exercise | Annually | CISO |
| SOC lab simulation (brute force, malware, privilege escalation) | Quarterly | SOC Lead |
| Breach notification procedure test | Bi-annually | DPO |

---

## Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-07-21 | CISO | Initial policy |
