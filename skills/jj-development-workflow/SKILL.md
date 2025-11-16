---
name: jj-development-workflow
description: Use when starting any development work with Jujutsu - complete workflow from idea refinement through planning, implementation, code review, and PR creation, all integrated with jj version control
---

# Jujutsu Development Workflow

## Overview

Complete development workflow integrated with Jujutsu version control. Takes you from rough ideas through design, planning, implementation, code review, and pull request creation.

**This skill orchestrates the complete development cycle:**

1. **Refine** - Turn rough ideas into clear designs
2. **Plan** - Create task commits with acceptance criteria
3. **Implement** - Execute tasks using commit stack
4. **Review** - Code review after each task
5. **Ship** - Create stacked PRs from your commits

**Core principle:** Jujutsu commits are your development roadmap. Each commit = one reviewable unit of work.

## When to Use This Skill

Use this skill when:

- Starting new feature development
- Have a rough idea that needs refinement
- Need to plan and execute complex work
- Want integrated planning → implementation → PR workflow
- Working with Jujutsu version control

**Don't use for:**

- Quick mechanical fixes (just make the change)
- Work already planned elsewhere
- Projects not using Jujutsu (use git-specific workflows)

## The Complete Workflow

### Phase 1: Refine the Idea

**Goal:** Turn rough concept into clear, fully-formed design.

**Process:**

1. Check current project state (files, docs, recent commits)
2. Ask questions one at a time to understand:
   - Purpose and goals
   - Constraints and requirements
   - Success criteria
3. Explore 2-3 different approaches with trade-offs
4. Present recommended approach with reasoning
5. Present design in sections (200-300 words each)
6. Validate each section before continuing

**Key principles:**

