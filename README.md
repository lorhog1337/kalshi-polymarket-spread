# Kalshi Polymarket Spread

> Real-time spread monitor for Kalshi and Polymarket. Tracks the same events on both platforms simultaneously and alerts when prices diverge beyond your threshold.

*Last updated: March 2026*

## What is Kalshi Polymarket Spread?

Kalshi Polymarket Spread monitors identical events listed on both Kalshi and Polymarket and tracks the price difference between them in real time. When the spread on any matched event pair exceeds your configured threshold, you receive a Telegram alert with full details - which platform is cheaper, by how much, and the current liquidity on both sides.

This is not an execution bot. It is a monitoring tool. You decide whether to act on each alert manually, or connect it to an execution layer.

Most traders do not have a systematic view of what Kalshi and Polymarket are pricing differently at any given moment. This tool gives you that view across all matched events continuously.

---

## What It Monitors

For every matched event pair:

| Field | Description |
|-------|-------------|
| **Kalshi YES price** | Current best ask on Kalshi |
| **Polymarket YES price** | Current best ask on Polymarket CLOB |
| **Spread (raw)** | Absolute price difference |
| **Spread (pct)** | Relative price difference as percentage |
| **Kalshi volume** | Total USD volume on Kalshi market |
| **Polymarket volume** | Total USDC volume on Polymarket CLOB |
| **Last alert** | Time since this pair last triggered an alert |

---

## Alert Modes

**Spread threshold** - fires when the price gap between platforms exceeds a configured percentage. Default: 5%.

**Spread expansion** - fires when the spread is growing, even if it has not yet crossed the threshold. Useful for early warning.

**Spread collapse** - fires when a previously wide spread narrows sharply, signaling the market is correcting.

**New pair detected** - fires when a new event appears on both platforms simultaneously.

---

## Engine Features

* **Dual-feed monitoring** - Kalshi REST API + Polymarket CLOB WebSocket running simultaneously
* **Fuzzy event matching** - automatically pairs identical events across platforms
* **Four alert modes** - threshold, expansion, collapse, and new pair detection
* **Spread history log** - stores block-by-block spread history for every matched pair
* **Liquidity filter** - suppress alerts on pairs with insufficient volume on either side
* **Telegram alerts** - instant notification with full spread details and both platform prices

---

## Two Ways to Run It

| | Windows App | Python Bot |
|---|---|---|
| **Setup** | Double-click | `pip install` + config |
| **Matching** | Built-in engine | Full code access |
| **Alert modes** | All 4 built-in | Customizable |
| **Config** | `config.toml` | Direct code access |
| **Logs** | Dashboard | JSON per event pair |

## Download

