# Appendix F — Corporate AI Configuration Repository

**Repository:** `company-ai-toolkit`
**Owner:** AI Implementation Department

---

## Repository Structure

```
company-ai-toolkit/
├── README.md
├── registry.md                          # Registry of all files with requirement markers
├── import.sh                            # Automatic import script for projects
│
├── skills/
│   ├── core/                            # Mandatory for all projects
│   │   ├── fix-issue/SKILL.md
│   │   ├── code-review/SKILL.md
│   │   └── security-review/SKILL.md
│   ├── backend/                         # For backend projects
│   │   ├── api-design/SKILL.md
│   │   └── db-migration/SKILL.md
│   ├── frontend/                        # For frontend projects
│   │   ├── component-review/SKILL.md
│   │   └── a11y-check/SKILL.md
│   └── devops/                          # For infrastructure projects
│       └── deploy/SKILL.md
│
├── agents/
│   ├── core/                            # Mandatory for all projects
│   │   ├── security-reviewer.md
│   │   └── code-reviewer.md
│   └── backend/
│       └── api-reviewer.md
│
├── hooks/
│   ├── core/                            # Mandatory for all projects
│   │   ├── lint-on-edit.json
│   │   ├── block-docs-write.json
│   │   └── test-after-impl.json
│   └── l3/                              # Additional for L3 projects
│       ├── block-external-requests.json
│       └── sast-on-commit.json
│
├── rules/
│   ├── core/                            # Mandatory for all projects
│   │   ├── security.md
│   │   └── testing.md
│   ├── backend/
│   │   └── api.md
│   ├── frontend/
│   │   └── components.md
│   └── l3/
│       └── restricted-security.md
│
└── mcp/
    ├── l1.mcp.json
    ├── l2.mcp.json
    └── l3.mcp.json
```

---

## File Registry (registry.md)

Each file is marked with YAML frontmatter:

```yaml
---
required-for:
  levels: [all]          # L1, L2, L3, or all
  types: [all]           # frontend, backend, fullstack, mobile, data, infra, or all
description: "Brief description of purpose"
---
```

### Mandatory Skills (core)

| Skill | Description | Levels | Project Types |
|-------|-------------|--------|---------------|
| `/fix-issue` | Issue → analysis → fix → tests → PR | all | all |
| `/code-review` | Code review by corporate checklist | all | all |
| `/security-review` | Code check for OWASP vulnerabilities | all | all |

### Mandatory Subagents (core)

| Agent | Description | Levels | Project Types |
|-------|-------------|--------|---------------|
| `security-reviewer` | Checks for injections, hardcoded secrets, authorization | all | all |
| `code-reviewer` | Checks SRP, code quality, patterns | all | all |

### Mandatory Hooks (core)

| Hook | Event | Description | Levels | Project Types |
|------|-------|-------------|--------|---------------|
| `lint-on-edit` | PostToolUse (Edit/Write) | Run linter after each file edit | all | all |
| `block-docs-write` | PreToolUse (Edit/Write) | Block writing to `docs/` without explicit approval | all | all |
| `test-after-impl` | PostToolUse (Edit/Write) | Run tests after implementation | all | all |

### Mandatory Hooks (L3)

| Hook | Event | Description | Levels | Project Types |
|------|-------|-------------|--------|---------------|
| `block-external-requests` | PreToolUse (Bash/Fetch) | Block external network requests | L3 | all |
| `sast-on-commit` | PostToolUse (Bash: git commit) | SAST scan on every commit | L3 | all |

### Mandatory Rules (core)

| Rule | Paths | Description | Levels | Project Types |
|------|-------|-------------|--------|---------------|
| `security.md` | all files | Basic security rules | all | all |
| `testing.md` | `**/*.test.*`, `**/*.spec.*` | Test writing rules | all | all |

---

## Mandatory File Contents

### Skill: `/fix-issue`

```markdown
---
name: fix-issue
description: Fix a task tracker issue following corporate standards
required-for:
  levels: [all]
  types: [all]
---
Analyze and fix the issue: $ARGUMENTS.

1. Use issue tracker to get the issue details
2. Understand the problem described in the issue
3. Search the codebase for relevant files using subagents
4. Write failing tests based on the issue description (Red)
5. Implement the minimal fix to make tests pass (Green)
6. Refactor without breaking tests (Refactor)
7. Ensure code passes linting and type checking
8. Run full test suite
9. Create an atomic commit with a descriptive message
10. Push and create a PR with the issue reference
```

