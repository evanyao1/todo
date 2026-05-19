<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Cat's Todo Ledger 🐾</title>
<link href="https://fonts.googleapis.com/css2?family=Noto+Serif+SC:wght@400;600&family=Noto+Sans+SC:wght@400;500&display=swap" rel="stylesheet">
<style>
:root {
  --bg:#F5F0EA; --surface:#FDFAF6; --surface2:#F0EBE3;
  --border:#E4DDD4; --text:#3D3530; --muted:#9A8E86;
  --accent:#C4A882; --accentS:#EDE0CC;
  --green:#8BAD8B; --greenS:#D6E8D6;
  --red:#C4817A; --redS:#F0D9D7;
}
*{box-sizing:border-box;margin:0;padding:0;}
body{background:var(--bg);font-family:'Noto Sans SC','PingFang SC',sans-serif;color:var(--text);min-height:100vh;}
#app{max-width:430px;margin:0 auto;min-height:100vh;display:flex;flex-direction:column;}

/* header */
#header{position:sticky;top:0;z-index:100;background:var(--surface);border-bottom:1px solid var(--border);padding:13px 18px 11px;display:flex;align-items:center;justify-content:space-between;}
#cat-display{display:flex;align-items:center;gap:10px;cursor:pointer;}
#cat-emoji{font-size:30px;display:inline-block;transition:all .3s;}
#cat-emoji.floating{animation:float 3s ease-in-out infinite;}
#cat-emoji.bounce{animation:bounce .5s ease;}
#cat-label{font-family:'Noto Serif SC',serif;font-size:14px;font-weight:600;color:var(--text);}
#cat-desc{font-size:11px;color:var(--muted);}
#coin-badge{display:flex;align-items:center;gap:5px;background:var(--accentS);border-radius:99px;padding:5px 12px;font-size:13px;color:var(--accent);font-weight:700;cursor:pointer;}

/* content */
#content{flex:1;overflow-y:auto;padding-bottom:76px;}
.page{display:none;padding:20px 16px;}
.page.active{display:block;}
.page-title{font-family:'Noto Serif SC',serif;font-size:17px;font-weight:600;color:var(--text);margin-bottom:16px;display:block;}

/* nav */
#nav{position:fixed;bottom:0;left:50%;transform:translateX(-50%);width:100%;max-width:430px;background:var(--surface);border-top:1px solid var(--border);display:flex;z-index:200;}
.nav-btn{flex:1;padding:9px 0 7px;background:none;border:none;border-top:2.5px solid transparent;cursor:pointer;display:flex;flex-direction:column;align-items:center;gap:2px;transition:border-color .15s;font-family:inherit;}
.nav-btn.active{border-top-color:var(--accent);}
.nav-icon{font-size:19px;}
.nav-label{font-size:10px;color:var(--muted);}
.nav-btn.active .nav-label{color:var(--accent);}

/* buttons */
.btn{border:none;cursor:pointer;font-family:inherit;transition:opacity .15s;border-radius:9px;}
.btn:hover{opacity:.82;}
.pill{display:inline-block;padding:4px 11px;border-radius:99px;font-size:11px;border:1.5px solid var(--border);background:transparent;color:var(--muted);cursor:pointer;transition:all .15s;font-family:inherit;}
.pill.active{border-color:var(--accent);background:var(--accentS);color:var(--accent);}

/* task */
.task-card{display:flex;align-items:center;gap:11px;background:var(--surface);border:1.5px solid var(--border);border-radius:13px;padding:11px 13px;box-shadow:0 2px 8px rgba(0,0,0,.04);transition:all .2s;margin-bottom:10px;}
.task-card.done{opacity:.62;background:var(--surface2);box-shadow:none;}
.task-card.overdue{border-color:var(--redS);}
.task-toggle{width:30px;height:30px;border-radius:50%;background:var(--accentS);border:none;cursor:pointer;font-size:15px;display:flex;align-items:center;justify-content:center;flex-shrink:0;}
.task-card.done .task-toggle{background:var(--greenS);}
.task-name{font-family:'Noto Serif SC',serif;font-size:14px;color:var(--text);white-space:nowrap;overflow:hidden;text-overflow:ellipsis;}
.task-card.done .task-name{text-decoration:line-through;color:var(--muted);}
.task-meta{display:flex;gap:7px;margin-top:3px;align-items:center;flex-wrap:wrap;}
.task-tag{font-size:10px;padding:1px 6px;border-radius:99px;background:var(--accentS);color:var(--accent);}
.task-del{background:none;border:none;cursor:pointer;font-size:13px;color:var(--muted);opacity:.5;flex-shrink:0;padding:4px;transition:opacity .15s;}
.task-del:hover{opacity:1;}
.progress-wrap{margin-top:22px;background:var(--surface);border-radius:13px;padding:15px;border:1px solid var(--border);}
.progress-head{display:flex;justify-content:space-between;font-size:12px;color:var(--muted);margin-bottom:7px;}
.progress-track{height:6px;background:var(--border);border-radius:99px;overflow:hidden;}
.progress-fill{height:100%;border-radius:99px;background:var(--accent);transition:width .5s ease;}
.empty{text-align:center;padding:38px 0;color:var(--muted);font-size:14px;background:var(--surface);border-radius:14px;border:1px dashed var(--border);}
.tab-strip{display:flex;gap:6px;margin-bottom:12px;overflow-x:auto;padding-bottom:2px;}
.sort-row{display:flex;gap:6px;margin-bottom:14px;align-items:center;}
.sort-lbl{font-size:11px;color:var(--muted);}
.sort-btn{padding:3px 9px;border-radius:99px;font-size:11px;border:1px solid var(--border);background:transparent;color:var(--muted);cursor:pointer;transition:all .15s;font-family:inherit;}
.sort-btn.active{border-color:var(--accent);background:var(--accentS);color:var(--accent);}