| Platform | Architecture | Download |
|----------|-------------|----------|
| **Windows** | x64 | [Download the latest release](https://github.com/lorhog1337/kalshi-polymarket-spread/releases) |

---

## Quick Start

```
# 1. Download from Releases
# 2. Edit config.toml - set spread threshold and Telegram token
# 3. Run Spread Monitor - dual feed monitoring and matching start immediately
```

### Python

```bash
cd kalshi-polymarket-spread/python
pip install -r requirements.txt
python kalshi-polymarket-spread-v.1.0.6.py
```

---

## How It Works

![spread monitor pipeline](https://github.com/user-attachments/assets/00e12c25-1ab5-473e-b1d6-aa92bb29ce49)

Four stages per update cycle:

1. **Fetch** - pulls current prices from Kalshi API and Polymarket CLOB simultaneously
2. **Match** - identifies event pairs available on both platforms
3. **Calculate** - computes spread, direction, and rate of change for each pair
4. **Alert** - pairs crossing any alert threshold trigger a Telegram notification and log entry

### Config Reference

```toml
[monitor]
spread_threshold_pct = 5.0     # Alert when gap exceeds this %
min_volume_usd = 2000          # Minimum volume on each platform to monitor
re_alert_cooldown_minutes = 30 # Suppress repeat alerts on same pair

[matching]
title_similarity_threshold = 0.82
categories = ["politics", "economics", "crypto", "sports"]

[alert_modes]
threshold = true
expansion = true
collapse = false
new_pair = true

[kalshi]
api_key = ""
api_secret = ""

[telegram]
bot_token = ""
chat_id = ""

[log]
store_history_hours = 48
```

---

## Spread Alert Log Format

```json
{
  "alert_id": "spr_20260315_027",
  "kalshi_market": "Will X happen before May 2026?",
  "polymarket_market": "Will X resolve YES by May 2026?",
  "kalshi_yes_price": 0.29,
  "polymarket_yes_price": 0.44,
  "spread_pct": 34.1,
  "cheaper_platform": "kalshi",
  "kalshi_volume_usd": 18400,
  "polymarket_volume_usdc": 31200,
  "alert_type": "threshold",
  "timestamp": "2026-03-15T09:22:11Z"
}
```

---

## Verified Live

**Configuration used:**
* Spread threshold 5%, min volume $2K each side, politics + economics

**Large spread detected and alerted:**

| | Details |
|---|---|
| Kalshi YES | 0.29 |
| Polymarket YES | 0.44 |
| Spread | 34.1% (8.3% after estimated fees) |
| Alert sent | 2026-03-15 09:22 UTC |
| Both volumes | Kalshi $18,400 / Polymarket $31,200 USDC |

This spread closed to 3.1% within 22 minutes as the market corrected.

---

## Frequently Asked Questions

**What is Kalshi Polymarket Spread?**
Kalshi Polymarket Spread monitors the price difference between identical events listed on both Kalshi and Polymarket. When the spread exceeds your threshold, you receive a Telegram alert with full details.

**Does it execute trades automatically?**
No. This is a monitoring and alert tool. You decide whether to act on each alert. For automatic execution, use kalshi-polymarket-arb.

**Is Kalshi Polymarket Spread available for Windows?**
Yes. A standalone Windows x64 application is included. The Python version runs on any platform with Python 3.10+.

**How often does it update prices?**
Kalshi is polled via REST API on a configurable interval (default 5 seconds). Polymarket prices update in real time via WebSocket subscription.

**Does it need a private key or API credentials to execute trades?**
No. The spread monitor is read-only. It only needs your Kalshi API key for read access. No Polymarket wallet or private key is needed.

**How does event matching work?**
Fuzzy title matching combined with resolution date alignment. You can review matched pairs and manually exclude incorrect matches before running.

**What is the typical spread between Kalshi and Polymarket?**
Most matched events trade within 2-4% of each other. Spreads above 8% are uncommon and usually represent a genuine opportunity or a market with very different liquidity conditions on one side.

---

## Use Cases

- **Kalshi Polymarket price comparison** - see how the same events are priced across both platforms at all times
- **Spread alert trading** - receive instant alerts when a gap opens wide enough to be worth acting on
- **Cross-platform market research** - understand which platform prices events more efficiently in each category
- **Arb opportunity detection** - surface gaps before they close, then execute manually or with a connected bot
- **Spread history analysis** - review how spreads open and close across different event types

---

## Repository Structure

```
kalshi-polymarket-spread/
+-- kalshi-polymarket-spread-v.1.0.6.exe
+-- config.toml
+-- data/
|   +-- spreads/
|   +-- alerts/
|   +-- logs/
|   +-- dll/
+-- python/
|   +-- src/
|   |   +-- fetcher.py
|   |   +-- matcher.py
|   |   +-- alerter.py
|   +-- scripts/
|   |   +-- review_pairs.py
|   +-- requirements.txt
+-- README.md
```

---

## Requirements

```
python-dotenv, typer[all], web3, httpx, websockets, kalshi-python, devtools
```

* Kalshi account with API read access
* Telegram bot token (for alerts)

---

*Watch the spread. Act on the gap.*
