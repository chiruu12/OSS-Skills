# OSS-Skills Development

## Project Structure

```
OSS-Skills/
├── skills/                        # All 15 skills
│   ├── oss-evaluate-repo/         # Assess repo health before investing time
│   ├── oss-find-issue/            # Find unclaimed issues matching user skills
│   ├── oss-prep-to-contribute/    # Check eligibility, knowledge gaps, educate
│   ├── oss-setup-dev-env/         # Set up dev environment for unfamiliar repos
│   ├── oss-contribute/            # Deep research, user describes logic, skill helps implement
│   ├── oss-write-tests/           # Write tests as standalone contribution
│   ├── oss-write-docs/            # Contribute documentation improvements
│   ├── oss-submit-pr/             # Pre-flight checks, submit PR
│   ├── oss-post-pr/               # Handle PR review feedback
│   ├── oss-debug-ci/              # Debug CI failures in unfamiliar pipelines
│   ├── oss-second-contribution/   # Growth path to regular contributor
│   ├── oss-review-prs/            # Review PRs to learn and build trust
│   ├── oss-find-real-issues/      # Find code issues not in the tracker
│   ├── oss-explore-repo/          # Guided codebase exploration (architecture, patterns)
│   └── oss-learn-stack/           # Learn unfamiliar tech from the repo's own code
├── docs/                          # PLATFORMS.md, PHILOSOPHY.md
├── .github/workflows/setup.yml    # Installs every target on Ubuntu and macOS
├── setup                          # Install script (bash)
├── AGENTS.md                      # Hand written, for agents reading this repo itself
├── CLAUDE.md                      # This file
├── CONTRIBUTING.md                # How to contribute
├── LICENSE                        # MIT
└── README.md                      # Project overview + install + usage
```

## Skill format

Every skill is a directory containing `SKILL.md` with YAML frontmatter:

```yaml
---
name: oss-{skill-name}
description: |
  What it does. When to use it. Trigger phrases.
---
```

Sections within each SKILL.md, in order. All fifteen skills carry all eight:

1. **Purpose** - why it exists, what problem it solves
2. **Prerequisites** - what the user needs before invoking
3. **Process** - numbered steps with clear phases
4. **Related Skills** - how this connects to the other skills
5. **Common Rationalizations** - a table of shortcuts and why each one fails
6. **Red Flags** - signs the user is going wrong, for the LLM to watch for
7. **Verification Checklist** - what must be true before the skill is done
8. **Anti-patterns** - what this skill explicitly does NOT do

A title and one-line summary sit above Purpose. A skill may add sections beyond
the eight: seven add **When to Use** after Purpose, and some carry output
templates inside Process. Extra sections are fine; the eight above must all be
present and in that order.

Thinking gates are usually not a section of their own. They are numbered steps
inside Process, headed `### N. Thinking gate - <what the user must articulate>`,
and there is at least one in every skill. `oss-explore-repo` also collects its
gates into a `## Thinking Gates` section, which is allowed.

## Core principle

**The user drives, the LLM researches and assists.** The user must describe the logic before the skill helps implement it. Every skill has thinking gates where the user must explain their understanding. The skill acts as a pair programmer, not a ghostwriter.

## Skill interconnection

Skills reference each other at handoff points:

**Evaluation:**
- `oss-evaluate-repo` → `oss-explore-repo` → `oss-find-issue`

**Contribution track:**
- `oss-find-issue` → `oss-prep-to-contribute` → `oss-setup-dev-env` → `oss-contribute` → `oss-submit-pr` → `oss-post-pr`
- `oss-post-pr` → `oss-contribute` (rework) or `oss-submit-pr` (minor fixes)
- `oss-post-pr` → `oss-debug-ci` (when CI fails)
- `oss-post-pr` → `oss-second-contribution` (after merge)
- `oss-find-real-issues` → `oss-find-issue` or `oss-contribute`

**Contribution types (alternatives to oss-contribute):**
- `oss-write-tests` - test contributions as standalone entry point
- `oss-write-docs` - documentation contributions

**Growth track:**
- `oss-second-contribution` → `oss-review-prs` → `oss-find-issue` (next contribution)

**Learning track:**
- `oss-explore-repo` → `oss-find-issue` or `oss-find-real-issues`
- `oss-learn-stack` ← triggered from `oss-prep-to-contribute`, `oss-explore-repo`, or `oss-contribute`

When editing a skill, check that cross-references remain valid.

## IDE compatibility

Skills are written against Claude Code (Agent tool, Skill tool, Explore agents), but they are plain markdown, so `setup` installs them anywhere:

```
./setup --target claude|agents|cursor|cline|copilot|windsurf|zed|gemini|kiro
```

Targets that use one instruction file (`agents`, `gemini`, `copilot`, `zed`) write each workflow to `.oss-skills/<skill>.md` and generate a small router that indexes them, because 15 concatenated skills is about 170KB. Targets with their own rules directory (`cursor`, `cline`, `windsurf`, `kiro`) get one file per skill and no router.

`--target agents` writes `AGENTS.md`, the open format under the Agentic AI Foundation that roughly 25 tools read. Use it for anything without a dedicated target.

`setup` discovers skills from `skills/*/SKILL.md` and reads each summary from the frontmatter. Do not hardcode the skill list anywhere. Adding a directory with a valid `SKILL.md` is all that is needed, and `.github/workflows/setup.yml` fails if frontmatter is missing or a target installs the wrong count.

## Conventions

- Read existing skills before editing - match the structure and tone
- Every code example must use `{placeholder}` syntax for user-specific values
- Every `gh` command that acts on a repo must name it explicitly, with `-R`,
  `--repo`, or positionally. Never rely on `gh` inferring it from the checkout:
  in a fork clone it infers the fork, which is the wrong repo for issues and CI
- Thinking gates use blockquote format (`> "Question to ask the user"`)
- Anti-patterns section is mandatory - it prevents skills from drifting toward code generation
- Every description ends with a `Not for X. Use Y for that.` sentence, naming the
  skill it is most often confused with. It is what stops the wrong skill firing
  on tools that route by description, which is most of them
- That sentence goes last. `skill_notfor` carries everything from `Not for` to
  the end of the description into the router, so anything written after it lands
  in the router too