/* modal */
.modal-bg{position:fixed;inset:0;background:rgba(61,53,48,.38);display:flex;align-items:center;justify-content:center;z-index:1000;backdrop-filter:blur(4px);}
.modal-box{background:var(--surface);border-radius:20px;padding:26px;width:310px;box-shadow:0 20px 60px rgba(0,0,0,.15);border:1px solid var(--border);max-height:90vh;overflow-y:auto;}
.modal-title{font-family:'Noto Serif SC',serif;color:var(--text);font-size:15px;margin-bottom:18px;}
.field-label{display:block;font-size:11px;color:var(--muted);margin-bottom:4px;}
.field-input{width:100%;padding:8px 11px;border-radius:9px;border:1.5px solid var(--border);background:var(--bg);color:var(--text);font-size:13px;margin-bottom:13px;outline:none;font-family:inherit;}
.field-input:focus{border-color:var(--accent);}
.pri-row{display:flex;gap:7px;margin-bottom:14px;}
.pri-btn{flex:1;padding:7px 0;border-radius:9px;border:1.5px solid var(--border);background:var(--surface);cursor:pointer;font-size:14px;transition:all .15s;}
.pri-btn.active{border-color:var(--accent);background:var(--accentS);}
.m-tab-row{display:flex;gap:5px;margin-bottom:20px;flex-wrap:wrap;}
.modal-actions{display:flex;gap:9px;}

/* toast */
#toast{position:fixed;bottom:88px;left:50%;transform:translateX(-50%);background:var(--text);color:#FFF8F0;border-radius:99px;padding:8px 20px;font-size:13px;z-index:999;white-space:nowrap;font-family:'Noto Serif SC',serif;pointer-events:none;opacity:0;transition:opacity .25s;}
#toast.show{opacity:1;}

/* particles */
#particles{position:fixed;inset:0;pointer-events:none;z-index:9999;}
.particle{position:absolute;top:-30px;animation:fall 2.1s ease-in forwards;display:inline-block;}

/* ── SHOP ── */
.shop-tabs{display:flex;gap:6px;margin-bottom:16px;}
.coin-bar{display:flex;align-items:center;gap:8px;background:var(--accentS);border-radius:12px;padding:10px 15px;margin-bottom:16px;}
.coin-earn-hint{font-size:11px;color:var(--muted);margin-bottom:16px;line-height:1.7;background:var(--surface);border-radius:10px;padding:10px 13px;border:1px solid var(--border);}
.shop-grid{display:grid;grid-template-columns:1fr 1fr;gap:11px;}
.shop-card{background:var(--surface);border-radius:14px;padding:15px;border:1.5px solid var(--border);display:flex;flex-direction:column;gap:5px;align-items:center;transition:border-color .2s;}
.shop-card.owned{border-color:var(--greenS);}
.shop-card.equipped{border-color:var(--accent);background:var(--accentS);}
.owned-badge{font-size:11px;color:var(--green);background:var(--greenS);padding:2px 10px;border-radius:99px;}
.equip-badge{font-size:11px;color:var(--accent);background:#fff;padding:2px 10px;border-radius:99px;border:1px solid var(--accent);}

/* wishlist */
.wish-card{display:flex;align-items:center;gap:11px;background:var(--surface);border:1.5px solid var(--border);border-radius:13px;padding:13px;margin-bottom:10px;}
.wish-progress-track{flex:1;height:6px;background:var(--border);border-radius:99px;overflow:hidden;}
.wish-progress-fill{height:100%;border-radius:99px;background:var(--accent);transition:width .5s ease;}
.wish-buy-btn{padding:5px 12px;border-radius:9px;font-size:12px;border:none;cursor:pointer;font-family:inherit;transition:opacity .15s;flex-shrink:0;}
.wish-buy-btn:hover{opacity:.8;}
.wish-del{background:none;border:none;cursor:pointer;font-size:12px;color:var(--muted);opacity:.5;flex-shrink:0;}

/* cat collection */
.cat-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;margin-bottom:20px;}
.cat-card{background:var(--surface);border:1.5px solid var(--border);border-radius:14px;padding:14px 10px;display:flex;flex-direction:column;align-items:center;gap:5px;cursor:pointer;transition:all .2s;position:relative;}
.cat-card.active-cat{border-color:var(--accent);background:var(--accentS);}
.cat-card.locked{opacity:.5;cursor:not-allowed;}
.cat-card .cat-big{font-size:36px;}
.cat-card .cat-cname{font-size:11px;color:var(--text);font-family:'Noto Serif SC',serif;text-align:center;}
.cat-card .cat-cost{font-size:10px;color:var(--muted);}
.cat-card .lock-badge{position:absolute;top:6px;right:6px;font-size:10px;}
.active-crown{position:absolute;top:-8px;left:50%;transform:translateX(-50%);font-size:14px;}

/* stats */
.stat-grid{display:grid;grid-template-columns:1fr 1fr 1fr;gap:10px;margin:16px 0 14px;}
.stat-card{background:var(--surface);border-radius:14px;padding:13px 8px;border:1px solid var(--border);text-align:center;}
.stat-val{font-size:16px;font-weight:700;color:var(--text);font-family:'Noto Serif SC',serif;}
.stat-lbl{font-size:10px;color:var(--muted);margin-top:2px;}
.data-card{background:var(--surface);border-radius:14px;padding:16px;border:1px solid var(--border);margin-bottom:14px;}
.data-title{font-size:13px;color:var(--text);margin-bottom:13px;font-family:'Noto Serif SC',serif;}
.bar-chart{display:flex;gap:7px;align-items:flex-end;height:72px;}
.bar-col{flex:1;display:flex;flex-direction:column;align-items:center;gap:3px;}
.bar-fill{width:100%;border-radius:3px 3px 0 0;transition:height .5s ease;min-height:4px;}
.bar-day{font-size:9px;color:var(--muted);}
.heat-row{display:flex;flex-wrap:wrap;gap:3px;}
.heat-cell{width:21px;height:21px;border-radius:5px;background:var(--surface2);display:flex;align-items:center;justify-content:center;font-size:9px;}
.heat-cell.active{background:var(--accentS);}
.heat-cell.hot{background:var(--accent);}
.monthly-card{background:linear-gradient(135deg,var(--accentS),var(--surface2));border-radius:14px;padding:18px;border:1px solid var(--border);}

/* pomodoro */
#page-pomodoro.active{display:flex;flex-direction:column;align-items:center;gap:22px;}
#pomo-cat{font-size:60px;display:inline-block;}
#pomo-cat.bobbing{animation:bob 2s ease-in-out infinite;}
#pomo-msg{font-size:13px;color:var(--muted);text-align:center;max-width:220px;}
#pomo-ring{transition:stroke-dashoffset 1s linear;}
#pomo-time{font-family:'Noto Serif SC',serif;font-size:30px;fill:var(--text);}
.preset-row{display:flex;gap:7px;}

