# Example 2 — Updating the portfolio via broker screenshot

The user just made some trades and drops a fresh Zerodha Kite Holdings screenshot.

## User
> [attaches Zerodha Kite Holdings screenshot]
>
> here's my updated portfolio

## Claude (using the skill)
> Reading the screenshot. I can see Zerodha Kite Holdings with 4 entries. Before I compute the diff:
>
> Is this your **complete portfolio** (so I should remove anything I have on file but not shown), or **just changes** to layer on top of your existing holdings?

## User
> complete portfolio

## Claude
> ```
> Proposed portfolio update:
>
>   ✱ ADD     INFY        qty: 25     avg: ₹1,450    sector: IT Services
>   ✏ UPDATE  HDFCBANK    qty: 50 → 75 (added 25 at ₹1,580)
>                         avg: ₹1,620 → ₹1,606 (weighted)
>   ✗ REMOVE  RELIANCE    (not in screenshot — full exit)
>
> Calculation for HDFCBANK weighted avg:
>   ((50 × 1620) + (25 × 1580)) / 75 = ₹1,606.67 → rounded to ₹1,606
>
> Summary: +1 new, 1 increased, 1 exited. Net 2 holdings after.
> ```
>
> Apply? (yes / no / edit)

## User
> yes

## Claude
> ✓ Portfolio updated.
> ✓ Backup saved: state/portfolio-backup-2026-05-15-093412.json
> ✓ Sectors monitored: Private Banks, IT Services
>
> Want me to scan INFY for anything in the last 7 days now that you've added it?
