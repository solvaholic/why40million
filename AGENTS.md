# AGENTS.md

Instructions for AI agents working in this repository. Follows the
[agents.md](https://agents.md) convention.
`.github/copilot-instructions.md` is a symlink to this file.

## Project: Why 40 Million?

A weekly blog exploring the recurring "40 million" coincidence across
news, culture, and statistics. Posts are researched and drafted with
help from Copilot, reviewed by a human (the repo owner), then published
to GitHub Pages.

**Audience:** Public, single maintainer (solvaholic).

**Success looks like:** A new, well-sourced post each week with minimal
manual toil; clean Pages site; reproducible build.

## Stack

- **Site generator:** MkDocs Material with the `blog` plugin
- **Language:** Python 3.12+
- **Env / deps:** [`uv`](https://docs.astral.sh/uv/) (uses `pyproject.toml`)
- **Lint / format:** `ruff` (lint + format in one tool)
- **Hosting:** GitHub Pages (built and deployed via Actions)
- **Automation:** GitHub Actions, including a weekly cron that drafts
  a post via Copilot and opens a PR

## Layout (planned)

```
docs/
  index.md                # site landing page
  blog/
    posts/                # one Markdown file per weekly post
.github/
  workflows/
    sync-from-template.yml  # template sync (existing)
    deploy.yml              # build + deploy to Pages on merge to main
    weekly-draft.yml        # weekly cron: research + draft + open PR
scripts/
  draft_post.py           # Copilot-driven research/drafting helper
mkdocs.yml                # site config
pyproject.toml            # python deps + ruff config
```

TODO: scaffold the above; this is intent, not yet on disk.

## Commands

Once the project is scaffolded, expect these to work:

- **Install deps:** `uv sync`
- **Serve locally:** `uv run mkdocs serve`
- **Build site:** `uv run mkdocs build --strict`
- **Lint / format:** `uv run ruff check .` and `uv run ruff format .`
- **Draft a post (manual):** `uv run python scripts/draft_post.py`

Run lint and `mkdocs build --strict` before declaring any change done.

## Conventions

- **Posts:** Markdown in `docs/blog/posts/YYYY-MM-DD-slug.md` with
  MkDocs Material blog frontmatter (`date`, `categories`, `tags`).
- **Sources:** Every factual "40 million" claim must cite a source
  inline (linked) and in a "Sources" section at the bottom of the post.
  No source = don't publish.
- **Voice:** Curious, skeptical, lightly playful. Plain punctuation
  only (no em-dashes, no smart quotes).
- **Commits:** Plain prose, imperative mood (`Add post on uninsured
  Americans`). No conventional-commits prefixes required.
- **Branching:** Feature branches off `main`. PRs only - no direct
  pushes to `main`.

## Agent guardrails

- **Always cite.** Never invent statistics or quotes. If a claim can't
  be sourced, drop it or flag it as `TODO: verify`.
- **Draft, don't publish.** Weekly automation opens a PR; a human
  merges. Don't auto-merge content PRs.
- **Small PRs.** One post or one infra change per PR.
- **Run the build.** `mkdocs build --strict` must pass before merge.
- **No secrets in repo.** Use Actions secrets for any API keys.
- **No history rewrites** on `main` without explicit owner consent.
- **Document before building** for non-trivial changes - sketch the
  approach in the PR description or an issue first.

## Out of scope for agents

- Don't change repo settings (branch protection, Pages config) from
  inside the repo - surface them as recommendations to the owner.
- Don't add analytics, tracking, or third-party JS without asking.
- Don't expand scope beyond "weekly blog about 40-million coincidences"
  without owner approval.
