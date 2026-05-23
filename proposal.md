# Flux — Proposal

**v0.4 · 2026-05-23**

> 在 Bittensor 上,让全球量化研究员通过电网真实数据的实时打分,生产可买卖的能源预测。

Real-time energy-market intelligence as a Bittensor subnet. Open-competition forecasting of power prices, load, and commodity flows — scored deterministically against ISO ground truth every tempo (~72 minutes).

---

## 1. Introduction

### 1.1 The problem

AI data-center buildout is the largest energy story of the decade. The International Energy Agency projects global data-center electricity consumption to **double from 415 TWh (2024) to 945 TWh by 2030** — roughly 3% of all electricity on Earth. ERCOT's own 2025 Long-Term Load Forecast projects Texas peak demand rising from **94 GW today to 208 GW by 2030** in the high case (139 GW even after a 50% data-center request haircut).

Layer on geopolitical supply shocks — Strait of Hormuz (20% of global oil and LNG flows), Russia/Ukraine restructuring European gas, Red Sea and Panama shipping disruptions — and energy markets reprice in days rather than quarters.

### 1.2 Why existing solutions fail

Legacy energy-intelligence vendors (Bloomberg NEF, Enverus, Modo Energy, Wood Mackenzie, Aurora) sell **quarterly static valuations from single proprietary models**. One firm, one model, one opinion — $300k per seat.

This works for slow markets. It does not work for a market that now moves every five minutes. Three structural reasons:

- **Single-vendor monoculture** — one team's biases dominate one firm's product.
- **Quarterly cadence** — by publication, the world has already moved.
- **Opaque scoring** — no buyer can audit how forecasts actually performed.

### 1.3 Why Bittensor fits

Most decentralized-AI subnets struggle with one question: *who decides who is right?* Flux's answer is simple — **the grid does**. ERCOT publishes the realized price. Either a forecast matched, or it didn't.

| Property | Implication |
|---|---|
| Deterministic ground truth | ISO publishes settled LMP; no DAO, no LLM judge, no dispute |
| Asymmetric compute | Forecasting is expensive; scoring is a single math operation |
| Many independent quants | Open competition beats single-vendor IP, which is the existing market structure |
| Composable | Validators sell API revenue · subnet captures emissions · stakers capture both |

The mechanism only works when validation is cheap, public, and unarguable. Power markets are all three.

### 1.4 What we are building, in one sentence

A marketplace for energy forecasts. Many competing models. One unarguable source of truth.

---

## 2. Incentive & Mechanism Design

### 2.1 Emission split

Standard Bittensor 41 / 41 / 18:

- **41%** to miners, distributed by incentive `I = normalize(W'ᵀ · S)`
- **41%** to validators + their delegators, distributed by dividends `D = normalize(B · I)`
- **18%** to the subnet owner (Flux treasury)

### 2.2 Composite score

For each miner `m` over the evaluation window:

```
score(m) = 0.5 · Skill(m) + 0.3 · Calibration(m) + 0.2 · HorizonBreadth(m)
```

| Signal | Rewards | Resists |
|---|---|---|
| **Skill** = `1 − MSE(m) / MSE(naive)`, sigmoid-normalized | Beating a public naive baseline | Overfitting — baseline rotates weekly |
| **Calibration** = `1 − CRPS(F_m) / CRPS_baseline` | Honest uncertainty intervals | Tight-but-wrong point forecasts |
| **HorizonBreadth** = mean skill across all four horizons | Depth across horizons | Single-horizon specialists |

A miner whose 30-day mean Skill drops below zero (worse than naive) is zeroed automatically before consensus.

### 2.3 Subnet hyperparameters

```python
sn.create_subnet(
    tempo=360,                  # ~72 min
    kappa=0.6,                  # raised above default 0.5
    alpha_low=0.05, alpha_high=0.35,
    min_allowed_weights=16,
    commit_reveal_period=5,     # tempos
    immunity_period=5_000,
    bonds_moving_avg=900_000,
)
```

Every value above defends a specific attack (§2.4). Spec frozen against Yuma v3 + Taoflow (November 2025).

### 2.4 Anti-gaming defenses