### Skill: `/code-review`

```markdown
---
name: code-review
description: Review code changes against corporate quality standards
required-for:
  levels: [all]
  types: [all]
---
Review the code changes: $ARGUMENTS.

Checklist:
1. TDD compliance: are there tests for all new functionality?
2. Coverage: does coverage meet the 80% threshold?
3. Single responsibility: does each function/class have one job?
4. No over-engineering: is the solution minimal for the task?
5. Security: no hardcoded secrets, validated inputs, parameterized queries?
6. Git discipline: atomic commits, clear messages?
7. Documentation: ADR updated if architecture changed?

Report findings as: PASS / WARN / FAIL for each item.
```

### Skill: `/security-review`

```markdown
---
name: security-review
description: Review code for security vulnerabilities (OWASP Top 10)
required-for:
  levels: [all]
  types: [all]
---
Review the code for security vulnerabilities: $ARGUMENTS.

Check for:
1. Injection (SQL, XSS, command injection, path traversal)
2. Broken authentication and session management
3. Sensitive data exposure (PII, secrets in logs or code)
4. Security misconfiguration
5. Insecure deserialization
6. Components with known vulnerabilities
7. Insufficient logging and monitoring

Provide specific file:line references and suggested fixes.
Severity levels: CRITICAL / HIGH / MEDIUM / LOW.
```

### Subagent: `security-reviewer`

```markdown
---
name: security-reviewer
description: Reviews code for security vulnerabilities in isolation
tools: Read, Grep, Glob, Bash
model: most-capable
required-for:
  levels: [all]
  types: [all]
---
You are a senior security engineer. Review code for:
- Injection vulnerabilities (SQL, XSS, command injection)
- Authentication and authorization flaws
- Secrets or credentials in code
- Insecure data handling
- OWASP Top 10 violations

Provide specific file:line references and suggested fixes.
```

### Subagent: `code-reviewer`

```markdown
---
name: code-reviewer
description: Reviews code quality, patterns, and compliance with standards
tools: Read, Grep, Glob
model: balanced
required-for:
  levels: [all]
  types: [all]
---
You are a senior code reviewer. Review for:
- Single responsibility principle violations
- Over-engineering and speculative abstractions
- Missing tests for new functionality
- Duplicated code that should be extracted
- Compliance with project architecture in docs/architecture/

Report as: PASS / WARN / FAIL with specific file:line references.
```

### Hook: `lint-on-edit`

The linter command depends on the project stack. Examples:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "PROJECT_LINT_CMD=${AI_LINT_CMD:-npm run lint} && $PROJECT_LINT_CMD -- $AI_FILE_PATH 2>/dev/null || true"
          }
        ]
      }
    ]
  }
}
```

> The `AI_LINT_CMD` environment variable allows overriding the linter command for the project stack:
> - JS/TS: `npm run lint` / `yarn lint` / `pnpm lint`
> - Python: `ruff check` / `flake8`
> - Go: `golangci-lint run`
> - Java: `./gradlew checkstyleMain`
>
> Set in `.env` or project CI variables.

### Hook: `block-docs-write`

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "echo $AI_FILE_PATH | grep -q '^docs/' && echo 'BLOCK: docs/ files require explicit human approval. Ask the user first.' && exit 1 || exit 0"
          }
        ]
      }
    ]
  }
}
```

### Hook: `test-after-impl`

The test runner command depends on the project stack. Examples:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "echo $AI_FILE_PATH | grep -qv '\\.test\\.' && echo $AI_FILE_PATH | grep -qv '\\.spec\\.' && PROJECT_TEST_CMD=${AI_TEST_CMD:-npm test} && $PROJECT_TEST_CMD 2>/dev/null || true"
          }
        ]
      }
    ]
  }
}
```

> The `AI_TEST_CMD` environment variable allows overriding the test command:
> - JS/TS: `npm test` / `yarn test` / `vitest run`
> - Python: `pytest`
> - Go: `go test ./...`
> - Java: `./gradlew test`
>
> Set in `.env` or project CI variables.

### Rule: `security.md`

```markdown
---
required-for:
  levels: [all]
  types: [all]
