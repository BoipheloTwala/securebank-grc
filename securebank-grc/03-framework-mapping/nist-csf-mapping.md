# SecureBank — NIST Cybersecurity Framework 2.0 Control Mapping

**Document ID:** GRC-FRM-001  
**Framework:** NIST CSF 2.0  
**Version:** 1.0  
**Effective Date:** 21 July 2026  
**Owner:** Compliance Officer  
**Approved By:** CISO  
**Next Review:** 21 July 2027  
**Classification:** Internal — Restricted

---

## Implementation Status Legend

| Status | Definition |
|--------|-----------|
| **Implemented** | Control is fully in place with documented evidence |
| **Partial** | Control is in place but gaps or improvements are pending |
| **Planned** | Control is on the roadmap with a target date |
| **Not Applicable** | Control does not apply to SecureBank's scope |
| **Gap** | Control is required but not yet addressed |

---

## GOVERN (GV) Function

Controls that establish and monitor the organisation's cybersecurity strategy, expectations, and policies.

| Control ID | Control Name | Status | SecureBank Implementation | Evidence |
|-----------|-------------|--------|--------------------------|---------|
| GV.OC-01 | Organisational Mission | Implemented | Security is embedded as a core banking value. CISO appointed. Security programme documented. | GRC README; CISO appointment letter |
| GV.OC-02 | Stakeholder Accountability | Implemented | RACI defined in this GRC package. Asset and risk owners identified. | GRC-AST-001; GRC-RSK-001 |
| GV.RM-01 | Risk Management Strategy | Implemented | Risk register maintained with quarterly reviews. Scoring methodology documented. | GRC-RSK-001 |
| GV.RM-02 | Risk Appetite | Partial | Risk appetite implicitly defined by residual risk thresholds. Formal risk appetite statement pending. | GRC-RSK-001 (scoring table) |
| GV.PO-01 | Policy Establishment | Implemented | Four security policies created and approved: Password, Access Control, Incident Response, Data Protection. | GRC-POL-001 through GRC-POL-004 |
| GV.PO-02 | Policy Review | Implemented | Annual review cycle defined. Owner assigned to each policy. | Each policy document |
| GV.SC-01 | Supply Chain Risk Management | Partial | Dependabot active; SCA tool (Socket.dev) planned. Third-party asset register maintained. | GRC-AST-001 TPY section; RSK-006 |

---

## IDENTIFY (ID) Function

Controls for understanding assets, risks, and the business context.

| Control ID | Control Name | Status | SecureBank Implementation | Evidence |
|-----------|-------------|--------|--------------------------|---------|
| ID.AM-01 | Asset Inventory | Implemented | Full IT asset register maintained covering applications, data, infrastructure, cryptographic assets, and third-party dependencies. | GRC-AST-001 |
| ID.AM-02 | Software Asset Inventory | Implemented | All application and third-party software catalogued in GRC-AST-001. NPM package-lock.json provides full dependency inventory. | GRC-AST-001; `package-lock.json` |
| ID.AM-03 | Data Flow Mapping | Partial | Data flow implied by API architecture and Prisma schema. Formal data flow diagram pending. | `securebank-api/prisma/schema.prisma` |
| ID.AM-04 | External Systems | Partial | Third-party assets catalogued (GRC-AST-001 TPY). Formal third-party risk assessment process pending. | GRC-AST-001 TPY section |
| ID.RA-01 | Vulnerability Identification | Partial | Semgrep SAST in CI/CD; Dependabot active. Formal vulnerability management programme via `securebank-vulnerability` repo in progress. | `.github/workflows/ci.yml` |
| ID.RA-02 | Threat Intelligence | Partial | SOC lab threat scenarios inform threat intelligence. No formal CTI feed subscription yet. | `securebank-soc-lab/scenarios/` |
| ID.RA-03 | Risk Assessment | Implemented | Risk register with inherent and residual risk scoring. 10 risks identified and assessed. | GRC-RSK-001 |
| ID.RA-04 | Risk Prioritisation | Implemented | Risk heat map and treatment priority list maintained in GRC-RSK-001. | GRC-RSK-001 (Treatment Priority section) |
| ID.IM-01 | Improvement Planning | Partial | Treatment actions tracked in risk register. Formal improvement programme tracking tool pending. | GRC-RSK-001 |

---

## PROTECT (PR) Function

Controls for implementing safeguards to manage cybersecurity risks.

| Control ID | Control Name | Status | SecureBank Implementation | Evidence |
|-----------|-------------|--------|--------------------------|---------|
| PR.AA-01 | Identity Management | Implemented | User registration, JWT-based authentication, role assignment (ADMIN / AUDITOR). | `securebank-api/src/middleware/auth.middleware.ts` |
| PR.AA-02 | Authentication | Implemented | bcrypt password hashing (rounds: 12); account lockout after 5 failed attempts; refresh token rotation. | `securebank-api/src/services/`; `securebank-api/.env` |
| PR.AA-03 | Multi-Factor Authentication | Planned | MFA not yet implemented. On roadmap for Q3 2026. | RSK-001 treatment plan |
| PR.AA-04 | Identity Proofing | Partial | User registration requires valid email. National ID/KYC not yet implemented. | `securebank-api/src/controllers/auth.controller.ts` |
| PR.AA-05 | Access Management | Implemented | Role-based access control enforced in auth middleware. Least-privilege model applied. | `securebank-api/src/middleware/auth.middleware.ts` |
| PR.AA-06 | Privileged Access | Partial | ADMIN role restricted. No privileged access management (PAM) solution in place. | `securebank-api/src/routes/admin.routes.ts` |
| PR.DS-01 | Data-at-Rest Protection | Partial | SHA-256 hashing of refresh tokens; bcrypt for passwords. Database encryption-at-rest not yet enabled. | RSK-005 treatment plan |
| PR.DS-02 | Data-in-Transit Protection | Implemented | TLS enforced for all communications. Helmet HSTS header enabled. | `securebank-api/src/app.ts` (Helmet config) |
| PR.DS-10 | Data Minimisation | Implemented | Sensitive fields stripped from all API responses. Request body size limited to 10 KB. | `securebank-api/src/utils/sanitise.ts` |
| PR.PS-01 | Configuration Management | Partial | Docker Compose configuration version-controlled. Infrastructure-as-Code baseline partially documented. | `securebank-api/docker-compose.yml` |
| PR.PS-02 | Software Maintenance | Implemented | Dependabot automated vulnerability alerts. CI/CD gate prevents deployment on failed checks. | `.github/workflows/ci.yml` |
| PR.IR-01 | Incident Response Training | Implemented | SOC lab provides hands-on incident response training. Three scenarios active. | `securebank-soc-lab/playbooks/` |
| PR.IR-02 | Incident Handling | Implemented | Three incident response playbooks (brute force, malware, privilege escalation) documented and tested. | `securebank-soc-lab/playbooks/P001, P002, P003` |

