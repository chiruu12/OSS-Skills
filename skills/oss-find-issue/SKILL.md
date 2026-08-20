---
name: oss-find-issue
description: |
  Find unclaimed open source issues that match the user's skills and experience level.
  Searches for issues created by maintainers/org admins, checks contribution eligibility,
  and ranks by learning value. Use when looking for an issue to contribute to, starting
  OSS contributions, or finding GSoC-friendly issues.
---

# Find Issue

Find a real, unclaimed issue that matches your skills. from repos that actually want your contribution. This skill does the research; you decide what's worth your time.

## Purpose

Not every issue is worth picking up. Random issues filed by drive-by users often get closed without merging. Issues from maintainers and org members are the ones that get reviewed and merged. This skill finds those, checks if the repo accepts outside contributions, and matches issues to what you actually know. so you don't waste weeks on something that gets rejected or ignored.

## Prerequisites

- A GitHub account with `gh` CLI authenticated
- A target repo or area of interest (language, framework, domain)

## Process

### 1. Understand the contributor

Before searching for anything, understand who's contributing. Ask the user:

- "What languages and frameworks are you comfortable with?"
- "What's your experience level? (first contribution / a few PRs merged / experienced contributor)"
- "Any specific repos or domains you're interested in? (web, CLI tools, data, infra, etc.)"

Do NOT skip this. Issue matching without knowing the contributor is useless.

### 2. Check contribution eligibility

Before looking at a single issue, verify the repo accepts outside contributions:

```bash
# Fetch contribution guidelines
gh api repos/{owner}/{repo}/contents/CONTRIBUTING.md --jq '.content' | base64 -d 2>/dev/null
gh api repos/{owner}/{repo}/contents/.github/CONTRIBUTING.md --jq '.content' | base64 -d 2>/dev/null
gh api repos/{owner}/{repo}/contents/CODE_OF_CONDUCT.md --jq '.content' | base64 -d 2>/dev/null
```

Check for:
- **Explicit "we welcome contributions"**: if absent, that's a yellow flag
- **CLA requirements**: some orgs require signing a Contributor License Agreement before any PR
- **"Internal only" signals**: some repos state contributions are restricted to org members
- **Stale contribution docs**: if CONTRIBUTING.md references tools/processes from 3+ years ago, the repo may not be actively maintained
- **Recent external PRs merged**: strongest signal that outside contributions are welcome

```bash
# Check if external PRs actually get merged
gh api "repos/{owner}/{repo}/pulls?state=closed&per_page=50" \
  --jq '.[] | select(.merged_at != null)
        | select(.author_association != "MEMBER" and .author_association != "OWNER")
        | "\(.author_association)\t\(.user.login)\t\(.title)"'
```

If the repo doesn't accept outside contributions, **tell the user immediately** and suggest alternatives. Don't waste their time.

### 3. Identify maintainers and core contributors

Issues filed by maintainers carry more weight. they represent actual project priorities.

```bash
# Get repo collaborators and recent committers
gh api repos/{owner}/{repo}/contributors --jq '.[0:10] | .[].login'

# Check issue author association
gh issue list -R {owner}/{repo} --state open --json number,title,author,labels,assignees,authorAssociation,createdAt --limit 50
```

Filter for issues where `authorAssociation` is `OWNER`, `MEMBER`, or `COLLABORATOR`. These are the issues maintainers actually care about.

### 4. Search for matching issues

```bash
# Good first issues from maintainers
gh issue list -R {owner}/{repo} --label "good first issue" --state open \
  --json number,title,labels,assignees,comments,createdAt,authorAssociation,author

# Help wanted
gh issue list -R {owner}/{repo} --label "help wanted" --state open \
  --json number,title,labels,assignees,comments,createdAt,authorAssociation,author

# GSoC-specific (if applicable)
gh issue list -R {owner}/{repo} --label "gsoc" --state open \
  --json number,title,labels,assignees,comments,createdAt,authorAssociation,author
```

