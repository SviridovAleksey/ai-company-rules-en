# AI-Assisted Development Standard

Corporate standard for development using AI tools.

**Version:** 0.9 (beta)

## About

The standard describes the process of product development using AI: from requirements elaboration to deployment. It is not tied to specific tools or technology stacks.

Key idea: AI acts as an executor but does not replace engineering culture, architectural thinking, and specialist accountability.

## Structure

| Document | Description |
|----------|---------|
| [AI_STANDARD.md](AI_STANDARD.md) | Main standard document |

### Appendices

| Appendix | Description | Status |
|----------|---------|--------|
| [A — Mandatory Rules](appendix-a-mandatory-rules.md) | Rule block for each project's AI_RULES.md | Ready |
| [B — Role Prompts](appendix-b-role-prompts.md) | Templates for BA, SA, Architect, QA, DevOps, Developer, Support | Ready |
| [C — Approval Checklist](appendix-c-approval-checklist.md) | Documentation review checklist before development starts | Ready |
| [D — MCP Configurations](appendix-d-mcp-configs.md) | .mcp.json templates for L1, L2, L3 levels | Ready |
| [E — Security](appendix-e-security-policies.md) | Security and compliance policies | Placeholder |
| [F — Toolkit Repository](appendix-f-toolkit-repository.md) | company-ai-toolkit structure: skills, agents, hooks, rules | Ready |
| [G — AI Code Review in CI/CD](appendix-g-ci-cd-ai-review.md) | AI review integration in pipeline (GitLab CI, GitHub Actions) | Ready |

## Core Concepts

**Three project levels:**
- **L1 (Public)** — any approved AI tools
- **L2 (Confidential)** — approved cloud tools
- **L3 (Restricted)** — self-hosted models only, within the perimeter

**Seven process phases:**
1. Documentation elaboration (BA → SA → Architect → QA → DevOps → Developer)
2. Roadmap construction
3. Documentation approval review
4. TDD development with AI code review
5. Support and incoming requests
6. Bug fixing process
7. Refactoring and technical debt

**Key principles:**
- AI is driven by documentation — product quality depends on documentation quality
- Single owner principle — each MD file has one accountable specialist (human)
- Writer/Reviewer pattern — AI review in a separate session from development
- TDD without compromise — test first, then implementation
- Context as a resource — AI context window management

## Quick Start

1. Read the [main document](AI_STANDARD.md)
2. Determine the project level (L1/L2/L3)
3. Insert [mandatory rules](appendix-a-mandatory-rules.md) into the project's AI_RULES.md
4. Connect [MCP servers](appendix-d-mcp-configs.md) according to the project level
5. Import [toolkit](appendix-f-toolkit-repository.md) as a git submodule

## Feedback

The standard is in beta. Issues and pull requests are welcome.
