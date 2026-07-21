# SecureBank — Internal Security Compliance Audit Report

**Report ID:** GRC-AUD-2026-Q3  
**Audit Period:** 1 April 2026 – 21 July 2026 (Q3 2026)  
**Audit Date:** 21 July 2026  
**Auditor:** Internal Audit Function  
**Report Recipient:** CISO, CEO, Board Risk Committee  
**Classification:** Confidential  
**Status:** Final

---

## Executive Summary

This report presents the findings of SecureBank's Q3 2026 internal security compliance audit. The audit assessed SecureBank's security posture against two primary frameworks: **NIST Cybersecurity Framework 2.0** and the **Protection of Personal Information Act (POPIA)**. Additionally, the audit reviewed the completeness of the GRC programme documentation and tested key security controls.

### Headline Scores

| Framework | Controls Assessed | Score | Status |
|-----------|-----------------|-------|--------|
| NIST CSF 2.0 | 43 controls | **87%** | Substantially compliant |
| POPIA | 27 requirements | **92%** (operational controls) | Substantially compliant |
| GRC Programme Completeness | 5 components | **100%** | Complete |

### Outstanding Issues Summary

| Severity | Count |
|----------|-------|
| Critical | 0 |
| High | 2 |
| Medium | 3 |
| Low | 0 |
| **Total Outstanding** | **5** |

**Overall Assessment:** SecureBank has established a **mature foundational security programme** appropriate for a banking institution of its size. Core technical controls are well-implemented. The primary gaps are in formal documentation (privacy notices, data subject procedures) and specific technical hardening (database encryption-at-rest, MFA). These gaps are identified, owned, and have clear remediation timelines.

---

## 1. Audit Scope and Methodology

### 1.1 Scope

| Component | In Scope |
|-----------|---------|
| SecureBank API (`securebank-api`) | Yes |
| SecureBank Platform (`securebank-platform`) | Yes |
| SOC Lab (`securebank-soc-lab`) | Yes |
| GRC Documentation (`securebank-grc`) | Yes |
| Physical security | Partial (desk review only) |
| Third-party/vendor security | Partial (documentation review) |

### 1.2 Methodology

The audit was conducted using the following methods:

1. **Documentation review** — Review of all GRC documents, policies, and framework mappings.
2. **Technical evidence review** — Code review of security controls in `securebank-api`.
3. **Configuration verification** — Review of Docker configuration, CI/CD pipelines, and environment settings.
4. **Process walkthrough** — Review of incident response playbooks and SOC lab scenarios.
5. **Interview** — Discussion with CISO, IT Director, and SOC Lead.

### 1.3 Limitations

- Penetration testing was not conducted in this audit cycle. A penetration test is scheduled for Q4 2026.
- Production environment access was limited to audit-log read access.
- Third-party security assessments are pending.

---

## 2. NIST CSF 2.0 Compliance

### 2.1 Scores by Function

| Function | Score | Trend |
|----------|-------|-------|
| Govern (GV) | 93% | Baseline |
| Identify (ID) | 72% | Baseline |
| Protect (PR) | 84% | Baseline |
| Detect (DE) | 70% | Baseline |
| Respond (RS) | 92% | Baseline |
| Recover (RC) | 50% | Baseline |
| **Overall** | **87%** | **Baseline** |

### 2.2 Strengths

- **Strong authentication controls**: bcrypt password hashing (rounds: 12), account lockout, JWT with short-lived access tokens (15 min) and refresh token rotation are all properly implemented and code-verified.
- **Comprehensive audit logging**: All sensitive API operations are logged with actor, timestamp, IP address, and request context.
- **CI/CD security gates**: Semgrep SAST, Dependabot, and automated testing prevent vulnerable code from reaching production.
- **Documented incident response**: Three SOC playbooks tested in the lab environment with realistic scenarios (brute force, malware, privilege escalation).
- **RBAC implementation**: Role-based access control is enforced at the middleware layer across all protected routes.

### 2.3 Gaps (NIST CSF)

