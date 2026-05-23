# Flux — Proposal

**v0.3 · 2026-05-23**

Real-time energy-market intelligence as a Bittensor subnet. Open-competition forecasting of power prices, load, and commodity flows, scored deterministically against ISO ground truth every tempo (~72 min).

---

## 1. Problem

AI data-center buildout is the largest energy story of the decade. The IEA projects global data-center electricity consumption to **double from 415 TWh (2024) to 945 TWh by 2030** — roughly 3% of all electricity on Earth. ERCOT's own 2025 Long-Term Load Forecast projects Texas peak demand rising from **94 GW today to 208 GW by 2030** in the high case (139 GW even after the operator's own 50% data-center haircut).

Layer on geopolitical supply shocks — Strait of Hormuz (20% of global oil and LNG flows), Russia/Ukraine restructuring European gas, Red Sea and Panama shipping disruptions — and energy markets reprice in days rather than quarters.

Legacy vendors (Bloomberg NEF, Enverus, Modo Energy, Wood Mackenzie) sell quarterly static valuations from single proprietary models. They structurally cannot serve real-time, multi-horizon, multi-commodity forecasting at the speed the market now moves.

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
| **Flow-emission death spiral** (Taoflow, Nov 2025: negative net stake flow → zero emissions → miner exodus) | Planned **$1M reserve** from owner share + 20% API revenue redirect to miners during low-emission windows + ≥6-month staker API discount | treasury policy |

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

Standard 41% miner / 41% validator+stakers / 18% owner emission split. Owner treasury funds the planned $1M reserve, ISO + commodity data feeds, and baseline maintenance.

## 6. Why Flux

| Axis | Answer |
|---|---|
| **Product** | 3 customer tiers with existing budget: crypto-native quant funds (~$5k/mo, dozens of active funds), trading desks (~$25k/mo), enterprise utilities and data-center operators ($100k+/mo). Three-year target: **$15–30M API ARR** across validators. |
| **Organization** | Open competition — no human coordination needed. Miners self-select on compute economics. No DAO bootstrap risk. |
| **Verification** | Skill + CRPS + horizon-breadth, all resolved against public ERCOT data. Zero human-in-the-loop. |
| **Game-theory** | 8 named defenses, each targeting a specific attack with a specific hyperparameter or treasury commitment (§4). |

## 7. Roadmap

All dates are targets, not commitments.

- **Today (May 2026):** Mechanism spec frozen. Proposal + deck + site shipped. Repo public.
- **Q3 2026:** Reference miner built. 30-day backtest gate test passed (≥20% improvement over naive baseline). Subnet registered on Bittensor mainnet. First crypto-native pilot customers.
- **Q4 2026:** CAISO + PJM markets added. First $10k/mo enterprise customer signed.
- **2027:** ENTSO-E (Europe), Henry Hub gas, refined-product cracks. Target $1M MRR by Q1 2027.

## 8. Current status

This is the design-phase honest version, not a marketing pitch.

| Built | Not yet built |
|---|---|
| Mechanism spec (this document) | Reference miner code |
| Public proposal + deck + landing page | Validator scoring pipeline |
| Open-source repository (MIT) | Backtest against ERCOT history |
| Bilingual pitch script (EN / 中文) | Customer LOIs |
| Captain-deployed site at bittensor-flux.lever-labs.com | Subnet registration |

## 9. Gate tests (six-month kill criteria)

Three tests. Pass all three to keep building. Fail any two, reconsider.

1. **Beat naive baselines by ≥20%** on ERCOT day-ahead LMP across 30 days of historical data. If competitive miners cannot beat yesterday-plus-seasonal, the mechanism is wrong.
2. **One paying enterprise customer at ≥$10k/month** (not a crypto-native pilot — a real energy-market participant using forecasts in production).
3. **Net stake inflow positive every month.** Under Taoflow, negative net flow zeros emissions. If stake does not flow in, the subnet dies.

## 10. Non-goals

- Real subtensor node in v0 demo — mock the chain layer; show the mechanism.
- Multi-market at launch — ERCOT only.
- Training models from scratch — fork open baselines.
- Custom on-chain governance — use Yuma v3 + Taoflow as-is.
- Unicorn ambitions — realistic ceiling is $50–150M enterprise value over five years.

## Sources

- [IEA, *Energy and AI*, April 2025](https://www.iea.org/reports/energy-and-ai) — 945 TWh by 2030, ~3% of global electricity
- [ERCOT 2025 Long-Term Load Forecast](https://www.ercot.com/files/docs/2025/04/07/8.1-Long-Term-Load-Forecast-Update-2025-2031-and-Methodology-Changes.pdf) — 94 → 208 GW high case
- [EIA, World Oil Transit Chokepoints](https://www.eia.gov/international/analysis/special-topics/World_Oil_Transit_Chokepoints) — Hormuz 20% of oil + LNG flows (2024)
- [Modo Energy Series B, December 2025](https://www.moltenventures.com/news/banking-on-better-benchmarks-moltens-leads-25m-series-b-investment-in-modo-energy) — £25M Series B, Molten Ventures lead
- [Bittensor Taoflow upgrade, November 2025](https://docs.learnbittensor.org/dynamic-tao/dtao-faq)
- [Bittensor subnet structure](https://docs.taostats.io/docs/subnet) — 256 UIDs, 64 validator + 192 miner slots
- [Perryman Group, Winter Storm Uri estimate, 2021](https://www.perrymangroup.com/publications/column/2021/03/01/the-freeze/) — $195–295B economic damage range

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
