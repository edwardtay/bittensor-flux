# Flux — Proposal

**v0.2 · 2026-05-23**

Real-time energy-market intelligence as a Bittensor subnet. Open-competition forecasting of power prices, load, and commodity flows, scored deterministically against ISO ground truth every tempo (~72 min).

---

## 1. Problem

AI data-center buildout repriced ERCOT peak load from 29 → 77 GW in 18 months. Every ISO is in the same shape. Legacy vendors (Bloomberg NEF, Enverus, Modo) sell quarterly static valuations from single proprietary models — structurally cannot serve real-time, multi-horizon, multi-commodity forecasting.

Bittensor fits because ISO data resolves forecasts deterministically, hundreds of quants can compete openly, and scoring is automatable with zero human judgment.

## 2. What miners do

Submit probabilistic forecasts on ERCOT day-ahead LMP across four horizons (5-min, 1h, 24h, 168h). Output per forecast:

```python
Forecast(horizon, target_time, mean, p10, p50, p90)
```

Forecasts are **commit-revealed** — sealed `sha256(forecast || nonce)` before the resolution window opens, revealed after. Miners cannot copy each other.

Reference miner = XGBoost on public ERCOT load + weather + gas-price features. Forkable starting point; miners improve from there.

## 3. What validators do

Every tempo:

1. Pull realized LMP from public ERCOT API
2. Compute composite score per miner:

```
score(m) = 0.5·Skill(m) + 0.3·Calibration(m) + 0.2·HorizonBreadth(m)
```

- **Skill** = `1 - MSE(forecast) / MSE(naive_baseline)`, sigmoid-normalized. Baseline rotates weekly (`persistence`, `ARIMA-seasonal`, `XGB-public`) so no single baseline becomes the target to overfit.
- **Calibration** = `1 - CRPS(forecast_dist) / CRPS_baseline`. Punishes overconfident intervals.
- **HorizonBreadth** = mean skill across all four horizons. Rewards depth over single-horizon specialists.

3. Submit weight vector via **commit-reveal (5 tempos / ~6h)** so weight copiers cannot harvest vTrust.
4. Sell forecast access via enterprise API ($5k–$100k/mo tiers). Validator captures revenue; subnet captures emissions.

Scoring is fully deterministic — no human DAO, no LLM judges, no off-chain disputes.

## 4. How we Sybil-/attack-protect

| Attack | Defense | Parameter |
|---|---|---|
| **Weight copying** between validators | Commit-reveal weights | `commit_reveal_period = 5` tempos |
| **Forecast copying** between miners | Sealed-submission commit-reveal on forecasts (chain-side hash) | per-tempo |
| **Validator cabal** colluding on weights | κ-clipping raised above default | `kappa = 0.6` |
| **Single-miner pump** | Min weight spread | `min_allowed_weights = 16` |
| **Sybil miners** flooding UIDs | Bittensor default burn + immunity + deregistration. Forecasting is compute-bound + data-feed-bound — Sybils self-bankrupt. | `immunity_period = 5_000` |
| **Naive-baseline gaming** | Baseline rotates weekly; miners with `Skill < 0` for 3+ tempos zeroed | rotating |
| **Bond whipsaw** by whales | Tight liquid-α | `alpha_low = 0.05`, `alpha_high = 0.35` |
| **Flow-emission death spiral** (dTAO: negative net stake flow → zero emissions → miner exodus) | $1M USDC owner pre-fund + 20% API revenue redirect to miners during low-emission windows + ≥6-month staker API discount | treasury policy |

No defense relies on humans, DAOs, or off-chain attestation. Every attack maps to a named hyperparameter or treasury commitment.

## 5. Subnet hyperparameters

```python
sn.create_subnet(
    tempo=360,                  # ~72 min
    kappa=0.6,
    alpha_low=0.05, alpha_high=0.35,
    min_allowed_weights=16,
    commit_reveal_period=5,
    immunity_period=5_000,
    bonds_moving_avg=900_000,
)
```

Standard 41% miner / 41% validator+stakers / 18% owner emission split. Owner treasury funds the $1M pre-fund, data-feed costs, and baseline maintenance.

## 6. Why Flux

| Axis | Answer |
|---|---|
| **Product** | 3 customer tiers with existing budget: crypto-energy funds ($5k/mo, ~40 active wallets), trading desks ($25k/mo), enterprise utilities ($100k+/mo). Path to $15–30M API ARR by year 3. |
| **Organization** | Open competition — no human coordination needed. Miners self-select on compute economics. No DAO bootstrap risk. |
| **Verification** | Skill + CRPS + horizon-breadth, all resolved against public ERCOT data. Zero human-in-the-loop. |
| **Game-theory** | 8 named defenses, each targeting a specific attack with a specific hyperparameter or treasury commitment (§4). |

## 7. Roadmap

- **v0:** ERCOT-only prototype + this proposal + slides
- **Q3 2026:** Subnet registration; 20 crypto-energy fund pilots
- **Q4 2026:** CAISO + PJM; first $10k/mo enterprise customer
- **2027:** ENTSO-E, Henry Hub gas, refined-product cracks; $1M MRR

## Appendix: scoring pseudocode

```python
def score(miner, window):
    skill = sigmoid(np.mean([
        1 - mse(s.forecast[miner], s.realized) / mse(baseline_for_week(s.week), s.realized)
        for s in window
    ]) * 2)

    calib = 1 - np.mean([crps(s.dist[miner], s.realized) for s in window]) / baseline_crps

    breadth = np.mean([skill_at(miner, h, window) for h in ["5m","1h","24h","168h"]])

    if mean_skill(miner, window) < 0:
        return 0.0
    return 0.5*skill + 0.3*calib + 0.2*breadth
```
