# Flux

**Real-time energy-market intelligence as a Bittensor subnet.**

Open-competition forecasting of power prices, load, and commodity flows — scored deterministically against ISO ground truth every ~72 minutes.

---

## Why

- AI data-center buildout repriced ERCOT peak load **29 → 77 GW in 18 months**. Every ISO is in the same shape.
- Legacy vendors (Bloomberg NEF, Enverus, Modo) sell quarterly static valuations from single proprietary models — structurally incapable of serving real-time, multi-horizon, multi-commodity forecasting.
- ISO data resolves forecasts deterministically, so Bittensor's incentive layer can run with **zero humans in the scoring loop**.

## What miners do

Submit probabilistic forecasts on ERCOT day-ahead LMP across four horizons (5min, 1h, 24h, 168h):

```python
Forecast(horizon, target_time, mean, p10, p50, p90)
```

Forecasts are **sealed via commit-reveal** before the resolution window opens — miners cannot copy each other. Reference miner = XGBoost on public load + weather + gas features (forkable).

## What validators do

Every tempo (~72 min):

1. Pull realized LMP from public ERCOT API
2. Score each miner deterministically
3. Commit-reveal weight vector (5-tempo delay defeats weight-copiers)
4. Sell forecast access via enterprise API ($5k–$100k/mo tiers)

```
score(m) = 0.5·Skill(m) + 0.3·Calibration(m) + 0.2·HorizonBreadth(m)
```

- **Skill** = `1 − MSE(forecast)/MSE(naive)`, baseline rotates weekly so it can't be overfit
- **Calibration** = CRPS-normalized; punishes overconfident intervals
- **HorizonBreadth** = mean skill across horizons; rewards depth

## Anti-gaming

| Attack | Defense |
|---|---|
| Weight copying (validator→validator) | `commit_reveal_period = 5` tempos |
| Forecast copying (miner→miner) | Sealed-submission commit-reveal per tempo |
| Validator cabal | `kappa = 0.6` κ-clipping |
| Single-miner pump | `min_allowed_weights = 16` |
| Naive-baseline gaming | Rotating baseline + skill-floor zeroing |
| Sybil miners | Compute + data-feed cost self-bankrupts attackers |
| Bond whipsaw | `alpha_low = 0.05`, `alpha_high = 0.35` |
| Flow-emission spiral (dTAO) | $1M USDC pre-fund + 20% API revenue redirect to miners |

Every attack maps to a named hyperparameter or treasury commitment. No human DAO. No off-chain attestation.

## Docs

- [`proposal.md`](proposal.md) — full mechanism design
- [`slides/pitch.md`](slides/pitch.md) — deck (render with `npx @marp-team/marp-cli slides/pitch.md`)

## Status

Early-stage. Mechanism spec frozen; reference implementation in progress.

## License

MIT (TBD).
