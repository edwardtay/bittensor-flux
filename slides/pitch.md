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
  ul { line-height: 1.6; }
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
    font-size: 1.8em;
    line-height: 1.15;
    color: #0a4d68;
    font-weight: 700;
    margin: 0.4em 0;
    letter-spacing: -0.02em;
  }
  .huge {
    font-size: 2.8em;
    line-height: 1.1;
    color: #0a4d68;
    font-weight: 800;
    letter-spacing: -0.03em;
    margin: 0.2em 0;
  }
  .sub {
    color: #555;
    font-size: 0.95em;
    line-height: 1.5;
  }
  header, footer { color: #999; font-size: 0.65em; letter-spacing: 0.05em; }
---

<!-- _class: lead -->

# Flux

## Real-time energy intelligence on Bittensor

<br>

A market for forecasts. Many competing models. One unarguable source of truth.

---

## AI is the largest energy story of our lifetime

<div class="kicker">The setup</div>

<p class="huge">Global electricity demand grew more in 2024 than in any year since WWII.</p>

AI data centers, EVs, electrified industry, and cooling load are compounding at once. Every major grid — China, US, Europe, India — is repricing electricity in real time.

<br>

> The people who can **see the next price before everyone else** make fortunes. The people who can't go bankrupt.

---

## But forecasts are stuck in the past

<div class="kicker">The gap</div>

Today's energy intelligence comes from a handful of consulting firms selling **PDF reports updated once a quarter**. One firm, one model, one opinion — sold for hundreds of thousands of dollars per seat.

Meanwhile, the **actual market** moves every five minutes.

<br>

Power traders, utilities, and data center operators are flying blind in the most volatile energy environment in 50 years — relying on tools built for a world that doesn't exist anymore.

<br>

> What if forecasts came from hundreds of competing models, were scored against reality every hour, and the best ones rose to the top automatically?

---

## That's what Flux is

<div class="kicker">The idea</div>

A **marketplace for energy forecasts** built on Bittensor.

<br>

| | |
|---|---|
| **Anyone** can submit a forecast | Hedge fund quants, independent researchers, universities |
| **Reality** decides who's right | Public grid data resolves every forecast automatically |
| **Customers** buy the best | Traders, utilities, data centers pay for the top-ranked outputs |
| **The network** rewards winners | Better forecasters earn more; bad ones earn nothing |

No single vendor. No proprietary black box. No quarterly delay.

---

## Why this actually works on Bittensor

<div class="kicker">The fit</div>

Most "decentralized AI" pitches struggle to answer one question: *who decides who's right?* If it's a committee or a vote, the mechanism breaks.

Flux's answer: **the grid does.** ERCOT publishes the realized price. Either your forecast matched, or it didn't.

<br>

| | |
|---|---|
| Ground truth | Public ISO data — no DAO, no LLM judge, no dispute |
| Compute economics | Forecasting is expensive; scoring is a single math operation |
| Open competition | Beats single-vendor IP, which *is* the existing market structure |
| Composable | Validators sell API access · subnet captures emissions · stakers capture both |

The mechanism only works when validation is cheap, public, and unarguable. Power markets are all three.

---

## v1 — day-ahead power prices

<div class="kicker">Where we start</div>

One market, one product, prove the mechanism. Start with the grid whose demand is doubling fastest and whose data is fully open.

| Property | Why this market first |
|---|---|
| Largest single demand shock on record | 94 GW today → **208 GW by 2030** (official high-case forecast) |
| Fully open public data | Validators score for free; no licensing risk |
| Highest price volatility globally | Biggest absolute forecast value |
| Weak public baseline | Easy first ≥20% improvement target |
| Existing crypto-native traders | First customers wallet-aligned |

Adjacent grids follow in Q4 (CAISO, PJM). Europe + Asia + commodities (gas, refined products) in 2027.

---

## What miners do

<div class="kicker">The supply side</div>

Submit **probabilistic forecasts** of tomorrow's power prices across four time horizons — 5 minutes ahead, 1 hour, 24 hours, 1 week.

```python
Forecast(horizon, target_time, mean, p10, p50, p90)
```

<br>

| Mechanic | How it works |
|---|---|
| Sealed submission | Forecasts cryptographically committed before the window opens |
| Anti-copying | Miners cannot peek at competitors before submitting |
| Reference model | XGBoost on public load + weather + gas-price features (forkable) |
| Sybil cost | Compute + data-feed fees self-bankrupt fake miners |

Forking the reference model is the starting line, not the finish.

---

## What validators do

<div class="kicker">The scoring side</div>

Every ~72 minutes:

```
1.  Pull realized prices from ERCOT's public API
2.  score(m) = 0.5·Skill + 0.3·Calibration + 0.2·Breadth
3.  Commit-reveal weight vector (5-tempo delay defeats copiers)
4.  Serve the best forecasts via enterprise API
```

<br>

| Signal | What it rewards | What it resists |
|---|---|---|
| **Skill** | Beating a public naive baseline | Overfitting (baseline rotates weekly) |
| **Calibration** | Honest uncertainty intervals (CRPS) | Tight-but-wrong point forecasts |
| **Breadth** | Good performance across all horizons | Single-horizon specialists gaming one cycle |

---

## A real scoring round

```
ERCOT day-ahead LMP · tempo 1284 · 2026-09-14 18:24 CDT

 uid   Skill   CRPS   Breadth   composite        W
   0   +0.42   0.89    0.78       0.742      0.471    top quant
   1   +0.11   0.62    0.55       0.412      0.262    competent
   2   −0.08   0.41    0.32       0.000      0.000    below naive → zeroed
   3   +0.31   0.95    0.61       0.628      0.267    tight calibration

commit_reveal  0x7f3e91ac42…  reveals in 5 tempos
```

<br>

Three independent signals differentiate quality. Forecasters worse than the naive baseline are **auto-excluded** before they can dilute consensus.

---

<style scoped>
table { font-size: 0.72em; }
td, th { padding: 5px 10px; }
</style>

## Eight attacks, eight defenses

| Attack vector | Defense | Parameter |
|---|---|---|
| Weight copying — validator harvests vTrust for free | Commit-reveal weights | `commit_reveal_period = 5` |
| Forecast copying — miner peeks at peers | Sealed-submission hash | per-tempo nonce |
| Validator cabal — collusion on consensus | κ-clipping raised above default | `kappa = 0.6` |
| Single-miner pump — narrow weight vector | Forced minimum weight spread | `min_allowed_weights = 16` |
| Naive-baseline gaming — fit to fixed target | Weekly baseline rotation + skill floor | persistence → ARIMA → XGB |
| Sybil miners — flood UID slots | Burn cost + data-feed cost + dereg | default burn + 5000-block immunity |
| Bond whipsaw — whales flip α to escape penalty | Tight liquid-α bounds | `alpha_low/high = 0.05 / 0.35` |
| Flow-emission spiral — Taoflow unstake death | $1M pre-fund + 20% API revenue redirect | treasury policy |

Every attack maps to one specific hyperparameter or capital commitment. **No human dependency anywhere.**

---

## How the system fits together

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

A closed loop: better forecasts → more API revenue → larger emissions → better forecasts.

---

## Who pays

| Tier | Audience | Annual contract |
|---|---|---|
| **Crypto-quant** | On-chain energy desks · RWA protocols · dozens of crypto-native funds | $60k |
| **Trading desk** | Mid-tier ERCOT/CAISO traders, NA + EU | $300k |
| **Enterprise** | Utilities · IPPs · data-center operators | $1.2M+ |

**Three-year ARR target: $15–30M** across validators.

First-customer wedge: crypto-native energy desks already trust both Bittensor *and* ERCOT markets. They're underserved by Bloomberg (too expensive, not crypto-native) and Modo (valuation-focused, not real-time). This wedge funds the first 18 months alone.

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

Every value above defends a named attack. Spec frozen against Yuma v3 + Taoflow (Nov 2025).

---

## Why Flux

| Axis | Position |
|---|---|
| **Product** | Three priced tiers · existing customer budgets · $15–30M ARR path |
| **Organization** | Open competition · zero DAO bootstrap risk |
| **Verification** | Public grid data only · zero humans in the scoring loop |
| **Game-theory** | Eight named defenses · each maps to a hyperparameter or capital commitment |

<br>

vs **Modo Energy** (£25M Series B, Dec 2025): single proprietary model, quarterly valuations, electrification assets only.

**Flux:** many competing models, live forecasts every 72 minutes, all energy commodities.

---

<!-- _class: lead -->

# Flux

## Real-time energy intelligence, Bittensor-native

<br>

`github.com/edwardtay/bittensor-flux`
