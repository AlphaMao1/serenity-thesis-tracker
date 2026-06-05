# Serenity Thesis Tracker

PaiWork-first skill for tracking Serenity (@aleabitoreddit) investment theses from X/Twitter posts.

This repository packages a Codex/PaiWork skill that turns Serenity's posts into cumulative research assets: company files, verifiable thesis and claim ledgers, supply-chain relationship maps, daily reports, weekly reviews, and research backlog items.

## Important Status

This version is strongly dependent on PaiWork.

Many workflows assume PaiWork-specific tools, data sources, analyst report generation, market-data access, Paipai search, and workspace conventions. The current release mainly supports the PaiWork environment. A more generic agent version can be added later, but this repository should not pretend to be fully portable yet.

The bundled fetch script intentionally adds no package dependencies. It uses Python standard-library modules and expects an existing PaiWork/front-end CDP-compatible browser bridge. Do not add new dependencies unless there is a clear product reason.

## What It Does

- Fetch Serenity posts from a logged-in browser session through an existing CDP bridge.
- Store raw posts in `raw/serenity_statuses.jsonl`.
- Generate a daily markdown intake file under `daily/`.
- Guide PaiWork/Codex to classify posts by research value.
- Extract structured thesis, claims, tickers, and company relationships.
- Update company files and supply-chain maps without duplicating source-of-truth content.
- Generate incremental daily and weekly research reports.

## Repository Layout

```text
serenity-thesis-tracker/
├── assets/
│   └── sample-deep-research-report.md
├── references/
│   ├── schemas.md
│   ├── company_one_pager_prompt.md
│   ├── daily_report_prompt.md
│   └── templates.md
├── scripts/
│   └── fetch_daily.py
├── agents/
│   └── openai.yaml
├── SKILL.md
└── README.md
```

## Runtime Expectations

For full functionality, run inside PaiWork or a PaiWork-compatible environment with:

- PaiWork analyst/report tooling for company pages, daily reports, and weekly reviews.
- PaiWork search, market-data, and research data sources for independent verification.
- A logged-in X/Twitter browser session.
- A CDP-compatible bridge at `http://localhost:3456` supporting `/health`, `/new`, `/eval`, `/scroll`, and `/close`.
- Python 3.10+ for the bundled fetch script.

No Python packages are required by `scripts/fetch_daily.py`.

## Quick Start

From the skill root:

```powershell
python scripts/fetch_daily.py --out-dir tree/Serenity --handle aleabitoreddit
```

The script writes:

```text
tree/Serenity/
├── raw/
│   ├── serenity_statuses.jsonl
│   └── serenity_status_ids.txt
└── daily/
    └── serenity_YYYY-MM-DD.md
```

Then ask PaiWork/Codex to process the daily intake, for example:

```text
执行 Serenity 每日观点处理
```

## Skill Workflow

The main workflow is defined in `SKILL.md`.

High-level flow:

1. Fetch Serenity posts.
2. Classify each post as thesis, update, evidence, catalyst, risk, rebuttal, price-action comment, noise, or meta.
3. Score materiality from 5 to 15.
4. Extract thesis only when the post contains a causal investment chain.
5. Convert thesis into verifiable claims and company-to-company relationships.
6. Update company research files as the single source of truth.
7. Update supply-chain maps as the relationship navigation layer.
8. Generate daily/weekly reports as incremental logs.

## Limitations

- This is not a standalone X scraper. It depends on a logged-in browser and an existing PaiWork/front-end CDP bridge.
- It does not perform translation or investment analysis by itself; PaiWork/Codex performs the reasoning after intake files are created.
- X/Twitter DOM selectors can change. If extraction fails, update the DOM extraction logic in `scripts/fetch_daily.py`.
- Source access and data quality depend on the logged-in account and PaiWork environment.

## Roadmap

- Add a generic-agent workflow that does not assume PaiWork tools.
- Add adapter documentation for non-PaiWork CDP bridges.
- Add optional tests/fixtures once the public contract stabilizes.

## License

MIT
