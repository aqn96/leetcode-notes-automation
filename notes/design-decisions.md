# Design Decisions & Tradeoffs

> The why behind the architecture choices, including bugs hit during development.

---

## Why a Separate Runner Repo?

The workflow in this repo checks out `aqn96/gitleetnotes` at runtime and runs its scripts:

```yaml
- uses: actions/checkout@v4
  with:
    repository: aqn96/gitleetnotes
    path: _runner
- run: python _runner/src/main.py
```

**Why:** Bug fixes and improvements to the pipeline automatically apply to all user repos without requiring users to update anything. Your repo just holds your notes and a thin workflow — the logic lives in gitleetnotes.

**Tradeoff:** You're pinned to whatever is on the main branch of gitleetnotes. A breaking change there would break your sync. Mitigation: pin to a specific commit SHA or tag if stability becomes a concern.

---

## Why GitHub Models Instead of Gemini?

Originally used Gemini 2.0 Flash (free tier). Switched to GitHub Models (gpt-4o-mini).

| | Gemini Free | GitHub Models (gpt-4o-mini) |
|---|---|---|
| Auth | Separate API key (manual setup step) | `GITHUB_TOKEN` (automatic in Actions) |
| Daily limit | 1,500 req/day | 200 req/day |
| Setup friction | User must get key from Google AI Studio | Zero — nothing to configure |
| Quality | Good | Better for structured JSON output |

**Decision:** GitHub Models wins on zero-setup UX. 200 req/day is plenty for LeetCode pace (~30/month).

**Tradeoff:** Lower daily limit. If you somehow solve 200+ problems in one day you'd hit the cap. Not a real concern in practice.

---

## Why `on: push` Trigger?

The workflow triggers on every push to main, not just on schedule.

**Why:** When setup.py creates a fresh repo and pushes the initial commit, the workflow needs to fire immediately to sync your existing solutions. Without this, you'd have to manually trigger it or wait until 9 AM UTC.

**Tradeoff:** The workflow also fires when the bot itself commits new notes (daily sync). That second run finds 0 new solutions and exits in ~15 seconds. Harmless on a public repo (free Actions minutes).

**What we tried first:** A fixed `sleep(8)` after pushing, then `gh workflow run`. Failed because GitHub's Actions API takes 1-3 minutes to index a newly created workflow file — the hardcoded delay wasn't long enough.

**What we tried second:** Polling `gh workflow view sync.yml` every 5s for up to 5 minutes. Still failed intermittently — the Actions API indexing delay is unpredictable.

**Final fix:** Push the workflow file only AFTER secrets are set. The `on: push` trigger fires reliably because it doesn't depend on Actions API indexing — it's triggered by the git push event directly.

---

## Why Secrets Before Files?

The critical ordering insight:

```
WRONG order:          CORRECT order:
1. Create repo        1. Create repo (empty)
2. Push files  ──►    2. Extract cookies
   (workflow fires)   3. Set secrets  ◄── secrets ready
3. Extract cookies    4. Push files  ──► workflow fires with creds
4. Set secrets
   (too late!)
```

The first version of setup.py pushed files and set secrets in the wrong order. The workflow fired on the initial push before `LEETCODE_SESSION` and `LEETCODE_CSRF` were set, causing an immediate failure with `ERROR: LEETCODE_SESSION and LEETCODE_CSRF must be set.`

Fix: `scaffold_repo()` (the push) was moved to the end of `main()`, after `configure_repo()` sets secrets.

---

## Why Cookie Auto-Refresh Is a Post-Setup Step

There's a chicken-and-egg problem with the auto-refresh PAT:

- Fine-grained PATs require selecting a specific repo to scope permissions to
- The notes repo doesn't exist until `setup.py` creates it
- Therefore the PAT can only be created after the initial setup completes

This makes `--setup-auto-refresh` necessarily a separate manual step after setup, not part of the initial flow.

**Workaround considered:** Use a PAT with "All repositories" scope — can be created before the repo exists. Rejected because broader permissions is worse security for marginal UX gain.

**Final design:** Document it clearly as an optional post-setup step in the README.

---

## Why GitHub Actions Doesn't Trigger CAPTCHA But Local Does

LeetCode's bot detection is IP-based. Your Mac's IP (or any residential/developer IP running Playwright) is flagged because automated browsers from those IPs are a known scraping pattern.

GitHub Actions runners use data center IPs from Microsoft Azure. LeetCode does not flag these — they're shared infrastructure used by millions of legitimate CI/CD pipelines.

**Practical consequence:** The initial `--setup-auto-refresh` run on your Mac may show a CAPTCHA (solve it once manually). Every subsequent run in GitHub Actions works headlessly without interruption.

---

## Why `progress.json` as State?

Instead of checking which files exist in `solutions/`, the script tracks everything in `progress.json` keyed by submission ID.

**Why submission ID, not problem ID?** LeetCode allows multiple accepted submissions per problem (different languages, different approaches). Using submission ID means each distinct submission gets its own note, even for the same problem.

**Why not query the filesystem?** Filesystem checks are fragile — a file might exist but be stale, missing metadata, or in the wrong folder if the pattern was updated. `progress.json` is the single source of truth.

**Tradeoff:** `progress.json` can drift from actual files if someone manually deletes a note. The retry logic for "Unknown" patterns partially mitigates this but doesn't cover all cases.

---

## Why Regenerate the Whole README?

Every run rewrites `README.md` from scratch using `progress.json`.

**Why not append?** Incremental updates are error-prone — counts drift, formatting breaks, sections get duplicated. Regenerating from a known-good data source (progress.json) is always correct.

**Tradeoff:** Any manual edits to README.md are wiped on the next sync. This is intentional — the README is treated as a generated artifact, not a hand-edited file.

---

## LeetCode API Stability Risk

LeetCode has no public API. The GraphQL queries used here are the same ones their web frontend makes. This is a brittle dependency:

- Session cookies expire every few weeks (handled automatically by refresh.yml if auto-refresh is configured)
- LeetCode could change their GraphQL schema at any time, breaking the fetcher
- Rate limiting or IP blocks are possible if requests look suspicious

This is an accepted risk for a free, zero-infrastructure tool.

