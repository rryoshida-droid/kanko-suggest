# kanko-suggest
<!doctype html>
<html lang="ja">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Pomodoro Timer + History</title>
  <style>
    :root{
      --bg:#0b0f17;
      --card:#111827;
      --text:#e5e7eb;
      --muted:#9ca3af;
      --border:rgba(255,255,255,.10);
      --shadow:0 14px 34px rgba(0,0,0,.38);
      --radius:18px;
      --radius2:14px;
      --accent:#60a5fa;
      --ok:#34d399;
      --warn:#fbbf24;
      --danger:#f87171;
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      font-family: ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Hiragino Kaku Gothic ProN", "Noto Sans JP", "Yu Gothic", "Meiryo", sans-serif;
      background:
        radial-gradient(1100px 600px at 20% -10%, rgba(96,165,250,.26), transparent 55%),
        radial-gradient(900px 500px at 90% 0%, rgba(52,211,153,.16), transparent 55%),
        var(--bg);
      color:var(--text);
    }
    .wrap{max-width:1100px;margin:0 auto;padding:24px 16px 44px}
    header{
      display:flex;align-items:flex-start;justify-content:space-between;gap:14px;margin-bottom:16px
    }
    h1{margin:0;font-size:22px;letter-spacing:.2px}
    .sub{margin:6px 0 0;color:var(--muted);font-size:13px;line-height:1.5}
    .pill{
      display:inline-flex;align-items:center;gap:8px;
      padding:10px 12px;border:1px solid var(--border);
      background:rgba(255,255,255,.04);
      border-radius:999px;box-shadow:var(--shadow);
      font-size:12px;color:var(--muted);white-space:nowrap
    }
    .grid{display:grid;grid-template-columns: 1.05fr .95fr;gap:14px}
    @media (max-width: 900px){.grid{grid-template-columns:1fr} header{flex-direction:column}}
    .card{
      background: linear-gradient(180deg, rgba(255,255,255,.05), rgba(255,255,255,.02));
      border:1px solid var(--border);
      border-radius:var(--radius);
      box-shadow:var(--shadow);
      padding:16px;
    }
    .card h2{margin:0 0 10px;font-size:15px;color:#f3f4f6}
    .row{display:flex;gap:10px;flex-wrap:wrap;align-items:center}
    input, select{
      background:rgba(255,255,255,.04);
      border:1px solid var(--border);
      color:var(--text);
      border-radius:12px;
      padding:10px 12px;
      outline:none;
      font-size:14px;
    }
    input::placeholder{color:rgba(229,231,235,.45)}
    button{
      border:1px solid var(--border);
      background:rgba(255,255,255,.06);
      color:var(--text);
      border-radius:12px;
      padding:10px 12px;
      cursor:pointer;
      font-size:14px;
      transition:transform .05s ease, background .15s ease, border-color .15s ease;
    }
    button:hover{background:rgba(255,255,255,.09);border-color:rgba(255,255,255,.18)}
    button:active{transform:translateY(1px)}
    .btn-primary{background:rgba(96,165,250,.20);border-color:rgba(96,165,250,.40)}
    .btn-primary:hover{background:rgba(96,165,250,.26)}
    .btn-ok{background:rgba(52,211,153,.14);border-color:rgba(52,211,153,.40)}
    .btn-danger{background:rgba(248,113,113,.16);border-color:rgba(248,113,113,.38)}
    .muted{color:var(--muted);font-size:12px}
    .timerBox{
      display:grid;grid-template-columns: 1fr;
      gap:12px;margin-top:10px
    }
    .bigTime{
      font-size:56px;font-weight:800;letter-spacing:1px;
      line-height:1.0;margin:2px 0 0;
    }
    .phase{
      display:flex;align-items:center;gap:10px;flex-wrap:wrap
    }
    .tag{
      font-size:11px;padding:4px 8px;border-radius:999px;
      border:1px solid var(--border);background:rgba(255,255,255,.04);color:var(--muted)
    }
    .tag.accent{border-color:rgba(96,165,250,.40);color:#cfe6ff}
    .tag.ok{border-color:rgba(52,211,153,.40);color:#c9ffe9}
    .tag.warn{border-color:rgba(251,191,36,.40);color:#ffe9b1}
    .progress{
      width:100%;height:12px;border-radius:999px;
      border:1px solid var(--border);
      background:rgba(255,255,255,.04);
      overflow:hidden;
    }
    .bar{height:100%;width:0%;background:rgba(96,165,250,.60)}
    .kpis{
      display:grid;grid-template-columns:repeat(3,1fr);gap:10px;margin-top:12px
    }
    @media(max-width:540px){.kpis{grid-template-columns:1fr}}
    .kpi{
      background:rgba(15,23,42,.55);
      border:1px solid var(--border);
      border-radius:var(--radius2);
      padding:12px;
    }
    .kpi .num{font-size:22px;font-weight:800;margin:2px 0 2px}
    .kpi .lab{color:var(--muted);font-size:12px}
    .list{display:flex;flex-direction:column;gap:10px;margin-top:10px}
    .item{
      display:flex;gap:10px;align-items:flex-start;
      padding:12px;background:rgba(17,24,39,.55);
      border:1px solid var(--border);
      border-radius:var(--radius2);
    }
    .item .main{flex:1}
    .item .top{display:flex;align-items:center;gap:8px;flex-wrap:wrap}
    .item .ttl{margin:0;font-weight:700;font-size:14px}
    .item .desc{margin:6px 0 0;color:var(--muted);font-size:12px;line-height:1.5}
    .actions{display:flex;gap:8px;flex-wrap:wrap;justify-content:flex-end}
    canvas{
      width:100%;height:160px;
      background:rgba(15,23,42,.45);
      border:1px solid var(--border);
      border-radius:var(--radius2);
    }
    .toast{
      position:fixed;bottom:16px;left:50%;
      transform:translateX(-50%);
      background:rgba(17,24,39,.92);
      border:1px solid var(--border);
      border-radius:999px;
      padding:10px 14px;
      color:var(--text);
      font-size:13px;
      box-shadow:var(--shadow);
      opacity:0;pointer-events:none;
      transition:opacity .18s ease, transform .18s ease;
    }
    .toast.show{opacity:1;transform:translateX(-50%) translateY(-2px)}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <div>
        <h1>Pomodoro Timer + History</h1>
        <p class="sub">集中→休憩を回し、履歴と統計を自動保存（localStorage）／GitHub Pagesで公開OK</p>
      </div>
      <div class="pill" id="clockPill">🕘 —</div>
    </header>

    <div class="grid">
      <!-- Left: Timer -->
      <section class="card">
        <h2>タイマー</h2>

        <div class="row">
          <label class="muted">集中</label>
          <input id="workMin" type="number" min="1" max="90" value="25" style="width:90px" />
          <label class="muted">短休憩</label>
          <input id="breakMin" type="number" min="1" max="30" value="5" style="width:90px" />
          <label class="muted">長休憩</label>
          <input id="longBreakMin" type="number" min="1" max="60" value="15" style="width:90px" />
          <label class="muted">何回ごとに長休憩</label>
          <input id="longEvery" type="number" min="2" max="8" value="4" style="width:90px" />
        </div>

        <div class="row" style="margin-top:10px">
          <input id="taskTag" type="text" placeholder="タグ（例：憲法、宅建、ES）" style="flex:1; min-width:220px" />
          <select id="mode">
            <option value="work">集中</option>
            <option value="break">短休憩</option>
            <option value="longBreak">長休憩</option>
          </select>
          <button id="applyBtn">適用</button>
        </div>

        <div class="timerBox">
          <div class="phase">
            <span class="tag accent" id="phaseTag">集中</span>
            <span class="tag" id="cycleTag">サイクル：0</span>
            <span class="tag warn" id="statusTag">停止中</span>
          </div>

          <div class="bigTime" id="timeText">25:00</div>

          <div class="progress" aria-label="progress">
            <div class="bar" id="bar"></div>
          </div>

          <div class="row" style="justify-content:space-between">
            <div class="row">
              <button class="btn-primary" id="startBtn">開始</button>
              <button id="pauseBtn">一時停止</button>
              <button id="skipBtn">スキップ</button>
              <button id="resetBtn">リセット</button>
            </div>
            <div class="row">
              <button class="btn-ok" id="saveManualBtn" title="いまのフェーズを履歴に保存（途中でも可）">履歴に保存</button>
            </div>
          </div>

          <div class="muted">
            ※ 終了時に軽い通知音が鳴ります（ブラウザ設定により無音の場合あり）
          </div>
        </div>

        <div class="kpis">
          <div class="kpi">
            <div class="lab">今日の集中（分）</div>
            <div class="num" id="kpiToday">—</div>
            <div class="muted" id="kpiTodaySub">セッション：—</div>
          </div>
          <div class="kpi">
            <div class="lab">連続日数</div>
            <div class="num" id="kpiStreak">—</div>
            <div class="muted">（1分以上でカウント）</div>
          </div>
          <div class="kpi">
            <div class="lab">総集中（時間）</div>
            <div class="num" id="kpiTotal">—</div>
            <div class="muted">すべての履歴</div>
          </div>
        </div>
      </section>

      <!-- Right: History -->
      <aside class="card">
        <h2>履歴</h2>

        <div class="row" style="justify-content:space-between">
          <div class="row">
            <input id="search" type="text" placeholder="検索（タグ/メモ）" style="min-width:220px" />
            <select id="filterPhase">
              <option value="all">すべて</option>
              <option value="work">集中のみ</option>
              <option value="break">短休憩のみ</option>
              <option value="longBreak">長休憩のみ</option>
            </select>
          </div>
          <div class="row">
            <button id="exportBtn">CSV出力</button>
            <button class="btn-danger" id="clearBtn">全削除</button>
          </div>
        </div>

        <div style="margin-top:12px">
          <canvas id="chart" width="900" height="320"></canvas>
          <div class="muted" style="margin-top:6px">最近14日：日別の集中分（workのみ）</div>
        </div>

        <div class="list" id="list"></div>
      </aside>
    </div>
  </div>

  <div class="toast" id="toast">保存しました</div>

<script>
(() => {
  const $ = (s) => document.querySelector(s);
  const pad2 = (n) => String(n).padStart(2,"0");
  const yyyyMmDd = (d=new Date()) => {
    const y=d.getFullYear();
    const m=pad2(d.getMonth()+1);
    const day=pad2(d.getDate());
    return `${y}-${m}-${day}`;
  };
  const nowStr = () => new Date().toLocaleString("ja-JP",{dateStyle:"medium", timeStyle:"short"});
  const toast = (msg) => {
    const el = $("#toast");
    el.textContent = msg;
    el.classList.add("show");
    clearTimeout(toast._t);
    toast._t = setTimeout(()=>el.classList.remove("show"), 1200);
  };

  const KEY = "pomodoro_history_v1";
  // data = { history: [{id, tsStart, tsEnd, date, phase, plannedSec, actualSec, tag}] , cyclesCompleted }
  const load = () => {
    try{
      const raw = localStorage.getItem(KEY);
      if(!raw) return { history: [], cyclesCompleted: 0 };
      const data = JSON.parse(raw);
      if(!data || !Array.isArray(data.history)) return { history: [], cyclesCompleted: 0 };
      if(typeof data.cyclesCompleted !== "number") data.cyclesCompleted = 0;
      return data;
    }catch{
      return { history: [], cyclesCompleted: 0 };
    }
  };
  const save = (data) => localStorage.setItem(KEY, JSON.stringify(data));

  let store = load();

  // ===== Timer state =====
  let phase = "work"; // work | break | longBreak
  let status = "stopped"; // running | paused | stopped
  let plannedSec = 25 * 60;
  let remainingSec = plannedSec;
  let startedAt = null;      // timestamp ms when current phase started (running)
  let accumulatedRunSec = 0; // seconds already spent in this phase (for pause/resume)
  let tick = null;

  // settings
  const getSettings = () => ({
    workMin: clampNum($("#workMin").value, 1, 90, 25),
    breakMin: clampNum($("#breakMin").value, 1, 30, 5),
    longBreakMin: clampNum($("#longBreakMin").value, 1, 60, 15),
    longEvery: clampNum($("#longEvery").value, 2, 8, 4),
  });

  function clampNum(v, min, max, def){
    const n = Number(v);
    if(!Number.isFinite(n)) return def;
    return Math.max(min, Math.min(max, Math.floor(n)));
  }

  const phaseLabel = (p) => p==="work" ? "集中" : (p==="break" ? "短休憩" : "長休憩");
  const phaseTagClass = (p) => p==="work" ? "accent" : (p==="break" ? "warn" : "ok");

  function setPhase(p){
    phase = p;
    const s = getSettings();
    plannedSec = (p==="work" ? s.workMin : (p==="break" ? s.breakMin : s.longBreakMin)) * 60;
    remainingSec = plannedSec;
    startedAt = null;
    accumulatedRunSec = 0;
    status = "stopped";
    syncUI();
  }

  function fmt(sec){
    sec = Math.max(0, Math.floor(sec));
    const m = Math.floor(sec/60);
    const s = sec%60;
    return `${pad2(m)}:${pad2(s)}`;
  }

  function playBeep(){
    // simple WebAudio beep (user gesture needed in many browsers; start button counts)
    try{
      const ctx = new (window.AudioContext || window.webkitAudioContext)();
      const o = ctx.createOscillator();
      const g = ctx.createGain();
      o.type = "sine";
      o.frequency.value = 880;
      o.connect(g); g.connect(ctx.destination);
      g.gain.setValueAtTime(0.0001, ctx.currentTime);
      g.gain.exponentialRampToValueAtTime(0.12, ctx.currentTime + 0.02);
      g.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + 0.25);
      o.start();
      o.stop(ctx.currentTime + 0.26);
      setTimeout(()=>ctx.close(), 350);
    }catch{}
  }

  function start(){
    if(status==="running") return;
    if(status==="stopped"){
      startedAt = Date.now();
      accumulatedRunSec = 0;
      remainingSec = plannedSec;
    }else if(status==="paused"){
      // resume
      startedAt = Date.now();
    }
    status = "running";
    tick = setInterval(onTick, 250);
    syncUI();
  }

  function pause(){
    if(status!=="running") return;
    // add elapsed since startedAt into accumulatedRunSec
    const elapsed = (Date.now() - startedAt)/1000;
    accumulatedRunSec += elapsed;
    startedAt = null;
    status = "paused";
    clearInterval(tick); tick = null;
    syncUI();
  }

  function reset(){
    clearInterval(tick); tick = null;
    status = "stopped";
    startedAt = null;
    accumulatedRunSec = 0;
    remainingSec = plannedSec;
    syncUI();
  }

  function onTick(){
    if(status!=="running") return;
    const elapsed = (Date.now() - startedAt)/1000;
    const spent = accumulatedRunSec + elapsed;
    remainingSec = plannedSec - spent;
    if(remainingSec <= 0){
      finishPhase(true);
    }
    syncUI(false);
  }

  function finishPhase(auto){
    // finalize spent seconds
    let actual = plannedSec;
    if(status==="running"){
      const elapsed = (Date.now() - startedAt)/1000;
      actual = Math.max(0, Math.round(accumulatedRunSec + elapsed));
    }else if(status==="paused"){
      actual = Math.max(0, Math.round(accumulatedRunSec));
    }else{
      actual = 0;
    }

    // save history only if actual >= 1 sec (and allow manual save for smaller)
    const tag = $("#taskTag").value.trim();
    const tsEnd = Date.now();
    const tsStart = tsEnd - actual*1000;

    // store history
    store.history.unshift({
      id: Math.random().toString(36).slice(2,10),
      tsStart, tsEnd,
      date: yyyyMmDd(new Date(tsEnd)),
      phase,
      plannedSec,
      actualSec: actual,
      tag: tag || ""
    });

    // cycles count: count completed work sessions only
    if(phase==="work"){
      store.cyclesCompleted += 1;
    }
    save(store);

    playBeep();
    toast(auto ? "終了！履歴に保存しました" : "履歴に保存しました");

    // move to next phase
    clearInterval(tick); tick = null;
    status = "stopped";
    startedAt = null;
    accumulatedRunSec = 0;

    const s = getSettings();
    if(phase==="work"){
      // next break: longBreak every N work cycles
      const isLong = (store.cyclesCompleted % s.longEvery === 0);
      setPhase(isLong ? "longBreak" : "break");
    }else{
      setPhase("work");
    }

    renderAll();
  }

  function manualSave(){
    // save whatever time spent so far (or planned if stopped -> 0)
    if(status==="stopped"){
      toast("停止中：まず開始するか、スキップで区切ってね");
      return;
    }
    finishPhase(false);
  }

  function skip(){
    // skip without saving (but keeps cycle logic? we won't increment cycles)
    clearInterval(tick); tick=null;
    status="stopped";
    startedAt=null;
    accumulatedRunSec=0;
    remainingSec=plannedSec;

    const s = getSettings();
    if(phase==="work"){
      // skipping work -> go to break (no cycle increment)
      setPhase("break");
    }else{
      setPhase("work");
    }
    toast("スキップしました");
    renderAll();
  }

  // ===== UI render =====
  function syncClock(){
    $("#clockPill").textContent = `🕘 ${nowStr()}`;
  }

  function syncUI(full=true){
    $("#phaseTag").textContent = phaseLabel(phase);
    $("#phaseTag").className = "tag " + phaseTagClass(phase);
    $("#cycleTag").textContent = `サイクル：${store.cyclesCompleted}`;
    $("#statusTag").textContent = status==="running" ? "稼働中" : (status==="paused" ? "一時停止" : "停止中");
    $("#statusTag").className = "tag " + (status==="running" ? "ok" : (status==="paused" ? "warn" : "tag"));

    $("#timeText").textContent = fmt(remainingSec);

    // progress
    const spent = Math.max(0, plannedSec - remainingSec);
    const pct = plannedSec ? Math.min(100, Math.max(0, (spent / plannedSec) * 100)) : 0;
    $("#bar").style.width = pct.toFixed(1) + "%";

    // document title shows time while running
    document.title = (status==="running" ? `${fmt(remainingSec)} • ${phaseLabel(phase)}` : "Pomodoro Timer + History");
    if(full) {
      calcKpis();
    }
  }

  function calcKpis(){
    const today = yyyyMmDd(new Date());
    const workHist = store.history.filter(h => h.phase==="work");
    const todayWork = workHist.filter(h => h.date===today);
    const todayMin = Math.round(todayWork.reduce((s,h)=>s + (h.actualSec||0), 0)/60);
    const todaySessions = todayWork.length;

    const totalWorkMin = Math.round(workHist.reduce((s,h)=>s + (h.actualSec||0), 0)/60);
    const totalWorkHour = (totalWorkMin/60).toFixed(1);

    $("#kpiToday").textContent = String(todayMin);
    $("#kpiTodaySub").textContent = `セッション：${todaySessions}`;
    $("#kpiTotal").textContent = totalWorkHour;

    // streak: consecutive days with >= 1 min work
    const daySet = new Set();
    for(const h of workHist){
      const min = (h.actualSec||0)/60;
      if(min >= 1) daySet.add(h.date);
    }
    let streak = 0;
    let d = new Date();
    for(let i=0;i<365;i++){
      const ds = yyyyMmDd(d);
      if(daySet.has(ds)) streak++;
      else break;
      d.setDate(d.getDate()-1);
    }
    $("#kpiStreak").textContent = String(streak);
  }

  function renderHistory(){
    const q = $("#search").value.trim().toLowerCase();
    const f = $("#filterPhase").value;

    const list = $("#list");
    list.innerHTML = "";

    const items = store.history.filter(h => {
      if(f!=="all" && h.phase!==f) return false;
      if(!q) return true;
      const t = (h.tag||"").toLowerCase();
      return t.includes(q) || (phaseLabel(h.phase)).includes(q);
    });

    if(items.length===0){
      const empty = document.createElement("div");
      empty.className="muted";
      empty.style.padding="10px 2px";
      empty.textContent="履歴がまだありません。開始して1セット回してみよう。";
      list.appendChild(empty);
      return;
    }

    for(const h of items){
      const el = document.createElement("div");
      el.className="item";
      const main = document.createElement("div");
      main.className="main";

      const top = document.createElement("div");
      top.className="top";

      const ttl = document.createElement("p");
      ttl.className="ttl";
      ttl.textContent = `${phaseLabel(h.phase)}  ${Math.round((h.actualSec||0)/60)}分`;

      const tag1 = document.createElement("span");
      tag1.className="tag " + phaseTagClass(h.phase);
      tag1.textContent=phaseLabel(h.phase);

      const tag2 = document.createElement("span");
      tag2.className="tag";
      tag2.textContent=h.date;

      const tag3 = document.createElement("span");
      tag3.className="tag";
      tag3.textContent = h.tag ? `#${h.tag}` : "（タグなし）";

      top.appendChild(ttl);
      top.appendChild(tag1);
      top.appendChild(tag2);
      top.appendChild(tag3);

      const desc = document.createElement("p");
      desc.className="desc";
      const s = new Date(h.tsStart).toLocaleString("ja-JP",{dateStyle:"short", timeStyle:"short"});
      const e = new Date(h.tsEnd).toLocaleString("ja-JP",{dateStyle:"short", timeStyle:"short"});
      const plannedMin = Math.round((h.plannedSec||0)/60);
      desc.textContent = `開始 ${s} → 終了 ${e} ／ 予定 ${plannedMin}分`;

      main.appendChild(top);
      main.appendChild(desc);

      const acts = document.createElement("div");
      acts.className="actions";

      const del = document.createElement("button");
      del.className="btn-danger";
      del.textContent="削除";
      del.onclick=() => {
        store.history = store.history.filter(x=>x.id!==h.id);
        save(store);
        toast("削除しました");
        renderAll();
      };

      acts.appendChild(del);

      el.appendChild(main);
      el.appendChild(acts);
      list.appendChild(el);
    }
  }

  function drawChart(){
    const canvas = $("#chart");
    const ctx = canvas.getContext("2d");
    const w=canvas.width, h=canvas.height;
    ctx.clearRect(0,0,w,h);

    // last 14 days (work minutes)
    const days = [];
    const now = new Date();
    for(let i=13;i>=0;i--){
      const d = new Date(now);
      d.setDate(d.getDate()-i);
      const ds = yyyyMmDd(d);
      const min = Math.round(store.history
        .filter(x => x.phase==="work" && x.date===ds)
        .reduce((s,x)=>s + (x.actualSec||0), 0)/60);
      days.push({ds, min});
    }
    const max = Math.max(10, ...days.map(x=>x.min));

    // grid
    ctx.strokeStyle = "rgba(255,255,255,.08)";
    ctx.lineWidth = 1;
    for(let i=0;i<=4;i++){
      const y = 18 + (h-56)*(i/4);
      ctx.beginPath();
      ctx.moveTo(16,y); ctx.lineTo(w-16,y);
      ctx.stroke();
    }
    ctx.fillStyle = "rgba(229,231,235,.65)";
    ctx.font = "12px ui-sans-serif, system-ui";
    ctx.fillText("集中分（work）", 16, 14);

    const left=16,right=w-16,top=24,bottom=h-20;
    const bw=(right-left)/days.length;

    for(let i=0;i<days.length;i++){
      const x = left + i*bw + 6;
      const barW = Math.max(6, bw-12);
      const barH = (bottom-top) * (days[i].min / max);
      const y = bottom - barH;

      ctx.fillStyle = "rgba(96,165,250,.55)";
      ctx.fillRect(x,y,barW,barH);

      // label sparse
      if(i%3===0 || i===days.length-1){
        const mmdd = days[i].ds.slice(5).replace("-","/");
        ctx.fillStyle = "rgba(229,231,235,.55)";
        ctx.font = "11px ui-sans-serif, system-ui";
        ctx.fillText(mmdd, x, h-6);
      }
    }
  }

  function exportCSV(){
    const rows = [
      ["date","phase","planned_minutes","actual_minutes","tag","start","end"].join(",")
    ];
    for(const h of store.history){
      const start = new Date(h.tsStart).toISOString();
      const end = new Date(h.tsEnd).toISOString();
      const planned = Math.round((h.plannedSec||0)/60);
      const actual = Math.round((h.actualSec||0)/60);
      const tag = (h.tag||"").replaceAll('"','""');
      rows.push([
        h.date,
        h.phase,
        planned,
        actual,
        `"${tag}"`,
        start,
        end
      ].join(","));
    }
    const blob = new Blob([rows.join("\n")], {type:"text/csv;charset=utf-8"});
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href=url;
    a.download=`pomodoro-history-${yyyyMmDd(new Date())}.csv`;
    a.click();
    URL.revokeObjectURL(url);
    toast("CSV出力しました");
  }

  function clearAll(){
    store = { history: [], cyclesCompleted: 0 };
    save(store);
    toast("全削除しました");
    renderAll();
  }

  function applySettings(){
    // apply selected mode and reset timer to its planned duration
    const p = $("#mode").value;
    setPhase(p);
    toast("適用しました");
    renderAll();
  }

  function renderAll(){
    syncUI();
    renderHistory();
    drawChart();
  }

  // ===== wire events =====
  $("#applyBtn").addEventListener("click", applySettings);
  $("#startBtn").addEventListener("click", start);
  $("#pauseBtn").addEventListener("click", () => status==="running" ? pause() : start());
  $("#resetBtn").addEventListener("click", reset);
  $("#skipBtn").addEventListener("click", skip);
  $("#saveManualBtn").addEventListener("click", manualSave);

  $("#search").addEventListener("input", renderHistory);
  $("#filterPhase").addEventListener("change", renderHistory);
  $("#exportBtn").addEventListener("click", exportCSV);
  $("#clearBtn").addEventListener("click", () => {
    if(confirm("履歴を全削除します。よろしいですか？")) clearAll();
  });

  // init
  setPhase("work");
  renderAll();
  syncClock();
  setInterval(syncClock, 1000*30);

})();
</script>
</body>
</html>
