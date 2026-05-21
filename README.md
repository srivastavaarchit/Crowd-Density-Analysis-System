<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI Crowd Density Analysis — README</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Sora:wght@300;400;600;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #080c10;
    --surface: #0e1318;
    --surface2: #141b22;
    --border: rgba(0,230,180,0.15);
    --accent: #00e6b4;
    --accent2: #0088ff;
    --accent3: #ff4d6d;
    --text: #e8f0f8;
    --muted: #7a90a8;
    --mono: 'Space Mono', monospace;
    --sans: 'Sora', sans-serif;
  }
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--sans);
    font-size: 15px;
    line-height: 1.7;
    min-height: 100vh;
  }

  /* ── HERO ── */
  .hero {
    position: relative;
    overflow: hidden;
    padding: 80px 48px 64px;
    border-bottom: 1px solid var(--border);
  }
  .hero::before {
    content: '';
    position: absolute;
    inset: 0;
    background:
      radial-gradient(ellipse 60% 50% at 80% 40%, rgba(0,230,180,0.07) 0%, transparent 70%),
      radial-gradient(ellipse 40% 60% at 10% 80%, rgba(0,136,255,0.06) 0%, transparent 70%);
    pointer-events: none;
  }
  .hero-grid {
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient(rgba(0,230,180,0.04) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,230,180,0.04) 1px, transparent 1px);
    background-size: 48px 48px;
    pointer-events: none;
  }
  .badge {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    background: rgba(0,230,180,0.1);
    border: 1px solid rgba(0,230,180,0.3);
    color: var(--accent);
    font-family: var(--mono);
    font-size: 11px;
    padding: 4px 12px;
    border-radius: 2px;
    letter-spacing: 0.08em;
    margin-bottom: 24px;
    text-transform: uppercase;
  }
  .badge::before { content: '▶'; font-size: 8px; }
  h1 {
    font-family: var(--sans);
    font-size: clamp(32px, 5vw, 52px);
    font-weight: 700;
    line-height: 1.1;
    letter-spacing: -0.02em;
    margin-bottom: 20px;
  }
  h1 span.glow {
    color: var(--accent);
    text-shadow: 0 0 40px rgba(0,230,180,0.4);
  }
  .hero-sub {
    font-size: 16px;
    color: var(--muted);
    max-width: 600px;
    margin-bottom: 40px;
    font-weight: 300;
    line-height: 1.8;
  }
  .stat-row {
    display: flex;
    gap: 32px;
    flex-wrap: wrap;
  }
  .stat {
    display: flex;
    flex-direction: column;
  }
  .stat-val {
    font-family: var(--mono);
    font-size: 28px;
    font-weight: 700;
    color: var(--accent);
    line-height: 1;
  }
  .stat-label {
    font-size: 11px;
    color: var(--muted);
    text-transform: uppercase;
    letter-spacing: 0.1em;
    margin-top: 4px;
  }

  /* ── LAYOUT ── */
  .container { max-width: 960px; margin: 0 auto; padding: 0 48px; }
  section { padding: 56px 48px; border-bottom: 1px solid var(--border); }
  .section-label {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--accent);
    letter-spacing: 0.15em;
    text-transform: uppercase;
    margin-bottom: 16px;
    display: flex;
    align-items: center;
    gap: 10px;
  }
  .section-label::after {
    content: '';
    flex: 1;
    max-width: 60px;
    height: 1px;
    background: var(--accent);
    opacity: 0.4;
  }
  h2 {
    font-family: var(--sans);
    font-size: 24px;
    font-weight: 600;
    margin-bottom: 28px;
    letter-spacing: -0.01em;
  }

  /* ── PIPELINE TABLE ── */
  .pipeline {
    width: 100%;
    border-collapse: collapse;
    font-size: 13.5px;
  }
  .pipeline th {
    font-family: var(--mono);
    font-size: 10px;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--muted);
    padding: 10px 16px;
    text-align: left;
    border-bottom: 1px solid var(--border);
  }
  .pipeline td {
    padding: 14px 16px;
    border-bottom: 1px solid rgba(255,255,255,0.04);
    vertical-align: middle;
  }
  .pipeline tr:hover td { background: rgba(0,230,180,0.03); }
  .pipeline tr:last-child td { border-bottom: none; }
  .tag {
    display: inline-block;
    font-family: var(--mono);
    font-size: 11px;
    padding: 3px 10px;
    border-radius: 2px;
    background: rgba(0,136,255,0.12);
    color: #5ab4ff;
    border: 1px solid rgba(0,136,255,0.25);
  }
  .stage-num {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--muted);
    background: var(--surface2);
    border: 1px solid var(--border);
    padding: 2px 8px;
    border-radius: 2px;
  }

  /* ── FEATURES GRID ── */
  .features-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 16px;
  }
  .feat-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 20px;
    position: relative;
    transition: border-color 0.2s, transform 0.2s;
  }
  .feat-card:hover {
    border-color: rgba(0,230,180,0.4);
    transform: translateY(-2px);
  }
  .feat-icon {
    font-size: 22px;
    margin-bottom: 12px;
    display: block;
  }
  .feat-title {
    font-size: 13.5px;
    font-weight: 600;
    margin-bottom: 6px;
    color: var(--text);
  }
  .feat-desc {
    font-size: 12.5px;
    color: var(--muted);
    line-height: 1.6;
  }

  /* ── INSTALL ── */
  .code-block {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 20px 24px;
    font-family: var(--mono);
    font-size: 13px;
    color: #a8d8a0;
    overflow-x: auto;
    margin-bottom: 16px;
    position: relative;
  }
  .code-block .comment { color: var(--muted); }
  .code-block .cmd { color: var(--accent); }

  /* ── METRICS ── */
  .metrics-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 12px;
    margin-bottom: 32px;
  }
  .metric-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 20px 16px;
    text-align: center;
  }
  .metric-val {
    font-family: var(--mono);
    font-size: 26px;
    font-weight: 700;
    display: block;
  }
  .metric-val.ok { color: var(--accent); }
  .metric-val.mid { color: var(--accent2); }
  .metric-label {
    font-size: 11px;
    color: var(--muted);
    text-transform: uppercase;
    letter-spacing: 0.1em;
    margin-top: 4px;
    display: block;
  }
  .metric-target {
    font-size: 10px;
    color: var(--accent);
    font-family: var(--mono);
    margin-top: 4px;
    display: block;
  }

  /* ── ZONES TABLE ── */
  .zones-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 13.5px;
  }
  .zones-table th {
    font-family: var(--mono);
    font-size: 10px;
    letter-spacing: 0.12em;
    color: var(--muted);
    padding: 8px 14px;
    text-align: left;
    border-bottom: 1px solid var(--border);
    text-transform: uppercase;
  }
  .zones-table td {
    padding: 12px 14px;
    border-bottom: 1px solid rgba(255,255,255,0.04);
    font-family: var(--mono);
    font-size: 13px;
  }
  .zones-table tr:last-child td { border-bottom: none; }
  .dot {
    display: inline-block;
    width: 8px; height: 8px;
    border-radius: 50%;
    margin-right: 8px;
    vertical-align: middle;
  }
  .dot-g { background: var(--accent); }
  .dot-b { background: var(--accent2); }

  /* ── STRUCTURE ── */
  .tree {
    font-family: var(--mono);
    font-size: 13px;
    color: var(--muted);
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 24px;
    line-height: 2;
  }
  .tree .dir { color: var(--accent2); }
  .tree .file { color: var(--text); }
  .tree .ann { color: var(--muted); font-size: 11px; }

  /* ── FOOTER ── */
  footer {
    padding: 40px 48px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 16px;
  }
  footer .copy {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--muted);
    letter-spacing: 0.05em;
  }
  .pill-row { display: flex; gap: 8px; flex-wrap: wrap; }
  .pill {
    font-family: var(--mono);
    font-size: 11px;
    padding: 4px 12px;
    border-radius: 2px;
    border: 1px solid;
    letter-spacing: 0.05em;
  }
  .pill-g { color: var(--accent); border-color: rgba(0,230,180,0.35); background: rgba(0,230,180,0.07); }
  .pill-b { color: #5ab4ff; border-color: rgba(0,136,255,0.35); background: rgba(0,136,255,0.07); }
  .pill-r { color: #ff8fa3; border-color: rgba(255,77,109,0.35); background: rgba(255,77,109,0.07); }

  /* ── ALERT BOX ── */
  .alert-box {
    border: 1px solid rgba(255,77,109,0.35);
    background: rgba(255,77,109,0.07);
    border-radius: 4px;
    padding: 16px 20px;
    display: flex;
    align-items: flex-start;
    gap: 12px;
    font-size: 13.5px;
  }
  .alert-icon { font-size: 18px; line-height: 1.4; }
  .alert-text { color: #ffc8d0; line-height: 1.6; }
  .alert-text strong { color: #ff8fa3; font-weight: 600; }

  @media (max-width: 680px) {
    .hero, section, footer { padding-left: 24px; padding-right: 24px; }
    .metrics-grid { grid-template-columns: repeat(2, 1fr); }
    h1 { font-size: 28px; }
  }
</style>
</head>
<body>

<!-- ══ HERO ══════════════════════════════════════════════════════════ -->
<div class="hero">
  <div class="hero-grid"></div>
  <div style="position:relative; max-width:960px; margin:0 auto;">
    <div class="badge">Computer Vision · Real-Time Analytics</div>
    <h1>AI-Based <span class="glow">Crowd Density</span><br>Analysis System</h1>
    <p class="hero-sub">
      Real-time person detection &amp; tracking across virtual monitoring zones —
      powered by YOLOv8m + ByteTrack with Gaussian heatmaps, density alerts,
      and a full analytics dashboard.
    </p>
    <div class="stat-row">
      <div class="stat">
        <span class="stat-val">93.2%</span>
        <span class="stat-label">Person Precision</span>
      </div>
      <div class="stat">
        <span class="stat-val">4</span>
        <span class="stat-label">Monitoring Zones</span>
      </div>
      <div class="stat">
        <span class="stat-val">568</span>
        <span class="stat-label">Frames Processed</span>
      </div>
      <div class="stat">
        <span class="stat-val">120</span>
        <span class="stat-label">Unique Persons Tracked</span>
      </div>
      <div class="stat">
        <span class="stat-val">CUDA</span>
        <span class="stat-label">Compute Device</span>
      </div>
    </div>
  </div>
</div>

<!-- ══ BADGES ══════════════════════════════════════════════════════════ -->
<section style="padding-top:28px; padding-bottom:28px; border-bottom:1px solid var(--border);">
  <div class="pill-row" style="max-width:960px; margin:0 auto;">
    <span class="pill pill-g">YOLOv8m</span>
    <span class="pill pill-g">ByteTrack</span>
    <span class="pill pill-g">PyTorch 2.10</span>
    <span class="pill pill-b">Python 3.12</span>
    <span class="pill pill-b">OpenCV 4.13</span>
    <span class="pill pill-b">Ultralytics ≥8.2</span>
    <span class="pill pill-r">CUDA GPU</span>
    <span class="pill pill-r">Kaggle Notebook</span>
  </div>
</section>

<!-- ══ PIPELINE ══════════════════════════════════════════════════════ -->
<section>
  <div style="max-width:960px; margin:0 auto;">
    <div class="section-label">01 — Architecture</div>
    <h2>Processing Pipeline</h2>
    <table class="pipeline">
      <thead>
        <tr>
          <th>Stage</th>
          <th>Component</th>
          <th>Purpose</th>
          <th>Output</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><span class="stage-num">01</span></td>
          <td><span class="tag">YOLOv8m</span></td>
          <td>Person detection with conf ≥ 0.40</td>
          <td>Bounding boxes, class scores</td>
        </tr>
        <tr>
          <td><span class="stage-num">02</span></td>
          <td><span class="tag">ByteTrack</span></td>
          <td>Persistent multi-object ID assignment</td>
          <td>Tracked trajectories per person</td>
        </tr>
        <tr>
          <td><span class="stage-num">03</span></td>
          <td><span class="tag">Polygon Zones</span></td>
          <td>Spatial zone partitioning (A–D)</td>
          <td>Per-zone head-count per frame</td>
        </tr>
        <tr>
          <td><span class="stage-num">04</span></td>
          <td><span class="tag">Gaussian Kernel</span></td>
          <td>Spatial density heatmap generation</td>
          <td>Normalised density overlay</td>
        </tr>
        <tr>
          <td><span class="stage-num">05</span></td>
          <td><span class="tag">Threshold Logic</span></td>
          <td>Overcrowding detection (≥12 persons/zone)</td>
          <td>Alert events + visual flags</td>
        </tr>
        <tr>
          <td><span class="stage-num">06</span></td>
          <td><span class="tag">CSV + JSON Logger</span></td>
          <td>Timestamped density event logging</td>
          <td>452 log rows across both videos</td>
        </tr>
        <tr>
          <td><span class="stage-num">07</span></td>
          <td><span class="tag">Matplotlib Dashboard</span></td>
          <td>Real-time analytics visualisation</td>
          <td>Annotated output video frames</td>
        </tr>
      </tbody>
    </table>
  </div>
</section>

<!-- ══ FEATURES ══════════════════════════════════════════════════════ -->
<section>
  <div style="max-width:960px; margin:0 auto;">
    <div class="section-label">02 — Capabilities</div>
    <h2>Key Features</h2>
    <div class="features-grid">
      <div class="feat-card">
        <span class="feat-icon">🎯</span>
        <div class="feat-title">High-Precision Detection</div>
        <div class="feat-desc">YOLOv8m achieves 93.2% person-class precision, exceeding the 90% target on COCO128 validation.</div>
      </div>
      <div class="feat-card">
        <span class="feat-icon">🔁</span>
        <div class="feat-title">Persistent Tracking</div>
        <div class="feat-desc">ByteTrack assigns stable IDs across frames, enabling unique person counts even through occlusions.</div>
      </div>
      <div class="feat-card">
        <span class="feat-icon">🗺️</span>
        <div class="feat-title">Virtual Zone Analysis</div>
        <div class="feat-desc">Four polygon monitoring zones (Left, Centre, Right, Entry) measure independent crowd densities.</div>
      </div>
      <div class="feat-card">
        <span class="feat-icon">🌡️</span>
        <div class="feat-title">Density Heatmaps</div>
        <div class="feat-desc">Gaussian kernel smoothing produces continuous spatial density maps overlaid on each frame.</div>
      </div>
      <div class="feat-card">
        <span class="feat-icon">🚨</span>
        <div class="feat-title">Overcrowding Alerts</div>
        <div class="feat-desc">Configurable threshold (≥12 persons/zone) triggers visual alerts and logs timestamped events.</div>
      </div>
      <div class="feat-card">
        <span class="feat-icon">📊</span>
        <div class="feat-title">Real-Time Dashboard</div>
        <div class="feat-desc">Matplotlib analytics display with bar charts, density trends, and per-zone statistics updated per frame.</div>
      </div>
      <div class="feat-card">
        <span class="feat-icon">🗂️</span>
        <div class="feat-title">Event Logging</div>
        <div class="feat-desc">CSV + JSON logs capture every frame's per-zone head-counts and alert states for post-analysis.</div>
      </div>
      <div class="feat-card">
        <span class="feat-icon">⚡</span>
        <div class="feat-title">GPU-Accelerated</div>
        <div class="feat-desc">Runs on CUDA (T4 GPU on Kaggle), achieving 5–6 FPS end-to-end on 848×478 input video.</div>
      </div>
    </div>
  </div>
</section>

<!-- ══ DATASET ══════════════════════════════════════════════════════ -->
<section>
  <div style="max-width:960px; margin:0 auto;">
    <div class="section-label">03 — Dataset</div>
    <h2>Input Videos</h2>
    <table class="zones-table" style="margin-bottom:24px;">
      <thead>
        <tr>
          <th>#</th>
          <th>File</th>
          <th>Duration</th>
          <th>Resolution</th>
          <th>FPS</th>
          <th>Frames</th>
          <th>Tracked Persons</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><span class="dot dot-g"></span>1</td>
          <td>WhatsApp_Video…9.48.37_PM.mp4</td>
          <td>14.9 s</td>
          <td>848×478</td>
          <td>30.0</td>
          <td>448</td>
          <td>91</td>
        </tr>
        <tr>
          <td><span class="dot dot-b"></span>2</td>
          <td>WhatsApp_Video…9.51.59_PM.mp4</td>
          <td>4.0 s</td>
          <td>848×478</td>
          <td>30.1</td>
          <td>120</td>
          <td>29</td>
        </tr>
      </tbody>
    </table>
    <p style="font-size:13px; color:var(--muted);">Videos are auto-located from <code style="color:var(--accent); font-family:var(--mono); font-size:12px;">/kaggle/input/</code> via recursive scan — no manual path configuration needed.</p>
  </div>
</section>

<!-- ══ METRICS ══════════════════════════════════════════════════════ -->
<section>
  <div style="max-width:960px; margin:0 auto;">
    <div class="section-label">04 — Performance</div>
    <h2>Accuracy Metrics — COCO128 Val (Person Class)</h2>
    <div class="metrics-grid">
      <div class="metric-card">
        <span class="metric-val ok">93.2%</span>
        <span class="metric-label">Precision</span>
        <span class="metric-target">✓ Target: ≥90%</span>
      </div>
      <div class="metric-card">
        <span class="metric-val mid">69.7%</span>
        <span class="metric-label">Recall</span>
        <span class="metric-target">—</span>
      </div>
      <div class="metric-card">
        <span class="metric-val mid">68.7%</span>
        <span class="metric-label">mAP@50</span>
        <span class="metric-target">—</span>
      </div>
      <div class="metric-card">
        <span class="metric-val mid">55.8%</span>
        <span class="metric-label">mAP@50-95</span>
        <span class="metric-target">—</span>
      </div>
    </div>

    <h2 style="font-size:18px; margin-bottom:16px;">Zone Density Summary</h2>
    <table class="zones-table">
      <thead>
        <tr>
          <th>Video</th>
          <th>Zone</th>
          <th>Mean Count</th>
          <th>Max Count</th>
          <th>Min Count</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td rowspan="4" style="color:var(--muted); font-size:12px; vertical-align:top; padding-top:14px;">Video 1 (14.9s)</td>
          <td><span class="dot dot-g"></span>Zone-A (Left)</td>
          <td>2.66</td>
          <td>6</td>
          <td>0</td>
        </tr>
        <tr>
          <td><span class="dot dot-g"></span>Zone-B (Centre)</td>
          <td>0.69</td>
          <td>4</td>
          <td>0</td>
        </tr>
        <tr>
          <td><span class="dot dot-g"></span>Zone-C (Right)</td>
          <td>0.84</td>
          <td>3</td>
          <td>0</td>
        </tr>
        <tr>
          <td><span class="dot dot-g"></span>Zone-D (Entry)</td>
          <td>0.00</td>
          <td>0</td>
          <td>0</td>
        </tr>
        <tr>
          <td rowspan="4" style="color:var(--muted); font-size:12px; vertical-align:top; padding-top:14px; border-top:1px solid var(--border);">Video 2 (4.0s)</td>
          <td style="border-top:1px solid var(--border);"><span class="dot dot-b"></span>Zone-A (Left)</td>
          <td style="border-top:1px solid var(--border);">3.50</td>
          <td style="border-top:1px solid var(--border);">6</td>
          <td style="border-top:1px solid var(--border);">2</td>
        </tr>
        <tr>
          <td><span class="dot dot-b"></span>Zone-B (Centre)</td>
          <td>6.33</td>
          <td>8</td>
          <td>5</td>
        </tr>
        <tr>
          <td><span class="dot dot-b"></span>Zone-C (Right)</td>
          <td>5.54</td>
          <td>7</td>
          <td>4</td>
        </tr>
        <tr>
          <td><span class="dot dot-b"></span>Zone-D (Entry)</td>
          <td>0.00</td>
          <td>0</td>
          <td>0</td>
        </tr>
      </tbody>
    </table>
  </div>
</section>

<!-- ══ SETUP ══════════════════════════════════════════════════════════ -->
<section>
  <div style="max-width:960px; margin:0 auto;">
    <div class="section-label">05 — Quick Start</div>
    <h2>Installation &amp; Setup</h2>
    <p style="color:var(--muted); margin-bottom:20px; font-size:13.5px;">Dependencies are auto-installed at runtime — just run the notebook top-to-bottom.</p>

    <div class="code-block">
      <span class="comment"># Dependencies installed automatically by Cell 1</span><br>
      <span class="cmd">ultralytics</span> &gt;= 8.2 &nbsp;&nbsp;&nbsp;<span class="comment"># YOLOv8 framework</span><br>
      <span class="cmd">supervision</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="comment"># ByteTrack wrapper</span><br>
      <span class="cmd">opencv-python</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="comment"># Frame processing</span><br>
      <span class="cmd">scipy</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="comment"># Gaussian heatmap kernel</span><br>
      <span class="cmd">matplotlib seaborn</span> &nbsp;<span class="comment"># Dashboard &amp; plots</span><br>
      <span class="cmd">scikit-learn pandas</span> <span class="comment"># Stats &amp; logging</span>
    </div>

    <div class="code-block">
      <span class="comment"># Runtime environment (Kaggle)</span><br>
      PyTorch  : <span class="cmd">2.10.0+cu128</span><br>
      Device   : <span class="cmd">cuda</span> &nbsp;&nbsp;&nbsp;<span class="comment"># T4 GPU recommended</span><br>
      OpenCV   : <span class="cmd">4.13.0</span><br>
      Python   : <span class="cmd">3.12</span>
    </div>

    <div class="alert-box" style="margin-top:4px;">
      <span class="alert-icon">⚠</span>
      <div class="alert-text"><strong>GPU Required.</strong> The notebook is optimised for a CUDA-enabled GPU. Enable GPU acceleration in Kaggle: <em>Settings → Accelerator → GPU T4 x2.</em> CPU mode will work but is significantly slower (≈1 FPS).</div>
    </div>
  </div>
</section>

<!-- ══ STRUCTURE ══════════════════════════════════════════════════════ -->
<section>
  <div style="max-width:960px; margin:0 auto;">
    <div class="section-label">06 — Structure</div>
    <h2>Notebook Sections</h2>
    <div class="tree">
      <span class="dir">📓 notebook.ipynb</span><br>
      &nbsp;&nbsp;├── <span class="file">§1 &nbsp;Environment Setup</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— auto-install &amp; GPU init</span><br>
      &nbsp;&nbsp;├── <span class="file">§2 &nbsp;Input Videos — Dataset</span> &nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— recursive video scan</span><br>
      &nbsp;&nbsp;├── <span class="file">§3 &nbsp;Model Loading</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— YOLOv8m weights</span><br>
      &nbsp;&nbsp;├── <span class="file">§4 &nbsp;Zone Configuration</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— polygon zone A–D</span><br>
      &nbsp;&nbsp;├── <span class="file">§5 &nbsp;Detection Engine</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— YOLOv8 inference loop</span><br>
      &nbsp;&nbsp;├── <span class="file">§6 &nbsp;ByteTrack Integration</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— persistent ID tracking</span><br>
      &nbsp;&nbsp;├── <span class="file">§7 &nbsp;Density Estimation</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— per-zone head-counts</span><br>
      &nbsp;&nbsp;├── <span class="file">§8 &nbsp;Heatmap Generation</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— Gaussian kernel overlay</span><br>
      &nbsp;&nbsp;├── <span class="file">§9 &nbsp;Alert System</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— threshold-based alerts</span><br>
      &nbsp;&nbsp;├── <span class="file">§10 Dashboard &amp; Visualisation</span> &nbsp;<span class="ann">— real-time matplotlib</span><br>
      &nbsp;&nbsp;├── <span class="file">§11 Video Output</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— annotated .mp4 export</span><br>
      &nbsp;&nbsp;├── <span class="file">§12 Accuracy Validation</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— COCO128 val metrics</span><br>
      &nbsp;&nbsp;├── <span class="file">§13 Log Analysis</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— CSV event review</span><br>
      &nbsp;&nbsp;└── <span class="file">§14 Final Summary</span> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="ann">— boxed stats report</span>
    </div>
  </div>
</section>

<!-- ══ OUTPUT ══════════════════════════════════════════════════════════ -->
<section>
  <div style="max-width:960px; margin:0 auto;">
    <div class="section-label">07 — Outputs</div>
    <h2>Generated Artefacts</h2>
    <div class="features-grid" style="grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));">
      <div class="feat-card">
        <span class="feat-icon">🎬</span>
        <div class="feat-title">Annotated Videos</div>
        <div class="feat-desc"><code style="font-family:var(--mono); font-size:11px; color:var(--accent);">output_video_1_*.mp4</code><br>Bounding boxes, IDs, zone overlays, and density HUD per frame.</div>
      </div>
      <div class="feat-card">
        <span class="feat-icon">📄</span>
        <div class="feat-title">Density Log (CSV)</div>
        <div class="feat-desc">452 rows — timestamp, video, zone, count, is_alert for every processed frame.</div>
      </div>
      <div class="feat-card">
        <span class="feat-icon">📊</span>
        <div class="feat-title">Accuracy Chart</div>
        <div class="feat-desc"><code style="font-family:var(--mono); font-size:11px; color:var(--accent);">accuracy_metrics.png</code><br>Horizontal bar chart: Precision / Recall / mAP@50 / mAP@50-95.</div>
      </div>
      <div class="feat-card">
        <span class="feat-icon">📋</span>
        <div class="feat-title">Summary Report</div>
        <div class="feat-desc">Console-printed ASCII table: model config, per-video stats, and accuracy results.</div>
      </div>
    </div>
  </div>
</section>

<!-- ══ FOOTER ══════════════════════════════════════════════════════════ -->
<footer>
  <div class="copy">
    AI CROWD DENSITY ANALYSIS SYSTEM &nbsp;·&nbsp; YOLOv8m + ByteTrack &nbsp;·&nbsp; Kaggle / Python 3.12
  </div>
  <div class="pill-row">
    <span class="pill pill-g">Precision 93.2%</span>
    <span class="pill pill-b">4 Zones</span>
    <span class="pill pill-r">CUDA GPU</span>
  </div>
</footer>

</body>
</html>