/* cat reaction popup */
#cat-reaction{position:fixed;top:80px;left:50%;transform:translateX(-50%) scale(0);background:var(--surface);border:1.5px solid var(--border);border-radius:16px;padding:14px 20px;z-index:500;text-align:center;box-shadow:0 8px 24px rgba(0,0,0,.12);transition:transform .3s cubic-bezier(.34,1.56,.64,1);pointer-events:none;white-space:nowrap;}
#cat-reaction.show{transform:translateX(-50%) scale(1);}
#cat-reaction .react-emoji{font-size:30px;}
#cat-reaction .react-msg{font-size:12px;color:var(--muted);margin-top:4px;font-family:'Noto Serif SC',serif;}

/* animations */
@keyframes fall{0%{transform:translateY(0) rotate(0deg);opacity:1}100%{transform:translateY(105vh) rotate(540deg);opacity:0}}
@keyframes float{0%,100%{transform:translateY(0)}50%{transform:translateY(-7px)}}
@keyframes bob{0%,100%{transform:translateY(0)}50%{transform:translateY(-5px)}}
@keyframes bounce{0%,100%{transform:scale(1)}50%{transform:scale(1.4)}}
@keyframes fadeUp{from{opacity:0;transform:translate(-50%,8px)}to{opacity:1;transform:translate(-50%,0)}}

::-webkit-scrollbar{width:4px;}
::-webkit-scrollbar-thumb{background:var(--border);border-radius:99px;}
</style>
</head>
<body>
<div id="app">

<!-- Header -->
<div id="header">
  <div id="cat-display" title="点击切换猫咪">
    <span id="cat-emoji" class="floating">😺</span>
    <div>
      <div id="cat-label">元气满满</div>
      <div id="cat-desc">今天超棒的！</div>
    </div>
  </div>
  <div id="coin-badge" title="🪙喵喵币">🪙 <span id="coin-count">120</span></div>
</div>

<!-- Content -->
<div id="content">

  <!-- TODO PAGE -->
  <div id="page-todo" class="page active">
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:16px;">
      <span class="page-title" style="margin-bottom:0">🐾 Cat's Todo Ledger</span>
      <button class="btn" id="add-btn" style="width:36px;height:36px;border-radius:50%;background:var(--accent);color:#fff;font-size:22px;display:flex;align-items:center;justify-content:center;box-shadow:0 4px 14px rgba(196,168,130,.45)">+</button>
    </div>
    <div class="tab-strip" id="tab-strip"></div>
    <div class="sort-row">
      <span class="sort-lbl">排序：</span>
      <button class="sort-btn active" data-sort="default">默认</button>
      <button class="sort-btn" data-sort="due">时间</button>
      <button class="sort-btn" data-sort="priority">🐟优先</button>
    </div>
    <div id="task-list"></div>
    <div class="progress-wrap" id="progress-wrap" style="display:none">
      <div class="progress-head"><span>今日投喂进度</span><span id="progress-label" style="color:var(--accent)"></span></div>
      <div class="progress-track"><div class="progress-fill" id="progress-fill" style="width:0%"></div></div>
    </div>
    <!-- coin hint on todo page -->
    <div style="margin-top:16px;font-size:11px;color:var(--muted);background:var(--surface);border-radius:10px;padding:10px 13px;border:1px solid var(--border);line-height:1.8;">
      🪙 <strong style="color:var(--accent)">喵喵币获取方式</strong><br>
      完成普通任务 +10 · 完成🐟🐟🐟高优先级 +30 · 专注成功 +50
    </div>
  </div>

  <!-- STATS PAGE -->
  <div id="page-stats" class="page">
    <span class="page-title">📊 喵迹追踪</span>
    <div class="stat-grid" id="stat-grid"></div>
    <div class="data-card">
      <div class="data-title">📈 本周捕鱼曲线</div>
      <div class="bar-chart" id="bar-chart"></div>
    </div>
    <div class="data-card">
      <div class="data-title">🐾 今日爪印时间轴</div>
      <div id="heat-comment" style="font-size:11px;color:var(--muted);margin-bottom:11px;"></div>
      <div class="heat-row" id="heat-row"></div>
    </div>
    <div class="monthly-card">
      <div style="font-size:14px;margin-bottom:8px;">📮 猫咪陪伴月报</div>
      <div id="monthly-body" style="font-family:'Noto Serif SC',serif;font-size:13px;color:var(--text);line-height:1.9;"></div>
    </div>
  </div>

  <!-- POMODORO PAGE -->
  <div id="page-pomodoro" class="page" style="padding:36px 20px;">
    <span class="page-title" style="margin-bottom:0">🍅 Neko Focus Timer</span>
    <span id="pomo-cat">🐱</span>
    <div id="pomo-msg">准备好了吗？本喵陪你专注！</div>
    <svg width="200" height="200" style="overflow:visible">
      <circle cx="100" cy="100" r="80" fill="none" stroke="var(--border)" stroke-width="9"/>
      <circle id="pomo-ring" cx="100" cy="100" r="80" fill="none" stroke="var(--accent)" stroke-width="9" stroke-dasharray="502.65" stroke-dashoffset="502.65" stroke-linecap="round" transform="rotate(-90 100 100)"/>
      <text id="pomo-time" x="100" y="108" text-anchor="middle">25:00</text>
    </svg>
    <div class="preset-row" id="preset-row">
      <button class="pill" data-min="15">15min</button>
      <button class="pill active" data-min="25">25min</button>
      <button class="pill" data-min="45">45min</button>
      <button class="pill" data-min="60">60min</button>
    </div>
    <div id="pomo-actions"></div>
  </div>

  <!-- SHOP PAGE -->
  <div id="page-shop" class="page">
    <span class="page-title">🏪 商城</span>
    <div class="coin-bar">
      <span style="font-size:20px">🪙</span>
      <span style="font-family:'Noto Serif SC',serif;color:var(--accent);font-size:16px;font-weight:700"><span id="shop-coins">120</span> 喵喵币</span>
    </div>
    <div class="coin-earn-hint">
      💡 <strong>怎么赚币？</strong> 完成任务 +10～+30 · 专注到底 +50<br>
      任务逾期不扣币，但猫猫会傲娇哈气～
    </div>
    <div class="shop-tabs" id="shop-tabs">
      <button class="pill active" data-stab="cats">🐱 猫咪收集</button>
      <button class="pill" data-stab="items">🎁 道具商城</button>
      <button class="pill" data-stab="wish">✨ 我的心愿单</button>
    </div>
    <div id="shop-content"></div>
  </div>

