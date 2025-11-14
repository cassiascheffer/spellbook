# Spellbook

A personal skills library forked from [superpowers](https://github.com/obra/superpowers) - includes Jujutsu workflows, TDD, debugging, and coding standards.

## What is Spellbook?

Spellbook is a fork of Jesse Vincent's excellent [superpowers](https://github.com/obra/superpowers) skills library, customized with personal workflows and preferences. It includes:

- All the proven techniques from superpowers (TDD, systematic debugging, collaboration patterns)
- Custom Jujutsu (jj) workflow skills for planning, commits, and PR stacks
- Personal coding standards and conventions
- Tailored skill activations for my development style

**Attribution:** Spellbook is built on superpowers by Jesse Vincent. The core skills system, patterns, and many individual skills originate from that project. This fork is released under the same MIT license.

## What You Get

### From Superpowers

- **Testing Skills** - TDD, async testing, anti-patterns
- **Debugging Skills** - Systematic debugging, root cause tracing, verification
- **Collaboration Skills** - Code review, parallel agents, git worktrees
- **Meta Skills** - Creating, testing, and sharing skills

### Spellbook Additions

- **Jujutsu Development Workflow** - Complete workflow from idea to PR, integrated with jj version control
- **Jujutsu Version Control** - Planning commits, commit stacks, stacked PRs, pre-commit hooks
- **Personal Standards** - Coding conventions via CLAUDE.md integration
- **Custom Slash Commands** - Tailored workflows for personal development

## Installation

### Option 1: Via Marketplace (Recommended)

In Claude Code, add the spellbook marketplace:

```bash
/plugin marketplace add cassiascheffer/spellbook-marketplace
```

Then install spellbook:

```bash
/plugin install spellbook@spellbook-marketplace
```

### Option 2: Local Installation

Clone this repository to your local machine:

```bash
git clone git@github.com:cassiascheffer/spellbook.git ~/.claude/plugins/spellbook
```

Or install via symlink if you keep it elsewhere:

```bash
ln -s /path/to/spellbook ~/.claude/plugins/spellbook
```

### Verify Installation

Check that commands appear:

```bash
/help
```

You should see spellbook commands:
```
/spellbook:brainstorm - Interactive design refinement
/spellbook:write-plan - Create implementation plan
/spellbook:execute-plan - Execute plan in batches
```

### Updating

If installed via marketplace, update with:

```bash
/plugin update spellbook
```

If installed locally, pull latest changes:

```bash
cd ~/.claude/plugins/spellbook
git pull
```

## Quick Start

### Using the Complete Workflow

**Start development work (idea → design → plan → implement → PR):**
```
Use spellbook:jj-development-workflow skill
```

Or use any of the slash commands:
```
/spellbook:brainstorm
/spellbook:write-plan
/spellbook:execute-plan
```

All three slash commands now invoke the complete jj-development-workflow.

### Using Individual Jujutsu Skills

For specific phases of the workflow:

**Plan work with empty commits:**
```
Use spellbook:jj-planning-commits skill
```

**Manage commit stacks:**
```
Use spellbook:jj-commit-workflow skill
```

**Create stacked PRs:**
```
Use spellbook:jj-stacked-prs skill
```

**Handle hook failures:**
```
Use spellbook:jj-pre-commit-hooks skill
```

### Automatic Skill Activation

Skills activate automatically when relevant. For example:
- `test-driven-development` activates when implementing features
- `systematic-debugging` activates when debugging issues
- `verification-before-completion` activates before claiming work is done
- `jj-pre-commit-hooks` activates when pre-commit hooks fail

## What's Inside

### Skills Library

All skills include group metadata in their frontmatter:
```yaml
---
name: skill-name
group: group-name
description: When to use this skill
---
```

This metadata enables commands to reference skill groups, making it clear to Claude which skills are relevant for each workflow phase.

**Development Workflows**
- **jj-development-workflow** - Complete workflow from idea to PR integrated with Jujutsu

**Jujutsu Version Control** (`skills/jj-*/`)
- **jj-planning-commits** - Plan work with empty commits
- **jj-commit-workflow** - Manage commit stacks and reorganize work
- **jj-stacked-prs** - Create and update PR stacks
- **jj-pre-commit-hooks** - Handle pre-commit hook failures

**Testing** (`skills/testing/`)
- **test-driven-development** - RED-GREEN-REFACTOR cycle
- **condition-based-waiting** - Async test patterns
- **testing-anti-patterns** - Common pitfalls to avoid

**Debugging** (`skills/debugging/`)
- **systematic-debugging** - 4-phase root cause process
- **root-cause-tracing** - Find the real problem
- **verification-before-completion** - Ensure it's actually fixed
- **defense-in-depth** - Multiple validation layers

**Collaboration** (`skills/collaboration/`)
- **dispatching-parallel-agents** - Concurrent subagent workflows
- **requesting-code-review** - Pre-review checklist
- **receiving-code-review** - Responding to feedback
- **using-git-worktrees** - Parallel development branches
- **finishing-a-development-branch** - Merge/PR decision workflow

**Meta** (`skills/meta/`)
- **writing-skills** - Create new skills following best practices
- **sharing-skills** - Contribute skills back via branch and PR
- **testing-skills-with-subagents** - Validate skill quality
- **using-spellbook** - Introduction to the skills system

### Commands

Each command maps to specific skill groups, making it clear which skills Claude should use:

- **brainstorm.md** - Brainstorm and refine ideas
  - Skill groups: `development-workflows`, `jujutsu-version-control`, `meta`
  - Primary: `jj-development-workflow` for complete idea-to-PR workflow
  - Use for: Turning rough ideas into actionable plans, creating new skills

- **write-plan.md** - Plan development work with Jujutsu commits
  - Skill groups: `jujutsu-version-control`, `development-workflows`
  - Primary: `jj-planning-commits` for task planning with empty commits
  - Use for: Creating structured development plans, organizing work into commits

- **execute-plan.md** - Implement features with TDD, debugging, and collaboration
  - Skill groups: `testing`, `debugging`, `collaboration`, `jujutsu-version-control`
  - Primary: `test-driven-development` (always start with tests)
  - Use for: Implementation with quality practices, handling bugs, code review

## How It Works

1. **SessionStart Hook** - Loads the `using-spellbook` skill at session start
2. **Skills System** - Uses Claude Code's first-party skills system
3. **Automatic Discovery** - Claude finds and uses relevant skills for your task
4. **Mandatory Workflows** - When a skill exists for your task, using it becomes required

## Philosophy

- **Test-Driven Development** - Write tests first, always
- **Systematic over ad-hoc** - Process over guessing
- **Complexity reduction** - Simplicity as primary goal
- **Evidence over claims** - Verify before declaring success
- **Domain over implementation** - Work at problem level, not solution level
- **Jujutsu for planning** - Plan with empty commits, implement sequentially

## Relationship to Superpowers

Spellbook is a fork, not a replacement for superpowers:

- **Upstream**: [obra/superpowers](https://github.com/obra/superpowers) - The original and actively maintained project
- **This Fork**: Personal customizations and Jujutsu workflows
- **Updates**: I may cherry-pick updates from superpowers, but this fork diverges independently
- **Contributing**: Contribute generic improvements back to superpowers upstream

## License

MIT License - see LICENSE file for details

Original work Copyright (c) 2025 Jesse Vincent
Modified work Copyright (c) 2025 Cassia Scheffer

## Support

- **Issues**: https://github.com/cassiascheffer/spellbook/issues
- **Upstream (Superpowers)**: https://github.com/obra/superpowers
