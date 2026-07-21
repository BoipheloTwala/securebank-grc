# SecureBank — Risk Register

**Document ID:** GRC-RSK-001  
**Version:** 1.0  
**Effective Date:** 21 July 2026  
**Owner:** CISO  
**Approved By:** CISO  
**Next Review:** 21 October 2026  
**Classification:** Internal — Restricted

---

## Risk Scoring Methodology

### Likelihood

| Score | Label | Definition |
|-------|-------|-----------|
| 1 | Rare | Unlikely in the next 3 years |
| 2 | Unlikely | Could occur within 3 years |
| 3 | Possible | Could occur within 1 year |
| 4 | Likely | Will probably occur within 1 year |
| 5 | Almost Certain | Expected to occur within 6 months |

### Impact

| Score | Label | Financial Impact | Operational Impact |
|-------|-------|-----------------|-------------------|
| 1 | Negligible | < R10 000 | Minor disruption < 1 hour |
| 2 | Minor | R10 000 – R100 000 | Disruption 1–8 hours |
| 3 | Moderate | R100 000 – R1 000 000 | Disruption 1–3 days |
| 4 | Major | R1M – R10M | Disruption 3–7 days |
| 5 | Catastrophic | > R10M or regulatory action | Disruption > 1 week |

### Risk Rating = Likelihood × Impact

| Score | Rating | Colour | Treatment |
|-------|--------|--------|-----------|
| 1–4 | Low | Green | Accept / monitor |
| 5–9 | Medium | Yellow | Treat / reduce |
| 10–14 | High | Orange | Treat urgently |
| 15–25 | Critical | Red | Immediate action required |

---

## Risk Register

### RSK-001 — Phishing Attack

| Field | Detail |
|-------|--------|
| **Risk ID** | RSK-001 |
| **Category** | Social Engineering |
| **Description** | Employees or customers are targeted by phishing emails or SMS designed to steal credentials or deliver malware. |
| **Affected Assets** | DAT-001, DAT-002, DAT-003, APP-001, SEC-001, SEC-002 |
| **Threat Actor** | External — Cybercriminal |
| **Vulnerability** | User susceptibility to social engineering; weak email filtering |
| **Likelihood** | 4 — Likely |
| **Impact** | 4 — Major |
| **Inherent Risk Score** | **16 — Critical** |
| **Existing Controls** | Multi-factor authentication (planned), JWT short-lived tokens (15 min), account lockout after 5 failed logins (APP-001), rate limiting on auth routes |
| **Residual Likelihood** | 3 |
| **Residual Impact** | 3 |
| **Residual Risk Score** | **9 — Medium** |
| **Treatment** | Reduce — Implement MFA; deploy email security gateway (DMARC/DKIM/SPF); conduct quarterly phishing simulation training |
| **Owner** | CISO |
| **Target Date** | 2026-09-30 |
| **Status** | In Progress |

---

### RSK-002 — Weak / Compromised Passwords

| Field | Detail |
|-------|--------|
| **Risk ID** | RSK-002 |
| **Category** | Identity & Access Management |
| **Description** | Users set weak passwords or reuse passwords from other breaches, enabling unauthorised account takeover. |
| **Affected Assets** | DAT-001, DAT-002, DAT-003, APP-001 |
| **Threat Actor** | External — Cybercriminal; Internal — Negligent User |
| **Vulnerability** | No minimum complexity enforcement at application layer (front-end only); no breach password check |
| **Likelihood** | 4 — Likely |
| **Impact** | 3 — Moderate |
| **Inherent Risk Score** | **12 — High** |
| **Existing Controls** | bcrypt hashing (rounds: 12), account lockout, refresh token rotation, short-lived access tokens |
| **Residual Likelihood** | 2 |
| **Residual Impact** | 3 |
| **Residual Risk Score** | **6 — Medium** |
| **Treatment** | Reduce — Enforce password policy server-side (Zod schema); integrate HaveIBeenPwned API for breach password check; implement MFA |
| **Owner** | IT Director |
| **Target Date** | 2026-09-30 |
| **Status** | In Progress |

---

### RSK-003 — SQL Injection / API Injection

| Field | Detail |
|-------|--------|
| **Risk ID** | RSK-003 |
| **Category** | Application Security |
| **Description** | Malicious input in API requests bypasses validation to manipulate database queries or application logic. |
| **Affected Assets** | APP-001, DAT-001, DAT-002, DAT-003, INF-001 |
| **Threat Actor** | External — Attacker |
| **Vulnerability** | Insufficient input validation; ORM misuse could expose raw queries |
| **Likelihood** | 2 — Unlikely |
| **Impact** | 5 — Catastrophic |
| **Inherent Risk Score** | **10 — High** |
| **Existing Controls** | Zod schema validation on all inputs; Prisma ORM (parameterised queries by default); Semgrep SAST in CI/CD; request body size limit (10 KB) |
| **Residual Likelihood** | 1 |
| **Residual Impact** | 5 |
| **Residual Risk Score** | **5 — Medium** |
| **Treatment** | Reduce — Maintain Semgrep rules; conduct annual penetration test; enforce no raw query usage via code review checklist |
| **Owner** | Backend Lead |
| **Target Date** | 2026-12-31 |
| **Status** | Partially mitigated |

