# Base Chain Analytics Dashboard (Ready-to-run)

This repository contains a **backend** (Node.js/Express) and a **frontend** (React) starter project to analyze Base chain meme coins and wallets, with charts for volume and wallet analytics.

## Features 
- Fetch top meme coins (CoinGecko) with sparkline and 24h/7d/30d changes
- Fetch recent transactions for a wallet (BaseScan)
- Compute wallet token balances from tokentx (BaseScan) and approximate USD values using CoinGecko price data
- Charts for coin trends and wallet value (Recharts)
- Ready to deploy to Vercel / Netlify / Render

## Setup

### Backend
1. `cd backend`
2. Copy `.env.example` to `.env` and fill your `BASESCAN_KEY`. Optionally set `COINGECKO_PLATFORM` (default: base).
3. `npm install`
4. `npm start` (or `npm run dev` if you have nodemon)

Endpoints:
- `GET /api/memecoins` -> top meme coins
- `GET /api/transactions/:address` -> recent txs
- `GET /api/wallet/:address` -> aggregated token balances + approximate USD values

### Frontend
1. `cd frontend`
2. `npm install`
3. Optionally set `REACT_APP_API_URL` to point to your backend (default `http://localhost:4000`)
4. `npm start`

## Notes & Limitations
- Wallet USD changes are approximated using CoinGecko percent-change fields. For precise historical balances you'd need onchain snapshots or a historical price-by-timestamp service.
- CoinGecko's token-price endpoints require the correct `platform` (e.g., `ethereum`, `base`). If prices return null, try changing `COINGECKO_PLATFORM` in the backend `.env`.
- BaseScan API rate limits may apply. Consider caching results in a DB for production.

## Deploy
- Backend: Render / Railway / Heroku
- Frontend: Vercel / Netlify
- After deploying backend, set `REACT_APP_API_URL` to the backend URL in your frontend deployment.


## Docker & docker-compose

You can run both services locally with Docker Compose:

```
docker-compose build
export BASESCAN_KEY=your_basescan_key_here
docker-compose up
```

- Backend will be available at `http://localhost:4000`
- Frontend will be served at `http://localhost:3000` (nginx)

The backend uses a small SQLite cache at `backend/cache.db` (persisted by docker-compose as a bind mount).

## GitHub Actions (CI)

Two example workflows are included in `.github/workflows/`:

- `backend-build-push.yml` — builds the backend and pushes a Docker image to Docker Hub.
  - Set secrets `DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN` in your repository settings.
  - The workflow tags the image as `${{ secrets.DOCKERHUB_USERNAME }}/base-analytics-backend:latest`.

- `frontend-build.yml` — builds the frontend and uploads the static `build/` as an artifact.
  - You can extend this workflow to deploy to GitHub Pages, Netlify, or Vercel using additional steps/permissions.

## SQLite cache details

- The backend caches CoinGecko / memecoin responses and token price lookups in `backend/cache.db`.
- Default TTLs:
  - Memecoin list: 60 seconds
  - Token price chunk responses: 300 seconds
- You can change TTLs or disable caching by editing `index.js`.

