# CertPath Fork — Review & Contribution Plan

**Owner:** John Krull (jkrull@techreformers.com)
**Last updated:** 2026-05-05
**Status:** Review complete; PR sequence not yet started.

---

## Goals

1. Help the upstream author (LinkedIn contact) by contributing fixes via Pull Request — building a working business relationship.
2. End up with a deployable fork at `Tech-Reformers/certpath` that John can use for AWS cert-prep teaching.

---

## Repo topology

```
upstream  davidodediran/certpath          ← original author; PR target for shared fixes
origin    Tech-Reformers/certpath          ← John's fork; deploys land here
local     /Users/johnkrull/Projects/certpath
```

Both `main` branches are identical as of 2026-05-05 — clean starting point.
Upstream also has a stale `Dev` branch (capital D) that is *behind* `main`. Do not branch from it.

---

## Findings from review

### Blocking — must fix before deploy

| # | Issue | File / location | Notes |
|---|---|---|---|
| B1 | Bootstrap clones wrong repo + wrong branch | `scripts/ec2-setup.sh:51-52` | Hardcodes `davidodediran/certpath` and `dev` (lowercase). Branch on upstream is `Dev` (capital D). Script currently fails on Linux. |
| B2 | IDOR — student answers leak | `backend/src/routes/exams.js:559` | `GET /:id/review` checks JWT but not session ownership. Sibling route at `:521` does check — pattern was forgotten. |
| B3 | Permissive CORS | `backend/src/server.js:14-17` | `origin: true, credentials: true` reflects any origin. Combined with no CSRF, dangerous if cookies are added later. |
| B4 | Hardcoded weak default credentials | `backend/src/routes/auth.js:15, 27` | Falls back to `sesan174` / `super@rt2025` if env vars missing. Auto-seeds on first login attempt. `docker-compose.yml` ships `admin1234` / `super1234`. |
| B5 | JWT in localStorage | `frontend/src/lib/api.js:10` | Exposed to any XSS. Combined with no CSP, large blast radius. |

### High — fix before scaling beyond a small pilot

- **No rate limiting** on `/api/auth/login` or `/api/auth/mfa/verify`. No `express-rate-limit` in deps.
- **Multer file filter is extension-only** (`admin.js:36-41`). Teacher upload has no size limit.
- **Auto-registration on empty cohorts** (`auth.js:139-148`) — anyone with the cohort code can register any email.
- **DB SSL skipped** — `rejectUnauthorized: false` in `db/connection.js:19`.

### Medium / observations
- `teacher.js` is 1,508 lines — needs splitting eventually.
- GitHub Actions deploy uses long-lived AWS keys, not OIDC.
- Anti-cheat is client-side only — UX deterrent, not security.

---

## PR sequence

One branch per PR. Each PR is independent and can be reviewed/merged in any order.

| # | Branch | Goal | Target | Issue |
|---|---|---|---|---|
| 1 | `fix/ec2-setup-branch-casing` | Fix the bootstrap script's branch reference | upstream | B1 (partial) |
| 2 | `fix/exam-review-idor` | Add ownership check to `/api/exams/:id/review` | upstream | B2 |
| 3 | `fix/remove-default-passwords` | Strip hardcoded password fallbacks; fail closed | upstream | B4 |
| 4 | `fix/cors-allowlist` | Lock CORS to explicit origin allowlist | upstream | B3 |
| 5 | `fix/auth-rate-limit` | Add `express-rate-limit` to login + MFA endpoints | upstream | (High) |
| 6 | `chore/fork-deploy-config` | Repoint `GITHUB_REPO` in `ec2-setup.sh` to the fork | **fork only — do NOT PR** | B1 (fork side) |

**Security disclosure note:** PRs #2, #3, #4 describe live vulnerabilities in a public repo. Before opening them publicly, follow the upstream `SECURITY.md` policy — DM the author first, give them a chance to merge privately or coordinate disclosure.

---

## Workflow per PR

1. `git fetch upstream && git checkout main && git pull upstream main` (start from latest upstream)
2. `git checkout -b <branch-name>`
3. Make the change. Commit with a clear message.
4. Review the diff together before pushing.
5. `git push -u origin <branch-name>` (pushes to John's fork)
6. Open PR on GitHub: `Tech-Reformers/certpath:<branch>` → `davidodediran/certpath:main`
7. Wait for review. Push more commits to the same branch if changes are requested.
8. After merge: `git checkout main && git pull upstream main && git push origin main` (sync fork)

---

## Status tracker

- [ ] PR #1 — `fix/ec2-setup-branch-casing`
- [ ] PR #2 — `fix/exam-review-idor` *(coordinate disclosure first)*
- [ ] PR #3 — `fix/remove-default-passwords` *(coordinate disclosure first)*
- [ ] PR #4 — `fix/cors-allowlist` *(coordinate disclosure first)*
- [ ] PR #5 — `fix/auth-rate-limit`
- [ ] Fork-only branch — `chore/fork-deploy-config`
- [ ] Fork deployed and verified for student use

---

## Notes

- This file is committed to the fork (`Tech-Reformers/certpath`) only — it is never PR'd upstream. It travels with the fork so John can work from any machine (MacBook, desktop) with consistent state.
- Update the status tracker as PRs land.
