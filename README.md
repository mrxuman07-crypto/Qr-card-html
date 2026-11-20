<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Hotel Lakhimi — Printable QR Table Cards</title>
  <style>
    :root {
      --primary: #991b1b;
      --accent: #b45309;
      --text: #111827;
      --muted: #4b5563;
      --border: #d1d5db;
    }
    * { box-sizing: border-box; font-family: system-ui, -apple-system, sans-serif; }
    body { margin: 0; background: #f3f4f6; color: var(--text); }

    /* Control Bar (Hidden when printing) */
    .controls {
      background: #fff; border-bottom: 1px solid var(--border);
      padding: 14px 16px; position: sticky; top: 0; z-index: 20;
    }
    .controls-inner {
      max-width: 900px; margin: 0 auto; display: flex; flex-wrap: wrap; gap: 10px; align-items: center;
    }
    .controls input {
      flex: 1; min-width: 240px; padding: 9px 12px; border: 1px solid var(--border);
      border-radius: 8px; font-size: 13px;
    }
    .btn {
      background: var(--primary); color: #fff; border: none; padding: 9px 14px;
      border-radius: 8px; font-weight: 700; font-size: 13px; cursor: pointer;
    }
    .btn.secondary { background: #111827; }

    /* Printable A4 Grid */
    .sheet {
      max-width: 900px; margin: 18px auto; padding: 0 12px 30px;
      display: grid; grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); gap: 16px;
    }

    .table-card {
      background: #fff;
      border: 1.5px dashed #9ca3af;
      border-radius: 14px;
      padding: 18px 14px;
      text-align: center;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: space-between;
      page-break-inside: avoid;
      break-inside: avoid;
    }

    .hotel-title {
      font-size: 18px; font-weight: 800; color: var(--primary);
      letter-spacing: 0.4px; margin: 0;
    }
    .hotel-sub {
      font-size: 11px; color: var(--muted); margin: 2px 0 10px;
      text-transform: uppercase; letter-spacing: 0.8px;
    }

    .table-badge {
      background: var(--primary); color: #fff;
      font-size: 15px; font-weight: 800;
      padding: 5px 18px; border-radius: 999px;
      margin-bottom: 12px; letter-spacing: 0.5px;
    }
    .table-badge.private {
      background: var(--accent);
    }

    .qr-frame {
      padding: 10px; border: 2px solid #f3f4f6; border-radius: 12px;
      background: #fff; margin-bottom: 10px;
    }
    .qr-frame img {
      width: 165px; height: 165px; display: block;
    }

    .scan-cta {
      font-size: 13px; font-weight: 700; color: var(--text); margin: 2px 0;
    }
    .scan-steps {
      font-size: 11px; color: var(--muted); line-height: 1.4; margin: 2px 0 6px;
    }
    .url-hint {
      font-size: 9px; color: #9ca3af; word-break: break-all;
    }

    @media print {
      body { background: #fff; }
      .controls { display: none !important; }
      .sheet {
        max-width: 100%; margin: 0; padding: 8mm;
        grid-template-columns: repeat(3, 1fr); gap: 8mm;
      }
      .table-card {
        border: 1px dashed #9ca3af; padding: 12px 10px;
      }
      .qr-frame img {
        width: 135px; height: 135px;
      }
    }
  </style>
</head>
<body>

<div class="controls">
  <div class="controls-inner">
    <input type="text" id="baseUrlInput" placeholder="Paste your live site URL (e.g. https://username.github.io/hotel-lakhimi/)" />
    <button class="btn" onclick="renderCards()">Generate All 17 Cards</button>
    <button class="btn secondary" onclick="window.print()">🖨️ Print / Save PDF</button>
  </div>
</div>

<div class="sheet" id="cardsContainer"></div>

<script>
  const TABLES = [
    "1","2","3","4","5","6","7","8","9","10",
    "11","12","13","14","15","Private 1","Private 2"
  ];

  // Auto-detect current GitHub Pages folder URL if hosted in the same repo
  const defaultBase = window.location.href.includes("http")
    ? window.location.href.replace(/qr-cards\.html.*$/, "")
    : "https://yourname.github.io/hotel-lakhimi/";

  document.getElementById("baseUrlInput").value = defaultBase;

  function normalizeBaseUrl(raw) {
    let url = (raw || "").trim();
    if (!url) url = defaultBase;
    if (!url.endsWith("/")) url += "/";
    return url;
  }

  function renderCards() {
    const base = normalizeBaseUrl(document.getElementById("baseUrlInput").value);
    const container = document.getElementById("cardsContainer");

    container.innerHTML = TABLES.map(t => {
      const isPrivate = t.startsWith("Private");
      const label = isPrivate ? t.toUpperCase() : `TABLE ${t}`;
      const targetUrl = `${base}?table=${encodeURIComponent(t)}`;
      const qrImg = `https://api.qrserver.com/v1/create-qr-code/?size=300x300&margin=6&data=${encodeURIComponent(targetUrl)}`;

      return `
        <div class="table-card">
          <div>
            <h2 class="hotel-title">HOTEL LAKHIMI</h2>
            <div class="hotel-sub">Self-Order & Table Service</div>
          </div>

          <div class="table-badge ${isPrivate ? "private" : ""}">${label}</div>

          <div class="qr-frame">
            <img src="${qrImg}" alt="QR Code for ${label}" loading="lazy" />
          </div>

          <div>
            <div class="scan-cta">Scan to View Menu & Order</div>
            <div class="scan-steps">1. Scan QR with Phone Camera<br>2. Select Dishes & Place Order<br>3. Tap "Notify" to Call Waiter</div>
            <div class="url-hint">${targetUrl}</div>
          </div>
        </div>
      `;
    }).join("");
  }

  renderCards();
</script>
</body>
</html>
