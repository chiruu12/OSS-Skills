# Philosophy

## Why I built this

I've been on both sides of the PR review. As a contributor, I made every mistake in the book - picking issues nobody maintained, submitting PRs that ignored conventions, not being able to explain my own code. As a GSoC mentor, I watched other contributors make the same mistakes, over and over.

The patterns are predictable. Someone finds an issue, asks an AI to fix it, submits the PR, and can't answer a single question during review. Or they skip CONTRIBUTING.md and get rejected for process, not code quality. Or they pick an issue filed by a random user and it never gets reviewed.

These skills exist to break that cycle. Not by writing better code for you, but by making sure you understand what you're doing before you submit.

## The problem

Open source has a contribution problem. Not a shortage of contributors - a shortage of contributors who understand what they're contributing to.

AI coding tools made this worse. Now someone can fork a repo, ask an LLM to fix an issue, submit the PR, and never understand a single line of what was generated. The maintainer reviews AI-generated code from someone who can't answer questions about it. The contributor lists an "open source contribution" on their resume but learned nothing.

This is bad for everyone. Maintainers waste time on PRs from people who can't iterate on feedback. Contributors don't grow. The open source ecosystem gets noisier without getting stronger.

## The model: LLM researches, you think

These skills invert the typical AI coding workflow:

**Typical AI tool**: You describe the problem → AI writes the code → You submit it
**OSS-Skills**: You pick the problem → AI researches everything about it → You explain the root cause → You plan the approach → You describe the logic → AI helps you implement it → AI reviews your work

The LLM handles what's genuinely tedious and doesn't build skills: reading 500 lines of CONTRIBUTING.md, tracing code paths through unfamiliar modules, finding examples of patterns in a codebase you just cloned, checking if an issue is actually claimed.

You handle what builds real engineering judgment: diagnosing root causes, choosing between approaches with real trade-offs, describing the logic flow for your fix, and explaining your decisions to reviewers. The skill then helps you implement what you described - like pair programming with a senior dev who knows the codebase.

## Thinking gates

The skills enforce this model through **thinking gates** - mandatory checkpoints where you must articulate your understanding before proceeding.

A thinking gate looks like this:

> "Based on what I've found - can you explain in your own words:
> 1. What's the root cause of this issue?
> 2. Which approach do you want to take, and why?"

The LLM won't continue until you answer. If your answer has gaps, it points to the specific code that fills them - it doesn't give you the answer.

This is uncomfortable. That's the point. The discomfort is the learning.

## What makes a good contribution

These skills optimize for contributions that:

1. **Come from understanding.** The contributor can explain every line of their diff.
2. **Follow the rules.** The contributor read CONTRIBUTING.md, follows the commit convention, links the issue, includes tests.
3. **Solve real problems.** The issue was filed by a maintainer, affects real users, and is clearly scoped.
4. **Get merged.** A contribution that gets rejected taught something, but a contribution that gets merged taught something AND made the project better.

One well-crafted PR beats five sloppy ones. Quality signals competence. Competence earns trust. Trust opens doors - to more interesting issues, to mentorship, to being invited as a maintainer.

## For GSoC candidates specifically

GSoC mentors can tell the difference between a candidate who understands the codebase and one who pasted AI output. These skills exist to put you in the first category.

When you use these skills, you'll be able to:
- Explain the project's architecture in your own words
- Describe why your approach works and what alternatives you considered
- Answer reviewer questions without needing to "check and get back"
- Contribute iteratively based on feedback

That's what gets you selected. Not the number of PRs - the quality of understanding behind them.
