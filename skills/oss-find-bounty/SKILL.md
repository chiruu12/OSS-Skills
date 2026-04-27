---
name: oss-find-bounty
description: |
  Find open source bounty programs and bounty-eligible issues. Searches for paid
  contributions on platforms like GitHub Sponsors, Open Collective, Algora, and
  repos with bounty labels. Use when looking for paid OSS work, bounty hunting,
  or monetizing your contributions.
---

# Find Bounty

Find issues with bounties attached — real money for real contributions. This skill searches across bounty platforms and repo labels, checks the bounty terms, and matches opportunities to your skills. You evaluate which ones are worth your time.

## Purpose

Bounties exist because maintainers need specific work done badly enough to pay for it. That means the work is valued, the review will be prioritized, and there's a financial incentive to ship quality. But bounties also attract low-effort submissions — AI-generated slop, half-baked PRs, and people who claim issues and never deliver. This skill helps you find legitimate bounties and stand out by actually understanding the problem before you start.

## Prerequisites

- A GitHub account with `gh` CLI authenticated
- Your skill profile (languages, frameworks, experience level)

## Process

### 1. Understand the contributor's skills

If not already known from config or a previous skill invocation, ask:

- "What languages and frameworks are you comfortable with?"
- "What's your experience level with open source contributions?"
- "Any minimum bounty amount you're targeting?"

### 2. Search for bounty-eligible issues

**GitHub-native bounty labels:**

```bash
# Common bounty labels across repos
for label in "bounty" "💰" "💰 bounty" "paid" "sponsored" "reward" "cash" "funded"; do
  gh search issues --label "$label" --state open --sort created --limit 10 \
    --json repository,number,title,labels,assignees,createdAt,updatedAt 2>/dev/null
done
```

**Bounty platform search:**

Research these platforms for the user's skill match:
- **Algora** (console.algora.io) — bounties on GitHub issues, typically $50-$500
- **Gitcoin** — crypto/web3 bounties, larger rewards
- **IssueHunt** — bounty funding for GitHub issues
- **Open Collective** — funded projects that pay for contributions
- **Polar.sh** — funding for maintainers, sometimes has bounties
- **GitHub Sponsors** — some sponsors fund specific issues

```bash
# Check if a specific repo uses bounty platforms
gh api repos/{owner}/{repo}/contents/.github --jq '.[].name' 2>/dev/null | grep -i "bounty\|fund\|sponsor"

# Check repo README for bounty platform badges
gh api repos/{owner}/{repo}/contents/README.md --jq '.content' | base64 -d 2>/dev/null | grep -i "bounty\|algora\|gitcoin\|issuehunt\|polar"
```

**Search by user's stack:**

```bash
# Find bounty issues matching user's languages
gh search issues --label "bounty" --language {user_language} --state open --sort created --limit 20 \
  --json repository,number,title,labels,assignees,createdAt
```

### 3. Verify bounty legitimacy

For each bounty found, verify:

| Check | What to look for | Red flag |
|-------|-----------------|----------|
| **Funder is real** | Bounty posted by maintainer or known platform | Random user posted "$1000 bounty" in a comment |
| **Amount is stated** | Clear dollar amount or funding source | Vague "there may be a bounty" |
| **Terms are clear** | What constitutes completion, who approves | No acceptance criteria |
| **Repo is active** | Recent commits, merged PRs, maintainer responses | Last commit 6+ months ago |
| **No one is already working on it** | No assignee, no "claiming this" comments, no open PRs | Someone claimed it 2 weeks ago with no update |
| **Timeline is reasonable** | Bounty isn't expiring tomorrow | "Must be done by EOD" |

```bash
# Check repo activity
gh api repos/{owner}/{repo} --jq '{pushed_at: .pushed_at, open_issues: .open_issues_count, stars: .stargazers_count}'

# Check if issue has linked PRs
gh pr list -R {owner}/{repo} --search "#{number}" --state open --json number,title,author
```

### 4. Assess bounty vs effort

For each legitimate bounty, estimate:

```
### Bounty: #{number} — {title}
- **Repo**: {owner}/{repo} ({stars} stars, last push {date})
- **Amount**: ${amount} (via {platform})
- **Complexity**: {low / medium / high}
- **Estimated effort**: {hours — based on issue scope and codebase size}
- **Effective rate**: ~${amount/hours}/hr
- **Skill match**: {which of your skills this exercises}
- **Competition**: {how many people are likely to submit — based on bounty age and amount}
- **Link**: {url}
```

### 5. Check contribution eligibility

Same as `oss-find-issue` step 2 — verify the repo accepts outside contributions:

```bash
gh api repos/{owner}/{repo}/contents/CONTRIBUTING.md --jq '.content' | base64 -d 2>/dev/null

# Check external PR merge rate
gh pr list -R {owner}/{repo} --state merged --limit 20 --json authorAssociation | \
  jq '[.[] | select(.authorAssociation != "MEMBER" and .authorAssociation != "OWNER")] | length'
```

Also check bounty-specific terms:
- Does the bounty require approval before starting?
- Is there a submission deadline?
- Payment method (direct, platform escrow, crypto)?
- Does accepting the bounty require any agreement or registration?

### 6. Present recommendations

Top 3 bounties, ranked by (effective rate * skill match * legitimacy):

```
## Recommended Bounties

### 1. #{number} — {title} — ${amount}
- **Repo**: {owner}/{repo}
- **Why this one**: {one sentence — why it's a good pick for THIS user}
- **Scope**: {what needs to change}
- **Effective rate**: ~${rate}/hr
- **Risk**: {low — clear terms, active repo / medium — some ambiguity / high — unclear terms}
- **Link**: {url}
```

### 7. Thinking gate — user evaluates

> "Before you commit to a bounty — is the effective rate worth your time? Do you have the skills to complete this in a reasonable timeframe? Bounties that go unclaimed for weeks usually have a reason — do you see why others haven't picked this up?"

The user must articulate why they're picking this specific bounty and what potential blockers they see.

## Related Skills

- **Next step**: → `oss-bounty-prep` — understand the bounty terms, scope, and requirements in detail
- **Alternative**: → `oss-find-issue` — if no good bounties exist, find regular contribution opportunities
- **After bounty prep**: → `oss-contribute` → `oss-submit-pr` → `oss-post-pr` (same workflow)

## Anti-patterns

- **DO NOT** claim bounties you can't complete — it blocks other contributors and damages your reputation
- **DO NOT** chase high-dollar bounties that are way above your skill level — start small, build reputation
- **DO NOT** ignore bounty terms — some require approval before starting, some have deadlines
- **DO NOT** submit AI-generated code to bounty issues — maintainers paying for work expect quality, and they'll check
- **DO NOT** work on bounties from inactive repos — if the last commit was months ago, your PR won't get reviewed
