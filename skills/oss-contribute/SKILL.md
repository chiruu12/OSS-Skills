---
name: oss-contribute
description: |
  Research an issue deeply and present all context to the user — but the user writes the code.
  The LLM investigates code paths, finds relevant files, explains patterns, and identifies
  constraints. The user thinks through the approach and implements it. Use when ready to
  start working on an issue after oss-prep-to-contribute.
---

# Contribute

The LLM is your research assistant, not your ghostwriter. This skill digs deep into the issue — traces code, reads history, finds patterns, identifies constraints — then hands everything to you. You think. You decide. You code.

## Purpose

The fastest way to NOT learn from open source is to let an AI write the fix. This skill exists to make you a better developer: it gives you the context a senior contributor would have, then makes you do the work a senior contributor would do. The thinking is yours. The code is yours. The learning is yours.

## Prerequisites

- Repo forked, cloned, and set up (from `oss-prep-to-contribute`)
- Working branch created
- Existing tests pass
- User has explained their understanding of the codebase and the issue

## Process

### 1. Deep investigation

Use Agent with subagent_type=Explore to thoroughly investigate the issue. This is where the LLM does heavy lifting — reading code so the user doesn't have to grep blindly.

**Investigate**:
- The exact code path the issue describes (trace entry → processing → output)
- All files that touch this code path (imports, callers, callees)
- Existing tests for this area (what's covered, what's missing)
- Git history for the relevant files (why was it written this way? any related past fixes?)
- Similar patterns elsewhere in the codebase (how do they handle the same problem?)
- Error handling in the code path
- Edge cases mentioned in comments or tests

```bash
# Trace the code path
grep -rn "function_name\|class_name\|relevant_symbol" src/ --include="*.{ts,py,go,rs}"

# Git history for context
git log --oneline -15 -- "path/to/relevant/files"
git log --all --oneline --grep="keyword_from_issue"

# Find who last touched this code and why
git blame "path/to/file" -L {start},{end}

# Find related tests
grep -rn "describe.*relevant\|test.*relevant\|def test_relevant" tests/ test/ __tests__/ spec/
```

### 2. Present the research

Deliver findings in a structured format. Every claim must have a file:line reference.

```
## Issue Research: #{number}

### The Problem
{What's wrong or missing — in plain language, with code references}

### Relevant Code
| File | Lines | What it does | Why it matters |
|------|-------|-------------|----------------|
| `src/foo.ts` | 42-67 | Handles X | This is where the bug manifests |
| `src/bar.ts` | 15-30 | Calls foo | Passes incorrect argument |
| `tests/foo.test.ts` | 80-95 | Tests happy path | Missing test for edge case Y |

### How the Code Works
{Trace the flow step by step — entry point → processing → output}
{Explain WHY it's structured this way — design decisions, patterns used}

### Constraints
- {constraint 1 — e.g., "this function is called from 3 places, changes must be backwards compatible"}
- {constraint 2 — e.g., "the test suite mocks this dependency, so your tests should too"}

### Approach Options
1. **{Option A}**: {description} — Trade-off: {pro/con}
2. **{Option B}**: {description} — Trade-off: {pro/con}
{Present options, don't pick for the user}

### What Tests Should Cover
- {behavior 1 that needs testing}
- {behavior 2 that needs testing}
- {edge case that's currently untested}
```

### 3. Thinking gate — user explains the root cause

After presenting the research, stop and ask:

> "Based on what I've found — can you explain in your own words:
> 1. What's the root cause of this issue?
> 2. Which of the approaches do you want to take, and why?"

**Wait for their answer.** Do NOT proceed until they've articulated their understanding.

If their explanation has gaps:
- Point out WHAT's wrong: "You mentioned X, but look at `src/foo.ts:45` — it actually does Y"
- Don't give the correct answer — give them the reference and let them correct themselves
- Ask again once they've revised

### 4. Thinking gate — user describes their plan

Once the root cause is understood, ask:

> "Walk me through your implementation plan:
> 1. What files will you change?
> 2. What's the logic change in each file?
> 3. What tests will you add or modify?
> 4. Are there any edge cases you're handling?"

Review their plan:
- If something is missing, say WHAT's missing: "You haven't mentioned how this affects `src/bar.ts` — it also calls the function you're changing"
- If the approach won't work, explain WHY: "That approach would break the callers at `src/baz.ts:20` — look at how they use the return value"
- Don't rewrite their plan — poke holes and let them patch

### 5. User writes the code

The user implements their plan. During this phase, the LLM's role changes:

**What the LLM DOES**:
- Answer specific questions about the codebase ("what does this function expect as input?")
- Point to examples of patterns in the repo ("how do other modules handle this?")
- Review the user's code when asked ("does this look right?") — identify issues but don't fix them
- Run tests when asked and explain failures

**What the LLM DOES NOT DO**:
- Write the implementation
- Write the tests
- Auto-complete code blocks
- Suggest "here's how I'd do it" with full code

If the user asks the LLM to write code, redirect:

> "I can point you to similar patterns in the codebase, explain what the function should do, or review code you've written. But the implementation should be yours — that's how you learn. What specific part are you stuck on?"

### 6. Thinking gate — user explains their changes

After implementation, before committing:

> "Before we move to submitting — explain what you changed and why:
> 1. What did you modify in each file?
> 2. Why did you choose this approach over the alternatives?
> 3. How do your tests verify the fix?
> 4. Any edge cases you're unsure about?"

This catches misunderstandings before they become PR review comments.

If the user can articulate all four clearly, they're ready. If not, point them back to the relevant code.

### 7. Verify before handoff

```bash
# Run the full test suite
# {repo-specific test command}

# Run linting
# {repo-specific lint command}

# Check the diff is clean
git diff --stat
```

If tests or lint fail, explain WHAT failed and WHERE — don't fix it. Point the user to the failing assertion and the relevant code.

## Related Skills

- **Previous step**: ← `oss-prep-to-contribute` — set up the environment and build understanding
- **Next step**: → `oss-submit-pr` — submit the PR following repo guidelines
- **If significant rework needed**: ← `oss-post-pr` sends back here after reviewer feedback

## Anti-patterns

- **DO NOT** write code for the user — not even "small helper functions" or "just the boilerplate"
- **DO NOT** skip thinking gates — they're not optional checkpoints, they're the whole point
- **DO NOT** present approach options with a clear recommendation — present trade-offs and let the user decide
- **DO NOT** rubber-stamp the user's plan — actively look for gaps and edge cases they missed
- **DO NOT** let "I'm stuck" turn into "let me write it for you" — ask what specifically is confusing and point to relevant code