</div><!-- /content -->

<!-- Nav -->
<div id="nav">
  <button class="nav-btn active" data-page="todo"><span class="nav-icon">📋</span><span class="nav-label">任务</span></button>
  <button class="nav-btn" data-page="stats"><span class="nav-icon">📊</span><span class="nav-label">喵迹</span></button>
  <button class="nav-btn" data-page="pomodoro"><span class="nav-icon">🍅</span><span class="nav-label">专注</span></button>
  <button class="nav-btn" data-page="shop"><span class="nav-icon">🏪</span><span class="nav-label">商城</span></button>
</div>
</div><!-- /app -->

<div id="particles"></div>
<div id="toast"></div>

<!-- Cat reaction popup -->
<div id="cat-reaction">
  <div class="react-emoji" id="react-emoji"></div>
  <div class="react-msg" id="react-msg"></div>
</div>

<!-- Add Task Modal -->
<div id="modal-task" class="modal-bg" style="display:none">
  <div class="modal-box">
    <div class="modal-title">🐾 发布猫咪密令</div>
    <label class="field-label">任务名称</label>
    <input class="field-input" id="m-name" placeholder="给猫咪一个任务吧…"/>
    <label class="field-label">截止时间</label>
    <input class="field-input" id="m-due" type="date"/>
    <label class="field-label">小鱼干优先级</label>
    <div class="pri-row" id="pri-row">
      <button class="pri-btn active" data-p="1">🐟</button>
      <button class="pri-btn" data-p="2">🐟🐟</button>
      <button class="pri-btn" data-p="3">🐟🐟🐟</button>
    </div>
    <label class="field-label">分类标签</label>
    <div class="m-tab-row" id="m-tab-row">
      <button class="pill active" data-tab="今日打卡">今日打卡</button>
      <button class="pill" data-tab="长期目标">长期目标</button>
      <button class="pill" data-tab="灵感捕获">灵感捕获</button>
    </div>
    <div class="modal-actions">
      <button class="btn" id="m-cancel" style="flex:1;padding:9px 0;font-size:13px;background:var(--surface2);color:var(--muted)">取消</button>
      <button class="btn" id="m-confirm" style="flex:2;padding:9px 0;font-size:13px;background:var(--accent);color:#fff;font-family:'Noto Serif SC',serif">🐟 投放任务</button>
    </div>
  </div>
</div>

<!-- Add Wishlist Modal -->
<div id="modal-wish" class="modal-bg" style="display:none">
  <div class="modal-box">
    <div class="modal-title">✨ 添加心愿单</div>
    <label class="field-label">心愿名称</label>
    <input class="field-input" id="w-name" placeholder="我想买一个…"/>
    <label class="field-label">目标金额（喵喵币）</label>
    <input class="field-input" id="w-price" type="number" min="1" placeholder="需要多少币？"/>
    <label class="field-label">表情图标（emoji）</label>
    <input class="field-input" id="w-emoji" placeholder="✨ 填一个 emoji 吧" maxlength="4"/>
    <div class="modal-actions">
      <button class="btn" id="w-cancel" style="flex:1;padding:9px 0;font-size:13px;background:var(--surface2);color:var(--muted)">取消</button>
      <button class="btn" id="w-confirm" style="flex:2;padding:9px 0;font-size:13px;background:var(--accent);color:#fff;font-family:'Noto Serif SC',serif">✨ 加入心愿单</button>
    </div>
  </div>
</div>

<script>
/* ════════════════════════════════════════
   DATA
════════════════════════════════════════ */
const TABS = ["今日打卡","长期目标","灵感捕获"];

// All cats — some locked until purchased
const ALL_CATS = [
  { id:"classic",  emoji:"😺", name:"经典橘猫",   desc:"今天超棒的！",    sad:"碗是空的喵呜…", focus:"认真工作中…", price:0,   unlocked:true },
  { id:"cool",     emoji:"😎", name:"墨镜酷猫",   desc:"酷得无与伦比",    sad:"哼，不理你",     focus:"专注是一种态度", price:80,  unlocked:false },
  { id:"sleepy",   emoji:"😴", name:"爱睡小懒猫", desc:"昨晚没睡够喵…",   sad:"zzz 摆烂中",     focus:"撑着眼皮陪你", price:100, unlocked:false },
  { id:"angry",    emoji:"😾", name:"傲娇猫",     desc:"才不是在等你呢",  sad:"哈——！",          focus:"本喵勉强陪你", price:120, unlocked:false },
  { id:"love",     emoji:"😻", name:"爱心猫",     desc:"最喜欢你了喵！",  sad:"你让我好伤心…",  focus:"爱你哟一起加油", price:150, unlocked:false },
  { id:"ghost",    emoji:"🙀", name:"惊吓猫",     desc:"好可怕的世界！",  sad:"吓到了喵呜",     focus:"小心翼翼专注中", price:180, unlocked:false },
  { id:"cool2",    emoji:"🦁", name:"霸气狮猫",   desc:"本王驾到",        sad:"孤独的王者",      focus:"王者级专注！",  price:200, unlocked:false },
  { id:"star",     emoji:"🌟", name:"星猫",       desc:"闪闪发光喵！",    sad:"星星黯淡了…",    focus:"宇宙级专注！",  price:250, unlocked:false },
  { id:"alien",    emoji:"👽", name:"外星猫",     desc:"喵星人降临",      sad:"想回喵星了",      focus:"思维超频中",   price:300, unlocked:false },
];

