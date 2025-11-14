---
name: jj-pre-commit-hooks
group: jujutsu-version-control
description: Use when pre-commit hooks fail during jj git push - systematic protocol for fixing failures without bypassing safety mechanisms
---

# Pre-Commit Hook Protocol for Jujutsu

> **Part of the complete workflow:** This skill is used in Phase 6 (Hook Failures) of `spellbook:jj-development-workflow`. For the full end-to-end process from idea to PR, use that skill instead.

## When to Use This Skill

Use this skill when:
- You're in Phase 6 of the jj-development-workflow (handling hook failures)
- `jj git push` fails due to pre-commit hooks
- Linters, formatters, or tests fail during push
- Any automated quality check blocks your push
- You need to fix issues before pushing to remote

**For complete workflow (idea → design → plan → implement → PR):** Use `spellbook:jj-development-workflow`

## What Are Pre-Commit Hooks?

Pre-commit hooks are automated scripts that run before code is pushed to a remote repository. They check for:
- Code formatting issues (prettier, black, rustfmt, ktlint, rubocop)
- Linting errors (eslint, ruff, clippy, ktlint, rubocop)
- Type errors (mypy, typescript, sorbet)
- Build failures (gradle, maven, rake, npm build, tsc)
- Test failures
- Security issues
- Commit message format

**Important:** Jujutsu respects Git hooks when pushing to Git remotes via `jj git push`.

## The 5-Step Protocol

When hooks fail during `jj git push`, follow this systematic protocol:

### Step 1: Read Error Output

**Display the complete error message to the user.**

Don't summarize or skip details. The full output contains essential information about what failed and why.

**Example output:**
```
Running pre-commit hooks...
[FAILED] eslint
  src/api/users.ts:42:3 - Unused variable 'userId'
  src/api/auth.ts:18:1 - Missing semicolon
[PASSED] prettier
[PASSED] tests
```

### Step 2: Identify Failure

**State clearly:**
- Which tool failed (eslint, ruff, tests, etc.)
- Why it failed (specific errors)
- Which files are affected

**Example:**
```
eslint failed with 2 errors:
1. Unused variable 'userId' in src/api/users.ts:42
2. Missing semicolon in src/api/auth.ts:18
```

### Step 3: Plan Fix

**Describe the fix you will apply and explain why it addresses the root cause.**

Be specific about what you'll change and why that fixes the problem.

**Example:**
```
I will:
1. Remove the unused 'userId' variable from src/api/users.ts:42
   - Root cause: Variable was declared but never used after refactoring
2. Add semicolon to src/api/auth.ts:18
   - Root cause: Missing required semicolon per project style

These fixes address the eslint errors directly and will allow the hook to pass.
```

### Step 4: Apply Fix

**Fix the issue, then use `jj edit` to modify the failing commit:**

```bash
# Edit the commit that failed hooks
jj edit <change-id>

# Make corrections to fix the issues
# ... apply fixes ...

# Finalize changes (jj auto-tracks them)
jj new

# Re-attempt push
jj git push --bookmark <bookmark-name>
```

**Critical workflow:**
1. Use `jj edit <change-id>` to check out the failing commit
2. Apply your fixes
3. Use `jj new` to finalize the changes (auto-tracked)
4. Re-run `jj git push`

### Step 5: Verify

**Only proceed when all hooks pass.**

If hooks still fail:
- Return to Step 1
- Read new error output
- Continue through protocol

**Do not bypass hooks.** If you cannot fix the issues, ask the user for help.

## Forbidden Practices

### Never Bypass Hooks

**FORBIDDEN:**
- Using `--no-verify` flag
- Using `-n` flag
- Using `--force` flag with `jj git push`
- Any mechanism to skip hooks
- Commenting out hook configuration

**Why:** Hooks are safety mechanisms. Bypassing them allows problematic code into the repository. Using `--force` can skip hooks and destructively overwrite remote history.

### Accountability Checks

**Before every `jj git push`, verify:**

- ❌ Am I bypassing safety mechanisms?
- ❌ Would this violate CLAUDE.md instructions?
- ❌ Am I choosing convenience over quality?

**If any answer is "yes" or "maybe":** Explain concern to user before proceeding.

## Quality Mindset

### Treat Failures as Learning Opportunities

When hooks fail:
1. **Don't get flustered** - This is normal and expected
2. **Research the specific error** - Understand what the tool is checking
3. **Learn from it** - Build understanding of the tool's purpose
4. **Explain what you learned** - Share understanding with user

### Example Learning Process

```
Hook failed: ruff found F401 (unused import)

What I learned:
- F401 is ruff's code for unused imports
- This import was likely left after refactoring
- Removing dead code improves maintainability
- ruff helps catch these automatically

I'm removing the unused import and will be more careful about cleaning up imports during refactoring.
```

## Common Hook Failures

### Formatting Issues

