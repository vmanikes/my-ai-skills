---
name: git-smart-commit
description: Tool to make conventional git commits using AI by looking at the local changes
---

# Git Smart Commit

This skill analyzes local git changes and generates conventional commit messages according to the [Conventional Commits specification](https://www.conventionalcommits.org/en/v1.0.0/#specification).

The goal is to offload the cognitive burden from the user—letting AI review and summarize all changes into well-structured commit messages.

## Instructions

### 1. Branch Safety Check
- **IMPORTANT**: Never commit directly to `main` or `master` branches
- If on a protected branch, ask the user for:
  - A ticket/issue number to create a branch (e.g., `feat/TICKET-123-add-user-auth`)
  - Or context to derive a descriptive branch name (e.g., `fix/resolve-login-timeout`)

### 2. Analyze Changes
- Run `git status` to see staged, unstaged, and untracked files
- Run `git diff --staged` for staged changes (preferred for commit)
- Run `git diff` for unstaged changes
- If nothing is staged, ask the user if they want to stage all changes or select specific files

### 3. Generate Commit Message

#### Commit Types
Choose the appropriate type based on the changes:
| Type | Description |
|------|-------------|
| `feat` | New feature or capability |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, whitespace (no code logic change) |
| `refactor` | Code restructuring without behavior change |
| `test` | Adding or updating tests |
| `chore` | Build, tooling, dependencies, config |
| `perf` | Performance improvement |
| `ci` | CI/CD configuration |

#### Commit Format
```
<type>(<scope>): <subject>

[optional body]

[optional footer(s)]
```

#### Rules
- **Subject line**: Max 50 characters, imperative mood ("add" not "added"), no period at end
- **Scope**: Optional, describes the area affected (e.g., `api`, `auth`, `ui`, `db`)
- **Body**: Wrap at 72 characters, explain *what* and *why* (not *how*)
- **Breaking changes**: Add `!` after type/scope (e.g., `feat(api)!:`) and include `BREAKING CHANGE:` in footer

#### Examples
```
feat(auth): add OAuth2 login support

Implement Google and GitHub OAuth providers for user authentication.
This replaces the legacy session-based auth system.

BREAKING CHANGE: removes /api/v1/session endpoints
```

```
fix(cart): resolve race condition in quantity update

Multiple rapid clicks could cause incorrect totals due to
async state updates. Added debouncing and optimistic locking.

Fixes #234
```

```
chore: upgrade dependencies to latest versions
```

### 4. Handle Multiple Logical Changes
- If changes span unrelated concerns, ask the user if they want:
  - Multiple focused commits (preferred)
  - A single combined commit

### 5. Commit and Push
- Stage the appropriate files if not already staged
- Create the commit with the generated message
- **Ask before pushing**: "Ready to push to remote?" (don't auto-push without confirmation)
- Push to the current branch: `git push -u origin HEAD`
