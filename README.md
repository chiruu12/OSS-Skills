# OSS-Skills

**Six Claude Code skills that make you a better open source contributor. Not by writing code for you — by doing the research so you can think clearly.**

Most AI coding tools want to write your code. That's backwards for open source. If an LLM writes your PR, you didn't learn anything. The maintainer reviews AI slop. And you're no better a developer than when you started.

OSS-Skills flips the model: **the LLM researches, you think and code.**

It reads the contribution docs. It traces the code paths. It finds the right issues. It checks if the repo even accepts outside contributions. Then it hands you everything and asks: *"What's the root cause? What's your plan? Why this approach?"*

If you can't answer, you're not ready. And that's the point — the skill tells you where the gaps are so you can fill them.

**Who this is for:**
- **First-time contributors** — who want to make their first PR without getting rejected for process mistakes
- **GSoC candidates** — who need to demonstrate understanding, not just output
- **Anyone who treats open source as learning** — not a checkbox on a resume

## The workflow

```
oss-find-issue → oss-prep-to-contribute → oss-contribute → oss-submit-pr → oss-post-pr
                                                                ↑                |
                                                                |                |
                                                                +-- (rework) ----+

oss-find-real-issues → (file issue) → oss-contribute → ...
```

| Skill | What the LLM does | What YOU do |
|-------|-------------------|-------------|
| `/oss-find-issue` | Searches for unclaimed issues from maintainers, checks eligibility, ranks by skill match | Explain why you want to pick an issue |
| `/oss-prep-to-contribute` | Reads all docs, maps codebase, identifies your knowledge gaps | Answer knowledge checks, explain the architecture in your own words |
| `/oss-contribute` | Traces code paths, finds patterns, presents approach options with trade-offs | Explain the root cause, plan the approach, write all the code |
| `/oss-submit-pr` | Reviews your diff, flags issues, checks conventions | Write the PR description yourself, fix flagged issues |
| `/oss-post-pr` | Researches reviewer comments, finds codebase examples of what they want | Explain what the reviewer wants, write the fixes |
| `/oss-find-real-issues` | Analyzes code for real problems (missing tests, error handling gaps, etc.) | Evaluate each finding — is it a real problem? Would a PR be welcome? |

## Thinking gates

Every skill has **thinking gates** — points where you must articulate your understanding before proceeding. The LLM won't continue until you can explain:

- What the root cause is (in your own words)
- What your plan is (and why you chose it)
- What you changed (and why it works)

If you can't explain it, you don't understand it yet. The skill will point you to the gaps.

## Install — 30 seconds

**Requirements:** [Claude Code](https://docs.anthropic.com/en/docs/claude-code), [Git](https://git-scm.com/), [`gh` CLI](https://cli.github.com/) authenticated

### Option 1: One-line install

Open Claude Code and paste:

> Install OSS-Skills: run `git clone https://github.com/chiruu12/OSS-Skills.git ~/.claude/skills/oss-skills && cd ~/.claude/skills/oss-skills && ./setup`

### Option 2: Manual install

```bash
git clone https://github.com/chiruu12/OSS-Skills.git ~/.claude/skills/oss-skills
cd ~/.claude/skills/oss-skills
./setup
```

### Option 3: Project-local install

```bash
# From inside your project repo
git clone https://github.com/chiruu12/OSS-Skills.git .claude/skills/oss-skills
cd .claude/skills/oss-skills && ./setup --local
```

## Quick start

1. Install OSS-Skills (see above)
2. Open Claude Code in any directory
3. Run `/oss-find-issue` — tell it your skills and what repos interest you
4. Follow the workflow: prep → contribute → submit → post-PR
5. Ship your first contribution

## The six skills

### `/oss-find-issue` — Find the right issue

Not every issue is worth picking up. This skill finds unclaimed issues **created by maintainers** (not random users), checks if the repo accepts outside contributions, and matches issues to your actual skills. It asks you to explain WHY you want to pick an issue before claiming it.

### `/oss-prep-to-contribute` — Get ready before you code

Reads CONTRIBUTING.md, CODE_OF_CONDUCT, and every relevant doc. Checks CLA requirements and contribution eligibility. Then runs a **knowledge check**: asks you targeted questions about what you need to know for THIS contribution. If you don't know something, it explains concisely and points to where to learn more. You must explain the repo's architecture in your own words before proceeding.

### `/oss-contribute` — Research deep, code yourself

The LLM does the heavy research: traces code paths, reads git history, finds existing patterns, identifies constraints. Then presents everything in a structured format with file:line references. **It does not write your code.** You explain the root cause, describe your plan, and implement it. The LLM reviews your approach and points out things you might have missed.

### `/oss-submit-pr` — Submit it right the first time

Re-reads contribution docs for PR-specific rules. Runs pre-flight checks: tests, lint, rebase on latest upstream. Reviews your diff for common rejection reasons (scope creep, debug leftovers, missing tests). **You write the PR description** — the LLM reviews it and suggests improvements.

### `/oss-post-pr` — Handle review feedback

Fetches all review comments, categorizes them (blocking / suggestion / question / style), and researches what each reviewer actually wants. Finds codebase examples of the patterns they're asking for. **You explain what the reviewer wants before you start fixing.** Tracks which comments are addressed and which are pending.

### `/oss-find-real-issues` — Find problems nobody filed

Analyzes the codebase for real problems: missing error handling, untested code paths, inconsistent patterns, documentation gaps. Presents findings with severity and an assessment of whether a PR would be welcome. **You evaluate each finding** — the LLM doesn't file issues or write fixes without your judgment call.

## Philosophy

Three rules every skill follows:

1. **Research, don't write.** The LLM reads docs, traces code, finds patterns, and presents context. You decide what to do with it. It will never write your implementation or your PR description.

2. **Gate on understanding.** Before you write code, you must explain the root cause. Before you submit, you must explain your approach. Before you address review feedback, you must explain what the reviewer wants. If you can't articulate it, the skill points you to where to look.

3. **Eligibility first.** Every skill checks whether the repo accepts outside contributions, whether there's a CLA, and whether the issue is actually maintained. No wasted effort on PRs that will never be reviewed.

## Configuration

Create `~/.oss-skills/config.json` to customize behavior:

```json
{
  "languages": ["python", "typescript", "go"],
  "experience": "intermediate",
  "interests": ["web", "cli", "data"],
  "strict_gates": true
}
```

| Key | Values | Default | What it does |
|-----|--------|---------|-------------|
| `languages` | Array of languages | `[]` (asks each time) | Pre-fills your skill profile for issue matching |
| `experience` | `"beginner"` / `"intermediate"` / `"experienced"` | `"beginner"` | Adjusts complexity filtering and knowledge check depth |
| `interests` | Array of domains | `[]` (asks each time) | Filters issues by domain |
| `strict_gates` | `true` / `false` | `true` | When true, thinking gates are enforced. When false, they're advisory |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). This project dogfoods its own skills.

## License

MIT. See [LICENSE](LICENSE).
