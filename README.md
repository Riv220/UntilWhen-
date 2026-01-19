<!doctype html>
<html lang="he" dir="rtl">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>ספירה לאחור</title>
  <style>
    body{margin:0;padding:24px;background:#0b0f14;color:#e8eef6;font-family:Arial,sans-serif}
    .wrap{max-width:560px;margin:0 auto}
    h1{margin:0 0 14px;font-size:24px}
    .card{background:#121a24;border:1px solid #1e2a3a;border-radius:16px;padding:16px;margin:12px 0;box-shadow:0 6px 18px rgba(0,0,0,.25)}
    .row{display:flex;justify-content:space-between;align-items:center;gap:10px}
    .label{opacity:.88;font-size:14px}
    .pill{font-size:12px;padding:6px 10px;border-radius:999px;border:1px solid #2a3b52;background:#0f1620;white-space:nowrap}
    .big{font-size:56px;font-weight:800;line-height:1;margin:10px 0 8px}
    .sub{opacity:.88;font-size:14px}
    .green{color:#9ff2c3}.orange{color:#ffd08a}.gray{color:#a9b7c6}
    .btn{width:100%;padding:12px 14px;border-radius:12px;border:1px solid #2a3b52;background:#0f1620;color:#e8eef6;font-size:14px;cursor:pointer;margin-top:12px}
    .modalBack{position:fixed;inset:0;display:none;background:rgba(0,0,0,.55);align-items:center;justify-content:center;padding:18px}
    .modal{width:100%;max-width:560px;background:#121a24;border:1px solid #1e2a3a;border-radius:16px;padding:16px}
    .modal h2{margin:0 0 10px;font-size:18px}
    .hint{opacity:.88;font-size:13px;margin:0 0 12px}
    .err{margin-top:10px;font-size:12px;opacity:.9}
  </style>
</head>

<body>
  <div class="wrap">
    <h1>ספירה לאחור</h1>

    <!-- Debug: if you click and nothing changes, JS isn't running -->
    <button class="btn" onclick="document.getElementById('debug').textContent='JS עובד ✅';">
      בדיקת JS
    </button>
    <div id="debug" class="err"></div>

    <!-- 1) Letter first -->
    <div class="card">
      <div class="row">
        <div class="label">מכתב התפטרות</div>
        <div class="pill">20.02.2026</div>
      </div>
      <div class="big orange" id="daysLetter">—</div>
      <div class="sub" id="textLetter">טוען…</div>
      <button class="btn" id="openLetter">הצג פרטי מכתב (ללא נוסח)</button>
    </div>

    <!-- 2) Start work second -->
    <div class="card">
      <div class="row">
        <div class="label">תחילת עבודה רשמית</div>
        <div class="pill">07.04.2026</div>
      </div>
      <div class="big orange" id="daysStart">—</div>
      <div class="sub" id="textStart">טוען…</div>
    </div>
  </div>

  <!-- Modal (no letter text) -->
  <div class="modalBack" id="modalBack">
    <div class="modal">
      <h2>מכתב התפטרות</h2>
      <p class="hint">אין כאן נוסח. רק חלון לפרטים.</p>
      <div class="card" style="margin:0;background:#0f1620">
        <div class="row">
          <div class="label">תאריך יעד</div>
          <div class="pill">20.02.2026</div>
        </div>
        <div class="sub" style="margin-top:10px">סטטוס: <span class="gray">לא הוגדר</span></div>
      </div>
      <button class="btn" id="closeModal">סגור</button>
    </div>
  </div>

  <script>
    // Fixed dates (no auto year switching)
    var TARGET_LETTER = new Date(2026, 1, 20, 0, 0, 0, 0); // 20.02.2026
    var TARGET_START  = new Date(2026, 3, 7, 0, 0, 0, 0);  // 07.04.2026

    function todayMidnight() {
      var now = new Date();
      return new Date(now.getFullYear(), now.getMonth(), now.getDate(), 0, 0, 0, 0);
    }

    function daysUntil(target) {
      var t = todayMidnight();
      return Math.floor((target - t) / (1000 * 60 * 60 * 24));
    }

    function setView(daysEl, textEl, days) {
      daysEl.classList.remove("green", "orange", "gray");

      if (days > 7) {
        daysEl.classList.add("orange");
        daysEl.textContent = String(days);
        textEl.textContent = "נותרו " + days + " ימים";
        return;
      }
      if (days > 0) {
        daysEl.classList.add("green");
        daysEl.textContent = String(days);
        textEl.textContent = (days === 1) ? "נותר יום אחד" : ("נותרו " + days + " ימים");
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
      var daysLetterEl = document.getElementById("daysLetter");
      var textLetterEl = document.getElementById("textLetter");
      var daysStartEl  = document.getElementById("daysStart");
      var textStartEl  = document.getElementById("textStart");

      setView(daysLetterEl, textLetterEl, daysUntil(TARGET_LETTER));
      setView(daysStartEl,  textStartEl,  daysUntil(TARGET_START));
    }

    // Run
    try {
      render();
      setInterval(render, 60000);
    } catch (e) {
      document.getElementById("debug").textContent = "JS שגיאה: " + e;
    }

    // Modal
    var modalBack = document.getElementById("modalBack");
    document.getElementById("openLetter").addEventListener("click", function () {
      modalBack.style.display = "flex";
    });
    document.getElementById("closeModal").addEventListener("click", function () {
      modalBack.style.display = "none";
    });
    modalBack.addEventListener("click", function (e) {
      if (e.target === modalBack) modalBack.style.display = "none";
    });
  </script>
</body>
</html>
