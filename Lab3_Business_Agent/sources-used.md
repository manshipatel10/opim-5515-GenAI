# Tesla Step 3 — source notes and verification

## Scope and access date

- Company: Tesla, Inc.; ticker: TSLA; audience: CFO.
- Requested price/news window: August 1–October 31, 2026, inclusive.
- Prepared and accessed: November 14, 2026, America/New_York. The successful price retrieval completed at approximately 20:24 EST (November 15, 2026, approximately 01:24 UTC).
- These successful retrievals supersede the earlier blocked status. `retrieval-attempts.json` retains the earlier failed requests and is historical troubleshooting evidence, not the current dataset.

## P1 — primary daily prices: Yahoo Finance

- Publisher: Yahoo Finance.
- Historical page: https://finance.yahoo.com/quote/TSLA/history/
- Exact retrieval URL: https://query1.finance.yahoo.com/v8/finance/chart/TSLA?period1=1785542400&period2=1793491200&interval=1d
- Publication dates: each observation's trading date, saved in `prices-used.csv`; this dynamic data endpoint has no single article publication date.
- HTTP 200; symbol TSLA; currency USD; exchange timezone America/New_York; no chart API error.
- Parsed `chart.result[0].timestamp` and `indicators.quote[0].close`; **not** `adjclose` or intraday prices.
- Converted timestamps to America/New_York dates; retained only dates within the requested window. All 64 observations had non-null, positive closes.
- Saved convention: unadjusted daily closing price in USD, rounded half up to two decimal places using Python `Decimal`. Binary floating-point representation in the Yahoo response is normalized to cents. The same saved series is used for the calculation and chart.
- Each CSV row records the primary source URL, date, ticker, closing price, and convention. No missing-session prices were fabricated or interpolated.

## P2 — independent price cross-check: Nasdaq

- Publisher: Nasdaq.
- Historical page: https://www.nasdaq.com/market-activity/stocks/tsla/historical
- Exact retrieval URL: https://api.nasdaq.com/api/quote/TSLA/historical?assetclass=stocks&fromdate=2026-08-01&todate=2026-10-31&limit=100
- Publication dates: observation trading dates; dynamic endpoint has no single publication date.
- HTTP 200; provider status `rCode: 200`; 64 dated price rows returned, below the requested limit of 100.
- Parsed `data.tradesTable.rows`, using `date` and `close`, and compared both date sets and every close with Yahoo.
- **64 of 64 closes match at cent precision** using `ROUND_HALF_UP` for both sources. This is not a claim that every raw quote is identical: Nasdaq reports September 3 as `$376.365`, while Yahoo rounds to `$376.37` at cents. Both normalize to `$376.37` under the stated convention.
- During the earlier network test, Nasdaq rejected slash-formatted `fromdate=08/01/2026`; switching to ISO `2026-08-01` resolved that parameter error. The successful ISO URL above was used for this dataset.

## N1 — operating development

- Title: **Tesla Third Quarter 2026 Production, Deliveries & Deployments**.
- Publisher: Tesla Investor Relations; page marked Business Wire.
- Publication date: **October 2, 2026**, verified in the release body and press-release index; within the requested period.
- URL: https://ir.tesla.com/press-release/tesla-third-quarter-2026-production-deliveries-and-deployments
- Accessed: November 14, 2026 (America/New_York); HTTP 200.
- Verified table: Model 3/Y production 457,387 and deliveries 478,237; other-model production 7,004 and deliveries 8,295; total production **464,391** and deliveries **486,532**. The introductory text reports **13.7 GWh** of energy storage deployments.
- Source excerpt: “In the third quarter, we produced over 464,000 vehicles, delivered over 486,000 vehicles and deployed 13.7 GWh of energy storage products.” Exact vehicle totals come from the accompanying table, not from treating the rounded introductory values as exact.
- The release cautions that vehicle deliveries and storage deployments should not be relied on as indicators of quarterly financial results, which also depend on selling prices, cost of sales, foreign exchange, and other factors.
- CFO relevance (**interpretation**): use volumes to frame revenue and working-capital questions; do not infer profit or cash generation from volume alone.

## N2 — financial-reporting development

- Title: **Tesla Releases Third Quarter 2026 Financial Results**.
- Publisher: Tesla Investor Relations; page marked Business Wire.
- Publication date: **October 21, 2026**, verified in the release body and press-release index; within the requested period.
- URL: https://ir.tesla.com/press-release/tesla-releases-third-quarter-2026-financial-results
- Accessed: November 14, 2026 (America/New_York); HTTP 200.
- Source excerpt: “Tesla has released its financial results for the third quarter of 2026 by posting an update on its Investor Relations website.”
- The advisory also announces a management results/outlook webcast. The verified development is the release of financial results, not a claim that particular financial metrics improved or deteriorated.
- CFO relevance (**interpretation**): the release is a checkpoint for reconciling operating volumes with earnings and cash flow. The advisory contains no revenue, margin, or cash-flow figures. The underlying shareholder deck and SEC filing were not reviewed for this briefing; no claims about those figures are made.
- N1 and N2 are two separate dated developments from the same company publisher. They are primary disclosures, not independent news reporting. Neither establishes that the development caused a share-price change.

