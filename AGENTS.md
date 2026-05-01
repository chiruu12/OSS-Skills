# OSS-Skills

This repo contains 8 skills for open source contribution workflows. Each skill is a structured markdown prompt in `skills/*/SKILL.md`.

## Available skills

**Contribution pipeline:**
- `skills/oss-find-issue/SKILL.md` - Find unclaimed issues matching the user's skills
- `skills/oss-prep-to-contribute/SKILL.md` - Check contribution eligibility, assess knowledge gaps
- `skills/oss-contribute/SKILL.md` - Research the issue deeply, user describes logic, assist with implementation
- `skills/oss-submit-pr/SKILL.md` - Pre-flight checks, review diff, user writes PR description
- `skills/oss-post-pr/SKILL.md` - Decode reviewer feedback, find codebase examples of requested patterns
- `skills/oss-find-real-issues/SKILL.md` - Find code problems not in the issue tracker

**Research and learning:**
- `skills/oss-explore-repo/SKILL.md` - Guided codebase exploration (architecture, patterns, domain language)
- `skills/oss-learn-stack/SKILL.md` - Learn unfamiliar technologies from the repo's own code

## Usage

Load the relevant skill file as context when the user wants to contribute to an open source project.

**Contribution track**: find-issue -> prep -> contribute -> submit-pr -> post-pr
**Learning track**: explore-repo -> learn-stack -> find-issue (or find-real-issues)

## Core principle

The user must describe the logic before any code is written. Thinking gates enforce understanding at every step. Never write code without the user specifying what it should do.
