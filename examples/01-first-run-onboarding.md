# Example 1 — First-run onboarding

A new user installs the skill and triggers it for the first time.

## User
> set up stock monitor

## Claude (using the skill)
> Welcome. Quick context on what this skill does:
>
> 1. It watches your Indian stocks every weekday morning at 8:30 IST.
> 2. It only pings you when something genuinely critical happens — auditor exits, surveillance moves, big earnings misses, governance issues, block deals.
> 3. Most days you'll hear nothing. That's the design.
>
> Want to set it up now? How would you like to share your portfolio?
>
> 1. Drop a screenshot of your broker app (Zerodha Kite, Groww, Upstox, Angel One, etc.) — easiest
> 2. Describe in text (e.g., "50 HDFC Bank at avg 1620, 10 Reliance at 2900")
> 3. Skip for now

## User
> 50 HDFC Bank at avg 1620

## Claude
> ```
> Proposed portfolio update:
>
>   ✱ ADD     HDFCBANK   qty: 50    avg: ₹1,620   sector: Private Banks
>
> Net result: 1 holding.
> Invested value: ₹81,000
> ```
>
> Confirm to apply? (yes / no / edit)

## User
> yes

## Claude
> ✓ Portfolio updated. Backup saved.
>
> Two more questions to finish setup:
>
> 1. Investment horizon: short-term (<3 months) / medium-term (3–12 months) / long-term (1+ year)?
> 2. Set up the daily 8:30 AM IST scan? (recommended)

## User
> short-term, yes

## Claude
> ✓ Preferences saved.
> ✓ Scheduled task `indian-stock-monitor-daily` created — runs Mon–Fri at 8:30 IST.
>
> Want me to do a quick first-look scan now to flag anything sitting in the last 7 days for HDFCBANK? (~30 seconds)
