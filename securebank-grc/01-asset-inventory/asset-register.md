# SecureBank — IT Asset Register

**Document ID:** GRC-AST-001  
**Version:** 1.0  
**Effective Date:** 21 July 2026  
**Owner:** IT Manager  
**Approved By:** CISO  
**Next Review:** 21 October 2026  
**Classification:** Internal — Restricted

---

## Classification Scheme

| Label | Definition | Examples |
|-------|-----------|---------|
| **Public** | Approved for public release | Marketing website, public API docs |
| **Internal** | For employees only — not public | Internal wikis, development tooling |
| **Confidential** | Sensitive business data — limited distribution | Financial reports, employee records |
| **Critical** | Compromise could cause severe harm or regulatory breach | Customer PII, payment data, auth secrets |

---

## 1. Application Assets

| ID | Asset Name | Description | Owner | Custodian | Classification | Environment | Repo / Location |
|----|-----------|-------------|-------|-----------|---------------|-------------|----------------|
| APP-001 | SecureBank API | Core banking REST API (Node.js / Express / TypeScript). Handles auth, accounts, transactions, audit logs. | IT Director | Backend Lead | Critical | Production | `securebank-api` |
| APP-002 | SecureBank Platform | Customer-facing React/Vite web application. SPA served via Vite build. | IT Director | Frontend Lead | Confidential | Production | `securebank-platform` |
| APP-003 | Swagger / OpenAPI Docs | Auto-generated API specification at `/api-docs`. Public-facing documentation. | IT Director | Backend Lead | Public | Production | `securebank-api/src/config/swagger.ts` |
| APP-004 | Admin Portal | ADMIN-role restricted section of the API (`/api/v1/admin`). User management, audit log viewer. | IT Director | Backend Lead | Critical | Production | `securebank-api` |
| APP-005 | SOC Lab | Wazuh-based detection lab for threat simulation (brute force, malware, privilege escalation). | CISO | SOC Lead | Internal | Lab | `securebank-soc-lab` |

---

## 2. Data Assets

| ID | Asset Name | Description | Owner | Classification | Retention Period | Storage Location |
|----|-----------|-------------|-------|---------------|-----------------|----------------|
| DAT-001 | Customer PII | Full name, email address, national ID (if collected), contact details. | Data Protection Officer | Critical | 5 years post-account closure | PostgreSQL — `User` table |
| DAT-002 | Account Records | Bank account numbers, balances, account status, currency. | Finance Director | Critical | 7 years (SARS requirement) | PostgreSQL — `Account` table |
| DAT-003 | Transaction History | Deposit, withdrawal, and transfer records including amounts, timestamps, descriptions. | Finance Director | Critical | 7 years (SARS requirement) | PostgreSQL — `Transaction` table |
| DAT-004 | Audit Logs | System audit trail — all sensitive operations (login, transfer, admin actions) with actor, timestamp, IP. | CISO | Confidential | 3 years | PostgreSQL — `AuditLog` table |
| DAT-005 | Refresh Tokens | SHA-256 hashed refresh tokens linked to user sessions. Never stored in plaintext. | IT Director | Critical | 7 days (auto-expiry) | PostgreSQL — `RefreshToken` table |
| DAT-006 | Application Logs | Winston-generated runtime logs including request IDs, errors, performance metrics. | IT Manager | Internal | 90 days | Container filesystem / log aggregator |
| DAT-007 | SOC Event Logs | Wazuh alerts, SIEM events, threat scenario logs. | CISO / SOC Lead | Confidential | 1 year | `securebank-soc-lab/logs/` |

---

## 3. Infrastructure Assets

