<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>GES Tech Compensation Calculator</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500;600&family=IBM+Plex+Sans:wght@300;400;500;600&display=swap');
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  :root {
    --bg: #0f0f0f; --surface: #181818; --surface2: #222222;
    --border: #2e2e2e; --border2: #3a3a3a;
    --text: #f0f0f0; --text2: #a0a0a0; --text3: #606060;
    --green: #22c55e; --green-bg: #052b12; --green-border: #134d25;
    --blue: #3b82f6; --blue-bg: #001022; --blue-border: #1e3a5f;
    --amber: #f59e0b; --amber-bg: #2a1f05; --amber-border: #4d3a0a;
    --red: #ef4444; --red-bg: #2a0505; --red-border: #4d1010;
    --accent: #3b82f6; --accent-dim: #1e3a5f;
    --summer: #f97316; --summer-bg: #2a1200; --summer-border: #6b3000;
    --winter: #38bdf8; --winter-bg: #001a2a; --winter-border: #0c4a6e;
  }
  body { font-family: 'IBM Plex Sans', sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; padding: 32px 24px; font-size: 14px; line-height: 1.5; }
  header { display: flex; align-items: baseline; gap: 16px; margin-bottom: 32px; padding-bottom: 20px; border-bottom: 1px solid var(--border); }
  header h1 { font-family: 'IBM Plex Mono', monospace; font-size: 16px; font-weight: 600; letter-spacing: 0.05em; }
  header span { font-size: 12px; color: var(--text3); font-family: 'IBM Plex Mono', monospace; }
  .layout { display: grid; grid-template-columns: 360px 1fr; gap: 20px; align-items: start; }
  .panel { background: var(--surface); border: 1px solid var(--border); border-radius: 6px; padding: 20px; margin-bottom: 16px; }
  .panel:last-child { margin-bottom: 0; }
  .panel-title { font-family: 'IBM Plex Mono', monospace; font-size: 11px; font-weight: 600; letter-spacing: 0.1em; color: var(--text3); text-transform: uppercase; margin-bottom: 16px; padding-bottom: 10px; border-bottom: 1px solid var(--border); }
 
  /* season tabs */
  .season-toggle { display: grid; grid-template-columns: 1fr 1fr; gap: 0; border: 1px solid var(--border2); border-radius: 5px; overflow: hidden; margin-bottom: 18px; }
  .season-btn { background: var(--surface2); border: none; cursor: pointer; font-family: 'IBM Plex Mono', monospace; font-size: 12px; font-weight: 600; letter-spacing: 0.05em; padding: 10px 12px; color: var(--text3); display: flex; align-items: center; justify-content: center; gap: 7px; transition: background 0.15s, color 0.15s; }
  .season-btn.active-summer { background: var(--summer-bg); color: var(--summer); }
  .season-btn.active-winter { background: var(--winter-bg); color: var(--winter); }
  .sdot { width: 8px; height: 8px; border-radius: 50%; }
 
  /* params grid: two columns summer/winter */
  .params-header { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 8px; margin-bottom: 8px; align-items: center; }
  .params-header span { font-size: 11px; font-family: 'IBM Plex Mono', monospace; color: var(--text3); text-align: center; }
  .params-header span:first-child { text-align: left; }
  .param-row { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 8px; align-items: center; margin-bottom: 12px; }
  .param-label { font-size: 13px; color: var(--text2); }
  .param-col { display: flex; flex-direction: column; gap: 4px; }
  .param-val { font-family: 'IBM Plex Mono', monospace; font-size: 12px; font-weight: 500; text-align: center; }
  .param-val.summer-val { color: var(--summer); }
  .param-val.winter-val { color: var(--winter); }
 
  input[type=range] { -webkit-appearance: none; appearance: none; width: 100%; height: 3px; background: var(--border2); border-radius: 2px; outline: none; cursor: pointer; }
  input[type=range].summer-range::-webkit-slider-thumb { -webkit-appearance: none; width: 13px; height: 13px; border-radius: 50%; background: var(--summer); cursor: pointer; border: 2px solid var(--bg); }
  input[type=range].summer-range::-moz-range-thumb { width: 13px; height: 13px; border-radius: 50%; background: var(--summer); cursor: pointer; border: 2px solid var(--bg); }
  input[type=range].winter-range::-webkit-slider-thumb { -webkit-appearance: none; width: 13px; height: 13px; border-radius: 50%; background: var(--winter); cursor: pointer; border: 2px solid var(--bg); }
  input[type=range].winter-range::-moz-range-thumb { width: 13px; height: 13px; border-radius: 50%; background: var(--winter); cursor: pointer; border: 2px solid var(--bg); }
  input[type=range].shared-range::-webkit-slider-thumb { -webkit-appearance: none; width: 13px; height: 13px; border-radius: 50%; background: var(--accent); cursor: pointer; border: 2px solid var(--bg); }
  input[type=range].shared-range::-moz-range-thumb { width: 13px; height: 13px; border-radius: 50%; background: var(--accent); cursor: pointer; border: 2px solid var(--bg); }
 
  .shared-field { margin-bottom: 14px; }
  .shared-field-label { display: flex; justify-content: space-between; align-items: center; margin-bottom: 6px; }
  .shared-field-label span:first-child { font-size: 13px; color: var(--text2); }
  .shared-val { font-family: 'IBM Plex Mono', monospace; font-size: 13px; font-weight: 500; color: var(--text); }
 
  input[type=number] { background: var(--surface2); border: 1px solid var(--border2); border-radius: 4px; color: var(--text); font-family: 'IBM Plex Mono', monospace; font-size: 12px; padding: 5px 8px; width: 100%; outline: none; }
  input[type=number]:focus { border-color: var(--accent); }
  input[type=number]:disabled { color: var(--text3); background: var(--surface); cursor: not-allowed; }
 
  /* tier rows */
  .tier-grid-header { display: grid; grid-template-columns: 1fr 1fr 1fr 28px; gap: 5px; margin-bottom: 5px; }
  .tier-grid-header span { font-size: 10px; color: var(--text3); font-family: 'IBM Plex Mono', monospace; }
  .tier-row { display: grid; grid-template-columns: 1fr 1fr 1fr 28px; gap: 5px; align-items: center; margin-bottom: 5px; }
  .winter-input { background: var(--winter-bg); border: 1px solid var(--winter-border); border-radius: 4px; color: var(--winter); font-family: 'IBM Plex Mono', monospace; font-size: 12px; padding: 5px 8px; width: 100%; outline: none; }
  .winter-input:focus { border-color: var(--winter); }
  .winter-input:disabled { color: var(--text3); background: var(--surface); border-color: var(--border); cursor: not-allowed; }
  .remove-btn { background: none; border: 1px solid var(--border2); border-radius: 4px; color: var(--text3); cursor: pointer; font-size: 16px; height: 28px; width: 28px; display: flex; align-items: center; justify-content: center; transition: border-color 0.15s, color 0.15s; }
  .remove-btn:hover { border-color: var(--red); color: var(--red); }
  .add-tier-btn { background: none; border: 1px dashed var(--border2); border-radius: 4px; color: var(--text3); cursor: pointer; font-size: 12px; font-family: 'IBM Plex Mono', monospace; padding: 6px 12px; width: 100%; margin-top: 2px; transition: border-color 0.15s, color 0.15s; }
  .add-tier-btn:hover { border-color: var(--accent); color: var(--accent); }
 
  /* metrics */
  .summary-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 14px; }
  .metric { background: var(--surface2); border: 1px solid var(--border); border-radius: 5px; padding: 12px; }
  .metric-label { font-size: 11px; color: var(--text3); font-family: 'IBM Plex Mono', monospace; text-transform: uppercase; letter-spacing: 0.06em; margin-bottom: 4px; }
  .metric-val { font-family: 'IBM Plex Mono', monospace; font-size: 20px; font-weight: 600; color: var(--text); }
  .metric-sub { font-size: 11px; color: var(--text3); margin-top: 2px; }
 
  .status-bar { background: var(--surface2); border: 1px solid var(--border); border-radius: 5px; padding: 12px; margin-bottom: 12px; }
  .status-bar-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px; }
  .sbl { font-size: 12px; color: var(--text2); }
  .sbp { font-family: 'IBM Plex Mono', monospace; font-size: 14px; font-weight: 600; }
  .bar-track { height: 6px; background: var(--border2); border-radius: 3px; position: relative; overflow: visible; }
  .bar-fill { height: 100%; border-radius: 3px; transition: width 0.25s, background 0.25s; }
  .bar-tick { position: absolute; top: -3px; width: 1px; height: 12px; background: var(--border2); }
  .btl { position: absolute; top: 10px; font-size: 10px; font-family: 'IBM Plex Mono', monospace; color: var(--text3); transform: translateX(-50%); white-space: nowrap; }
  .bar-zone-labels { display: flex; justify-content: space-between; margin-top: 18px; font-size: 10px; font-family: 'IBM Plex Mono', monospace; color: var(--text3); }
  .status-msg { border-radius: 4px; padding: 8px 12px; font-size: 12px; font-family: 'IBM Plex Mono', monospace; }
  .msg-ok   { background: var(--green-bg); border: 1px solid var(--green-border); color: var(--green); }
  .msg-info { background: var(--blue-bg);  border: 1px solid var(--blue-border);  color: var(--blue); }
  .msg-warn { background: var(--amber-bg); border: 1px solid var(--amber-border); color: var(--amber); }
  .msg-bad  { background: var(--red-bg);   border: 1px solid var(--red-border);   color: var(--red); }
 
  /* right col */
  .period-tabs { display: flex; gap: 2px; background: var(--surface2); border: 1px solid var(--border); border-radius: 5px; padding: 3px; width: fit-content; margin-bottom: 16px; }
  .tab-btn { background: none; border: none; border-radius: 3px; color: var(--text3); cursor: pointer; font-family: 'IBM Plex Mono', monospace; font-size: 11px; font-weight: 500; letter-spacing: 0.04em; padding: 5px 14px; transition: background 0.15s, color 0.15s; }
  .tab-btn.active { background: var(--accent-dim); color: var(--accent); }
 
  table { width: 100%; border-collapse: collapse; }
  th { font-family: 'IBM Plex Mono', monospace; font-size: 10px; font-weight: 600; letter-spacing: 0.08em; text-transform: uppercase; color: var(--text3); text-align: right; padding: 8px 10px; border-bottom: 1px solid var(--border); white-space: nowrap; }
  th:first-child { text-align: left; }
  td { font-family: 'IBM Plex Mono', monospace; font-size: 12px; text-align: right; padding: 9px 10px; border-bottom: 1px solid var(--border); color: var(--text2); white-space: nowrap; }
  td:first-child { text-align: left; color: var(--text); }
  tr:last-child td { border-bottom: none; }
  tr:hover td { background: var(--surface2); }
 
  .badge { display: inline-block; font-family: 'IBM Plex Mono', monospace; font-size: 11px; font-weight: 600; padding: 2px 7px; border-radius: 3px; }
  .b-ok   { background: var(--green-bg); color: var(--green); border: 1px solid var(--green-border); }
  .b-info { background: var(--blue-bg);  color: var(--blue);  border: 1px solid var(--blue-border); }
  .b-warn { background: var(--amber-bg); color: var(--amber); border: 1px solid var(--amber-border); }
  .b-bad  { background: var(--red-bg);   color: var(--red);   border: 1px solid var(--red-border); }
  .tier-badge { display: inline-block; font-family: 'IBM Plex Mono', monospace; font-size: 10px; padding: 1px 5px; border-radius: 3px; background: var(--surface2); border: 1px solid var(--border2); color: var(--text3); }
  .season-pill { display: inline-flex; align-items: center; gap: 5px; font-family: 'IBM Plex Mono', monospace; font-size: 11px; font-weight: 600; padding: 3px 9px; border-radius: 3px; }
  .pill-summer { background: var(--summer-bg); color: var(--summer); border: 1px solid var(--summer-border); }
  .pill-winter { background: var(--winter-bg); color: var(--winter); border: 1px solid var(--winter-border); }
 
  @media (max-width: 960px) { .layout { grid-template-columns: 1fr; } }
