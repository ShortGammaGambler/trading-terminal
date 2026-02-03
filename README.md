# Trading Terminal v4.0

Derivatives trading terminal with live Polygon.io data, 3D volatility surface, and options strategy engine.

**Live demo:** https://shortgammagambler.github.io/trading-terminal/

---

## Features

- Real-time quotes and options chains (SPY, SPX, QQQ, IWM, VIX, ES)
- 3D implied volatility surface visualization (Three.js)
- IV term structure charting
- Options strategy P&L engine
- Mobile-responsive layout
- Three data modes: **Demo**, **Polygon.io live**, and **Hybrid** (Polygon + backend)

## Data Modes

| Mode | How it works |
|------|-------------|
| **Demo** | Simulated data, no API key needed. Default when visiting the GitHub Pages site. |
| **Polygon.io** | Enter your free [Polygon.io](https://polygon.io/) API key in the settings bar. Key is saved in your browser's localStorage. Free tier: 5 req/min. |
| **Hybrid** | Polygon.io for quotes + the Flask backend for options/IV data via yfinance. Activates automatically when both an API key is set and the backend is detected. |

---

## Quick Start (Frontend Only)

Just open `index.html` in a browser, or visit the GitHub Pages URL above. It starts in demo mode with simulated data. Enter a Polygon.io API key in the settings bar to switch to live data.

## Running the Backend Locally

The Flask backend provides options chains, IV surface, and term structure data via yfinance. The frontend auto-detects it at `http://localhost:5000`.

### Prerequisites

- Python 3.8+

### Steps

```bash
# 1. Clone the repo
git clone https://github.com/ShortGammaGambler/trading-terminal.git
cd trading-terminal

# 2. Install dependencies
pip install -r requirements.txt

# 3. Start the backend
python trading-terminal-backend.py
```

The backend starts on port 5000. Open `index.html` (or the GitHub Pages URL) and the frontend will detect the backend automatically — the status indicator in the settings bar will turn green.

### Backend API Endpoints

| Endpoint | Description |
|----------|-------------|
| `GET /api/health` | Health check |
| `GET /api/quote/<ticker>` | Real-time quote via yfinance |
| `GET /api/options/<ticker>` | Full options chain (first 4 expirations) |
| `GET /api/iv-surface/<ticker>` | IV surface data (0.8-1.2 moneyness, 6 expirations) |
| `GET /api/term-structure/<ticker>` | ATM IV across expirations mapped to standard tenors |

Supported tickers: `SPY`, `SPX` (^GSPC), `QQQ`, `IWM`, `VIX` (^VIX), `ES` (ES=F)

---

## How This Repo Was Set Up

For reference, these are the exact steps used to create this repository and enable GitHub Pages:

```bash
# 1. Create the GitHub repo
gh repo create trading-terminal --public \
  --description "Derivatives trading terminal with live Polygon.io data, 3D vol surface, and options strategy engine"

# 2. Set up local repo
mkdir trading-terminal
cp index.html trading-terminal-backend.py requirements.txt trading-terminal/
cd trading-terminal
git init
git add index.html trading-terminal-backend.py requirements.txt
git commit -m "Initial commit: trading terminal v4.0 with live data + mobile support"

# 3. Push to GitHub
git remote add origin https://github.com/ShortGammaGambler/trading-terminal.git
git branch -M main
git push -u origin main

# 4. Enable GitHub Pages (serves index.html from main branch root)
gh api repos/ShortGammaGambler/trading-terminal/pages \
  -X POST -f source[branch]=main -f source[path]=/
```

After step 4, the site is live at: `https://shortgammagambler.github.io/trading-terminal/`

---

## Production Deployment

To run the full stack (frontend + backend) on a server:

1. **Backend** -- Run `trading-terminal-backend.py` behind a reverse proxy (nginx/caddy). Set `host='0.0.0.0'` and `debug=False` in the Flask `app.run()` call for production.

2. **Frontend** -- Serve `index.html` from any static host (GitHub Pages, Netlify, S3, etc.). Update the `backendUrl` in the JavaScript config if your backend isn't at `localhost:5000`:
   ```javascript
   // Line ~870 in index.html
   backendUrl: 'https://your-server.com',
   ```

3. **API key** -- Each visitor enters their own Polygon.io key in the browser. Nothing is sent to your server; it stays in localStorage.

## License

MIT
