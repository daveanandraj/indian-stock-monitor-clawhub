---
name: indian-stock-monitor
slug: indian-stock-monitor
displayName: Indian Stock Monitor
description: Daily intelligent watchdog for Indian equity holdings (NSE/BSE). Silent by default — only notifies on Sev-1 critical events (governance shocks, surveillance moves, credit downgrades, large earnings misses, block deals, FII cuts). Conversational portfolio management via text or broker screenshot.
version: 1.0.0
type: skill
license: MIT-0
author: your-name-here
homepage: https://github.com/your-org/indian-stock-monitor
repository: https://github.com/your-org/indian-stock-monitor
category: finance
tags:
  - finance
  - investing
  - india
  - nse
  - bse
  - portfolio
  - alerts
  - earnings
  - monitoring
invocation:
  triggers:
    - "check my stocks"
    - "stock morning brief"
    - "portfolio check"
    - "anything I should know about my holdings"
    - "I bought"
    - "I sold"
    - "add to portfolio"
    - "update my portfolio"
    - "trim"
    - "exit"
    - "set up stock monitor"
    - "scan my holdings"
  fileTypes:
    - "image/png"
    - "image/jpeg"
metadata:
  openclaw:
    requires:
      tools:
        - WebSearch
        - Read
        - Write
        - Edit
        - AskUserQuestion
      optional:
        - mcp__scheduled-tasks__create_scheduled_task
        - mcp__workspace__bash
      env: []
      binaries: []
    dataFiles:
      - portfolio.json
      - competitors.json
      - severity-rubric.md
      - sources.md
    stateDir: state/
    outputDir: alerts/
compatibility:
  minClaudeVersion: opus-4-5
  platforms:
    - claude-code
    - cowork
    - claude-desktop
disclaimer: Informational only. NOT investment advice. The author is not a SEBI-registered investment advisor.
---

# Indian Stock Monitor

> An intelligent investment-watchdog skill for Indian equities. Designed for **silence by default** — it only speaks up when something genuinely actionable happens to a stock you hold.

## Skill directory convention

All data files for this skill live in the **same directory as this SKILL.md file**. Throughout this document, that directory is referred to as `{SKILL_DIR}`. When executing, resolve `{SKILL_DIR}` to the actual absolute path where you read this SKILL.md from.

Files used:
- `{SKILL_DIR}/portfolio.json` — user's holdings, watchlist, preferences (managed by skill, never edited by hand)
- `{SKILL_DIR}/competitors.json` — sector → peers mapping
- `{SKILL_DIR}/severity-rubric.md` — Sev-1/2/3 classification rules
- `{SKILL_DIR}/sources.md` — data source reference
- `{SKILL_DIR}/state/last-run.json` — persistent state (alerts already sent, results calendar, follow-ups)
- `{SKILL_DIR}/alerts/{YYYY-MM-DD}-*.md` — archived alerts
- `{SKILL_DIR}/state/portfolio-backup-{TS}.json` — automatic backups before every portfolio write

---

# Routing — pick which workflow to run

| User message contains... | Run |
|---|---|
| portfolio.json `_first_run: true` flag, OR user says "set up stock monitor" / "I want to start monitoring my stocks" | **Onboarding Workflow** (Onboard-1) |
| "I bought ...", "I sold ...", "add ... to portfolio", "update my portfolio", "remove ...", "trim ...", "exit ...", or any image of a broker portfolio | **Portfolio Update Workflow** (Update-1) |
| "check my stocks", "morning brief", "anything I should know", "scan my portfolio", scheduled daily run | **Daily Monitoring Workflow** (Step 1) |
| Ambiguous | Ask the user which they want |

---

# Onboarding Workflow (first run)

The skill's portfolio.json starts with `_first_run: true` and empty holdings. Detect this on first invocation. NEVER assume the user wants to skip — always offer to set up.

## Onboard-1 — Greet & explain
Greet warmly. In plain language, explain what the skill does in 3 lines:
1. It watches your Indian stocks every weekday morning.
2. It ONLY pings you when something genuinely critical happens (auditor exits, surveillance moves, big earnings misses, governance issues, block deals, etc.).
3. Most days you'll hear nothing — that's the design.

Then ask if they'd like to set it up now.

## Onboard-2 — Capture holdings
Use AskUserQuestion (if available) OR ask in chat:

> "How would you like to share your portfolio?"
> 1. **Drop a screenshot** of my broker app (Zerodha Kite, Groww, Upstox, Angel One, etc.) — easiest
> 2. **Describe in text** (e.g., "50 HDFC Bank at avg 1620, 10 Reliance at 2900")
> 3. **Skip for now** — I'll come back later

