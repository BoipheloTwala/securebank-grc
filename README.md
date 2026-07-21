# SecureBank — Governance, Risk & Compliance (GRC) Package

**Organisation:** SecureBank (Pty) Ltd  
**Package Version:** 1.0  
**Effective Date:** 21 July 2026  
**Package Owner:** Chief Information Security Officer (CISO)  
**Classification:** Internal — Restricted

---

## Purpose

This GRC package provides the complete security governance documentation for SecureBank. It demonstrates to regulators, auditors, and stakeholders that:

1. The organisation understands its assets and the risks attached to them.
2. Security controls exist and are actively maintained.
3. Applicable regulations are identified, mapped, and complied with.

---

## Repository Structure

```
securebank-grc/
├── 01-asset-inventory/
│   └── asset-register.md          # Full IT asset register with classification
├── 02-risk-register/
│   └── risk-register.md           # Identified risks, impact, likelihood, treatment
├── 03-framework-mapping/
│   ├── nist-csf-mapping.md        # NIST Cybersecurity Framework 2.0 control mapping
│   └── popia-mapping.md           # POPIA (Protection of Personal Information Act) mapping
├── 04-policies/
│   ├── password-policy.md         # Password & credential management policy
│   ├── access-control-policy.md   # Logical and physical access control policy
│   ├── incident-response-policy.md# Security incident classification and response
│   └── data-protection-policy.md  # Data handling, retention, and privacy policy
└── 05-audit-report/
    └── audit-report-2026-Q3.md    # Q3 2026 compliance audit findings & scores
```

---

## Regulatory Scope

| Framework | Applicability | Current Score |
|-----------|--------------|---------------|
| NIST CSF 2.0 | Voluntary best-practice baseline | 87% |
| POPIA | Mandatory — South African law | 92% |
| PCI DSS v4 | Applicable to payment card data | In progress |

---

## Related Repositories

| Repository | Description |
|------------|-------------|
| `securebank-api` | Core banking REST API (Node.js / TypeScript) |
| `securebank-platform` | Internal employee and auditor dashboard (React / Vite) |
| `securebank-soc-lab` | SOC detection lab with scenarios and playbooks |
| `securebank-vulnerability` | Vulnerability management tracking |

---

## Document Owners

| Document | Owner | Review Cycle |
|----------|-------|--------------|
| Asset Register | IT Manager | Quarterly |
| Risk Register | CISO | Quarterly |
| Framework Mapping | Compliance Officer | Annually |
| Policies | CISO / Policy Committee | Annually or upon material change |
| Audit Report | Internal Audit | Quarterly |

---

## Change Log

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-07-21 | CISO | Initial package creation |