| Attack | Defense | Parameter |
|---|---|---|
| **Weight copying** — validator harvests vTrust | Commit-reveal weights | `commit_reveal_period = 5` |
| **Forecast copying** — miner observes peers | Sealed-submission hash per tempo | per-tempo nonce |
| **Validator cabal** — colluding on consensus | κ-clipping raised above default | `kappa = 0.6` |
| **Single-miner pump** — narrow weight vector | Forced minimum weight spread | `min_allowed_weights = 16` |
| **Naive-baseline gaming** — fit to fixed target | Weekly baseline rotation + skill floor | persistence → ARIMA → XGB |
| **Sybil miners** — flood UIDs | Burn + data-feed cost + dereg | default burn + 5,000-block immunity |
| **Bond whipsaw** — whales flip α | Tight liquid-α bounds | `alpha_low/high = 0.05 / 0.35` |
| **Flow-emission death spiral** — Taoflow unstake | Planned **$1M reserve** from owner share + 20% API revenue redirect + ≥6-month staker incentive | treasury policy |

No defense relies on humans, DAOs, or off-chain attestation. Every attack maps to a named hyperparameter or capital commitment.

### 2.5 Gate tests (kill criteria)

Three six-month tests. Pass all three to keep building. Fail any two, reconsider.

1. **Beat naive baselines by ≥20%** on ERCOT day-ahead LMP across 30 days of historical backtest.
2. **One paying enterprise customer at ≥$10k/month** (not a crypto-native pilot — a real energy-market participant).
3. **Net stake inflow positive every month.** Under Taoflow, negative net flow zeros emissions — this is the canary.

---

## 3. Miner Design

### 3.1 What miners produce

Probabilistic forecasts of ERCOT day-ahead LMP across four time horizons:

| Horizon | Use case |
|---|---|
| 5 minutes | High-frequency dispatch, battery operations |
| 1 hour | Real-time trading, demand response |
| 24 hours | Day-ahead bidding |
| 168 hours (1 week) | Maintenance scheduling, hedging |

Output per forecast:

```python
Forecast(
    horizon: Literal["5min", "1h", "24h", "168h"],
    target_time: datetime,
    mean: float,
    p10: float,  # 10th percentile
    p50: float,  # median
    p90: float,  # 90th percentile
)
```

### 3.2 Submission protocol

```
At tempo t (window opens):
    submit H = sha256(forecast || nonce_t)        # sealed commit

At tempo t+1 (window closes):
    reveal (forecast, nonce_t)                    # chain verifies H
```

Late reveals are slashed. Miners cannot observe peer forecasts before committing — peers are invisible until the commitment lands on-chain.

### 3.3 Reference implementation

Open-source starting point provided by the subnet owner:

- **Model:** Gradient-boosted tree (XGBoost) on public ERCOT load + weather + Henry Hub gas-price features
- **Training data:** Five years of ERCOT historical LMP + weather + gas
- **Inference:** ~150 ms per forecast on a single CPU core
- **Probabilistic intervals:** quantile regression heads

The reference miner is the starting line. Miners fork it, ensemble it, swap in transformer-based forecasters, or write something better. The composite score determines which approach earns emissions.

### 3.4 Compute and cost floor

| Component | Approximate cost |
|---|---|
| ERCOT API access | Free (public) |
| Weather feed (NOAA / DWD) | Free |
| Cloud compute (small CPU instance) | $30–80 / month |
| Optional GPU for transformer ensembles | $200–500 / month |

This is the Sybil-resistance moat — fake miners self-bankrupt on feed + compute costs without ever earning emissions.

### 3.5 Miner economics

A median-quality miner earns ~0.2% of subnet emissions (`I_normalize` across ~150 active miners). At a target subnet alpha price of $X per tempo, this works out to monthly net earnings after compute that miners can model deterministically before deciding to participate.

---

## 4. Validator Design

### 4.1 Validator loop (per tempo, ~72 min)

```
1. Pull realized LMP from ERCOT public API
2. For each registered miner m:
   - Resolve forecasts that have matured this tempo
   - Compute Skill(m), Calibration(m), HorizonBreadth(m)
   - Apply naive-baseline floor (Skill < 0 ⇒ zero)
3. Compute composite score(m) → weight vector W_i
4. Submit commit-reveal weight on chain
5. Serve enterprise API to paying customers
```

### 4.2 Validator scoring pseudocode

```python
def score(miner, window):
    skill_raw = np.mean([
        1 - mse(s.forecast[miner], s.realized)
            / mse(baseline_for_week(s.week), s.realized)
        for s in window
    ])
    skill = sigmoid(skill_raw * 2)

    calib = 1 - np.mean([
        crps(s.dist[miner], s.realized) for s in window
    ]) / baseline_crps

    breadth = np.mean([
        skill_at(miner, h, window)
        for h in ["5min", "1h", "24h", "168h"]
    ])

    # Skill floor
    if skill_raw < 0:
        return 0.0

    return 0.5*skill + 0.3*calib + 0.2*breadth
```

