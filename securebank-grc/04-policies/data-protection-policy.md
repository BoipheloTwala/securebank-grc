# SecureBank — Data Protection Policy

**Document ID:** GRC-POL-004  
**Version:** 1.0  
**Effective Date:** 21 July 2026  
**Owner:** Data Protection Officer (DPO)  
**Approved By:** CISO  
**Next Review:** 21 July 2027  
**Classification:** Internal  
**Scope:** All personal and financial information processed by SecureBank, including customer data, employee data, and third-party data.

---

## 1. Purpose

This policy establishes SecureBank's commitment to protecting the personal information of its customers, employees, and other data subjects in accordance with the **Protection of Personal Information Act 4 of 2013 (POPIA)** and international best practices. It defines how personal information is collected, used, stored, shared, protected, and deleted.

---

## 2. Definitions

| Term | Definition |
|------|-----------|
| **Personal Information** | Information that identifies or can identify a natural person — including name, email address, ID number, financial records, and IP addresses. |
| **Processing** | Any operation performed on personal information — collection, storage, use, modification, disclosure, destruction. |
| **Responsible Party** | SecureBank (Pty) Ltd — the entity that determines the purpose and means of processing personal information. |
| **Data Subject** | The person whose personal information is being processed (e.g. a SecureBank customer). |
| **Operator** | A third party that processes personal information on behalf of SecureBank under a data processing agreement. |
| **Special Personal Information** | Sensitive categories — health, race, religion, political opinion, biometrics, criminal history — subject to stricter controls under POPIA. |

---

## 3. Principles of Data Protection

SecureBank processes personal information in accordance with the following POPIA conditions:

| Condition | Principle |
|-----------|-----------|
| Accountability | SecureBank takes responsibility for compliance and has appointed a DPO. |
| Processing Limitation | Personal information is processed lawfully, with a valid legal basis. |
| Purpose Specification | Information is collected for a specific, explicitly defined purpose. |
| Further Processing Limitation | Personal information is not processed in ways incompatible with the original purpose. |
| Information Quality | Personal information is accurate, complete, and kept up to date. |
| Openness | Data subjects are informed about the processing of their information. |
| Security Safeguards | Appropriate technical and organisational measures protect personal information. |
| Data Subject Participation | Data subjects can access, correct, and object to the processing of their information. |

---

## 4. Legal Basis for Processing

| Data Category | Legal Basis (POPIA Section 11) | Justification |
|--------------|-------------------------------|--------------|
| Customer account data | Section 11(1)(b) — Contract | Necessary to provide banking services |
| Transaction records | Section 11(1)(c) — Legal obligation | SARS / FICA retention requirements |
| Audit logs | Section 11(1)(f) — Legitimate interest | Security monitoring; fraud detection |
| Employee data | Section 11(1)(b) — Contract | Employment contract |
| Marketing communications | Section 11(1)(a) — Consent | Explicit opt-in required |

**SecureBank does not process Special Personal Information** as defined under POPIA Chapter 3, Section 26. If this changes, a separate processing justification and enhanced safeguards must be approved by the DPO and CISO before processing begins.

---

## 5. Data Inventory and Classification

| Data Category | Classification | Retention Period | Disposal Method |
|--------------|---------------|-----------------|-----------------|
| Customer PII (name, email) | Critical | 5 years post-account closure | Secure deletion; PostgreSQL record anonymisation |
| Account records | Critical | 7 years (SARS requirement) | Secure deletion after retention period |
| Transaction records | Critical | 7 years (SARS requirement) | Secure deletion after retention period |
| Audit logs | Confidential | 3 years | Secure deletion |
| Authentication tokens (hashed) | Critical | 7 days (auto-expiry) | Auto-deleted by database |
| Employee records | Confidential | Duration of employment + 5 years | Secure deletion |
| Application logs | Internal | 90 days | Log rotation |

---

## 6. Data Collection

- Personal information is collected **only** for the purposes stated at the point of collection.
- A **privacy notice** is presented at the point of collection (customer registration). *(Action: Implement by Q3 2026.)*
- Customers are informed of: what data is collected, why it is collected, how it will be used, who it may be shared with, and their data subject rights.
- **Consent** is obtained where required. Consent must be specific, informed, and freely given.
- **Special personal information** is not collected without explicit consent and DPO approval.

---

## 7. Data Minimisation

SecureBank collects the **minimum personal information** necessary to provide banking services:

- Registration requires only: first name, last name, email address, and password.
- Unnecessary fields are not collected.
- Request body size is limited to 10 KB to prevent excessive data submission.
- Sensitive fields (passwords, tokens) are stripped from all API responses.

---

## 8. Data Accuracy