- One question at a time (don't overwhelm)
- Multiple choice preferred when possible
- YAGNI ruthlessly - remove unnecessary features
- Explore alternatives before settling
- Incremental validation throughout

**Output:** Design document covering:

- Architecture and approach
- Components and data flow
- Error handling strategy
- Testing approach

**Save to:** `docs/plans/YYYY-MM-DD-<topic>-design.md`

**Tools to use:**

- Read existing files for context
- Ask questions with AskUserQuestion
- Write design document when validated

### Phase 2: Plan with Empty Commits

**Goal:** Break design into discrete, implementable tasks as Jujutsu commits.

**Required sub-skill:** `spellbook:jj-planning-commits`

**Process:**

1. Start from main branch
2. Create empty commit for each task
3. Write clear commit message with acceptance criteria
4. Build commit stack representing full plan

**Template for each planning commit:**

```
[Action]: [What needs to be done]

[Detailed explanation of the task]
- [Acceptance criterion 1]
- [Acceptance criterion 2]
- [Acceptance criterion 3]

Dependencies: [List any tasks this depends on, or "none"]
Acceptance: [How to verify this task is complete]
```

**Task granularity:**

- Each task should be 5-15 minutes of work
- Independently implementable and testable
- Follows TDD: write test → verify fail → implement → verify pass → commit
- Clear acceptance criteria

**Commands:**

```bash
jj new main
jj describe -m "[Task description with acceptance criteria]"
jj commit

jj new
jj describe -m "[Next task...]"
jj commit
# Continue for all tasks
```

**View your plan:**

```bash
jj log                    # See the full task stack
```

**Save detailed plan to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

**Plan document should include:**

- Header with goal, architecture, tech stack
- Each task with:
  - Files to create/modify (exact paths)
  - Complete code examples
  - Exact commands to run
  - Expected output
  - Commit message template

### Phase 3: Implement Tasks

**Goal:** Execute planned tasks using commit stack as roadmap.

**Required sub-skill:** `spellbook:jj-commit-workflow`

**Execution strategies:**

#### Option A: Sequential Implementation (Simple)

Work through tasks in order:

```bash
jj log                         # Review the plan
jj edit <first-task-id>       # Start implementing first task
# Follow TDD: write test, verify fail, implement, verify pass
jj edit <second-task-id>      # Implement second task
# Continue through stack
```

**When to use:**

- Tasks are sequential and dependent
- Want direct control of implementation
- Small number of tasks (< 5)

#### Option B: Subagent per Task (Quality gates)

Dispatch fresh subagent for each task with code review:

**When to use:**

- Tasks are mostly independent
- Want automated code review between tasks
- Larger number of tasks (5+)
- Want to catch issues early

**Process:**

1. Create TodoWrite with all tasks
2. For each task:
   - Dispatch implementation subagent
   - Get git SHAs (before/after)
   - Dispatch code-reviewer subagent
   - Fix any Critical/Important issues
   - Mark task complete
3. Final code review after all tasks
4. Proceed to completion

**See:** `spellbook:subagent-driven-development` for detailed protocol

**Implementation principles:**

- Follow TDD for every task (test → fail → implement → pass)
- Use `spellbook:test-driven-development`
- Keep commits focused on single task
- Update commit description if approach changes
- Run verifications as specified in plan

**For discovered issues:**

- Don't fix unrelated problems immediately
- Create new planning commit for the issue
- Rebase it into correct position
- Continue with current task

### Phase 4: Code Review

**Goal:** Validate implementation before creating PRs.

**For subagent-driven approach:** Already done between tasks

**For sequential approach:** Dispatch final code review:

**Required sub-skill:** `spellbook:requesting-code-review`

```bash
# Get commit range
jj log -r 'mine()'

# Use requesting-code-review skill to dispatch reviewer
# Reviewer checks:
# - All plan requirements met
# - Tests cover functionality
# - Code follows standards
# - No obvious bugs or issues
```

**Address all Critical and Important issues before proceeding.**

### Phase 5: Create Pull Requests

**Goal:** Turn commit stack into reviewable GitHub PRs.

**Required sub-skill:** `spellbook:jj-stacked-prs`

**Process:**

1. Group related commits into logical PRs
2. Create bookmarks for each PR
3. Push bookmarks to GitHub
4. Create PRs with proper dependencies
5. Mark all PRs as draft

**For single PR:**

```bash
jj bookmark create my-feature
jj git push --bookmark my-feature
gh pr create --draft --title "..." --body "..."
```

**For stacked PRs:**

```bash
# Create bookmarks for each PR in stack
jj bookmark create feature-part-1 -r <first-commit-id>
jj bookmark create feature-part-2 -r <last-commit-id>

# Push in order
jj git push --bookmark feature-part-1
jj git push --bookmark feature-part-2

# Create PRs with dependencies
gh pr create --draft --base main --head feature-part-1 --title "..." --body "..."
gh pr create --draft --base feature-part-1 --head feature-part-2 --title "..." --body "..."
```

**PR description template:**

```markdown
## Summary

- [Bullet point 1]
- [Bullet point 2]
- [Bullet point 3]

## Test plan

- [ ] [Test step 1]
- [ ] [Test step 2]
- [ ] [Test step 3]

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

**See:** `spellbook:jj-stacked-prs` for complete stacked PR workflow

### Phase 6: Handle Pre-commit Hooks

**When hooks fail during push:**

**Required sub-skill:** `spellbook:jj-pre-commit-hooks`

**Never bypass hooks.** Always fix the issues.

**5-step protocol:**

1. Identify which commit failed
2. Use `jj edit <commit-id>` to check out failing commit
3. Fix the issues
4. Use `jj new` to finalize fixes
5. Re-run push

**See:** `spellbook:jj-pre-commit-hooks` for complete protocol

## Key Principles

### Jujutsu Integration

**Commits are your roadmap:**

- Empty commits = planned tasks
- Filled commits = completed tasks
- `jj log` = progress visualization
- `jj edit` = context for current work

**Non-destructive reorganization:**

- Use `jj rebase` to reorder tasks
- Use `jj squash` to combine related work
- Use `jj split` to break tasks down
- Auto-rebasing keeps children up-to-date

### Quality Gates

**At each phase:**

- Design: Validate sections before proceeding
- Planning: Clear acceptance criteria required
- Implementation: TDD for every task
- Review: Fix Critical/Important issues
- PRs: All tests pass, hooks succeed

### DRY, YAGNI, TDD

**Throughout workflow:**

- **DRY** - Don't repeat yourself in code or plans
- **YAGNI** - You aren't gonna need it (ruthlessly cut features)
- **TDD** - Test-driven development for every task

## Integration with Jujutsu Skills

This master workflow orchestrates these specialized skills:

**Planning:** `spellbook:jj-planning-commits`

- Create empty commits for tasks
- Template for planning commits
- Managing commit stacks

**Implementation:** `spellbook:jj-commit-workflow`

- Sequential task execution
- Managing working copy
- Reorganizing commits
- Handling discovered issues

**Execution:** `spellbook:subagent-driven-development` (optional)

- Fresh subagent per task
- Automated code review between tasks
- Fast iteration with quality gates

**Code Review:** `spellbook:requesting-code-review`

- Validate before PRs
- Catch issues early
- Ensure requirements met

**Pull Requests:** `spellbook:jj-stacked-prs`

- Create stacked PRs
- Manage dependencies
- Update after review

**Hook Failures:** `spellbook:jj-pre-commit-hooks`

- Fix protocol when hooks fail
- Never bypass safety mechanisms

## Example End-to-End Workflow

```
You: I want to add user authentication to the app

Agent: I'm using the jj-development-workflow skill for this.

[Phase 1: Refine]
Agent: Let me check your current app structure first...
Agent: I see you're using Express. A few questions about your auth needs:

Which authentication method do you prefer?
- [ ] JWT tokens (stateless, scales easily)
- [ ] Session cookies (simpler, more secure)
- [ ] OAuth integration (delegates to provider)

User: JWT tokens

Agent: [More questions about password hashing, token expiry, etc.]
Agent: [Presents design in sections, validates each]
Agent: [Saves to docs/plans/2025-11-13-user-authentication-design.md]

[Phase 2: Plan]
Agent: Now creating planning commits...

jj new main
jj describe -m "Add user registration endpoint..."
jj commit

jj new
jj describe -m "Add login endpoint..."
jj commit

[Continues for all tasks]
[Saves detailed plan to docs/plans/2025-11-13-user-authentication.md]

Agent: Plan created with 8 tasks. View with: jj log
Which execution approach?
1. Sequential (you implement each task)
2. Subagent-driven (automated with code review)

User: Subagent-driven

[Phase 3: Implement]
Agent: Using subagent-driven-development...
[Creates TodoWrite with 8 tasks]

Task 1: User registration endpoint
[Dispatches implementation subagent]
[Dispatches code-reviewer]
Reviewer: Looks good, no issues
[Marks task 1 complete]

Task 2: Login endpoint
[Dispatches implementation subagent]
[Dispatches code-reviewer]
Reviewer: Issue - password validation too weak
[Dispatches fix subagent]
[Marks task 2 complete]

[Continues through all 8 tasks]

[Phase 4: Review]
[Final code review shows all requirements met]

[Phase 5: PRs]
Agent: This is small enough for one PR. Creating now...

jj bookmark create user-auth
jj git push --bookmark user-auth
gh pr create --draft --title "Add user authentication" --body "..."

Agent: PR created: https://github.com/user/repo/pull/123

Done! All tests passing, PR ready for review.
```

## Common Patterns

### Large Feature → Stacked PRs

For features that should be broken into multiple reviewable PRs:

1. **During planning:** Group related tasks
2. **Create natural breakpoints** between groups
3. **After implementation:** Create bookmark per group
4. **Use stacked PRs:** Each PR builds on previous

```bash
jj bookmark create auth-models -r <task-3-id>
jj bookmark create auth-endpoints -r <task-6-id>
jj bookmark create auth-middleware -r <task-8-id>

jj git push --bookmark auth-models
jj git push --bookmark auth-endpoints
jj git push --bookmark auth-middleware

# Create PRs in order with dependencies
```

### Discovered Issues During Work

When you find unrelated problems:

1. **Don't fix immediately**
2. **Create planning commit:**

   ```bash
   jj new
   jj describe -m "Fix discovered issue: [description]"
   jj commit
   ```

3. **Rebase to correct position:** `jj rebase -s <issue-commit> -d <where-it-should-go>`
4. **Continue current task:** `jj edit <current-task-id>`

### Plan Changes During Implementation

When you realize the plan needs adjustment:

1. **Update current commit description:** `jj describe`
2. **Create new task commits if needed:** `jj new` + `jj describe`
3. **Reorder with rebase:** `jj rebase -s <commit> -d <new-parent>`
4. **Squash related work:** `jj squash`
5. **Split overly large commits:** `jj split`

## Why This Works

**For AI agents:**

- Clear context at every step (design → plan → task)
- `jj edit` tells me exactly what to work on
- Visible progress with `jj log`
- Safe reorganization without losing work
- Automated quality gates catch issues early

**For humans:**

- Transparent roadmap before coding starts
- Reviewable at every phase (design, plan, implementation)
- Flexible - easy to adjust as understanding improves
- Git-native - uses commits, not external tools
- Stacked PRs make large features reviewable

## Troubleshooting

### Lost track of where you are

```bash
jj log -r 'mine()' --graph    # See all your commits
jj st                          # See current changes
```

### Want to reorganize the plan

```bash
jj rebase -s <commit> -d <new-parent>   # Reorder
jj squash                                # Combine with parent
jj split                                 # Break into pieces
```

### Hook failures on push

**REQUIRED SUB-SKILL:** Use `spellbook:jj-pre-commit-hooks`

Never bypass - always fix.

### Want to start over on a task

```bash
jj edit <task-commit-id>      # Check out the task
jj restore --from <parent>    # Reset to before your changes
# Start fresh on the task
```
