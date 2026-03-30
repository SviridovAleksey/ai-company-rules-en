# Appendix E — Security and Compliance Policies

**Status:** Placeholder — requires completion by the security department
**Owner:** Information Security / AI Implementation Department

---

## E.1 Data Classification

> TODO: Describe the corporate data classification and its mapping to project levels L1/L2/L3.

| Data Class | Description | Project Level |
|-----------|-------------|---------------|
| Public | | L1 |
| Internal | | L1 |
| Confidential | | L2 |
| Restricted | | L3 |

---

## E.2 Compliance Requirements

> TODO: Specify applicable regulatory standards and requirements.

- [ ] GDPR
- [ ] PCI DSS
- [ ] HIPAA
- [ ] Internal InfoSec policies
- [ ] Other: ___

---

## E.3 AI Tool Requirements

> TODO: Complete based on evaluation of each tool by the security department.

### Mandatory Conditions for Tool Approval

- [ ] Data retention policy
- [ ] Data storage jurisdiction
- [ ] Signed DPA (Data Processing Agreement)
- [ ] Data encryption in transit and at rest
- [ ] SOC 2 / ISO 27001 certification
- [ ] Ability to disable model training on user data

### Approved Tool Status

| Tool | DPA | Jurisdiction | Certification | Training on Data | Status |
|------|-----|-------------|---------------|-----------------|--------|
| ChatGPT (OpenAI) | TODO | TODO | TODO | TODO | Pending |
| Claude (Anthropic) | TODO | TODO | TODO | TODO | Pending |
| Cursor | TODO | TODO | TODO | TODO | Pending |
| Claude Code | TODO | TODO | TODO | TODO | Pending |
| Codex (OpenAI) | TODO | TODO | TODO | TODO | Pending |

---

## E.4 Self-Hosted LLM Requirements (L3)

> TODO: Describe requirements for deployment and operation of local models.

- Allowed models: ___
- Infrastructure requirements: ___
- Network isolation requirements: ___
- Model update process: ___
- Usage audit: ___

---

## E.5 Incident Management

> TODO: Describe the incident response process for AI tool-related incidents.

### What Constitutes an Incident
- Sending L3 data to an external AI tool
- Leaking credentials or secrets via an AI tool
- Publishing confidential data as a result of AI operations
- Using an unapproved AI tool on an L2/L3 project

### Response Process

> TODO: Complete together with the security department.

1. Detection → immediately report to: ___
2. Documentation: what / when / which tool / what data
3. Isolation: ___
4. Investigation: ___
5. Remediation and post-mortem: ___

---

## E.6 AI Tool Usage Audit

> TODO: Define logging and audit requirements.

- What is logged: ___
- Where logs are stored: ___
- Retention period: ___
- Who has access to logs: ___
- Audit frequency: ___