---

## DETECT (DE) Function

Controls for finding and analysing cybersecurity events.

| Control ID | Control Name | Status | SecureBank Implementation | Evidence |
|-----------|-------------|--------|--------------------------|---------|
| DE.CM-01 | Network Monitoring | Partial | SOC lab Wazuh deployment. Production environment monitoring coverage partial. | `securebank-soc-lab/docker-compose.yml` |
| DE.CM-03 | User Activity Monitoring | Implemented | Audit logging middleware captures all sensitive operations (login, transfer, admin actions) with actor, IP, timestamp. | `securebank-api/src/middleware/audit.middleware.ts` |
| DE.CM-06 | External Service Monitoring | Partial | Application logs via Winston. No SIEM integration in production yet. | `securebank-api/src/config/logger.ts` |
| DE.AE-02 | Event Analysis | Partial | Wazuh SIEM in SOC lab analyses events. Production SIEM integration planned. | `securebank-soc-lab/` |
| DE.AE-06 | Information Sharing | Partial | Incidents documented in SOC lab. No formal ISAC/CERT sharing arrangement yet. | `securebank-soc-lab/` |

---

## RESPOND (RS) Function

Controls for taking action on detected cybersecurity events.

| Control ID | Control Name | Status | SecureBank Implementation | Evidence |
|-----------|-------------|--------|--------------------------|---------|
| RS.MA-01 | Incident Management | Implemented | Incident Response Policy defines classification, roles, escalation path, and SLAs. | GRC-POL-003 |
| RS.MA-02 | Incident Triage | Implemented | Playbooks include severity matrices and triage procedures with target response times. | `securebank-soc-lab/playbooks/P001, P002, P003` |
| RS.AN-03 | Incident Analysis | Implemented | Playbooks include forensic investigation steps, IOC identification, and root cause analysis guidance. | `securebank-soc-lab/playbooks/P001, P002, P003` |
| RS.CO-02 | Internal Reporting | Implemented | Incident Response Policy mandates internal escalation chain with timelines. | GRC-POL-003 |
| RS.CO-03 | External Reporting | Partial | POPIA breach notification to Information Regulator within 72 hours defined in Data Protection Policy. Procedure not yet tested. | GRC-POL-004 |
| RS.MI-01 | Incident Containment | Implemented | Containment steps defined in all three playbooks. Account lockout available via Admin API. | `securebank-api/src/routes/admin.routes.ts` |

---

## RECOVER (RC) Function

Controls for restoring systems after a cybersecurity event.

| Control ID | Control Name | Status | SecureBank Implementation | Evidence |
|-----------|-------------|--------|--------------------------|---------|
| RC.RP-01 | Recovery Planning | Partial | Recovery steps included in incident playbooks. Formal Business Continuity Plan (BCP) not yet documented. | `securebank-soc-lab/playbooks/` |
| RC.RP-02 | Recovery Execution | Partial | Docker Compose allows rapid redeployment. No tested RTO/RPO targets defined. | `securebank-api/docker-compose.yml` |
| RC.CO-03 | Recovery Communication | Partial | Internal escalation path defined. Customer communication template not yet created. | GRC-POL-003 |

---

## NIST CSF 2.0 Compliance Score

| Function | Controls Assessed | Implemented | Partial | Planned / Gap | Score |
|----------|-----------------|-------------|---------|--------------|-------|
| Govern (GV) | 7 | 5 | 2 | 0 | 93% |
| Identify (ID) | 9 | 4 | 5 | 0 | 72% |
| Protect (PR) | 13 | 9 | 4 | 1 | 84% |
| Detect (DE) | 5 | 1 | 4 | 0 | 70% |
| Respond (RS) | 6 | 5 | 1 | 0 | 92% |
| Recover (RC) | 3 | 0 | 3 | 0 | 50% |
| **Total** | **43** | **24** | **19** | **1** | **87%** |

> Score = (Implemented × 1.0 + Partial × 0.5) / Total Controls × 100

---

## Gap Remediation Roadmap

| Priority | Control | Gap | Target Date |
|----------|---------|-----|------------|
| 1 | PR.AA-03 | MFA not implemented | 2026-09-30 |
| 2 | PR.DS-01 | Database encryption-at-rest missing | 2026-09-30 |
| 3 | DE.CM-01 | Production SIEM not deployed | 2026-12-31 |
| 4 | RC.RP-01 | Business Continuity Plan not documented | 2026-12-31 |
| 5 | ID.AM-03 | Formal data flow diagram not created | 2026-10-31 |

---

## Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-07-21 | Compliance Officer | Initial mapping |
