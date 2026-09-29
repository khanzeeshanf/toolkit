---
name: documentation-generator
description: Generate or update a project's README and related documentation in a consistent, concise structure — leading with what the project actually does, not marketing language. Use this skill whenever asked to write, create, or update a README, or general project documentation, or when a project is nearing completion and needs to be documented for the first time. Pulls from PROJECT_BRIEF.md if one exists, rather than re-deriving stack and architecture details from scratch.
---

# Documentation Generator

## Purpose

READMEs written fresh each time tend to either balloon into marketing copy or skip the parts
that actually matter to someone picking up the project later (a recruiter, a collaborator,
future-you in six months). This skill fixes the structure and tone so documentation is
consistently useful, not just present.

## When to use this

- Explicit requests: "write a README," "document this project," "update the docs."
- A project is nearing completion (see `deployment-readiness-checklist`'s documentation
  section) and doesn't have a README yet, or has one that's gone stale relative to the code.
- The user asks for a "what I'd do next" or "known limitations" section specifically — these
  are part of the standard structure below, not a separate task.

## Before writing anything

Check for `PROJECT_BRIEF.md` (see the `project-context-brief` skill) — pull stack, architecture,
and key decisions from there instead of re-deriving them by reading through the codebase or
asking the user to re-explain. If no brief exists, this is also a good moment to offer creating
one, since the README and the brief overlap significantly in content.

## README structure

```markdown
# <Project Name>

<One-sentence motto — what this does, stated plainly, not "a revolutionary platform that...">

## What it does
<2-4 sentences. The actual functionality, described concretely with a real example if
possible, not abstract feature-speak.>

## Architecture
<A simple diagram (ASCII or a linked image) plus a short tech stack table. Pull directly from
PROJECT_BRIEF.md if available rather than re-describing from scratch.>

## Setup
<Concrete, copy-pasteable steps to run this locally. Assume the reader has never seen the
project before — no skipped steps because they seemed "obvious." Include required environment
variables by name (not real values) if any.>

## Live demo
<Link, if one exists. If not, say so rather than leaving the section oddly absent.>

## Known limitations
<Stated plainly and specifically — e.g. "saved items are stored in browser local storage only
and don't sync across devices" rather than a vague "some features are limited." This section
is not optional; it's what separates an honest project writeup from a sales pitch, and it's
often the part a technical reviewer reads most carefully.>

## What's next
<A short, real list of what v2 would add — pulls from the project's "future enhancements" or
"deferred" notes if a PROJECT_BRIEF.md or outline doc has them, rather than inventing new ones
on the spot.>
```

## Tone rules

- **State what the project does before why it's impressive.** Let the reader judge
  impressiveness; don't assert it.
- **No unearned superlatives** — "revolutionary," "seamless," "cutting-edge" — describe the
  actual mechanism instead ("retrieves the top-k chunks and generates a cited answer" beats
  "leverages advanced AI for intelligent insights").
- **Concrete examples beat abstract feature lists.** One real sample input/output is worth more
  than five bullet points describing capabilities in the abstract.
- **Known limitations are a feature of good documentation, not an admission of failure.**
  Always include them, specifically, even if the user doesn't ask.
- **Keep it scannable.** Headers, short paragraphs, tables over prose where the content is
  naturally tabular (stack choices, environment variables, endpoints).

## For non-README docs (API reference, architecture deep-dives)

Same tone rules apply. Prefer linking out from the README to a `/docs` folder for anything long
enough to need its own file (a full API reference, a detailed architecture rationale) rather
than growing the README itself past a few hundred lines — the README's job is to orient a new
reader quickly, not contain everything.