---
# Security Rules

- Never hardcode credentials, API keys, tokens, or secrets
- All user inputs must be validated at system boundaries
- SQL and other queries must use parameterized statements only
- Never log sensitive data (passwords, tokens, PII)
- Use secure defaults: HTTPS, encrypted storage, minimal permissions
- Report any discovered vulnerability before proceeding with other work
```

### Rule: `testing.md`

```markdown
---
paths:
  - "**/*.test.*"
  - "**/*.spec.*"
  - "**/__tests__/**"
required-for:
  levels: [all]
  types: [all]
---
# Testing Rules

- Follow TDD: failing test first, then implementation, then refactor
- Tests must be based on acceptance criteria from docs/qa/acceptance-criteria.md
- Each test must test one behaviour — no multi-assertion tests without clear reason
- Use descriptive test names: "should [expected behaviour] when [condition]"
- Prefer real implementations over mocks where feasible
- CRUD operations are excluded from 80% coverage threshold
- Clean up test data after each test run
```

---

## Repository Versioning

`company-ai-toolkit` uses **semantic versioning** (SemVer):

| Change Type | Version | Examples |
|-------------|---------|---------|
| Breaking change | `MAJOR` (1.x.x → 2.0.0) | Hook format change, mandatory skill removal |
| New file / extension | `MINOR` (1.2.x → 1.3.0) | New skill, new subagent |
| Fix / clarification | `PATCH` (1.2.3 → 1.2.4) | Rule fix, hook command correction |

Each release is tagged in Git: `v1.3.0`. A `CHANGELOG.md` is maintained.

**Rule:** projects may remain on any version, but **MAJOR updates are mandatory** — the migration deadline is set by the AI Implementation Department.

---

## Project Integration (Git Submodule)

### Initial Setup

The toolkit is connected as a **git submodule** in the `.ai/toolkit` directory:

```bash
# 1. Add submodule (during project initialization)
git submodule add https://git.company.internal/ai/company-ai-toolkit .ai/toolkit

# 2. Pin to a specific version (tag)
cd .ai/toolkit
git checkout v1.3.0
cd ../..
git add .gitmodules .ai/toolkit
git commit -m "chore: add company-ai-toolkit v1.3.0 as submodule"

# 3. Run import — copy necessary files to .ai/
.ai/toolkit/import.sh --level L2 --type backend
git add .ai/
git commit -m "chore: import ai toolkit files for L2 backend"
```

After this, `.gitmodules` will contain:

```ini
[submodule ".ai/toolkit"]
    path = .ai/toolkit
    url = https://git.company.internal/ai/company-ai-toolkit
    branch = main
```

### Cloning the Project (for new team members)

```bash
git clone --recurse-submodules https://git.company.internal/team/project.git

# Or if cloned without the flag:
git submodule update --init --recursive
```

### Structure After Initialization

```
.ai/
├── toolkit/                     ← git submodule (company-ai-toolkit, pinned to tag)
│   ├── skills/
│   ├── agents/
│   ├── hooks/
│   ├── rules/
│   ├── mcp/
│   ├── registry.md
│   └── import.sh
├── skills/                      ← copied by import.sh from toolkit (under project control)
├── agents/
├── rules/
└── settings.json                ← hooks merged by import.sh
```

> Files in `.ai/skills/`, `.ai/agents/`, `.ai/rules/` are **copies**, not symlinks. This ensures compatibility across all OS including Windows.

---

## Updating the Toolkit in a Project

### Manual Update (recommended for MAJOR)

```bash
# 1. Update submodule to new version
cd .ai/toolkit
git fetch --tags
git checkout v2.0.0          # or specific tag
cd ../..

# 2. Run import to apply changes
.ai/toolkit/import.sh --level L2 --type backend --update

# 3. Review diff — especially important for MAJOR
git diff .ai/

# 4. Commit the update
git add .ai/
git commit -m "chore: update ai toolkit to v2.0.0

