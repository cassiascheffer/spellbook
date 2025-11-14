---
name: jj-planning-commits
description: Use when starting new project work or feature development - plan tasks by creating empty commits with Jujutsu that describe each piece of work
---

# Planning Projects with Jujutsu Empty Commits

## When to Use This Skill

Use this skill when:
- Starting a new feature or project
- Need to break down complex work into tasks
- Want to create a visible roadmap before coding
- Planning work that will be implemented sequentially

## Philosophy

Plan projects by creating empty commits that describe each task. Each commit becomes a milestone in your plan that you'll implement later by checking it out and adding the actual code.

## Planning Workflow

### Start a New Project Plan

```bash
jj new main                    # Create first planning commit
jj describe                    # Describe the first task in detail
jj commit                      # Finalize the empty commit

jj new                         # Create next planning commit
jj describe                    # Describe the second task
jj commit                      # Continue building the plan
```

**Key principle:** Each empty commit represents one discrete task with clear acceptance criteria.

### View Your Plan

```bash
jj log                         # View all planned tasks as a commit stack
jj log -r 'mine()'            # View only your commits
jj show <change-id>           # View detailed description of a specific task
```

### Start Working

```bash
jj edit <change-id>           # Check out the commit you want to implement
# Make your changes to implement the feature
jj describe                    # Update description if needed
jj new                         # Move to next task when done
```

### Reorganize Plans

```bash
jj rebase -s <source> -d <dest>    # Reorder tasks in your plan
jj squash                           # Combine related tasks
jj split                            # Split a task into smaller pieces
```

## Planning Best Practices

### Create Descriptive Empty Commits

Each commit description should:
- Fully explain what needs to be done
- Include acceptance criteria
- Note any dependencies or prerequisites
- Use clear, actionable language

**Template for planning commits:**
```
[Action]: [What needs to be done]

[Detailed explanation of the task]
- [Acceptance criterion 1]
- [Acceptance criterion 2]
- [Acceptance criterion 3]

Dependencies: [List any tasks this depends on, or "none"]
Acceptance: [How to verify this task is complete]
```

### Example Planning Session

```bash
# Plan a new authentication feature
jj new main
jj describe -m "Add user registration endpoint

Create POST /api/register endpoint that:
- Accepts email and password
- Validates email format
- Hashes password with bcrypt
- Stores user in database
- Returns JWT token

Dependencies: none
Acceptance: Can register user and receive valid JWT"
jj commit

jj new
jj describe -m "Add login endpoint

Create POST /api/login endpoint that:
- Accepts email and password
- Verifies credentials
- Returns JWT token on success
- Returns 401 on failure

Dependencies: User registration endpoint
Acceptance: Can login with registered credentials"
jj commit

jj new
jj describe -m "Add JWT middleware

Create authentication middleware that:
- Validates JWT tokens
- Attaches user to request context
- Returns 401 for invalid tokens

Dependencies: Login endpoint
Acceptance: Protected routes require valid JWT"
jj commit
```

### Work Through Your Plan

```bash
jj log                        # Review the plan
jj edit <first-task>         # Start with first task
# Implement the feature
jj new                        # Move to next task
jj edit <second-task>        # Work on second task
# Continue through the stack
```

## Key Principles

1. **Plan first, code later** - Create all planning commits before implementing
2. **One task, one commit** - Each commit should be independently implementable
3. **Clear acceptance criteria** - Know exactly when a task is done
4. **Dependencies matter** - Note which tasks build on others
5. **Plans are flexible** - Use `jj rebase`/`jj squash`/`jj split` to reorganize as you learn

## Common Patterns

### Breaking Down Large Features

When planning a large feature:
1. Create high-level planning commits first
2. Use `jj split` to break them into smaller tasks
3. Reorder with `jj rebase` to sequence dependencies
4. Add more detailed acceptance criteria as you refine

### Discovering New Tasks

When you discover work during implementation:
1. Use `jj new` to create a planning commit for the new task
2. Use `jj rebase` to position it in the correct order
3. Return to your current task with `jj edit`

### Planning for Stacked PRs

When planning work that will become stacked PRs:
1. Group related tasks together in the commit stack
2. Plan natural breakpoints between PRs
3. Ensure each PR group is independently reviewable
4. See `spellbook:jj-stacked-prs` skill for creating the actual PRs

## Integration with Other Skills

- **After planning:** Use `spellbook:jj-commit-workflow` to implement tasks
- **For PR creation:** Use `spellbook:jj-stacked-prs` to turn your stack into PRs
- **When hooks fail:** Use `spellbook:jj-pre-commit-hooks` for the fix protocol

## Why This Works

**For AI agents:**
- Clear context: Each `jj edit` tells me exactly what to work on
- Visible progress: `jj log` shows what's done and what's left
- Easy reorganization: I can adjust the plan without losing work
- Natural checkpoints: Each commit is a stopping point for review

**For humans:**
- Transparent roadmap: See the full plan before any code is written
- Reviewable planning: Comment on approach before implementation
- Flexible: Easy to reorder or split tasks as understanding improves
- Git-native: Uses commits, not external tools