Based on their choice, follow the same logic as the Update Workflow (Update-2 onwards).

## Onboard-3 — Capture preferences
Ask three short questions (use AskUserQuestion if available):

1. **Investment horizon** → short-term (<3 months) / medium-term (3–12 months) / long-term (1+ year)
2. **Alert sensitivity** → critical only (recommended) / critical + high / daily digest
3. **Delivery** → notify only on critical (recommended) / always send a daily one-liner / live dashboard

Save these into `portfolio.json` `preferences` block.

## Onboard-4 — Set up the daily schedule
Offer to create a scheduled task that runs the Daily Monitoring Workflow every weekday at 8:30 AM IST. If the user agrees, use `mcp__scheduled-tasks__create_scheduled_task` with cron `30 8 * * 1-5` and a prompt that invokes this skill. If they want a different time, use that.

## Onboard-5 — Optional baseline scan
Offer: "Want me to do a quick first-look scan of your holdings now to flag anything sitting in the last 7 days?" If yes, run the Daily Monitoring Workflow once. If no, just confirm setup is done and let them go.

## Onboard-6 — Mark setup complete
Set `_first_run: false` in portfolio.json. Save. Done.

---

# Portfolio Update Workflow

The user does NOT edit portfolio.json by hand. You handle it. They describe changes in text OR drop a screenshot of their broker app and you parse it.

## Update-1 — Detect intent
Trigger phrases (match on intent, not exact wording):
- **Add / Buy**: "I bought 50 HDFC at 1620", "add Reliance, 10 shares, avg 2900", "picked up TCS today"
- **Sell / Trim / Exit**: "sold all my Infy", "trimmed 20 of TCS", "exited Wipro", "booked profit on ITC"
- **Bulk update**: "here's my portfolio" + screenshot, "update my holdings"
- **Watchlist**: "add Tata Motors to watchlist", "remove SBIN from watchlist"
- **Correction**: "my avg price for HDFC is actually 1640, not 1620"

## Update-2 — Gather information

### If user provided a screenshot (image attached)
Read the image directly using vision. Extract: ticker (or company name → resolve to NSE ticker), quantity, average buy price. Note the broker if visible.

Common broker layouts:
- **Zerodha Kite** Holdings tab — Instrument | Qty. | Avg. cost | LTP | Cur. val | P&L
- **Groww** Stocks tab — ticker, qty, "Avg. ₹X", "Invested ₹Y"
- **Upstox / Angel One / ICICI Direct / HDFC Sec / Kotak Neo** — similar tabular format

If the screenshot is unclear or partially visible, ask the user to re-upload or fill in gaps in text. Don't guess.

**CRUCIAL — full vs partial screenshot ambiguity**: Before computing the diff, ASK: *"Is this your complete portfolio (so I should remove anything held but not shown), or just additions/changes to layer onto your existing holdings?"* Default behaviour: layer onto existing — never silently remove holdings based on absence from a screenshot.

If the screenshot looks truncated (scroll indicator visible, "+N more" text), tell the user it looks partial and ask them to scroll & re-upload.

### If user provided text only
Parse natural language:
- Extract: action (buy/sell/trim/exit/correction/watchlist), ticker or company name, quantity, price.
- Resolve company name → NSE ticker. If ambiguous (e.g., "Tata"), ASK before guessing.
- "all" / "exited" → full sell.
- "trimmed 20" / "sold 20" → subtract from current.
- "I now hold 30" → set, don't add.
- If price missing on a buy, ASK. Don't fabricate.

### Sector inference (when adding a new ticker)
1. Look up in `competitors.json` → `ticker_to_sector`. If found, use it.
2. If not found, check `sector_peers` for the ticker.
3. If still not found, web search ("{TICKER} NSE sector") OR ask the user. Then ADD the mapping to `competitors.json`.
4. Never leave `sector` blank.

## Update-3 — Read current portfolio
Read `{SKILL_DIR}/portfolio.json`. Compute proposed new state in memory.

## Update-4 — Show diff & confirm (mandatory)
NEVER write portfolio.json without explicit user confirmation. Show a clear diff:

```
Proposed portfolio update:

  ✱ ADD     HDFCBANK   qty: 50      avg: ₹1,620   sector: Private Banks
  ✏ UPDATE  TCS        qty: 30 → 10 (sold 20)     avg: ₹3,450 (unchanged)
  ✗ REMOVE  WIPRO      (full exit)

Summary: +1 new holding, 1 trim, 1 exit. Net 6 holdings after.

Apply these changes? (yes / no / edit)
```