</style>
</head>
<body>
 
<header>
  <h1>GES // TECH COMP CALCULATOR</h1>
  <span>Gold Eagle Services · seasonal · all figures monthly</span>
</header>
 
<div class="layout">
<!-- LEFT -->
<div>
 
  <div class="panel">
    <div class="panel-title">Shared parameters</div>
    <div class="shared-field">
      <div class="shared-field-label"><span>Hourly rate</span><span class="shared-val" id="lbl-hourly">$33 / hr</span></div>
      <input type="range" class="shared-range" id="s-hourly" min="20" max="55" step="1" value="33">
    </div>
    <div class="shared-field" style="margin-bottom:0">
      <div class="shared-field-label"><span>Burden rate multiplier</span><span class="shared-val" id="lbl-burden">1.25×</span></div>
      <input type="range" class="shared-range" id="s-burden" min="1.00" max="1.60" step="0.01" value="1.25">
    </div>
    <div style="font-size:11px;color:var(--text3);font-family:'IBM Plex Mono',monospace;margin-top:6px;line-height:1.6">
      FICA (7.65%) + workers comp (~5%) + benefits. Typical HVAC range: 1.18–1.35.
    </div>
  </div>
 
  <div class="panel">
    <div class="panel-title">Seasonal parameters</div>
    <div class="params-header">
      <span></span>
      <span style="color:var(--summer)">▲ Summer<br>May–Oct</span>
      <span style="color:var(--winter)">❄ Winter<br>Nov–Apr</span>
    </div>
    <div class="param-row">
      <span class="param-label">Reg. hours / wk</span>
      <div class="param-col">
        <span class="param-val summer-val" id="lbl-s-hours">40 hrs</span>
        <input type="range" class="summer-range" id="s-hours-sum" min="32" max="50" step="1" value="40">
      </div>
      <div class="param-col">
        <span class="param-val winter-val" id="lbl-w-hours">40 hrs</span>
        <input type="range" class="winter-range" id="s-hours-win" min="32" max="50" step="1" value="40">
      </div>
    </div>
    <div class="param-row">
      <span class="param-label">OT hours / wk</span>
      <div class="param-col">
        <span class="param-val summer-val" id="lbl-s-ot">0 hrs OT</span>
        <input type="range" class="summer-range" id="s-ot-sum" min="0" max="20" step="1" value="0">
      </div>
      <div class="param-col">
        <span class="param-val winter-val" id="lbl-w-ot">0 hrs OT</span>
        <input type="range" class="winter-range" id="s-ot-win" min="0" max="20" step="1" value="0">
      </div>
    </div>
    <div class="param-row">
      <span class="param-label">Calls / week</span>
      <div class="param-col">
        <span class="param-val summer-val" id="lbl-s-calls">25</span>
        <input type="range" class="summer-range" id="s-calls-sum" min="5" max="50" step="1" value="25">
      </div>
      <div class="param-col">
        <span class="param-val winter-val" id="lbl-w-calls">15</span>
        <input type="range" class="winter-range" id="s-calls-win" min="5" max="50" step="1" value="15">
      </div>
    </div>
    <div class="param-row">
      <span class="param-label">Avg ticket</span>
      <div class="param-col">
        <span class="param-val summer-val" id="lbl-s-ticket">$700</span>
        <input type="range" class="summer-range" id="s-ticket-sum" min="200" max="2000" step="50" value="700">
      </div>
      <div class="param-col">
        <span class="param-val winter-val" id="lbl-w-ticket">$600</span>
        <input type="range" class="winter-range" id="s-ticket-win" min="200" max="2000" step="50" value="600">
      </div>
    </div>
    <div style="font-size:11px;color:var(--text3);font-family:'IBM Plex Mono',monospace;margin-top:-4px;line-height:1.6">
      OT paid at 1.5× · reg. hours capped at 40 for OT threshold
    </div>
  </div>
 
  <div class="panel">
    <div class="panel-title">Commission tiers — monthly revenue floors</div>
    <div class="tier-grid-header">
      <span style="color:var(--summer)">Summer floor ($)</span>
      <span style="color:var(--winter)">Winter floor ($)</span>
      <span>Comm. %</span>
      <span></span>
    </div>
    <div id="tier-rows"></div>
    <button class="add-tier-btn" onclick="addTier()">+ add tier</button>
    <div style="margin-top:10px;font-size:11px;color:var(--text3);font-family:'IBM Plex Mono',monospace;line-height:1.7">
      Summer and winter floors are independently editable.<br>
      Commission % is the same year-round.
    </div>
  </div>
 
  <div class="panel">
    <div class="panel-title">
      Active season —
      <span id="live-season-pill" class="season-pill pill-summer" style="margin-left:4px">▲ Summer</span>
    </div>
 
    <div class="season-toggle" style="margin-bottom:16px">
      <button class="season-btn active-summer" id="btn-summer" onclick="setSeason('summer')">
        <span class="sdot" style="background:var(--summer)"></span>SUMMER · May–Oct
      </button>
      <button class="season-btn" id="btn-winter" onclick="setSeason('winter')">
        <span class="sdot" style="background:var(--winter)"></span>WINTER · Nov–Apr
      </button>
    </div>
 
    <div class="summary-grid">
      <div class="metric"><div class="metric-label">Monthly revenue</div><div class="metric-val" id="m-rev">—</div><div class="metric-sub" id="m-rev-sub">—</div></div>
      <div class="metric"><div class="metric-label">Base hourly pay</div><div class="metric-val" id="m-hp">—</div><div class="metric-sub" id="m-hp-sub">—</div></div>
      <div class="metric"><div class="metric-label">Commission</div><div class="metric-val" id="m-comm">—</div><div class="metric-sub" id="m-comm-sub">—</div></div>
      <div class="metric"><div class="metric-label">Burdened total (GES cost)</div><div class="metric-val" id="m-total" style="color:var(--accent)">—</div><div class="metric-sub" id="m-total-sub">—</div></div>
    </div>
    <div style="background:var(--surface2);border:1px solid var(--green-border);border-radius:5px;padding:12px;margin-bottom:14px;display:flex;justify-content:space-between;align-items:center">
      <div>
        <div style="font-size:11px;color:var(--green);font-family:'IBM Plex Mono',monospace;text-transform:uppercase;letter-spacing:.06em;margin-bottom:3px">Tech net pay (take-home)</div>
        <div style="font-size:11px;color:var(--text3);font-family:'IBM Plex Mono',monospace" id="m-net-sub">—</div>
      </div>
      <div style="font-family:'IBM Plex Mono',monospace;font-size:26px;font-weight:600;color:var(--green)" id="m-net">—</div>
    </div>
 
    <div class="status-bar">
      <div class="status-bar-header">
        <span class="sbl">Labor % of revenue</span>
        <span class="sbp" id="pct-label">—</span>
      </div>
      <div class="bar-track">
        <div class="bar-fill" id="bar-fill" style="width:0%"></div>
        <div class="bar-tick" style="left:35%"></div><div class="btl" style="left:35%">14%</div>
        <div class="bar-tick" style="left:50%"></div><div class="btl" style="left:50%">20%</div>
        <div class="bar-tick" style="left:62.5%"></div><div class="btl" style="left:62.5%">25%</div>
      </div>
      <div class="bar-zone-labels"><span>0%</span><span>ideal ▶</span><span>ok</span><span>watch</span><span>40%</span></div>
    </div>
    <div id="status-msg" class="status-msg msg-ok">—</div>
  </div>
 