---

### RSK-004 — Unauthorised Privilege Escalation

| Field | Detail |
|-------|--------|
| **Risk ID** | RSK-004 |
| **Category** | Identity & Access Management |
| **Description** | A user escalates privileges beyond their assigned role (CUSTOMER → ADMIN) via API manipulation or token forgery. |
| **Affected Assets** | APP-001, APP-004, DAT-001, DAT-002, DAT-003, DAT-004 |
| **Threat Actor** | Internal — Malicious Insider; External — Attacker |
| **Vulnerability** | JWT role claims stored in token; if signing secret is compromised, roles can be forged |
| **Likelihood** | 2 — Unlikely |
| **Impact** | 5 — Catastrophic |
| **Inherent Risk Score** | **10 — High** |
| **Existing Controls** | RBAC enforced in auth middleware; JWT signed with strong secret; short-lived access tokens (15 min); SOC lab scenario for privilege escalation (P003) |
| **Residual Likelihood** | 1 |
| **Residual Impact** | 5 |
| **Residual Risk Score** | **5 — Medium** |
| **Treatment** | Reduce — Rotate JWT secrets every 90 days; store secrets in secrets manager; implement token revocation list; review RBAC middleware quarterly |
| **Owner** | IT Director |
| **Target Date** | 2026-10-31 |
| **Status** | Partially mitigated |

---

### RSK-005 — Data Breach (Customer PII / Financial Data)

| Field | Detail |
|-------|--------|
| **Risk ID** | RSK-005 |
| **Category** | Data Protection |
| **Description** | Unauthorised disclosure of customer personally identifiable information or financial records through technical attack, insider threat, or misconfiguration. |
| **Affected Assets** | DAT-001, DAT-002, DAT-003, INF-001 |
| **Threat Actor** | External — Attacker; Internal — Malicious Insider |
| **Vulnerability** | Database exposed without encryption-at-rest; backup files not encrypted |
| **Likelihood** | 3 — Possible |
| **Impact** | 5 — Catastrophic |
| **Inherent Risk Score** | **15 — Critical** |
| **Existing Controls** | Sensitive fields stripped from all API responses; audit logging of all data access; RBAC; Helmet security headers; TLS in transit |
| **Residual Likelihood** | 2 |
| **Residual Impact** | 5 |
| **Residual Risk Score** | **10 — High** |
| **Treatment** | Reduce — Enable PostgreSQL encryption-at-rest; encrypt database backups; implement database activity monitoring (DAM); define POPIA breach notification procedure (72-hour window) |
| **Owner** | Data Protection Officer |
| **Target Date** | 2026-09-30 |
| **Status** | In Progress — High Priority |

---

### RSK-006 — Supply Chain Compromise (Malicious NPM Package)

| Field | Detail |
|-------|--------|
| **Risk ID** | RSK-006 |
| **Category** | Supply Chain |
| **Description** | A malicious or compromised NPM dependency introduces a backdoor, cryptominer, or data exfiltration capability into the SecureBank API or Platform. |
| **Affected Assets** | APP-001, APP-002, TPY-001 through TPY-008 |
| **Threat Actor** | External — Supply Chain Attacker |
| **Vulnerability** | High dependency count; automated dependency updates could introduce malicious code |
| **Likelihood** | 2 — Unlikely |
| **Impact** | 5 — Catastrophic |
| **Inherent Risk Score** | **10 — High** |
| **Existing Controls** | Dependabot automated dependency scanning; Semgrep SAST; package-lock.json pinned versions; GitHub Actions CI gate |
| **Residual Likelihood** | 2 |
| **Residual Impact** | 4 |
| **Residual Risk Score** | **8 — Medium** |
| **Treatment** | Reduce — Add Socket.dev or similar SCA tool; review Dependabot PRs within 72 hours; pin critical dependency versions; conduct annual supply chain review |
| **Owner** | IT Manager |
| **Target Date** | 2026-10-31 |
| **Status** | Partially mitigated |

---

### RSK-007 — Ransomware / Malware Infection

| Field | Detail |
|-------|--------|
| **Risk ID** | RSK-007 |
| **Category** | Malware |
| **Description** | Ransomware or destructive malware encrypts or destroys database or application data, causing service outage and potential data loss. |
| **Affected Assets** | INF-001, INF-003, DAT-001, DAT-002, DAT-003 |
| **Threat Actor** | External — Cybercriminal |
| **Vulnerability** | No immutable backup strategy; no offline backup |
| **Likelihood** | 3 — Possible |
| **Impact** | 4 — Major |
| **Inherent Risk Score** | **12 — High** |
| **Existing Controls** | SOC lab malware response playbook (P002); Docker isolation; non-root container execution |
| **Residual Likelihood** | 2 |
| **Residual Impact** | 4 |
| **Residual Risk Score** | **8 — Medium** |
| **Treatment** | Reduce — Implement daily encrypted offsite backups with 30-day retention; test backup restoration quarterly; deploy EDR on host; implement network segmentation |
| **Owner** | IT Manager |
| **Target Date** | 2026-09-30 |
| **Status** | In Progress |

