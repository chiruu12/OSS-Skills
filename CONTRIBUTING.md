# Contributing to OSS-Skills

Thanks for wanting to contribute. This project is about helping people become better open source contributors - and we practice what we preach.

## Before you start

1. Read the [README](README.md) - understand the philosophy (LLM researches, user thinks and codes)
2. Read existing skills in `skills/` - match the structure and tone
3. If your change is non-trivial, open an issue first to discuss the approach

## Setting up

```bash
git clone https://github.com/chiruu12/OSS-Skills.git
cd OSS-Skills
```

No build step. Skills are plain Markdown files with YAML frontmatter.

## Making changes

### Editing a skill

1. Read the full skill before editing
2. Keep the eight sections every skill has, in this order: Purpose, Prerequisites,
   Process, Related Skills, Common Rationalizations, Red Flags, Verification
   Checklist, Anti-patterns. Some skills add a `When to Use` section after Purpose.
3. Check that cross-references to other skills remain valid
4. Verify thinking gates still exist - removing them violates the core principle.
   Gates are numbered steps inside Process, not a section of their own. They are
   written as a blockquote: `> "Question to ask the user"`

### Adding a new skill

1. Create `skills/oss-{name}/SKILL.md`
2. Follow the frontmatter format:
   ```yaml
   ---
   name: oss-{name}
   description: |
     What it does. When to use it.
   ---
   ```
3. Include all eight required sections (see CLAUDE.md for the list)
4. Add cross-references to/from related skills
5. Update the skill listing in README.md, CLAUDE.md and AGENTS.md. All three are
   hand maintained and all three will otherwise go stale

`setup` finds skills by globbing `skills/*/SKILL.md`, so there is no list to
update. A new directory with valid frontmatter installs on every target.

### What NOT to change

- **Don't make skills write code without the user describing the logic first.** The user drives, the LLM assists. If your change has the LLM generating code unprompted, it will be rejected.
- **Don't remove thinking gates.** They're the whole point.
- **Don't add skills that are generic Claude Code features.** These skills are specifically for open source contribution workflows.
- **Don't add AI jargon to any skill output.** No "comprehensive", "robust", "leverages", "utilizing". PR descriptions, issue text, and review responses must be concise and direct. This is enforced in every skill that produces external-facing text.

## Testing

CI runs `.github/workflows/setup.yml` on every PR, across Ubuntu and macOS. It
installs all nine targets, checks each one lands the right number of non-empty
skill files, re-runs each to prove the install is idempotent, and confirms that
an existing `AGENTS.md` survives. If you add a skill, that is what catches
missing frontmatter.

CI cannot tell you whether a skill is any good. For that:

1. Install locally: `./setup --local`
2. Try the full workflow on a real repo
3. Check that thinking gates actually block progress until the user demonstrates understanding
4. Verify `gh` commands work with the placeholder syntax

## Submitting a PR

1. One logical change per PR
2. Link the issue if one exists
3. Explain what changed and why in the PR description
4. Keep the diff focused - no drive-by cleanups

## Code of Conduct

Be respectful. Be constructive. Remember that this project exists to help new contributors - the tone of everything we write should reflect that.