- Customers may update their personal information via the SecureBank Platform.
- Data subject correction requests submitted via the DSAR process will be actioned within **30 days**.
- SecureBank will correct inaccurate data and notify any third parties to whom the data was previously disclosed.

---

## 9. Data Security

### 9.1 Technical Controls

| Control | Implementation |
|---------|--------------|
| Passwords | bcrypt hashed (rounds: 12) — never stored in plaintext |
| Tokens | SHA-256 hashed refresh tokens — never stored in plaintext |
| Data in transit | TLS encryption enforced for all API communications |
| Data at rest | PostgreSQL database encryption-at-rest *(Planned — Q3 2026)* |
| Access control | RBAC — ADMIN, AUDITOR roles (least privilege enforced per role) |
| Audit logging | All sensitive operations logged with actor and timestamp |
| Security headers | Helmet.js: HSTS, CSP, X-Frame-Options, X-XSS-Protection, noSniff |
| Rate limiting | Auth: 10 req/window; Global: 100 req/window |

### 9.2 Organisational Controls

- Access to customer data is restricted to employees with a business need.
- All access to production customer data is audit-logged.
- Employees handling personal information complete annual data protection training.
- Third parties with access to personal information sign a Data Processing Agreement (DPA).

---

## 10. Data Sharing

SecureBank does **not** sell customer personal information to third parties.

Personal information may be disclosed to:

| Recipient | Purpose | Legal Basis |
|-----------|---------|-------------|
| South African Revenue Service (SARS) | Statutory reporting | Legal obligation |
| Financial Intelligence Centre (FIC) | AML/CFT reporting | Legal obligation |
| Law enforcement | Court order or warrant | Legal obligation |
| Auditors | External audit | Legitimate interest |
| IT service providers | System operation | Contract + DPA |

All disclosures to third parties are documented in the Third-Party Disclosure Register (maintained by DPO).

---

## 11. Cross-Border Data Transfers

Personal information is **not transferred outside South Africa** without:

1. The recipient country providing an adequate level of protection (as determined by the Information Regulator); or
2. Explicit consent from the data subject; or
3. Binding corporate rules or standard contractual clauses approved by the Information Regulator.

---

## 12. Data Subject Rights

Data subjects have the following rights under POPIA. Requests must be submitted to the DPO (dpo@securebank.co.za) and actioned within the stated timeframe.

| Right | Description | Response Time |
|-------|-------------|--------------|
| **Right of Access** | Request a copy of all personal information held. | 30 days |
| **Right to Correction** | Request correction of inaccurate personal information. | 30 days |
| **Right to Deletion** | Request deletion of personal information (subject to retention obligations). | 30 days |
| **Right to Object** | Object to processing based on legitimate interest. | 30 days |
| **Right to Complain** | Lodge a complaint with the Information Regulator if rights are not respected. | Immediate referral |

Data Subject Access Request (DSAR) procedure: *(Document pending — Target: Q3 2026)*

---

## 13. Data Breach Response

SecureBank's obligations upon discovery of a data breach involving personal information:

| Obligation | Deadline | Owner |
|------------|----------|-------|
| Internal incident declared | Immediately | SOC Lead |
| Breach assessment completed (scope, affected data subjects, risk of harm) | 24 hours | DPO + CISO |
| Notification to Information Regulator (Form 2) | 72 hours from discovery | DPO |
| Notification to affected data subjects (if risk of harm is high) | As soon as reasonably practicable | DPO |
| Incident report finalised | 10 business days | DPO + CISO |

The Information Regulator can be reached at:  
**The Information Regulator (South Africa)**  
JD House, 27 Stiemens Street, Braamfontein, Johannesburg  
complaints.IR@justice.gov.za  
+27 10 023 5200

---

## 14. Data Retention and Disposal

Personal information is retained for the minimum period necessary and then securely disposed of:

| Method | Applicability |
|--------|--------------|
| Secure database deletion (PostgreSQL `DELETE`) | Customer records after retention period |
| Record anonymisation | Where deletion is not possible due to referential integrity |
| Overwriting / cryptographic erasure | Backup media and archived files |
| Physical destruction | Printed documents containing personal information |

Disposal actions are logged in the Data Disposal Register (maintained by IT Manager).

---

## 15. Roles and Responsibilities

| Role | Responsibility |
|------|--------------|
| Data Protection Officer | Policy owner; DSAR management; breach notifications; regulatory liaison |
| CISO | Technical security controls; incident response; policy approver |
| IT Director | System security implementation; access controls |
| All employees | Comply with this policy; report data concerns to DPO |
| Developers | Implement privacy-by-design in all new features |

---

## Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 1.0 | 2026-07-21 | DPO | Initial policy |