const SHOP_ITEMS = [
  { id:1, name:"高级猫罐头", emoji:"🥫", price:80,  desc:"好感度 +15", reaction:{emoji:"😸",msg:"哇是最爱的罐头！咕噜咕噜…"} },
  { id:2, name:"逗猫棒",     emoji:"🪄", price:50,  desc:"解锁特殊对话", reaction:{emoji:"😹",msg:"哇！逗猫棒！快来玩！"} },
  { id:3, name:"猫咪斗笠",   emoji:"🎩", price:120, desc:"稀有装扮",    reaction:{emoji:"🐱",msg:"哼，戴上了也很帅呢"} },
  { id:4, name:"莫兰迪猫窝", emoji:"🛏️", price:200, desc:"背景皮肤",   reaction:{emoji:"😺",msg:"这个窝好软！最喜欢了！"} },
  { id:5, name:"小鱼干礼包", emoji:"🐟", price:60,  desc:"好感度 +10", reaction:{emoji:"😋",msg:"小鱼干！小鱼干！快给我！"} },
  { id:6, name:"毛线球",     emoji:"🧶", price:40,  desc:"玩耍动画",   reaction:{emoji:"🤩",msg:"毛线球！！！滚来滚去！！"} },
];

let state = {
  tasks:[
    {id:1,name:"完成今日写作任务",due:"",priority:3,tab:"今日打卡",done:false},
    {id:2,name:"备考刷题 2小时",due:"",priority:2,tab:"今日打卡",done:false},
    {id:3,name:"记录一个灵感点子",due:"",priority:1,tab:"灵感捕获",done:false},
  ],
  coins:120, owned:[], sessions:3,
  activeCat:"classic",
  cats: ALL_CATS.map(c=>({...c})),
  wishlist:[
    {id:1,name:"新键盘",emoji:"⌨️",price:500,bought:false},
    {id:2,name:"奶茶自由",emoji:"🧋",price:80,bought:false},
  ],
  nextId:10, nextWId:10,
  currentTab:"今日打卡", currentSort:"default",
  modalPriority:1, modalTab:"今日打卡",
  shopTab:"cats",
};

/* ════════════════════════════════════════
   HELPERS
════════════════════════════════════════ */
let toastTimer=null;
function showToast(msg){
  const el=document.getElementById("toast");
  el.textContent=msg; el.classList.add("show");
  clearTimeout(toastTimer);
  toastTimer=setTimeout(()=>el.classList.remove("show"),2600);
}

function esc(s){return s.replace(/&/g,"&amp;").replace(/</g,"&lt;").replace(/>/g,"&gt;");}

function getActiveCat(){return state.cats.find(c=>c.id===state.activeCat)||state.cats[0];}

function setCatDisplay(){
  const c=getActiveCat();
  const mood=state.catState||"happy";
  const emoji=c.emoji;
  const label=c.name;
  const desc=mood==="sad"?c.sad:mood==="focus"?c.focus:c.desc;
  document.getElementById("cat-emoji").textContent=emoji;
  document.getElementById("cat-label").textContent=label;
  document.getElementById("cat-desc").textContent=desc;
  const el=document.getElementById("cat-emoji");
  el.className=mood==="happy"?"floating":mood==="focus"?"bobbing":"";
}

function setCatState(key){
  state.catState=key;
  setCatDisplay();
}

function autoUpdateCat(){
  if(state.catState==="focus")return;
  const today=state.tasks.filter(t=>t.tab==="今日打卡");
  if(!today.length){setCatState("happy");return;}
  const rate=today.filter(t=>t.done).length/today.length;
  const overdue=state.tasks.some(t=>!t.done&&t.due&&new Date(t.due)<new Date());
  setCatState(rate>0.8?"happy":(overdue||rate<0.3)?"sad":"happy");
}

function updateCoins(){
  document.getElementById("coin-count").textContent=state.coins;
  document.getElementById("shop-coins").textContent=state.coins;
}

/* cat reaction popup */
let reactTimer=null;
function showReaction(emoji,msg){
  document.getElementById("react-emoji").textContent=emoji;
  document.getElementById("react-msg").textContent=msg;
  const el=document.getElementById("cat-reaction");
  el.classList.add("show");
  // also bounce the header cat
  const catEl=document.getElementById("cat-emoji");
  catEl.classList.add("bounce");
  setTimeout(()=>catEl.classList.remove("bounce"),500);
  clearTimeout(reactTimer);
  reactTimer=setTimeout(()=>el.classList.remove("show"),2800);
}

function fireParticles(){
  const c=document.getElementById("particles");
  c.innerHTML="";
  const e=["🐟","🐠","🥫","✨","⭐"];
  for(let i=0;i<22;i++){
    const el=document.createElement("span");
    el.className="particle";
    el.textContent=e[i%5];
    el.style.left=Math.random()*100+"%";
    el.style.fontSize=(18+Math.random()*12)+"px";
    el.style.animationDelay=Math.random()*.55+"s";
    c.appendChild(el);
  }
  setTimeout(()=>c.innerHTML="",2700);
}

/* ════════════════════════════════════════
   TODO
════════════════════════════════════════ */
function renderTabStrip(){
  const s=document.getElementById("tab-strip");
  s.innerHTML=TABS.map(t=>`<button class="pill${t===state.currentTab?" active":""}" onclick="setTab('${t}')">${t}</button>`).join("");
}
function setTab(t){state.currentTab=t;renderTabStrip();renderTaskList();}

function renderTaskList(){
  let visible=state.tasks.filter(t=>t.tab===state.currentTab);
  if(state.currentSort==="due") visible=[...visible].sort((a,b)=>(a.due||"9")>(b.due||"9")?1:-1);
  if(state.currentSort==="priority") visible=[...visible].sort((a,b)=>b.priority-a.priority);
  const list=document.getElementById("task-list");
  if(!visible.length){list.innerHTML=`<div class="empty"><div style="font-size:30px;margin-bottom:8px">🐟</div>这里空空如也喵…快发密令吧！</div>`;renderProgress();return;}
  list.innerHTML=visible.map(t=>{
    const od=!t.done&&t.due&&new Date(t.due)<new Date();
    return`<div class="task-card${t.done?" done":""}${od?" overdue":""}">
      <button class="task-toggle" onclick="toggleTask(${t.id})">${t.done?"🍲":"🍜"}</button>
      <div style="flex:1;min-width:0">
        <div class="task-name">${esc(t.name)}</div>
        <div class="task-meta">
          <span>${"🐟".repeat(t.priority)}</span>
          ${t.due?`<span style="font-size:11px;color:${od?"var(--red)":"var(--muted)"}">${od?"⚠️ ":"⏰ "}${t.due}</span>`:""}
          <span class="task-tag">${t.tab}</span>
        </div>
      </div>
      <button class="task-del" onclick="deleteTask(${t.id})">✕</button>
    </div>`;
  }).join("");
  renderProgress();
}