---

### RSK-008 — Denial of Service (DoS / DDoS)

| Field | Detail |
|-------|--------|
| **Risk ID** | RSK-008 |
| **Category** | Availability |
| **Description** | Volumetric or application-layer attack overwhelms the API, causing service unavailability for customers. |
| **Affected Assets** | APP-001, APP-002, INF-003 |
| **Threat Actor** | External — Hacktivist; Cybercriminal |
| **Vulnerability** | No CDN or DDoS protection layer; single-instance deployment |
| **Likelihood** | 3 — Possible |
| **Impact** | 3 — Moderate |
| **Inherent Risk Score** | **9 — Medium** |
| **Existing Controls** | express-rate-limit (global: 100 req/window; auth: 10 req/window); request body size limit (10 KB) |
| **Residual Likelihood** | 2 |
| **Residual Impact** | 3 |
| **Residual Risk Score** | **6 — Medium** |
| **Treatment** | Reduce — Deploy behind CDN/WAF (Cloudflare); implement auto-scaling; define SLA and RTO/RPO targets |
| **Owner** | IT Manager |
| **Target Date** | 2026-12-31 |
| **Status** | Planned |

---

### RSK-009 — Regulatory Non-Compliance (POPIA)

| Field | Detail |
|-------|--------|
| **Risk ID** | RSK-009 |
| **Category** | Compliance |
| **Description** | Failure to comply with the Protection of Personal Information Act (POPIA) resulting in regulatory fines, sanctions, or reputational damage. |
| **Affected Assets** | DAT-001, DAT-002, DAT-003 |
| **Threat Actor** | Regulatory Body — Information Regulator (South Africa) |
| **Vulnerability** | Data retention policy not formally documented; breach notification procedure not tested |
| **Likelihood** | 2 — Unlikely |
| **Impact** | 4 — Major |
| **Inherent Risk Score** | **8 — Medium** |
| **Existing Controls** | DPO appointed; data protection policy in place; POPIA mapping documented; audit logs maintained |
| **Residual Likelihood** | 1 |
| **Residual Impact** | 4 |
| **Residual Risk Score** | **4 — Low** |
| **Treatment** | Accept with monitoring — Conduct annual POPIA audit; test breach notification procedure bi-annually; maintain data subject request log |
| **Owner** | Data Protection Officer |
| **Target Date** | 2026-12-31 |
| **Status** | Substantially compliant |

---

### RSK-010 — Insider Threat (Data Theft / Sabotage)

| Field | Detail |
|-------|--------|
| **Risk ID** | RSK-010 |
| **Category** | Insider Threat |
| **Description** | A current or former employee intentionally exfiltrates customer data or sabotages systems for personal gain or malice. |
| **Affected Assets** | DAT-001, DAT-002, DAT-003, DAT-004, APP-001 |
| **Threat Actor** | Internal — Malicious Insider |
| **Vulnerability** | Privileged access not regularly reviewed; no user behaviour analytics (UBA) |
| **Likelihood** | 2 — Unlikely |
| **Impact** | 4 — Major |
| **Inherent Risk Score** | **8 — Medium** |
| **Existing Controls** | RBAC (least privilege); audit logging of all sensitive operations; role-based API access; deactivation procedure for departing employees |
| **Residual Likelihood** | 1 |
| **Residual Impact** | 4 |
| **Residual Risk Score** | **4 — Low** |
| **Treatment** | Reduce — Implement quarterly access reviews; deploy UBA tooling; define offboarding checklist with immediate credential revocation |
| **Owner** | CISO |
| **Target Date** | 2026-10-31 |
| **Status** | Partially mitigated |

---

## Risk Heat Map Summary

| Risk ID | Name | Residual Score | Rating |
|---------|------|---------------|--------|
| RSK-005 | Data Breach (PII / Financial) | 10 | High |
| RSK-001 | Phishing Attack | 9 | Medium |
| RSK-007 | Ransomware / Malware | 8 | Medium |
| RSK-006 | Supply Chain Compromise | 8 | Medium |
| RSK-002 | Weak / Compromised Passwords | 6 | Medium |
| RSK-008 | Denial of Service | 6 | Medium |
| RSK-003 | SQL / API Injection | 5 | Medium |
| RSK-004 | Privilege Escalation | 5 | Medium |
| RSK-009 | Regulatory Non-Compliance (POPIA) | 4 | Low |
| RSK-010 | Insider Threat | 4 | Low |

---

## Treatment Priority

1. **RSK-005** — Database encryption-at-rest + POPIA breach procedure (Q3 2026)
2. **RSK-001** — MFA rollout + phishing training (Q3 2026)
3. **RSK-007** — Offsite encrypted backup strategy (Q3 2026)
4. **RSK-002** — Server-side password policy enforcement (Q3 2026)
5. **RSK-004** — JWT secret rotation + secrets manager (Q4 2026)

---

## Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-07-21 | CISO | Initial risk register |