### 4.3 Commit-reveal cadence

```
At tempo t:    submit hash(W_t || nonce_t)
At tempo t+5:  reveal (W_t, nonce_t); chain verifies
```

Five-tempo delay (~6 hours) defeats weight-copying validators that would otherwise read the consensus and free-ride.

### 4.4 Enterprise API tier (validator-side revenue)

Validators serve a paid forecast API to enterprise customers:

| Tier | Audience | Annual contract |
|---|---|---|
| **Crypto-quant** | On-chain energy desks, RWA protocols, dozens of crypto-native funds | $60k |
| **Trading desk** | Mid-tier ERCOT/CAISO traders | $300k |
| **Enterprise** | Utilities, IPPs, data-center operators | $1.2M+ |

Validator captures API revenue directly. Subnet captures emissions. Long-term stakers (≥6 month lockup) receive discounted enterprise access — sticky stake aligned against the Taoflow spiral.

### 4.5 Validator selection and number

Standard Bittensor subnet structure: 64 validator UID slots, 192 miner slots, 256 total. Active set in steady state targets 8–12 validators, with API-revenue ranking determining which validators attract the most stake.

---

## 5. Business Logic & Market Rationale

### 5.1 Why now

Three exogenous forces compound simultaneously:

| Force | Magnitude | Why now |
|---|---|---|
| **AI data-center demand** | 945 TWh by 2030 (2× from 2024) — IEA | Hyperscaler capex commitments locked in |
| **Geopolitical supply fragility** | 20% of oil + LNG through Hormuz alone | Multiple active conflicts and chokepoints |
| **Grid decarbonization volatility** | LMP volatility ~3× pre-2020 levels in ERCOT | Renewables + retiring thermal = lumpy supply |

This volatility is permanent, not cyclical. Forecast value rises with volatility.

### 5.2 Customer segmentation and pricing

| Tier | Wedge characteristic | Pain point | Annual ACV |
|---|---|---|---|
| Crypto-quant funds | Trust Bittensor + trade ERCOT | Underserved by Bloomberg/Modo | $60k |
| Mid-tier trading desks | Need ensemble inputs | Cannot afford in-house ML team | $300k |
| Enterprise utilities + DC ops | Need auditable forecasts | Regulatory + procurement | $1.2M+ |

**Three-year revenue target: $15–30M ARR** across validators.

### 5.3 First-customer wedge

Traditional energy traders will not trust a Bittensor subnet on day one. Sales cycles are too slow to fund the first 18 months.

The wedge: **crypto-native energy desks** that already trade ERCOT and already use Bittensor. They are underserved by Bloomberg (too expensive, not crypto-native) and by Modo (valuation-focused, not real-time). Dozens of such funds exist today.

This wedge funds operations through Q4 2026, by which time on-chain track records exist for traditional buyers to audit.

### 5.4 Competitive positioning

| Incumbent | What they sell | How Flux differs |
|---|---|---|
| **Bloomberg NEF** | Premium quarterly reports + Terminal data | Real-time vs quarterly; $5k/mo vs $30k+/mo |
| **Modo Energy** (£25M Series B, Dec 2025) | Static valuations of electrification assets | All commodities + operational vs valuation |
| **Enverus** | Real-time oil/gas data + analytics | Open competition vs single vendor; power markets |
| **Yes Energy** | ISO market data + alerts | Predictions vs raw data; probabilistic + auditable |
| **Aurora Energy Research** | Long-term market simulation | Operational horizons vs strategic |
| **In-house quant teams** | Proprietary internal models | Distribution for long-tail forecasters |

The only quadrant Flux occupies alone: **many independent models** combined with **real-time scoring against public grid data**.

### 5.5 Honest mechanism limitations

Public visibility — to maintain credibility with sophisticated buyers, three known weaknesses are explicit:

1. **Public forecast = no alpha** for high-frequency trading. The trading-desk tier ($300k ACV) assumes buyers use forecasts as ensemble inputs, not as standalone alpha. Sophisticated desks may pay only $5–30k/mo.
2. **256 UID hard cap** limits miner count to 192. Modo can hire more analysts than Flux can host miners.
3. **Beat-naive-by-20%** is the table-stakes target, not a moat. Forecast skill ceilings in this domain are real (~5–8% MAPE on normal days, ~15–30% on volatile days).

