---
name: jj-commit-workflow
description: Use when managing commits, building commit stacks, reorganizing work, or working on specific commits in your Jujutsu repository
---

# Jujutsu Commit Workflow

## When to Use This Skill

Use this skill when:
- Managing commits in your working copy
- Building or reorganizing commit stacks
- Switching between different commits to work on
- Splitting, squashing, or reordering commits
- Developing multiple features in parallel

## Core Concepts

### The Working Copy Commit

**Key understanding:** In Jujutsu, your working directory is itself a commit marked with `@`.

- Changes are automatically tracked in this working copy commit
- Use `jj describe` to set the commit message
- Use `jj new` to finalize the current commit and create a new one
- The working copy commit is always where your edits happen

### Basic Workflow

```bash
# View current changes
jj st

# Set commit description
jj describe

# Create new commit, finalizing current one
jj new
```

## Building Commit Stacks

### Create a Stack for Related Work

```bash
jj new main                    # Start from main
jj describe -m "First change"
# Make changes
jj new                         # Create second commit in stack
jj describe -m "Second change"
# Make more changes
jj new                         # Create third commit
jj describe -m "Third change"
```

**Each commit builds on the previous one, creating a stack.**

### Reorganize Your Stack

```bash
# View your stack
jj log

# Reorder commits
jj rebase -s <commit> -d <new-parent>

# Merge current commit into parent
jj squash

# Split commit into smaller pieces
jj split
```

### Work on Specific Commits

```bash
# Check out specific commit to modify
jj edit <change-id>

# Make changes to this commit
# ...

# Return to tip of stack
jj new
```

**Important:** When you use `jj edit`, you're modifying an existing commit. Child commits will be automatically rebased on top of your changes.

## Common Workflows

### Sequential Feature Development

When implementing planned tasks in order:

```bash
# View your plan
jj log

# Start with first task
jj edit <first-commit-id>
# Implement the feature
# Changes are automatically added to this commit

# Move to next task
jj new
jj edit <second-commit-id>
# Implement next feature

# Continue through stack
```

### Developing Features Independently

Work on multiple features in parallel:

```bash
# Create first feature branch
jj new main
jj describe -m "Feature A: User authentication"
jj bookmark create feature-a
# Work on feature A

# Create second independent feature
jj new main                    # Start from main again
jj describe -m "Feature B: Email notifications"
jj bookmark create feature-b
# Work on feature B

# Switch between features
jj edit <feature-a-change-id>
# Work on feature A

jj edit <feature-b-change-id>
# Work on feature B
```

### Combining Independent Work

```bash
# Merge both features locally
jj new feature-a feature-b    # Create commit with both parents
jj describe -m "Integrate features A and B"
```

## Reorganizing Commits

### Reordering Tasks

```bash
# Move commit to different position
jj rebase -s <commit-to-move> -d <new-parent>

# Example: Move commit C to come before commit B
jj rebase -s <commit-c-id> -d <commit-a-id>
```

### Combining Related Commits

```bash
# Squash current commit into its parent
jj squash

# Squash with specific target
jj squash --into <target-commit>
```

### Splitting Large Commits

```bash
# Start interactive split
jj split

# Jujutsu will open editor:
# 1. Review changes
# 2. Select which changes go in first commit
# 3. Remaining changes go in second commit
```

## Managing Change Scope

### For Unrelated Issues Discovered During Work

**CRITICAL RULE:** Only make changes directly related to current task.

**When you discover unrelated problems:**

1. Do NOT fix immediately
2. Create new planning commit:
   ```bash
   jj new
   jj describe -m "Fix discovered issue: [description]"
   jj new  # Return to empty working copy
   ```
3. Use `jj rebase` to position it appropriately in your stack
4. Continue with assigned task using `jj edit <original-task-id>`

This keeps commits focused and reviewable.

## Key Principles

1. **Automatic tracking** - Jujutsu snapshots your working copy continuously
2. **Edit existing commits** - Use `jj edit` to modify commits in your stack
3. **Auto-rebasing** - Child commits update automatically when parents change
4. **Bookmarks for branches** - Use `jj bookmark` to mark commits for PRs
5. **Non-destructive** - Jujutsu preserves history; reorganizing is safe

## Common Commands Reference

### Viewing State

```bash
jj st                          # View current changes
jj log                         # View commit graph
jj log -r 'mine()'            # View only your commits
jj show <change-id>           # View commit details
jj diff                        # View current diff
```

### Modifying Commits

```bash
jj describe                    # Set commit message
jj describe -m "Message"      # Set message directly
jj edit <change-id>           # Check out commit to modify
jj new                         # Create new commit
jj new <parent>               # Create commit with specific parent
```

### Reorganizing

```bash
jj rebase -s <src> -d <dest>  # Move commit
jj squash                      # Merge into parent
jj squash --into <target>     # Merge into specific commit
jj split                       # Split commit interactively
```

### Bookmarks and Branches

```bash
jj bookmark create <name>      # Create bookmark at current commit
jj bookmark create <name> -r <change-id>  # Create at specific commit
jj bookmark list               # List all bookmarks
jj bookmark delete <name>      # Delete bookmark
```

## Integration with Other Skills

- **Before commit workflow:** Use `spellbook:jj-planning-commits` to plan tasks
- **For PRs:** Use `spellbook:jj-stacked-prs` to create GitHub pull requests
- **When hooks fail:** Use `spellbook:jj-pre-commit-hooks` for fix protocol
- **For unrelated issues:** Create planning commits, don't fix immediately

## Why This Works

**For AI agents:**
- **Clear task context** - `jj edit` tells me exactly which task I'm working on
- **Safe reorganization** - I can reorder, split, or squash without fear
- **Automatic rebasing** - Child commits stay up-to-date automatically
- **Visual progress** - `jj log` shows the entire commit stack and progress

**For humans:**
- **Clean history** - Each commit represents one logical change
- **Easy reorganization** - Reorder tasks as understanding evolves
- **Parallel work** - Multiple features can progress independently
- **Reviewable** - Each commit in stack is independently reviewable

## Troubleshooting

### Forgot to create bookmark before pushing

```bash
# Create bookmark pointing to specific commit
jj bookmark create my-feature -r <change-id>
jj git push --bookmark my-feature
```

### Want to undo recent changes

```bash
# Jujutsu tracks operation history
jj op log                      # View recent operations
jj op undo                     # Undo last operation
jj op restore <operation-id>  # Restore to specific point
```

### Lost in the commit stack

```bash
# View your commits with graph
jj log -r 'mine()' --graph

# Return to latest commit on bookmark
jj edit <bookmark-name>
```
