---
marp: true
theme: default
size: 16:9
paginate: true
header: 'Flux · Real-time energy intelligence on Bittensor'
footer: 'github.com/edwardtay/bittensor-flux'
style: |
  section {
    font-size: 24px;
    padding: 50px 70px;
    background: #fafafa;
    color: #1a1a1a;
  }
  h1 { color: #0a4d68; font-weight: 700; }
  h2 { color: #0a4d68; font-weight: 600; }
  h3 { color: #088395; }
  table {
    font-size: 0.78em;
    border-collapse: collapse;
    margin: 0.4em 0;
  }
  th {
    background: #0a4d68;
    color: white;
    padding: 6px 10px;
    text-align: left;
  }
  td { padding: 5px 10px; border-bottom: 1px solid #ddd; }
  tr:nth-child(even) td { background: #f0f4f8; }
  code {
    background: #e8eef2;
    color: #0a4d68;
    padding: 1px 5px;
    border-radius: 3px;
    font-size: 0.85em;
  }
  pre {
    background: #1a1a1a;
    color: #e8eef2;
    font-size: 0.7em;
    padding: 14px 18px;
    border-radius: 6px;
    line-height: 1.4;
  }
  pre code { background: transparent; color: #e8eef2; }
  blockquote {
    border-left: 4px solid #088395;
    padding-left: 1em;
    color: #333;
    font-style: italic;
  }
  section.lead {
    text-align: center;
    justify-content: center;
  }
  section.lead h1 { font-size: 3em; }
  header, footer { color: #888; font-size: 0.6em; }
---

<!-- _class: lead -->

# ⚡ Flux

## Real-time energy intelligence on Bittensor

Open-competition forecasting of power prices, load, and commodity flows — scored deterministically against ISO ground truth every ~72 minutes.

---

## 🔥 The Problem

- AI data-center buildout repriced **ERCOT peak load 29 → 77 GW in 18 months**
- Every major ISO (CAISO · PJM · ENTSO-E) is in the same shape
- Hormuz disruptions + climate shocks → **forecast-value spikes by the hour**
- Legacy vendors (Bloomberg NEF · Enverus · Modo) sell **quarterly static valuations** from single proprietary models

> No Bittensor subnet serves real-time energy forecasting.

---

## 🎯 v1: ERCOT day-ahead LMP

| | Why this market |
|---|---|
| 📈 | **AI-DC repricing narrative** — 29 → 77 GW load revision in 18mo |
| 🔓 | **Open public data** — ERCOT API + EIA, validators score for free |
| ⚡ | **Highest LMP volatility** in US — biggest forecast-value ROI |
| 📊 | **Weak naive baseline** — easy first ≥20% skill target to beat |
| 💼 | **Crypto-energy desks already trade ERCOT** — first 20 customers wallet-aligned |

---

## ⛏️ What miners do

Submit **probabilistic forecasts** on ERCOT LMP across 4 horizons:

```python
Forecast(horizon ∈ {5min, 1h, 24h, 168h},
         target_time, mean, p10, p50, p90)
```

- 🔒 **Sealed commit-reveal** — `sha256(forecast ‖ nonce)` before window opens
- 🚫 Miners cannot copy each other before submission
- 🧰 Reference miner = XGBoost on public load + weather + gas features (forkable)

---

## ✅ What validators do

Every tempo (~72 min):

1. 📥 Pull realized LMP from public ERCOT API
2. 🧮 Score each miner deterministically — **no humans in the loop**
3. 🔐 Commit-reveal weight vector (5-tempo delay defeats copiers)
4. 💰 Sell forecast access via enterprise API ($5k–$100k/mo tiers)

```
score(m) = 0.5 · Skill(m)         (vs rotating naive baseline)
        + 0.3 · Calibration(m)    (CRPS on prob. forecast)
        + 0.2 · HorizonBreadth(m) (mean skill across horizons)
```

---

## 📊 Live scoring

```
ERCOT day-ahead LMP · tempo 1284

 uid   Skill   CRPS   Breadth  composite     W
   0   +0.42  0.89   0.78      0.742    0.471   ← top quant
   1   +0.11  0.62   0.55      0.412    0.262   ← competent
   2   −0.08  0.41   0.32      0.000    0.000   ← below naive ⇒ zeroed
   3   +0.31  0.95   0.61      0.628    0.267   ← tight calibration

🔐 commit_reveal hash submitted, reveals in 5 tempos
   0x7f3e91ac42…
```

3 signals differentiate quality · baseline-losers auto-zeroed

---

<style scoped>
table { font-size: 0.68em; }
td, th { padding: 4px 8px; }
</style>

## 🛡️ Anti-gaming — 8 defenses, 8 attacks

| | Attack | Defense | Param |
|---|---|---|---|
| 🔁 | Weight copying (validator→validator) | Commit-reveal weights | `commit_reveal=5` |
| 👯 | Forecast copying (miner→miner) | Sealed-submission hash | per-tempo |
| 🤝 | Validator cabal | κ-clipping raised | `kappa=0.6` |
| 📌 | Single-miner pump | Min weight spread | `min_weights=16` |
| 🎭 | Naive-baseline gaming | Rotating baseline + skill floor | weekly |
| 👥 | Sybil miners | Compute + data-feed cost self-bankrupts | default burn |
| 🌊 | Bond whipsaw | Tight liquid-α bounds | `0.05 / 0.35` |
| 💀 | Flow-emission spiral (dTAO) | $1M pre-fund + 20% API redirect | treasury |

---

## 🏗️ Architecture

```
              ISO / Exchange feeds
   (ERCOT · CAISO · PJM · ENTSO-E · EIA · AIS · WX)
                       │
                       │ deterministic resolution
                       ▼
   Miners          Validators              Yuma
   probabilistic ─▶ Skill+CRPS+Breadth ──▶ 41 / 41 / 18
   multi-horizon    + enterprise API
```

**No human DAO · No LLM judge · No off-chain attestation**
Public ISO data is the only source of truth.

---

## 💼 Customers

| Tier | Audience | Monthly |
|---|---|---|
| 🪙 **Crypto-quant** | On-chain energy desks · RWA protocols · ~40 funds | $5k |
| 📈 **Trading desk** | Mid-tier ERCOT/CAISO traders (NA + EU) | $25k |
| 🏢 **Enterprise** | Utility · IPP · data-center operator | $100k+ |

**Path to $15–30M API ARR by year 3.** First 20 customers are crypto-energy crossover — wallet-aligned with Bittensor, underserved by Modo / Bloomberg.

---

## ⚙️ Chain interface

```bash
btcli subnet create --network finney --wallet.name flux
btcli sudo set --param tempo                --value 360
btcli sudo set --param kappa                --value 39320   # 0.6
btcli sudo set --param commit_reveal_period --value 5
btcli sudo set --param alpha_low            --value 3277    # 0.05
btcli sudo set --param alpha_high           --value 22937   # 0.35
btcli sudo set --param min_allowed_weights  --value 16
btcli sudo set --param bonds_moving_avg     --value 900000
```

Each hyperparam maps to a specific defended attack.

---

## 🏆 Why Flux

| | Axis | Answer |
|---|---|---|
| 💰 | **Product** | 3 customer tiers · $15–30M ARR path · 20 wallet-aligned wedge customers |
| 🌐 | **Organization** | Open competition · miners self-select on compute · zero DAO bootstrap |
| ✅ | **Verification** | Skill + CRPS + breadth · all from public ERCOT data · zero humans |
| 🎲 | **Game-theory** | 8 named defenses · each maps to a hyperparam or treasury commitment |

vs **Modo Energy** ($33M Series B, Dec 2025): static valuations, one proprietary model.
**Flux:** live operational forecasts across all energy commodities, scored every 72 min.

---

<!-- _class: lead -->

# ⚡ Thank you

**Flux** — real-time energy intelligence, Bittensor-native

`github.com/edwardtay/bittensor-flux`