For each candidate issue, read the whole thing:

```bash
gh issue view {number} -R {owner}/{repo} --json body,comments,assignees,labels,author,authorAssociation,createdAt,updatedAt
```

Step 5 decides whether anyone else already has it. Do not skip ahead to ranking.

### 5. Rule out issues that are already someone else's

An issue somebody claimed is not available. Taking it anyway costs more than the
merge is worth: maintainers notice, and the contributor you raced remembers. Run
every check below on each candidate. Any one of them hitting means drop the issue
and move on.

```bash
# Assignees, labels, and every comment. Not just the recent ones.
gh issue view {number} -R {owner}/{repo} \
  --json state,assignees,labels,comments,author,authorAssociation

# Cross-referenced pull requests. This is the check people skip, and it is the
# one that catches someone who opened a PR without ever commenting.
gh api repos/{owner}/{repo}/issues/{number}/timeline --paginate \
  --jq '.[] | select(.event == "cross-referenced") | .source.issue
        | select(.pull_request != null)
        | "\(.state)\t\(.repository.full_name)#\(.number)\t\(.user.login)"'
```

That prints state, source repo, and author for every referenced PR. Cross-repo
references are noise: a fork or an unrelated project mentioning the issue does
not claim it. Only rows from `{owner}/{repo}` count.

Drop the issue if any of these is true:

| Signal | What to look for |
|--------|------------------|
| Assigned | Any assignee who is not the user and is not a bot |
| Reserved by label | `assigned`, `claimed`, `taken`, `in progress`, `wip`, `has-pr`, `pr-open`, or whatever the repo uses locally |
| Claimed in a comment | Read every comment, oldest to newest: "I'll take this", "can I work on this", "please assign me", "/assign", "I've opened a PR", "working on it", "on it" |
| Open cross-referenced PR | A pull request in this repo, still open, authored by someone else |
| No longer open | The issue was closed while the user was reading it |

Three rules decide the close calls:

- **A closed or merged PR is history, not a claim.** Somebody's abandoned attempt
  does not reserve the issue. Only an open PR does.
- **Bots are not people.** A `[bot]` assignee or comment claims nothing.
- **Unknown is not clear.** If the timeline call errors, the comment list is
  truncated, or you cannot tell who a referenced PR belongs to, treat the issue as
  taken. Guessing is how the user ends up in a race they did not know they entered.

**Scan the comments mechanically.** Reading every comment by hand stops working
past a handful of candidates:

```bash
CLAIM='(?i)(i.ll take|i will take|can i (work on|take|have|be assigned)|(i.d|i would) like to (work on|take|try|tackle|pick up)|please assign|/assign|working on (this|it)|i (have |.ve )?(opened|raised|submitted) a (pr|pull request)|taking (this|it) (up|on))'
BOT='(?i)(\[bot\]$|[-_]bot$|robot$|^bot$|[-_]ci$)'

gh api repos/{owner}/{repo}/issues/{number}/comments --paginate \
  | jq -r --arg re "$CLAIM" --arg bot "$BOT" \
    '[.[] | select(.body | test($re))]
     | (map(select(.user.type != "Bot") | select(.user.login | test($bot) | not))
        | map(.user.login) | unique | .[] | "claimant: \(.)")
     , (map(select(.user.type == "Bot" or (.user.login | test($bot))))
        | map(.user.login) | unique | .[] | "check by hand: \(.)")'
```

Use the REST endpoint, not `gh issue view --json comments`. Two reasons. It strips
the `[bot]` suffix from bot logins and gives you no type field, so from its output
you cannot tell a bot from a person. And triage bots post the exact phrases in
`CLAIM`, because instructing people to comment `/assign` is what they are for.

`user.type` alone is not enough either. On `kubernetes/kubernetes` the prow bot
reports `type: Bot` and gets filtered, while `k8s-ci-robot` reports `type: User`
and does not. The login pattern is what catches the second one.

