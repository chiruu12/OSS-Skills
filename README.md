# OSS-Skills

**15 skills that walk you through your first open source contribution, and your tenth.** Install in 30 seconds on Claude Code, Codex, Cursor, Cline, Copilot, Windsurf, Zed, Gemini CLI or Kiro. Start with `oss-find-issue`. The skill handles the research, you handle the thinking.

Built by a GSoC mentor who got tired of watching contributors make the same avoidable mistakes - picking issues nobody maintains, skipping CONTRIBUTING.md, submitting PRs that ignore the repo's conventions, and not being able to explain their own code during review.

These skills encode what I wish every contributor knew before their first submission.

> **This is NOT a curated list or a collection of resources.** These are executable skills you install and run. They search GitHub issues, read contribution docs, trace code paths, check if repos accept outside contributions, and ask you questions at every step. If you want a markdown list, this isn't it.

## See it work

An abridged session. The issue numbers are invented, everything else is what
the skills actually do.

```
You:     /oss-find-issue
Skill:   What languages do you know? Any specific repos, or want me to find one?
You:     I know Python. No specific repo - find me a good first issue anywhere.
Skill:   [searches repos, checks eligibility, filters by maintainer-filed issues]
         Found 3 unclaimed issues matching your skills...

         1. fastapi/fastapi #5621 - Fix type hint in dependency injection
         2. pallets/click #2734 - CLI argument parsing edge case
         3. encode/httpx #3102 - Connection timeout not respected

You:     That second one looks interesting - the CLI argument parsing bug.
Skill:   Why that one? What do you think the fix involves?
         (Hint: look at the scope above - what about it matches your skills?)
You:     I've worked with Click before. The description mentions argument groups
         which I dealt with in my last project...
Skill:   Good. Re-checking nobody took it while we talked... still clear.
         Some repos want a maintainer to assign instead. This one lets you
         self-claim, so post a one-liner and keep it short:
         "I'd like to work on this - looks like it's in the argument group
          handling. Happy to take it if nobody else has started."
You:     Posted.
Skill:   Run /oss-prep-to-contribute next.

You:     /oss-prep-to-contribute
Skill:   [reads CONTRIBUTING.md, CODE_OF_CONDUCT, maps architecture]
         This repo uses pytest, conventional commits, and requires DCO sign-off.
         Quick check: do you know what DCO sign-off means?
You:     Not really...
Skill:   It's a one-line attestation that you have the right to submit this code.
         Add it with: git commit -s. Here's the full guide: ...
         Now explain the repo's architecture in your own words.
```

## Install - 30 seconds

