---
description: Execute development plans with test-driven development, debugging when needed, and code review
---

**Primary skill (MANDATORY):** Use `test-driven-development` for ALL implementation work

**When to use:** Implementing features, fixing bugs, making any code changes

**The RED-GREEN-REFACTOR cycle:**
1. Write the test FIRST
2. Watch it FAIL (proves test actually checks the behavior)
3. Write minimal code to make it pass
4. Refactor while tests stay green

**Additional skills - Use when needed:**

**When tests are flaky or have timing issues:**
- `condition-based-waiting` - Replace arbitrary timeouts with condition polling
- `testing-anti-patterns` - Avoid testing mocks instead of behavior

**When encountering bugs or test failures:**
- `systematic-debugging` - MUST find root cause before attempting fixes (4-phase framework)
- `root-cause-tracing` - Trace errors backward through call stack to find the source
- `defense-in-depth` - Add validation at multiple system layers to prevent bugs structurally

**Before claiming work is complete:**
- `verification-before-completion` - MUST run verification commands and confirm output (evidence before assertions)

**When working with Jujutsu commits:**
- `jj-commit-workflow` - Manage commit stacks during implementation
- `jj-stacked-prs` - Create PR stacks from commits when ready to ship
- `jj-pre-commit-hooks` - Handle pre-commit hook failures without bypassing safety

**When ready for code review:**
- `requesting-code-review` - Dispatch code-reviewer subagent before merging
- `receiving-code-review` - Respond to feedback with technical rigor, not performative agreement

**When facing multiple independent failures:**
- `dispatching-parallel-agents` - Dispatch multiple agents to investigate concurrently (3+ independent issues)
