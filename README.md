# Alpha Arena Scraper (nof1.ai)

This project scrapes visible market data, completed trades, model chat, and positions from `https://nof1.ai/` using Playwright.

## Setup

1. Create a virtual environment and install dependencies:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m playwright install chromium
```

2. Optional: run headed with slow motion to stabilize selectors while you confirm the UI:

```bash
python src/nof1_scraper.py --headed --slow 250
```

## Usage

Run the scraper and save outputs to `data/out` by default:

```bash
python src/nof1_scraper.py --headless --out data/out
```

### Polling for bot training

Run on an interval and keep timestamped snapshots for offline training:

```bash
# Every 60s, run indefinitely, saving to data/out/YYYYMMDD_HHMMSS/
python src/nof1_scraper.py --headless --interval 60 --out data/out

# Run 100 iterations every 30s
python src/nof1_scraper.py --headless --interval 30 --iterations 100 --out data/out
```

### Upload to GitHub (free, convenient)

You can upload each snapshot to a GitHub repository instead of storing locally. Create a repo (private or public), then create a classic PAT with `repo` scope. Export it as an env var (default name `GITHUB_TOKEN`).

```bash
export GITHUB_TOKEN=ghp_yourtokenhere

# Upload one run to owner/repo under path data/nof1
python src/nof1_scraper.py \
  --headless \
  --out data/out \
  --github-repo yourname/yourrepo \
  --github-branch main \
  --github-path data/nof1

# With polling, each timestamped directory is uploaded
python src/nof1_scraper.py \
  --headless --interval 60 --out data/out \
  --github-repo yourname/yourrepo --github-path data/nof1
```

Notes:
- Files must be <100MB each (GitHub limit). CSV/JSON from this scraper are typically small.
- Storage is subject to repo limits; for bigger datasets consider Backblaze B2 (10GB free), Supabase (1GB free storage), or S3 free tier.

Outputs:
- `nof1_dump.json`: all collected data
- `market_data.csv`, `completed_trades.csv`, `model_chat.csv`, `positions.csv`

## Notes
- The site is a dynamic SPA; data may be rendered after websockets/async requests. The scraper adds listeners for JSON responses and websocket frames for troubleshooting.
- If parts of the UI are gated or change, adjust `_click_tab` and the DOM locators.
- This scraper targets content that is publicly visible in the browser. Respect the site’s Terms of Service and robots policies.

## Source
- `nof1.ai` ([link](https://nof1.ai/))
# Alpha_Arena_scrape