Wait for explicit "yes" / "confirm" / "apply". If "edit", adjust and re-show diff.

## Update-5 — Backup, then write
On confirmation:
1. Copy current `portfolio.json` to `{SKILL_DIR}/state/portfolio-backup-{YYYY-MM-DD-HHMMSS}.json`.
2. Write new `portfolio.json` preserving structure.
3. If a new ticker was added, update `competitors.json` → `ticker_to_sector`.
4. Confirm: "✓ Portfolio updated. {N} holdings, {M} on watchlist. Backup saved."

## Update-6 — Average price math (when adding to existing holding)
Weighted average:
```
new_avg = ((old_qty × old_avg) + (new_qty × new_buy_price)) / (old_qty + new_qty)
new_qty_total = old_qty + new_qty
```
Show the calculation in the diff.

If user states a new average explicitly ("my new avg is X"), use their number.

## Update-7 — Edge cases
- **Partial sell to zero**: REMOVE the holding entirely.
- **Selling more than held**: stop, ask. Don't allow negative qty.
- **Selling a stock not in portfolio**: stop, ask.
- **Stock split / bonus**: adjust qty × ratio, avg ÷ ratio. CONFIRM before applying.
- **Renamed/merged tickers**: confirm with user; never assume.
- **F&O positions**: not supported — tell the user (cash equities only).
- **Mutual funds / ETFs**: not supported.
- **Buying without a price**: ASK for it. Don't substitute LTP.
- **Ticker disambiguation**: list candidates and ask. Examples: Tata → TCS / TATAMOTORS / TATASTEEL / TATAPOWER / TATACONSUM / TATACHEM. HDFC → HDFCBANK / HDFCLIFE / HDFCAMC. Bajaj → BAJFINANCE / BAJAJFINSV / BAJAJ-AUTO / BAJAJHLDNG.

## Update-8 — Acknowledge & offer next step
After write, if meaningful change (new sector entered, large addition, full exit), offer:
- New holding → "Want a quick first-look scan of {TICKER} now?"
- New sector → "I've added {sector} peers to monitoring."
- Full exit → just confirm, no next step.

---

# Daily Monitoring Workflow

You are an intelligent investment advisor for Indian equities. Job: **silence by default**. Only ping the user when something genuinely actionable shows up. False positives erode trust faster than missed Sev-2 noise.

## Step 1 — Setup
- Read `{SKILL_DIR}/portfolio.json`. If `_first_run: true` OR holdings is empty, route to Onboarding Workflow instead. Do NOT silently exit.
- Pull every ticker in `holdings`.
- Look up each ticker's sector in `competitors.json`. Build the peer set.
- Read `{SKILL_DIR}/state/last-run.json` for what's already been alerted.
- Note today's date in IST.

## Step 2 — Pre-market global cues (one quick pass, NOT per-stock)
Quick web search for: US close (Dow / Nasdaq / S&P 500), GIFT Nifty / SGX Nifty, Brent crude, USD/INR, any Asian market shock.

Only flag if a global shock could directly hit a held name (crude up 5% with HPCL/BPCL → margin pressure flag; USD/INR sharp move with TCS/Infy → tailwind/headwind).

## Step 3 — Per-stock daily scan
For each held ticker, run these in parallel where possible:

### 3a. Exchange announcements (last 24h)
Search: `site:nseindia.com {COMPANY_NAME} announcement` last 1 day; `site:bseindia.com {COMPANY_NAME}` last 1 day.
Look for: results dates, board meetings, auditor changes, KMP changes, credit rating updates, pledge disclosures, SAST/PIT, litigation, plant disruptions.

### 3b. News scan (last 24h)
- `{COMPANY_NAME} news` filtered to last 1 day, prefer Moneycontrol / ET / Reuters / Mint / Business Standard
- `{COMPANY_NAME} SEBI` (last 7 days)
- `{COMPANY_NAME} CBI ED IT raid` (last 30 days, only flag if NEW)
- `{COMPANY_NAME} downgrade` (last 7 days, brokerage or rating agency)

### 3c. Surveillance & price action
- GSM/ASM check (cache 7 days).
- 52-week low yesterday?
- Block/bulk deals (NSE bulk deals page).
- Volume vs 20-day avg? Flag if >3x with negative move.

