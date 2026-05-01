# Severity Rubric — Indian Stock Monitor

The skill MUST stay silent unless a Sev-1 trigger is hit. Sev-2/3 only land in the optional dashboard. Sev-1 = "the user should see this within hours."

## Sev-1 (Notify the user immediately)

### Governance / Trust collapse signals
- Auditor resignation (any reason, especially mid-cycle)
- SEBI / ED / CBI / Income-Tax raid, investigation, or order naming the company or promoter
- Forensic audit ordered
- Resignation of CEO/MD or CFO without named successor
- CEO + CFO exit within 90 days of each other
- Independent director resignation citing governance concerns
- Whistleblower allegations reported by reputable media
- Restatement of past financials

### Surveillance / regulatory red flags
- Stock entered GSM (Graded Surveillance Measure) Stage 2+
- Stock entered ASM Long-term framework
- Stock moved to Trade-to-Trade (T2T) segment
- Trading suspended or circuit limit reduced
- SEBI takeover regulation breach disclosure

### Capital structure / solvency stress
- Promoter pledge increased by >5 percentage points QoQ
- Promoter pledge crossed 50% of holding
- Credit rating downgrade by 2+ notches OR to below investment grade (BBB- and below)
- Negative outlook → Watch with negative implication
- Default or delay on debt servicing reported
- Debt-to-equity jump >30% QoQ in latest results

### Earnings shock (when results are out)
- Revenue or PAT >25% below consensus (use Trendlyne / Bloomberg consensus where available, else flag as "missed estimates significantly")
- EBITDA margin compression >300 bps QoQ without one-off explanation
- FY guidance cut by >15% on revenue OR margin
- Working capital days deteriorated >20% QoQ
- Receivables / inventory growing materially faster than revenue (3-quarter trend)
- Going-concern note added by auditors

### Concall delta signals (Sev-1 if 2+ hit, Sev-2 if 1)
- Management explicitly mentions demand weakness in core segment
- Capex plan cut materially
- Order book / pipeline declining QoQ for 2nd consecutive quarter
- Tone shift on guidance: from "confident" to "cautious / watching"
- Pricing pressure called out as new

### Litigation / business shock
- Adverse SC / HC ruling with material financial impact
- Loss of a top-3 customer / contract / regulatory licence
- Plant shutdown lasting >7 days
- Cyber breach disclosed
- Major product recall

### Sector-wide regulatory shocks
- Government ban or sharp duty change on the company's primary product
- RBI action against a bank/NBFC in the holding (PCA, lending restrictions)
- GST rate change with direct revenue line impact

### Market-microstructure short-term triggers (because user is short-term)
- Block / bulk deal: promoter or 5%+ shareholder sold ≥1% of equity
- Single-day fall ≥7% on volumes >3x 20-day average
- 52-week low breached on >2x volume
- FII holding cut by >2 percentage points in latest disclosure

## Sev-2 (Show in dashboard, do NOT notify)
- Brokerage downgrade (note in dashboard with target price change)
- Single concall warning sign
- Peer earnings miss / guidance cut (industry signal)
- Mutual fund net selling >0.5% of equity in latest month
- Single block deal 0.5–1% of equity by non-promoter
- 200-DMA breakdown
- Sector deratings

## Sev-3 (Log only, no surface)
- Routine corporate actions (dividend, AGM, board meeting notice for routine items)
- Minor management changes (below KMP)
- Regular brokerage initiation reports
- Macro news without direct sector mapping

## Decision rule
Notify the user IF AND ONLY IF: at least one Sev-1 trigger fires for a held stock, OR at least 2 Sev-1 triggers fire across peers in the same sector within 7 days (industry-wide stress signal).

When in doubt, default to silence. False positives erode trust faster than missed Sev-2 signals.
