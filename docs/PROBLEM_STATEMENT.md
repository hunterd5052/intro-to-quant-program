# Problem Statement

## The question

Can a small, rules-based program, built on free public data, consistently point to stocks that are more likely than average to outperform over the next few days to weeks? And if it can't, can it at least teach its builder how professional quantitative research separates real signals from noise?

## Why the first attempt struggled

Over summer 2026, an earlier project collected signals on 100 stocks across the tech, cyclical, industrial, and home-goods sectors. A machine learning model learned weights from historical correlations between those signals and one-week returns. Each Sunday, the program bought the single stock with the best score. The weekly edge turned out to be unreliable. Five structural problems explain most of that:

1. **Weekly returns are mostly noise.** A typical stock moves about 4 to 5% in a week. Even a good signal explains only a sliver of that, which means a correlation of roughly 0.03 to 0.07. With 100 stocks and a few years of data, that is hard to tell apart from zero.
2. **Holding one stock throws away the edge.** A weak signal pays off only on average across many bets. One stock a week means the result is driven by that stock's news, not by the signal.
3. **Mixing horizons cancels signals out.** Momentum predicts gains over 3 to 12 months but *reversals* over one week to one month. A one-week target blends opposite effects.
4. **Correlations learned from the past decay.** Published signals lose about a third to half of their returns after publication, and more features plus more tuning make overfitting worse.
5. **Weekly turnover eats small edges.** Trading every week pays spreads and slippage 52 times a year, plus short-term capital gains tax.

## The approach

Change the question from "which feature correlates with next week's return?" to **"which events create a documented, explainable mispricing, and how long does it last?"**

1. **Event-driven signals.** Focus on signals with a clear start date, a known decay window, and an economic reason to exist: earnings surprises, analyst estimate revisions, insider buying clusters, and options and short-selling positioning. See [SIGNALS.md](SIGNALS.md).
2. **Understand the data at its source.** Learn where each signal really comes from (an SEC filing, an exchange report, an options chain) before trusting any library that wraps it.
3. **Point-in-time storage.** Save every value with the date it was seen, so backtests only use what was knowable at the time.
4. **One signal, one hypothesis, one backtest.** Test each signal alone, write up the result, and only then add it to the rules.
5. **A matrix of rules, not a single score.** A stock qualifies when it has a fresh *trigger*, at least one independent *confirmation*, and no *vetoes*. Every recommendation can be explained in plain English.
6. **A live journal alongside the code.** A small practice portfolio is traded by hand every other week while the program is built. Real outcomes test the reasoning, and later test the program.

## The architecture, in one line

Public data sources → fetchers → point-in-time snapshot database → signal calculations → rules matrix → markdown report → human judgment → journal.

## What success looks like

- Each signal in the roadmap has a written backtest in `logs/`, honest about whether it worked.
- The program produces an every-other-week markdown report that explains each candidate's trigger, confirmations, and vetoes.
- Hunter can explain, and rebuild alone, where every number in the report comes from.
- After 30 or more journal trades, there is an honest comparison against simply holding SPY. Losing to SPY is a valid and useful result.

## Out of scope

- Automated trading or connecting to a brokerage to place orders
- High-frequency or intraday strategies
- Paid data feeds, unless a free source proves unworkable
- Machine learning, until the rules-based version has been backtested and run live
