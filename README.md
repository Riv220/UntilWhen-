<!doctype html>
<html lang="he" dir="rtl">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>ספירה לאחור</title>

  <style>
    :root { font-family: Arial, sans-serif; }
    body{ margin:0; padding:24px; background:#0b0f14; color:#e8eef6; }
    .wrap{ max-width:520px; margin:0 auto; }
    h1{ margin:0 0 14px; font-size:22px; font-weight:700; }
    .card{
      background:#121a24; border:1px solid #1e2a3a;
      border-radius:16px; padding:16px; margin:12px 0;
      box-shadow:0 6px 18px rgba(0,0,0,.25);
    }
    .row{ display:flex; gap:10px; align-items:center; justify-content:space-between; }
    .label{ opacity:.88; font-size:14px; }
    .pill{
      font-size:12px; padding:6px 10px; border-radius:999px;
      border:1px solid #2a3b52; background:#0f1620;
      white-space:nowrap;
    }
    .big{ font-size:56px; font-weight:800; line-height:1; margin:10px 0 8px; }
    .sub{ opacity:.88; font-size:14px; }
    .green{ color:#9ff2c3; }
    .orange{ color:#ffd08a; }
    .gray{ color:#a9b7c6; }

    button{
      width:100%;
      padding:12px 14px; border-radius:12px;
      border:1px solid #2a3b52; background:#0f1620; color:#e8eef6;
      font-size:14px; cursor:pointer;
      margin-top:12px;
    }

    .modalBack{
      position:fixed; inset:0; display:none;
      background:rgba(0,0,0,.55);
      align-items:center; justify-content:center;
      padding:18px;
    }
    .modal{
      width:100%; max-width:520px;
      background:#121a24; border:1px solid #1e2a3a;
      border-radius:16px; padding:16px;
    }
    .modal h2{ margin:0 0 10px; font-size:18px; }
    .hint{ opacity:.88; font-size:13px; margin:0 0 12px; }
  </style>

  <script defer>
    // Fixed dates (no auto year switching)
    const TARGET_LETTER = new Date(2026, 1, 20, 0, 0, 0, 0); // 20.02.2026
    const TARGET_START  = new Date(2026, 3, 7, 0, 0, 0, 0);  // 07.04.2026

    function todayMidnight() {
      const now = new Date();
      return new Date(now.getFullYear(), now.getMonth(), now.getDate(), 0, 0, 0, 0);
    }

    function daysUntil(target) {
      const t = todayMidnight();
      return Math.floor((target - t) / (1000 * 60 * 60 * 24));
    }

    function setView(daysEl, textEl, days) {
      daysEl.classList.remove("green", "orange", "gray");

      if (days > 7) {
        daysEl.classList.add("orange");
        daysEl.textContent = String(days);
        textEl.textContent = `נותרו ${days} ימים`;
        return;
      }

      if (days > 0) {
        daysEl.classList.add("green");
        daysEl.textContent = String(days);
        textEl.textContent = (days === 1) ? "נותר יום אחד" : `נותרו ${days} ימים`;
        return;
      }

      if (days === 0) {
        daysEl.classList.add("green");
        daysEl.textContent = "0";
        textEl.textContent = "היום זה היום";
        return;
      }

      daysEl.classList.add("gray");
      daysEl.textContent = "0";
      textEl.textContent = "התאריך עבר";
    }

    function render() {
      const daysLetterEl = document.getElementById("daysLetter");
      const textLetterEl = document.getElementById("textLetter");
      const daysStartEl  = document.getElementById("daysStart");
      const textStartEl  = document.getElementById("textStart");

      if (!daysLetterEl || !textLetterEl || !daysStartEl || !textStartEl) return;

      setView(daysLetterEl, textLetterEl, daysUntil(TARGET_LETTER));
      setView(daysStartEl,  textStartEl,  daysUntil(TARGET_START));
    }

    window.addEventListener("DOMContentLoaded", () => {
      try {
        render();
        setInterval(render, 60 * 1000);
      } catch (e) {
        // If something breaks, show it on screen instead of "טוען…"
        const t1 = document.getElementById("textLetter");
        const t2 = document.getElementById("textStart");
        if (t1) t1.textContent = "שגיאת JS - פתח Console";
        if (t2) t2.textContent = "שגיאת JS - פתח Console";
      }

      const modalBack = document.getElementById("modalBack");
      const openBtn = document.getElementById("openLetter");
      const closeBtn = document.getElementById("closeModal");

      if (openBtn && modalBack) openBtn.addEventListener("click", () => modalBack.style.display = "flex");
      if (closeBtn && modalBack) closeBtn.addEventListener("click", () => modalBack.style.display = "none");
      if (modalBack) modalBack.addEventListener("click", (e) => { if (e.target === modalBack) modalBack.style.display = "none"; });
    });
  </script>
</head>

<body>
  <div class="wrap">
    <h1>ספירה לאחור</h1>

    <div class="card">
      <div class="row">
        <div class="label">מכתב התפטרות</div>
        <div class="pill">20.02.2026</div>
      </div>
      <div class="big orange" id="daysLetter">—</div>
      <div class="sub" id="textLetter">טוען…</div>
      <button id="openLetter">הצג פרטי מכתב (ללא נוסח)</button>
    </div>

    <div class="card">
      <div class="row">
        <div class="label">תחילת עבודה רשמית</div>
        <div class="pill">07.04.2026</div>
      </div>
      <div class="big orange" id="daysStart">—</div>
      <div class="sub" id="textStart">טוען…</div>
    </div>
  </div>

  <div class="modalBack" id="modalBack">
    <div class="modal">
      <h2>מכתב התפטרות</h2>
      <p class="hint">אין כאן נוסח. רק חלון לפרטים.</p>
      <div class="card" style="margin:0; background:#0f1620;">
        <div class="row">
          <div class="label">תאריך יעד</div>
          <div class="pill">20.02.2026</div>
        </div>
        <div class="sub" style="margin-top:10px;">סטטוס: <span class="gray">לא הוגדר</span></div>
      </div>
      <button id="closeModal">סגור</button>
    </div>
  </div>
</body>
</html>