function renderProgress(){
  const today=state.tasks.filter(t=>t.tab==="今日打卡");
  const w=document.getElementById("progress-wrap");
  if(!today.length){w.style.display="none";return;}
  w.style.display="block";
  const done=today.filter(t=>t.done).length;
  const pct=Math.round(done/today.length*100);
  document.getElementById("progress-label").textContent=`${done}/${today.length} · ${pct}%`;
  const f=document.getElementById("progress-fill");
  f.style.width=pct+"%";
  f.style.background=pct>=80?"var(--green)":"var(--accent)";
}

function toggleTask(id){
  const t=state.tasks.find(x=>x.id===id);
  if(!t)return;
  if(!t.done){
    const earn=t.priority===3?30:10;
    state.coins+=earn;updateCoins();
    fireParticles();
    showToast(`✨ 喂食成功！+${earn} 喵喵币`);
  }
  t.done=!t.done;
  autoUpdateCat();renderTaskList();
}
function deleteTask(id){state.tasks=state.tasks.filter(t=>t.id!==id);autoUpdateCat();renderTaskList();}

document.querySelectorAll(".sort-btn").forEach(b=>{
  b.onclick=()=>{
    state.currentSort=b.dataset.sort;
    document.querySelectorAll(".sort-btn").forEach(x=>x.classList.remove("active"));
    b.classList.add("active");renderTaskList();
  };
});

/* ════════════════════════════════════════
   ADD TASK MODAL
════════════════════════════════════════ */
document.getElementById("add-btn").onclick=()=>{
  document.getElementById("m-name").value="";
  document.getElementById("m-due").value="";
  state.modalPriority=1;state.modalTab="今日打卡";syncModal();
  document.getElementById("modal-task").style.display="flex";
};
document.getElementById("m-cancel").onclick=()=>document.getElementById("modal-task").style.display="none";
document.getElementById("modal-task").onclick=e=>{if(e.target===document.getElementById("modal-task"))document.getElementById("modal-task").style.display="none";};
document.querySelectorAll("#pri-row .pri-btn").forEach(b=>{b.onclick=()=>{state.modalPriority=+b.dataset.p;syncModal();};});
document.querySelectorAll("#m-tab-row .pill").forEach(b=>{b.onclick=()=>{state.modalTab=b.dataset.tab;syncModal();};});
function syncModal(){
  document.querySelectorAll("#pri-row .pri-btn").forEach(b=>b.classList.toggle("active",+b.dataset.p===state.modalPriority));
  document.querySelectorAll("#m-tab-row .pill").forEach(b=>b.classList.toggle("active",b.dataset.tab===state.modalTab));
}
document.getElementById("m-confirm").onclick=()=>{
  const name=document.getElementById("m-name").value.trim();
  if(!name)return;
  state.nextId++;
  state.tasks.push({id:state.nextId,name,due:document.getElementById("m-due").value,priority:state.modalPriority,tab:state.modalTab,done:false});
  document.getElementById("modal-task").style.display="none";
  showToast("🐾 密令已发布！");
  renderTabStrip();renderTaskList();
};

/* ════════════════════════════════════════
   SHOP
════════════════════════════════════════ */
document.querySelectorAll(".shop-tabs .pill").forEach(b=>{
  b.onclick=()=>{
    state.shopTab=b.dataset.stab;
    document.querySelectorAll(".shop-tabs .pill").forEach(x=>x.classList.remove("active"));
    b.classList.add("active");renderShopContent();
  };
});

function renderShop(){
  document.getElementById("shop-coins").textContent=state.coins;
  renderShopContent();
}

function renderShopContent(){
  const el=document.getElementById("shop-content");
  if(state.shopTab==="cats")    el.innerHTML=renderCatGrid();
  else if(state.shopTab==="items") el.innerHTML=renderItemGrid();
  else el.innerHTML=renderWishlist();
}

/* ── CAT GRID ── */
function renderCatGrid(){
  const grid=state.cats.map(c=>{
    const isActive=c.id===state.activeCat;
    const equipped=isActive?`<div class="active-crown">👑</div>`:"";
    if(!c.unlocked){
      const canBuy=state.coins>=c.price;
      return`<div class="cat-card locked" style="opacity:1">
        ${equipped}
        <div class="cat-big">${c.emoji}</div>
        <div class="cat-cname">${c.name}</div>
        <div class="cat-cost">🪙 ${c.price}</div>
        <button class="btn" onclick="buyCat('${c.id}')"
          style="padding:4px 12px;font-size:11px;margin-top:2px;
            background:${canBuy?"var(--accent)":"var(--surface2)"};
            color:${canBuy?"#fff":"var(--muted)"};
            cursor:${canBuy?"pointer":"not-allowed"}">
          ${canBuy?"解锁":"🔒 不够"}
        </button>
      </div>`;
    }
    return`<div class="cat-card${isActive?" active-cat":""}" onclick="switchCat('${c.id}')">
      ${equipped}
      <div class="cat-big">${c.emoji}</div>
      <div class="cat-cname">${c.name}</div>
      <div class="cat-cost">${isActive?"✓ 使用中":"点击切换"}</div>
    </div>`;
  }).join("");
  return`<div class="cat-grid">${grid}</div>`;
}

function buyCat(id){
  const c=state.cats.find(x=>x.id===id);
  if(!c||c.unlocked||state.coins<c.price)return;
  state.coins-=c.price;updateCoins();
  c.unlocked=true;
  state.activeCat=id;
  setCatDisplay();
  showToast(`🎉 解锁了${c.name}！`);
  showReaction(c.emoji,"新主人好！我是"+c.name+"喵！");
  renderShopContent();
}

function switchCat(id){
  const c=state.cats.find(x=>x.id===id);
  if(!c||!c.unlocked)return;
  state.activeCat=id;
  setCatDisplay();
  showReaction(c.emoji,"换我了！我是"+c.name+"喵～");
  showToast("已切换为 "+c.name+" 🐾");
  renderShopContent();
}

