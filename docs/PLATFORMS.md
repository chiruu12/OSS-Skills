# Platform Integration Guide

Install OSS-Skills on any AI coding tool that reads markdown instructions. Skills are plain markdown — the content is portable, only the installation location changes.

## Claude Code (Primary)

The native target. Full support for Agent tool, Explore agents, and slash command routing.

**Global install (all projects):**

```bash
git clone https://github.com/chiruu12/OSS-Skills.git ~/.claude/skills/oss-skills
cd ~/.claude/skills/oss-skills && ./setup
```

**Project-local install:**

```bash
git clone https://github.com/chiruu12/OSS-Skills.git .claude/skills/oss-skills
cd .claude/skills/oss-skills && ./setup --local
```

Skills auto-discovered via SKILL.md frontmatter. Invoke with `/oss-find-issue`, `/oss-contribute`, etc.

---

## Cursor

Copy skills into Cursor's rules directory. Each skill becomes a rule file.

**Install:**

```bash
mkdir -p .cursor/rules
for skill in skills/oss-*/SKILL.md; do
  name=$(basename $(dirname "$skill"))
  cp "$skill" ".cursor/rules/${name}.mdc"
done
```

**Or manually:** Copy the content of any `skills/*/SKILL.md` into `.cursor/rules/` as a `.mdc` file.

**Usage:** Skills load automatically based on context. Reference them in chat: "Follow the oss-contribute workflow" or "Use the oss-find-issue process to find me an issue."

**Limitations:** No slash command routing. Agent/Explore tool references won't work — Cursor uses its own tools. The workflows and thinking gates still apply.

---

## GitHub Copilot (VS Code / JetBrains)

Add skills as custom instructions that Copilot reads for context.

**Install:**

```bash
mkdir -p .github
# Combine all skills into one instructions file
echo "# OSS Contribution Skills" > .github/copilot-instructions.md
echo "" >> .github/copilot-instructions.md
for skill in skills/oss-*/SKILL.md; do
  name=$(basename $(dirname "$skill"))
  echo "---" >> .github/copilot-instructions.md
  echo "" >> .github/copilot-instructions.md
  cat "$skill" >> .github/copilot-instructions.md
  echo "" >> .github/copilot-instructions.md
done
```

**Usage:** Copilot reads `.github/copilot-instructions.md` as context. Ask: "Help me contribute to this repo following the OSS contribution workflow."

**Limitations:** No progressive disclosure — all skills loaded at once. For large context models this is fine; for smaller contexts, copy only the skills you need.

---

## Windsurf

Copy skills into Windsurf's rules file.

**Install:**

```bash
# Single skill
cp skills/oss-contribute/SKILL.md .windsurfrules

# All skills (concatenated)
cat skills/oss-*/SKILL.md > .windsurfrules
```

**Or create `.windsurf/rules/` directory:**

```bash
mkdir -p .windsurf/rules
for skill in skills/oss-*/SKILL.md; do
  name=$(basename $(dirname "$skill"))
  cp "$skill" ".windsurf/rules/${name}.md"
done
```

**Usage:** Rules load automatically. Reference workflows in chat.

---

## Gemini CLI

Gemini CLI reads `GEMINI.md` in the project root for instructions.

**Install:**

```bash
# Create GEMINI.md with all skill content
echo "# OSS Contribution Skills for Gemini" > GEMINI.md
echo "" >> GEMINI.md
echo "Follow these workflows when contributing to open source." >> GEMINI.md
echo "" >> GEMINI.md
for skill in skills/oss-*/SKILL.md; do
  echo "---" >> GEMINI.md
  cat "$skill" >> GEMINI.md
  echo "" >> GEMINI.md
done
```

**Usage:** Gemini reads GEMINI.md automatically. Ask: "Use the oss-find-issue workflow to find me an issue."

---

## OpenAI Codex CLI

Codex reads `AGENTS.md` in the repo root (already included in this repo).

**Install:** No additional setup needed. `AGENTS.md` is already in the repo root.

**Usage:** Codex discovers skills through AGENTS.md. Ask: "Follow the oss-contribute skill to help me work on this issue."

**Limitations:** No Agent tool or Explore capabilities. Thinking gates work as conversation checkpoints.

---

## Kiro IDE

Kiro uses spec files and hooks for workflow automation.

**Install:**

```bash
mkdir -p .kiro/skills
for skill in skills/oss-*/SKILL.md; do
  name=$(basename $(dirname "$skill"))
  cp "$skill" ".kiro/skills/${name}.md"
done
```

**Usage:** Reference skills in Kiro's workflow definitions or load as context in chat.

---

## Manual / Any AI Tool

For any AI coding tool that accepts markdown context:

1. Copy the content of the relevant `skills/*/SKILL.md` file
2. Paste into the tool's system prompt, custom instructions, or context file
3. Tell the AI: "Follow this workflow step by step"

The skills are self-contained markdown. No dependencies beyond `gh` CLI for GitHub operations.

---

## What works everywhere vs. Claude Code-specific

| Feature | All platforms | Claude Code only |
|---------|:---:|:---:|
| Thinking gates (stop and ask) | ✓ | ✓ |
| Step-by-step workflows | ✓ | ✓ |
| Anti-patterns enforcement | ✓ | ✓ |
| `gh` CLI commands | ✓ | ✓ |
| Slash command routing (`/oss-find-issue`) | | ✓ |
| Explore agents (parallel codebase search) | | ✓ |
| Skill auto-discovery via frontmatter | | ✓ |
| Progressive disclosure (load on demand) | | ✓ |

The core value — structured workflows with thinking gates — works on every platform. Claude Code adds convenience (auto-routing, parallel search) but the workflows are portable.
