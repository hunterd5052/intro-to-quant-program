# Signals Guide

This is the reference for every signal the program uses or considers: what it is, why it might work, where the data really comes from, how to get it, and how strong the evidence is. The full lesson behind it is the [Short-Term Stock Signals field guide](https://claude.ai/code/artifact/c1ef14c4-fd78-4c20-b3a3-0e96a71ac5a4).

Evidence ratings:
- **Strong:** decades of replication, and it holds up in mid and large caps.
- **Moderate:** credible support, but weaker, smaller, or fading.
- **Weak:** mixed results, mostly in tiny stocks, or largely arbitraged away.

## At a glance

| Signal | Family | Evidence | Window | Role in the rules | Primary source |
| --- | --- | --- | --- | --- | --- |
| Insider buying clusters | Event | Strong | 1–6 months | Trigger | SEC EDGAR Form 4 |
| Post-earnings drift | Event | Strong (Moderate in large caps) | 1 day–8 weeks | Trigger | Company 8-K / earnings data API |
| Analyst estimate revisions | Event | Strong | 2–12 weeks | Trigger | Estimates API, stored by us |
| 12-month momentum, 52-week high | Price | Strong, crash-prone | 1–12 months | Confirmation | Daily prices |
| Options implied-vol skew | Positioning | Moderate to Strong | Days–3 weeks | Confirmation or veto | Options chains |
| Short interest and borrow fee | Positioning | Strong (as a negative) | Weeks–months | Veto | FINRA / exchanges; broker |
| Filing language changes | Qualitative | Moderate | 1–6 months | Veto | SEC EDGAR 10-K / 10-Q |
| Short-term reversal (no-news moves) | Price | Moderate, cost-sensitive | Days–4 weeks | Entry timing | Daily prices |
| Retail attention spikes | Sentiment | Moderate | ~2 weeks, then reverses | Caution flag | Google Trends |
| Seasonality | Price | Weak to Moderate | Days–1 month | Tiebreaker | Daily prices |
| 13F "best ideas" | Positioning | Weak to Moderate | Months | Idea list only | SEC EDGAR 13F-HR |
| Unusual options flow | Positioning | Weak for retail | Days | Not used | Paid services |
| Congressional trades | Positioning | Weak | None after 45-day delay | Not used | STOCK Act disclosures |

## Event signals (triggers)

### Insider buying clusters

- **What it is.** Company officers, directors, and 10% owners must report their trades in the company's stock on SEC Form 4 within two business days. Open-market purchases (transaction code `P`) by several insiders within about 30 days form a cluster.
- **Why it might work.** Insiders sell for many reasons (taxes, diversification, a house), but buy for one: they think the stock is cheap. Cohen, Malloy & Pomorski (2012) found that "opportunistic" trades, which break an insider's usual pattern, predict returns of about 0.8% a month, while routine trades predict nothing.
- **Where the data comes from.** The SEC's EDGAR system. Each company has a CIK number. The ticker-to-CIK map is at `https://www.sec.gov/files/company_tickers.json`. A company's filing list is at `https://data.sec.gov/submissions/CIK##########.json` (CIK padded to 10 digits). Each Form 4 is an XML file in the filing's folder under `https://www.sec.gov/Archives/edgar/data/`.
- **What the raw data looks like.** Form 4 XML has a `reportingOwner` block (name, and whether they are a director or officer) and a `nonDerivativeTable` with one `nonDerivativeTransaction` per trade: date, transaction code, shares, and price per share.
- **Quirks.** Only code `P` is a true open-market buy; `A` is a grant, `M` is an option exercise, `S` is a sale, `F` is shares withheld for tax. Use the *filing date*, not the trade date, as the date the market could know. The SEC requires a User-Agent header with contact details and allows at most 10 requests per second.
- **Window.** Slow: about 1 to 6 months. Strongest in small and mid caps, and when the CEO or CFO buys.

### Post-earnings announcement drift (PEAD)

- **What it is.** After a company reports earnings well above or below expectations, the stock tends to keep drifting the same way for weeks.
- **Why it might work.** Investors anchor on old beliefs and underreact to how lasting a surprise is (Bernard & Thomas, 1989).
- **Where the data comes from.** The original source is the company's earnings press release, filed with the SEC as an 8-K (Item 2.02). Expectations ("consensus") come from analysts and are collected by data vendors. Free access: Finnhub's earnings-surprise endpoint (free API key), Financial Modeling Prep (limited free tier), or the unofficial `yfinance` library.
- **Quirks.** "Beat" depends on whose consensus you use. Adjusted (non-GAAP) EPS is what analysts estimate, so compare like with like. Martineau (2022) found that in large US stocks, most of the drift now happens in the first day or two.
- **How we use it.** Look at the quality of the surprise: an EPS beat, a revenue beat, *raised guidance*, and a price gap on heavy volume that holds. A beat that the stock sells off on is a warning, not a buy.

### Analyst estimate revisions

- **What it is.** When analysts start raising earnings estimates, more raises tend to follow, and the stock tends to outperform.
- **Why it might work.** Analysts herd and revise gradually, so early revisions predict later ones.
- **Where the data comes from.** Analysts publish estimates to vendors (LSEG I/B/E/S, FactSet). Free APIs expose only today's consensus, so **the history has to be built by saving a snapshot every day**. This is why the daily snapshot logger is early on the roadmap.
- **Signal.** The 4-week percent change in the next fiscal year's consensus EPS, and the count of raises minus cuts.

## Price signals (confirmations and timing)

### 12-month momentum and the 52-week high

- **What it is.** Stocks that outperformed over the past 12 months, skipping the latest month, tend to keep outperforming for several months (Jegadeesh & Titman, 1993). Stocks near their 52-week high do too (George & Hwang, 2004).
- **Where the data comes from.** Daily adjusted closing prices. Free: Tiingo, Alpaca, or `yfinance`. "Adjusted" means corrected for splits and dividends; always use it for returns.
- **Quirks.** Momentum crashes hard when markets rebound from a bottom (2009 is the classic case).

### Short-term reversal

- **What it is.** Last week's or month's biggest losers tend to bounce, and winners give some back, *when the move had no news behind it*.
- **Why it matters here.** A drop caused by an earnings miss tends to continue (that's PEAD). The same price move predicts opposite things depending on whether news caused it, which likely confused the summer model.

## Positioning signals (confirmations and vetoes)

### Options implied-volatility skew

- **What it is.** Skew compares the implied volatility of out-of-the-money puts with at-the-money calls. Unusually expensive puts mean someone is paying for downside protection.
- **Why it might work.** Informed traders often use options first. Xing, Zhang & Zhao (2010) found the steepest-skew stocks underperform the flattest by about 10% a year.
- **Where the data comes from.** Options trade on exchanges, and quotes are distributed through OPRA. Free option chains with implied volatility come through `yfinance`. Brokers show the same data.
- **Signal.** Implied vol of a roughly 25-delta put minus a roughly 50-delta call, about 30 days to expiry. A sudden jump is the warning, more than the level.

### Short interest and borrow fee

- **What it is.** Short interest is the number of shares sold short, as a percent of shares available to trade. The borrow fee is what short sellers pay to borrow shares.
- **Why it might work.** Short sellers are, on average, informed researchers. High and rising borrow fees predict low returns (Engelberg, Reed & Ringgenberg, 2018).
- **Where the data comes from.** Brokers report short positions to FINRA twice a month; FINRA and the exchanges publish them about two weeks later. Borrow fees come from brokers (for example Interactive Brokers) and are the hardest signal to get for free.
- **How we use it.** As a veto. Buying a stock *because* it is heavily shorted, hoping for a squeeze, is a losing bet on average.

## Qualitative signals (vetoes)

### Filing language changes ("Lazy Prices")

- **What it is.** When a company quietly makes large changes to the wording of its 10-K or 10-Q, especially the risk factors, the stock tends to underperform over the following months (Cohen, Malloy & Nguyen, 2020).
- **Where the data comes from.** 10-K and 10-Q filings on SEC EDGAR, the same system as Form 4.
- **Signal.** Text similarity between this filing and the same section of the previous one. A low similarity score means big changes.

### Retail attention spikes

- **What it is.** Sudden jumps in Google searches or social media mentions.
- **Evidence.** Spikes predict higher prices for about two weeks, then a reversal (Da, Engelberg & Gao, 2011). Stocks heavily bought by Robinhood users later underperformed (Barber et al., 2022).
- **Where the data comes from.** Google Trends, through the unofficial `pytrends` library, which is fragile.

## Signals we deliberately skip

- **Unusual options flow.** The research that found real predictive power used non-public data on who opened positions. Retail "flow" feeds can't tell openings from closings or hedges.
- **Congressional trades.** A 45-day reporting delay, and better-data studies find no average edge (Eggers & Hainmueller, 2013).

## How the signals combine

A stock reaches the report only if all three are true:

1. **A trigger:** a fresh earnings surprise with raised guidance, an insider cluster, or a revision wave.
2. **A confirmation from a different family:** for example revisions after a beat, or insiders buying while the stock is in a long-term uptrend.
3. **No vetoes:** no high and rising short interest, no sudden put-skew jump, no big filing-language change, no attention spike.

Each candidate then gets an exit plan: a time exit matched to its signal's window, and a price stop.

## Key papers

Barber et al. (2022) · Bernard & Thomas (1989) · Cohen, Malloy & Nguyen (2020) · Cohen, Malloy & Pomorski (2012) · Da, Engelberg & Gao (2011) · Eggers & Hainmueller (2013) · Engelberg, Reed & Ringgenberg (2018) · George & Hwang (2004) · Harvey, Liu & Zhu (2016) · Jegadeesh & Titman (1993) · Martineau (2022) · McLean & Pontiff (2016) · Xing, Zhang & Zhao (2010). Full titles are in the field guide linked at the top.