</div>
 
<!-- RIGHT -->
<div>
 
  <div class="panel">
    <div class="panel-title">Side-by-side — summer vs winter at current inputs</div>
    <table>
      <thead>
        <tr>
          <th>Season</th>
          <th style="text-align:right">Calls/wk</th>
          <th style="text-align:right">Avg ticket</th>
          <th style="text-align:right">Mo. revenue</th>
          <th style="text-align:right">Tier</th>
          <th style="text-align:right">Mo. comm.</th>
          <th style="text-align:right">Base pay</th>
          <th style="text-align:right">Burdened pay</th>
          <th style="text-align:right">Mo. total</th>
          <th style="text-align:right">Bi-wkly check</th>
          <th style="text-align:right">Labor %</th>
        </tr>
      </thead>
      <tbody id="compare-tbody"></tbody>
    </table>
  </div>
 
  <div class="panel">
    <div class="panel-title">Annual projection — 6 months each</div>
    <div id="annual-grid" style="display:grid;grid-template-columns:repeat(4,1fr);gap:10px"></div>
  </div>
 
  <div class="panel">
    <div class="panel-title">Detailed view — <span id="detail-season-label">Summer · May–Oct</span></div>
 
    <div style="display:flex;align-items:center;gap:12px;margin-bottom:16px;flex-wrap:wrap">
      <div class="period-tabs" style="margin-bottom:0">
        <button class="tab-btn" onclick="setTab('weekly',this)">Weekly</button>
        <button class="tab-btn" onclick="setTab('biweekly',this)">Bi-weekly</button>
        <button class="tab-btn active" onclick="setTab('monthly',this)">Monthly</button>
      </div>
      <div style="display:flex;gap:6px">
        <span class="season-pill pill-summer" id="det-summer-pill" style="cursor:pointer" onclick="setSeason('summer')">▲ Summer</span>
        <span class="season-pill pill-winter" id="det-winter-pill" style="cursor:pointer;opacity:0.35" onclick="setSeason('winter')">❄ Winter</span>
      </div>
    </div>
 
    <table>
      <thead>
        <tr>
          <th></th>
          <th id="th-rev" style="text-align:right">Monthly revenue</th>
          <th style="text-align:right">Tier</th>
          <th id="th-hp" style="text-align:right">Hourly pay</th>
          <th id="th-comm" style="text-align:right">Commission</th>
          <th id="th-total" style="text-align:right">Total pay</th>
          <th style="text-align:right">Bi-wkly check</th>
          <th style="text-align:right">Labor %</th>
        </tr>
      </thead>
      <tbody id="detail-tbody"></tbody>
    </table>
  </div>
 
