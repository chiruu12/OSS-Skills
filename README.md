# OSS-Skills

**You want to contribute to open source but don't know where to start.** Which repo? Which issue? What if you pick the wrong one and waste a week on a PR that gets ignored?

OSS-Skills walks you through it. Six Claude Code skills that handle the research - reading contribution docs, tracing code paths, checking if the repo even accepts outside contributions - so you can focus on understanding the code and writing a PR that actually gets merged.

**The model: you think, the LLM researches.** You describe the logic. You explain the root cause. You write the PR description. The skill does the grunt work (searching issues, reading 500-line CONTRIBUTING.md files, finding code patterns) and asks you questions that force you to actually understand what you're doing.

**Who this is for:**
- **First-time contributors** - make your first PR without getting rejected for process mistakes
- **GSoC candidates** - demonstrate understanding, not just output
- **Anyone who treats open source as learning** - not a checkbox on a resume

## Install - 30 seconds

**Requirements:** [Claude Code](https://docs.anthropic.com/en/docs/claude-code), [Git](https://git-scm.com/), [`gh` CLI](https://cli.github.com/) (authenticated)

### One-line install

Open Claude Code and paste:

> Install OSS-Skills: run `git clone https://github.com/chiruu12/OSS-Skills.git ~/.claude/skills/oss-skills && cd ~/.claude/skills/oss-skills && ./setup`

### Manual install

```bash
git clone https://github.com/chiruu12/OSS-Skills.git ~/.claude/skills/oss-skills
cd ~/.claude/skills/oss-skills
./setup
```

### Project-local install

```bash
git clone https://github.com/chiruu12/OSS-Skills.git .claude/skills/oss-skills
cd .claude/skills/oss-skills && ./setup --local
```

## Quick start

```
You:     /oss-find-issue
Skill:   What languages do you know? Any specific repos, or want me to find one?
You:     I know Python. No specific repo - find me a good first issue anywhere.
Skill:   [searches repos, checks eligibility, filters by maintainer-filed issues]
         Found 3 unclaimed issues matching your skills...
You:     That second one looks interesting - the CLI argument parsing bug.
Skill:   Why that one? What do you think the fix involves?
You:     Because I've worked with argparse before, and from the description it sounds like...
Skill:   Good thinking. Claimed. Run /oss-prep-to-contribute next.
```

## The workflow

```
  oss-find-issue → oss-prep-to-contribute → oss-contribute → oss-submit-pr → oss-post-pr
                                                                  ↑                |
                                                                  +-- (rework) ----+

  oss-find-real-issues → (file issue) → oss-contribute → ...
```

## The six skills

| Skill | Your role | What it does for you |
|-------|-----------|---------------------|
| `/oss-find-issue` | The scout | Finds unclaimed issues from maintainers, checks repo eligibility, matches to your skills |
| `/oss-prep-to-contribute` | The tutor | Reads all docs, maps the codebase, runs a knowledge check on what you need to know |
| `/oss-contribute` | The researcher | Traces code paths, finds patterns, presents context. You describe the logic, it helps you implement |
| `/oss-submit-pr` | The reviewer | Reviews your diff, flags issues, checks conventions. You write the PR description |
| `/oss-post-pr` | The translator | Decodes reviewer feedback, finds codebase examples of what they want. You address it |
| `/oss-find-real-issues` | The auditor | Finds real code problems not in the issue tracker. You evaluate what's worth filing |

### `/oss-find-issue` - Find the right issue

Not every issue is worth picking up. Issues from random users get ignored. Issues from maintainers get reviewed and merged. This skill finds the ones that matter, filters for unclaimed work, and matches to your actual skills. It asks you to explain WHY you want the issue before claiming it.

### `/oss-prep-to-contribute` - Get ready before you code

Reads CONTRIBUTING.md, CODE_OF_CONDUCT, checks CLA requirements, maps the architecture. Then runs a knowledge check: "Do you know X? Are you familiar with Y?" If you don't know something, it explains concisely and points you to where to learn more. You must explain the repo's architecture in your own words before proceeding.

### `/oss-contribute` - Research deep, you drive the code

The core skill. The LLM traces code paths, reads git history, finds patterns, identifies constraints. Presents everything with `file:line` references. You explain the root cause, describe the logic flow you want to implement, and the skill helps you write it. Think of it as pair programming: you drive the logic, the skill fills in what you've specified.

### `/oss-submit-pr` - Submit it right the first time

Runs pre-flight checks (tests, lint, rebase). Reviews your diff for scope creep, debug leftovers, missing tests. You write the PR description - concise, no jargon, facts only. The LLM reviews it and tells you what to cut.

### `/oss-post-pr` - Handle review feedback

Your PR got reviewed. This skill decodes terse reviewer comments into actionable context. Fetches codebase examples of the patterns they're asking for. You explain what the reviewer wants before you start fixing - catches misunderstandings before they waste a review cycle.

### `/oss-find-real-issues` - Find problems nobody filed

Analyzes the codebase for missing error handling, untested code paths, inconsistent patterns, documentation gaps. Presents findings with severity. You evaluate whether each one is a real problem and whether a PR would be welcome.

## Thinking gates

Every skill has **thinking gates** - checkpoints where you must articulate your understanding before the skill continues. Each gate gives you a hint about where to look, not just a bare question.

| Gate | When | What you must explain |
|------|------|----------------------|
| Issue selection | After seeing options | Why this issue? What about it matches your skills? |
| Architecture check | After codebase tour | How is the repo structured? Where does the issue live? |
| Root cause | After LLM research | What's actually wrong? Look at the code paths presented above |
| Approach | Before coding | What files change? Walk through the logic step by step |
| Post-implementation | After coding | What did you change and why? Any edge cases? |
| Review response | After seeing feedback | What does the reviewer want? Look at the examples found above |

If you can't explain it, you don't understand it yet. The skill points you to the specific code, not just "try harder."

## Writing rules

All skills that create external-facing content (PRs, issues, comments, review responses) enforce:

- **No AI jargon.** "Comprehensive", "robust", "leverages", "utilizing" - cut all of it
- **No filler.** "This PR addresses the issue where..." -> "Fixes null check in auth handler"
- **No self-narration.** "I noticed that..." / "After investigating..." - state facts
- **Short and direct.** One sentence per point. No paragraphs where a bullet works
- **Technical terms are fine. Buzzwords are not.**

## Project structure

```
OSS-Skills/
├── skills/                        # All 6 skills
│   ├── oss-find-issue/            # Find unclaimed issues matching your skills
│   ├── oss-prep-to-contribute/    # Check eligibility, knowledge gaps, educate
│   ├── oss-contribute/            # Deep research - you describe logic, skill helps implement
│   ├── oss-submit-pr/             # Pre-flight checks, submit PR
│   ├── oss-post-pr/               # Handle PR review feedback
│   └── oss-find-real-issues/      # Find code issues not in the tracker
├── docs/
│   └── PHILOSOPHY.md              # Why "you think, LLM researches"
├── setup                          # Install script (global or --local)
├── CLAUDE.md                      # Development guide
├── CONTRIBUTING.md                # How to contribute
├── LICENSE                        # MIT
└── README.md                      # This file
```

## Philosophy

Three rules every skill follows:

1. **You drive, it researches.** The LLM reads docs, traces code, finds patterns. You describe the logic, explain your reasoning, and make decisions. It never writes code without you specifying what the code should do first.

2. **Gate on understanding.** Before you code, explain the root cause. Before you submit, explain your approach. Before you address feedback, explain what the reviewer wants. Can't articulate it? The skill shows you where to look.

3. **Eligibility first.** Every skill checks whether the repo accepts outside contributions, whether there's a CLA, and whether the issue is actually maintained. No wasted effort on PRs that will never be reviewed.

Read the full philosophy: [docs/PHILOSOPHY.md](docs/PHILOSOPHY.md)

## Requirements

| Tool | Version | Why |
|------|---------|-----|
| [Claude Code](https://docs.anthropic.com/en/docs/claude-code) | Latest | Runs the skills |
| [Git](https://git-scm.com/) | 2.x+ | Clone, branch, commit |
| [`gh` CLI](https://cli.github.com/) | 2.x+ | Issue search, PR creation, API calls |

`gh` must be authenticated (`gh auth login`). All skills depend on it.

## Troubleshooting

**"gh: command not found"** - Install the GitHub CLI: https://cli.github.com/

**"gh: not logged in"** - Run `gh auth login` and follow the prompts.

**Skills not showing up in Claude Code** - Check that SKILL.md files are in `~/.claude/skills/oss-*/SKILL.md`. Run `ls ~/.claude/skills/oss-*` to verify.

**Token usage** - Skills that use Explore agents (oss-contribute, oss-find-real-issues) consume more tokens because they dispatch sub-agents to trace code. Budget accordingly on large codebases.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). This project dogfoods its own skills.

## Acknowledgments

Inspired by [gstack](https://github.com/garrytan/gstack) by Garry Tan - a framework that turns Claude Code into a virtual engineering team. OSS-Skills borrows the idea of structured, role-based skills but applies it to the open source contribution workflow. Also draws from [mattpocock/skills](https://github.com/mattpocock/skills) for skill design patterns.

## License

MIT. See [LICENSE](LICENSE).
