# OSS-Skills Development

## Project Structure

```
OSS-Skills/
├── skills/                        # All 8 skills
│   ├── oss-find-issue/            # Find unclaimed issues matching user skills
│   ├── oss-prep-to-contribute/    # Check eligibility, knowledge gaps, educate
│   ├── oss-contribute/            # Deep research, user thinks and codes
│   ├── oss-submit-pr/             # Pre-flight checks, submit PR
│   ├── oss-post-pr/               # Handle PR review feedback
│   ├── oss-find-real-issues/      # Find code issues not in the tracker
│   ├── oss-find-bounty/           # Find paid issues across platforms
│   └── oss-bounty-prep/           # Bounty terms, payout history, scope assessment
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
1. **Title + one-liner** — what this skill does
2. **Purpose** — why it exists, what problem it solves
3. **Prerequisites** — what the user needs before invoking
4. **Process** — numbered steps with clear phases
5. **Thinking Gates** — points where user must articulate understanding
6. **Related Skills** — how this connects to the other 7 skills
7. **Anti-patterns** — what this skill explicitly does NOT do

## Core principle

**The LLM researches, the user thinks and codes.** No skill writes code for the user. Every skill has thinking gates where the user must explain their understanding. This is non-negotiable — it's the entire value proposition.

## Skill interconnection

Skills reference each other at handoff points:
- `oss-find-issue` → `oss-prep-to-contribute`
- `oss-find-bounty` → `oss-bounty-prep` → `oss-prep-to-contribute`
- `oss-prep-to-contribute` → `oss-contribute`
- `oss-contribute` → `oss-submit-pr`
- `oss-submit-pr` → `oss-post-pr`
- `oss-post-pr` → `oss-contribute` (rework) or `oss-submit-pr` (minor fixes)
- `oss-find-real-issues` → `oss-find-issue` or `oss-contribute`

When editing a skill, check that cross-references remain valid.

## Conventions

- Read existing skills before editing — match the structure and tone
- Every code example must use `{placeholder}` syntax for user-specific values
- Every `gh` command must include `-R {owner}/{repo}` for explicit repo targeting
- Thinking gates use blockquote format (`> "Question to ask the user"`)
- Anti-patterns section is mandatory — it prevents skills from drifting toward code generation
