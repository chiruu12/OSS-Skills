# OSS-Skills

This repo contains 15 skills for open source contribution workflows. Each skill is a structured markdown prompt in `skills/*/SKILL.md`.

## Available skills

**Evaluation:**
- `skills/oss-evaluate-repo/SKILL.md` - Assess repo health, governance, and community before investing time

**Contribution pipeline:**
- `skills/oss-find-issue/SKILL.md` - Find unclaimed issues matching the user's skills
- `skills/oss-prep-to-contribute/SKILL.md` - Check contribution eligibility, assess knowledge gaps
- `skills/oss-setup-dev-env/SKILL.md` - Set up dev environment for unfamiliar repos (with IDE hints)
- `skills/oss-contribute/SKILL.md` - Research the issue deeply, user describes logic, assist with implementation
- `skills/oss-submit-pr/SKILL.md` - Pre-flight checks, review diff, user writes PR description
- `skills/oss-post-pr/SKILL.md` - Decode reviewer feedback, find codebase examples of requested patterns
- `skills/oss-find-real-issues/SKILL.md` - Find code problems not in the issue tracker

**Contribution types:**
- `skills/oss-write-tests/SKILL.md` - Write tests for untested code as a standalone contribution
- `skills/oss-write-docs/SKILL.md` - Contribute documentation improvements verified against source code

**CI and review:**
- `skills/oss-debug-ci/SKILL.md` - Debug CI failures in unfamiliar repo pipelines
- `skills/oss-review-prs/SKILL.md` - Review other contributors' PRs to learn and build trust

**Growth:**
- `skills/oss-second-contribution/SKILL.md` - Plan the transition from one-off to regular contributor

**Research and learning:**
- `skills/oss-explore-repo/SKILL.md` - Guided codebase exploration (architecture, patterns, domain language)
- `skills/oss-learn-stack/SKILL.md` - Learn unfamiliar technologies from the repo's own code

## Usage

Load the relevant skill file as context when the user wants to contribute to an open source project.

**Evaluation**: evaluate-repo -> explore-repo -> find-issue
**Contribution track**: find-issue -> prep -> setup-dev-env -> contribute -> submit-pr -> post-pr
**Contribution types**: write-tests or write-docs (alternatives to contribute)
**After merge**: post-pr -> second-contribution -> review-prs
**Learning track**: explore-repo -> learn-stack -> find-issue (or find-real-issues)

## Core principle

The user must describe the logic before any code is written. Thinking gates enforce understanding at every step. Never write code without the user specifying what it should do.
