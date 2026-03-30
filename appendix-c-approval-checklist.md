# Appendix C — Documentation Approval Review Checklist

Performed **before development starts**, after completing Phase 1 and building the Roadmap.
All three roles must give approval. If there are comments — documentation is returned for revision to the corresponding specialist.

---

## Architect

- [ ] Architectural approach aligns with functional and non-functional requirements
- [ ] Technology stack is justified and documented in ADR
- [ ] All significant trade-offs are recorded in `docs/architecture/adr/`
- [ ] System components and their interactions are described in `docs/architecture/overview.md`
- [ ] Architectural risks are identified and have a mitigation plan
- [ ] Documentation is sufficient to begin development without architectural questions

---

## Developer

- [ ] All requirements are implementable within the chosen stack
- [ ] No contradictions between `docs/business/`, `docs/sa/`, `docs/architecture/`, `docs/qa/`, `docs/devops/`
- [ ] No ambiguities blocking development start
- [ ] `ROADMAP.md` covers all requirements and is broken into implementable stages
- [ ] Completion criteria for each Roadmap stage are clearly defined
- [ ] `docs/dev/technical-notes.md` has recorded all technical decisions and open questions

---

## QA Lead

- [ ] Acceptance criteria in Given/When/Then format are defined for each key requirement
- [ ] Testing strategy covers all levels: unit, integration, e2e, performance, security
- [ ] Test scenarios are implementable and do not depend on undefined data
- [ ] Non-functional requirements have measurable acceptance criteria
- [ ] `docs/qa/acceptance-criteria.md` is ready for use as TDD input

---

## Review Summary

| Role | Status | Date | Comment |
|------|--------|------|---------|
| Architect | ☐ Approved / ☐ Rejected | | |
| Developer | ☐ Approved / ☐ Rejected | | |
| QA Lead | ☐ Approved / ☐ Rejected | | |

**Development starts only with Approved from all three roles.**

---

## AI-Generated Documentation Verification (automatic in pipeline)

- [ ] Cross-validation: no contradictions between MD files of different roles
- [ ] Completeness: all mandatory sections are present per template
- [ ] Fact-check: specific claims (numbers, SLAs, constraints) are flagged for human confirmation
- [ ] Verification report is attached to the MR

---

## Infrastructure Checklist (verified automatically or by DevOps Lead)

- [ ] `AI_RULES.md` contains the mandatory corporate rule block (Appendix A)
- [ ] `.mcp.json` matches the project level (L1/L2/L3) per Appendix D
- [ ] `company-ai-toolkit` is connected as a git submodule and pinned to an approved version (Appendix F)
- [ ] Mandatory skills, agents, hooks, rules are imported from the repository
- [ ] CI/CD pipeline includes AI code review (Appendix G)
