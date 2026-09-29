---
name: code-style-standards
description: Apply a consistent set of personal coding conventions (naming, error handling, comments, file organization) to any code Claude writes, instead of defaulting to generic or inconsistent style choices each time. Use this skill whenever writing, generating, or editing code in any project — before producing the first line of code in a session, check for a CODING_STANDARDS.md file in the project and follow it. If none exists, use the sensible defaults documented in this skill and offer to save them as a starting CODING_STANDARDS.md so the same choices persist across sessions and projects.
---

# Code Style Standards

## Purpose

Without a persisted standard, code style drifts between sessions and projects — different
naming conventions, inconsistent error handling, uneven comment density. This skill defines
both a default style (usable immediately, in any language) and the mechanism for persisting a
project-specific version of it in `CODING_STANDARDS.md`.

## When to use this

- Before writing new code of any kind (a function, a file, a full feature) in any project.
- When editing existing code, to keep new additions consistent with the surrounding style.
- When a project already has a `CODING_STANDARDS.md` — load and follow it exactly, even where
  it conflicts with this skill's defaults. The project file always wins.
- When no `CODING_STANDARDS.md` exists yet — apply this skill's defaults, and near the start of
  substantial work on a new project, offer once to save those defaults into the project (see
  "Persisting standards" below) so later sessions don't have to re-derive them.

## Default standards (used when no project file overrides them)

### Naming
- Variables and functions: `camelCase` (JS/TS), `snake_case` (Python), per-language convention
  otherwise (`PascalCase` for C# methods/classes, `camelCase` for C# locals).
- Booleans read as a question: `isReady`, `hasError`, `canRetry` — not `ready`, `error_flag`.
- No abbreviations that aren't immediately obvious (`config` is fine, `cfg` is not, unless the
  surrounding codebase already uses it consistently).

### Error handling
- Fail loudly in development, gracefully in user-facing paths — never swallow an exception
  silently with an empty catch block.
- Error messages should say what failed and, where possible, what to do about it — not just
  "an error occurred."
- Prefer typed/specific exceptions over generic ones where the language supports it.

### Comments
- Comment *why*, not *what* — the code already shows what it does; a comment earns its place by
  explaining a non-obvious reason, trade-off, or constraint.
- No commented-out code left in place "just in case" — delete it, version control remembers it.
- Public functions/methods get a short doc comment describing purpose, parameters, and return
  value; private/internal helpers usually don't need one unless the logic is genuinely subtle.

### File & folder organization
- One primary responsibility per file — a file that does three unrelated things should usually
  be three files.
- Group by feature/domain over group by type, once a project grows past a handful of files
  (e.g. `features/articles/` containing its own controller, service, and model, rather than
  scattering those across top-level `controllers/`, `services/`, `models/` folders).
- Configuration and secrets never live in source files — environment variables or a gitignored
  config file, always with a committed `.example` version documenting the expected keys.

### Formatting
- Follow the language/ecosystem's dominant formatter defaults (Prettier for JS/TS, `gofmt` for
  Go, `black` for Python, etc.) rather than inventing custom formatting rules — consistency
  with tooling beats a personal preference here.

## Persisting standards

When it's worth saving these into a project (first substantial piece of code written in a new
project, or the user asks to customize the defaults), create `CODING_STANDARDS.md`:

```markdown
# Coding Standards — <Project Name>

## Naming
- ...

## Error handling
- ...

## Comments
- ...

## File organization
- ...

## Project-specific exceptions to the defaults
- <anything this project deliberately does differently, and why>
```

Keep it short — this is a reference file, not a style guide essay. If the project also has a
`PROJECT_BRIEF.md` (see the `project-context-brief` skill), link to it rather than repeating
stack information here; this file is about *how* code is written, not *what* the stack is.
