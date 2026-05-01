# Example 3 — Sev-1 alert fired by the daily scan

The scheduled task runs at 8:30 AM IST. Most days, nothing. This day, something.

## Trigger
Scheduled task `indian-stock-monitor-daily` fires.

## What the skill does (silently)
- Reads portfolio.json — 2 holdings
- Pre-market global cues check — nothing relevant
- Per-stock scan: news, exchange filings, surveillance, peer activity
- Finds: HDFCBANK Chairman resigned + board commissioned external law firm review
- Classifies against severity-rubric.md → Sev-1 governance trigger
- Checks state/last-run.json → not previously alerted
- Surfaces the alert

## What the user sees in chat

> 🔴 STOCK ALERT — 2026-05-01
>
> **HDFCBANK — Governance red flag at the top**
> Severity: Sev-1 | Sector: Private Banks
>
> **What happened:** Atanu Chakraborty, part-time Chairman and Independent Director, resigned 18 March 2026. Keki Mistry was appointed interim Chairman for 3 months. The board has commissioned external law firms (domestic and international) to review the contents of his resignation letter — an unusual move that typically signals material concerns the board needs investigated independently.
>
> **Why it matters for YOU (short-term holder, bought at ₹1,620):**
> - Independent director exit + board-level legal review = early-warning governance signal
> - NIM compression in Q4 FY26 (NII grew only 3.2% YoY) compounds the risk
> - Stock down ~20% YTD; risk-reward has tilted
>
> **Suggested action:** WATCH CLOSELY. Don't add. Triggers to escalate to EXIT BIAS:
> - Adverse findings from law firm review
> - No named permanent chairman by mid-June 2026
> - Further NIM deterioration in Q1 FY27
>
> **Confidence:** Medium-High
>
> **Counter-view:** Resignations sometimes happen for personal/policy reasons. Franchise + asset quality remain intact. Legal review could be precautionary.
>
> **Sources:**
> - [HDFC Bank Q4FY26 Results — Business Standard](https://www.business-standard.com/companies/quarterly-results/hdfc-bank-q4fy26-results-profit-up-9-pc-to-rs-19-221-cr-on-lower-provisions-126041800535_1.html)
> - [HDFC Bank shares drop 20% in 2026 — Upstox](https://upstox.com/news/market-news/stocks/hdfc-bank-shares-drop-20-in-2026-key-updates-to-know-ahead-of-q4-results/article-192270/)

## What happens next
The alert is also archived to `alerts/2026-05-01-alert.md` and `state/last-run.json` is updated so the same signal isn't re-flagged in the next 14 days.
