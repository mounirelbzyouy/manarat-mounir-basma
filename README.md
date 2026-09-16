<!doctype html>
<html lang="ar" dir="rtl">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<meta name="theme-color" content="#176b4d">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="منارة منير للبصمة">
<title>منارة منير للبصمة - نظام التنبيه الذكي</title>
<!-- PWA Manifest Data -->
<link rel="manifest" href='data:application/manifest+json,{"name":"منارة منير للبصمة","short_name":"البصمة","start_url":".","display":"standalone","background_color":"#f4f7f5","theme_color":"#176b4d"}'>
<style>
:root{
  --primary: #176b4d;
  --primary-dark: #104e38;
  --primary-light: #dceee6;
  --bg: #f4f7f5;
  --card-bg: #ffffff;
  --text: #17352b;
  --text-muted: #6b7b74;
  --border: #edf0ee;
  --shadow: 0 4px 18px rgba(0,0,0,0.06);
}
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
body{margin:0;background:var(--bg);color:var(--text);font-family:-apple-system,BlinkMacSystemFont,"Segoe UI","SF Arabic",Roboto,Arial,sans-serif;line-height:1.5}
main{max-width:560px;margin:auto;padding:20px 16px 50px}
header{text-align:center;margin-bottom:20px}
h1{margin:5px 0;font-size:26px;color:var(--primary-dark);display:flex;align-items:center;justify-content:center;gap:8px}
.sub{color:var(--text-muted);font-size:14px}
.card{background:var(--card-bg);border-radius:20px;padding:20px;margin:14px 0;box-shadow:var(--shadow);border:1px solid rgba(0,0,0,0.03)}
.next-card{background:linear-gradient(135deg, #176b4d, #0e4b35);color:#fff;position:relative;overflow:hidden}
.next-card .hint{color:rgba(255,255,255,0.85)}
.next-title{font-size:14px;opacity:0.9;margin-bottom:4px}
.next-time{font-size:22px;font-weight:900;margin:6px 0}
.countdown{font-size:16px;background:rgba(255,255,255,0.2);display:inline-block;padding:6px 14px;border-radius:20px;margin-top:6px;font-weight:bold;letter-spacing:1px}
.progress-bar-bg{background:rgba(0,0,0,0.08);border-radius:10px;height:10px;overflow:hidden;margin:12px 0 6px}
.progress-bar-fill{background:var(--primary);height:100%;width:0%;transition:width 0.4s ease}
.stats-text{font-size:14px;color:var(--text-muted);display:flex;justify-content:space-between;font-weight:700}
.row{display:flex;align-items:center;justify-content:space-between;gap:12px;padding:14px 0;border-bottom:1px solid var(--border)}
.row:last-child{border-bottom:0}
.name{font-size:18px;font-weight:800;color:var(--text)}
.time{font-size:13px;color:var(--text-muted);margin-top:3px}
button{border:0;border-radius:12px;padding:11px 16px;font-size:15px;font-weight:800;cursor:pointer;transition:all 0.2s;display:inline-flex;align-items:center;justify-content:center;gap:6px}
button:active{transform:scale(0.97)}
.btn-main{background:var(--primary);color:#fff;width:100%}
.btn-main:hover{background:var(--primary-dark)}
.btn-done{background:var(--primary-light);color:var(--primary)}
.btn-done:hover{background:#cbe5da}
.btn-sec{background:#eef3f0;color:var(--text);margin-top:8px;width:100%}
.btn-gps{background:#e3f2fd;color:#1565c0;width:100%;margin-bottom:10px}
label{display:block;font-weight:700;font-size:14px;margin-top:10px;color:var(--text)}
input{width:100%;padding:12px;border:1px solid #d9e2dd;border-radius:12px;font-size:15px;margin-top:6px;background:#fff;color:var(--text)}
.hint{font-size:13px;color:var(--text-muted);line-height:1.6}

/* Modal Popup */
.modal-overlay{position:fixed;top:0;left:0;right:0;bottom:0;background:rgba(0,0,0,0.6);display:none;align-items:center;justify-content:center;z-index:999;padding:20px}
.modal-card{background:#fff;border-radius:24px;padding:24px;width:100%;max-width:400px;text-align:center;box-shadow:0 10px 30px rgba(0,0,0,0.3)}
.modal-card h2{margin-top:0;color:var(--primary)}
</style>
</head>
<body>
<main>
  <header>
    <h1>🕌 منارة منير للبصمة</h1>
    <div class="sub">نظام ذكي للتذكير ببصمة العمل 5 مرات يومياً حسب أوقات الصلاة</div>
  </header>

  <!-- بطاقة التقدم اليومي -->
  <div class="card">
    <div class="stats-text">
      <span>إنجاز البصمات اليومية</span>
      <span id="progress-text">0 من 5</span>
    </div>
    <div class="progress-bar-bg">
      <div class="progress-bar-fill" id="progress-fill"></div>
    </div>
  </div>

  <!-- بطاقة التنبيه القادم -->
  <div class="card next-card">
    <div class="next-title">التنبيه القادم لبصمة الدخول/الخروج</div>
    <div class="next-time" id="next-event">جاري تحميل الأوقات…</div>
    <div class="countdown" id="countdown">--:--:--</div>
    <div class="hint" style="margin-top:10px;">⚡ يتم التذكير تلقائياً قبل الأذان بـ 15 دقيقة وبعده بـ 15 دقيقة.</div>
  </div>

  <!-- قائمة الصلوات والبصمات -->
  <div class="card" id="prayers-list">
    <div style="text-align:center;color:var(--text-muted);padding:20px">جاري تحميل أوقات الصلاة...</div>
  </div>

  <!-- الإعدادات والموقع الجغرافي -->
  <div class="card">
    <h3 style="margin-top:0">⚙️ إعدادات الموقع والتحديث</h3>
    <button class="btn-gps" onclick="getLocationGPS()">📍 استخدام موقعي الحالي (GPS)</button>
    <label for="city">المدينة</label>
    <input id="city" value="Ajman" placeholder="مثال: Ajman, Fujairah, Dubai">
    <label for="country">الدولة</label>
    <input id="country" value="United Arab Emirates" placeholder="مثال: United Arab Emirates">
    <button class="btn-sec" onclick="loadTimes()">🔄 تحديث أوقات الصلاة</button>
  </div>

  <!-- اختبار التنبيه والإشعارات -->
  <div class="card">
    <h3 style="margin-top:0">🔔 التنبيهات والصوت</h3>
    <button class="btn-main" onclick="notifyTest()">🔊 اختبار التنبيه الصوتي والإشعارات</button>
    <div class="hint" style="margin-top:12px">
      📌 <strong>تلميح لمستخدمي الآيفون:</strong> قم بإضافة التطبيق للصفحة الرئيسية (Add to Home Screen) للحصول على أفضل تجربة وتنبيهات مستمرة.
    </div>
  </div>
</main>

<!-- نافذة تنبيه منبثقة مع صوت عند الموعد -->
<div class="modal-overlay" id="alarm-modal">
  <div class="modal-card">
    <div style="font-size:50px;margin-bottom:10px">⏰</div>
    <h2>حان موعد تسجيل البصمة!</h2>
    <p id="modal-desc" style="color:var(--text-muted);margin-bottom:20px">تذكير بموعد البصمة</p>
    <button class="btn-main" onclick="dismissAlarm()">تم التسجيل / إغلاق</button>
  </div>
</div>

<script>
const PRAYERS = [
  ["الفجر", "Fajr"],
  ["الظهر", "Dhuhr"],
  ["العصر", "Asr"],
  ["المغرب", "Maghrib"],
  ["العشاء", "Isha"]
];

let times = {};
let alertHistory = {};

function getTodayKey() {
  const d = new Date();
  return `mounir-done-${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`;
}

function parseMinutes(tStr) {
  if (!tStr) return 0;
  const clean = tStr.split(" ")[0];
  const [h, m] = clean.split(":").map(Number);
  return h * 60 + m;
}

function formatClock(m) {
  m = (m + 1440) % 1440;
  const h = Math.floor(m / 60);
  const min = m % 60;
  const ampm = h >= 12 ? 'م' : 'ص';
  const h12 = h % 12 || 12;
  return `${h12}:${String(min).padStart(2, '0')} ${ampm}`;
}

// توليد صوت تنبيه نقي بدون ملفات خارجية
function playChime() {
  try {
    const ctx = new (window.AudioContext || window.webkitAudioContext)();
    const osc = ctx.createOscillator();
    const gain = ctx.createGain();
    osc.type = 'sine';
    osc.frequency.setValueAtTime(587.33, ctx.currentTime);
    osc.frequency.exponentialRampToValueAtTime(880, ctx.currentTime + 0.3);
    gain.gain.setValueAtTime(0.3, ctx.currentTime);
    gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.8);
    osc.connect(gain);
    gain.connect(ctx.destination);
    osc.start();
    osc.stop(ctx.currentTime + 0.8);
  } catch(e) {}
}

function triggerAlarm(msg) {
  playChime();
  if ("vibrate" in navigator) navigator.vibrate([300, 100, 300, 100, 400]);
  
  if ("Notification" in window && Notification.permission === "granted") {
    new Notification("🕌 منارة منير للبصمة", {
      body: msg,
      icon: "https://fav.farm/🕌"
    });
  }
  
  document.getElementById("modal-desc").textContent = msg;
  document.getElementById("alarm-modal").style.display = "flex";
}

function dismissAlarm() {
  document.getElementById("alarm-modal").style.display = "none";
}

function render() {
  const box = document.getElementById("prayers-list");
  const doneData = JSON.parse(localStorage.getItem(getTodayKey()) || "{}");
  
  box.innerHTML = "";
  const now = new Date();
  const currentMinutes = now.getHours() * 60 + now.getMinutes();
  const currentSeconds = now.getSeconds();
  
  let nextEvent = null;
  let doneCount = 0;

  PRAYERS.forEach(([arName, enKey]) => {
    if (doneData[enKey]) doneCount++;
    if (!times[enKey]) return;

    const baseMin = parseMinutes(times[enKey]);
    const preTime = baseMin - 15;
    const postTime = baseMin + 15;
    
    const events = [
      { at: preTime, label: `قبل أذان ${arName} (15د)` },
      { at: postTime, label: `بعد أذان ${arName} (15د)` }
    ];

    events.forEach(ev => {
      let diff = ev.at - currentMinutes;
      if (diff < 0 || (diff === 0 && currentSeconds > 59)) {
        diff += 1440;
      }
      if (!nextEvent || diff < nextEvent.diff) {
        nextEvent = { diff, at: ev.at, arName, label: ev.label };
      }

      // إطلاق التنبيه لحظة وصول الموعد بالدقيقة والسيرفر
      const alertId = `${getTodayKey()}-${enKey}-${ev.at}`;
      if (currentMinutes === (ev.at + 1440) % 1440 && currentSeconds < 2 && !alertHistory[alertId]) {
        alertHistory[alertId] = true;
        triggerAlarm(`حان الآن موعد: ${ev.label}`);
      }
    });

    const isDone = !!doneData[enKey];
    const row = document.createElement("div");
    row.className = "row";
    row.innerHTML = `
      <div>
        <div class="name">${arName} <span style="font-size:14px;font-weight:normal;color:var(--primary)">(${formatClock(baseMin)})</span></div>
        <div class="time">⏰ التنبيهات: ${formatClock(preTime)} (قبل) · ${formatClock(postTime)} (بعد)</div>
      </div>
      <button class="${isDone ? 'btn-done' : 'btn-main'}" onclick="toggleMark('${enKey}')">
        ${isDone ? '✓ تم البصمة' : 'تأكيد البصمة'}
      </button>
    `;
    box.appendChild(row);
  });

  // تحديث شريط الإنجاز
  const pct = Math.round((doneCount / PRAYERS.length) * 100);
  document.getElementById("progress-text").textContent = `${doneCount} من ${PRAYERS.length}`;
  document.getElementById("progress-fill").style.width = `${pct}%`;

  // تحديث التنبيه القادم والعد التنازلي
  if (nextEvent) {
    document.getElementById("next-event").textContent = `${nextEvent.label} - الساعة ${formatClock(nextEvent.at)}`;
    const totalSecsRemaining = (nextEvent.diff * 60) - currentSeconds;
    const hrs = Math.floor(totalSecsRemaining / 3600);
    const mins = Math.floor((totalSecsRemaining % 3600) / 60);
    const secs = totalSecsRemaining % 60;
    
    document.getElementById("countdown").textContent = 
      `متبقي: ${String(hrs).padStart(2,'0')}:${String(mins).padStart(2,'0')}:${String(secs).padStart(2,'0')}`;
  } else {
    document.getElementById("next-event").textContent = "جاري تحميل أوقات الصلاة…";
    document.getElementById("countdown").textContent = "--:--:--";
  }
}

function toggleMark(id) {
  const doneData = JSON.parse(localStorage.getItem(getTodayKey()) || "{}");
  if (doneData[id]) {
    delete doneData[id];
  } else {
    doneData[id] = true;
  }
  localStorage.setItem(getTodayKey(), JSON.stringify(doneData));
  render();
}

async function loadTimes() {
  const city = document.getElementById("city").value.trim();
  const country = document.getElementById("country").value.trim();
  if (!city || !country) return;

  document.getElementById("next-event").textContent = "جاري تحديث الأوقات…";

  const saved = localStorage.getItem("mounir-times");
  if (saved) {
    try { times = JSON.parse(saved); render(); } catch(e){}
  }

  try {
    const url = `https://api.aladhan.com/v1/timingsByCity?city=${encodeURIComponent(city)}&country=${encodeURIComponent(country)}&method=4`;
    const res = await fetch(url);
    const json = await res.json();
    if (json.data && json.data.timings) {
      times = json.data.timings;
      localStorage.setItem("mounir-times", JSON.stringify(times));
      render();
    }
  } catch (e) {
    if (!Object.keys(times).length) {
      document.getElementById("next-event").textContent = "تعذر تحميل الأوقات. تحقق من الاتصال بالإنترنت.";
    }
  }
}

function getLocationGPS() {
  if (!navigator.geolocation) {
    alert("عذراً، خاصية تحديد الموقع غير مدعومة في متصفحك.");
    return;
  }
  document.getElementById("next-event").textContent = "جاري تحديد موقعك الجغرافي…";
  navigator.geolocation.getCurrentPosition(async (pos) => {
    const lat = pos.coords.latitude;
    const lng = pos.coords.longitude;
    try {
      const url = `https://api.aladhan.com/v1/timings?latitude=${lat}&longitude=${lng}&method=4`;
      const res = await fetch(url);
      const json = await res.json();
      if (json.data && json.data.timings) {
        times = json.data.timings;
        localStorage.setItem("mounir-times", JSON.stringify(times));
        if (json.data.meta && json.data.meta.timezone) {
          document.getElementById("city").value = json.data.meta.timezone.split('/')[1] || "موقعي الحالي";
        }
        render();
      }
    } catch(e) {
      alert("حدث خطأ أثناء جلب أوقات الصلاة بموقعك الحالي.");
    }
  }, () => {
    alert("تعذر الوصول للموقع الجغرافي. يرجى التأكد من السماح بالصلاحيات.");
  });
}

async function notifyTest() {
  playChime();
  if (!("Notification" in window)) {
    alert("الإشعارات النصية غير مدعومة في هذا المتصفح، لكن الصوت يعمل بنجاح!");
    return;
  }
  const perm = await Notification.requestPermission();
  if (perm === "granted") {
    new Notification("🕌 منارة منير للبصمة", {
      body: "🔔 التنبيه الصوتي والإشعارات تعمل بنجاح!",
      icon: "https://fav.farm/🕌"
    });
  } else {
    alert("تم اختبار التنبيه الصوتي بنجاح. يرجى تفعيل الإشعارات من إعدادات المتصفح/الهاتف للتنبيهات النصية.");
  }
}

// تسجيل Service Worker للعمل بدون إنترنت
if ('serviceWorker' in navigator) {
  const swCode = `
    self.addEventListener('install', e => e.waitUntil(self.skipWaiting()));
    self.addEventListener('activate', e => e.waitUntil(self.clients.claim()));
    self.addEventListener('fetch', e => e.respondWith(fetch(e.request).catch(() => caches.match(e.request))));
  `;
  const blob = new Blob([swCode], {type: 'application/javascript'});
  navigator.serviceWorker.register(URL.createObjectURL(blob)).catch(()=>{});
}

loadTimes();
setInterval(render, 1000); // تحديث العد التنازلي كل ثانية
</script>
</body>
</html>