</div>
</div>
 
<script>
// 52 weeks/yr split evenly: 26 summer, 26 winter, 6 months each
// Exact weeks per month per season: 26/6 = 4.333... same as before but
// we now use exact season totals for annual to avoid floating point drift.
// Monthly = weekly * (26/6) for display; annual = weekly * 26 per season.
const WPM = 26 / 6;   // ~4.333, exact fraction
const SEASON_WEEKS = 26;
let tiers = [
  { summerFloor: 0,      winterFloor: 0,      rate: 0  },
  { summerFloor: 52000,  winterFloor: 33800,  rate: 5  },
  { summerFloor: 75800,  winterFloor: 49300,  rate: 7  },
  { summerFloor: 95300,  winterFloor: 61900,  rate: 10 }
];
let activeSeason = 'summer';
let activePeriod = 'monthly';
 
function winterFloor(t) { return t.winterFloor; }
 
function fmt(n) { return '$' + Math.round(n).toLocaleString('en-US'); }
function fmtPct(n) { return (Math.round(n * 10) / 10).toFixed(1) + '%'; }
 
function getActiveTier(moRev, season) {
  let active = tiers[0];
  tiers.forEach(t => {
    const fl = season === 'summer' ? t.summerFloor : winterFloor(t);
    if (moRev >= fl) active = t;
  });
  return active;
}
 
