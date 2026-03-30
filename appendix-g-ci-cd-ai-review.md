# Appendix G — CI/CD AI Code Review Integration

Automatic AI code review in the MR pipeline via non-interactive CLI AI agent mode.

---

## Principle

When an MR is created targeting the `dev` branch, the CI/CD pipeline automatically runs an AI code review. The result is a comment on the MR with findings. The developer receives findings before human review.

**Writer/Reviewer pattern:** AI review is performed in a fresh session — AI is not biased toward code it may have written in another session.

> **CLI abstraction:** the examples below use `ai-cli --prompt` as a generalized command. During implementation, replace with the specific tool: `claude -p` (Claude Code), `codex --prompt` (Codex), or equivalent. Adapt flags (`--output-format`, `--allowed-tools`) to the chosen CLI.

---

## Pipeline Configuration

### GitLab CI

```yaml
ai-code-review:
  stage: review
  rules:
    - if: $CI_MERGE_REQUEST_TARGET_BRANCH_NAME == "dev"
  script:
    - |
      ai-cli --prompt "
        You are a code reviewer. Review the diff for this MR.

        Run: git diff origin/dev...HEAD

        Review checklist:
        1. TDD: are there tests for all new functionality?
        2. Coverage: does the change maintain 80% threshold?
        3. Security: no hardcoded secrets, validated inputs, parameterized queries?
        4. Single responsibility: minimal changes for the task?
        5. Git discipline: atomic commits, clear messages?
        6. Documentation: ADR updated if architecture changed?

        Output format:
        - PASS / WARN / FAIL for each item
        - For WARN and FAIL: file:line and description
        - Summary: APPROVED / CHANGES REQUESTED
      " --output-format json > review.json
    - # Parse and post comment to MR
    - python scripts/post-review-comment.py review.json
  allow_failure: true
```

### GitHub Actions

```yaml
name: AI Code Review
on:
  pull_request:
    branches: [dev]

jobs:
  ai-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: AI Code Review
        run: |
          ai-cli --prompt "
            You are a code reviewer. Review the diff for this PR.
            Run: git diff origin/dev...HEAD

            Review checklist:
            1. TDD: are there tests for all new functionality?
            2. Coverage: does the change maintain 80% threshold?
            3. Security: no hardcoded secrets, validated inputs, parameterized queries?
            4. Single responsibility: minimal changes for the task?
            5. Git discipline: atomic commits, clear messages?
            6. Documentation: ADR updated if architecture changed?

            Output as markdown comment.
          " --output-format text > review.md

      - name: Post review comment
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const review = fs.readFileSync('review.md', 'utf8');
            github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: `## AI Code Review\n\n${review}`
            });
```

---

## Auto-Fix

To automatically fix AI review findings, add a second step:

```bash
ai-cli --prompt "
  Read review.json. Fix all WARN and FAIL items.
  Follow TDD: update tests first if needed.
  Run tests after fixing. Commit fixes as 'fix: address AI review feedback'.
" --allowed-tools "Read,Edit,Bash(git diff *),Bash(git commit *),Bash(${PROJECT_TEST_CMD:-npm test} *)"
```

> Replace `${PROJECT_TEST_CMD}` with the test runner command for your stack.

**Limitations:**
- `--allowed-tools` restricts permitted operations
- Auto-fix does not replace human review — final approval always rests with the developer
- For L3 projects — use a self-hosted CLI agent

---

## Metrics

Collect from each AI review:
- Number of findings (PASS / WARN / FAIL)
- % MRs passing without findings
- Average AI review time (for cost estimation)
- Token cost per review
