# SYSTEM.md — Basis Flux Internal Development Reference

> Internal documentation for maintainers and AI assistants working on this project.

---

## Quick Reference

| Item | Value |
|------|-------|
| **Live URL** | https://flux.clawpurse.ai |
| **Repo** | https://github.com/mhue-ai/basis-flux |
| **Hosting** | GitHub Pages (main branch root) → Cloudflare |
| **Stack** | Single-file HTML/CSS/JS, zero dependencies |
| **Sister project** | [Basis Timpi](https://github.com/mhue-ai/basis-timpi) (same architecture, Neutaro chain) |

---

## Repository Structure

```
basis-flux/
├── index.html      # Complete app (HTML + embedded CSS + JS)
├── prices.js       # Static FLUX/USD daily prices, loaded via <script>
├── CNAME           # flux.clawpurse.ai
├── LICENSE         # MIT
├── README.md       # Public documentation
├── SYSTEM.md       # This file — internal dev reference
└── sitemap.xml     # SEO sitemap
```

---

## Key Differences from Basis Timpi

| Aspect | Basis Timpi (Neutaro) | Basis Flux |
|--------|-----------------------|------------|
| Chain type | Cosmos SDK (account model) | Zcash fork (UTXO model) |
| API | Neutaro REST (`/cosmos/...`) | Insight API (`explorer.runonflux.io/api/`) |
| Addresses | `neutaro1...` (bech32) | `t1...` / `t3...` (base58) |
| Denomination | uneutaro (6 decimals) | satoshis (8 decimals) |
| Node types | Synaptron, Guardian, Collector, GeoCore | Cumulus, Nimbus, Stratus |
| NFTs | CW721 node access NFTs | None (collateral-based nodes) |
| Staking | Cosmos delegations | Collateral soft-lock |
| Tx memos | Yes (Cosmos tx memo field) | No (UTXO has no memo) |
| Classification | Sender address + memo parsing | Coinbase tx output analysis |

---

## API Endpoints Used

### Balance
```
GET /api/addr/{address}?noTxList=1
→ { addrStr, balance, totalReceived, totalSent, txApperances }
```

### Transactions (paginated)
```
GET /api/txs?address={address}&pageNum={n}
→ { pagesTotal, txs: [{ txid, vin, vout, time, ... }] }
```

### Block info
```
GET /api/blocks?limit=1
GET /api/block/{hash}
```

### Network status
```
GET /api/status
→ { info: { version, blocks, difficulty, reward, ... } }
```

### Currency price (from explorer)
```
GET /api/currency
→ { data: { rate: 0.0616, short: "FLUX" } }
```

---

## Transaction Classification Logic

### UTXO Analysis
Unlike Cosmos (which has typed messages), Flux uses UTXO. Classification:

1. **Check `vin`**: If any input has `coinbase` field → coinbase transaction
2. **Sum inputs from address** = total spent
3. **Sum outputs to address** = total received
4. **Net = received - spent**: positive = inflow, negative = outflow

### Coinbase (Block Reward) Classification
Flux PoN distributes 14 FLUX per block:
- Foundation: 0.5 FLUX
- Cumulus pool: 1.0 FLUX (shared)
- Nimbus pool: 3.5 FLUX (shared)
- Stratus pool: 9.0 FLUX (shared)

Heuristic classification by received amount:
- ≥8.0 FLUX → Stratus
- ≥3.0 and <8.0 → Nimbus
- ≥0.8 and <3.0 → Cumulus
- Other → generic node_reward

---

## Deployment Notes

- **Push to `main`** → GitHub Pages auto-deploys
- **CNAME file** must remain as `flux.clawpurse.ai`
- **No build step** — The HTML file IS the app

### GitHub API Deployment (used in dev sessions)

```bash
# Get current file SHA
SHA=$(curl -s -H "Authorization: token $PAT" \
  "https://api.github.com/repos/mhue-ai/basis-flux/contents/index.html" \
  | jq -r '.sha')

# Base64 encode and push
CONTENT=$(base64 -w 0 index.html)
curl -X PUT -H "Authorization: token $PAT" \
  "https://api.github.com/repos/mhue-ai/basis-flux/contents/index.html" \
  -d "{\"message\":\"commit message\",\"content\":\"$CONTENT\",\"sha\":\"$SHA\"}"
```

---

## Future Improvements

- [ ] Better tier detection using explorer's FluxNode API
- [ ] Parallel asset tracking (FLUX-ETH, FLUX-BSC, FLUX-KDA)
- [ ] Collateral lock/unlock tracking
- [ ] Multi-address support
- [ ] Historical reward rate tracking
- [ ] Price chart visualization
- [ ] Tax year summary reports
- [ ] Integration with Zelcore wallet export

---

## Related Projects

| Project | URL | Description |
|---------|-----|-------------|
| Basis Timpi | [basis.clawpurse.ai](https://basis.clawpurse.ai) | NTMPI cost basis tracker (sister project) |
| ClawPurse | [clawpurse.ai](https://clawpurse.ai) | Main wallet platform |
| ClawPurse Gateway | [gateway.clawpurse.ai](https://gateway.clawpurse.ai) | HTTP 402 micropayment gateway |
| Drip Faucet | [drip.clawpurse.ai](https://drip.clawpurse.ai) | NTMPI token faucet |
| Flux Explorer | [explorer.runonflux.io](https://explorer.runonflux.io) | Chain explorer (source of truth) |
