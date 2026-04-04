# Architecture Overview

> How GitLeetNotes works under the hood.

---

## System Diagram

```
LeetCode (GraphQL API)
        │
        ▼
  fetcher.py  ──── session cookies (LEETCODE_SESSION, LEETCODE_CSRF)
        │
        ▼
  main.py  (orchestrator)
        │
        ├── fetch 20 most recent accepted submissions
        ├── for each new submission:
        │       ├── fetch submission code
        │       ├── fetch problem details (tags, difficulty)
        │       ├── fetch editorial analysis (optional complexity prefill)
        │       ├── infer pattern from topic tags (free fallback)
        │       └── call analyzer.py
        │
        ▼
  analyzer.py  ──── GITHUB_TOKEN (automatic in Actions)
        │            calls GitHub Models API (gpt-4o-mini)
        │            returns: pattern, time/space complexity,
        │                     key_insight, review_tip
        ▼
  note_generator.py
        │            builds markdown note file
        ▼
  progress_tracker.py
        │            updates progress.json
        │            regenerates README.md dashboard
        ▼
  GitHub Actions bot
               commits + pushes all changes to this repo
```

---

## Components

### `fetcher.py` — LeetCode GraphQL Client
- LeetCode has no public API. This uses the same internal GraphQL endpoint their website uses.
- Authenticated via two cookies: `LEETCODE_SESSION` and `csrftoken` (stored as repo secrets).
- Key queries:
  - `userStatus` — verifies login, gets username
  - `submissionList` — fetches recent accepted submissions
  - `submissionDetails` — fetches actual source code for a submission
  - `questionData` — fetches topic tags, difficulty, problem ID
  - `ugcArticleSolutions` — fetches editorial for complexity hints (best-effort)

### `analyzer.py` — GitHub Models Integration
- Calls **gpt-4o-mini** via the GitHub Models API (OpenAI-compatible endpoint at `https://models.inference.ai.azure.com`).
- Authenticated via `GITHUB_TOKEN` — automatically injected by GitHub Actions, no extra setup.
- Returns structured JSON: pattern category, time complexity, space complexity, key insight, review tip.
- Rate limit: 200 requests/day on free tier — well above any daily solving pace.
- **Fallback chain** when the model call fails:
  1. Pattern inferred from LeetCode topic tags (`_TAG_TO_PATTERN` map)
  2. Complexity pulled from editorial if available
  3. "Unknown" if everything fails (retried on next run)

### `note_generator.py` — Markdown Builder
- Assembles the structured analysis + code into a markdown file.
- Files saved to `solutions/<pattern>/XXXX_problem-slug.md`.
- Pattern name becomes the folder — that's how the dashboard groups things.

### `progress_tracker.py` — State + Dashboard
- `progress.json` is the source of truth — tracks every submission by ID so duplicates are skipped.
- Generates the README.md dashboard by reading progress.json — counts by difficulty, counts by pattern, progress bars, recent solutions table.
- The README is fully regenerated on every run (not incrementally updated).

### `.github/workflows/sync.yml` — Daily Sync
- Triggers on: `push` to main (catches initial setup), `schedule` (daily 9 AM UTC), `workflow_dispatch` (manual).
- Checks out this repo AND the `aqn96/gitleetnotes` runner repo at runtime.
- Running the runner from a separate repo means bug fixes to the pipeline automatically apply here without touching this repo.
- Requires permissions: `contents: write` (to commit notes), `models: read` (for GitHub Models API).

### `.github/workflows/refresh.yml` — Weekly Cookie Refresh
- Triggers on: `schedule` (weekly, Sunday midnight UTC), `workflow_dispatch` (manual).
- Checks out `aqn96/gitleetnotes` runner and runs `src/cookie_refresher.py`.
- Uses `LEETCODE_EMAIL` + `LEETCODE_PASSWORD` secrets to log into LeetCode headlessly via Playwright.
- Extracts fresh `LEETCODE_SESSION` + `LEETCODE_CSRF` cookies.
- Updates those secrets using a GitHub PAT (`GH_PAT`) with Secrets: write permission.
- GitHub's server IPs are not flagged by LeetCode's bot detection — no CAPTCHA in Actions.

### `src/cookie_refresher.py` — Headless Login Script
- Uses Playwright Chromium in headless mode to automate LeetCode login.
- Fills email/password, presses Enter, polls for `LEETCODE_SESSION` cookie to appear.
- Updates repo secrets via `gh secret set` using the stored `GH_PAT`.
- Called by `refresh.yml` in Actions (headless) and by `setup.py --setup-auto-refresh` locally (visible browser to bypass CAPTCHA on first run).

---

## Data Flow for a Single Solution

1. `main.py` fetches submission list → finds submission not in `progress.json`
2. Fetches code for that submission ID
3. Fetches problem metadata (tags, difficulty, question ID)
4. Fetches editorial (optional) → extracts complexity hints if present
5. Builds `prefill` dict from tags + editorial (used if model fails)
6. Calls `analyzer.py` → waits 3s (rate limit) → posts to GitHub Models
7. Parses JSON response → gets pattern, complexities, insight, tip
8. `note_generator.py` builds markdown → `save_note()` writes to `solutions/<pattern>/`
9. `record_solution()` adds entry to `progress.json`
10. After all submissions: regenerate `README.md`
11. GitHub Actions bot commits and pushes

---

## Retry Logic for Unknown Notes

If the model fails (rate limit, network error), the note is saved with `pattern = "Unknown"`.

On every subsequent run, `_retry_unknown()` in `main.py`:
- Finds all progress entries where `pattern == "Unknown"`
- Re-fetches code and metadata
- Re-runs analysis
- Updates the note file and progress entry in place

This means rate limit failures are self-healing — they get fixed automatically on the next daily run.

---

## Cookie Refresh Flow

```
Weekly (Sunday midnight UTC)
        │
        ▼
  refresh.yml runs on GitHub Actions
        │
        ├── checkout aqn96/gitleetnotes runner
        ├── install playwright + chromium
        └── run cookie_refresher.py
                │
                ├── headless Chromium login to LeetCode
                │   (LEETCODE_EMAIL + LEETCODE_PASSWORD secrets)
                ├── extract LEETCODE_SESSION + csrftoken cookies
                └── gh secret set (using GH_PAT secret)
                        │
                        ▼
                LEETCODE_SESSION + LEETCODE_CSRF secrets updated
                        │
                        ▼
                sync.yml continues to work with fresh cookies
```

