# Rules for AI assistants

This file is read automatically by Claude, and should be read by any other AI assistant working in this repo. Follow it in every session.

## Read these first

- [docs/PROBLEM_STATEMENT.md](docs/PROBLEM_STATEMENT.md): what the project is trying to learn, and why the earlier weekly-signals project struggled
- [docs/PRINCIPLES.md](docs/PRINCIPLES.md): the research principles. Check every signal, backtest, and design decision against them.
- [docs/SIGNALS.md](docs/SIGNALS.md): the signals, their data sources, and their evidence
- [docs/ROADMAP.md](docs/ROADMAP.md): the phased checklist. Each item is one session and one GitHub issue.

## Who you are working with

The owner, Hunter, builds this project solo, as a passion project, in about 1 to 2 hours a week. The main goals are **learning quantitative research end to end** and **being able to rebuild this project alone**. Treat every session as coaching, not just delivery.

- Explain what you are doing and why, in enough detail that Hunter could do it alone next time.
- Hunter has done a summer project that used machine learning to correlate signals with weekly returns on 100 stocks. They know Python data work, but are new to financial data sources (SEC EDGAR, market data APIs, options data) and to backtesting methodology. Connect every piece of work back to two questions: **where does this data really come from**, and **how could this result be fooling us**.
- Never make big decisions silently. Offer a recommendation, explain the trade-off, and let Hunter choose.

## Session format (always follow)

1. **Recap**: what we did last session, and where it sits on the roadmap.
2. **Goal and forecast**: what we are about to do, why it matters, and what "done" looks like. Make sure Hunter understands the task before building.
3. **Build together**: walk through how you are building it and how Hunter could change it. For any data source, show the raw data (a raw API response or filing) before writing code that parses it.
4. **Wrap-up**: what Hunter just did and how it fits into the overall architecture. Update the roadmap and close the issue.

Size each session to fit 1 to 2 hours.

## Follow the phases in order

Understand a data source before fetching it, fetch it before backtesting it, and backtest a signal on its own before combining it with others. Do not add a signal to the rules matrix until its backtest has been written up in `logs/`. Do not train machine learning models on the signals until the roadmap says so.

## Code style

- Stack: **Python 3.11+**, with `requests`, `pandas`, and SQLite (built into Python). Add a dependency only when it earns its place, and explain why.
- **Comment heavily.** Use section banners such as `#========== FORM 4 PARSING ==========` and a plain-English comment or docstring above nearly every function, class, and important variable, explaining what it is and why it exists. When code touches a data source, the comment says where the data comes from (the exact URL or endpoint), what the raw format looks like, and any quirks. This is a learning codebase, so comments are a feature.
- Keep files small and focused. Separate **fetching** (talking to outside sources), **storage** (the snapshot database), **signals** (turning data into signal values), **backtests**, and **reporting**, so the architecture stays visible in the folder structure.
- Prefer clear rules over clever code. Readability wins over brevity.
- Write a test for every parser, using a small saved sample of real raw data.

## Research rules

- Every backtest starts with a written hypothesis and the result is written up in `logs/`, including when the signal fails.
- Store every fetched value with the date it was seen (point-in-time). Never overwrite history.
- Use official APIs and filings over scraping. Respect rate limits and terms of service. The SEC requires a User-Agent with contact details and allows at most 10 requests per second.
- Never commit API keys or personal contact details. Keep them in environment variables or a git-ignored `.env` file.
- The program produces a shortlist for human judgment. It never places trades.

## Journal

Trades live in `journal/`, one markdown file per check-in (every other week). Each trade records its trigger, confirmation, vetoes checked, exit plan, and outcome vs SPY. See [journal/README.md](journal/README.md).

## GitHub workflow

Hunter is using this project to practice GitHub.

- Every unit of work starts as a **GitHub issue**, and its branch is named after it (for example `12-form4-parser`).
- Make small commits with clear messages that explain *why*, not just *what*.
- Open a **pull request** for every change, link the issue (`Closes #12`), and explain the change so Hunter can review the diff.
- Never push directly to `main`.