## Calculation and coverage checks

- Observation count: **64**; unique, ascending trading dates; no null or nonpositive closes.
- Actual first session: **August 3, 2026**; starting close: **$322.08**.
- Actual last session: **October 30, 2026**; ending close: **$399.81**.
- August 1 and October 31 were Saturdays. The dates match all weekdays in the requested window excluding September 7 (Labor Day). Both providers returned the same complete date set.
- Dollar change: **$77.73**.
- Python Decimal result: **24.13375558867362146050670640%**, reported as **24.13%**.
- This is endpoint price appreciation, not total shareholder return or a benchmark-relative performance calculation.

The actual calculation read the saved CSV and used this logic:

```python
import csv
from decimal import Decimal

with open('prices-used.csv', newline='') as file:
    rows = list(csv.DictReader(file))
assert len(rows) == 64
assert rows[0]['date'] == '2026-08-03'
assert rows[-1]['date'] == '2026-10-30'
start = Decimal(rows[0]['close_usd'])
end = Decimal(rows[-1]['close_usd'])
percentage_change = (end / start - Decimal(1)) * Decimal(100)
print(percentage_change)  # 24.13375558867362146050670640
```

## Chart and briefing checks

- `tesla-price-chart.png` was generated with Matplotlib directly from the 64 saved CSV rows. Each observation has a marker; lines connect actual observations without adding synthetic observations.
- Title: Tesla (NASDAQ: TSLA) — Daily Closing Prices; subtitle states the requested period and 64 observations.
- X-axis: Trading date (2026). Y-axis: Closing price (USD). Endpoint annotations show dates and prices; source and cross-check are labeled.
- `brief-template.md` uses all ten headings specified by the user, in the requested order.
- `brief-v1.md` follows those ten headings, cites both dated developments, includes two explicit limitations, and separates interpretation from reported facts. It makes no causal attribution or buy/sell recommendation.
- Word count: **351**, including title, headings, and visible source labels, after replacing Markdown links with their visible text and removing formatting markers. Raw Markdown whitespace count: **362**. Both are within 300–400 words.

## Errors encountered and corrections

- Reuters company-coverage request (`https://www.reuters.com/markets/companies/TSLA.O/`) returned **HTTP 401 Unauthorized**. No Reuters content was used; Tesla's accessible primary releases supplied both dated developments.
- The first strict cross-check assertion failed because Nasdaq's September 3 quote had three decimals. Inspection isolated that one precision difference. Applying the explicitly stated half-up cent convention to both providers resolved the discrepancy; the failed raw comparison was not ignored or described as passing.
- Matplotlib initially could not write its default home-directory cache and emitted a Fontconfig cache warning. Set `MPLCONFIGDIR=/tmp/lab3-matplotlib` and `XDG_CACHE_HOME=/tmp/lab3-cache`, then successfully generated the PNG.
- The chart's initial layout crowded the footer against the x-axis label. Adjusted the margins and visually rechecked the final chart.
- No price-data access blocker remains. Stooq was not needed for this run; the preceding network test reached it but received a JavaScript verification page rather than CSV.

## Actual tool-call example and next action

- Tool: `exec_command`, running Python in `Lab3_Business_Agent` with the writable Matplotlib cache settings above.
- Calls: `urllib.request.urlopen(Request(url, headers={'User-Agent': 'Mozilla/5.0', 'Accept': 'application/json'}), timeout=30)` against P1 and P2, followed by CSV parsing, Decimal arithmetic, cross-check assertions, and chart generation.
- Actual successful result: `observations: 64`, `first_date: 2026-08-03`, `first_close_usd: 322.08`, `last_date: 2026-10-30`, `last_close_usd: 399.81`, `percentage_change_rounded: 24.13`, `nasdaq_matching_closes_at_cent_precision: 64`, `chart_points: 64`, `protected_files_unchanged: true`.
- Next action: opened the PNG for visual inspection, corrected its spacing, and wrote the template and CFO briefing using the verified releases and calculations.

## Protected files

The following SHA-256 hashes are retained for the final unchanged-file check:

- `brief-v0.md`: `7b620048b6b42b9ad89a78c262fb80f1b21a022b7c76d18e747dc9813ccae4f2`
- `AGENTS.md`: `66a3080616e984034d45872e644149a1cf5331ca33d4928f3908730515eaf61c`
- `AGENTS-v1.md`: `66a3080616e984034d45872e644149a1cf5331ca33d4928f3908730515eaf61c`
