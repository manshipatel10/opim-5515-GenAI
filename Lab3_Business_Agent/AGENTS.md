# Business Briefing Agent

## Audience

The briefing is written for a CFO who understands financial concepts but needs a clear, concise summary to support business decisions. Focus on stock performance, important company developments, financial risks, and business implications. Avoid unnecessary technical jargon.

## Workflow

1. Identify the company, ticker symbol, and requested date range. Find real historical daily stock prices from a reliable financial source and record the actual first and last trading days.
2. Save the price observations, calculate the percentage change using code, and generate a labeled price chart showing the available trading-day observations.
3. Find two relevant, dated company news developments within the requested period. Write a 300–400-word business briefing following brief-template.md, then verify the calculations and sources.

## Evidence rules

- Use verifiable financial data and credible news sources. Record source URLs, publication dates, and the date accessed.
- Save the actual price observations in prices-used.csv and source information in sources-used.md.
- Never invent stock prices, news, calculations, or citations. Do not claim that news caused stock movements without evidence.
- If a source is blocked, try another reliable source. If real price data remains unavailable after approximately 15 minutes, document the attempts and request the instructor's sourced price file.
- When selecting news developments for a CFO briefing, prioritize substantive financial or operating information over announcements that merely say a report was published. Include relevant verified figures when available in the saved sources. If the saved evidence does not contain those figures, clearly state the limitation rather than inventing them.

## Checks before finishing

- Verify the first and last trading dates, starting and ending prices, percentage change, and consistent price convention (such as daily closing price in USD).
- Confirm that the chart contains real dated price observations, properly labeled axes, and a descriptive title.
- Check that both news items have dates and sources, that the brief contains two limitations, and that the interpretation is appropriate for a CFO.
- Clearly distinguish verified facts from interpretations and avoid unsupported investment recommendations.

## Outputs

- Save all files inside Lab3_Business_Agent.
- Follow brief-template.md and keep each business briefing between 300 and 400 words.
- Produce prices-used.csv, sources-used.md, brief-v1.md, and a price chart. Use brief-v2.md for the revised Tesla briefing and brief-[company].md for another company.
