# AGENTS.md — rules for every coding agent (Codex, Cursor, Antigravity/agy, others)

1. **Read first:** `HANDOFF.md` (current state), then `PLAN.md` (milestones), then skim `DECISIONS.md`. The repo files are the source of truth; `/workspace/projects/BOARD.md` is only an overview index.
2. **Claim before working:** pick the first unchecked, unclaimed milestone in `PLAN.md` and claim it with
   `/workspace/projects/board.sh claim <TASK-ID> <agent> <model> job/<TASK-ID>` (flock-safe; it marks the PLAN.md line `[~] claimed by …`). Exit 3 means someone else holds it: pick another. One active claim per milestone. A claim older than 24h with no HANDOFF update is stale and may be taken over (say so in HANDOFF.md).
3. **Branch:** work only on local branch `job/<TASK-ID>` created from the latest base branch (named in HANDOFF.md). Never push, never change remotes, never deploy, never touch secrets or token files.
4. **At every stop** (done, blocked, out of time/quota): overwrite `HANDOFF.md` (keep its section headings), then run
   `/workspace/projects/board.sh checkpoint <TASK-ID> '<one-line note>' --status claimed|in-review|blocked --next '<next step>'` and commit.
5. **Decisions:** append real choices to `DECISIONS.md` as `## Dnnn — <title> (<date>, <agent>)` with Decision / Why / Alternatives rejected. Never edit old entries; supersede with a new one.
6. **Done means:** tests pass, plus (for web apps) a Playwright browser test and screenshots in `artifacts/<TASK-ID>/`, HANDOFF.md updated, and status set to `in-review`. Only a reviewer (Codex) or Anil moves a task to done.
7. Change `PLAN.md` only for scope changes (besides the claim markers board.sh writes).

## This repo (udagram-frontend)
- Path: `/workspace/repos/udagram-frontend` | Remote: https://github.com/anilkumarnandamuri/udagram-frontend | Base branch: `master`
- Run: `npm install && ionic serve`
- Tests: udagram_tests/, e2e/ (unknown status).
- **Dormant: no commits since 2020-07-15. No active work planned; treat as archived unless Anil revives it.**

## Cursor Cloud specific instructions

- Angular CLI 8 and webpack 4 need Node.js 12.22.12. The image Node 22 fails this toolchain. Install puts Node at `/opt/node-v12.22.12`. Prefix PATH before `npm` or `ng`: `export PATH="/opt/node-v12.22.12/bin:$PATH"`.
- The boot `start` script serves the app at `http://127.0.0.1:4200` with `npx ng serve --host 0.0.0.0 --port 4200 --disable-host-check`. `@ionic/cli` is not a project dependency; use `ng`, not `ionic serve`.
- Unit tests need the sandbox-free Chrome wrapper from install: `CHROME_BIN=/opt/chrome-nosandbox/google-chrome npx ng test --watch=false --browsers=ChromeHeadless`.
- `npx ng build` succeeds. `npx ng lint` fails on a missing newline at the end of `src/app/feed/services/feed.provider.service.ts`. Several Karma specs fail because they omit `HttpClient` or `ReactiveFormsModule`.
- `src/environments/environment.ts` sets `apiHost` to an AWS ELB hostname that does not resolve. The shell still loads. Feed, login, and register requests show `Http failure response ... 0 Unknown Error`.
