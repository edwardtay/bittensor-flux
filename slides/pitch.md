---
marp: true
theme: default
size: 16:9
paginate: true
header: 'Flux'
footer: 'github.com/edwardtay/bittensor-flux'
style: |
  section {
    font-size: 22px;
    padding: 55px 75px;
    background: #fbfbfa;
    color: #1a1a1a;
    font-family: -apple-system, "Inter", system-ui, sans-serif;
    letter-spacing: -0.005em;
  }
  h1 {
    color: #0a4d68;
    font-weight: 700;
    letter-spacing: -0.02em;
    margin-bottom: 0.2em;
  }
  h2 {
    color: #0a4d68;
    font-weight: 600;
    letter-spacing: -0.015em;
    border-bottom: 2px solid #0a4d68;
    padding-bottom: 6px;
    margin-bottom: 0.6em;
  }
  h3 { color: #088395; font-weight: 500; }
  strong { color: #0a4d68; }
  table {
    font-size: 0.85em;
    border-collapse: collapse;
    margin: 0.4em 0;
    width: 100%;
  }
  th {
    background: #0a4d68;
    color: white;
    padding: 8px 12px;
    text-align: left;
    font-weight: 600;
    font-size: 0.88em;
    letter-spacing: 0.02em;
    text-transform: uppercase;
  }
  td { padding: 7px 12px; border-bottom: 1px solid #e4e4e0; }
  tr:nth-child(even) td { background: #f3f5f3; }
  code {
    background: #ecefe8;
    color: #0a4d68;
    padding: 1px 6px;
    border-radius: 3px;
    font-size: 0.88em;
    font-family: "JetBrains Mono", "SF Mono", Menlo, monospace;
  }
  pre {
    background: #1a1f24;
    color: #d8e0e8;
    font-size: 0.72em;
    padding: 16px 20px;
    border-radius: 5px;
    line-height: 1.45;
    border-left: 3px solid #088395;
  }
  pre code { background: transparent; color: #d8e0e8; padding: 0; }
  blockquote {
    border-left: 3px solid #088395;
    padding-left: 1em;
    color: #444;
    margin: 1em 0;
    font-style: normal;
  }
  ul { line-height: 1.55; }
  ul li::marker { color: #088395; }
  hr { border: 0; border-top: 1px solid #d8d8d4; margin: 1.2em 0; }
  section.lead {
    text-align: center;
    justify-content: center;
    background: linear-gradient(180deg, #fbfbfa 0%, #eef3f5 100%);
  }
  section.lead h1 {
    font-size: 4.5em;
    border: 0;
    margin-bottom: 0.1em;
  }
  section.lead h2 {
    border: 0;
    font-weight: 400;
    color: #444;
    font-size: 1.3em;
  }
  .kicker {
    color: #088395;
    font-size: 0.75em;
    font-weight: 600;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: -0.4em;
  }
  .big {
    font-size: 1.6em;
    line-height: 1.2;
    color: #0a4d68;
    font-weight: 600;
    margin: 0.3em 0;
  }
  header, footer { color: #999; font-size: 0.65em; letter-spacing: 0.05em; }
---

<!-- _class: lead -->

# Flux

## Real-time energy intelligence on Bittensor

<br>

Open-competition forecasting of power, gas, and refined-product markets
— scored against ISO ground truth every 72 minutes.

---

## The repricing

<div class="kicker">Demand shock</div>

<p class="big">ERCOT peak load: 29 → 77 GW in 18 months.</p>

Every major ISO is revising upward. AI data-center buildout is the largest single-decade demand event since post-war electrification.

**Forecast value scales with volatility.** Hormuz disruptions, climate shocks, and dispatch-mix swings now move LMPs by 10× intraday.

> Legacy vendors (Bloomberg NEF, Enverus, Modo) sell **quarterly static valuations** from one proprietary model. The market needs **live operational forecasts** from many competing models.

---

## Why this fits Bittensor

|  |  |
|---|---|
| **Deterministic ground truth** | ISO publishes settled LMP — no DAO, no LLM judge, no dispute |
| **Asymmetric compute** | Forecasting is expensive · scoring is a `numpy.mean` |
| **Many independent quants** | Open competition beats single-vendor IP, which is the existing market structure |
| **Composable** | Validators sell API revenue · subnet captures emissions · stakers capture both |

The mechanism *only* works when validation is cheap, public, and unarguable. ERCOT day-ahead LMP is all three.

---

## v1 — ERCOT day-ahead LMP

| Property | Why it matters |
|---|---|
| Open data via ERCOT API + EIA | Validators score free; no licensing risk |
| Highest LMP volatility in US | Largest absolute forecast value |
| Weak public naive baseline | Clean first ≥20% skill-score target |
| Crypto-energy desks already trade it | First 20 customers wallet-aligned |
| AI-DC repricing narrative | The story that closes meetings |

Single market for v1. CAISO + PJM follow in Q4. ENTSO-E + commodities in 2027.

---

## What miners do

Submit **probabilistic forecasts** across four horizons — 5min, 1h, 24h, 168h:

```python
Forecast(horizon, target_time, mean, p10, p50, p90)
```

<br>

| Mechanic | Detail |
|---|---|
| Submission | `sha256(forecast ‖ nonce)` committed before window opens, revealed after |
| Anti-copy | Miners cannot observe peers before committing |
| Reference impl | XGBoost on public load + weather + Henry Hub features |
| Compute floor | Sybils self-bankrupt on feature pipeline + inference cost |

Forking the reference miner is the starting line, not the finish.

---

## What validators do

Every tempo (~72 min):

<br>

```
1.  Pull realized LMP from ERCOT API
2.  score(m) = 0.5·Skill + 0.3·Calibration + 0.2·Breadth
3.  Commit-reveal weights (5-tempo delay defeats copiers)
4.  Serve forecast API to enterprise customers
```

<br>

| Signal | Definition | Resists |
|---|---|---|
| **Skill** | `1 − MSE(m)/MSE(naive)`, baseline rotates weekly | Overfitting to one baseline |
| **Calibration** | CRPS-normalized; punishes tight-but-wrong intervals | Point-forecast gaming |
| **Breadth** | Mean skill across all four horizons | Single-horizon specialists |

---

## Sample tempo output

```
ERCOT day-ahead LMP · tempo 1284 · 2026-09-14 18:24 CDT

 uid   Skill   CRPS   Breadth   composite        W
   0   +0.42   0.89    0.78       0.742      0.471    top quant
   1   +0.11   0.62    0.55       0.412      0.262    competent
   2   −0.08   0.41    0.32       0.000      0.000    below naive → zeroed
   3   +0.31   0.95    0.61       0.628      0.267    tight calibration

commit_reveal  0x7f3e91ac42…  reveals in 5 tempos
```

Three signals differentiate quality. Below-naive forecasters are auto-excluded
before they can dilute consensus.

---

<style scoped>
table { font-size: 0.72em; }
td, th { padding: 5px 10px; }
</style>

## Eight attacks, eight defenses

| Attack vector | Defense | Parameter |
|---|---|---|
| Weight copying — validator harvests vTrust | Commit-reveal weights | `commit_reveal_period = 5` |
| Forecast copying — miner observes peers | Sealed-submission hash | per-tempo nonce |
| Validator cabal — collusion on consensus | κ-clipping raised | `kappa = 0.6` |
| Single-miner pump — narrow weight vector | Min weight spread | `min_allowed_weights = 16` |
| Naive-baseline gaming — fit to fixed target | Baseline rotates weekly + skill floor | persistence → ARIMA → XGB |
| Sybil miners — flood UIDs | Burn + feed cost + dereg | default burn + 5000-block immunity |
| Bond whipsaw — whales flip α | Tight liquid-α bounds | `alpha_low/high = 0.05 / 0.35` |
| Flow-emission spiral — dTAO unstake death | $1M pre-fund + 20% API revenue redirect | treasury policy |

Every attack maps to a specific hyperparameter or capital commitment. No human dependency anywhere.

---

## Architecture

```
              ISO / Exchange feeds
   ERCOT · CAISO · PJM · ENTSO-E · EIA · AIS · weather
                       │
                       │  deterministic resolution
                       ▼
   ┌─────────┐    ┌──────────────┐    ┌──────────┐
   │ Miners  │───▶│  Validators  │───▶│   Yuma   │
   │  prob.  │    │  Skill+CRPS  │    │ 41/41/18 │
   │ forecasts    │  +Breadth    │    │          │
   └─────────┘    └──────────────┘    └──────────┘
                         │
                         ▼
                  Enterprise API
                  $5k–$100k / mo
```

Closed loop. Forecast quality → API revenue → miner emissions → forecast quality.

---

## Customer economics

| Tier | Audience | ACV |
|---|---|---|
| **Crypto-quant** | On-chain energy desks · RWA protocols · ~40 funds | $60k |
| **Trading desk** | Mid-tier ERCOT/CAISO traders, NA + EU | $300k |
| **Enterprise** | Utilities · IPPs · data-center operators | $1.2M+ |

**Three-year ARR target: $15–30M** across validators.

First-customer wedge: crypto-native energy desks already trust both Bittensor and ERCOT markets. Underserved by Bloomberg (too expensive, not crypto-native) and by Modo (valuation-focused, not real-time). 18-month runway funded from this wedge alone.

---

## Subnet creation

```bash
btcli subnet create --network finney --wallet.name flux

btcli sudo set --param tempo                --value 360
btcli sudo set --param kappa                --value 39320   # 0.6
btcli sudo set --param commit_reveal_period --value 5
btcli sudo set --param alpha_low            --value 3277    # 0.05
btcli sudo set --param alpha_high           --value 22937   # 0.35
btcli sudo set --param min_allowed_weights  --value 16
btcli sudo set --param bonds_moving_avg     --value 900000
btcli sudo set --param immunity_period      --value 5000
```

Every value above defends a named attack on the previous slide. Spec is frozen against Yuma v3.

---

## Why Flux

| Axis | Position |
|---|---|
| **Product** | Three priced tiers · existing budget lines · $15–30M ARR path · 20 wedge customers wallet-aligned |
| **Organization** | Open competition · miners self-select on compute economics · zero DAO bootstrap risk |
| **Verification** | Skill + CRPS + breadth · public ERCOT data only · zero humans in scoring loop |
| **Game-theory** | Eight named defenses · each maps to a hyperparameter or capital commitment |

vs **Modo Energy** ($33M Series B, Dec 2025): proprietary single-model, quarterly valuations.
**Flux:** open-competition multi-model, live forecasts scored every 72 min, across all energy commodities.

---

<!-- _class: lead -->

# Flux

## Real-time energy intelligence, Bittensor-native

<br>

`github.com/edwardtay/bittensor-flux`