Breaking changes: <describe what changed>"
```

### Automatic Update via CI (for MINOR and PATCH)

A CI task runs on schedule (weekly) and creates an MR when new MINOR/PATCH versions are available:

#### GitLab CI

```yaml
update-ai-toolkit:
  stage: maintenance
  rules:
    - if: $CI_PIPELINE_SOURCE == "schedule"
  script:
    - cd .ai/toolkit && git fetch --tags
    - CURRENT=$(git describe --tags)
    - LATEST=$(git tag --sort=-v:refname | grep -v "^v[0-9]*\.0\.0$" | head -1)
    - |
      if [ "$CURRENT" != "$LATEST" ]; then
        git checkout $LATEST
        cd ../..
        .ai/toolkit/import.sh --level $PROJECT_LEVEL --type $PROJECT_TYPE --update
        git config user.email "ci@company.internal"
        git config user.name "AI Toolkit Bot"
        git checkout -b "chore/update-ai-toolkit-$LATEST"
        git add .ai/
        git commit -m "chore: update ai toolkit to $LATEST"
        git push origin "chore/update-ai-toolkit-$LATEST"
        # Create MR via API
        curl -X POST "$CI_API_V4_URL/projects/$CI_PROJECT_ID/merge_requests" \
          -H "PRIVATE-TOKEN: $BOT_TOKEN" \
          -d "source_branch=chore/update-ai-toolkit-$LATEST&target_branch=dev&title=chore: update ai toolkit to $LATEST"
      fi
  variables:
    PROJECT_LEVEL: "L2"     # override in project settings
    PROJECT_TYPE: "backend"  # override in project settings
```

#### GitHub Actions

```yaml
name: Update AI Toolkit
on:
  schedule:
    - cron: '0 9 * * 1'  # every Monday at 9:00

jobs:
  update-toolkit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive
          token: ${{ secrets.BOT_TOKEN }}

      - name: Check for toolkit updates
        run: |
          cd .ai/toolkit
          git fetch --tags
          CURRENT=$(git describe --tags)
          LATEST=$(git tag --sort=-v:refname | grep -v "^v[0-9]*\.0\.0$" | head -1)
          echo "CURRENT=$CURRENT" >> $GITHUB_ENV
          echo "LATEST=$LATEST" >> $GITHUB_ENV

      - name: Apply update if available
        if: env.CURRENT != env.LATEST
        run: |
          cd .ai/toolkit && git checkout ${{ env.LATEST }} && cd ../..
          .ai/toolkit/import.sh --level ${{ vars.PROJECT_LEVEL }} --type ${{ vars.PROJECT_TYPE }} --update

      - name: Create Pull Request
        if: env.CURRENT != env.LATEST
        uses: peter-evans/create-pull-request@v6
        with:
          token: ${{ secrets.BOT_TOKEN }}
          branch: chore/update-ai-toolkit-${{ env.LATEST }}
          base: dev
          title: "chore: update ai toolkit to ${{ env.LATEST }}"
          body: |
            Automatic update of company-ai-toolkit from ${{ env.CURRENT }} to ${{ env.LATEST }}.
            Review the diff in .ai/ before merging.
```

---

## Import into Project

### Automatic (recommended)

```bash
# From the project root — after adding the submodule
.ai/toolkit/import.sh --level L2 --type backend
```

The script:
1. Reads `registry.md`
2. Filters files by project level and type
3. Copies mandatory files to the project's `.ai/`
4. Generates `.mcp.json` by level
5. Merges hooks into `.ai/settings.json`
6. Outputs a list of imported and skipped files

### Manual

1. Copy files from `core/` to the project's `.ai/`
2. Copy files specific to the project type
3. For L3 — add files from `l3/`
4. Merge hooks into `.ai/settings.json`
5. Copy `.mcp.json` by level

---

## Specifics for L3 Projects

L3 projects have no access to external networks. For them:

1. The AI Implementation Department maintains a **self-hosted mirror** of `company-ai-toolkit` within the perimeter
2. The submodule URL points to the internal server: `https://git.company.internal/ai/company-ai-toolkit`
3. CI update automation works similarly but via internal CI
4. MAJOR updates undergo additional security review before publication to the mirror

---

## Update Policy

| Type | Application | Timeline |
|------|------------|----------|
| PATCH | Automatic MR from bot, merge without blockers | Within one week |
| MINOR | Automatic MR from bot, developer review | Within one sprint |
| MAJOR | Manual update, team + architect review | Deadline set by AI Implementation Dept |

> The AI Implementation Department publishes release notes in the corporate knowledge base with each toolkit release. MAJOR releases include a migration guide.
