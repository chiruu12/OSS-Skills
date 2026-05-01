# OSS-Skills Development

## Project Structure

```
OSS-Skills/
├── skills/                        # All 8 skills
│   ├── oss-find-issue/            # Find unclaimed issues matching user skills
│   ├── oss-prep-to-contribute/    # Check eligibility, knowledge gaps, educate
│   ├── oss-contribute/            # Deep research, user describes logic, skill helps implement
│   ├── oss-submit-pr/             # Pre-flight checks, submit PR
│   ├── oss-post-pr/               # Handle PR review feedback
│   ├── oss-find-real-issues/      # Find code issues not in the tracker
│   ├── oss-explore-repo/          # Guided codebase exploration (architecture, patterns)
│   └── oss-learn-stack/           # Learn unfamiliar tech from the repo's own code
├── docs/                          # Documentation
├── setup                          # Install script (bash)
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

Sections within each SKILL.md:
1. **Title + one-liner** - what this skill does
2. **Purpose** - why it exists, what problem it solves
3. **Prerequisites** - what the user needs before invoking
4. **Process** - numbered steps with clear phases
5. **Thinking Gates** - points where user must articulate understanding
6. **Related Skills** - how this connects to the other skills
7. **Anti-patterns** - what this skill explicitly does NOT do

## Core principle

**The user drives, the LLM researches and assists.** The user must describe the logic before the skill helps implement it. Every skill has thinking gates where the user must explain their understanding. The skill acts as a pair programmer, not a ghostwriter.

## Skill interconnection

Skills reference each other at handoff points:

**Contribution track:**
- `oss-find-issue` → `oss-prep-to-contribute` → `oss-contribute` → `oss-submit-pr` → `oss-post-pr`
- `oss-post-pr` → `oss-contribute` (rework) or `oss-submit-pr` (minor fixes)
- `oss-find-real-issues` → `oss-find-issue` or `oss-contribute`

**Learning track:**
- `oss-explore-repo` → `oss-find-issue` or `oss-find-real-issues`
- `oss-learn-stack` ← triggered from `oss-prep-to-contribute`, `oss-explore-repo`, or `oss-contribute`

When editing a skill, check that cross-references remain valid.

## IDE compatibility

Skills are Claude Code native (they use the Agent tool, Skill tool, and Explore agents). But since all skills are plain markdown, they're portable:

- **Cursor**: Copy SKILL.md content into `.cursor/rules/*.mdc`
- **VS Code Copilot**: Copy into `.github/copilot-instructions.md`
- **Windsurf**: Copy into `.windsurfrules`
- **Codex / Antigravity**: `AGENTS.md` in repo root provides basic discovery

v2 will add a setup flag (`./setup --cursor`, `./setup --copilot`, etc.) that installs in the right format automatically. For now, Claude Code is the primary target.

## Conventions

- Read existing skills before editing - match the structure and tone
- Every code example must use `{placeholder}` syntax for user-specific values
- Every `gh` command must include `-R {owner}/{repo}` for explicit repo targeting
- Thinking gates use blockquote format (`> "Question to ask the user"`)
- Anti-patterns section is mandatory - it prevents skills from drifting toward code generation