**Requirements:** [Git](https://git-scm.com/), [`gh` CLI](https://cli.github.com/) (authenticated), and an AI coding tool.

```bash
git clone https://github.com/chiruu12/OSS-Skills.git
cd OSS-Skills
./setup
```

That installs for Claude Code. For anything else, name it:

```bash
./setup --target cursor      # or cline, copilot, windsurf, zed, gemini, kiro
./setup --target agents      # AGENTS.md, read by Codex, Aider, Goose, Amp and ~25 more
./setup --list               # every target
```

Non-Claude targets install into the current directory, so run them from your
project, or pass `--dest /path/to/project`. For a project-local Claude install,
use `./setup --local`.

Or paste this into Claude Code and it handles everything:

> Install OSS-Skills: run `git clone https://github.com/chiruu12/OSS-Skills.git && cd OSS-Skills && ./setup`

## The skills

Slash syntax is Claude Code. On every other tool there is no slash
command: ask your agent for the workflow by name, such as "follow the
oss-find-issue workflow".

### Evaluation

| Skill | Role | What it does |
|-------|------|-------------|
| `/oss-evaluate-repo` | Analyst | Assesses project health, governance, bus factor, community before you invest time. Prevents wasting weeks on abandoned repos |

### Contribution pipeline

| Skill | Role | What it does |
|-------|------|-------------|
| `/oss-find-issue` | Scout | Finds unclaimed issues from maintainers, checks if repo accepts outside contributions, matches to your skills |
| `/oss-prep-to-contribute` | Tutor | Reads all docs, maps the codebase, checks your knowledge gaps, won't let you code until you can explain the architecture |
| `/oss-setup-dev-env` | Mechanic | Gets the repo building, testing, and linting on your machine. Reads CI config as source of truth when README is stale. IDE hints for VS Code, JetBrains, Cursor, Windsurf, Neovim |
| `/oss-contribute` | Pair programmer | Traces code paths, finds patterns. You describe the logic, it helps you implement. Won't write code until you explain what it should do |
| `/oss-submit-pr` | Reviewer | Pre-flight checks (tests, lint, rebase). Reviews your diff. You write the PR description, it tells you what to cut. Finds and enforces PR templates |
| `/oss-post-pr` | Translator | Decodes terse reviewer comments. Finds codebase examples of what they want. You explain the feedback before fixing |
| `/oss-find-real-issues` | Auditor | Finds real code problems not in the tracker (missing tests, error handling gaps). Fetches issue templates before filing. You evaluate what's worth filing |

### Contribution types

| Skill | Role | What it does |
|-------|------|-------------|
| `/oss-write-tests` | Test author | Write tests for untested code as a standalone contribution. Highest-value, lowest-risk first contribution. Studies repo test conventions and makes you match them exactly |
| `/oss-write-docs` | Documentarian | Contribute documentation improvements. Verifies every claim against source code, tests every example, matches the repo's docs voice |

### CI and review

| Skill | Role | What it does |
|-------|------|-------------|
| `/oss-debug-ci` | Debugger | CI failed and the error makes no sense. Reads CI config, fetches logs, classifies failure type, determines if it's your fault or pre-existing |
| `/oss-review-prs` | Reviewer | Review other contributors' PRs to learn the codebase and build maintainer trust. Teaches structured diff reading and comment etiquette |

### Growth

| Skill | Role | What it does |
|-------|------|-------------|
| `/oss-second-contribution` | Coach | Your first PR merged. Now what? Plans the transition from one-off contributor to regular. Progressive complexity escalation, relationship building, community presence |

### Research and learning

| Skill | Role | What it does |
|-------|------|-------------|
| `/oss-explore-repo` | Guide | Walks you through the architecture, domain language, and patterns of a repo. You build a mental model before picking an issue |
| `/oss-learn-stack` | Teacher | When the repo uses tech you don't know, teaches it from the repo's own code. Every example is from the actual codebase, not generic docs |

```
  Evaluation:
  oss-evaluate-repo -> oss-explore-repo -> oss-find-issue

  Learning track:
  oss-explore-repo -> oss-learn-stack -> oss-find-issue (or oss-find-real-issues)

  Contribution track:
  oss-find-issue -> oss-prep-to-contribute -> oss-setup-dev-env -> oss-contribute -> oss-submit-pr -> oss-post-pr
                                                                        |                                  |
                                              (alternatives) oss-write-tests / oss-write-docs              |
                                                                                          (CI fails) oss-debug-ci
                                                                                                           |
                                                                                              oss-second-contribution
                                                                                                           |
                                                                                                   oss-review-prs

  oss-find-real-issues -> (file issue) -> oss-contribute -> ...
```

## From the mentor's side

Having reviewed contributions from both sides, here's what actually gets PRs rejected:

1. **Picking orphaned issues.** Issues filed by random users often get closed without review. Issues from maintainers get prioritized. The skills that pick or triage issues - `oss-find-issue`, `oss-find-real-issues`, `oss-evaluate-repo`, `oss-prep-to-contribute` - read `authorAssociation` so you can tell which is which.

2. **Skipping the docs.** CONTRIBUTING.md exists for a reason. Half of rejected PRs fail on process - wrong branch convention, missing sign-off, no linked issue. The prep skill reads every doc so you don't skip the one that matters.

3. **Not understanding the code.** "I used AI to fix it" is obvious in review. When a maintainer asks "why did you do it this way?" and you can't answer, the PR dies. Thinking gates force you to explain the root cause before you write a line of code.

4. **AI-generated PR descriptions.** Maintainers can tell. "This comprehensive PR robustly addresses..." gets closed. "Fixes null check in auth handler. Added test for empty input case." gets merged.

5. **Ignoring review feedback.** A reviewer says "use X pattern" and the contributor makes a surface change without understanding why. The post-pr skill researches what the reviewer actually wants and makes you explain it before you start fixing.

These aren't hypothetical. These are patterns I've seen repeatedly.

## Thinking gates

Every skill has **thinking gates** - checkpoints where you must explain your understanding before proceeding. Each gate hints at where to look, not just bare questions.

| Gate | When | What you must explain |
|------|------|----------------------|
| Issue selection | After seeing options | Why this issue? What about it matches your skills? |
| Architecture check | After codebase tour | How is the repo structured? Where does the issue live? |
| Root cause | After LLM research | What's actually wrong? Look at the code paths presented above |
| Approach | Before coding | What files change? Walk through the logic step by step |
| Post-implementation | After coding | What did you change and why? Any edge cases? |
| Review response | After seeing feedback | What does the reviewer want? Look at the examples found above |

The model: **you drive the logic, the skill fills in what you've specified.** Like pair programming with a senior dev who knows the codebase. You can't just say "fix it" - you have to describe what the code should do first.

## Writing rules

All skills that create PRs, issues, or comments enforce:

- **No AI jargon.** "Comprehensive", "robust", "leverages" - cut all of it
- **No filler.** "This PR addresses the issue where..." -> "Fixes null check in auth handler"
- **Short and direct.** One sentence per point. Bullets over paragraphs
- **Technical terms are fine. Buzzwords are not.**

## Platform support

| Platform | Install |
|----------|---------|
| **Claude Code** | `./setup` - slash commands, auto-discovery, Explore agents |
| **Cursor** | `./setup --target cursor` |
| **Cline** | `./setup --target cline` |
| **GitHub Copilot** (VS Code / JetBrains) | `./setup --target copilot` |
| **Windsurf** | `./setup --target windsurf` |
| **Zed** | `./setup --target zed` |
| **Gemini CLI** | `./setup --target gemini` |
| **Kiro** | `./setup --target kiro` |
| **Codex, Aider, Goose, Amp, Jules, Junie, Devin, RooCode, Warp, Factory** | `./setup --target agents` |
| **Anything else** | `./setup --target agents`, then paste if that fails |

`--target agents` writes `AGENTS.md`, the open format stewarded by the Agentic AI
Foundation under the Linux Foundation. Roughly 25 tools read it, so it is the
right default for anything without a row of its own.

All skills are plain markdown, so the thinking gates and workflows work everywhere. Claude Code adds convenience (auto-routing, parallel search) but the core process is portable.

**Full setup instructions for each platform:** [docs/PLATFORMS.md](docs/PLATFORMS.md)

## Philosophy

1. **You drive, it researches.** The LLM reads docs, traces code, finds patterns. You describe the logic, explain your reasoning, make decisions. It never writes code without you specifying what it should do first.

2. **Gate on understanding.** Before you code, explain the root cause. Before you submit, explain your approach. Before you address feedback, explain what the reviewer wants.

3. **Eligibility first.** Before you write anything, `oss-evaluate-repo` counts whether outsiders actually get merged, `oss-find-issue` checks the issue is unclaimed and maintainer-filed, and `oss-prep-to-contribute` finds the CLA and the process rules. No wasted effort on PRs that never get reviewed.

Read more: [docs/PHILOSOPHY.md](docs/PHILOSOPHY.md)

## Requirements

| Tool | Version | Why |
|------|---------|-----|
| An AI coding tool | - | Runs the skills. Claude Code, Cursor, Cline, Copilot, Windsurf, Zed, Gemini CLI, Kiro, or anything that reads `AGENTS.md`. See [Platform support](#platform-support) |
| [Git](https://git-scm.com/) | 2.x+ | Clone, branch, commit |
| [`gh` CLI](https://cli.github.com/) | 2.x+ | Issue search, PR creation, API calls |

`gh` must be authenticated (`gh auth login`). All skills depend on it.

## Troubleshooting

**"gh: command not found"** - Install the GitHub CLI: https://cli.github.com/

**"gh: not logged in"** - Run `gh auth login` and follow the prompts.

**Skills not showing up** - Claude Code: `ls ~/.claude/skills/oss-*`, each should
have a SKILL.md inside. Cursor, Cline, Windsurf, Kiro: look in the rules
directory inside your project (`.cursor/rules/`, `.clinerules/`, and so on).
Codex, Copilot, Zed, Gemini CLI: check that the router file (`AGENTS.md`,
`.rules`, `GEMINI.md`, `.github/copilot-instructions.md`) contains the
OSS-SKILLS block, and that `.oss-skills/` sits beside it.

**Installed, but the agent never uses them** - Outside Claude Code nothing
auto-routes. Name the workflow you want: "follow the oss-find-issue workflow".
The router file tells your agent which file to open.

**Token usage** - oss-explore-repo, oss-prep-to-contribute and oss-find-real-issues
use Explore agents, which consume more tokens on large codebases.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## Acknowledgments

Inspired by [gstack](https://github.com/garrytan/gstack) by Garry Tan and [mattpocock/skills](https://github.com/mattpocock/skills). OSS-Skills borrows the idea of structured, role-based skills and applies it to the open source contribution workflow.

## License

MIT. See [LICENSE](LICENSE).