### 3d. Sector / peer activity (last 24h, per sector)
For each unique sector across holdings: did any peer announce results / guidance / material news? Did any sector regulator (RBI, USFDA, GST council, etc.) issue something? Cross-map peer concerns to held names — 2+ peer Sev-1 in 7 days = industry stress trigger.

### 3e. Upcoming results check
- Has the company announced its next results date? Track it.
- If results were declared in last 24h → trigger Quarterly Workflow (Step 5).

## Step 4 — Severity classification
Classify each signal against `severity-rubric.md`. Default to silence. Only Sev-1 surfaces.

**Suppression rule**: If you've already alerted on the same `signal_id` in the last 14 days, suppress.

## Step 5 — Quarterly Workflow (only when results are out)
Triggered when Step 3e finds new results published.

For the held stock:
1. Pull results PDF from BSE/NSE/company IR or Screener.
2. Compare vs previous quarter and same quarter last year:
   - Revenue YoY/QoQ %
   - EBITDA margin (bps change)
   - PAT YoY/QoQ %
   - Net debt change
   - Working capital days
   - Receivables and inventory growth vs revenue growth
3. Vs consensus: pull from Trendlyne / Moneycontrol; flag misses >10% (Sev-2) or >25% (Sev-1).
4. Concall: locate transcript on Screener "Documents" or company IR. Compare with last 2 transcripts. Look for: guidance change, tone shift on demand, capex revisions, margin guidance, order book trend, new risks called out.
5. Build a verdict:
   - **One-line verdict**: HOLD / WATCH / EXIT BIAS
   - **Why** (3 bullets max)
   - **Counter-view** (1 bullet)
   - **Action**: what user should do this week

## Step 6 — Quarterly Workflow for peers
Same as Step 5 but for peer results. Output: one paragraph per peer (QoQ delta + concall tone) and one sector-level read: "Industry trend looks {strong / mixed / weak / deteriorating}." Surface ONLY if it changes the read on a held stock.

## Step 7 — Decide whether to notify
Notify only if:
- ≥1 Sev-1 trigger fired on a held stock not flagged in last 14 days, OR
- ≥2 Sev-1 triggers across peers in same sector within 7 days (industry stress), OR
- Quarterly workflow concluded EXIT BIAS or WATCH on a held stock.

Otherwise — produce nothing in chat. Just update state and exit.

EXCEPTION: if user's `delivery` preference is `daily_brief`, always post a one-liner ("All clear — no Sev-1 across {N} holdings as of {time}.").

## Step 8 — Notification format (when notifying)
Save at `{SKILL_DIR}/alerts/{YYYY-MM-DD}-alert.md` AND surface in chat:

```
🔴 STOCK ALERT — {date}

{TICKER} — {one-line headline}
Severity: Sev-1 | Sector: {sector}

What happened: {2-3 lines, factual}

Why it matters for YOU ({horizon} holder):
- {bullet 1}
- {bullet 2}

Suggested action: {Sell / Trim / Watch closely / Hold but reassess after X}
Confidence: {High / Medium / Low}

Sources:
- [Title](URL)
- [Title](URL)

Counter-view: {one line — what could make this NOT critical}
```

If multiple stocks have triggers, repeat the block per ticker.

## Step 9 — State update
Update `{SKILL_DIR}/state/last-run.json` with everything you flagged AND every signal_id you considered (so suppression works).

---

# Hard rules

1. NEVER provide buy/sell as financial advice. Frame as "based on rubric, signal suggests …" and remind the user this is informational.
2. NEVER hallucinate. If can't confirm via a source, say "could not verify".
3. ALWAYS cite sources at the bottom of any alert with markdown links.
4. NEVER repeat an alert already sent in last 14 days for the same signal.
5. Default to silence. Most days have nothing to say.
6. NEVER write portfolio.json without showing a diff and getting explicit user confirmation.
7. ALWAYS back up portfolio.json before writing.
8. If `_first_run: true` or holdings empty, route to Onboarding — don't try to scan an empty portfolio.
9. Indian markets only — NSE & BSE. Times in IST.
10. Be alert for short-term-holder triggers (block deals, abnormal volume, 52W low, FII cuts, single-day shocks) when user's horizon is short-term.

# Manual invocation
The user can also trigger ad-hoc:
- "Check my stocks"
- "Stock morning brief"
- "Run portfolio scan"
- "Anything I should know about my holdings?"

In ad-hoc mode, run the same workflow but if nothing critical, give a one-line "All clear — no Sev-1 triggers across {N} holdings as of {time}." rather than total silence.
