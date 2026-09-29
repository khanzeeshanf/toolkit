---
name: deployment-readiness-checklist
description: Run a structured pre-deployment checklist covering secrets, security, observability, and documentation before anything is described as ready to ship or go live. Use this skill whenever the user says something is "ready to deploy," asks "is this ready to ship," is about to push to a hosting provider, or is wrapping up a project that's about to become publicly reachable — even if they don't ask for a checklist explicitly. This is a pre-flight check, not a deployment guide — it catches the "did I forget something" gaps rather than walking through how to deploy to a specific platform.
---

# Deployment Readiness Checklist

## Purpose

The gap between "the code works" and "this is safe to put on the public internet" is where
real incidents happen — a committed secret, an unprotected endpoint, no rate limit on something
that costs money per call. This skill is a final pass that catches those gaps before deployment,
not a how-to for any specific hosting provider.

## When to use this

- The user says a project (or a part of it) is "ready to deploy," "ready to ship," or similar.
- Right before connecting a repo to a hosting provider for the first time.
- When wrapping up work on a project that's about to become publicly reachable, even if the
  user hasn't explicitly asked for a review.
- Periodically for a project that deploys repeatedly (not just the first release) — new
  endpoints or new secrets added since the last check need the same pass.

## The checklist

Go through every section. For each, either confirm it's handled, flag it as a gap, or mark it
"not applicable to this project" with a one-line reason — don't skip a section silently.

### 1. Secrets & configuration
- [ ] No API keys, tokens, or passwords committed to the repository (check current files *and*
      git history if there's any doubt — a secret removed from the latest commit can still
      exist in an earlier one).
- [ ] `.gitignore` covers every local config/env file actually in use.
- [ ] A `.env.example` (or equivalent) exists, documenting required variable names without
      real values.
- [ ] Secrets used by CI/automation (e.g. a scheduled workflow) are stored in the platform's
      secret manager, not hardcoded in the workflow file.

### 2. Access control
- [ ] Every endpoint's intended audience is explicit: fully public, secret-gated, or
      authenticated — nothing is "public by accident" because a check was forgotten.
- [ ] Any secret-gated endpoint uses a long, random secret (not a guessable string), checked
      server-side.
- [ ] CORS is scoped to the actual frontend origin(s), not left wide open (`*`) unless the
      endpoint is genuinely meant to be called from anywhere.

### 3. Rate limiting & cost protection
- [ ] Any endpoint that costs money or quota per call (LLM calls, paid APIs, expensive
      queries) has a rate limit — public reachability without one means anyone can run up
      costs or exhaust a shared quota, not just legitimate users.
- [ ] Rate limits are tuned per endpoint cost, not a single blanket number for everything
      (see `api-design-conventions` for the shape of this).

### 4. Observability
- [ ] There's a `/health` (or equivalent) endpoint for uptime checks.
- [ ] Errors are logged somewhere actually reachable after deployment — not just visible in a
      local terminal during development.
- [ ] If the project has a scheduled/background job, there's some way to tell afterward
      whether the last run succeeded or failed, without needing to guess.

### 5. Data & privacy
- [ ] What personal data (if any) is collected and stored is deliberate, not incidental — and
      the project can explain *why* each piece is needed if asked.
- [ ] Anything stored client-side only (e.g. local storage) versus server-side is documented,
      so it's clear what does and doesn't persist across devices.

### 6. Documentation
- [ ] A README exists and actually reflects the current state of the project, not an earlier
      plan that's since changed (see the `documentation-generator` skill).
- [ ] Known limitations are stated explicitly, not left for a user/reviewer to discover.

## Output format

Report results grouped by the six sections above, each item marked one of: **Done**, **Gap
found** (with what's missing and a concrete fix), or **N/A** (with the one-line reason). Finish
with a single clear verdict: ready to deploy, or not yet — listing exactly which gaps block
that verdict versus which are lower-priority follow-ups.

## Rules

- **This is not a deployment how-to.** Don't walk through platform-specific deploy steps
  (Render, Vercel, etc.) as part of this skill — that's a separate task. This skill only checks
  whether it's *safe and complete* to do so.
- **Don't rubber-stamp.** If something can't actually be verified (e.g. no access to check git
  history), say so explicitly rather than assuming it's fine.
- **Severity matters.** A missing rate limit on a paid-API endpoint is not the same urgency as
  a missing README section — the final verdict should make that distinction clear, not treat
  every gap as equally blocking.
