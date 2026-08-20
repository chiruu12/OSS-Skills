# Platform Guide

Skills are plain markdown, so they work with any AI coding tool that reads
instructions from a file. Only the install location changes, and `./setup`
knows all of them.

```bash
git clone https://github.com/chiruu12/OSS-Skills.git
cd OSS-Skills
./setup --list
```

## Pick a target

| Tool | Command | Writes |
|------|---------|--------|
| Claude Code | `./setup` | `~/.claude/skills/` |
| Claude Code, one project | `./setup --local --dest /path/to/project` | `.claude/skills/` |
| Codex, Aider, Goose, Amp, Jules, Junie, Devin, RooCode, Warp, Factory | `./setup --target agents` | `AGENTS.md` + `.oss-skills/` |
| Cursor | `./setup --target cursor` | `.cursor/rules/` |
| Cline | `./setup --target cline` | `.clinerules/` |
| GitHub Copilot | `./setup --target copilot` | `.github/copilot-instructions.md` + `.oss-skills/` |
| Windsurf | `./setup --target windsurf` | `.windsurf/rules/` |
| Zed | `./setup --target zed` | `.rules` + `.oss-skills/` |
| Gemini CLI | `./setup --target gemini` | `GEMINI.md` + `.oss-skills/` |
| Kiro | `./setup --target kiro` | `.kiro/skills/` |

Every target except `claude` installs into the current directory. Use `--dest`
to point somewhere else.

## If your tool is not listed

Use `--target agents`. `AGENTS.md` is an open format stewarded by the Agentic AI
Foundation under the Linux Foundation, and roughly 25 tools read it, including
several with their own native format. It is the best default for anything not
named above.

```bash
cd /path/to/your-project
/path/to/OSS-Skills/setup --target agents
```

Failing that, paste any `skills/*/SKILL.md` into your tool's custom instructions
and tell it to follow the workflow step by step. Nothing in the skills depends on
a particular runtime. The only external requirement is the `gh` CLI.

## How the non-Claude targets are laid out

Concatenating 15 skills produces about 170KB of markdown, which is too much to
keep in context for a single question. So `setup` splits them:

- `.oss-skills/<skill>.md` holds each full workflow
- a small router file (`AGENTS.md`, `GEMINI.md`, `.rules`, and so on) lists what
  each skill is for and tells the agent to open the matching file

The router is about 4KB, and a single workflow is 9 to 17KB. Your agent loads
one workflow when it needs it instead of carrying all fifteen. Cursor, Cline,
Windsurf and Kiro use their own per-file rule directories, so those get one file
per skill with no router.

## What is portable and what is not

| | Every tool | Claude Code only |
|---|:---:|:---:|
| Thinking gates | yes | yes |
| Step-by-step workflows | yes | yes |
| Anti-pattern checks | yes | yes |
| `gh` CLI commands | yes | yes |
| Slash routing (`/oss-find-issue`) | | yes |
| Parallel codebase search via Explore | | yes |
| Load-on-demand skill discovery | | yes |

Some skills mention Claude Code's Explore agent for searching a codebase. On
other tools, use whatever search that tool provides. It changes how a step is
carried out, not whether the step happens.

The part that matters, structured workflows that stop and make you think, works
everywhere.

## Updating

```bash
cd OSS-Skills && git pull && ./setup --target <your-target>
```

Re-running updates the installed copies in place. If a target writes into a
shared file such as `AGENTS.md`, only the block between the OSS-SKILLS markers
is rewritten, so your own instructions in that file are kept. Targets with a
rules directory record what they wrote in `.oss-skills-manifest`, along with a
checksum of each file. Cleanup removes a file only when its name is one `setup`
installs and its contents are still exactly what `setup` put there, so a skill
removed upstream goes away, while anything you wrote or edited yourself stays.

`setup` will not write through a symlink it finds at a path it manages, such as
`AGENTS.md`, a rules directory, or `.oss-skills-manifest`. It stops and tells
you which path, having changed nothing. This matters when you run it inside a
repo you just cloned, where a symlink could otherwise point the install at
files elsewhere on your machine. The directory you pass to `--dest` is your
own choice and may be a symlink.
