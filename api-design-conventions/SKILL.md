---
name: api-design-conventions
description: Apply a consistent set of REST API design conventions — endpoint naming, HTTP status codes, error response shape, pagination, versioning, and rate-limit headers — whenever designing, building, or reviewing a backend API. Use this skill any time new endpoints are being designed or added, an existing API is being reviewed for consistency, or the user asks how an endpoint "should" be structured, even if they don't explicitly ask for "conventions" or "standards." This keeps every backend built across projects structurally consistent instead of re-deciding these choices each time.
---

# API Design Conventions

## Purpose

Small inconsistencies across projects — one API uses `/getArticles`, another uses
`/articles/list`, error shapes differ between endpoints — make every new project's API a fresh
set of decisions instead of a known pattern. This skill fixes those decisions once.

## When to use this

- Designing any new REST endpoint, in any project.
- Reviewing an existing API for internal consistency (pairs well with `code-review-playbook`
  for the endpoint's implementation, and this skill for its *shape*).
- The user asks something like "how should this endpoint be structured" or "what should this
  return on error."

## Conventions

### Endpoint naming
- Resource-based, plural nouns: `/articles`, not `/getArticles` or `/article`.
- Nested resources reflect real ownership: `/articles/{id}/comments`, not
  `/comments?articleId={id}` — unless the resource is genuinely independent and just
  filterable by that relationship.
- Actions that don't map cleanly to a resource (e.g. triggering a batch job) use a verb, but
  sparingly and clearly: `/ingest/run`, not shoehorned into a fake resource.
- Internal/automation-only endpoints are named for clarity, not hidden — a name like
  `/internal/ingest` documents intent, but is never treated as a substitute for actual access
  control (see the `deployment-readiness-checklist` skill for that).

### HTTP methods & status codes
- `GET` for reads (never mutates state), `POST` for creation or actions, `PUT`/`PATCH` for
  updates, `DELETE` for removal.
- `200` success with body, `201` created, `204` success with no body, `400` bad request
  (client error, malformed input), `401` unauthenticated, `403` authenticated but not
  authorized, `404` not found, `429` rate limited, `500` unhandled server error.
- Never return `200` with an error message in the body — if it failed, the status code says so.

### Error response shape (consistent across every endpoint in a project)
```json
{
  "error": {
    "code": "ARTICLE_NOT_FOUND",
    "message": "No article found for the given id.",
    "details": {}
  }
}
```
- `code` is a stable, machine-readable string a client can branch on — not just the HTTP status
  repeated.
- `message` is human-readable and safe to show to a developer/log, not necessarily an end user.
- `details` is optional, used for things like validation field errors.

### Pagination (for any list endpoint expected to grow)
- Prefer cursor-based (`?cursor=...&limit=...`) over offset-based for anything backed by a
  frequently-changing dataset, to avoid skipped/duplicated items as data shifts.
- Always include a `hasMore` (or equivalent) flag in the response so the client doesn't have to
  guess from a short page.

### Versioning
- Only version explicitly (e.g. `/v1/articles`) once there's a real reason to — don't add
  `/v1/` prefixes to a solo/portfolio project speculatively; it's fine to add when a genuine
  breaking change is needed later. Note this decision explicitly in `PROJECT_BRIEF.md` either
  way, so it's not silently inconsistent later.

### Rate limiting
- Rate-limited responses return `429` with a `Retry-After` header where possible.
- Rate limits differ by endpoint cost, not a single blanket number — an endpoint that triggers
  an LLM call or another paid/metered dependency gets a stricter limit than a plain database
  read. Document the chosen numbers in the project's brief or README, not just in code.

## Rules

- **Consistency within a project matters more than matching this skill's defaults perfectly.**
  If a project already has an established (different) convention, follow the project's
  existing pattern rather than introducing a second inconsistent one — flag the mismatch to the
  user instead of silently picking one.
- **Never invent a new error shape per endpoint.** If a new case doesn't fit the existing error
  envelope, extend it consistently rather than creating a one-off structure.
- **Public vs. internal endpoints get flagged explicitly** — this skill decides *shape and
  naming*; whether an endpoint needs auth/secrets is a security decision, not a naming one
  (that's `deployment-readiness-checklist`'s job).
