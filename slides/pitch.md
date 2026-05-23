---
marp: true
theme: default
size: 16:9
paginate: true
header: 'Flux'
footer: 'github.com/edwardtay/bittensor-flux'
style: |
  section {
    font-size: 21px;
    padding: 50px 70px;
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
    margin-bottom: 0.5em;
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
    font-size: 0.85em;
    letter-spacing: 0.04em;
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
    font-size: 0.7em;
    padding: 14px 18px;
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
    font-size: 0.7em;
    font-weight: 600;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    margin-bottom: 0.2em;
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
    font-size: 2.5em;
    line-height: 1.1;
    color: #0a4d68;
    font-weight: 800;
    letter-spacing: -0.03em;
    margin: 0.15em 0;
  }
  .sub { color: #555; font-size: 0.95em; line-height: 1.5; }
  .cols { display: grid; grid-template-columns: 1fr 1fr; gap: 24px; margin: 0.6em 0; }
  .cols-3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 18px; margin: 0.6em 0; }
  .card {
    background: white;
    border: 1px solid #d8e0e0;
    border-left: 3px solid #088395;
    border-radius: 4px;
    padding: 14px 18px;
  }
  .card-dark {
    background: #0a4d68;
    color: white;
    border-radius: 4px;
    padding: 14px 18px;
  }
  .card-dark .label { color: #88c5d4; font-size: 0.7em; letter-spacing: 0.1em; text-transform: uppercase; }
  .card-dark .value { font-size: 1.8em; font-weight: 700; line-height: 1.1; margin: 0.2em 0; }
  .card .label { color: #088395; font-size: 0.7em; letter-spacing: 0.1em; text-transform: uppercase; font-weight: 600; display: flex; align-items: center; gap: 8px; }
  .icon { width: 18px; height: 18px; stroke: currentColor; fill: none; stroke-width: 1.6; stroke-linecap: round; stroke-linejoin: round; flex-shrink: 0; }
  .icon-lg { width: 28px; height: 28px; stroke: #0a4d68; fill: none; stroke-width: 1.6; stroke-linecap: round; stroke-linejoin: round; }
  .flow-box .icon-lg { margin: 0 auto 6px; display: block; }
  .card .value { font-size: 1.6em; font-weight: 700; color: #0a4d68; line-height: 1.1; margin: 0.2em 0; }
  .card .note { color: #666; font-size: 0.85em; }
  .pill {
    display: inline-block;
    background: #ecefe8;
    color: #0a4d68;
    padding: 3px 10px;
    border-radius: 20px;
    font-size: 0.78em;
    font-weight: 500;
    letter-spacing: 0.02em;
    margin: 0 4px 4px 0;
  }
  .pill-accent { background: #0a4d68; color: white; }
  .step {
    display: flex;
    align-items: flex-start;
    gap: 14px;
    margin: 10px 0;
    padding: 10px 12px;
    background: white;
    border: 1px solid #e0e4e0;
    border-radius: 4px;
  }
  .step-num {
    background: #0a4d68;
    color: white;
    width: 28px;
    height: 28px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
    font-size: 0.9em;
    flex-shrink: 0;
  }
  .step-body { font-size: 0.92em; }
  .step-body strong { display: block; margin-bottom: 2px; }
  .flow {
    display: grid;
    grid-template-columns: 1fr 30px 1fr 30px 1fr;
    align-items: center;
    gap: 8px;
    margin: 1em 0;
  }
  .flow-box {
    background: white;
    border: 2px solid #0a4d68;
    border-radius: 6px;
    padding: 16px 12px;
    text-align: center;
  }
  .flow-box .t { font-weight: 700; color: #0a4d68; font-size: 1em; margin-bottom: 4px; }
  .flow-box .d { font-size: 0.78em; color: #555; line-height: 1.3; }
  .flow-arrow { font-size: 1.6em; color: #088395; text-align: center; }
  .vs {
    display: grid;
    grid-template-columns: 1fr 60px 1fr;
    gap: 0;
    align-items: stretch;
    margin: 0.5em 0;
  }
  .vs > .a, .vs > .b {
    padding: 16px 20px;
    border-radius: 4px;
  }
  .vs > .a { background: #f3f5f3; border: 1px solid #d8d8d4; }
  .vs > .b { background: #0a4d68; color: white; }
  .vs > .b strong { color: white; }
  .vs > .mid {
    display: flex;
    align-items: center;
    justify-content: center;
    color: #088395;
    font-weight: 700;
    font-size: 0.85em;
    letter-spacing: 0.1em;
  }
  .vs .head { font-size: 0.75em; text-transform: uppercase; letter-spacing: 0.1em; opacity: 0.7; margin-bottom: 6px; }
  .vs .body { font-size: 0.92em; line-height: 1.45; }
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

<p class="huge">By 2030, data centers will use<br>more electricity than Japan.</p>

<div class="cols" style="grid-template-columns: 1.4fr 1fr; align-items: center;">
<div>

<svg viewBox="0 0 460 220" style="width:100%; height:auto;">
  <defs>
    <linearGradient id="g1" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0%" stop-color="#0a4d68" stop-opacity="0.85"/>
      <stop offset="100%" stop-color="#088395" stop-opacity="0.35"/>
    </linearGradient>
  </defs>
  <!-- axes -->
  <line x1="50" y1="180" x2="445" y2="180" stroke="#aab" stroke-width="1"/>
  <line x1="50" y1="20" x2="50" y2="180" stroke="#aab" stroke-width="1"/>
  <!-- gridlines + y labels -->
  <line x1="50" y1="40" x2="445" y2="40" stroke="#e0e4e0"/>
  <line x1="50" y1="80" x2="445" y2="80" stroke="#e0e4e0"/>
  <line x1="50" y1="120" x2="445" y2="120" stroke="#e0e4e0"/>
  <line x1="50" y1="160" x2="445" y2="160" stroke="#e0e4e0"/>
  <text x="44" y="44" text-anchor="end" font-size="10" fill="#666">1000</text>
  <text x="44" y="84" text-anchor="end" font-size="10" fill="#666">750</text>
  <text x="44" y="124" text-anchor="end" font-size="10" fill="#666">500</text>
  <text x="44" y="164" text-anchor="end" font-size="10" fill="#666">250</text>
  <text x="44" y="184" text-anchor="end" font-size="10" fill="#666">0</text>
  <text x="14" y="100" text-anchor="middle" font-size="10" fill="#0a4d68" font-weight="600" transform="rotate(-90 14 100)">TWh / year</text>
  <!-- bars: 2020=290, 2022=350, 2024=415, 2026=570, 2028=720, 2030=945 -->
  <!-- scale: 945 TWh -> y=22; 0 -> y=180; per TWh = 158/945 = 0.167 -->
  <g fill="url(#g1)">
    <rect x="68"  y="133.6" width="44" height="46.4"/>
    <rect x="130" y="121.5" width="44" height="58.5"/>
    <rect x="192" y="110.6" width="44" height="69.4"/>
    <rect x="254" y="84.7"  width="44" height="95.3"/>
    <rect x="316" y="59.7"  width="44" height="120.3"/>
    <rect x="378" y="22.4"  width="44" height="157.6"/>
  </g>
  <!-- value labels -->
  <g font-size="10" fill="#0a4d68" text-anchor="middle" font-weight="600">
    <text x="90"  y="128">290</text>
    <text x="152" y="116">350</text>
    <text x="214" y="105">415</text>
    <text x="276" y="79">570</text>
    <text x="338" y="55">720</text>
    <text x="400" y="18">945</text>
  </g>
  <!-- x labels -->
  <g font-size="10" fill="#666" text-anchor="middle">
    <text x="90"  y="195">2020</text>
    <text x="152" y="195">2022</text>
    <text x="214" y="195">2024</text>
    <text x="276" y="195">2026</text>
    <text x="338" y="195">2028</text>
    <text x="400" y="195">2030</text>
  </g>
  <!-- annotation -->
  <line x1="400" y1="22" x2="400" y2="14" stroke="#088395" stroke-width="1"/>
  <text x="395" y="11" font-size="9.5" fill="#088395" font-weight="600" text-anchor="end">~Japan total</text>
</svg>

<div class="sub" style="font-size:0.7em; text-align:right; margin-top:-4px;">Source: IEA, <em>Energy and AI</em>, April 2025</div>
</div>
<div>

<div class="card">
<div class="label"><svg class="icon" viewBox="0 0 24 24"><path d="M13 2L3 14h7l-1 8 10-12h-7l1-8z"/></svg>Data-center load</div>
<div class="value">2×</div>
<div class="note">by 2030 (IEA)</div>
</div>
<div class="card" style="margin-top:8px;">
<div class="label"><svg class="icon" viewBox="0 0 24 24"><polyline points="3 17 9 11 13 15 21 7"/><polyline points="14 7 21 7 21 14"/></svg>Global power demand</div>
<div class="value">~4% / yr</div>
<div class="note">through 2027 (IEA)</div>
</div>
<div class="card" style="margin-top:8px; border-left-color:#c43d3d;">
<div class="label" style="color:#c43d3d;"><svg class="icon" viewBox="0 0 24 24"><path d="M10.29 3.86L1.82 18a2 2 0 001.71 3h16.94a2 2 0 001.71-3L13.71 3.86a2 2 0 00-3.42 0z"/><line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/></svg>Supply shocks</div>
<div class="value" style="color:#c43d3d; font-size:1.1em;">Hormuz · Ukraine · LNG</div>
<div class="note">Geopolitics rewires energy flows in days</div>
</div>
</div>
</div>

> Demand is exploding. Supply is fragile. Prices move in minutes. **See it first or go bankrupt.**

---

## Geopolitics rewires the grid

<div class="kicker">The other half</div>

Demand is one side. Supply is the other — and supply runs through chokepoints.

<div class="cols">
<div class="card">
<div class="label"><svg class="icon" viewBox="0 0 24 24"><path d="M4 19c4-3 12-3 16 0"/><path d="M4 13c4-3 12-3 16 0"/><path d="M4 7c4-3 12-3 16 0"/></svg>Strait of Hormuz</div>
<strong>20% of world's oil. 20% of LNG.</strong>
<div class="note">Iran tensions → freight rates 3× in 48h. Power-gas spreads blow out.</div>
</div>
<div class="card">
<div class="label"><svg class="icon" viewBox="0 0 24 24"><path d="M21 16V8a2 2 0 00-1-1.73l-7-4a2 2 0 00-2 0l-7 4A2 2 0 003 8v8a2 2 0 001 1.73l7 4a2 2 0 002 0l7-4A2 2 0 0021 16z"/></svg>Russia–Ukraine</div>
<strong>European gas permanently restructured.</strong>
<div class="note">Pipeline → LNG cargo. Every disruption ripples to ERCOT and CAISO via global gas pricing.</div>
</div>
<div class="card">
<div class="label"><svg class="icon" viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/></svg>Red Sea · Taiwan · Panama</div>
<strong>Shipping chokepoints multiply.</strong>
<div class="note">Houthi attacks reroute LNG carriers. Drought slows Panama. China–Taiwan tensions sit over LNG flows.</div>
</div>
<div class="card">
<div class="label"><svg class="icon" viewBox="0 0 24 24"><polyline points="3 17 9 11 13 15 21 7"/><polyline points="14 7 21 7 21 14"/></svg>Climate volatility</div>
<strong>Extreme weather hits weekly.</strong>
<div class="note">Texas freeze 2021 = $130B economic damage. Each event = forecast-value spike.</div>
</div>
</div>

> Quarterly PDFs cannot price a world that resets every week.

---

## But forecasts are stuck in the past

<div class="kicker">The gap</div>

<div class="vs">
<div class="a">
<div class="head">Today's intelligence</div>
<div class="body">
One firm. One model. One opinion.<br>
PDF reports. Quarterly.<br>
<strong>$300k per seat.</strong>
</div>
</div>
<div class="mid">VS</div>
<div class="b">
<div class="head">What the market does</div>
<div class="body">
Prices move <strong>every 5 minutes</strong>.<br>
Traders fly blind.<br>
Most volatile decade in 50 years.
</div>
</div>
</div>

> Hundreds of competing models. Scored against reality every hour. Best ones rise automatically.

---

## That's what Flux is

<div class="kicker">The idea</div>

A **marketplace for energy forecasts** built on Bittensor.

<div class="cols">
<div class="card">
<div class="label">Anyone submits</div>
<strong>Quants. Researchers. Universities.</strong>
<div class="note">Fork the reference model. Improve it. Submit.</div>
</div>
<div class="card">
<div class="label">Reality decides</div>
<strong>Public grid data resolves everything.</strong>
<div class="note">Automatic. Deterministic. Final.</div>
</div>
<div class="card">
<div class="label">Customers buy the best</div>
<strong>Traders, utilities, data centers.</strong>
<div class="note">$5k–$100k per month.</div>
</div>
<div class="card">
<div class="label">Network rewards winners</div>
<strong>Better forecasters earn more.</strong>
<div class="note">Bittensor emissions enforce the ranking on-chain.</div>
</div>
</div>

---

## Who pays

<div class="cols-3">
<div class="card-dark">
<div class="label">Crypto-quant</div>
<div class="value" style="font-size:1.4em;">$60k</div>
<span class="pill" style="background:#88c5d4;color:#0a4d68;">/ year</span>
<div class="note" style="color:#a8c8d8; margin-top:8px;">On-chain energy desks · RWA protocols · crypto-native funds</div>
</div>
<div class="card-dark">
<div class="label">Trading desk</div>
<div class="value" style="font-size:1.4em;">$300k</div>
<span class="pill" style="background:#88c5d4;color:#0a4d68;">/ year</span>
<div class="note" style="color:#a8c8d8; margin-top:8px;">Mid-tier ERCOT/CAISO traders · NA + EU</div>
</div>
<div class="card-dark">
<div class="label">Enterprise</div>
<div class="value" style="font-size:1.4em;">$1.2M+</div>
<span class="pill" style="background:#88c5d4;color:#0a4d68;">/ year</span>
<div class="note" style="color:#a8c8d8; margin-top:8px;">Utilities · IPPs · data-center operators</div>
</div>
</div>

<p class="big" style="text-align:center; margin-top:0.6em;">Three-year ARR target: $15–30M</p>

<div class="sub" style="text-align:center;">Wedge: crypto-native energy desks underserved by Bloomberg (too expensive) and Modo (valuation-focused). Funds the first 18 months alone.</div>

---

## Why this works on Bittensor

<div class="kicker">The fit</div>

The hard question for every decentralized AI subnet: *who decides who's right?*

**Flux's answer: the grid.** ERCOT publishes the realized price. The forecast matched or it didn't.

<div class="cols">
<div class="card">
<div class="label">Ground truth</div>
Public ISO data. Deterministic. Final.
</div>
<div class="card">
<div class="label">Compute economics</div>
Forecasting: expensive. Scoring: a single math op. Asymmetric — exactly what Bittensor rewards.
</div>
<div class="card">
<div class="label">Open competition</div>
Many models beat one model. Every time.
</div>
<div class="card">
<div class="label">Composable</div>
Validators sell APIs. Subnet captures emissions. Stakers capture both.
</div>
</div>

---

## v1 — day-ahead power prices

<div class="kicker">Where we start</div>

One market. One product. Prove the mechanism. Start with the grid doubling fastest, with fully open data.

<div class="cols" style="grid-template-columns: 1.3fr 1fr; align-items: center;">
<div>

<svg viewBox="0 0 460 200" style="width:100%; height:auto;">
  <!-- axes -->
  <line x1="50" y1="170" x2="445" y2="170" stroke="#aab"/>
  <line x1="50" y1="20" x2="50" y2="170" stroke="#aab"/>
  <line x1="50" y1="50" x2="445" y2="50" stroke="#e0e4e0"/>
  <line x1="50" y1="80" x2="445" y2="80" stroke="#e0e4e0"/>
  <line x1="50" y1="110" x2="445" y2="110" stroke="#e0e4e0"/>
  <line x1="50" y1="140" x2="445" y2="140" stroke="#e0e4e0"/>
  <!-- y labels: 0 to 220 GW -->
  <text x="44" y="174" text-anchor="end" font-size="10" fill="#666">0</text>
  <text x="44" y="144" text-anchor="end" font-size="10" fill="#666">50</text>
  <text x="44" y="114" text-anchor="end" font-size="10" fill="#666">100</text>
  <text x="44" y="84" text-anchor="end" font-size="10" fill="#666">150</text>
  <text x="44" y="54" text-anchor="end" font-size="10" fill="#666">200</text>
  <text x="14" y="95" text-anchor="middle" font-size="10" fill="#0a4d68" font-weight="600" transform="rotate(-90 14 95)">Peak GW</text>
  <!-- scale: 220 GW => y=14; 0 GW => y=170; px per GW = 156/220 = 0.709 -->
  <!-- Today/historical line, then split into two scenarios -->
  <!-- 2024=85, 2025=94, 2027=125, 2030 high=208, 2030 adj=139 -->
  <!-- High case (top, darker) -->
  <polyline fill="none" stroke="#0a4d68" stroke-width="2.5" points="100,109.7 170,103.3 270,81.3 410,22.5"/>
  <!-- Adjusted case (lighter, dashed) -->
  <polyline fill="none" stroke="#088395" stroke-width="2" stroke-dasharray="5,3" points="170,103.3 270,90 410,71.4"/>
  <!-- points -->
  <g fill="#0a4d68">
    <circle cx="100" cy="109.7" r="3.5"/>
    <circle cx="170" cy="103.3" r="3.5"/>
    <circle cx="270" cy="81.3" r="3.5"/>
    <circle cx="410" cy="22.5" r="4"/>
  </g>
  <g fill="#088395">
    <circle cx="410" cy="71.4" r="4"/>
  </g>
  <!-- value labels -->
  <text x="100" y="103" text-anchor="middle" font-size="10" fill="#0a4d68" font-weight="600">85</text>
  <text x="170" y="97" text-anchor="middle" font-size="10" fill="#0a4d68" font-weight="600">94</text>
  <text x="270" y="75" text-anchor="middle" font-size="10" fill="#0a4d68" font-weight="600">~125</text>
  <text x="410" y="17" text-anchor="middle" font-size="11" fill="#0a4d68" font-weight="700">208</text>
  <text x="415" y="86" font-size="11" fill="#088395" font-weight="700">139</text>
  <!-- x labels -->
  <g font-size="10" fill="#666" text-anchor="middle">
    <text x="100" y="185">2024</text>
    <text x="170" y="185">2025</text>
    <text x="270" y="185">2027</text>
    <text x="410" y="185">2030</text>
  </g>
  <!-- legend -->
  <g font-size="9.5">
    <line x1="60" y1="33" x2="80" y2="33" stroke="#0a4d68" stroke-width="2.5"/>
    <text x="84" y="36" fill="#0a4d68">High case</text>
    <line x1="160" y1="33" x2="180" y2="33" stroke="#088395" stroke-width="2" stroke-dasharray="5,3"/>
    <text x="184" y="36" fill="#088395">Adjusted (−50% data-center haircut)</text>
  </g>
</svg>

<div class="sub" style="font-size:0.7em; text-align:right; margin-top:-4px;">Source: ERCOT 2025 Long-Term Load Forecast (LTLF3)</div>
</div>
<div>
<div class="card-dark">
<div class="label">2025 forecast</div>
<div class="value">94 GW</div>
</div>
<div class="card-dark" style="margin-top:10px;">
<div class="label">2030 high case</div>
<div class="value">208 GW</div>
</div>
</div>
</div>

<div class="cols-3" style="margin-top: 0.4em;">
<div class="card">
<div class="label"><svg class="icon" viewBox="0 0 24 24"><rect x="3" y="3" width="18" height="18" rx="2"/><path d="M7 12h10M12 7v10"/></svg>Open data</div>
<div class="note" style="font-size:0.85em;">Free for validators · zero licensing risk</div>
</div>
<div class="card">
<div class="label"><svg class="icon" viewBox="0 0 24 24"><polyline points="3 17 9 11 13 15 21 7"/></svg>Highest volatility</div>
<div class="note" style="font-size:0.85em;">Biggest absolute forecast value</div>
</div>
<div class="card">
<div class="label"><svg class="icon" viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"/><circle cx="12" cy="12" r="6"/><circle cx="12" cy="12" r="2"/></svg>Easy first target</div>
<div class="note" style="font-size:0.85em;">Weak public baseline · ≥20% beat</div>
</div>
</div>

---

## What miners do

<div class="kicker">The supply side</div>

Submit **probabilistic forecasts** across four horizons: 5min · 1h · 24h · 1 week.

```python
Forecast(horizon, target_time, mean, p10, p50, p90)
```

<div class="cols">
<div class="card">
<div class="label">Sealed submission</div>
Forecasts hash-committed before each window. Reveal after.
</div>
<div class="card">
<div class="label">Anti-copying</div>
Peers invisible until commitment lands.
</div>
<div class="card">
<div class="label">Reference model</div>
XGBoost on public load + weather + gas. Forkable.
</div>
<div class="card">
<div class="label">Sybil cost</div>
Compute + data-feed fees self-bankrupt fakes.
</div>
</div>

---

## What validators do

<div class="kicker">The scoring side</div>

<div class="step">
<div class="step-num">1</div>
<div class="step-body"><strong>Pull realized prices</strong>from the grid's public API.</div>
</div>
<div class="step">
<div class="step-num">2</div>
<div class="step-body"><strong>Score each miner.</strong><code>score(m) = 0.5·Skill + 0.3·Calibration + 0.2·Breadth</code></div>
</div>
<div class="step">
<div class="step-num">3</div>
<div class="step-body"><strong>Commit-reveal weights.</strong>5-tempo delay defeats copiers.</div>
</div>
<div class="step">
<div class="step-num">4</div>
<div class="step-body"><strong>Serve forecast API.</strong>$5k–$100k / month.</div>
</div>

| Signal | Rewards | Resists |
|---|---|---|
| **Skill** | Beating the naive baseline | Overfitting — baseline rotates weekly |
| **Calibration** | Honest uncertainty (CRPS) | Tight-but-wrong forecasts |
| **Breadth** | Depth across horizons | Single-horizon specialists |

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

<div class="cols-3">
<div class="card">
<div class="label">Differentiation</div>
Three independent signals split four miners into four quality tiers.
</div>
<div class="card">
<div class="label">Auto-exclusion</div>
Forecasters worse than naive baseline are zeroed before diluting consensus.
</div>
<div class="card">
<div class="label">On-chain proof</div>
Weight commit hash settles in 5 tempos — copy attacks fail.
</div>
</div>

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

<div class="flow">
<div class="flow-box">
<div class="t">Miners</div>
<div class="d">Probabilistic forecasts<br>multi-horizon · sealed</div>
</div>
<div class="flow-arrow">▶</div>
<div class="flow-box">
<div class="t">Validators</div>
<div class="d">Skill + CRPS + Breadth<br>+ enterprise API</div>
</div>
<div class="flow-arrow">▶</div>
<div class="flow-box">
<div class="t">Yuma · 41/41/18</div>
<div class="d">Emissions enforce ranking<br>commit-reveal weights</div>
</div>
</div>

<div style="text-align:center; color:#088395; font-weight:600; margin: 1em 0;">▲ ISO ground truth · weather · AIS feeds · EIA ▲</div>

<div class="cols">
<div class="card">
<div class="label">Closed loop</div>
Better forecasts → more API revenue → larger emissions → better forecasts.
</div>
<div class="card">
<div class="label">Single source of truth</div>
Public grid data. No human DAO. No LLM judge. No off-chain attestation.
</div>
</div>

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

<div class="vs">
<div class="a">
<div class="head">Modo Energy</div>
<div class="body">
£25M Series B · Dec 2025<br>
<strong>Single proprietary model</strong><br>
Quarterly valuations<br>
Electrification assets only
</div>
</div>
<div class="mid">VS</div>
<div class="b">
<div class="head">Flux</div>
<div class="body">
Open-competition subnet<br>
<strong>Many competing models</strong><br>
Live forecasts every 72 min<br>
All energy commodities
</div>
</div>
</div>

| Axis | Position |
|---|---|
| **Product** | Three priced tiers · existing customer budgets · $15–30M ARR path |
| **Organization** | Open competition · zero DAO bootstrap risk |
| **Verification** | Public grid data only · zero humans in scoring loop |
| **Game-theory** | Eight named defenses · each maps to a hyperparameter or capital commitment |

---

<!-- _class: lead -->

# Flux

## Real-time energy intelligence, Bittensor-native

<br>

`github.com/edwardtay/bittensor-flux`
