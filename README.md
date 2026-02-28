# Basis Flux

**FLUX Cost Basis Calculator** — A zero-dependency, static web tool for tracking transactions, calculating USD cost basis, and monitoring FluxNode rewards on the [Flux](https://runonflux.com) blockchain.

🌐 **Live:** [flux.clawpurse.ai](https://flux.clawpurse.ai)  
📦 Part of the [ClawPurse](https://github.com/mhue-ai/ClawPurse) ecosystem.

---

## Features

### Cost Basis Tracking
- **Four IRS-compliant cost basis methods** — FIFO (IRS default), LIFO, HIFO (tax-optimized), and Average Cost with instant switching
- **Bundled historical prices** — 365+ daily FLUX/USD prices (no API calls for lookups)
- **Manual override** — Edit the per-token cost on any transaction row
- **Real-time stats** — Balance, cost basis, current value, unrealized P&L

### Transaction Classification
- **Coinbase tx parsing** — Coinbase transactions are classified by reward tier (Cumulus, Nimbus, Stratus)
- **UTXO analysis** — Inflows and outflows computed from transaction inputs/outputs
- **Filter chips** — Toggle visibility by category (Transfers, Node Rewards, Mining, Coinbase)

### FluxNode Rewards
- **Tier detection** — Heuristic classification of Cumulus (☁️), Nimbus (🌤️), Stratus (⛈️) rewards
- **Reward reference table** — Per-block distribution by tier
- **Collateral info** — Required FLUX collateral per tier

### Other
- **CSV export** — Download full transaction history with cost basis data
- **Deep-linkable** — Share via `?address=t1...` URL parameter
- **Zero dependencies** — Single-file HTML/CSS/JS, no build step, no backend, no framework
- **Responsive** — Works on desktop and mobile
- **Tax form generation** — Form 8949 + Schedule 1 for dispositions and ordinary income

---

## Files

| File | Purpose |
|------|---------|
| `index.html` | Complete application — HTML, CSS, and all JavaScript in a single file |
| `prices.js` | Static FLUX/USD daily price history (~365 days) |
| `CNAME` | GitHub Pages custom domain → `flux.clawpurse.ai` |
| `LICENSE` | MIT License |
| `README.md` | This file |
| `SYSTEM.md` | Internal development reference |
| `sitemap.xml` | SEO sitemap |

---

## How It Works

1. Enter a `t1...` or `t3...` Flux wallet address (or use the URL parameter)
2. The app fetches from the Flux Insight API (`explorer.runonflux.io/api`):
   - Address balance
   - Transaction history (paginated, all pages)
3. Each transaction's inputs/outputs are analyzed to determine direction and amount
4. Coinbase transactions are classified by tier based on reward amount
5. Historical prices are matched from bundled `prices.js`
6. Select your cost basis method (FIFO, LIFO, HIFO, or Average Cost)
7. View cost basis, current value, and unrealized gain/loss

---

## Technical Details

| Property | Value |
|----------|-------|
| **Chain** | Flux (Zcash fork, UTXO-based) |
| **Consensus** | Proof of Node (PoN v2 / PoUW) |
| **Explorer API** | `https://explorer.runonflux.io/api` (Insight API) |
| **Denomination** | FLUX (8 decimals) |
| **Address prefix** | `t1` (transparent) / `t3` (multisig) |
| **Block time** | ~30 seconds |
| **Block reward** | 14 FLUX (Cumulus 1.0, Nimbus 3.5, Stratus 9.0, Foundation 0.5) |
| **CoinGecko ID** | `zelcash` |
| **Explorer** | [explorer.runonflux.io](https://explorer.runonflux.io) |
| **Hosting** | GitHub Pages behind Cloudflare |
| **Custom domain** | `flux.clawpurse.ai` (CNAME) |

---

## FluxNode Tiers

| Tier | Collateral | Per Block | Hardware |
|------|-----------|-----------|----------|
| ☁️ Cumulus | 1,000 FLUX | 1.0 FLUX (shared) | 2 vCPU, 4 GB RAM, 220 GB SSD |
| 🌤️ Nimbus | 12,500 FLUX | 3.5 FLUX (shared) | 4 vCPU, 8 GB RAM, 440 GB SSD |
| ⛈️ Stratus | 40,000 FLUX | 9.0 FLUX (shared) | 8 vCPU, 32 GB RAM, 880 GB SSD |

> 0.5 FLUX per block goes to the Flux Foundation.

---

## Price Data Sources

| Date Range | Source | Notes |
|------------|--------|-------|
| Last 365 days | CoinGecko (aggregated daily) | Multi-exchange average |
| Current price | CoinGecko live API | Fetched on page load |

### Updating `prices.js`

```bash
curl -s "https://api.coingecko.com/api/v3/coins/zelcash/market_chart?vs_currency=usd&days=365"
```

---

## Deployment

This is a static site hosted on GitHub Pages:

1. All files are in the repo root (`index.html`, `prices.js`, `CNAME`)
2. GitHub Pages deploys from the `main` branch root
3. Cloudflare DNS points `flux.clawpurse.ai` to GitHub Pages
4. No build step required — push to `main` and it deploys

---

## IRS Tax Compliance

| Rule | Reference | Implementation |
|------|-----------|---------------|
| Crypto treated as property | Notice 2014-21 | All acquisitions and dispositions tracked with FMV |
| Mining/node rewards = ordinary income at FMV | Rev. Rul. 2023-14 | FluxNode rewards classified as ordinary income |
| Per-wallet cost basis tracking | Rev. Proc. 2024-28 | Single-wallet tracker (compliant by design) |
| FIFO is default if no election | IRS guidance | FIFO is the default method; LIFO, HIFO, Avg Cost available |

---

## Limitations

- **UTXO complexity** — Some multi-input/multi-output transactions may not classify perfectly
- **Historical prices** — Data starts ~365 days back from generation date. Earlier transactions won't have automatic pricing.
- **Tier detection** — Heuristic-based; may misclassify unusual reward amounts
- **No shielded txs** — Only tracks transparent (t-address) transactions
- **Read-only** — This tool never touches private keys or submits transactions

---

## Disclaimer

This tool is for informational purposes only and is not financial or tax advice. Always verify data independently and consult a tax professional for your specific situation.

## License

MIT — Copyright (c) 2026 Mhue AI