| ID | Asset Name | Description | Owner | Classification | Environment | Notes |
|----|-----------|-------------|-------|---------------|-------------|-------|
| INF-001 | PostgreSQL Database (Production) | Primary relational database. PostgreSQL 16. Contains all customer and transaction data. | IT Manager | Critical | Production | Containerised via Docker Compose |
| INF-002 | PostgreSQL Database (Test) | Isolated test database on port 5433. Seeded with anonymised data only. | IT Manager | Internal | Development / CI | Not connected to production |
| INF-003 | Docker Host (Production) | Host running all production containers (API, database). | IT Manager | Critical | Production | Non-root user enforced in Dockerfile |
| INF-004 | CI/CD Pipeline (GitHub Actions) | Automated lint, type-check, test, build, Semgrep SAST, and Dependabot scanning. | IT Manager | Internal | CI/CD | `.github/workflows/ci.yml` |
| INF-005 | SOC Lab Docker Stack | Wazuh Manager, Wazuh Indexer, Wazuh Dashboard containers for security monitoring. | CISO / SOC Lead | Internal | Lab | `securebank-soc-lab/docker-compose.yml` |

---

## 4. Cryptographic & Secret Assets

| ID | Asset Name | Description | Owner | Classification | Rotation Cycle | Storage |
|----|-----------|-------------|-------|---------------|----------------|---------|
| SEC-001 | JWT Access Token Secret | 32+ char secret used to sign 15-minute access tokens. | IT Director | Critical | 90 days | Environment variable — `.env` / secrets manager |
| SEC-002 | JWT Refresh Token Secret | 32+ char secret used to sign 7-day refresh tokens. | IT Director | Critical | 90 days | Environment variable — `.env` / secrets manager |
| SEC-003 | Database Password | PostgreSQL connection password for the production database. | IT Manager | Critical | 90 days | Environment variable — `.env` / secrets manager |
| SEC-004 | bcrypt Key Factor | Cost factor (rounds: 12) used for password hashing. Not a secret but a security parameter. | IT Director | Internal | Review annually | Application config |

---

## 5. Third-Party & Supply Chain Assets

| ID | Asset Name | Vendor | Purpose | Classification | Risk Level | Monitoring |
|----|-----------|--------|---------|---------------|-----------|-----------|
| TPY-001 | Node.js Runtime | OpenJS Foundation | Application runtime | Internal | Medium | Dependabot |
| TPY-002 | Prisma ORM | Prisma Data | Database access layer | Internal | Medium | Dependabot |
| TPY-003 | Express.js | OpenJS Foundation | HTTP framework | Internal | Medium | Dependabot |
| TPY-004 | Helmet.js | Helmet Contributors | HTTP security headers | Internal | Low | Dependabot |
| TPY-005 | jsonwebtoken | Auth0 | JWT signing/verification | Critical | High | Dependabot + manual review |
| TPY-006 | bcrypt | bcrypt Contributors | Password hashing | Critical | High | Dependabot + manual review |
| TPY-007 | Zod | Colin McDonnell | Input validation | Internal | Low | Dependabot |
| TPY-008 | GitHub Actions | GitHub / Microsoft | CI/CD platform | Internal | Medium | Workflow audit |

---

## 6. Personnel Assets

| ID | Role | Responsibilities | Access Level |
|----|------|-----------------|-------------|
| PER-001 | CISO | Owns security programme, approves policies, manages GRC | Full (read-only production data) |
| PER-002 | IT Director | Oversees infrastructure, approves architecture changes | Admin |
| PER-003 | Backend Lead | Develops and maintains API, reviews security code | Developer |
| PER-004 | Frontend Lead | Develops and maintains Platform | Developer |
| PER-005 | SOC Lead | Manages SOC lab, responds to incidents | SOC |
| PER-006 | Data Protection Officer | Ensures POPIA compliance, manages data subject requests | Compliance Read |
| PER-007 | Internal Auditor | Conducts quarterly compliance audits | Auditor (read-only) |

---

## Asset Risk Summary

| Classification | Count | % of Total |
|---------------|-------|-----------|
| Critical | 12 | 44% |
| Confidential | 5 | 19% |
| Internal | 8 | 30% |
| Public | 2 | 7% |
| **Total** | **27** | **100%** |

---

## Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-07-21 | IT Manager | Initial register |