function colorForPct(lp) {
  if (lp <= 14) return { bar: '#22c55e', pct: '#22c55e', badge: 'b-ok',   msg: 'msg-ok'   };
  if (lp <= 20) return { bar: '#3b82f6', pct: '#3b82f6', badge: 'b-info', msg: 'msg-info'  };
  if (lp <= 25) return { bar: '#f59e0b', pct: '#f59e0b', badge: 'b-warn', msg: 'msg-warn'  };
  return           { bar: '#ef4444', pct: '#ef4444', badge: 'b-bad',  msg: 'msg-bad'   };
}
 
function compute(calls, ticket, hourly, hours, otHours, burden, season) {
  const wkRev      = calls * ticket;
  const moRev      = wkRev * WPM;
  // weekly base pay: regular + OT at 1.5x
  const wkHpRaw    = (hourly * Math.min(hours, 40)) + (hourly * 1.5 * otHours);
  const moHpRaw    = wkHpRaw * WPM;
  const moHpBurd   = moHpRaw * burden;
  const tier       = getActiveTier(moRev, season);
  const moComm     = tier.rate > 0 ? moRev * (tier.rate / 100) : 0;
  const moTotRaw   = moHpRaw  + moComm;
  const moTotBurd  = moHpBurd + moComm;
  const lpRaw      = moRev > 0 ? (moTotRaw  / moRev) * 100 : 0;
  const lpBurd     = moRev > 0 ? (moTotBurd / moRev) * 100 : 0;
  const moNetPay   = moTotRaw;  // what tech takes home: base wage + commission, no burden
  return { wkRev, moRev, moHpRaw, moHpBurd, moComm, moTotRaw, moTotBurd, moNetPay, lpRaw, lpBurd, tier, wkHpRaw };
}
 
