# Research Principles

These are the rules of the project. Every signal, backtest, line of code, and trade should be checked against them. When two options are both reasonable, pick the one that fits these better.

## 1. Every signal needs a story

Before testing a signal, write down who is on the other side of the trade and why they are wrong. Good answers: investors underreact to news, insiders know more, short sellers do research, forced sellers must sell. "The pattern looks good in the data" is not a story, and a signal without one is probably noise.

## 2. One hypothesis at a time

Write the hypothesis before running the test, then test one signal alone. Testing many features together and keeping whatever correlates is how the summer project found patterns that didn't hold. When you try many variations, say so, and demand stronger evidence (a t-statistic above 3, not 2).

## 3. Only use what you could have known

Use the date data became public, not the date it describes. A Form 4 for a trade on September 28 is usable only after it is filed. Store every value with the date it was seen, and never overwrite history. Look-ahead bias is the most common way backtests lie.

## 4. Know where every number comes from

Trace every signal to its original source: an SEC filing, an exchange file, an options chain, a company report. Look at the raw data before writing code that parses it. Libraries and websites that repackage data can be wrong, delayed, or quietly change.

## 5. Match the holding period to the signal

Every signal has a window where it works and then fades. Hold for that window, set the exit before entering, and don't trade a signal after its window has closed.

## 6. Costs and size are part of the result

A backtest that ignores spreads, slippage, and taxes is incomplete. Check that a signal works in stocks large enough to actually trade, not just in tiny, illiquid names.

## 7. Rules before machine learning

Prefer clear, explainable rules: a trigger, a confirmation, and vetoes. Every recommendation must be explainable in a sentence. Machine learning comes later, if ever, and only once there is enough clean point-in-time data to justify it.

## 8. Diversify the bets

Several small positions beat one big one. Cap any single trade's loss at about 1 to 2% of the account, and hold no more than two positions in the same sector.

## 9. Respect sources and secrets

Prefer official APIs and filings over scraping. Follow rate limits and terms of service. Never commit API keys or personal contact details to the repo.

## 10. Honest results, including failures

Write up every backtest, especially the ones that fail. Compare everything against simply holding SPY. A signal that doesn't work is a finding, not a waste of time.
