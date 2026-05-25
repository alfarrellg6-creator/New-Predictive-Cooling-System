<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>PC1250 Cooling System — Lifetime Prediction</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<style>
  @import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;500;600;700&family=DM+Sans:wght@300;400;500;600;700&display=swap');

  :root {
    --bg-primary: #0a0e17;
    --bg-secondary: #111827;
    --bg-card: #151d2e;
    --bg-card-hover: #1a2540;
    --border: #1e2d4a;
    --border-glow: #2563eb33;
    --text-primary: #e8ecf4;
    --text-secondary: #7a8baa;
    --text-muted: #4a5678;
    --accent-blue: #3b82f6;
    --accent-cyan: #06b6d4;
    --accent-green: #10b981;
    --accent-amber: #f59e0b;
    --accent-red: #ef4444;
    --accent-purple: #8b5cf6;
    --gradient-blue: linear-gradient(135deg, #3b82f6, #06b6d4);
    --gradient-danger: linear-gradient(135deg, #ef4444, #f59e0b);
    --gradient-success: linear-gradient(135deg, #10b981, #06b6d4);
    --shadow-glow: 0 0 30px rgba(59,130,246,0.15);
    --shadow-card: 0 4px 24px rgba(0,0,0,0.3);
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    font-family: 'DM Sans', sans-serif;
    background: var(--bg-primary);
    color: var(--text-primary);
    min-height: 100vh;
    overflow-x: hidden;
  }

  .bg-grid {
    position: fixed; inset: 0; z-index: 0; pointer-events: none;
    background-image:
      linear-gradient(rgba(59,130,246,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(59,130,246,0.03) 1px, transparent 1px);
    background-size: 60px 60px;
  }

  .bg-radial {
    position: fixed; top: -200px; right: -200px; width: 700px; height: 700px;
    background: radial-gradient(circle, rgba(59,130,246,0.08), transparent 70%);
    z-index: 0; pointer-events: none;
  }

  .container {
    position: relative; z-index: 1;
    max-width: 1440px; margin: 0 auto; padding: 24px;
  }

  /* HEADER */
  .header {
    display: flex; align-items: center; justify-content: space-between;
    padding: 20px 0 32px; border-bottom: 1px solid var(--border);
    margin-bottom: 32px; flex-wrap: wrap; gap: 16px;
  }
  .header-left { display: flex; align-items: center; gap: 16px; }
  .logo-mark {
    width: 48px; height: 48px; border-radius: 12px;
    background: var(--gradient-blue); display: flex; align-items: center;
    justify-content: center; font-family: 'JetBrains Mono'; font-weight: 700;
    font-size: 14px; color: #fff; letter-spacing: -0.5px;
    box-shadow: 0 0 20px rgba(59,130,246,0.3);
  }
  .header h1 {
    font-family: 'JetBrains Mono'; font-size: 22px; font-weight: 600;
    letter-spacing: -0.5px;
    background: var(--gradient-blue); -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }
  .header .subtitle { font-size: 12px; color: var(--text-muted); margin-top: 2px;
    font-family: 'JetBrains Mono'; letter-spacing: 1px; text-transform: uppercase; }

  .header-right { display: flex; gap: 8px; flex-wrap: wrap; }

  /* BUTTONS */
  .btn {
    font-family: 'JetBrains Mono'; font-size: 12px; font-weight: 500;
    padding: 10px 20px; border-radius: 8px; border: 1px solid var(--border);
    background: var(--bg-card); color: var(--text-secondary);
    cursor: pointer; transition: all 0.2s; letter-spacing: 0.3px;
  }
  .btn:hover { background: var(--bg-card-hover); border-color: var(--accent-blue); color: var(--text-primary); }
  .btn-primary {
    background: var(--gradient-blue); border: none; color: #fff;
    box-shadow: 0 0 16px rgba(59,130,246,0.25);
  }
  .btn-primary:hover { box-shadow: 0 0 24px rgba(59,130,246,0.4); transform: translateY(-1px); }

  /* TABS */
  .tabs {
    display: flex; gap: 4px; background: var(--bg-secondary); border-radius: 12px;
    padding: 4px; margin-bottom: 24px; overflow-x: auto;
  }
  .tab {
    font-family: 'JetBrains Mono'; font-size: 12px; font-weight: 500;
    padding: 10px 20px; border-radius: 8px; border: none;
    background: transparent; color: var(--text-muted); cursor: pointer;
    transition: all 0.2s; white-space: nowrap;
  }
  .tab:hover { color: var(--text-secondary); }
  .tab.active { background: var(--bg-card); color: var(--accent-cyan);
    box-shadow: 0 2px 8px rgba(0,0,0,0.2); }

  /* CARDS */
  .card {
    background: var(--bg-card); border: 1px solid var(--border);
    border-radius: 16px; padding: 24px; box-shadow: var(--shadow-card);
    transition: all 0.3s;
  }
  .card:hover { border-color: var(--border-glow); }
  .card-title {
    font-family: 'JetBrains Mono'; font-size: 11px; font-weight: 600;
    text-transform: uppercase; letter-spacing: 1.5px; color: var(--text-muted);
    margin-bottom: 16px; display: flex; align-items: center; gap: 8px;
  }
  .card-title .dot { width: 6px; height: 6px; border-radius: 50%; }

  /* GRID LAYOUTS */
  .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 24px; }
  .grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; margin-bottom: 24px; }
  .grid-4 { display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px; margin-bottom: 24px; }

  @media (max-width: 1200px) { .grid-4 { grid-template-columns: repeat(2, 1fr); } }
  @media (max-width: 900px) {
    .grid-2, .grid-3 { grid-template-columns: 1fr; }
    .grid-4 { grid-template-columns: 1fr; }
  }

  /* DATA INPUT */
  .upload-zone {
    border: 2px dashed var(--border); border-radius: 16px;
    padding: 48px; text-align: center; cursor: pointer;
    transition: all 0.3s; position: relative; overflow: hidden;
  }
  .upload-zone:hover { border-color: var(--accent-blue); background: rgba(59,130,246,0.03); }
  .upload-zone.dragover { border-color: var(--accent-cyan); background: rgba(6,182,212,0.05); }
  .upload-icon { font-size: 48px; margin-bottom: 12px; }
  .upload-text { font-family: 'JetBrains Mono'; font-size: 14px; color: var(--text-secondary); }
  .upload-sub { font-size: 12px; color: var(--text-muted); margin-top: 8px; }

  input[type="file"] { display: none; }

  /* METRIC CARD */
  .metric { text-align: center; padding: 20px; }
  .metric-value {
    font-family: 'JetBrains Mono'; font-size: 32px; font-weight: 700;
    letter-spacing: -1px; line-height: 1;
  }
  .metric-label {
    font-size: 11px; color: var(--text-muted); margin-top: 8px;
    font-family: 'JetBrains Mono'; text-transform: uppercase; letter-spacing: 1px;
  }
  .metric-sub { font-size: 12px; color: var(--text-secondary); margin-top: 4px; }

  /* COMPONENT TABLE */
  .comp-table { width: 100%; border-collapse: collapse; }
  .comp-table th {
    font-family: 'JetBrains Mono'; font-size: 10px; text-transform: uppercase;
    letter-spacing: 1.2px; color: var(--text-muted); text-align: left;
    padding: 12px 16px; border-bottom: 1px solid var(--border);
  }
  .comp-table td {
    padding: 14px 16px; border-bottom: 1px solid rgba(30,45,74,0.5);
    font-size: 13px; vertical-align: middle;
  }
  .comp-table tr:hover td { background: rgba(59,130,246,0.03); }
  .comp-name { font-weight: 600; color: var(--text-primary); }
  .comp-model { font-family: 'JetBrains Mono'; font-size: 11px; color: var(--accent-cyan); }

  /* HEALTH BAR */
  .health-bar-wrap { width: 120px; height: 6px; background: var(--bg-secondary); border-radius: 3px; overflow: hidden; }
  .health-bar { height: 100%; border-radius: 3px; transition: width 0.5s; }

  /* STATUS BADGES */
  .badge {
    font-family: 'JetBrains Mono'; font-size: 10px; font-weight: 600;
    padding: 4px 10px; border-radius: 6px; letter-spacing: 0.5px;
    display: inline-block;
  }
  .badge-ok { background: rgba(16,185,129,0.15); color: var(--accent-green); }
  .badge-warn { background: rgba(245,158,11,0.15); color: var(--accent-amber); }
  .badge-crit { background: rgba(239,68,68,0.15); color: var(--accent-red); }

  /* CHART CONTAINER */
  .chart-wrap { position: relative; height: 320px; }

  /* FORMULA DISPLAY */
  .formula-box {
    background: var(--bg-secondary); border: 1px solid var(--border);
    border-radius: 10px; padding: 16px 20px; margin: 12px 0;
    font-family: 'JetBrains Mono'; font-size: 12px; color: var(--accent-cyan);
    line-height: 1.8; overflow-x: auto;
  }

  /* SECTION */
  .section { display: none; }
  .section.active { display: block; animation: fadeIn 0.3s ease; }
  @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: none; } }

  /* PARAM GRID */
  .param-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(240px, 1fr)); gap: 12px; }
  .param-item {
    display: flex; justify-content: space-between; align-items: center;
    padding: 10px 14px; background: var(--bg-secondary); border-radius: 8px;
    border: 1px solid var(--border);
  }
  .param-key { font-family: 'JetBrains Mono'; font-size: 11px; color: var(--text-muted); }
  .param-val { font-family: 'JetBrains Mono'; font-size: 13px; font-weight: 600; color: var(--text-primary); }

  .param-input {
    font-family: 'JetBrains Mono'; font-size: 13px; font-weight: 600;
    color: var(--text-primary); background: transparent; border: none;
    text-align: right; width: 80px; outline: none;
    border-bottom: 1px solid transparent; transition: border-color 0.2s;
  }
  .param-input:focus { border-bottom-color: var(--accent-blue); }

  /* SCROLLBAR */
  ::-webkit-scrollbar { width: 6px; height: 6px; }
  ::-webkit-scrollbar-track { background: var(--bg-secondary); }
  ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 3px; }
  ::-webkit-scrollbar-thumb:hover { background: var(--text-muted); }

  .overflow-x { overflow-x: auto; }

  /* TIMELINE */
  .timeline-item {
    display: flex; gap: 16px; padding: 12px 0;
    border-bottom: 1px solid rgba(30,45,74,0.3);
  }
  .timeline-dot {
    width: 10px; height: 10px; border-radius: 50%; margin-top: 4px; flex-shrink: 0;
  }
  .timeline-content { flex: 1; }
  .timeline-date { font-family: 'JetBrains Mono'; font-size: 11px; color: var(--text-muted); }
  .timeline-text { font-size: 13px; color: var(--text-secondary); margin-top: 2px; }

  .monte-carlo-info {
    display: flex; gap: 12px; align-items: center; padding: 16px;
    background: rgba(139,92,246,0.08); border: 1px solid rgba(139,92,246,0.2);
    border-radius: 10px; margin-top: 16px;
  }
  .monte-carlo-info .icon { font-size: 24px; }
  .monte-carlo-info .text { font-size: 12px; color: var(--accent-purple); line-height: 1.5; }

  .download-row { display: flex; gap: 8px; margin-top: 16px; flex-wrap: wrap; }
