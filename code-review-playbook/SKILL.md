---
name: code-review-playbook
description: Apply a consistent, structured review rubric to any code, diff, or pull request Claude is asked to review — covering correctness, security, readability, performance, error handling, and consistency with the project's own coding standards. Use this skill whenever asked to "review," "check," "look over," or give feedback on code, a diff, or a pull request, even if the user doesn't use the word "review" explicitly (e.g. "does this look right," "any issues with this," "is this ready to merge"). Always produce output sorted by severity, not a flat list of freeform comments.
---

# Code Review Playbook

## Purpose

Freeform code review feedback is inconsistent — sometimes thorough, sometimes shallow,
depending on what happens to catch attention first. This skill fixes the rubric and the output
shape so every review covers the same ground and is easy to act on.

## When to use this

- Any explicit review request: "review this," "check this PR," "look over this diff."
- Implicit review requests: "does this look right," "any issues here," "is this ready to
  merge/ship," "what do you think of this code."
- Before approving something described as ready for deployment — if a `deployment-readiness-
  checklist` skill review hasn't already covered the code itself, run this rubric first.

## Review rubric

Go through every category below for the code under review. Not every category will have
findings — that's fine, note "no issues found" rather than skipping the category silently, so
the user knows it was actually checked.

1. **Correctness** — does the code do what it's supposed to do? Check edge cases (empty input,
   null/undefined, boundary values, concurrent access if relevant), not just the happy path.
2. **Security** — hardcoded secrets, injection risks (SQL, command, prompt injection if this
   touches an LLM call), unvalidated input reaching a sensitive operation, missing
   authorization checks, overly permissive CORS.
3. **Error handling** — are failures handled explicitly? Are error messages useful? Any
   swallowed exceptions?
4. **Readability & maintainability** — naming clarity, function length/complexity, whether a
   future reader (including the author, in six months) could follow the logic without
   extensive comments.
5. **Performance** — obvious inefficiencies (N+1 queries, unnecessary loops over large data,
   blocking calls in a hot path) — not micro-optimization unless it's genuinely warranted.
6. **Consistency** — does this match the project's own `CODING_STANDARDS.md` (see the
   `code-style-standards` skill) if one exists? Flag deviations even if the code is otherwise
   fine, since inconsistency compounds over a codebase.

## Output format

Structure every review this way, not as a flat list:

```markdown
## Review: <file/feature/PR being reviewed>

### Blocking (must fix before merge)
- <issue>: <why it's blocking, and a concrete suggested fix>

### Should fix (not blocking, but should be addressed soon)
- <issue>: <reasoning and suggestion>

### Nitpicks (optional, stylistic)
- <issue>

### What's good
- <at least one genuine, specific positive — not filler praise. If nothing stands out, it's
  fine to skip this section, but don't pad it with generic compliments.>
```

## Rules

- **Never approve silently.** Every review ends with an explicit verdict: "blocking issues
  present — not ready" or "no blocking issues — ready to merge" (or "should-fix items present,
  your call on whether those block").
- **Be specific.** "This could be cleaner" is not useful feedback — name the exact line/function
  and what specifically should change.
- **Don't invent issues to fill categories.** "No issues found" is a valid, useful result for a
  category — padding with manufactured nitpicks erodes trust in the review.
- **Cross-reference project context.** If `PROJECT_BRIEF.md` or `CODING_STANDARDS.md` exist for
  this project, load them first (see the `project-context-brief` and `code-style-standards`
  skills) so the review judges the code against the project's actual stated conventions, not
  generic defaults.
