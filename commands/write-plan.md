---
description: Plan development work by creating empty commits as a meta todo list with Jujutsu
---

Use the `jj-planning-commits` skill to plan work with empty commits:

**When to use:** Starting new project work, breaking down features into tasks, need a concrete development roadmap

**How planning works:**
1. Create empty commits (no file changes) that describe each piece of work
2. Each commit message becomes your task description and acceptance criteria
3. The commit stack becomes your development roadmap
4. Reorganize commits as you refine the plan using `jj-commit-workflow`

**Empty commits as your todo list:**
- Each empty commit = one unit of work
- Commit messages contain task description + acceptance criteria
- Use `jj new` to create planning commits
- Use `jj describe` to refine task descriptions
- Use `jj rebase` to reorganize the work sequence

**When to use related skills:**
- `jj-commit-workflow` - Manage and reorganize commit stacks as you refine your plan
- `jj-development-workflow` - For complete end-to-end workflow including planning + implementation