function setSeason(s) {
  activeSeason = s;
  document.getElementById('btn-summer').className = 'season-btn' + (s === 'summer' ? ' active-summer' : '');
  document.getElementById('btn-winter').className = 'season-btn' + (s === 'winter' ? ' active-winter' : '');
  document.getElementById('det-summer-pill').style.opacity = s === 'summer' ? '1' : '0.35';
  document.getElementById('det-winter-pill').style.opacity = s === 'winter' ? '1' : '0.35';
  const pill = document.getElementById('live-season-pill');
  const dlbl = document.getElementById('detail-season-label');
  if (s === 'summer') {
    pill.className = 'season-pill pill-summer'; pill.textContent = '▲ Summer';
    dlbl.textContent = 'Summer · May–Oct';
  } else {
    pill.className = 'season-pill pill-winter'; pill.textContent = '❄ Winter';
    dlbl.textContent = 'Winter · Nov–Apr';
  }
  calc();
}
 
function setTab(period, btn) {
  activePeriod = period;
  document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
  calc();
}
 
function addTier() { tiers.push({ summerFloor: 0, winterFloor: 0, rate: 0 }); renderTiers(); calc(); }
function removeTier(i) { if (tiers.length <= 1) return; tiers.splice(i, 1); renderTiers(); calc(); }
 
function renderTiers() {
  const el = document.getElementById('tier-rows');
  el.innerHTML = '';
  tiers.forEach((t, i) => {
    const row = document.createElement('div');
    row.className = 'tier-row';
    const wf = t.winterFloor;
    row.innerHTML = `
      <input type="number" min="0" step="1000" value="${t.summerFloor}"
        ${i === 0 ? 'disabled placeholder="Any"' : ''}
        onchange="tiers[${i}].summerFloor=+this.value; calc()">
      <input class="winter-input" type="number" min="0" step="1000" value="${wf}"
        ${i === 0 ? 'disabled placeholder="Any"' : ''}
        onchange="tiers[${i}].winterFloor=+this.value; calc()">
      <input type="number" min="0" max="100" step="0.5" value="${t.rate}"
        onchange="tiers[${i}].rate=+this.value; calc()">
      <button class="remove-btn" onclick="removeTier(${i})" ${tiers.length <= 1 ? 'disabled' : ''}>×</button>
    `;
    el.appendChild(row);
  });
}
 
function dispVal(mo, wk, period) {
  if (period === 'weekly')    return wk;
  if (period === 'biweekly')  return wk * 2;
  return mo;
}
 
