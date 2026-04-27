---
name: oss-bounty-prep
description: |
  Understand bounty scope, rules, requirements, and payment terms before starting work.
  Reads bounty platform details, repo guidelines, and prior submissions to prevent wasted
  effort. Use after finding a bounty with oss-find-bounty, before starting implementation.
---

# Bounty Prep

You found a bounty. Before you write a single line of code, understand exactly what "done" means, what the rules are, and what will get your submission rejected. Most bounty disputes happen because the contributor didn't read the terms.

## Purpose

Bounties are more structured than regular contributions — there's money involved, which means clearer acceptance criteria but also stricter rules. This skill reads every relevant document, checks for hidden requirements, and makes sure you know what you're signing up for before you invest time. It also checks whether the bounty has a history of paying out or leaving contributors hanging.

## Prerequisites

- A specific bounty issue identified (from `oss-find-bounty`)
- `gh` CLI authenticated

## Process

### 1. Read the bounty terms

```bash
# Read the full issue with all comments
gh issue view {number} -R {owner}/{repo} --json body,comments,labels,author,authorAssociation,createdAt,updatedAt

# Check for bounty platform details in the issue or linked pages
# Look for: Algora badges, IssueHunt links, Gitcoin references
```

Extract and present:

```
## Bounty Terms: #{number}

**Amount**: ${amount}
**Platform**: {Algora / IssueHunt / Gitcoin / repo-direct / other}
**Posted by**: {author} ({authorAssociation})
**Posted date**: {date}
**Deadline**: {date or "none stated"}
**Acceptance criteria**: {what constitutes "done" — from the issue description}
**Payment method**: {platform escrow / direct / crypto / unclear}
**Requires approval to start**: {yes / no / unclear}
```

### 2. Check bounty platform specifics

If the bounty is through a platform, verify:

- **Algora**: Check if the bounty is funded (not just a label). Look for the Algora bot comment confirming the amount
- **IssueHunt**: Check the funding status and payout terms
- **Gitcoin**: Verify the grant round status and payment mechanism
- **Direct/repo-based**: Check if the repo has a track record of paying out

```bash
# Check for bot comments confirming bounty
gh api repos/{owner}/{repo}/issues/{number}/comments --jq '.[] | select(.user.login | test("algora|issuehunt|gitcoin"; "i")) | {user: .user.login, body: .body}'
```

### 3. Verify payout history

**Critical step.** Check if this repo/org actually pays bounties:

```bash
# Find closed bounty issues
gh issue list -R {owner}/{repo} --label "bounty" --state closed --limit 10 \
  --json number,title,closedAt,comments

# For each closed bounty, check if there are "paid" / "completed" signals
```

Look for:
- Closed bounty issues with "paid" or "completed" labels
- Thank-you comments from maintainers confirming payment
- Platform transaction confirmations

If zero evidence of past payouts, **warn the user**: "This repo has bounty labels but no evidence of actually paying them. Proceed with caution."

### 4. Read contribution requirements

Same as `oss-prep-to-contribute` — but with bounty-specific additions:

```bash
gh api repos/{owner}/{repo}/contents/CONTRIBUTING.md --jq '.content' | base64 -d 2>/dev/null
```

Bounty-specific checks:
- Does the repo have a BOUNTY.md or bounty policy?
- Does the bounty require registering on a platform first?
- Are there submission format requirements (specific branch naming, PR title format)?
- Is there a review SLA (how long until the maintainer reviews bounty submissions)?

### 5. Analyze prior submissions

Check if others have attempted this bounty:

```bash
# Check for closed PRs that reference this issue
gh pr list -R {owner}/{repo} --state closed --search "#{number}" --json number,title,author,closedAt,mergedAt

# Check for open PRs
gh pr list -R {owner}/{repo} --state open --search "#{number}" --json number,title,author,createdAt
```

If prior submissions were rejected:
- Read the PR to understand what approach they took
- Read the review comments to understand WHY it was rejected
- Present this to the user: "A previous submission was rejected because {reason}. Your approach should address this."

If a PR is currently open:
- Check when it was submitted and if it's actively being reviewed
- **Warn the user**: "There's an active PR for this bounty. You may be competing or wasting time."

### 6. Scope the work

Based on the bounty terms and codebase analysis:

```
## Scope Assessment

**What needs to change**: {specific files/modules}
**Estimated effort**: {hours}
**Key risks**:
- {risk 1 — e.g., "acceptance criteria are vague — 'improved performance' without a benchmark target"}
- {risk 2 — e.g., "touches auth module — high scrutiny area, expect thorough review"}
**Missing from the bounty description**: {anything ambiguous or unstated}
**Questions to ask maintainer before starting**: {if any}
```

### 7. Thinking gate — user commits or walks

> "Here's what I found about this bounty. Before you start:
> 1. Are the acceptance criteria clear enough that you'd know when you're done?
> 2. Is the effective rate (${amount} for ~{hours} of work) worth your time?
> 3. Are there any red flags — unclear terms, no payout history, competing submissions?
> 4. What's your plan if the maintainer moves the goalposts after you submit?"

The user must decide whether to proceed. If proceeding:

- If platform requires registration, help the user register
- If approval is needed before starting, draft a comment (user writes, LLM reviews)
- Comment claiming the bounty (keep it short — one sentence)

```bash
gh issue comment {number} -R {owner}/{repo} --body "I'd like to work on this bounty. Will submit a PR within [timeframe]."
```

## Related Skills

- **Previous step**: ← `oss-find-bounty` — find the bounty
- **Next step**: → `oss-prep-to-contribute` — set up the dev environment and understand the codebase
- **Then**: → `oss-contribute` → `oss-submit-pr` → `oss-post-pr` (standard workflow)

## Anti-patterns

- **DO NOT** start working before verifying payout history — protect the user's time
- **DO NOT** ignore competing submissions — if someone else is actively working on it, the user should know
- **DO NOT** gloss over vague acceptance criteria — ambiguity is where bounty disputes happen
- **DO NOT** let the user skip the scope assessment — underestimating effort on a bounty leads to abandoned PRs
- **DO NOT** write the claim comment for the user — they write it, keep it to one sentence
