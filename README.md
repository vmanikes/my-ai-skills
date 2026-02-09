# my-ai-skills

A collection of AI agent skills for [Cursor](https://cursor.com). Each skill teaches the AI assistant a specialized workflow -- giving it structured, repeatable instructions for tasks that benefit from consistency.

## Skills

| Skill | Description |
|-------|-------------|
| [git-smart-commit](git-smart-commit/) | Analyzes local git changes and generates conventional commit messages |
| [pr-review](pr-review/) | Performs structured pull request reviews across correctness, security, performance, and more |

### git-smart-commit

Offloads the cognitive burden of writing commit messages. The agent inspects your staged/unstaged changes and produces messages following the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/#specification) spec.

**What it does:**
- Checks branch safety (never commits to `main`/`master`)
- Auto-detects and runs project formatters (Prettier, Black, gofmt, etc.) on changed files only
- Generates structured `type(scope): subject` commit messages
- Suggests splitting unrelated changes into multiple commits
- Asks before pushing to remote

**Trigger phrases:** "commit my changes", "smart commit", "generate a commit message"

### pr-review

Runs a multi-dimensional review of a pull request, covering seven categories drawn from production engineering best practices.

**What it checks:**
- **Scope & Intent** -- is the PR focused and small enough to review confidently?
- **Correctness & Edge Cases** -- boundary conditions, failure modes, idempotency
- **Security & Privacy** -- secrets, input validation, PII handling, least privilege
- **Observability** -- structured logs, metrics, tracing, actionable alerts
- **Performance & Cost** -- N+1 queries, caching, cloud cost impact
- **API & Backward Compatibility** -- non-breaking changes, versioning, migration steps
- **Data & Migrations** -- backward-compatible migrations, rollback strategy, batching safety
- **Docs & Developer Ergonomics** -- README updates, local test instructions, operational callouts

Every finding cites exact file and line locations. Output is tiered into Critical / Suggestion / Nit so the author knows what blocks the merge.

**Trigger phrases:** "review this PR", "review pull request #123", "code review my branch"

## Installation

### Personal use (available across all your projects)

```bash
# Clone into your Cursor skills directory
git clone git@github.com:vmanikes/my-ai-skills.git ~/.cursor/skills/my-ai-skills
```

### Per-project (shared with your team via the repo)

```bash
# From your project root
git subtree add --prefix=.cursor/skills/my-ai-skills git@github.com:vmanikes/my-ai-skills.git main --squash
```

Or simply copy the skill folders you want into `.cursor/skills/` in your project.

### Verify

After installation, the skills will appear in Cursor's skill list. You can confirm by asking the agent:

> "What skills do you have available?"

## Skill Structure

Each skill follows the same layout:

```
skill-name/
├── SKILL.md          # Required -- main instructions (frontmatter + workflow)
├── CHECKLIST.md      # Optional -- detailed reference material
├── examples.md       # Optional -- usage examples
└── scripts/          # Optional -- utility scripts
```

The `SKILL.md` file contains YAML frontmatter (`name`, `description`) that Cursor uses for skill discovery, and a markdown body with the agent's instructions.

## Prerequisites

Some skills rely on external tools being available on your system:

| Skill | Requirements |
|-------|-------------|
| git-smart-commit | `git` |
| pr-review | `git`, [`gh` CLI](https://cli.github.com/) (for GitHub PR reviews) |

## Contributing

To add a new skill:

1. Create a new directory with a descriptive, hyphenated name (e.g., `api-design-review`)
2. Add a `SKILL.md` with the required frontmatter:
   ```yaml
   ---
   name: your-skill-name
   description: What it does and when to use it. Written in third person.
   ---
   ```
3. Keep `SKILL.md` under 500 lines -- use separate reference files for detailed content
4. Include trigger terms in the description so Cursor can auto-discover the skill
5. Test the skill by invoking it in Cursor and verifying the output

## License

MIT
