---
name: ai-news-radar
description: Use when working on the LearnPrompt AI News Radar / AI Signal Board repo, adding high-signal AI news sources, configuring private OPML feeds, deploying the GitHub Pages site, or helping Codex/Claude agents maintain the project safely.
---

# AI News Radar

## First Reads

When this skill triggers inside the repo, read these files first:

- `README.md` for project usage and current commands.
- `docs/SOURCE_COVERAGE.md` before changing source strategy.
- `scripts/update_news.py` before changing data generation.
- `assets/app.js`, `assets/styles.css`, and `index.html` before changing the UI.

## Product Direction

Maintain a two-layer product:

- **Default layer**: a simple curated Signal view for ordinary AI enthusiasts.
- **Advanced layer**: custom OPML, source health, GitHub Actions, and maintainer controls.

Avoid adding many reader-facing choices. Prefer better defaults, source quality,
and clearer status output.

## Safety Rules

- Never commit private `feeds/follow.opml`.
- Never paste secrets, tokens, cookies, browser exports, or `.env` values into code or logs.
- Keep the public repo runnable without API keys.
- Prefer official RSS/Atom/OPML sources over fragile scraping.
- Avoid account-bound social timelines as defaults.

## Add Personal Sources

Use OPML for private customization:

```bash
cp feeds/follow.example.opml feeds/follow.opml
python scripts/update_news.py --output-dir data --window-hours 24 --rss-opml feeds/follow.opml
```

For GitHub Actions deployment, base64 encode `feeds/follow.opml` and save it as
the repository secret `FOLLOW_OPML_B64`. Do not commit the private OPML file.

## Add A Built-In Source

Only add a built-in source when it is useful to most public visitors.

1. Inspect existing fetchers in `scripts/update_news.py`.
2. Add `fetch_<source>(session, now)` returning `list[RawItem]`.
3. Use existing helpers for URL normalization, date parsing, and sessions.
4. Register the fetcher in the built-in task list.
5. Update `docs/SOURCE_COVERAGE.md` when coverage changes.
6. Add or update tests when behavior changes.

## Validate

Run the fastest relevant checks:

```bash
python -m py_compile scripts/update_news.py
pytest -q
```

For an end-to-end local run:

```bash
python scripts/update_news.py --output-dir data --window-hours 24 --rss-opml feeds/follow.opml
python -m http.server 8080
```

Open `http://localhost:8080` and confirm the Signal view, all-source view,
WaytoAGI block, search, site filter, and source counts still work.