/* ── ITEM GRID ── */
function renderItemGrid(){
  return`<div class="shop-grid">`+SHOP_ITEMS.map(item=>{
    const owned=state.owned.includes(item.id);
    const can=state.coins>=item.price;
    const btn=owned
      ?`<span class="owned-badge">已拥有 ✓</span>`
      :`<button class="btn" onclick="buyItem(${item.id})"
          style="padding:5px 14px;font-size:12px;
            background:${can?"var(--accent)":"var(--surface2)"};
            color:${can?"#fff":"var(--muted)"};
            cursor:${can?"pointer":"not-allowed"}">
          🪙 ${item.price}
        </button>`;
    return`<div class="shop-card${owned?" owned":""}">
      <span style="font-size:34px">${item.emoji}</span>
      <span style="font-size:13px;color:var(--text);font-family:'Noto Serif SC',serif;text-align:center">${item.name}</span>
      <span style="font-size:11px;color:var(--muted);text-align:center">${item.desc}</span>
      ${btn}
    </div>`;
  }).join("")+`</div>`;
}

function buyItem(id){
  const item=SHOP_ITEMS.find(x=>x.id===id);
  if(!item||state.owned.includes(id)||state.coins<item.price)return;
  state.coins-=item.price;state.owned.push(id);
  updateCoins();
  showToast(`🎁 ${item.emoji} ${item.name} 到手！`);
  // cat reacts!
  showReaction(item.reaction.emoji, item.reaction.msg);
  renderShopContent();
}

/* ── WISHLIST ── */
function renderWishlist(){
  const items=state.wishlist.map(w=>{
    const pct=Math.min(100,Math.round(state.coins/w.price*100));
    const canBuy=state.coins>=w.price;
    const done=w.bought;
    return`<div class="wish-card" style="${done?"opacity:.6":""}">
      <span style="font-size:28px">${w.emoji||"✨"}</span>
      <div style="flex:1;min-width:0">
        <div style="font-family:'Noto Serif SC',serif;font-size:13px;color:${done?"var(--muted)":""};text-decoration:${done?"line-through":""}">${esc(w.name)}</div>
        <div style="font-size:11px;color:var(--muted);margin:3px 0 5px">目标 🪙${w.price} · 当前 ${Math.min(state.coins,w.price)}/${w.price}</div>
        <div class="wish-progress-track">
          <div class="wish-progress-fill" style="width:${pct}%;background:${canBuy?"var(--green)":"var(--accent)"}"></div>
        </div>
      </div>
      ${done
        ?`<span style="font-size:18px">✅</span>`
        :`<button class="wish-buy-btn" onclick="buyWish(${w.id})"
            style="background:${canBuy?"var(--accent)":"var(--surface2)"};color:${canBuy?"#fff":"var(--muted)"};cursor:${canBuy?"pointer":"not-allowed"}">
            ${canBuy?"达成！":"攒币中"}
          </button>`
      }
      <button class="wish-del" onclick="deleteWish(${w.id})">✕</button>
    </div>`;
  }).join("");
  return`
    <button class="btn" onclick="openWishModal()"
      style="width:100%;padding:11px;margin-bottom:14px;background:var(--accentS);color:var(--accent);font-size:13px;font-family:'Noto Serif SC',serif;border:1.5px dashed var(--accent)">
      + 添加新心愿
    </button>
    ${items||`<div class="empty"><div style="font-size:30px;margin-bottom:8px">✨</div>心愿单空空的，添加你的目标吧！</div>`}`;
}

function buyWish(id){
  const w=state.wishlist.find(x=>x.id===id);
  if(!w||w.bought||state.coins<w.price)return;
  state.coins-=w.price;updateCoins();
  w.bought=true;
  showToast(`🎉 ${w.emoji} ${w.name} 心愿达成！`);
  showReaction("🎊","心愿实现了！恭喜两脚兽喵！");
  fireParticles();
  renderShopContent();
}

function deleteWish(id){
  state.wishlist=state.wishlist.filter(x=>x.id!==id);
  renderShopContent();
}

function openWishModal(){
  document.getElementById("w-name").value="";
  document.getElementById("w-price").value="";
  document.getElementById("w-emoji").value="";
  document.getElementById("modal-wish").style.display="flex";
}
document.getElementById("w-cancel").onclick=()=>document.getElementById("modal-wish").style.display="none";
document.getElementById("modal-wish").onclick=e=>{if(e.target===document.getElementById("modal-wish"))document.getElementById("modal-wish").style.display="none";};
document.getElementById("w-confirm").onclick=()=>{
  const name=document.getElementById("w-name").value.trim();
  const price=parseInt(document.getElementById("w-price").value);
  const emoji=document.getElementById("w-emoji").value.trim()||"✨";
  if(!name||!price||price<1)return;
  state.nextWId++;
  state.wishlist.push({id:state.nextWId,name,price,emoji,bought:false});
  document.getElementById("modal-wish").style.display="none";
  showToast("✨ 心愿已加入！攒币冲！");
  renderShopContent();
};

/* ════════════════════════════════════════
   STATS
════════════════════════════════════════ */
function renderStats(){
  const done=state.tasks.filter(t=>t.done).length;
  const total=state.tasks.length;
  const rate=total?Math.round(done/total*100):0;
  const fish=state.tasks.filter(t=>t.done).reduce((s,t)=>s+t.priority*10,0);
  document.getElementById("stat-grid").innerHTML=[
    {e:"🎣",v:`${rate}%`,l:"捕鱼成功率"},
    {e:"🐟",v:`×${fish}`,l:"总小鱼干"},
    {e:"🍅",v:`${state.sessions}次`,l:"专注场次"},
  ].map(c=>`<div class="stat-card"><div style="font-size:22px">${c.e}</div><div class="stat-val">${c.v}</div><div class="stat-lbl">${c.l}</div></div>`).join("");
  const week=[
    {day:"周一",d:3,t:4},{day:"周二",d:5,t:5},{day:"周三",d:2,t:6},
    {day:"周四",d:4,t:4},{day:"周五",d:1,t:3},{day:"周六",d:3,t:3},
    {day:"今天",d:done,t:Math.max(total,1)},
  ];
  document.getElementById("bar-chart").innerHTML=week.map(d=>{
    const p=d.t?d.d/d.t:0;
    const h=Math.max(p*58,4);
    const bg=p>=.8?"var(--green)":p>=.5?"var(--accent)":"var(--redS)";
    return`<div class="bar-col"><div class="bar-fill" style="height:${h}px;background:${bg}"></div><div class="bar-day">${d.day}</div></div>`;
  }).join("");
  const hr=new Date().getHours();
  document.getElementById("heat-comment").textContent=
    hr>=6&&hr<=9?"你是早起捕猎的优秀小猫喵！🌅":
    hr>=14&&hr<=17?"下午阳光让猫猫打起精神啦！☀️":
    hr>=23||hr<=3?"夜猫子大爆发！但该睡觉了喵呜…🌙":
    "今天也要认真捕鱼哦喵！🐾";
  const hot=[10,15,22],act=[9,10,14,15,16,21,22];
  document.getElementById("heat-row").innerHTML=Array.from({length:24},(_,i)=>{
    const cls=hot.includes(i)?"heat-cell hot":act.includes(i)?"heat-cell active":"heat-cell";
    return`<div class="${cls}" title="${i}:00">${hot.includes(i)?"🐾":""}</div>`;
  }).join("");
  document.getElementById("monthly-body").innerHTML=
    `本月，两脚兽陪我度过了 <strong>${state.sessions*25}</strong> 分钟的专注时光，一共抓到了 <strong>${fish}</strong> 根小鱼干。谢谢你的养活喵！🐟`;
}

