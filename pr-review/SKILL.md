---
name: pr-review
description: Perform thorough pull request reviews covering correctness, security, performance, observability, backward compatibility, data migrations, and developer ergonomics. Use when the user asks to review a PR, examine a pull request, or requests a code review of a branch or GitHub PR link.
---

# PR Review

Perform a structured, multi-dimensional review of a pull request. The review is **advisory, not authoritative** -- the human reviewer verifies all claims.

## Workflow

### 1. Gather the PR Context

Determine the source of the PR:

**GitHub PR URL or number provided:**
```bash
gh pr view <number-or-url> --json title,body,additions,deletions,changedFiles,baseRefName,headRefName
gh pr diff <number-or-url>
```

**Local branch (no PR link):**
```bash
git log --oneline main..HEAD          # commit list
git diff main...HEAD                  # full diff
git diff main...HEAD --stat           # file-level summary
```

Collect:
- PR title & description (the "why")
- Full diff (the "what")
- File-level stats (additions / deletions / files changed)

### 2. Assess Scope & Reviewability

Before diving into the code, evaluate the PR holistically:

- Is the PR small enough to review confidently? If it touches > ~400 lines or > ~10 files, flag it and suggest splitting.
- Does every change align with the stated goal? Call out any "drive-by" refactors that are not justified in the description.
- Is there a clear rollout plan and expected user impact? If the description is silent on this, ask.

### 3. Run the Review Checklist

Walk through **each category** below. For every finding, cite the exact file and line range (or patch hunk) so the author can locate it instantly.

For the detailed checklist items under each category, see [CHECKLIST.md](CHECKLIST.md).

#### Correctness & Edge Cases
- Boundary cases: nulls, empty collections, timezones, retries, partial failures
- Explicit failure modes: what happens when a dependency is down?
- Idempotency: are retries safe (no double effects)?

#### Security & Privacy
- No secrets logged or committed
- Input validation + output encoding where applicable
- Least privilege for new permissions/roles
- PII handling: logging/redaction, retention, access controls

#### Observability
- Structured, meaningful logs (not noisy)
- Key metrics: counters/timers relevant to the change (SLO/SLA impact)
- New flows tied into existing traces (if tracing is used)
- Alerts are actionable (no "FYI" alerts)

#### Performance & Cost
- Hot-path code measured or at least reasoned about
- Query/index checks: N+1, missing indexes, pagination
- Caching strategy and cache invalidation story
- Cloud cost impact: extra calls, bigger payloads, heavier compute

#### API & Backward Compatibility
- Non-breaking changes where possible
- Versioning / deprecation strategy if breaking is unavoidable
- Migration steps for schema/config changes

#### Data & Migrations
- Migrations are backward compatible (expand -> migrate -> contract)
- Rollback strategy for data changes (not just code rollback)
- Safety for long-running migrations: batching, locks, timeouts

#### Docs & Developer Ergonomics
- README / runbook / ADR updates when behavior changes
- "How to test locally" is clear
- Notable operational steps called out (env vars, toggles, backfills)

### 4. Format the Review

Structure the output as follows:

```
## PR Review: <PR title>

### Summary
<1-2 sentence overview of what the PR does and overall impression>

### Scope & Reviewability
<Assessment of PR size, focus, rollout plan>

### Findings

#### Critical (must fix before merge)
- **[file:line]** <description of issue and suggested fix>

#### Suggestions (consider improving)
- **[file:line]** <description and rationale>

#### Nits (optional, low priority)
- **[file:line]** <minor style or clarity improvement>

### Checklist Summary
| Category                    | Status | Notes              |
|-----------------------------|--------|--------------------|
| Correctness & Edge Cases    | pass/warn/fail | ...       |
| Security & Privacy          | pass/warn/fail | ...       |
| Observability               | pass/warn/fail | ...       |
| Performance & Cost          | pass/warn/fail | ...       |
| API & Backward Compat       | pass/warn/fail | ...       |
| Data & Migrations           | pass/warn/fail | ...       |
| Docs & Developer Ergonomics | pass/warn/fail | ...       |

### Verdict
<approve / request-changes / comment-only>
<Brief rationale>
```

### 5. Review Principles

- **Cite exact locations**: every finding references a file and line range or patch hunk.
- **Explain the "why"**: don't just say "this is wrong" -- explain the risk or impact.
- **Be proportional**: a one-line typo fix doesn't need a performance analysis. Skip categories that are clearly not applicable.
- **Separate blocking from non-blocking**: use the Critical / Suggestion / Nit tiers so the author knows what must change.
- **Acknowledge good work**: if something is well-done, say so briefly.
