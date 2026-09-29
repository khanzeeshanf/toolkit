# claude-dev-skills

Six reusable Claude Skills that standardize how I work with Claude across every project — from
first context-setting through code review and pre-deployment checks. Drop any of these into a
new project's skills folder and the conventions carry over automatically, instead of
re-explaining stack, style, and review standards in every new chat.

## What's here

| Skill | Phase | What it does |
|---|---|---|
| [`project-context-brief`](./project-context-brief) | Setup / all phases | Maintains a compact `PROJECT_BRIEF.md` per project so Claude never has to re-derive stack, architecture, or key decisions from scratch each session. |
| [`code-style-standards`](./code-style-standards) | Development | Applies consistent naming, error-handling, and file-organization conventions to any code Claude writes. |
| [`code-review-playbook`](./code-review-playbook) | Code review | Structured review rubric (correctness, security, readability, performance) with output sorted by severity, not a flat comment list. |
| [`api-design-conventions`](./api-design-conventions) | Development (backend) | Consistent REST conventions — endpoint naming, status codes, error shape, pagination, rate limits — across every API I build. |
| [`deployment-readiness-checklist`](./deployment-readiness-checklist) | Deployment | Pre-ship checklist covering secrets, access control, rate limiting, and observability, before anything is called "ready." |
| [`documentation-generator`](./documentation-generator) | Deployment / wrap-up | Generates README/docs in a consistent structure — this very file was written using it. |

Testing is intentionally not part of this set — these six cover setup through deployment.

## Why this exists

Every new project meant re-explaining the same stack details, re-deciding the same API
conventions, and reviewing code against whatever rubric came to mind that day. These skills
turn those repeated decisions into reusable, versioned files instead of one-off context living
only inside old chat threads.

## How to use these in a new project

1. Copy the skill folder(s) you want into your project (or its skills directory).
2. Start with `project-context-brief` first — it'll help generate a `PROJECT_BRIEF.md` for the
   project, which the other five skills reference to stay consistent with each other.
3. From there, the skills trigger on their own based on what you're doing — writing code,
   asking for a review, designing an endpoint, or asking if something's ready to deploy.

## Known limitations

- These were drafted directly rather than run through a full trigger-accuracy eval loop —
  they're solid first versions based on real project work, not benchmark-tuned.
- `api-design-conventions` assumes a REST-style backend; it doesn't cover GraphQL or gRPC.
- Written for my own conventions — worth reading through and adjusting defaults (naming style,
  status code choices, etc.) before adopting as-is.

## What's next

- Run each skill through the trigger-accuracy eval loop individually as they get real use.
- Possibly split `api-design-conventions` by protocol if a non-REST project comes up.

---
Built and used across the [Locayaan](.) and Ayah-of-the-Day projects.
