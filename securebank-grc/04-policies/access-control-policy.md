# SecureBank — Access Control Policy

**Document ID:** GRC-POL-002  
**Version:** 1.0  
**Effective Date:** 21 July 2026  
**Owner:** CISO  
**Approved By:** CISO  
**Next Review:** 21 July 2027  
**Classification:** Internal  
**Scope:** All SecureBank employees, contractors, systems, applications, and data.

---

## 1. Purpose

This policy establishes the principles and controls for granting, managing, reviewing, and revoking access to SecureBank systems and data. The goal is to ensure that individuals have access only to the resources they need to perform their role (least privilege) and that access is properly authorised, documented, and regularly reviewed.

---

## 2. Principles

| Principle | Definition |
|-----------|-----------|
| **Least Privilege** | Access is granted only to the minimum resources required to perform a job function. |
| **Need-to-Know** | Access to confidential or critical data requires a demonstrable business need. |
| **Separation of Duties** | Conflicting responsibilities are split across multiple individuals to prevent fraud or error. |
| **Default Deny** | All access is denied by default; access is explicitly granted and documented. |
| **Accountability** | All access and privileged actions are logged and attributable to an individual. |

---

## 3. Access Classification

### 3.1 System Roles

| Role | Description | Accessible Resources |
|------|-------------|---------------------|
| **ADMIN** | Internal operations staff | All user data, account management, audit logs, user lock/unlock |
| **AUDITOR** | Internal or external auditor | Read-only access to audit logs, compliance reports, and user data |

### 3.2 Access Tiers

| Tier | Examples | Authentication Requirement |
|------|---------|--------------------------|
| Public | API docs, marketing site | None |
| Authenticated | Employee banking operations | JWT access token |
| Privileged | ADMIN operations | JWT + ADMIN role |
| Audit | Read-only audit access | JWT + AUDITOR role |

---

## 4. Access Provisioning

### 4.1 New User Access

1. Access requests must be submitted to the IT Director and approved before provisioning.
2. The minimum required role must be assigned based on the employee's job function.
3. ADMIN role assignment requires dual approval: IT Director and CISO.
4. All new accounts are provisioned with a temporary credential that must be changed on first use.

### 4.2 Employee Access

1. Access is provisioned based on the job description and team approval.
2. Privileged (ADMIN/AUDITOR) access requires written justification and CISO approval.
3. All provisioning actions are recorded in the access provisioning log.

### 4.3 Contractor and Third-Party Access

1. Third-party access must be limited to the specific systems and data required.
2. Access is time-bound and reviewed at contract renewal.
3. A signed confidentiality and acceptable use agreement is required before access is granted.
4. All third-party access is revoked immediately upon contract termination.

---

## 5. Access Review

| Review Type | Frequency | Responsible |
|-------------|-----------|-------------|
| Privileged access review | Quarterly | IT Director + CISO |
| General employee access review | Annually | IT Manager |
| Dormant account review (no login > 90 days) | Quarterly | IT Manager |
| Dormant AUDITOR access review | Annually | IT Director + CISO |

---

## 6. Access Revocation

### 6.1 Employee Departure (Immediate on Day of Departure)

1. IT Manager revokes all system access including application accounts, VPN, and email.
2. IT Director deactivates the employee's ADMIN/AUDITOR API role: `PATCH /api/v1/admin/users/:id/deactivate`.
3. Any shared secrets or API keys the employee had access to are rotated within 24 hours.
4. All active sessions are invalidated (refresh tokens revoked).

### 6.2 Suspected Compromise

1. Account is locked immediately via ADMIN API or by the IT Director.
2. All active tokens associated with the account are invalidated.
3. Password is reset and the user is notified.
4. Incident response procedure (GRC-POL-003) is triggered.

---

## 7. Technical Access Controls

### 7.1 API Authentication

All API requests to protected endpoints must include a valid JWT access token in the `Authorization: Bearer <token>` header. Requests without a valid token are rejected with HTTP 401.

### 7.2 Role-Based Access Control (RBAC)

Role enforcement is implemented in `securebank-api/src/middleware/auth.middleware.ts`. Each protected route declares its minimum required role. Requests with insufficient roles are rejected with HTTP 403.

### 7.3 Rate Limiting

| Scope | Limit |
|-------|-------|
| Global | 100 requests per window |
| Authentication endpoints | 10 requests per window |
| Transaction endpoints | Configurable per route |

### 7.4 Session Tokens

- Access tokens expire after **15 minutes**.
- Refresh tokens expire after **7 days** and are stored as SHA-256 hashes.
- Tokens are revoked on explicit logout.
- Account deactivation invalidates all associated sessions.

---

## 8. Privileged Access Management

| Requirement | Standard |
|-------------|---------|
| ADMIN credentials | Not shared; each admin has individual credentials |
| ADMIN session | Additional verification at privilege elevation (MFA — Planned Q3 2026) |
| Privileged actions | All ADMIN API calls are audit-logged with actor, timestamp, and IP |
| Privileged access hours | ADMIN access permitted 06:00–22:00 SAST; off-hours access triggers SOC alert |

---

## 9. Physical Access Controls

| Area | Control |
|------|---------|
| Server room / data centre | Access card required; access log maintained |
| Developer workstations | Screen lock on inactivity (maximum 5 minutes); full-disk encryption |
| Remote access | VPN required for all remote access to internal systems |

---

## 10. Monitoring and Auditing

- All authentication events (login, logout, failed login) are logged by `audit.middleware.ts`.
- All ADMIN actions are logged with full request context.
- Audit logs are retained for a minimum of 3 years (GRC-AST-001 DAT-004).
- Logs are reviewed by the SOC Lead weekly and by the Internal Auditor quarterly.

---

## 11. Non-Compliance

Unauthorised access attempts or policy violations may result in:

- Immediate account suspension.
- Disciplinary action up to and including dismissal.
- Criminal referral in cases of unauthorised access to banking data (Computer Misuse provisions; POPIA Section 99).

---

## Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-07-21 | CISO | Initial policy |