function calc() {
  const hourly   = +document.getElementById('s-hourly').value;
  const burden   = +document.getElementById('s-burden').value;
  const sHours   = +document.getElementById('s-hours-sum').value;
  const wHours   = +document.getElementById('s-hours-win').value;
  const sOt      = +document.getElementById('s-ot-sum').value;
  const wOt      = +document.getElementById('s-ot-win').value;
  const sCalls   = +document.getElementById('s-calls-sum').value;
  const wCalls   = +document.getElementById('s-calls-win').value;
  const sTicket  = +document.getElementById('s-ticket-sum').value;
  const wTicket  = +document.getElementById('s-ticket-win').value;
 
  document.getElementById('lbl-hourly').textContent   = '$' + hourly + ' / hr';
  document.getElementById('lbl-burden').textContent   = burden.toFixed(2) + '×';
  document.getElementById('lbl-s-hours').textContent  = sHours + ' hrs';
  document.getElementById('lbl-w-hours').textContent  = wHours + ' hrs';
  document.getElementById('lbl-s-ot').textContent     = sOt + ' hrs OT';
  document.getElementById('lbl-w-ot').textContent     = wOt + ' hrs OT';
  document.getElementById('lbl-s-calls').textContent  = sCalls;
  document.getElementById('lbl-w-calls').textContent  = wCalls;
  document.getElementById('lbl-s-ticket').textContent = '$' + sTicket;
  document.getElementById('lbl-w-ticket').textContent = '$' + wTicket;
 
  const hours   = activeSeason === 'summer' ? sHours  : wHours;
  const otHours = activeSeason === 'summer' ? sOt     : wOt;
  const calls   = activeSeason === 'summer' ? sCalls  : wCalls;
  const ticket  = activeSeason === 'summer' ? sTicket : wTicket;
 
  const d = compute(calls, ticket, hourly, hours, otHours, burden, activeSeason);
  const colors = colorForPct(d.lpBurd);
 
  document.getElementById('m-rev').textContent       = fmt(d.moRev);
  document.getElementById('m-rev-sub').textContent   = fmt(d.wkRev) + ' / wk';
  document.getElementById('m-hp').textContent        = fmt(d.moHpRaw);
  document.getElementById('m-hp-sub').textContent    = fmt(d.moHpBurd) + ' burdened';
  document.getElementById('m-comm').textContent      = fmt(d.moComm);
  document.getElementById('m-comm-sub').textContent  = d.tier.rate > 0 ? d.tier.rate + '% tier active' : 'below floor';
  document.getElementById('m-total').textContent     = fmt(d.moTotBurd);
  document.getElementById('m-total-sub').textContent = fmt(d.moTotBurd / 2) + ' bi-wkly cost to GES';
  document.getElementById('m-net').textContent       = fmt(d.moNetPay);
  document.getElementById('m-net-sub').textContent   = fmt(d.moNetPay / 2) + ' bi-wkly check  ·  ' + fmt(d.moComm) + ' commission included';
 
  const pctEl = document.getElementById('pct-label');
  pctEl.textContent = fmtPct(d.lpBurd) + ' burdened'; pctEl.style.color = colors.pct;
  const barEl = document.getElementById('bar-fill');
  barEl.style.width = Math.min(d.lpBurd / 40 * 100, 100) + '%';
  barEl.style.background = colors.bar;
 
  const msgEl = document.getElementById('status-msg');
  msgEl.className = 'status-msg ' + colors.msg;
  const fl = activeSeason === 'summer' ? d.tier.summerFloor : winterFloor(d.tier);
  const tierInfo = d.tier.rate > 0 ? `${d.tier.rate}% commission active (floor: ${fmt(fl)}/mo).` : 'Below commission floor — hourly only.';
  const otNote = otHours > 0 ? ` Includes ${otHours}h OT/wk at 1.5×.` : '';
  if      (d.lpBurd <= 14) msgEl.textContent = `✓ Burdened labor at ${fmtPct(d.lpBurd)} — ideal.${otNote} ${tierInfo}`;
  else if (d.lpBurd <= 20) msgEl.textContent = `✓ Burdened labor at ${fmtPct(d.lpBurd)} — healthy.${otNote} ${tierInfo}`;
  else if (d.lpBurd <= 25) msgEl.textContent = `⚠ Burdened labor at ${fmtPct(d.lpBurd)} — above 20% target.${otNote} ${tierInfo}`;
  else                     msgEl.textContent = `✗ Burdened labor at ${fmtPct(d.lpBurd)} — above 25% ceiling.${otNote} ${tierInfo}`;
 
  // compare table
  const cTbody = document.getElementById('compare-tbody');
  cTbody.innerHTML = '';
  [['summer', sCalls, sTicket, sHours, sOt, '▲ Summer', 'pill-summer'], ['winter', wCalls, wTicket, wHours, wOt, '❄ Winter', 'pill-winter']].forEach(([s, c, tk, hrs, ot, label, pill]) => {
    const r   = compute(c, tk, hourly, hrs, ot, burden, s);
    const col = colorForPct(r.lpBurd);
    const fl2 = s === 'summer' ? r.tier.summerFloor : winterFloor(r.tier);
    const tierTxt = r.tier.rate > 0 ? `${r.tier.rate}% ≥${fmt(fl2)}` : 'hourly only';
    const tr = document.createElement('tr');
    if (s === activeSeason) tr.style.background = 'var(--surface2)';
    tr.innerHTML = `
      <td><span class="season-pill ${pill}">${label}</span></td>
      <td style="text-align:right">${c}</td>
      <td style="text-align:right">$${tk}</td>
      <td style="text-align:right">${fmt(r.moRev)}</td>
      <td style="text-align:right"><span class="tier-badge">${tierTxt}</span></td>
      <td style="text-align:right">${r.moComm > 0 ? fmt(r.moComm) : '—'}</td>
      <td style="text-align:right;color:var(--text2)">${fmt(r.moHpRaw)}</td>
      <td style="text-align:right">${fmt(r.moHpBurd)}</td>
      <td style="text-align:right;color:var(--text)">${fmt(r.moTotBurd)}</td>
      <td style="text-align:right">${fmt(r.moTotBurd / 2)}</td>
      <td style="text-align:right"><span class="badge ${col.badge}">${fmtPct(r.lpBurd)}</span><br><span style="font-size:10px;color:var(--text3)">${fmtPct(r.lpRaw)} base</span></td>
    `;
    cTbody.appendChild(tr);
  });
 
  // annual
  const sum = compute(sCalls, sTicket, hourly, sHours, sOt, burden, 'summer');
  const win = compute(wCalls, wTicket, hourly, wHours, wOt, burden, 'winter');
  const annRev     = (sum.moRev     + win.moRev)     * 6;
  const annHpRaw   = (sum.moHpRaw   + win.moHpRaw)   * 6;
  const annHpBurd  = (sum.moHpBurd  + win.moHpBurd)  * 6;
  const annComm    = (sum.moComm    + win.moComm)    * 6;
  const annNetPay  = (sum.moNetPay  + win.moNetPay)  * 6;
  const annTotBurd = (sum.moTotBurd + win.moTotBurd) * 6;
  const annLpRaw   = annRev > 0 ? (annHpRaw   / annRev) * 100 : 0;  // base wage only % (no commission)
  const annLpTotRaw  = annRev > 0 ? ((annHpRaw + annComm)  / annRev) * 100 : 0; // base + comm %
  const annLpBurd  = annRev > 0 ? (annTotBurd / annRev) * 100 : 0;
  const blendCol   = colorForPct(annLpBurd);
  const rawCol     = colorForPct(annLpTotRaw);
  document.getElementById('annual-grid').innerHTML = `
    <div class="metric"><div class="metric-label">Annual revenue gen.</div><div class="metric-val">${fmt(annRev)}</div><div class="metric-sub">S: ${fmt(sum.moRev*6)} · W: ${fmt(win.moRev*6)}</div></div>
    <div class="metric"><div class="metric-label">Annual commission</div><div class="metric-val">${fmt(annComm)}</div><div class="metric-sub">S: ${fmt(sum.moComm*6)} · W: ${fmt(win.moComm*6)}</div></div>
    <div class="metric"><div class="metric-label">Annual base pay</div><div class="metric-val">${fmt(annHpRaw)}</div><div class="metric-sub">wage only · incl. OT</div></div>
    <div class="metric"><div class="metric-label">Annual burdened pay</div><div class="metric-val">${fmt(annHpBurd)}</div><div class="metric-sub">${burden.toFixed(2)}× burden applied</div></div>
    <div class="metric" style="border:1px solid var(--green-border);background:var(--green-bg)">
      <div class="metric-label" style="color:var(--green)">Tech annual net pay</div>
      <div class="metric-val" style="color:var(--green);font-size:22px">${fmt(annNetPay)}</div>
      <div class="metric-sub" style="color:var(--green-border)">wage + commission · ${fmt(annNetPay/24)} bi-wkly</div>
    </div>
    <div class="metric"><div class="metric-label">Total annual cost (GES)</div><div class="metric-val" style="color:var(--accent)">${fmt(annTotBurd)}</div><div class="metric-sub">burdened + commission</div></div>
    <div class="metric" style="border:2px solid ${rawCol.bar};padding:11px">
      <div class="metric-label" style="color:${rawCol.bar}">Base labor % of revenue</div>
      <div style="font-family:'IBM Plex Mono',monospace;font-size:30px;font-weight:600;color:${rawCol.bar};line-height:1.1">${fmtPct(annLpTotRaw)}</div>
      <div class="metric-sub" style="margin-top:4px">wage + comm · S: ${fmtPct(sum.lpRaw)} · W: ${fmtPct(win.lpRaw)}</div>
    </div>
    <div class="metric" style="border:2px solid ${blendCol.bar};padding:11px">
      <div class="metric-label" style="color:${blendCol.bar}">Burdened labor % of revenue</div>
      <div style="font-family:'IBM Plex Mono',monospace;font-size:30px;font-weight:600;color:${blendCol.bar};line-height:1.1">${fmtPct(annLpBurd)}</div>
      <div class="metric-sub" style="margin-top:4px">fully loaded · S: ${fmtPct(sum.lpBurd)} · W: ${fmtPct(win.lpBurd)}</div>
    </div>
  `;
 
  // detail table — single row for active season
  const lbl = activePeriod === 'weekly' ? 'Wkly' : activePeriod === 'biweekly' ? 'Bi-wkly' : 'Monthly';
  document.getElementById('th-rev').textContent   = lbl + ' revenue';
  document.getElementById('th-hp').textContent    = lbl + ' burdened pay';
  document.getElementById('th-comm').textContent  = lbl + ' commission';
  document.getElementById('th-total').textContent = lbl + ' total (burdened)';
 
  const dTbody = document.getElementById('detail-tbody');
  dTbody.innerHTML = '';
  const r   = compute(calls, ticket, hourly, hours, otHours, burden, activeSeason);
  const col = colorForPct(r.lpBurd);
  const fl3 = activeSeason === 'summer' ? r.tier.summerFloor : winterFloor(r.tier);
  const tierTxt = r.tier.rate > 0 ? `${r.tier.rate}% ≥${fmt(fl3)}/mo` : 'hourly only';
 
  const mult      = activePeriod === 'weekly' ? 1/WPM : activePeriod === 'biweekly' ? 2/WPM : 1;
  const dispRev   = r.moRev     * mult;
  const dispHp    = r.moHpBurd  * mult;
  const dispComm  = r.moComm    * mult;
  const dispTotal = r.moTotBurd * mult;
  const biWk      = r.moTotBurd / WPM * 2;
 
  const seasonLabel = activeSeason === 'summer' ? '▲ Summer' : '❄ Winter';
  const seasonPill  = activeSeason === 'summer' ? 'pill-summer' : 'pill-winter';
  const tr = document.createElement('tr');
  tr.innerHTML = `
    <td><span class="season-pill ${seasonPill}">${seasonLabel}</span></td>
    <td style="text-align:right">${fmt(dispRev)}</td>
    <td style="text-align:right"><span class="tier-badge">${tierTxt}</span></td>
    <td style="text-align:right">${fmt(dispHp)}<br><span style="font-size:10px;color:var(--text3)">base ${fmt(r.moHpRaw*mult)}</span></td>
    <td style="text-align:right">${r.moComm > 0 ? fmt(dispComm) : '—'}</td>
    <td style="text-align:right;color:var(--text)">${fmt(dispTotal)}</td>
    <td style="text-align:right">${fmt(biWk)}</td>
    <td style="text-align:right"><span class="badge ${col.badge}">${fmtPct(r.lpBurd)}</span></td>
  `;
  dTbody.appendChild(tr);
}
 
renderTiers();
calc();
['s-hourly','s-burden','s-hours-sum','s-hours-win','s-ot-sum','s-ot-win','s-calls-sum','s-calls-win','s-ticket-sum','s-ticket-win'].forEach(id => {
  document.getElementById(id).addEventListener('input', calc);
});
</script>
</body>
</html>