/* ════════════════════════════════════════
   POMODORO
════════════════════════════════════════ */
let pomoState="idle",pomoPreset=25,pomoMins=25,pomoSecs=0,pomoTimer=null;
const CIRC=2*Math.PI*80;

function renderPomo(){
  const cat=getActiveCat();
  const catMap={idle:cat.emoji,running:"🦁",done:"😸",fail:"😾"};
  const msgMap={
    idle:"准备好了吗？本喵陪你专注！",
    running:"认真工作，本喵守候着…",
    done:"专注成功！奉上高纯度小鱼干 🐟🐟🐟",
    fail:"哈—— 本次专注失败，猫猫好失望…",
  };
  const catEl=document.getElementById("pomo-cat");
  catEl.textContent=catMap[pomoState]||cat.emoji;
  catEl.className=pomoState==="running"?"bobbing":"";
  document.getElementById("pomo-msg").textContent=msgMap[pomoState]||"";
  const total=pomoPreset*60,remain=pomoMins*60+pomoSecs;
  const pct=pomoState==="idle"?0:Math.max(0,1-remain/total);
  document.getElementById("pomo-ring").setAttribute("stroke-dashoffset",CIRC*(1-pct));
  document.getElementById("pomo-time").textContent=pad(pomoMins)+":"+pad(pomoSecs);
  document.getElementById("preset-row").style.display=pomoState==="idle"?"flex":"none";
  const acts=document.getElementById("pomo-actions");
  acts.innerHTML="";
  if(pomoState==="idle"){
    const b=mkBtn("🐾 开始专注","padding:10px 32px;font-size:14px;background:var(--accent);color:#fff;font-family:'Noto Serif SC',serif;box-shadow:0 4px 14px rgba(196,168,130,.4)");
    b.onclick=pomoStart;acts.appendChild(b);
  }else if(pomoState==="running"){
    const b=mkBtn("放弃本次","padding:10px 32px;font-size:14px;background:var(--redS);color:var(--red)");
    b.onclick=pomoFail;acts.appendChild(b);
  }else{
    const b=mkBtn("再来一次","padding:10px 32px;font-size:14px;background:var(--accentS);color:var(--accent);font-family:'Noto Serif SC',serif");
    b.onclick=pomoReset;acts.appendChild(b);
  }
}
function pad(n){return String(n).padStart(2,"0");}
function mkBtn(text,style){const b=document.createElement("button");b.className="btn";b.textContent=text;b.style.cssText=style;return b;}
function pomoStart(){pomoMins=pomoPreset;pomoSecs=0;pomoState="running";setCatState("focus");renderPomo();pomoTimer=setInterval(pomoTick,1000);}
function pomoTick(){
  if(pomoSecs>0)pomoSecs--;
  else if(pomoMins>0){pomoMins--;pomoSecs=59;}
  else{
    clearInterval(pomoTimer);pomoState="done";
    state.coins+=50;state.sessions++;updateCoins();
    setCatState("happy");
    showToast("✨ 专注成功！+50 喵喵币");
    showReaction(getActiveCat().emoji,"你太棒了！奖励小鱼干！🐟");
    renderPomo();return;
  }
  const total=pomoPreset*60,remain=pomoMins*60+pomoSecs;
  const pct=Math.max(0,1-remain/total);
  document.getElementById("pomo-ring").setAttribute("stroke-dashoffset",CIRC*(1-pct));
  document.getElementById("pomo-time").textContent=pad(pomoMins)+":"+pad(pomoSecs);
}
function pomoFail(){clearInterval(pomoTimer);pomoState="fail";setCatState("sad");showReaction("😾","哼！竟然放弃了！");renderPomo();}
function pomoReset(){clearInterval(pomoTimer);pomoMins=pomoPreset;pomoSecs=0;pomoState="idle";renderPomo();}
document.querySelectorAll("#preset-row .pill").forEach(b=>{
  b.onclick=()=>{
    pomoPreset=+b.dataset.min;pomoMins=pomoPreset;pomoSecs=0;
    document.querySelectorAll("#preset-row .pill").forEach(x=>x.classList.remove("active"));
    b.classList.add("active");renderPomo();
  };
});

/* header cat click → cycle to next unlocked cat */
document.getElementById("cat-display").onclick=()=>{
  const unlocked=state.cats.filter(c=>c.unlocked);
  const idx=unlocked.findIndex(c=>c.id===state.activeCat);
  const next=unlocked[(idx+1)%unlocked.length];
  switchCat(next.id);
};

/* ════════════════════════════════════════
   NAV
════════════════════════════════════════ */
document.querySelectorAll(".nav-btn").forEach(b=>{
  b.onclick=()=>{
    const pid=b.dataset.page;
    document.querySelectorAll(".page").forEach(p=>{p.classList.remove("active");p.style.display="none";});
    const target=document.getElementById("page-"+pid);
    target.style.display=pid==="pomodoro"?"flex":"block";
    target.classList.add("active");
    document.querySelectorAll(".nav-btn").forEach(x=>x.classList.remove("active"));
    b.classList.add("active");
    if(pid==="stats")renderStats();
    if(pid==="shop")renderShop();
    if(pid==="pomodoro")renderPomo();
  };
});

/* ════════════════════════════════════════
   INIT
════════════════════════════════════════ */
renderTabStrip();
renderTaskList();
setCatDisplay();
</script>
</body>
</html>
