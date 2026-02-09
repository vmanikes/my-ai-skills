# PR Review Checklist (Detailed Reference)

This is the expanded checklist referenced from SKILL.md. Use it as a deep-dive reference when reviewing each category.

---

## Scope & Intent

- [ ] PR is small enough to review confidently (< ~400 LOC changed, < ~10 files). If not, can it be split?
- [ ] Every change aligns with the stated goal -- no unjustified "drive-by" refactors
- [ ] Clear rollout plan and expected user impact described in the PR body

---

## Correctness & Edge Cases

- [ ] Boundary cases handled: nulls, empty lists, timezones, retries, partial failures
- [ ] Failure modes are explicit: what happens when a dependency is down or returns unexpected data?
- [ ] Idempotency where relevant: safe retries without double effects (especially for writes, payments, notifications)
- [ ] Concurrency / race conditions: shared state properly synchronized
- [ ] Error handling: errors are caught, logged, and surfaced appropriately (not silently swallowed)

---

## Security & Privacy

- [ ] No secrets logged or committed (API keys, tokens, passwords)
- [ ] Input validation on all external inputs (user input, API payloads, query params)
- [ ] Output encoding where applicable (HTML, SQL, shell commands -- prevent injection)
- [ ] Least privilege for new permissions, roles, or scopes
- [ ] PII handling:
  - Logging: PII is redacted or excluded from logs
  - Retention: data retention policies respected
  - Access controls: only authorized paths can read sensitive data
- [ ] Authentication & authorization checks on new endpoints or actions
- [ ] Dependencies: no new dependencies with known vulnerabilities

---

## Observability

- [ ] Logs are structured (JSON / key-value), meaningful, and not noisy
- [ ] Key metrics added: counters and timers for the change
  - Consider SLO/SLA relevance (latency percentiles, error rates)
- [ ] New flows are tied into existing distributed traces (if tracing is in use)
- [ ] Alerts are actionable -- avoid "FYI" alerts that train on-call to ignore them
- [ ] Health checks updated if new dependencies or services are introduced

---

## Performance & Cost

- [ ] Hot-path code is measured or at least reasoned about (benchmarks, profiling, or written justification)
- [ ] Query/index checks:
  - No N+1 query patterns
  - Required indexes exist for new queries
  - Pagination used for unbounded result sets
- [ ] Caching strategy is clear:
  - What is cached?
  - What is the TTL?
  - How is the cache invalidated?
- [ ] Cloud cost impact considered:
  - Extra API calls or service invocations
  - Bigger payloads (bandwidth / storage)
  - Heavier compute (CPU / memory / GPU)
- [ ] No unnecessary allocations, copies, or serialization in tight loops

---

## API & Backward Compatibility

- [ ] Changes are non-breaking where possible
- [ ] If breaking changes are unavoidable:
  - Versioning or deprecation strategy documented
  - Migration guide for consumers provided
- [ ] Schema/config changes have corresponding migration steps
- [ ] API contracts (request/response shapes, status codes) are consistent with existing patterns
- [ ] Clients are not forced to update simultaneously (loose coupling)

---

## Data & Migrations

- [ ] Database migrations are backward compatible: expand -> migrate -> contract
- [ ] Rollback strategy exists for data changes (not just code rollback)
  - Can we revert the migration safely?
  - Is there a compensating migration?
- [ ] Long-running migrations are safe:
  - Batching to avoid locking tables
  - Timeouts to avoid runaway processes
  - Locks are scoped and short-lived
- [ ] Seed data or backfills are scripted and idempotent

---

## Docs & Developer Ergonomics

- [ ] README or runbook updated when behavior changes
- [ ] "How to test locally" is clear and up to date
- [ ] Notable operational steps called out:
  - New environment variables
  - Feature toggles or flags to enable/disable
  - Data backfills or one-time scripts
- [ ] ADR (Architecture Decision Record) created for significant design decisions
- [ ] CHANGELOG or release notes updated if the project uses them

---

## Test Coverage

- [ ] New code paths have corresponding tests
- [ ] Edge cases from the "Correctness" section are covered by tests
- [ ] Tests are deterministic (no flaky reliance on timing, ordering, or external services)
- [ ] Integration or end-to-end tests updated if behavior changed at system boundaries
- [ ] Test names clearly describe the scenario being tested

---

## AI Reviewer Guidance

When AI is performing this review, keep in mind:

- **Advisory, not authoritative**: the human reviewer verifies all AI claims
- **Focus areas for AI**: edge cases, security footguns, concurrency/race conditions, test gaps
- **Cite exact locations**: every finding must reference specific file + line range or patch hunk
- **Don't hallucinate issues**: if unsure about something, say so and suggest the author verify
- **Skip non-applicable categories**: a docs-only PR doesn't need a performance review