**Examples:** prettier, black, rustfmt

**Typical fix:**
```bash
# Edit the failing commit
jj edit <change-id>

# Run formatter
npm run format
# or: black .
# or: cargo fmt

# Finalize and push
jj new
jj git push --bookmark <name>
```

### Linting Errors

**Examples:** eslint, ruff, clippy

**Typical fix:**
```bash
# Edit the failing commit
jj edit <change-id>

# Fix specific linting errors
# (Use linter output to guide fixes)

# Some linters can auto-fix
npm run lint:fix
# or: ruff check --fix

# Finalize and push
jj new
jj git push --bookmark <name>
```

### Test Failures

**Examples:** jest, pytest, cargo test

**Typical fix:**
```bash
# Edit the failing commit
jj edit <change-id>

# Run tests to see failures
npm test
# or: pytest
# or: cargo test

# Fix the failing tests or code
# ...

# Verify tests pass
npm test

# Finalize and push
jj new
jj git push --bookmark <name>
```

### Type Errors

**Examples:** typescript, mypy, sorbet

**Typical fix:**
```bash
# Edit the failing commit
jj edit <change-id>

# Run type checker
npm run type-check
# or: mypy .
# or: srb tc

# Fix type errors
# ...

# Verify types pass
npm run type-check

# Finalize and push
jj new
jj git push --bookmark <name>
```

### Build Failures

**Examples:** gradle (Kotlin/Java), maven (Java), rake (Ruby), tsc (TypeScript), npm build

**Typical fix:**
```bash
# Edit the failing commit
jj edit <change-id>

# Run build to see failures
./gradlew build
# or: mvn compile
# or: rake build
# or: npm run build
# or: tsc

# Fix the build errors
# ...

# Verify build passes
./gradlew build

# Finalize and push
jj new
jj git push --bookmark <name>
```

## Working With Multiple Commits

### When Multiple Commits Fail Hooks

If you're pushing multiple commits and hooks fail:

```bash
# Identify which commit failed
# (Hook output usually shows commit hash)

# Edit that specific commit
jj edit <failing-commit-id>

# Fix issues
# ...

# Continue - jj auto-rebases child commits
jj new

# Push again
jj git push --bookmark <name>
```

**Note:** Jujutsu automatically rebases child commits when you modify a parent, so the entire stack stays current.

### Running Hooks Locally Before Push

To catch issues early:

```bash
# Run linter
npm run lint

# Run formatter
npm run format

# Run tests
npm test

# Run type check
npm run type-check

# If all pass, then push
jj git push --bookmark <name>
```

## Integration with Other Skills

- **Complete workflow:** `spellbook:jj-development-workflow` orchestrates the full process
- **Before pushing:** Verify work is complete and tests pass
- **For verification:** Use `spellbook:verification-before-completion` before claiming done
- **During commit workflow:** Use `spellbook:jj-commit-workflow` to organize work
- **For stacked PRs:** Use `spellbook:jj-stacked-prs` after hooks pass

## Why This Protocol Matters

### For Code Quality

- Hooks catch issues before they reach the repository
- Consistent code style across the project
- Tests verify functionality
- Type safety prevents runtime errors

### For Collaboration

- Other developers get clean, working code
- Reduces time spent on code review
- Prevents broken builds
- Maintains project standards

### For AI Agents

- Systematic approach prevents panic
- Clear steps to follow
- Learning opportunity from each failure
- Builds tool competence over time

## Example: Complete Hook Failure Resolution

### Initial Push Attempt

```bash
jj git push --bookmark feature-auth

Running pre-commit hooks...
[FAILED] eslint
  src/auth.ts:15:7 - 'user' is assigned but never used
[FAILED] tests
  FAIL src/auth.test.ts
    ✕ should validate JWT token (5 ms)
      Expected: true
      Received: false
```

### Step 1: Read Error Output
"The push failed due to two issues:
1. eslint found an unused variable
2. A test is failing"

### Step 2: Identify Failure
"eslint failed: Unused variable 'user' at src/auth.ts:15
tests failed: JWT validation test in src/auth.test.ts is failing"

### Step 3: Plan Fix
"I will:
1. Check src/auth.ts:15 and either use the variable or remove it
2. Investigate why the JWT validation test is failing
3. Fix the underlying issue in the auth logic or update the test"

### Step 4: Apply Fix
```bash
jj edit <auth-commit-id>

# Fix unused variable - turns out it should be returned
# Fix JWT validation - was using wrong secret key

jj new
jj git push --bookmark feature-auth
```

### Step 5: Verify
```
Running pre-commit hooks...
[PASSED] eslint
[PASSED] tests
✓ All checks passed
```

**Success!** Hooks pass, push succeeds.

## Remember

**Quality tools are guardrails (helpful), not barriers (blocking).**

Hooks exist to catch mistakes early. Embrace them, learn from them, and never bypass them.
