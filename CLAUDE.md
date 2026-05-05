# CLAUDE.md — CertPath (Tech-Reformers fork)

This file is loaded automatically by Claude Code on every session. It orients you to this fork. The **active working plan** lives in `PLAN.md` (committed to the fork, not PR'd upstream) — read it for current priorities and PR status.

---

## What this is

CertPath is an open-source AWS certification exam-prep platform. This repo (`Tech-Reformers/certpath`) is a fork of `davidodediran/certpath`, intended for use by Tech Reformers students.

The original author is a LinkedIn contact of the owner, John Krull. Most fixes go back upstream as PRs, and the fork tracks upstream `main`.

---

## Working with upstream — how we behave

The upstream relationship is the most important thing in this repo. Trust with the author accrues over time through small, well-scoped, respectful contributions; it erodes fast through surprise refactors, scope creep, or aggressive PR tone. Default to "helpful guest" disposition, not co-owner.

**Norms:**

- **He decides.** We propose changes; he accepts, declines, or asks for revisions. If a PR is rejected, that's his call — don't argue, don't re-open. If we still want the change for our deployment, carry it on the fork.
- **Communicate intent before non-trivial PRs.** For anything beyond a small fix, open an issue or DM first to check direction. Don't surprise him with sweeping changes or cross-cutting refactors.
- **One concern per PR.** Small, focused diffs are easier to review and easier to accept. Don't bundle unrelated fixes hoping they ride together.
- **Match his style.** Use the existing patterns, naming, and idioms. Don't refactor for taste while fixing a bug — if a minimal fix fits his style, prefer that over imposing ours.
- **PR descriptions stay factual.** Describe the change, the reason, and how to verify. Don't lecture, don't moralize, don't reference what "should have been" done.
- **Security: private first, always.** Use the process in `SECURITY.md`. DM the author before any public artifact (PR, issue, commit message) references a live vulnerability. Give him time to merge a fix before discussion goes public.
- **Credit and attribution stay intact.** Don't strip his copyright, badges, or authorship. If/when this fork is deployed, the README still acknowledges his work.
- **Be transparent about divergence.** If we ever decide to take the fork in a meaningfully different direction — rebrand, rewrite, or scope expansion — tell him before, not after.
- **Patience.** Independent maintainers have day jobs. A slow review is normal; don't escalate or pressure.

---

## Repo topology

```
upstream  davidodediran/certpath          ← original author; PR target for shared fixes
origin    Tech-Reformers/certpath          ← this fork; deploys land here
```

To sync: `git fetch upstream && git checkout main && git pull upstream main && git push origin main`

---

## Current architecture (baseline — do not migrate without explicit direction)

| Layer | Technology |
|---|---|
| Frontend | React 18 + Vite + Tailwind |
| Backend | Node.js + Express |
| Database | PostgreSQL (Supabase in production, local Postgres in dev) |
| Auth | JWT + bcrypt + TOTP MFA (otplib) |
| Deployment | Docker on AWS EC2 (single-image, EC2 user-data bootstrap) |

The owner's general stack preference (AWS-native serverless, Python backend, Amplify) is **not** the current architecture. Whether to migrate is a Phase 2 decision pending validation with students. Until that decision is explicitly made, respect the existing stack — propose Node/Express solutions, do not suggest Python rewrites unsolicited.

---

## Working agreements

### Branch / PR workflow
- One branch per logical change. Don't bundle unrelated fixes.
- Branches that benefit upstream → PR against `davidodediran/certpath:main`.
- Branches that are fork-specific (e.g. `GITHUB_REPO` value in deploy scripts, this `CLAUDE.md`, `PLAN.md`) → commit to fork's `main` directly, **do not PR upstream**.
- Naming convention: `fix/...` for bugs, `feat/...` for features, `chore/...` for maintenance.

### Confirmation before destructive actions
The owner is new to the fork/PR workflow. Walk through git operations with brief explanations rather than bare commands. Confirm before any `push`, `git reset --hard`, force operations, or anything that crosses the local→remote boundary.

---

## Authoritative documents

| Document | Location | Contains |
|---|---|---|
| **Working plan** | `PLAN.md` (committed to fork only — never PR'd upstream) | Active findings, PR sequence, status tracker |
| Project README | `README.md` | Quick start, deployment, user roles (still references upstream URLs in places — see PLAN.md) |
| Security policy | `SECURITY.md` | Disclosure process for upstream |
| Contributing | `CONTRIBUTING.md` | Upstream's contribution guide |
| Changelog | `CHANGELOG.md` | Release history |

When this `CLAUDE.md` disagrees with `PLAN.md` on current priorities, `PLAN.md` wins (it's the moving target).

---

## Things to surface proactively

- Conflicts between fork and upstream when syncing
- Findings during fixes that should change the PR sequence in `PLAN.md`
- Questions about whether a change should go upstream or stay fork-only
- Any push toward stack migration before Phase 1 (deploy + student validation) is complete
- Cost concerns for the EC2 deployment as student usage grows
- Security issues that warrant private disclosure rather than a public PR
