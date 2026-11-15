# Apple source notes — August 1–October 31, 2026

Prepared/accessed November 14, 2026, America/New_York. Successful price retrieval completed at 21:34 EST. All news below was accessed on the same local date. Apple supporting evidence is stored in this subfolder so the existing Tesla prices-used.csv and sources-used.md remain unchanged.

## P1: Yahoo Finance daily closing prices

- Publisher: Yahoo Finance. Historical page: https://finance.yahoo.com/quote/AAPL/history/
- Exact retrieved URL: https://query1.finance.yahoo.com/v8/finance/chart/AAPL?period1=1785542400&period2=1793491200&interval=1d
- HTTP 200; chart error null; symbol AAPL; currency USD; exchange timezone America/New_York.
- Publication dates: the individual trading dates in prices-used.csv; the dynamic endpoint has no single article publication date.
- Parsed timestamps and `indicators.quote[0].close`, not adjusted-close values. Converted dates to America/New_York and filtered inclusively to the requested window. No null or nonpositive retained prices.
- Convention: unadjusted daily closing price in USD, `Decimal` rounded half up to cents. Raw JSON floating-point representation is normalized to cents. Each saved row includes date, ticker, close, price convention, and the exact source URL.
- Saved 64 unique chronologically ordered observations to this folder's `prices-used.csv`.

## P2: Nasdaq cross-check

- Publisher: Nasdaq. Historical page: https://www.nasdaq.com/market-activity/stocks/aapl/historical
- Exact retrieved URL: https://api.nasdaq.com/api/quote/AAPL/historical?assetclass=stocks&fromdate=2026-08-01&todate=2026-10-31&limit=100
- HTTP 200; provider status `rCode: 200`; 64 rows. Publication dates are the individual observation dates, not an article date.
- Parsed `data.tradesTable.rows` date and close fields. All 64 date/close pairs match Yahoo after applying the same `ROUND_HALF_UP` cent convention. This is a comparison at cent precision, not a claim of identical raw precision.
- Dates equal all weekdays in the window excluding September 7 (Labor Day). August 1 and October 31 were Saturdays. Actual first session: August 3; last: October 30.

## Price calculations and chart

- First close: August 3, 2026, **$303.42**.
- Last close: October 30, 2026, **$344.63**.
- Dollar change: **$41.21**.
- Python Decimal percentage change: **13.58183376178234790059982860%**, reported as **13.58%**.
- Formula: `(last_close / first_close - 1) * 100`. This is price appreciation, not total shareholder return; no dividend or benchmark adjustment was made.
- Calculation read the saved CSV, using `Decimal` for its starting and ending price. Reproduction from this subfolder:

```python
import csv
from decimal import Decimal
with open('prices-used.csv', newline='') as f:
    rows = list(csv.DictReader(f))
assert len(rows) == 64
start = Decimal(rows[0]['close_usd'])
end = Decimal(rows[-1]['close_usd'])
print((end / start - 1) * 100)
```

- `../apple-price-chart.png` plots all 64 saved observations with markers and connecting lines; no missing-session observations were fabricated or interpolated. Title identifies Apple, AAPL, requested window, and count. X-axis: Trading date (2026); Y-axis: Closing price (USD). Endpoint annotations and source labels are included.

## N1: substantive operating risk — replacement of defective phones

- Title: **Apple To Replace IPhone Duo Units Affected By Display Defects**.
- Author/publisher: RTTNews.com for RTTNews, carried by Nasdaq.
- Publication: **October 27, 2026, 02:09 am EDT**, verified in the retrieved article header and the Nasdaq news index; within the requested window.
- URL: https://www.nasdaq.com/articles/apple-replace-iphone-duo-units-affected-display-defects
- Access: November 14, 2026; HTTP 200; article body read, not merely the search snippet.
- Reported fact: Apple said it would replace affected iPhone Duo devices after inner-display problems were reported soon after deliveries began.
- Saved quotation from the article, attributed there to Apple: “We are aware of an issue impacting the inner display on a small number of iPhone Duo units. If users experience issues with their iPhone Duo display, they should contact AppleCare for a replacement”.
- No unit count, financial provision, replacement cost, or revenue impact was provided. “Small number” is the company's characterization as quoted by the reporter, not a quantified risk assessment.
- CFO relevance (interpretation): monitor warranty reserves, service logistics, customer retention, and cash requirements. No claim of a quantified charge or materiality is supported.

## N2: substantive operating development — product pricing

- Title: **Apple Unveils Its First-Ever Touchscreen MacBook Pro. Should Investors Be Concerned About the $2,899 Price Tag?**
- Author/publisher: **Patrick Sanders for The Motley Fool**, carried by Nasdaq.
- Publication: **October 29, 2026, 12:06 pm EDT**, verified in article header and Nasdaq news index; within the requested window. Article describes the unveiling as Wednesday (October 28); distinguish the publication date from the event date.
- URL: https://www.nasdaq.com/articles/apple-unveils-its-first-ever-touchscreen-macbook-pro-should-investors-be-concerned-about
- Access: November 14, 2026; HTTP 200; full article body read.
- Saved article excerpt: “the 14-inch model starting at $2,899 and the 16-inch version costing $3,399”.
- Saved comparison: the non-touch M5 Pro 14-inch is reported at $2,499 and the 16-inch at $2,999, making both touchscreen premiums $400. The 14-inch percentage premium is `(2899 / 2499 - 1) * 100`, approximately 16%.
- The article's views about recovering memory costs and protecting margins are commentary, not verified realized financial outcomes. The briefing does not present them as established margin improvements or incorporate the article's investment recommendations.
- CFO relevance (interpretation): test premium product mix and demand sensitivity against component cost pressure. Selling-price premiums alone do not determine profitability.

## Discovery, errors, and evidence limits

- Apple newsroom candidate https://www.apple.com/newsroom/2026/10/apple-reports-fourth-quarter-results/ failed during proxy tunnel setup: **403 Forbidden**. No content or publication date from that candidate was verified; it is not used as a source or evidence that Apple reported results within the period.
- Yahoo's AAPL news endpoint was accessible but initially returned November articles. Targeted text queries returned no news results. November articles were excluded from the briefing's two developments.
- Nasdaq press-release candidate API `/api/quote/AAPL/pressreleases?limit=100` returned **404**. Used accessible Nasdaq news instead.
- Discovery index: https://api.nasdaq.com/api/news/topic/articlebysymbol?symbol=AAPL&limit=100&offset=100 . This returned the October articles above; followed their actual links and read their dated full text.
- A separate October 28 RTTNews article about Korean payment restrictions was examined but excluded because it misidentified Google's ticker/parent. No fine figure or assertion from that story is used.
- N1 and N2 are secondary reporting from different publishers carried by the same host. Apple primary disclosures and regulatory filings were not available for independent verification in this run. Reported facts are attributed, and missing costs or realized margins remain explicit limitations.
- No assertion that either development caused an AAPL price movement is made. No buy or sell recommendation is made.
- Existing Tesla data, source notes, chart, briefings, template, and agent instructions were preserved unchanged.