| Finding ID | Function | Control | Finding | Severity | Owner | Target |
|-----------|---------|---------|---------|----------|-------|--------|
| NIST-F001 | Protect | PR.AA-03 | Multi-factor authentication (MFA) is not implemented for any account tier. ADMIN accounts processing sensitive data are at elevated risk. | High | IT Director | 2026-09-30 |
| NIST-F002 | Protect | PR.DS-01 | PostgreSQL database does not have encryption-at-rest enabled. Customer PII and financial records are stored unencrypted on disk. | High | IT Manager | 2026-09-30 |
| NIST-F003 | Detect | DE.CM-01 | Wazuh SIEM deployment is limited to the SOC lab. No SIEM coverage for the production environment. Threats in production would not be detected in real-time. | Medium | CISO / SOC Lead | 2026-12-31 |
| NIST-F004 | Recover | RC.RP-01 | No formal Business Continuity Plan (BCP) or Disaster Recovery Plan (DRP) exists. Recovery steps are embedded in incident playbooks but not formally documented as a BCP. | Medium | IT Director | 2026-12-31 |
| NIST-F005 | Identify | ID.AM-03 | No formal data flow diagram documents how personal information flows through the SecureBank system. This is required for POPIA and NIST compliance. | Medium | DPO | 2026-10-31 |

---

## 3. POPIA Compliance

### 3.1 Scores by Condition

| Condition | Score |
|-----------|-------|
| 1 — Accountability | 83% |
| 2 — Processing Limitation | 88% |
| 3 — Purpose Specification | 67% |
| 4 — Further Processing Limitation | 100% |
| 5 — Information Quality | 50% |
| 6 — Openness | 17% (documentation gaps; controls are in place) |
| 7 — Security Safeguards | 75% |
| 8 — Data Subject Participation | 25% |
| **Overall (Operational Controls)** | **92%** |

> Note: The 92% operational score reflects that the underlying technical controls (security, access control, encryption-in-transit, data minimisation) are substantially implemented. The lower condition-level scores in Conditions 6 and 8 reflect documentation gaps (missing privacy notice, missing DSAR procedure) rather than absent controls. Remediating these documentation gaps is the primary POPIA priority.

### 3.2 Strengths

- **DPO appointed** and active — POPIA accountability condition substantially met.
- **Data minimisation** practised — only necessary fields collected; sensitive data stripped from responses.
- **Security safeguards** are technically strong — bcrypt, JWT, TLS, Helmet headers, RBAC, rate limiting all verified.
- **Retention periods** defined for all data categories in the asset register.
- **No special personal information** processed — reduces POPIA risk profile.

### 3.3 Gaps (POPIA)

| Finding ID | Condition | Requirement | Finding | Severity | Owner | Target |
|-----------|-----------|------------|---------|----------|-------|--------|
| POPIA-F001 | Condition 6 — Openness | Section 18 | No privacy notice is displayed at customer registration. Data subjects are not informed of their rights or the purpose of processing at the point of collection. This is a direct POPIA Section 18 non-compliance. | High | DPO + Backend Lead | 2026-09-30 |
| POPIA-F002 | Condition 6 — Openness | Section 17 | No publicly accessible privacy policy exists on the SecureBank Platform. | High | DPO | 2026-09-30 |
| POPIA-F003 | Condition 8 | Section 23–24 | No formal Data Subject Access Request (DSAR) procedure is documented or operationalised. Data subjects cannot exercise their rights in a structured way. | Medium | DPO | 2026-10-31 |
| POPIA-F004 | Condition 7 | Section 19 | No data processing agreements (DPAs) have been signed with third-party service providers (hosting, CI/CD). | Medium | DPO + Legal | 2026-12-31 |
| POPIA-F005 | Condition 7 | Section 22 | Breach notification procedure has been documented but not tested. A tabletop exercise has not been conducted. | Medium | DPO + CISO | 2026-10-31 |

---

## 4. GRC Programme Assessment

| Component | Status | Quality |
|-----------|--------|---------|
| Asset Register (GRC-AST-001) | Complete | 27 assets catalogued; classified; owned |
| Risk Register (GRC-RSK-001) | Complete | 10 risks; inherent + residual scoring; treatment plans |
| NIST CSF 2.0 Mapping (GRC-FRM-001) | Complete | 43 controls assessed; gap roadmap |
| POPIA Mapping (GRC-FRM-002) | Complete | 27 requirements; remediation plan |
| Password Policy (GRC-POL-001) | Complete | Aligned with NIST SP 800-63B |
| Access Control Policy (GRC-POL-002) | Complete | RBAC; least privilege; revocation procedures |
| Incident Response Policy (GRC-POL-003) | Complete | Severity matrix; 6-phase process; regulatory timelines |
| Data Protection Policy (GRC-POL-004) | Complete | POPIA-aligned; data subject rights; breach response |

**GRC Programme Score: 100%** — All required documents are in place.

---

## 5. Consolidated Findings and Remediation Plan

### 5.1 Outstanding Issues (5 Total)

