---
name: jj-stacked-prs
description: Use when creating GitHub pull request stacks or updating stacked PRs - turn commit stacks into reviewable PRs with proper dependencies
---

# Jujutsu Stacked Pull Requests

> **Part of the complete workflow:** This skill is used in Phase 5 (Pull Requests) of `spellbook:jj-development-workflow`. For the full end-to-end process from idea to PR, use that skill instead.

## When to Use This Skill

Use this skill when:
- You're in Phase 5 of the jj-development-workflow (creating PRs)
- Creating multiple related PRs that build on each other
- Need to break large features into reviewable chunks
- Updating PRs in a stack after review feedback
- Managing dependencies between pull requests

**For complete workflow (idea → design → plan → implement → PR):** Use `spellbook:jj-development-workflow`

## What is a PR Stack?

A PR stack is a series of pull requests where each builds on the previous one. Instead of one large PR, you create multiple smaller PRs that:
- Are independently reviewable
- Build on each other logically
- Can be merged sequentially
- Make review faster and more focused

**Example stack:**
- PR 1: Add database schema → main
- PR 2: Add API endpoints → PR 1
- PR 3: Add UI components → PR 2

## Creating a PR Stack

### Prerequisites

You should have:
- Multiple commits in a stack (see `spellbook:jj-commit-workflow`)
- Each commit independently implementable and reviewable
- Clear understanding of which commits belong in which PR

### Step 1: Create Bookmarks for Each PR

```bash
# View your commit stack
jj log

# Create bookmark for first PR
jj bookmark create part-1 -r <change-id-1>

# Create bookmark for second PR
jj bookmark create part-2 -r <change-id-2>

# Create bookmark for third PR
jj bookmark create part-3 -r <change-id-3>
```

**Naming convention:** Use descriptive names that indicate order and content:
- `part-1-schema`, `part-2-api`, `part-3-ui`
- `step-1-auth`, `step-2-middleware`, `step-3-routes`

### Step 2: Push Each Bookmark

```bash
# Push first PR
jj git push --bookmark part-1

# Push second PR
jj git push --bookmark part-2

# Push third PR
jj git push --bookmark part-3
```

### Step 3: Create PRs in GitHub

**In GitHub UI:**

1. **Create first PR:**
   - Base: `main`
   - Compare: `part-1`
   - Title: Descriptive title for part 1
   - Description: What this PR does, why needed
   - Mark as draft if not ready for review

2. **Create second PR:**
   - Base: `part-1` (builds on first PR)
   - Compare: `part-2`
   - Title: Descriptive title for part 2
   - Link to first PR in description

3. **Create third PR:**
   - Base: `part-2` (builds on second PR)
   - Compare: `part-3`
   - Title: Descriptive title for part 3
   - Link to previous PRs in description

**Critical:** Each PR's base should be the previous PR's branch, except the first which targets `main`.

## Updating PRs After Review

### Update Specific Commit in Stack

When review feedback requires changes to a specific PR:

```bash
# Edit the commit that needs changes
jj edit <change-id>

# Make requested changes
# Changes are automatically added to this commit

# Update commit description if needed
jj describe

# Force-push updated commit
jj git push --bookmark <bookmark-name> --force-with-lease
```

**Important:** Jujutsu automatically rebases child commits when you modify parents, so the entire stack stays up-to-date.

### Verify Stack is Updated

```bash
# View updated stack
jj log

# Push all affected bookmarks
jj git push --bookmark part-1 --force-with-lease
jj git push --bookmark part-2 --force-with-lease
jj git push --bookmark part-3 --force-with-lease
```

## Managing Merged PRs

### After First PR Merges

When `part-1` merges to `main`:

```bash
# Update local main
jj git fetch
jj bookmark set main -r <remote-main-commit>

# Rebase remaining PRs onto main
jj rebase -s <part-2-commit> -d main

# Update bookmarks
jj bookmark set part-2 -r <part-2-commit>
jj bookmark set part-3 -r <part-3-commit>

# Force-push updated PRs
jj git push --bookmark part-2 --force-with-lease
jj git push --bookmark part-3 --force-with-lease
```

**In GitHub UI:**
- Change `part-2` base from `part-1` to `main`
- `part-3` base remains `part-2`

### After Each Subsequent Merge

Repeat the process:
- Fetch and update local main
- Rebase remaining work
- Update PR bases in GitHub

## Best Practices

### Sizing PRs in a Stack

**Good PR size:**
- 200-400 lines of changes
- Single logical feature or component
- Can be reviewed in 15-30 minutes
- Independently deployable (if possible)

