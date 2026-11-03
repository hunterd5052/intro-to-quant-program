# Quant Program: Roadmap Checklist

Each box is one session of about 1 to 2 hours, and each one becomes a GitHub issue. Every session follows the same shape: recap, then goal and why, then building it together, then a wrap-up on where it fits in the architecture.

Rough size: about 40 sessions. At one per week, that's under a year to a working, backtested report. The practice portfolio runs alongside from day one, with a journal check-in every other week; check-ins are not coding sessions.

## Phase 0: Foundations (3 sessions)
- [x] 0.0 Project docs: problem statement, principles, signals guide, roadmap, journal
- [ ] 0.1 Architecture map: how data sources, fetchers, the snapshot database, signal calculations, the rules matrix, and the report connect
- [ ] 0.2 Python setup: virtual environment, `requirements.txt`, folder structure, `.gitignore`, running tests with pytest
- [ ] 0.3 Turn this checklist into GitHub issues, labels, and a project board

## Phase 1: Learn the data before coding it (5 sessions)
- [ ] 1.1 Summer project post-mortem: rerun one of its correlations and see the noise problem first-hand
- [ ] 1.2 SEC EDGAR by hand: find a company's CIK, browse its filings, and read one Form 4 XML in a browser
- [ ] 1.3 Market data APIs compared: yfinance vs Finnhub vs Tiingo vs Financial Modeling Prep, what each gives for free, and the terms
- [ ] 1.4 Options basics: what implied volatility and skew are, read a real option chain
- [ ] 1.5 Backtesting pitfalls: look-ahead bias, survivorship bias, multiple testing, and costs, with a toy example of each

## Phase 2: Data layer (5 sessions)
- [ ] 2.1 Snapshot database design: SQLite tables, point-in-time `seen_on` dates, never overwriting
- [ ] 2.2 EDGAR client: ticker-to-CIK lookup, filing lists, User-Agent, rate limiting, and retries
- [ ] 2.3 Form 4 parser plus tests on saved real filings
- [ ] 2.4 Insider buying report: find clusters and write a markdown report to `logs/`
- [ ] 2.5 Daily snapshot logger (prices, consensus estimates, options IV) and scheduling it with cron or GitHub Actions

## Phase 3: First backtest, insider clusters (5 sessions)
- [ ] 3.1 Historical prices: adjusted closes for the watchlist and SPY, stored locally
- [ ] 3.2 Event-study framework: returns after an event vs SPY, with averages and t-statistics
- [ ] 3.3 Load about 20 years of Form 4 history for the watchlist
- [ ] 3.4 Backtest: do insider buying clusters beat SPY over 1, 3, and 6 months?
- [ ] 3.5 Robustness and write-up: sub-periods, company size, trading costs; result in `logs/`

## Phase 4: Earnings signals (5 sessions)
- [ ] 4.1 Earnings dates and surprises fetcher, compared against company press releases
- [ ] 4.2 Estimate revisions from our own daily snapshots
- [ ] 4.3 Post-earnings drift event study, using the Phase 3 framework
- [ ] 4.4 The "gap that holds" rule: does the day-one price reaction change the drift?
- [ ] 4.5 Write-up: what works in large caps today vs in the 1990s research

## Phase 5: Positioning and qualitative signals (5 sessions)
- [ ] 5.1 Short interest fetcher and the veto rule
- [ ] 5.2 Options skew from option chains
- [ ] 5.3 Filing language changes: download 10-Qs, compare risk-factor sections, score similarity
- [ ] 5.4 Momentum and 52-week-high confirmations
- [ ] 5.5 Write-up: which vetoes actually helped in backtests

## Phase 6: Rules matrix and report (5 sessions)
- [ ] 6.1 Rules as a config file: triggers, confirmations, vetoes, and their thresholds
- [ ] 6.2 Combined backtest of the full rules, with costs, compared against SPY
- [ ] 6.3 Every-other-week markdown report: candidates and the reason for each
- [ ] 6.4 Open-position tracker: current price vs stop, days until time exit
- [ ] 6.5 Automate the report on a schedule

## Phase 7: Review and portfolio (3 sessions)
- [ ] 7.1 Compare the program's picks against journal outcomes after 30+ trades
- [ ] 7.2 Decide what's next: more signals, more stocks, or machine learning on the clean point-in-time data
- [ ] 7.3 Portfolio write-up: README polish, charts of results, and what you learned

## Later
- Machine learning on our own point-in-time data (only after Phase 7)
- A paid data tier, if a free source proves unworkable
- A simple dashboard for the report
