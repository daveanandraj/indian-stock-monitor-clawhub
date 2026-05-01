# Changelog

All notable changes to this skill are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this skill adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] — 2026-05-01

### Added
- Initial release
- Daily monitoring workflow scanning NSE/BSE filings, news, surveillance lists, credit ratings, block deals, FII flows
- Conversational portfolio management — text or broker-screenshot input
- First-run onboarding flow
- Severity rubric (Sev-1 / Sev-2 / Sev-3) with default Sev-1-only notification
- Quarterly workflow with QoQ/YoY/concall delta comparison
- Sector peer monitoring (industry stress trigger when 2+ peers hit Sev-1 in 7 days)
- Auto-backup of portfolio.json on every write
- Suppression of repeat alerts within 14 days
- Support for screenshot extraction from Zerodha Kite, Groww, Upstox, Angel One, ICICI Direct, HDFC Securities, Kotak Neo
- 30+ pre-mapped sectors with peer lists in `competitors.json`
- Optional scheduled task creation (cron `30 8 * * 1-5` IST by default)

### Out of scope (deliberately)
- F&O / derivatives
- Mutual funds / ETFs
- US/global stocks
- Trade execution

## Roadmap (planned)

### [1.1.0] — possible additions
- Trendlyne / Screener API integration if available (currently web-search based)
- Telegram / WhatsApp / email notification adapters
- Live HTML artifact dashboard with auto-refresh
- Concall transcript caching to speed up repeat compares
- Customisable severity weights per sector

### [1.2.0] — under consideration
- Multi-portfolio support (e.g., separate "core" and "tactical" books)
- Tax/STCG/LTCG awareness in trim suggestions
- Indexed peer-relative valuation (P/E, P/B, EV/EBITDA vs sector median)