**Signs PR is too large:**
- More than 500 lines
- Multiple unrelated changes
- Takes more than 30 minutes to review
- Can't describe in 2-3 sentences

### Structuring the Stack

**Good stack structure:**
```
part-1: Database schema changes
part-2: API endpoints using new schema
part-3: UI components calling new endpoints
part-4: Integration tests for full flow
```

**Each PR builds naturally on the previous one.**

### PR Descriptions for Stacks

**Include in each PR description:**
- What this PR does (2-3 sentences)
- Why this change is needed
- Link to previous PRs in stack
- Note if this PR depends on others merging first
- Testing instructions specific to this PR

**Template:**
```markdown
## Part 2 of 4: API Endpoints

This PR adds the API endpoints for user registration and login.

**Depends on:** #123 (database schema)
**Stack:** #123 → **#124 (this PR)** → #125 → #126

## Changes
- POST /api/register endpoint
- POST /api/login endpoint
- JWT token generation
- Input validation

## Testing
See #123 merged first, then:
1. Start server
2. POST to /api/register with email/password
3. Verify JWT returned
```

## Pull Request Guidelines

### When Creating PRs

- **Always open as draft** - Don't mark ready for review until complete
- **Use numbered lists with "1"** - Easier to reorder items
- **Don't mark todos complete** - Leave for PR creator
- **Each commit independently reviewable** - Stack should be reviewable PR by PR

### Force Pushing

**Use `--force-with-lease` always:**
```bash
jj git push --bookmark <name> --force-with-lease
```

**Never use bare `--force`** - `--force-with-lease` prevents overwriting others' work.

## Common Workflows

### Adding a New PR to Middle of Stack

When you need to add work between existing PRs:

```bash
# Create new commit between part-1 and part-2
jj edit <part-1-commit>
jj new
jj describe -m "New intermediate feature"
# Implement feature

# Rebase part-2 and later commits on new commit
jj rebase -s <part-2-commit> -d <new-commit>

# Create bookmark for new PR
jj bookmark create part-1a -r <new-commit>

# Push new PR
jj git push --bookmark part-1a

# Update affected PRs
jj git push --bookmark part-2 --force-with-lease
jj git push --bookmark part-3 --force-with-lease
```

### Squashing Commits Within a PR

When a PR has multiple commits that should be one:

```bash
# Edit the later commit
jj edit <later-commit>

# Squash into earlier commit
jj squash --into <earlier-commit>

# Force-push updated PR
jj git push --bookmark <name> --force-with-lease
```

### Removing a PR from Stack

When a PR is no longer needed:

```bash
# Identify commits to remove
jj log

# Rebase subsequent commits directly onto parent
jj rebase -s <next-commit> -d <parent-of-removed-commit>

# Delete bookmark
jj bookmark delete <removed-pr-bookmark>

# Close PR in GitHub
# Update subsequent PRs' force-push
jj git push --bookmark <next-pr> --force-with-lease
```

## Integration with Other Skills

- **Complete workflow:** `spellbook:jj-development-workflow` orchestrates the full process
- **Before stacked PRs:** Use `spellbook:jj-planning-commits` to plan the work
- **During implementation:** Use `spellbook:jj-commit-workflow` to build commits
- **When hooks fail:** Use `spellbook:jj-pre-commit-hooks` before pushing
- **For review:** Use `spellbook:requesting-code-review` before marking ready

## Troubleshooting

### GitHub shows wrong diff

**Problem:** PR shows all changes from main, not just this PR's changes.

**Solution:** Check PR base branch in GitHub. Should be previous PR's branch, not main (except for first PR).

### Conflicts after rebasing

**Problem:** Rebase creates conflicts when updating stack.

**Solution:**
```bash
# Jujutsu will mark conflicts in files
jj st                          # View conflicted files

# Resolve conflicts manually
# Edit files to resolve conflicts

# Jujutsu automatically creates new commit with resolution
jj new                         # Continue after resolution
```

### Lost track of which bookmark is which

**Problem:** Too many bookmarks, unclear which is which.

**Solution:**
```bash
# List all bookmarks with commits
jj bookmark list

# View log with bookmarks highlighted
jj log

# Delete obsolete bookmarks
jj bookmark delete <old-bookmark>
```

## Why This Works

**For reviewers:**
- Small, focused PRs are easier to review
- Can review and approve incrementally
- Less context needed per review
- Faster feedback cycles

**For AI agents:**
- Clear structure for organizing work
- Each PR has explicit dependencies
- Automatic rebasing keeps stack current
- Easy to update specific parts

**For project:**
- Incremental progress visible
- Can merge parts as they're ready
- Easier to identify issues early
- Better git history