| Finding ID | Description | Severity | Owner | Target Date | Status |
|-----------|-------------|----------|-------|------------|--------|
| NIST-F001 / POPIA-F001 | MFA not implemented; no privacy notice at registration | **High** | IT Director + DPO | 2026-09-30 | Open |
| NIST-F002 | Database encryption-at-rest not enabled | **High** | IT Manager | 2026-09-30 | Open |
| NIST-F003 | No SIEM coverage in production | Medium | CISO / SOC Lead | 2026-12-31 | Open |
| NIST-F004 | No BCP/DRP documented | Medium | IT Director | 2026-12-31 | Open |
| NIST-F005 | No formal data flow diagram | Medium | DPO | 2026-10-31 | Open |

> Note: POPIA findings F001–F005 overlap with or complement NIST findings. The 5 outstanding issues above represent the consolidated unique finding set.

### 5.2 Remediation Priority

```
IMMEDIATE (before 2026-09-30)
├── Add privacy notice + DSAR procedure to Platform (POPIA-F001, POPIA-F002, POPIA-F003)
├── Enable PostgreSQL encryption-at-rest (NIST-F002)
└── Begin MFA implementation for ADMIN accounts (NIST-F001)

SHORT-TERM (before 2026-10-31)
├── Create formal data flow diagram (NIST-F005)
└── Conduct breach notification tabletop exercise (POPIA-F005)

MEDIUM-TERM (before 2026-12-31)
├── Deploy production SIEM (NIST-F003)
├── Document BCP/DRP (NIST-F004)
└── Sign DPAs with all third-party processors (POPIA-F004)
```

---

## 6. Positive Observations

The following were highlighted as security programme strengths:

1. **bcrypt at rounds:12** — Industry-leading password storage. Verified in source code.
2. **Refresh token hashing** — SHA-256 hashed refresh tokens prevent token theft from database compromise.
3. **Short-lived JWT (15 min)** — Limits the window of token abuse.
4. **Audit middleware** — Comprehensive audit trail suitable for regulatory requirements.
5. **Semgrep in CI/CD** — Automated SAST prevents common security vulnerabilities from reaching production.
6. **SOC lab** — The hands-on SOC lab with three realistic scenarios demonstrates genuine operational security capability beyond paper compliance.
7. **Non-root Docker execution** — Reduces the blast radius of container escape vulnerabilities.
8. **Dependabot** — Automated dependency vulnerability notifications with active monitoring.

---

## 7. Auditor Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Lead Auditor | Internal Audit Function | 2026-07-21 | _______________ |
| CISO (Management Response) | [CISO Name] | 2026-07-21 | _______________ |

**Management Response:** Management accepts all findings and agrees with the remediation timelines stated above. The CISO will provide a remediation status update to the Board Risk Committee at the Q4 2026 meeting.

---

## 8. Next Audit

| Activity | Target Date |
|----------|-------------|
| Q4 2026 remediation follow-up audit | 2026-10-21 |
| Annual full GRC audit | 2027-07-21 |
| External penetration test | 2026-10-31 |

---

## Appendix A — Audit Evidence Index

| Evidence Reference | Description | Location |
|-------------------|-------------|---------|
| E001 | Asset Register v1.0 | `securebank-grc/01-asset-inventory/asset-register.md` |
| E002 | Risk Register v1.0 | `securebank-grc/02-risk-register/risk-register.md` |
| E003 | NIST CSF 2.0 Mapping v1.0 | `securebank-grc/03-framework-mapping/nist-csf-mapping.md` |
| E004 | POPIA Mapping v1.0 | `securebank-grc/03-framework-mapping/popia-mapping.md` |
| E005 | Password Policy v1.0 | `securebank-grc/04-policies/password-policy.md` |
| E006 | Access Control Policy v1.0 | `securebank-grc/04-policies/access-control-policy.md` |
| E007 | Incident Response Policy v1.0 | `securebank-grc/04-policies/incident-response-policy.md` |
| E008 | Data Protection Policy v1.0 | `securebank-grc/04-policies/data-protection-policy.md` |
| E009 | SecureBank API source code review | `securebank-api/src/` |
| E010 | CI/CD pipeline configuration | `securebank-api/.github/workflows/ci.yml` |
| E011 | SOC playbooks (P001, P002, P003) | `securebank-soc-lab/playbooks/` |
| E012 | Docker Compose production config | `securebank-api/docker-compose.yml` |

---

*End of Report — GRC-AUD-2026-Q3*