Flux's genuine novelty: **continuous tournament**, **on-chain provenance**, **probabilistic-first scoring**, and **long-tail talent monetization**. Not "many models beat one."

### 5.6 Roadmap (targets, not commitments)

- **Today (May 2026):** Mechanism spec frozen. Proposal + deck + site shipped.
- **Q3 2026:** Reference miner built. Backtest gate test passed. Subnet registered. First crypto-native pilots.
- **Q4 2026:** CAISO + PJM added. First ≥$10k/mo enterprise customer.
- **2027:** ENTSO-E (Europe), Henry Hub gas, refined-product cracks. Target $1M MRR by Q1 2027.

---

## 6. System Architecture and Workflows

### 6.1 Three actors, one closed loop

```
              ISO / Exchange feeds
   ERCOT · CAISO · PJM · ENTSO-E · EIA · AIS · weather
                       │
                       │  deterministic resolution
                       ▼
   ┌─────────┐    ┌──────────────┐    ┌──────────┐
   │ Miners  │───▶│  Validators  │───▶│   Yuma   │
   │ probabil-    │  Skill+CRPS  │    │ 41/41/18 │
   │ istic    │   │  +Breadth    │    │          │
   │ forecasts│   │ + API revenue│    │          │
   └─────────┘    └──────────────┘    └──────────┘
                         │
                         ▼
                  Enterprise API
                  $5k–$100k / mo
```

Closed loop: better forecasts → more API revenue → larger emissions → better forecasters.

### 6.2 Per-tempo workflow (~72 minutes)

```
t = 0:00   Validators publish naive baseline + resolution targets
t = 0:05   Miners submit sealed forecast commitments (sha256)
t = 0:15   Forecast submission window closes
t = ...    Forecasts mature: 5min ones resolve immediately,
           1h after 1h, 24h after 24h, 168h after 168h
t = T:00   Tempo end. Validators compute scores on all
           forecasts resolved this tempo. Commit weight hash.
t = T+5    Validators reveal weight vectors. Yuma processes.
           Emissions distributed.
```

### 6.3 Data feeds (validator-side)

| Feed | Source | Use |
|---|---|---|
| ERCOT settled LMP | `ercot.com/api` | Primary ground truth |
| ERCOT load forecast | `ercot.com/api` | Baseline + feature |
| NOAA / OpenMeteo weather | Public | Baseline + feature |
| Henry Hub gas | EIA | Cross-commodity signal |
| AIS LNG cargo | Public AIS feeds | Cross-commodity signal |

All feeds are public and free. Licensing risk: zero.

### 6.4 On-chain interface

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

### 6.5 Current build status

| Built | Not yet built |
|---|---|
| Mechanism spec (this document) | Reference miner code |
| Public proposal + deck + landing page | Validator scoring pipeline |
| Open-source repository (MIT) | Backtest against ERCOT history |
| Bilingual pitch script (EN / 中文) | Customer letters of intent |
| Deployed site at `bittensor-flux.lever-labs.com` | Subnet registration on mainnet |

Design phase. Code and registration follow gate-test pass.

---

## Sources

- [IEA, *Energy and AI*, April 2025](https://www.iea.org/reports/energy-and-ai) — 945 TWh by 2030, ~3% of global electricity
- [ERCOT 2025 Long-Term Load Forecast (LTLF3)](https://www.ercot.com/files/docs/2025/04/07/8.1-Long-Term-Load-Forecast-Update-2025-2031-and-Methodology-Changes.pdf) — 94 → 208 GW high case
- [EIA, World Oil Transit Chokepoints](https://www.eia.gov/international/analysis/special-topics/World_Oil_Transit_Chokepoints) — Hormuz: 20% of oil + LNG (2024)
- [Modo Energy Series B, December 2025](https://www.moltenventures.com/news/banking-on-better-benchmarks-moltens-leads-25m-series-b-investment-in-modo-energy) — £25M, Molten Ventures lead
- [Bittensor Taoflow upgrade, November 2025](https://docs.learnbittensor.org/dynamic-tao/dtao-faq)
- [Bittensor subnet structure](https://docs.taostats.io/docs/subnet) — 256 UIDs, 64 validator + 192 miner slots
- [Perryman Group, Winter Storm Uri estimate, 2021](https://www.perrymangroup.com/publications/column/2021/03/01/the-freeze/) — $195–295B economic damage range
