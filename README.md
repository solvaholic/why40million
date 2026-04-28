# Why 40 Million?

> 40 million Americans have no health insurance. 40 million people
> suffer anxiety. Taylor Swift's cat scratch cost her 40 million
> dollars. Why is it always 40 million?!

A weekly blog chasing the suspiciously recurring "40 million"
coincidence wherever it shows up - in policy, pop culture, science,
and statistics. Posts are researched and drafted with the help of
GitHub Copilot, reviewed by a human, and published to GitHub Pages.

## Read it

Site: <https://solvaholic.github.io/why40million/> (live once Pages is
enabled)

## How it works

1. A weekly GitHub Actions cron asks Copilot to research a fresh
   "40 million" angle and draft a post.
2. The workflow opens a pull request with the draft.
3. A human reviews, edits, and merges.
4. A separate workflow builds the site with MkDocs Material and
   deploys to GitHub Pages.

## Local development

Requires [`uv`](https://docs.astral.sh/uv/).

```sh
uv sync
uv run mkdocs serve     # http://127.0.0.1:8000
uv run mkdocs build --strict
uv run ruff check .
```

## Contributing

This is a personal project, but spotted a "40 million" claim worth
investigating? Open an issue with a source link.

## License

Dual-licensed:

- **Code** (workflows, scripts, config): [MIT](LICENSE)
- **Content** (posts, images, prose in `docs/`): [CC0 1.0 Universal](LICENSE-content) -
  effectively public domain. Do whatever you like; no warranty.

