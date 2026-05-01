# Indian Stock Monitor — A Claude Skill

A daily intelligent watchdog for your Indian equity holdings (NSE/BSE). Runs every weekday at 8:30 AM IST. Stays silent unless something genuinely critical happens.

> **Built for the busy retail investor.** You give it your portfolio once. It watches the news, regulatory filings, surveillance lists, credit ratings, brokerage actions, block deals, FII flows, peer activity, and earnings every morning. It only pings you when it sees something that looks like an early warning. Most days you'll hear nothing — that's the design.

## What it watches for

The skill classifies every signal it sees against a severity rubric and only surfaces **Sev-1 critical** events. The headline categories:

| Category | Examples |
|---|---|
| **Governance shocks** | Auditor resignation, SEBI/ED/CBI/IT action, forensic audit ordered, sudden CEO/CFO exit, independent director resignation |
| **Surveillance flags** | Stock entered GSM Stage 2+, ASM Long-term, T2T segment, trading suspended |
| **Capital structure stress** | Promoter pledge >5pp jump or >50%, credit rating downgrade ≥2 notches or to below IG, debt servicing default |
| **Earnings shock** | Revenue or PAT >25% below consensus, EBITDA margin compression >300bps, FY guidance cut >15%, going-concern note |
| **Concall red flags** | Demand weakness in core segment, capex cut, declining order book, tone shift on guidance |
| **Litigation / business** | Adverse SC/HC ruling, loss of top-3 customer, plant shutdown >7 days, cyber breach, major recall |
| **Sector regulation** | Government ban / sharp duty change, RBI PCA/lending restrictions, GST rate change with revenue impact |
| **Short-term triggers** | Block deal by promoter ≥1% equity, single-day fall ≥7% on >3x volume, 52-week low on heavy volume, FII cut >2pp |

Full rubric: see `severity-rubric.md`.

## How you use it

You don't edit any JSON. Everything is conversational.

### First time
Just say *"set up stock monitor"* and the skill walks you through:
1. How you want to share your holdings (screenshot of your broker app OR text description)
2. Your investment horizon (short / medium / long term)
3. Alert sensitivity (default: critical-only)
4. Whether to set up the daily 8:30 AM IST scheduled scan (recommended)

### Ongoing
- **Update holdings**: *"I bought 50 HDFC at 1620"*, *"sold all my Wipro"*, *"trimmed 20 of TCS"*, or just drop a fresh broker screenshot
- **Manual scan**: *"check my stocks"*, *"morning brief"*, *"anything I should know about my holdings"*
- **Customise**: *"make alerts less noisy"*, *"switch to a daily brief"*, *"run the scan at 9 AM instead"*

### What gets shown to you
On a Sev-1 hit, you get a structured alert in chat:

```
🔴 STOCK ALERT — 2026-05-01

HDFCBANK — Governance red flag at the top
Severity: Sev-1 | Sector: Private Banks

What happened: [factual 2-3 line summary]

Why it matters for YOU (short-term holder):
- [bullet 1]
- [bullet 2]

Suggested action: WATCH CLOSELY
Confidence: Medium

Sources:
- [Title](URL)

Counter-view: [one line — what could make this NOT critical]
```

On quiet days, **nothing**. Silence is the feature.

## Supported brokers (screenshot extraction)
- Zerodha Kite
- Groww
- Upstox
- Angel One
- ICICI Direct
- HDFC Securities
- Kotak Neo
- Most others — any clear holdings table works

## Out of scope
- F&O / derivatives (cash equities only)
- Mutual funds / ETFs
- US/global stocks
- Direct trade execution

## Install

### Recommended — via ClawHub
```bash
clawhub install your-username/indian-stock-monitor
```
This installs into your `./skills/` directory. Then in Claude, say *"set up stock monitor"*.

### Alternative — manual
Copy this entire folder into:
- `~/.claude/skills/indian-stock-monitor/` (Claude Code user-level)
- Your team plugin's `skills/` directory
- Or any path Claude is configured to read skills from

### After install
Say *"set up stock monitor"* in any Claude conversation. The skill auto-loads, detects first-run state, and walks you through onboarding (see `examples/01-first-run-onboarding.md` for the full flow).

## Publishing your fork to ClawHub
See `PUBLISH.md` for the full step-by-step guide, including the verification checklist to run against the live ClawHub spec before submitting.

## Files in this skill

| File | Purpose |
|---|---|
| `SKILL.md` | The full workflow Claude follows |
| `portfolio.json` | Your holdings (managed by the skill, never edited by hand) |
| `competitors.json` | Sector → peer mapping (used for industry signals) |
| `severity-rubric.md` | Sev-1/2/3 classification rules |
| `sources.md` | Where the skill fetches each kind of data |
| `manifest.json` | Skill metadata for distribution |
| `state/last-run.json` | Tracks alerts already sent (prevents duplicates) |
| `state/portfolio-backup-*.json` | Auto-backups before every portfolio write |
| `alerts/{date}-*.md` | Archive of every alert that fired |
| `LICENSE` | MIT |
| `CHANGELOG.md` | Version history |

## Disclaimer
**This skill is informational only and is NOT investment advice.** It surfaces signals against a transparent rubric you can read and customise. All buy/sell decisions are yours. The author is not a SEBI-registered investment advisor. Stock investments are subject to market risk. Past performance is not indicative of future returns. Read all scheme-related documents carefully before investing.

## License
MIT — see `LICENSE`.

## Contributing
Open an issue or PR if you'd like to add support for more brokers, sectors, or signal types. The severity rubric in particular is designed to be customisable — fork and tune to your own conviction.
