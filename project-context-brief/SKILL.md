---
name: project-context-brief
description: Maintain and load a compact PROJECT_BRIEF.md at the root of any project so Claude never has to re-derive stack, architecture, conventions, or key decisions from scratch in a new session. Use this skill at the START of any non-trivial task inside a project folder — before writing code, reviewing code, designing an API, writing docs, or discussing architecture — to check for and load an existing brief. Also use it whenever a project's stack, architecture, scope, or key decisions are being discussed for the first time, to offer creating one. Trigger this even if the user doesn't explicitly mention "context," "brief," or this skill by name — any request that would otherwise require Claude to ask "what's your stack/architecture/conventions" is a signal to check for this file first.
---

# Project Context Brief

## Purpose

Re-explaining a project's stack, architecture, and conventions in every new chat wastes tokens
and produces inconsistent answers across sessions. This skill defines one compact file,
`PROJECT_BRIEF.md`, that lives at the root of a project's working directory and acts as the
single source of truth Claude loads before doing anything else in that project.

The brief is deliberately dense (tables and bullets, not prose) because it gets loaded in full,
every session — token efficiency is the entire point of this skill.

## When to use this

- **Starting any task in a project folder**: before writing code, reviewing code, designing an
  endpoint, writing documentation, or discussing architecture, check whether
  `PROJECT_BRIEF.md` exists at the project root. If it does, read it in full before proceeding.
- **No brief exists yet, and the conversation reveals real project context** (stack, scope, key
  decisions): offer to create one from what's already been discussed, rather than asking the
  user to repeat themselves in a structured interview.
- **The user asks "what's the context on this project"**: read and summarize the brief rather
  than searching the codebase or asking the user again.
- **A decision changes** (e.g. a new tech stack choice, a scope change, a constraint discovered
  mid-build): update the brief immediately rather than letting the file go stale. A stale brief
  is worse than no brief, because it actively misleads future sessions.

## How to load it

1. Look for `PROJECT_BRIEF.md` in the current working directory root (check parent directories
   too if working inside a subfolder, up to a reasonable depth).
2. If found, read it fully before starting the task — treat its contents as ground truth over
   any general assumption Claude would otherwise make about the stack or conventions.
3. If the task is unrelated to any of the brief's sections (e.g. a one-off math question), it's
   fine to skip loading it — this skill is for project-scoped work, not everything in the chat.

## How to create one

If no brief exists and the project has real shape (more than a one-off script), offer to create
`PROJECT_BRIEF.md` using this template. Fill in only what's actually known — leave a section as
`TBD` rather than inventing plausible-sounding detail.

```markdown
# <Project Name>

**One-line motto:** <what this project actually does, in one sentence>

## Stack
| Layer | Choice |
|---|---|
| Backend | ... |
| Frontend | ... |
| Database | ... |
| Hosting (backend) | ... |
| Hosting (frontend) | ... |

## Key architectural decisions
- <decision>: <one-line reason it was made this way, not the alternatives considered>
- <decision>: <reason>

## Conventions
- Naming: <e.g. camelCase for JS, PascalCase for C# classes>
- Error handling: <pattern used>
- Folder structure: <brief layout, not a full file tree>

## Constraints / non-negotiables
- <e.g. "must stay on free-tier infrastructure">
- <e.g. "no user accounts/auth in v1">

## Known gaps / deferred to later
- <anything explicitly out of scope for now, so it isn't accidentally "fixed" later>

## Links
- Repo: ...
- Live deployment: ...
- Related docs: ...
```

## Rules for keeping it useful

- **Bullets and tables only in the "facts" sections** — no narrative paragraphs. This file is
  read by Claude every session; verbosity here is pure token waste.
- **Keep it under ~150 lines.** If a project genuinely needs more detail than that, split
  deep-dive material into a `/docs` folder and link to it from the brief, rather than bloating
  the brief itself.
- **Update it when a real decision changes**, not on every trivial edit. This file tracks
  decisions and shape, not a changelog.
- **Never let the brief silently drift from reality.** If Claude notices the codebase now
  contradicts the brief (e.g. brief says "no auth" but auth code now exists), flag this to the
  user rather than quietly working around the mismatch.