</style>
</head>
<body>
<div class="bg-grid"></div>
<div class="bg-radial"></div>

<div class="container">
  <!-- ===================== HEADER ===================== -->
  <!--
    CARA EDIT:
    - Ganti "PC1250" di logo-mark untuk nama alat lain
    - Ganti judul h1 sesuai kebutuhan
    - Ganti subtitle sesuai metode yang digunakan
  -->
  <div class="header">
    <div class="header-left">
      <div class="logo-mark">PC<br>1250</div>
      <div>
        <h1>Cooling System Lifetime Prediction</h1>
        <div class="subtitle">Arrhenius · Miner's Rule · Q10 · Weibull · Fouling</div>
      </div>
    </div>
    <div class="header-right">
      <button class="btn" onclick="loadSampleData()">▶ Load Sample Data</button>
      <button class="btn btn-primary" onclick="document.getElementById('csvInput').click()">↑ Upload CSV</button>
      <input type="file" id="csvInput" accept=".csv" onchange="handleCSV(event)">
    </div>
  </div>

  <!-- ===================== TABS ===================== -->
  <div class="tabs" id="mainTabs">
    <button class="tab active" data-tab="dashboard" onclick="switchTab('dashboard')">Dashboard</button>
    <button class="tab" data-tab="components" onclick="switchTab('components')">Components</button>
    <button class="tab" data-tab="models" onclick="switchTab('models')">Models &amp; Formulas</button>
    <button class="tab" data-tab="charts" onclick="switchTab('charts')">Charts</button>
    <button class="tab" data-tab="montecarlo" onclick="switchTab('montecarlo')">Monte Carlo</button>
    <button class="tab" data-tab="maintenance" onclick="switchTab('maintenance')">Maintenance Plan</button>
  </div>

  <!-- ===================== DASHBOARD ===================== -->
  <div class="section active" id="sec-dashboard">
    <!-- Upload zone — tampil saat belum ada data -->
    <div id="uploadZone"
      class="upload-zone"
      ondragover="event.preventDefault();this.classList.add('dragover')"
      ondragleave="this.classList.remove('dragover')"
      ondrop="handleDrop(event)"
      onclick="document.getElementById('csvInput').click()">
      <div class="upload-icon">⬆</div>
      <div class="upload-text">Drop your pc1250_coolant_log.csv here</div>
      <div class="upload-sub">Format: timestamp, coolant_temp_C, ambient_temp_C, dust_mg_m3, hours_meter</div>
      <div class="upload-sub" style="margin-top:16px; color:var(--accent-cyan);">
        Atau klik "Load Sample Data" untuk mencoba dengan data demo
      </div>
    </div>

    <!-- Konten dashboard — tampil setelah data dimuat -->
    <div id="dashContent" style="display:none;">
      <div class="grid-4" id="summaryCards"></div>
      <div class="grid-2">
        <div class="card">
          <div class="card-title">
            <span class="dot" style="background:var(--accent-cyan)"></span>
            Temperature Profile
          </div>
          <div class="chart-wrap"><canvas id="chartTemp"></canvas></div>
        </div>
        <div class="card">
          <div class="card-title">
            <span class="dot" style="background:var(--accent-amber)"></span>
            Cumulative Damage (Miner's Rule)
          </div>
          <div class="chart-wrap"><canvas id="chartDamage"></canvas></div>
        </div>
      </div>
      <div class="grid-2">
        <div class="card">
          <div class="card-title">
            <span class="dot" style="background:var(--accent-green)"></span>
            Component Health Overview
          </div>
          <div class="overflow-x">
            <table class="comp-table" id="healthTable"></table>
          </div>
        </div>
        <div class="card">
          <div class="card-title">
            <span class="dot" style="background:var(--accent-red)"></span>
            Overheat Events Detected
          </div>
          <div id="overheatTimeline"></div>
        </div>
      </div>
    </div>
  </div>

  <!-- ===================== COMPONENTS ===================== -->
  <div class="section" id="sec-components">
    <div class="grid-3" id="compCards"></div>
  </div>

  <!-- ===================== MODELS & FORMULAS ===================== -->
  <div class="section" id="sec-models">
    <div class="card" style="margin-bottom:20px;">
      <div class="card-title">
        <span class="dot" style="background:var(--accent-purple)"></span>
        Degradation Models — Editable Parameters
      </div>
      <p style="font-size:13px;color:var(--text-secondary);margin-bottom:20px;">
        Sistem ini menggabungkan lima model degradasi berbasis fisika dan statistik untuk
        memprediksi Remaining Useful Life (RUL) setiap komponen sistem pendingin.
      </p>

      <!-- ① Arrhenius -->
      <h3 style="font-family:'JetBrains Mono';font-size:14px;color:var(--accent-cyan);margin:20px 0 8px;">
        ① Arrhenius Equation — Thermal Aging
      </h3>
      <div class="formula-box">
        k(T) = A · exp(−Ea / (R · T))<br>
        A = Pre-exponential factor &nbsp;|&nbsp; Ea = Activation energy (J/mol) &nbsp;|&nbsp;
        R = 8.314 J/(mol·K) &nbsp;|&nbsp; T = Temperature (K)
      </div>
      <div class="param-grid" id="paramsArrhenius"></div>

      <!-- ② Miner's Rule -->
      <h3 style="font-family:'JetBrains Mono';font-size:14px;color:var(--accent-amber);margin:20px 0 8px;">
        ② Miner's Rule — Cumulative Fatigue Damage
      </h3>
      <div class="formula-box">
        D = Σ (nᵢ / Nᵢ) &nbsp;→&nbsp; Failure when D ≥ 1.0<br>
        nᵢ = cycles at stress level i &nbsp;|&nbsp; Nᵢ = cycles to failure at stress level i
      </div>
      <div class="param-grid" id="paramsMiner"></div>

      <!-- ③ Q10 -->
      <h3 style="font-family:'JetBrains Mono';font-size:14px;color:var(--accent-green);margin:20px 0 8px;">
        ③ Van't Hoff Q10 — Temperature Acceleration
      </h3>
      <div class="formula-box">
        Rate(T) = Rate(T_ref) · Q10^((T − T_ref) / 10)<br>
        Q10 = rate factor per 10°C rise &nbsp;|&nbsp; T_ref = reference temperature
      </div>
      <div class="param-grid" id="paramsQ10"></div>

      <!-- ④ Weibull -->
      <h3 style="font-family:'JetBrains Mono';font-size:14px;color:var(--accent-red);margin:20px 0 8px;">
        ④ Weibull Distribution — Failure Probability
      </h3>
      <div class="formula-box">
        F(t) = 1 − exp(−(t/η)^β)<br>
        β = Shape parameter (wear-out >1) &nbsp;|&nbsp; η = Scale parameter (characteristic life, hours)
      </div>
      <div class="param-grid" id="paramsWeibull"></div>

      <!-- ⑤ Fouling -->
      <h3 style="font-family:'JetBrains Mono';font-size:14px;color:var(--text-muted);margin:20px 0 8px;">
        ⑤ Fouling Decay — Dust Accumulation Effect
      </h3>
      <div class="formula-box">
        η_fouling(t) = η₀ · exp(−λ · D_dust · t)<br>
        η₀ = Initial efficiency &nbsp;|&nbsp; λ = Fouling rate constant &nbsp;|&nbsp;
        D_dust = dust concentration factor
      </div>
      <div class="param-grid" id="paramsFouling"></div>

      <button class="btn btn-primary" style="margin-top:20px;" onclick="recalculate()">
        ⟳ Recalculate All Models
      </button>
    </div>
  </div>

  <!-- ===================== CHARTS ===================== -->
  <div class="section" id="sec-charts">
    <div class="grid-2">
      <div class="card">
        <div class="card-title"><span class="dot" style="background:var(--accent-purple)"></span> Weibull Failure Probability</div>
        <div class="chart-wrap"><canvas id="chartWeibull"></canvas></div>
      </div>
      <div class="card">
        <div class="card-title"><span class="dot" style="background:var(--accent-green)"></span> Fouling Efficiency Decay</div>
        <div class="chart-wrap"><canvas id="chartFouling"></canvas></div>
      </div>
    </div>
    <div class="grid-2">
      <div class="card">
        <div class="card-title"><span class="dot" style="background:var(--accent-cyan)"></span> Arrhenius Degradation Rate vs Temperature</div>
        <div class="chart-wrap"><canvas id="chartArrhenius"></canvas></div>
      </div>
      <div class="card">
        <div class="card-title"><span class="dot" style="background:var(--accent-amber)"></span> Q10 Acceleration Factor</div>
        <div class="chart-wrap"><canvas id="chartQ10"></canvas></div>
      </div>
    </div>
  </div>

  <!-- ===================== MONTE CARLO ===================== -->
  <div class="section" id="sec-montecarlo">
    <div class="card" style="margin-bottom:20px;">
      <div class="card-title">
        <span class="dot" style="background:var(--accent-purple)"></span>
        Monte Carlo Simulation — RUL Distribution
      </div>
      <p style="font-size:13px;color:var(--text-secondary);margin-bottom:16px;">
        Menjalankan 5.000 simulasi stokastik dengan variasi temperatur, debu, dan
        probabilitas overheat untuk menghasilkan distribusi Remaining Useful Life (RUL)
        setiap komponen.
      </p>
      <button class="btn btn-primary" onclick="runMonteCarlo()">▶ Run Monte Carlo (5,000 iterations)</button>
      <div class="monte-carlo-info">
        <div class="icon">🎲</div>
        <div class="text">
          Simulasi menggunakan Latin Hypercube Sampling pada temperatur (±8°C),
          debu (±30%), dan frekuensi overheat (Poisson λ dari data historis).
          Hasil menampilkan P10/P50/P90 RUL untuk setiap komponen.
        </div>
      </div>
    </div>
    <div class="grid-2">
      <div class="card">
        <div class="card-title">
          <span class="dot" style="background:var(--accent-purple)"></span>
          RUL Distribution — All Components
        </div>
        <div class="chart-wrap"><canvas id="chartMC"></canvas></div>
      </div>
      <div class="card">
        <div class="card-title">
          <span class="dot" style="background:var(--accent-cyan)"></span>
          Confidence Intervals
        </div>
        <div id="mcResults" style="font-size:13px;color:var(--text-secondary);">
          Run simulation to see results.
        </div>
      </div>
    </div>
  </div>

  <!-- ===================== MAINTENANCE PLAN ===================== -->
  <div class="section" id="sec-maintenance">
    <div class="card">
      <div class="card-title">
        <span class="dot" style="background:var(--accent-green)"></span>
        Predictive Maintenance Schedule
      </div>
      <div class="overflow-x">
        <table class="comp-table" id="maintTable"></table>
      </div>
      <div class="download-row">
        <button class="btn" onclick="downloadReport()">↓ Download Full Report (CSV)</button>
        <button class="btn" onclick="downloadJSON()">↓ Export JSON</button>
      </div>
    </div>
  </div>
</div><!-- /container -->

<script>
/* =========================================================
   KONFIGURASI KOMPONEN
   =========================================================
   Untuk menambah/mengurangi komponen, edit array COMPONENTS.
   Parameter per komponen:
     id           — ID unik (string tanpa spasi)
     name         — Nama tampilan
     icon         — Emoji ikon
     Ea           — Activation energy Arrhenius (J/mol)
     A            — Pre-exponential factor Arrhenius
     beta         — Weibull shape parameter
     eta          — Weibull scale / characteristic life (hours)
     q10          — Q10 factor
     Tref         — Reference temperature (°C)
     foulingLambda— Fouling rate constant
     baseLife     — Base design life (hours)
     critTemp     — Critical overheat threshold (°C)
   ========================================================= */
const COMPONENTS = [
  { id:'radiator',    name:'Radiator Core',          icon:'🔲', Ea:50000, A:1e8, beta:2.5, eta:18000, q10:2.0, Tref:85, foulingLambda:0.0003,  baseLife:18000, critTemp:105 },
  { id:'waterpump',   name:'Water Pump',              icon:'⚙️', Ea:45000, A:5e7, beta:2.8, eta:15000, q10:1.8, Tref:80, foulingLambda:0.0002,  baseLife:15000, critTemp:100 },
  { id:'thermostat',  name:'Thermostat Valve',        icon:'🌡', Ea:40000, A:2e7, beta:3.2, eta:12000, q10:2.2, Tref:82, foulingLambda:0.0001,  baseLife:12000, critTemp:98  },
  { id:'hoses',       name:'Coolant Hoses',           icon:'🔗', Ea:55000, A:2e8, beta:2.0, eta:20000, q10:2.5, Tref:85, foulingLambda:0.00005, baseLife:20000, critTemp:110 },
  { id:'fan',         name:'Cooling Fan & Drive',     icon:'🌀', Ea:35000, A:1e7, beta:3.5, eta:16000, q10:1.5, Tref:75, foulingLambda:0.0005,  baseLife:16000, critTemp:95  },
  { id:'oilcooler',   name:'Hydraulic Oil Cooler',    icon:'🛢', Ea:48000, A:8e7, beta:2.3, eta:17000, q10:2.1, Tref:90, foulingLambda:0.0004,  baseLife:17000, critTemp:108 },
  { id:'intercooler', name:'Charge Air Cooler',       icon:'💨', Ea:42000, A:3e7, beta:2.6, eta:14000, q10:1.9, Tref:88, foulingLambda:0.00035, baseLife:14000, critTemp:102 },
  { id:'coolant',     name:'Coolant Fluid',           icon:'💧', Ea:60000, A:5e8, beta:1.8, eta:6000,  q10:2.8, Tref:85, foulingLambda:0.0006,  baseLife:6000,  critTemp:100 },
];

/* =========================================================
   KONSTANTA FISIKA
   ========================================================= */
const R_GAS = 8.314; // J/(mol·K)

/* =========================================================
   STATE GLOBAL
   ========================================================= */
let rawData = [];
let analysisResults = {};
let charts = {};

/* ---------------------------------------------------------
   TAB SWITCHING
   --------------------------------------------------------- */
function switchTab(tab) {
  document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
  document.querySelector(`.tab[data-tab="${tab}"]`).classList.add('active');
  document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
  document.getElementById('sec-' + tab).classList.add('active');
}

/* ---------------------------------------------------------
   CSV PARSING
   --------------------------------------------------------- */
function handleCSV(e) { parseCSV(e.target.files[0]); }

function handleDrop(e) {
  e.preventDefault();
  e.target.classList.remove('dragover');
  if (e.dataTransfer.files.length) parseCSV(e.dataTransfer.files[0]);
}

function parseCSV(file) {
  const reader = new FileReader();
  reader.onload = (e) => {
    const lines = e.target.result.trim().split('\n');
    rawData = [];
    for (let i = 1; i < lines.length; i++) {
      const v = lines[i].split(',').map(x => x.trim());
      if (v.length >= 5) {
        rawData.push({
          timestamp: v[0],
          coolant_temp: parseFloat(v[1]),
          ambient_temp: parseFloat(v[2]),
          dust:         parseFloat(v[3]),
          hours:        parseFloat(v[4])
        });
      }
    }
    if (rawData.length > 0) runAnalysis();
  };
  reader.readAsText(file);
}

/* ---------------------------------------------------------
   SAMPLE DATA GENERATOR
   --------------------------------------------------------- */
function loadSampleData() {
  rawData = [];
  const start = new Date('2023-01-01');
  let hm = 8500;
  for (let i = 0; i < 1500; i++) {
    const d = new Date(start.getTime() + i * 4 * 3600000);
    hm += 3 + Math.random() * 5;
    const ambient = 28 + 10 * Math.sin(i / 180 * Math.PI) + (Math.random() - 0.5) * 8;
    let coolant = 78 + (ambient - 28) * 0.4 + Math.random() * 12;
    if (Math.random() < 0.02) coolant += 15 + Math.random() * 20; // occasional overheat
    const dust = 2 + Math.random() * 6 + (ambient > 35 ? 3 : 0);
    rawData.push({
      timestamp:    d.toISOString().slice(0,16).replace('T',' '),
      coolant_temp: +coolant.toFixed(1),
      ambient_temp: +ambient.toFixed(1),
      dust:         +dust.toFixed(2),
      hours:        +hm.toFixed(1)
    });
  }
  runAnalysis();
}

/* ---------------------------------------------------------
   ANALYSIS ENGINE
   --------------------------------------------------------- */
function runAnalysis() {
  document.getElementById('uploadZone').style.display  = 'none';
  document.getElementById('dashContent').style.display = 'block';

  const temps       = rawData.map(d => d.coolant_temp);
  const dusts       = rawData.map(d => d.dust);
  const hours       = rawData.map(d => d.hours);
  const currentHM   = Math.max(...hours);
  const avgTemp     = temps.reduce((a,b)=>a+b,0) / temps.length;
  const maxTemp     = Math.max(...temps);
  const avgDust     = dusts.reduce((a,b)=>a+b,0) / dusts.length;
  const overheatEvt = rawData.filter(d => d.coolant_temp > 100);

  analysisResults = {};
  COMPONENTS.forEach(comp => {
    /* Arrhenius */
    const T_K     = avgTemp + 273.15;
    const T_ref_K = comp.Tref + 273.15;
    const k_avg   = comp.A * Math.exp(-comp.Ea / (R_GAS * T_K));
    const k_ref   = comp.A * Math.exp(-comp.Ea / (R_GAS * T_ref_K));
    const arrheniusFactor = k_avg / k_ref;

    /* Q10 */
    const q10Factor = Math.pow(comp.q10, (avgTemp - comp.Tref) / 10);

    /* Miner's cumulative damage */
    const hoursPerPt = rawData.length > 1
      ? (hours[hours.length-1] - hours[0]) / rawData.length : 4;
    const tempBins = [
      { lo:0,   hi:80,  Nf: comp.baseLife * 1.5  },
      { lo:80,  hi:90,  Nf: comp.baseLife         },
      { lo:90,  hi:100, Nf: comp.baseLife * 0.5   },
      { lo:100, hi:110, Nf: comp.baseLife * 0.15  },
      { lo:110, hi:999, Nf: comp.baseLife * 0.05  },
    ];
    let minerDamage = 0;
    tempBins.forEach(bin => {
      const n = rawData.filter(d => d.coolant_temp >= bin.lo && d.coolant_temp < bin.hi).length * hoursPerPt;
      minerDamage += n / bin.Nf;
    });

    /* Fouling efficiency */
    const foulingEff = Math.exp(-comp.foulingLambda * avgDust * currentHM / 1000);

    /* Weibull failure probability */
    const weibullF = 1 - Math.exp(-Math.pow(currentHM / comp.eta, comp.beta));

    /* Combined RUL */
    const combinedFactor = arrheniusFactor * q10Factor / foulingEff;
    const effectiveLife  = comp.baseLife / combinedFactor;
    const rul            = Math.max(0, effectiveLife - currentHM);
    const healthPct      = Math.max(0, Math.min(100, (1 - minerDamage) * foulingEff * 100));

    const status = healthPct < 30 || rul < 500  ? 'CRITICAL'
                 : healthPct < 60 || rul < 2000 ? 'WARNING' : 'OK';

    analysisResults[comp.id] = {
      comp, arrheniusFactor, q10Factor, minerDamage,
      foulingEff, weibullF, effectiveLife, rul, healthPct, status, currentHM
    };
  });

  renderDashboard(avgTemp, maxTemp, avgDust, currentHM, overheatEvt);
  renderComponents();
  renderParams();
  renderCharts();
  renderMaintenance();
}

/* Tombol "Recalculate" — baca nilai yang diedit user dari input */
function recalculate() {
  COMPONENTS.forEach(comp => {
    ['Ea','A','beta','eta','q10','Tref','foulingLambda','baseLife'].forEach(key => {
      const el = document.getElementById(`param_${comp.id}_${key}`);
      if (el) { const v = parseFloat(el.value); if (!isNaN(v)) comp[key] = v; }
    });
  });
  runAnalysis();
  switchTab('dashboard');
}

/* ---------------------------------------------------------
   RENDER DASHBOARD
   --------------------------------------------------------- */
function renderDashboard(avgTemp, maxTemp, avgDust, currentHM, overheatEvt) {
  const worst = Object.values(analysisResults).sort((a,b) => a.healthPct - b.healthPct)[0];

  document.getElementById('summaryCards').innerHTML = [
    metricCard('Current HM',      currentHM.toLocaleString()+' hrs', 'Operating Hours', '--accent-cyan'),
    metricCard('Avg Coolant',     avgTemp.toFixed(1)+'°C', `Peak: ${maxTemp.toFixed(1)}°C`, '--accent-amber'),
    metricCard('Overheat Events', overheatEvt.length, '> 100°C detected', '--accent-red'),
    metricCard('Worst Component', worst.comp.name, `Health: ${worst.healthPct.toFixed(0)}%`,
      worst.status === 'CRITICAL' ? '--accent-red' : '--accent-amber'),
  ].join('');

  /* Health table */
  let thtml = `<thead><tr>
    <th>Component</th><th>Health</th><th>RUL (hrs)</th>
    <th>Miner D</th><th>Weibull F(t)</th><th>Fouling η</th><th>Status</th>
  </tr></thead><tbody>`;
  Object.values(analysisResults).sort((a,b)=>a.healthPct-b.healthPct).forEach(r => {
    const barColor   = r.healthPct>60 ? 'var(--accent-green)' : r.healthPct>30 ? 'var(--accent-amber)' : 'var(--accent-red)';
    const badgeClass = r.status==='OK' ? 'badge-ok' : r.status==='WARNING' ? 'badge-warn' : 'badge-crit';
    thtml += `<tr>
      <td><span class="comp-name">${r.comp.icon} ${r.comp.name}</span></td>
      <td>
        <div class="health-bar-wrap">
          <div class="health-bar" style="width:${r.healthPct}%;background:${barColor}"></div>
        </div>
        ${r.healthPct.toFixed(0)}%
      </td>
      <td style="font-family:'JetBrains Mono';font-weight:600;color:${r.rul<1000?'var(--accent-red)':'var(--text-primary)'}">
        ${r.rul.toFixed(0)}
      </td>
      <td style="font-family:'JetBrains Mono'">${r.minerDamage.toFixed(3)}</td>
      <td style="font-family:'JetBrains Mono'">${(r.weibullF*100).toFixed(1)}%</td>
      <td style="font-family:'JetBrains Mono'">${(r.foulingEff*100).toFixed(1)}%</td>
      <td><span class="badge ${badgeClass}">${r.status}</span></td>
    </tr>`;
  });
  thtml += '</tbody>';
  document.getElementById('healthTable').innerHTML = thtml;

  /* Overheat timeline */
  const ohDiv = document.getElementById('overheatTimeline');
  if (!overheatEvt.length) {
    ohDiv.innerHTML = '<p style="color:var(--text-muted);font-size:13px;">Tidak ada overheat terdeteksi (>100°C).</p>';
  } else {
    const show = overheatEvt.slice(-15);
    ohDiv.innerHTML = show.map(ev => `
      <div class="timeline-item">
        <div class="timeline-dot" style="background:var(--accent-red)"></div>
        <div class="timeline-content">
          <div class="timeline-date">${ev.timestamp} | HM ${ev.hours.toFixed(0)}</div>
          <div class="timeline-text" style="color:var(--accent-red)">
            ${ev.coolant_temp.toFixed(1)}°C — Ambient ${ev.ambient_temp.toFixed(1)}°C,
            Dust ${ev.dust.toFixed(1)} mg/m³
          </div>
        </div>
      </div>`).join('')
      + (overheatEvt.length>15 ? `<p style="color:var(--text-muted);font-size:11px;margin-top:8px;">Menampilkan 15 dari ${overheatEvt.length} event</p>` : '');
  }

  renderTempChart();
  renderDamageChart();
}

function metricCard(label, value, sub, colorVar) {
  return `<div class="card metric">
    <div class="metric-value" style="color:var(${colorVar})">${value}</div>
    <div class="metric-label">${label}</div>
    <div class="metric-sub">${sub}</div>
  </div>`;
}

/* ---------------------------------------------------------
   RENDER COMPONENTS
   --------------------------------------------------------- */
function renderComponents() {
  document.getElementById('compCards').innerHTML = Object.values(analysisResults).map(r => {
    const barColor   = r.healthPct>60 ? 'var(--accent-green)' : r.healthPct>30 ? 'var(--accent-amber)' : 'var(--accent-red)';
    const badgeClass = r.status==='OK' ? 'badge-ok' : r.status==='WARNING' ? 'badge-warn' : 'badge-crit';
    return `<div class="card">
      <div class="card-title"><span class="dot" style="background:${barColor}"></span> ${r.comp.icon} ${r.comp.name}</div>
      <div style="margin-bottom:16px;">
        <div class="health-bar-wrap" style="width:100%;height:8px;">
          <div class="health-bar" style="width:${r.healthPct}%;background:${barColor}"></div>
        </div>
        <div style="display:flex;justify-content:space-between;margin-top:6px;">
          <span style="font-family:'JetBrains Mono';font-size:20px;font-weight:700;color:var(--text-primary)">${r.healthPct.toFixed(0)}%</span>
          <span class="badge ${badgeClass}">${r.status}</span>
        </div>
      </div>
      <div class="param-grid">
        <div class="param-item"><span class="param-key">RUL</span><span class="param-val">${r.rul.toFixed(0)} hrs</span></div>
        <div class="param-item"><span class="param-key">Arrhenius ×</span><span class="param-val">${r.arrheniusFactor.toFixed(2)}</span></div>
        <div class="param-item"><span class="param-key">Q10 ×</span><span class="param-val">${r.q10Factor.toFixed(2)}</span></div>
        <div class="param-item"><span class="param-key">Miner D</span><span class="param-val">${r.minerDamage.toFixed(4)}</span></div>
        <div class="param-item"><span class="param-key">Weibull F(t)</span><span class="param-val">${(r.weibullF*100).toFixed(1)}%</span></div>
        <div class="param-item"><span class="param-key">Fouling η</span><span class="param-val">${(r.foulingEff*100).toFixed(1)}%</span></div>
        <div class="param-item"><span class="param-key">Eff. Life</span><span class="param-val">${r.effectiveLife.toFixed(0)} hrs</span></div>
        <div class="param-item"><span class="param-key">Base Life</span><span class="param-val">${r.comp.baseLife} hrs</span></div>
      </div>
    </div>`;
  }).join('');
}

/* ---------------------------------------------------------
   RENDER EDITABLE PARAMS
   --------------------------------------------------------- */
function renderParams() {
  const makeGrid = (comp, keys) => keys.map(([key, label]) =>
    `<div class="param-item">
      <span class="param-key">${label}</span>
      <input class="param-input" id="param_${comp.id}_${key}" value="${comp[key]}">
    </div>`
  ).join('');

  document.getElementById('paramsArrhenius').innerHTML = COMPONENTS.map(c =>
    `<div style="margin:8px 0">
      <span style="font-size:12px;color:var(--text-secondary);font-weight:600">${c.icon} ${c.name}</span>
      <div class="param-grid" style="margin-top:6px">${makeGrid(c,[['Ea','Ea (J/mol)'],['A','A (pre-exp)']])}</div>
    </div>`).join('');

  document.getElementById('paramsMiner').innerHTML = COMPONENTS.map(c =>
    `<div style="margin:8px 0">
      <span style="font-size:12px;color:var(--text-secondary);font-weight:600">${c.icon} ${c.name}</span>
      <div class="param-grid" style="margin-top:6px">${makeGrid(c,[['baseLife','Base Life (hrs)']])}</div>
    </div>`).join('');

  document.getElementById('paramsQ10').innerHTML = COMPONENTS.map(c =>
    `<div style="margin:8px 0">
      <span style="font-size:12px;color:var(--text-secondary);font-weight:600">${c.icon} ${c.name}</span>
      <div class="param-grid" style="margin-top:6px">${makeGrid(c,[['q10','Q10'],['Tref','T_ref (°C)']])}</div>
    </div>`).join('');

  document.getElementById('paramsWeibull').innerHTML = COMPONENTS.map(c =>
    `<div style="margin:8px 0">
      <span style="font-size:12px;color:var(--text-secondary);font-weight:600">${c.icon} ${c.name}</span>
      <div class="param-grid" style="margin-top:6px">${makeGrid(c,[['beta','β (shape)'],['eta','η (scale, hrs)']])}</div>
    </div>`).join('');

  document.getElementById('paramsFouling').innerHTML = COMPONENTS.map(c =>
    `<div style="margin:8px 0">
      <span style="font-size:12px;color:var(--text-secondary);font-weight:600">${c.icon} ${c.name}</span>
      <div class="param-grid" style="margin-top:6px">${makeGrid(c,[['foulingLambda','λ (fouling rate)']])}</div>
    </div>`).join('');
}

/* ---------------------------------------------------------
   CHART OPTIONS (shared base)
   --------------------------------------------------------- */
const baseOpts = {
  responsive: true, maintainAspectRatio: false,
  plugins: { legend: { labels: { color:'#7a8baa', font:{ family:'JetBrains Mono', size:10 } } } },
  scales: {
    x: { ticks:{ color:'#4a5678', font:{ family:'JetBrains Mono', size:9 } }, grid:{ color:'rgba(30,45,74,0.3)' } },
    y: { ticks:{ color:'#4a5678', font:{ family:'JetBrains Mono', size:9 } }, grid:{ color:'rgba(30,45,74,0.3)' } }
  }
};
const PALETTE = ['#3b82f6','#06b6d4','#10b981','#f59e0b','#ef4444','#8b5cf6','#ec4899','#6366f1'];

function destroyChart(id) { if (charts[id]) { charts[id].destroy(); delete charts[id]; } }

/* ---------------------------------------------------------
   TEMPERATURE & DAMAGE CHARTS (Dashboard)
   --------------------------------------------------------- */
function renderTempChart() {
  destroyChart('chartTemp');
  const step = Math.max(1, Math.floor(rawData.length / 200));
  const labels=[], cool=[], amb=[];
  for (let i=0; i<rawData.length; i+=step) {
    labels.push(rawData[i].hours.toFixed(0));
    cool.push(rawData[i].coolant_temp);
    amb.push(rawData[i].ambient_temp);
  }
  charts.chartTemp = new Chart(document.getElementById('chartTemp'), {
    type: 'line',
    data: { labels, datasets: [
      { label:'Coolant °C', data:cool, borderColor:'#ef4444', borderWidth:1.5, pointRadius:0, tension:0.3,
        fill:{ target:'origin', above:'rgba(239,68,68,0.05)' } },
      { label:'Ambient °C', data:amb,  borderColor:'#3b82f6', borderWidth:1.5, pointRadius:0, tension:0.3 },
    ]},
    options: { ...baseOpts }
  });
}

function renderDamageChart() {
  destroyChart('chartDamage');
  const names=[], damages=[], colors=[];
  Object.values(analysisResults).forEach(r => {
    names.push(r.comp.name);
    damages.push(r.minerDamage);
    colors.push(r.minerDamage>0.7 ? '#ef4444' : r.minerDamage>0.4 ? '#f59e0b' : '#10b981');
  });
  charts.chartDamage = new Chart(document.getElementById('chartDamage'), {
    type: 'bar',
    data: { labels:names, datasets:[{ label:'Miner Damage D', data:damages, backgroundColor:colors, borderRadius:6 }] },
    options: { ...baseOpts, indexAxis:'y' }
  });
}

/* ---------------------------------------------------------
   CHARTS TAB
   --------------------------------------------------------- */
function renderCharts() {
  const hrs = []; for (let t=0; t<=30000; t+=500) hrs.push(t);
  const tmps = []; for (let t=60; t<=130; t+=2) tmps.push(t);

  /* Weibull */
  destroyChart('chartWeibull');
  charts.chartWeibull = new Chart(document.getElementById('chartWeibull'), {
    type:'line',
    data:{ labels:hrs, datasets: COMPONENTS.map((c,i)=>({
      label:c.name,
      data: hrs.map(t => 1-Math.exp(-Math.pow(t/c.eta, c.beta))),
      borderColor: PALETTE[i], borderWidth:1.5, pointRadius:0, tension:0.3
    }))},
    options:{ ...baseOpts }
  });

  /* Fouling */
  destroyChart('chartFouling');
  const avgDust = rawData.length ? rawData.reduce((s,d)=>s+d.dust,0)/rawData.length : 4;
  charts.chartFouling = new Chart(document.getElementById('chartFouling'), {
    type:'line',
    data:{ labels:hrs, datasets: COMPONENTS.map((c,i)=>({
      label:c.name,
      data: hrs.map(t => Math.exp(-c.foulingLambda * avgDust * t / 1000) * 100),
      borderColor: PALETTE[i], borderWidth:1.5, pointRadius:0, tension:0.3
    }))},
    options:{ ...baseOpts, scales:{ ...baseOpts.scales, y:{ ...baseOpts.scales.y, min:0, max:100 } } }
  });

  /* Arrhenius */
  destroyChart('chartArrhenius');
  charts.chartArrhenius = new Chart(document.getElementById('chartArrhenius'), {
    type:'line',
    data:{ labels:tmps, datasets: COMPONENTS.slice(0,4).map((c,i)=>({
      label:c.name,
      data: tmps.map(t => {
        const kRef = c.A * Math.exp(-c.Ea/(R_GAS*(c.Tref+273.15)));
        return c.A * Math.exp(-c.Ea/(R_GAS*(t+273.15))) / kRef;
      }),
      borderColor: PALETTE[i], borderWidth:1.5, pointRadius:0, tension:0.3
    }))},
    options:{ ...baseOpts, scales:{ ...baseOpts.scales, y:{ ...baseOpts.scales.y, type:'logarithmic' } } }
  });

  /* Q10 */
  destroyChart('chartQ10');
  charts.chartQ10 = new Chart(document.getElementById('chartQ10'), {
    type:'line',
    data:{ labels:tmps, datasets: COMPONENTS.slice(0,4).map((c,i)=>({
      label:c.name,
      data: tmps.map(t => Math.pow(c.q10, (t-c.Tref)/10)),
      borderColor: PALETTE[i], borderWidth:1.5, pointRadius:0, tension:0.3
    }))},
    options:{ ...baseOpts }
  });
}

/* ---------------------------------------------------------
   MONTE CARLO
   --------------------------------------------------------- */
function runMonteCarlo() {
  const N         = 5000;
  const avgTemp   = rawData.reduce((s,d)=>s+d.coolant_temp,0)/rawData.length;
  const avgDust   = rawData.reduce((s,d)=>s+d.dust,0)/rawData.length;
  const currentHM = Math.max(...rawData.map(d=>d.hours));
  const ohRate    = rawData.filter(d=>d.coolant_temp>100).length / rawData.length;

  const mc = {};
  COMPONENTS.forEach(comp => {
    const ruls = [];
    for (let i=0; i<N; i++) {
      const tVar  = avgTemp + (Math.random()-0.5)*16;
      const dVar  = avgDust * (0.7 + Math.random()*0.6);
      const ohF   = 1 + Math.random()*ohRate*5;
      const kAvg  = comp.A * Math.exp(-comp.Ea/(R_GAS*(tVar+273.15)));
      const kRef  = comp.A * Math.exp(-comp.Ea/(R_GAS*(comp.Tref+273.15)));
      const arrF  = kAvg/kRef;
      const q10F  = Math.pow(comp.q10,(tVar-comp.Tref)/10);
      const foulF = Math.exp(-comp.foulingLambda*dVar*currentHM/1000);
      const eff   = comp.baseLife / (arrF*q10F*ohF/foulF);
      ruls.push(Math.max(0, eff-currentHM));
    }
    ruls.sort((a,b)=>a-b);
    mc[comp.id] = {
      p10:  ruls[Math.floor(N*0.1)],
      p50:  ruls[Math.floor(N*0.5)],
      p90:  ruls[Math.floor(N*0.9)],
      mean: ruls.reduce((a,b)=>a+b,0)/N
    };
  });

  /* Chart */
  destroyChart('chartMC');
  charts.chartMC = new Chart(document.getElementById('chartMC'), {
    type:'bar',
    data:{
      labels: COMPONENTS.map(c=>c.name),
      datasets:[
        { label:'P10 (Pessimistic)', data:COMPONENTS.map(c=>mc[c.id].p10), backgroundColor:'rgba(239,68,68,0.7)',  borderRadius:4 },
        { label:'P50 (Median)',      data:COMPONENTS.map(c=>mc[c.id].p50), backgroundColor:'rgba(59,130,246,0.7)', borderRadius:4 },
        { label:'P90 (Optimistic)',  data:COMPONENTS.map(c=>mc[c.id].p90), backgroundColor:'rgba(16,185,129,0.7)', borderRadius:4 },
      ]
    },
    options:{ ...baseOpts }
  });

  /* Table */
  let html = '<table class="comp-table"><thead><tr><th>Component</th><th>P10</th><th>P50</th><th>P90</th><th>Mean RUL</th></tr></thead><tbody>';
  COMPONENTS.forEach(c => {
    const m = mc[c.id];
    html += `<tr>
      <td class="comp-name">${c.icon} ${c.name}</td>
      <td style="font-family:'JetBrains Mono';color:var(--accent-red)">${m.p10.toFixed(0)} hrs</td>
      <td style="font-family:'JetBrains Mono';color:var(--accent-blue)">${m.p50.toFixed(0)} hrs</td>
      <td style="font-family:'JetBrains Mono';color:var(--accent-green)">${m.p90.toFixed(0)} hrs</td>
      <td style="font-family:'JetBrains Mono';font-weight:600">${m.mean.toFixed(0)} hrs</td>
    </tr>`;
  });
  html += '</tbody></table>';
  document.getElementById('mcResults').innerHTML = html;
}

/* ---------------------------------------------------------
   MAINTENANCE PLAN
   --------------------------------------------------------- */
function renderMaintenance() {
  let html = `<thead><tr>
    <th>Priority</th><th>Component</th><th>Action</th>
    <th>RUL</th><th>Recommended At (HM)</th><th>Urgency</th>
  </tr></thead><tbody>`;
  Object.values(analysisResults).sort((a,b)=>a.rul-b.rul).forEach((r,i) => {
    const action     = r.status==='CRITICAL' ? 'Replace / Overhaul Immediately'
                     : r.status==='WARNING'  ? 'Schedule Replacement'
                     : 'Monitor — Next Inspection';
    const recHM      = r.currentHM + r.rul*0.7;
    const badgeClass = r.status==='OK' ? 'badge-ok' : r.status==='WARNING' ? 'badge-warn' : 'badge-crit';
    html += `<tr>
      <td style="font-family:'JetBrains Mono';font-weight:700;color:var(--accent-cyan)">#${i+1}</td>
      <td class="comp-name">${r.comp.icon} ${r.comp.name}</td>
      <td style="font-size:12px">${action}</td>
      <td style="font-family:'JetBrains Mono';font-weight:600;color:${r.rul<1000?'var(--accent-red)':'var(--text-primary)'}">${r.rul.toFixed(0)} hrs</td>
      <td style="font-family:'JetBrains Mono'">${recHM.toFixed(0)} hrs</td>
      <td><span class="badge ${badgeClass}">${r.status}</span></td>
    </tr>`;
  });
  html += '</tbody>';
  document.getElementById('maintTable').innerHTML = html;
}

/* ---------------------------------------------------------
   EXPORT / DOWNLOAD
   --------------------------------------------------------- */
function downloadReport() {
  let csv = 'Component,Health%,RUL_hrs,Miner_D,Weibull_Ft,Fouling_Eff,Arrhenius_Factor,Q10_Factor,Effective_Life,Status\n';
  Object.values(analysisResults).forEach(r => {
    csv += `${r.comp.name},${r.healthPct.toFixed(1)},${r.rul.toFixed(0)},${r.minerDamage.toFixed(4)},`
         + `${r.weibullF.toFixed(4)},${r.foulingEff.toFixed(4)},${r.arrheniusFactor.toFixed(4)},`
         + `${r.q10Factor.toFixed(4)},${r.effectiveLife.toFixed(0)},${r.status}\n`;
  });
  dl(csv, 'pc1250_cooling_lifetime_report.csv', 'text/csv');
}

function downloadJSON() {
  const out = { generated: new Date().toISOString(), components:{} };
  Object.entries(analysisResults).forEach(([id,r]) => {
    out.components[id] = {
      name: r.comp.name, healthPct:+r.healthPct.toFixed(1), rul:+r.rul.toFixed(0),
      minerDamage:+r.minerDamage.toFixed(4), weibullF:+r.weibullF.toFixed(4),
      foulingEff:+r.foulingEff.toFixed(4), arrheniusFactor:+r.arrheniusFactor.toFixed(4),
      q10Factor:+r.q10Factor.toFixed(4), effectiveLife:+r.effectiveLife.toFixed(0), status:r.status
    };
  });
  dl(JSON.stringify(out,null,2), 'pc1250_cooling_lifetime.json', 'application/json');
}

function dl(content, name, type) {
  const a = Object.assign(document.createElement('a'), {
    href: URL.createObjectURL(new Blob([content],{type})),
    download: name
  });
  a.click();
}
</script>
</body>
</html>