The login pattern is a guess, so it reports rather than discards. A person can be
called `nick-ci` or `deathrobot`, and silently dropping them would turn a claimed
issue into an available-looking one, which is the expensive direction to be wrong
in. Anything on a `check by hand` line, open the issue and read those comments
yourself.

An empty result is not proof the issue is free. Somebody can open a PR without ever
commenting, which is what the timeline check catches. Run both, always.

**If the label is a race, stop using the label.** In a popular repo a
`good first issue` is watched by hundreds of people and claimed within hours of
being applied. Count the claimants across the whole current crop before investing
in any single one:

```bash
for n in $(gh issue list -R {owner}/{repo} --label "good first issue" --state open \
             --limit 10 --json number --jq '.[].number'); do
  c=$(gh api repos/{owner}/{repo}/issues/$n/comments --paginate \
      | jq -r --arg re "$CLAIM" --arg bot "$BOT" \
        '[.[] | select(.user.type != "Bot") | select(.user.login | test($bot) | not)
          | select(.body | test($re)) | .user.login] | unique | length')
  echo "#$n claimants: $c"
done
```

Several issues carrying two or more distinct claimants means the label is a
feeding frenzy and the user is arriving late. Two ways out, both better than
racing:

- **Pick an issue with no beginner label.** They draw far less traffic, and a
  plain maintainer-filed bug is worth more to the project than a curated starter
  task. The user is usually more capable than the label assumes.
- **Stop picking and start finding.** `oss-find-real-issues` sources work nobody
  has filed yet, and nobody can race the user for an issue that does not exist.
  In repos where every labeled issue is triple-claimed, this is the faster path,
  not the fallback.

**Going quiet does not release a claim.** Someone who claimed an issue three weeks
ago and disappeared still holds it, unless a maintainer has explicitly reopened it
to others. Do not open a competing PR, do not prepare one "just in case", and do
not ask them to hand it over.

### 6. Filter and rank

**Must-have filters** (skip issue if any fail):
- Cleared every check in step 5. nobody else has claimed it
- Created or updated within last 6 months
- Clearly scoped. you can describe what needs to change in 2 sentences
- Filed by maintainer/member/collaborator (or explicitly endorsed by one in comments)

**Ranking criteria**:

| Criteria | Weight | What to check |
|----------|--------|---------------|
| Skill match | High | Does the issue require languages/frameworks the user knows? |
| Learning value | High | Will the user learn something non-trivial? |
| Clear scope | High | Is the expected outcome well-defined? |
| Maintainer engagement | Medium | Has a maintainer commented or labeled recently? |
| Impact | Medium | Does this affect real users or is it cosmetic? |
| Complexity fit | Medium | Not trivial (typo fix) but not overwhelming (full rewrite) |

### 7. Present recommendations

For each of the top 3 issues, present:

```
### #{number} - {title}
- **Filed by**: {author} ({authorAssociation})
- **Why this issue**: {one sentence. what makes it a good pick for THIS user}
- **What it involves**: {what needs to change, in plain language}
- **Skills exercised**: {what the user will learn/practice}
- **Complexity**: {low / medium / high. relative to user's stated experience}
- **Maintainer activity**: {last maintainer comment date, engagement level}
- **Link**: {url}
```

### 8. Thinking gate: user decides

**Do NOT let the user just say "number 1."** Ask:

> "Before you pick one. tell me:
> 1. Why does this issue interest you? (What about it matches your skills from step 1?)
> 2. What do you think the fix might involve? (Look at the 'What it involves' section above. does that match what you'd expect?)
> 3. Is the complexity right for you? (Not so easy you learn nothing, not so hard you get stuck for weeks)"

If the user can't articulate why, that's a signal to dig deeper or suggest a different issue.

### 9. Claim the issue

Once the user has chosen AND explained their reasoning:

**Check the repo's assignment workflow first.** Some repos require maintainer assignment rather than self-claiming. Look for:
- "Please request to be assigned" in CONTRIBUTING.md or issue templates
- Issues with an `assignees` field in their template YAML frontmatter
- Repos where maintainers assign work (check recent closed issues for the pattern)

If the repo uses **assignment-based workflow**:

```bash
gh issue comment {number} -R {owner}/{repo} --body "I'd like to be assigned to this issue."
```

If the repo allows **self-claiming**:

```bash
gh issue comment {number} -R {owner}/{repo} --body "I'd like to work on this issue. I'll submit a PR within [user-specified timeframe]."
```

**Re-run step 5 first.** Minutes pass between picking an issue and claiming it,
and that is long enough for somebody else to claim it. Check again immediately
before commenting, and once more before opening the PR. `oss-submit-pr` repeats
this for the same reason.

**Keep the claim comment short.** One sentence is enough. Don't write a paragraph about your background, your approach, or how excited you are. Maintainers see dozens of these. concise signals competence.

## Related Skills

- **Next step**: → `oss-prep-to-contribute`: prepare to actually contribute (set up dev env, understand codebase, knowledge check)
- **Preparation**: → `oss-explore-repo`: explore the codebase broadly before committing to a specific issue
- **Alternative**: → `oss-find-real-issues`: if no existing issues match, find real code problems to file as new issues

## Common Rationalizations

| Shortcut | Why It Fails |
|----------|-------------|
| "I'll just pick the first 'good first issue' I see" | Most "good first issue" labels are stale. The issue may be claimed, have a linked PR, or be filed by a random user whose request will never be reviewed. |
| "I don't need to check eligibility, it's open source" | Many repos don't merge external PRs, require CLAs, or only accept assigned work. Skipping this check wastes weeks on a PR that gets closed without review. |
| "I'll claim 3 issues so I have options" | Maintainers notice. Claiming multiple issues signals you won't finish any of them. Claim one, ship it, earn trust, then claim the next. |
| "Any issue will do, I just want a contribution" | Issues from random users often get closed. Maintainer-filed issues represent actual project priorities and get reviewed. The source matters. |
| "I'll figure out if I can do it after I claim it" | Abandoning claimed issues hurts your reputation and blocks others. Evaluate complexity and skill match BEFORE claiming. |

## Red Flags

- All recent "good first issue" labels are 6+ months old. repo may not be maintaining these labels
- Zero external PRs merged in recent history. repo may not actually accept outside contributions
- User can't explain why they picked a specific issue. they're optimizing for "easy" instead of "learning value"
- Issue has 5+ comments saying "I'll work on this" with no PRs. something about this issue makes people give up
- Two or more distinct claimants on several open beginner-labeled issues. the label is farmed, and picking from it wastes the user's time
- The timeline call fails or returns nothing on an issue that clearly has linked PRs. the facts are not established, so the issue is not clear

## Verification Checklist

- [ ] Repo accepts external contributions (verified by checking recent merged external PRs)
- [ ] Every check in step 5 cleared: no assignee, no reservation label, no claim
      in any comment, no open cross-referenced PR from another author
- [ ] Step 5 re-run immediately before the claim comment was posted
- [ ] Issue was created or updated within the last 6 months
- [ ] Issue was filed or endorsed by a maintainer/member/collaborator
- [ ] User explained why this issue matches their skills and what they'll learn
- [ ] Issue is clearly scoped (can describe the expected change in 2 sentences)
- [ ] Claim comment posted (following repo's assignment workflow)

## Anti-patterns

- **DO NOT** pick issues just because they look easy. pick ones that match skills AND teach something
- **DO NOT** skip the eligibility check. getting a PR rejected because the org doesn't accept outside contributions is demoralizing
- **DO NOT** claim multiple issues at once. claim one, ship it, then claim the next
- **DO NOT** take an issue somebody else claimed, whatever the state of their work. no competing PR, no fix held in reserve, no asking them to hand it over
- **DO NOT** read silence as abandonment. a claim stands until a maintainer reopens the issue to others
- **DO NOT** present issues from random users as equal to maintainer-filed issues. they're not
