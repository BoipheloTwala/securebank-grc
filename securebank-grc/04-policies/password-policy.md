# SecureBank — Password Policy

**Document ID:** GRC-POL-001  
**Version:** 1.0  
**Effective Date:** 21 July 2026  
**Owner:** CISO  
**Approved By:** CISO  
**Next Review:** 21 July 2027  
**Classification:** Internal  
**Scope:** All SecureBank employees, contractors, systems, and customer-facing authentication mechanisms.

---

## 1. Purpose

This policy establishes minimum requirements for the creation, management, protection, and rotation of passwords and credentials used to access SecureBank systems, applications, and data. Strong password practices are a foundational control against unauthorised access.

---

## 2. Scope

This policy applies to:

- All employees, contractors, and third-party users with access to SecureBank systems.
- All customer accounts on the SecureBank Platform and API.
- All service accounts, API keys, and machine credentials.
- All environments: production, staging, development, and lab.

---

## 3. Password Requirements

### 3.1 Customer Account Passwords

| Requirement | Standard |
|-------------|---------|
| Minimum length | 12 characters |
| Complexity | Must include: uppercase letter, lowercase letter, digit, special character |
| Maximum length | 128 characters |
| Common password check | Verified against HaveIBeenPwned API (Planned — Q3 2026) |
| Password hints | Prohibited |
| Password history | Last 5 passwords may not be reused |
| Expiry | No mandatory expiry (aligned with NIST SP 800-63B) |
| Change on compromise | Required immediately upon suspected compromise |

### 3.2 Employee / Internal Account Passwords

| Requirement | Standard |
|-------------|---------|
| Minimum length | 14 characters |
| Complexity | Must include: uppercase letter, lowercase letter, digit, special character |
| Privileged accounts (ADMIN) | Minimum 20 characters or passphrase |
| Password manager | Mandatory for all employee accounts |
| Shared passwords | Prohibited |
| Default passwords | Must be changed immediately upon provisioning |

### 3.3 Service Accounts & Secrets

| Requirement | Standard |
|-------------|---------|
| JWT signing secrets | Minimum 32 characters, cryptographically random |
| Database passwords | Minimum 24 characters, cryptographically random |
| API keys | Minimum 32 characters, cryptographically random |
| Storage | Environment variables or approved secrets manager only. Never in source code or version control. |
| Rotation cycle | 90 days maximum |

---

## 4. Password Storage

### 4.1 Customer Passwords

All customer passwords MUST be:

1. Hashed using **bcrypt** with a minimum of **12 rounds** before storage.
2. Never stored in plaintext in any database, log file, or backup.
3. Never transmitted in cleartext. TLS required for all password transmission.
4. Never logged, including in error messages or debug output.

Current implementation: `bcrypt` with `BCRYPT_ROUNDS=12` in the SecureBank API (`securebank-api/src/services/auth.service.ts`).

### 4.2 System Secrets

All system secrets (JWT secrets, database passwords) MUST be:

1. Stored as environment variables (`.env`) or in an approved secrets manager.
2. Never committed to version control (`.gitignore` must exclude `.env` files).
3. Rotated every 90 days and immediately upon suspected compromise.

---

## 5. Account Lockout

To protect against brute force attacks:

| Parameter | Value |
|-----------|-------|
| Maximum failed login attempts | 5 consecutive failures |
| Account lock duration | 30 minutes (auto-unlock) |
| Manual unlock | Available via ADMIN API (`PATCH /api/v1/admin/users/:id/unlock`) |
| Rate limiting | Auth endpoints: maximum 10 requests per window |

---

## 6. Multi-Factor Authentication (MFA)

| Scope | Requirement | Target Date |
|-------|-------------|-------------|
| Customer accounts | MFA optional (encouraged) | Q3 2026 |
| Admin accounts | MFA mandatory | Q3 2026 |
| Employee systems access | MFA mandatory | Q3 2026 |

Until MFA is implemented, short-lived JWT access tokens (15 minutes) and refresh token rotation mitigate the risk of token compromise.

---

## 7. Session Management

| Parameter | Value |
|-----------|-------|
| Access token lifetime | 15 minutes |
| Refresh token lifetime | 7 days |
| Refresh token storage | SHA-256 hash only (never plaintext) |
| Token revocation | Available via logout endpoint (`POST /api/v1/auth/logout`) |
| Concurrent sessions | Allowed (each session has independent refresh token) |

---

## 8. Roles and Responsibilities

| Role | Responsibility |
|------|--------------|
| CISO | Policy owner; approves changes; reviews annually |
| IT Director | Enforces technical controls; approves exceptions |
| Developers | Implement password controls as per this policy |
| All Employees | Comply with password requirements; report suspected compromises |
| Help Desk | Execute account unlock and password reset procedures |

---

## 9. Non-Compliance

Violations of this policy may result in:

- Access revocation pending investigation.
- Disciplinary action up to and including dismissal.
- Regulatory reporting if the violation resulted in a data breach (POPIA Section 22).

---

## 10. Exceptions

Exceptions to this policy must be:

1. Submitted in writing to the CISO.
2. Accompanied by a compensating control.
3. Approved by the CISO and documented.
4. Reviewed at each annual policy review.

---

## Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-07-21 | CISO | Initial policy |
