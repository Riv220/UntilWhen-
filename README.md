<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Countdown</title>
  <style>
    :root { font-family: Arial, sans-serif; }
    body {
      margin: 0; padding: 24px;
      background: #0b0f14; color: #e8eef6;
    }
    .wrap { max-width: 520px; margin: 0 auto; }
    h1 { margin: 0 0 14px; font-size: 22px; font-weight: 700; }
    .card {
      background: #121a24; border: 1px solid #1e2a3a;
      border-radius: 16px; padding: 16px; margin: 12px 0;
      box-shadow: 0 6px 18px rgba(0,0,0,.25);
    }
    .label { opacity: .85; font-size: 14px; margin-bottom: 10px; }
    .big {
      font-size: 56px; font-weight: 800; line-height: 1;
      letter-spacing: 1px; margin: 6px 0 10px;
    }
    .sub { opacity: .85; font-size: 14px; }
    .row { display: flex; gap: 10px; align-items: center; justify-content: space-between; }
    .pill {
      font-size: 12px; padding: 6px 10px; border-radius: 999px;
      border: 1px solid #2a3b52; background: #0f1620; opacity: .95;
      white-space: nowrap;
    }
    .ok { color: #9ff2c3; }
    .warn { color: #ffd08a; }
    .done { color: #a9b7c6; }
    button {
      width: 100%;
      padding: 12px 14px; border-radius: 12px;
      border: 1px solid #2a3b52; background: #0f1620; color: #e8eef6;
      font-size: 14px; cursor: pointer;
    }
    .modalBack {
      position: fixed; inset: 0; display: none;
      background: rgba(0,0,0,.55); align-items: center; justify-content: center;
      padding: 18px;
    }
    .modal {
      width: 100%; max-width: 520px;
      background: #121a24; border: 1px solid #1e2a3a;
      border-radius: 16px; padding: 16px;
    }
    .modal h2 { margin: 0 0 10px; font-size: 18px; }
    .hint { opacity: .85; font-size: 13px; margin: 0 0 12px; }
    .closeRow { display: flex; gap: 10px; }
  </style>
</head>
<body>
  <div class="wrap">
    <h1>ספירה לאחור</h1>

    <!-- 1) Letter first -->
    <div class="card" id="cardLetter">
      <div class="row">
        <div class="label">מכתב התפטרות</div>
        <div class="pill" id="dateLetterPill">20.02</div>
      </div>
      <div class="big" id="daysLetter">—</div>
      <div class="sub" id="textLetter">טוען…</div>
      <div style="margin-top:12px;">
        <button id="openLetter">הצג פרטי מכתב (ללא נוסח)</button>
      </div>
    </div>

    <!-- 2) Start work second -->
    <div class="card" id="cardStart">
      <div class="row">
        <div class="label">תחילת עבודה רשמית</div>
        <div class="pill" id="dateStartPill">04.07</div>
      </div>
      <div class="big" id="daysStart">—</div>
      <div class="sub" id="textStart">טוען…</div>
    </div>
  </div>

  <!-- Modal (no letter text) -->
  <div class="modalBack" id="modalBack">
    <div class="modal">
      <h2>מכתב התפטרות</h2>
      <p class="hint">
        כאן אין נוסח. רק מקום לשדות/פרטים שתרצה להציג (תאריך, למי נשלח, סטטוס “נשלח/לא נשלח” וכו׳).
      </p>
      <div class="card" style="margin:0; background:#0f1620;">
        <div class="row">
          <div class="label" style="margin:0;">סטטוס</div>
          <div class="pill">לא הוגדר</div>
        </div>
        <div class="sub" style="margin-top:10px;">תאריך יעד: <span id="modalDate">—</span></div>
      </div>
      <div class="closeRow" style="margin-top:12px;">
        <button id="closeModal">סגור</button>
      </div>
    </div>
  </div>

  <script>
    // --- Helpers (English code only) ---
    const TZ = "Asia/Jerusalem";

    function formatDM(date) {
      const d = String(date.getDate()).padStart(2, "0");
      const m = String(date.getMonth() + 1).padStart(2, "0");
      return `${d}.${m}`;
    }

    function makeTargetDate(day, month) {
      // month: 1-12
      const now = new Date();
      const year = now.getFullYear();

      // Create date at local midnight (user device)
      let target = new Date(year, month - 1, day, 0, 0, 0, 0);

      // If already passed, move to next year
      const todayMid = new Date(now.getFullYear(), now.getMonth(), now.getDate(), 0,0,0,0);
      if (target < todayMid) target = new Date(year + 1, month - 1, day, 0, 0, 0, 0);

      return target;
    }

    function daysDiffFromToday(target) {
      const now = new Date();
      const today = new Date(now.getFullYear(), now.getMonth(), now.getDate(), 0,0,0,0);
      const diffMs = target - today;
      return Math.round(diffMs / (1000 * 60 * 60 * 24));
    }

    function setCardState(cardEl, daysEl, textEl, days) {
      // Color by status
      cardEl.classList.remove("ok", "warn", "done");
      if (days > 7) {
        daysEl.classList.add("warn");
      } else if (days > 0) {
        daysEl.classList.add("ok");
      } else {
        daysEl.classList.add("done");
      }

      if (days > 1) textEl.textContent = `נותרו ${days} ימים`;
      else if (days === 1) textEl.textContent = `נותר יום אחד`;
      else if (days === 0) textEl.textContent = `היום זה היום`;
      else textEl.textContent = `התאריך עבר`;
    }

    // --- Targets (LETTER first, START second) ---
    const targetLetter = makeTargetDate(20, 2); // 20.02
    const targetStart  = makeTargetDate(7, 4);    // 04.07

    // Pills show the actual target date (with year)
    document.getElementById("dateLetterPill").textContent = `${formatDM(targetLetter)}.${targetLetter.getFullYear()}`;
    document.getElementById("dateStartPill").textContent  = `${formatDM(targetStart)}.${targetStart.getFullYear()}`;
    document.getElementById("modalDate").textContent      = `${formatDM(targetLetter)}.${targetLetter.getFullYear()}`;

    function render() {
      const dLetter = daysDiffFromToday(targetLetter);
      const dStart  = daysDiffFromToday(targetStart);

      document.getElementById("daysLetter").textContent = dLetter >= 0 ? dLetter : 0;
      document.getElementById("daysStart").textContent  = dStart  >= 0 ? dStart  : 0;

      setCardState(
        document.getElementById("cardLetter"),
        document.getElementById("daysLetter"),
        document.getElementById("textLetter"),
        dLetter
      );

      setCardState(
        document.getElementById("cardStart"),
        document.getElementById("daysStart"),
        document.getElementById("textStart"),
        dStart
      );
    }

    render();

    // Refresh every minute (safe)
    setInterval(render, 60 * 1000);

    // Modal handlers
    const modalBack = document.getElementById("modalBack");
    document.getElementById("openLetter").addEventListener("click", () => {
      modalBack.style.display = "flex";
    });
    document.getElementById("closeModal").addEventListener("click", () => {
      modalBack.style.display = "none";
    });
    modalBack.addEventListener("click", (e) => {
      if (e.target === modalBack) modalBack.style.display = "none";
    });
  </script>
</body>
</html>

