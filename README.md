<!DOCTYPE html>

<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no" />
<title>SLITHER ARENA</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Major+Mono+Display&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #07060d;
    --ink: #e7e9ff;
    --dim: #6f72a3;
    --acid: #b8ff2e;
    --magenta: #ff3eaa;
    --cyan: #2ee0ff;
    --gold: #ffd84d;
  }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  html, body {
    height: 100%; width: 100%;
    background: var(--bg);
    color: var(--ink);
    font-family: 'Space Mono', monospace;
    overflow: hidden;
    user-select: none;
    -webkit-user-select: none;
    -webkit-tap-highlight-color: transparent;
    touch-action: none;
    cursor: crosshair;
  }
  #game { display: block; width: 100vw; height: 100vh; }

/* ===== MENU OVERLAY ===== */
.menu {
position: fixed; inset: 0;
background:
radial-gradient(ellipse at 30% 20%, rgba(255,62,170,0.18), transparent 50%),
radial-gradient(ellipse at 80% 80%, rgba(46,224,255,0.15), transparent 50%),
linear-gradient(180deg, #07060d 0%, #0d0a1c 100%);
display: flex; flex-direction: column;
align-items: center; justify-content: center;
z-index: 10;
padding: 2rem;
}
.menu.hidden { display: none; }
.title {
font-family: ‘Major Mono Display’, monospace;
font-size: clamp(2.5rem, 8vw, 6rem);
letter-spacing: 0.1em;
line-height: 0.9;
background: linear-gradient(90deg, var(–acid), var(–cyan), var(–magenta));
-webkit-background-clip: text;
background-clip: text;
color: transparent;
text-align: center;
margin-bottom: 0.5rem;
text-shadow: 0 0 60px rgba(184,255,46,0.2);
}
.subtitle {
color: var(–dim);
font-size: 0.85rem;
letter-spacing: 0.3em;
text-transform: uppercase;
margin-bottom: 3rem;
text-align: center;
}
.panel {
background: rgba(20,17,40,0.6);
backdrop-filter: blur(12px);
-webkit-backdrop-filter: blur(12px);
border: 1px solid rgba(231,233,255,0.08);
padding: 2rem;
border-radius: 4px;
width: min(420px, 90vw);
box-shadow: 0 0 80px rgba(46,224,255,0.05);
}
.field { margin-bottom: 1.25rem; }
.field label {
display: block;
font-size: 0.7rem;
letter-spacing: 0.25em;
color: var(–dim);
text-transform: uppercase;
margin-bottom: 0.5rem;
}
.field input {
width: 100%;
background: rgba(0,0,0,0.4);
border: 1px solid rgba(231,233,255,0.15);
color: var(–ink);
padding: 0.7rem 0.9rem;
font-family: inherit;
font-size: 1rem;
letter-spacing: 0.05em;
border-radius: 2px;
outline: none;
transition: border-color 0.2s, box-shadow 0.2s;
}
.field input:focus {
border-color: var(–acid);
box-shadow: 0 0 0 3px rgba(184,255,46,0.15);
}
.diff-row { display: flex; gap: 0.5rem; }
.diff-btn {
flex: 1;
background: rgba(0,0,0,0.4);
border: 1px solid rgba(231,233,255,0.15);
color: var(–ink);
padding: 0.7rem;
font-family: inherit;
font-size: 0.75rem;
letter-spacing: 0.2em;
text-transform: uppercase;
cursor: pointer;
border-radius: 2px;
transition: all 0.15s;
}
.diff-btn:hover { border-color: rgba(231,233,255,0.4); }
.diff-btn.active {
background: rgba(184,255,46,0.12);
border-color: var(–acid);
color: var(–acid);
box-shadow: 0 0 20px rgba(184,255,46,0.2);
}
.diff-btn[data-d=“medium”].active {
background: rgba(46,224,255,0.12);
border-color: var(–cyan);
color: var(–cyan);
box-shadow: 0 0 20px rgba(46,224,255,0.2);
}
.diff-btn[data-d=“hard”].active {
background: rgba(255,62,170,0.12);
border-color: var(–magenta);
color: var(–magenta);
box-shadow: 0 0 20px rgba(255,62,170,0.2);
}
.play-btn {
width: 100%;
background: var(–acid);
color: #0a0a0a;
border: none;
padding: 1rem;
font-family: ‘Major Mono Display’, monospace;
font-size: 1.1rem;
letter-spacing: 0.3em;
cursor: pointer;
border-radius: 2px;
margin-top: 1rem;
transition: transform 0.1s, box-shadow 0.2s;
}
.play-btn:hover {
transform: translateY(-1px);
box-shadow: 0 8px 30px rgba(184,255,46,0.3);
}
.play-btn:active { transform: translateY(0); }
.hint {
margin-top: 1.5rem;
text-align: center;
color: var(–dim);
font-size: 0.7rem;
letter-spacing: 0.15em;
line-height: 1.8;
}
.hint kbd {
background: rgba(231,233,255,0.08);
border: 1px solid rgba(231,233,255,0.15);
padding: 1px 6px;
border-radius: 2px;
font-family: inherit;
color: var(–ink);
}

/* ===== Sound toggle ===== */
.sound-toggle {
margin-top: 1rem;
display: flex;
align-items: center;
justify-content: center;
gap: 0.5rem;
color: var(–dim);
font-size: 0.7rem;
letter-spacing: 0.2em;
text-transform: uppercase;
cursor: pointer;
}
.sound-toggle input { accent-color: var(–acid); }

/* ===== HUD ===== */
.hud {
position: fixed;
top: 1rem; left: 1rem;
z-index: 5;
pointer-events: none;
}
.hud-card {
background: rgba(13,10,28,0.55);
backdrop-filter: blur(8px);
-webkit-backdrop-filter: blur(8px);
border: 1px solid rgba(231,233,255,0.08);
padding: 0.6rem 1rem;
border-radius: 2px;
margin-bottom: 0.4rem;
font-size: 0.85rem;
}
.hud-label {
color: var(–dim);
font-size: 0.6rem;
letter-spacing: 0.25em;
text-transform: uppercase;
display: block;
}
.hud-value {
font-family: ‘Major Mono Display’, monospace;
font-size: 1.4rem;
color: var(–acid);
letter-spacing: 0.05em;
}
.leaderboard {
position: fixed;
top: 1rem; right: 1rem;
z-index: 5;
background: rgba(13,10,28,0.55);
backdrop-filter: blur(8px);
-webkit-backdrop-filter: blur(8px);
border: 1px solid rgba(231,233,255,0.08);
padding: 0.8rem 1rem;
border-radius: 2px;
min-width: 200px;
pointer-events: none;
}
.lb-title {
color: var(–dim);
font-size: 0.6rem;
letter-spacing: 0.25em;
text-transform: uppercase;
margin-bottom: 0.5rem;
}
.lb-row {
display: flex; justify-content: space-between;
font-size: 0.8rem;
padding: 2px 0;
color: var(–ink);
}
.lb-row.me { color: var(–acid); }
.lb-row .rank { color: var(–dim); margin-right: 0.5rem; }
.lb-row .name { flex: 1; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.lb-row .score { font-family: ‘Major Mono Display’, monospace; }

/* ===== MINIMAP ===== */
.minimap-wrap {
position: fixed;
bottom: 1rem; right: 1rem;
z-index: 5;
background: rgba(13,10,28,0.55);
backdrop-filter: blur(8px);
-webkit-backdrop-filter: blur(8px);
border: 1px solid rgba(231,233,255,0.08);
padding: 0.4rem;
border-radius: 2px;
pointer-events: none;
}
#minimap { display: block; border-radius: 2px; }

/* ===== DEATH OVERLAY ===== */
.death {
position: fixed; inset: 0;
background: rgba(7,6,13,0.85);
backdrop-filter: blur(8px);
-webkit-backdrop-filter: blur(8px);
display: flex; flex-direction: column;
align-items: center; justify-content: center;
z-index: 20;
opacity: 0;
pointer-events: none;
transition: opacity 0.4s;
}
.death.show { opacity: 1; pointer-events: auto; }
.death-title {
font-family: ‘Major Mono Display’, monospace;
font-size: clamp(2.5rem, 7vw, 5rem);
color: var(–magenta);
letter-spacing: 0.15em;
margin-bottom: 1rem;
text-shadow: 0 0 40px rgba(255,62,170,0.4);
}
.death-stats {
color: var(–dim);
font-size: 0.9rem;
letter-spacing: 0.2em;
margin-bottom: 0.5rem;
text-transform: uppercase;
}
.death-score {
font-family: ‘Major Mono Display’, monospace;
color: var(–acid);
font-size: 3rem;
margin-bottom: 2rem;
}
.death-btn {
background: transparent;
color: var(–ink);
border: 1px solid var(–ink);
padding: 0.8rem 1.6rem;
font-family: inherit;
font-size: 0.75rem;
letter-spacing: 0.25em;
text-transform: uppercase;
cursor: pointer;
border-radius: 2px;
transition: all 0.15s;
margin: 0 0.3rem 0.5rem;
}
.death-btn:hover {
background: var(–ink);
color: var(–bg);
}
.death-btn.primary {
border-color: var(–acid);
color: var(–acid);
}
.death-btn.primary:hover {
background: var(–acid);
color: #0a0a0a;
}
.death-earned {
color: var(–gold);
font-size: 0.8rem;
letter-spacing: 0.2em;
margin-bottom: 1.5rem;
text-transform: uppercase;
}
.death-earned span {
color: var(–gold);
font-family: ‘Major Mono Display’, monospace;
font-size: 1.3rem;
margin-left: 0.5rem;
}
.death-row {
display: flex;
flex-wrap: wrap;
justify-content: center;
gap: 0.3rem;
max-width: 90vw;
}

/* ===== UPGRADES / SHOP PANELS ===== */
.menu-btn-row { display: flex; gap: 0.5rem; margin-top: 0.6rem; }
.menu-btn {
flex: 1;
background: rgba(0,0,0,0.4);
border: 1px solid rgba(231,233,255,0.15);
color: var(–ink);
padding: 0.7rem;
font-family: inherit;
font-size: 0.7rem;
letter-spacing: 0.2em;
text-transform: uppercase;
cursor: pointer;
border-radius: 2px;
transition: all 0.15s;
}
.menu-btn:hover { border-color: var(–cyan); color: var(–cyan); }
.score-pill {
margin-top: 1rem;
text-align: center;
font-family: ‘Space Mono’, monospace;
font-size: 0.65rem;
letter-spacing: 0.25em;
color: var(–dim);
text-transform: uppercase;
}
.score-pill span {
display: inline-block;
margin-left: 0.4rem;
font-family: ‘Major Mono Display’, monospace;
font-size: 1rem;
color: var(–gold);
letter-spacing: 0.05em;
}

.shop-overlay {
position: fixed; inset: 0;
background:
radial-gradient(ellipse at 30% 20%, rgba(255,216,77,0.12), transparent 50%),
radial-gradient(ellipse at 80% 80%, rgba(46,224,255,0.12), transparent 50%),
linear-gradient(180deg, #07060d 0%, #0d0a1c 100%);
z-index: 15;
display: none;
flex-direction: column;
align-items: center;
justify-content: flex-start;
padding: 1.5rem 1rem;
overflow-y: auto;
}
.shop-overlay.show { display: flex; }
.shop-header {
display: flex;
justify-content: space-between;
align-items: center;
width: min(640px, 95vw);
margin-bottom: 1.2rem;
gap: 1rem;
}
.shop-title {
font-family: ‘Major Mono Display’, monospace;
font-size: clamp(1.5rem, 5vw, 2.4rem);
letter-spacing: 0.15em;
background: linear-gradient(90deg, var(–gold), var(–magenta));
-webkit-background-clip: text;
background-clip: text;
color: transparent;
}
.shop-score {
font-family: ‘Major Mono Display’, monospace;
color: var(–gold);
font-size: 1rem;
letter-spacing: 0.1em;
background: rgba(255,216,77,0.08);
border: 1px solid var(–gold);
padding: 0.4rem 0.8rem;
border-radius: 2px;
white-space: nowrap;
}
.shop-score-label {
color: var(–dim);
font-size: 0.55rem;
letter-spacing: 0.25em;
text-transform: uppercase;
display: block;
font-family: ‘Space Mono’, monospace;
margin-bottom: 0.15rem;
}
.upgrade-grid {
width: min(640px, 95vw);
display: grid;
grid-template-columns: 1fr;
gap: 0.6rem;
margin-bottom: 1.2rem;
}
.upgrade-card {
background: rgba(20,17,40,0.6);
backdrop-filter: blur(12px);
-webkit-backdrop-filter: blur(12px);
border: 1px solid rgba(231,233,255,0.08);
padding: 0.9rem;
border-radius: 3px;
display: flex;
justify-content: space-between;
align-items: center;
gap: 0.8rem;
transition: border-color 0.2s;
}
.upgrade-card:hover { border-color: rgba(46,224,255,0.3); }
.upgrade-info { flex: 1; min-width: 0; }
.upgrade-name {
font-size: 0.8rem;
letter-spacing: 0.2em;
text-transform: uppercase;
color: var(–acid);
margin-bottom: 0.25rem;
}
.upgrade-desc {
font-size: 0.65rem;
color: var(–dim);
letter-spacing: 0.05em;
line-height: 1.4;
}
.upgrade-level {
font-family: ‘Major Mono Display’, monospace;
font-size: 0.7rem;
color: var(–cyan);
letter-spacing: 0.1em;
margin-top: 0.25rem;
}
.upgrade-buy {
background: rgba(184,255,46,0.1);
border: 1px solid var(–acid);
color: var(–acid);
padding: 0.6rem 0.8rem;
font-family: inherit;
font-size: 0.65rem;
letter-spacing: 0.12em;
text-transform: uppercase;
cursor: pointer;
border-radius: 2px;
white-space: nowrap;
transition: all 0.15s;
min-width: 80px;
}
.upgrade-buy:hover:not(:disabled) {
background: var(–acid);
color: #0a0a0a;
box-shadow: 0 0 20px rgba(184,255,46,0.4);
}
.upgrade-buy:disabled {
opacity: 0.4;
cursor: not-allowed;
border-color: var(–dim);
color: var(–dim);
background: transparent;
}
.upgrade-buy.maxed {
border-color: var(–magenta);
color: var(–magenta);
background: rgba(255,62,170,0.08);
cursor: default;
opacity: 1;
}

.skins-grid {
width: min(640px, 95vw);
display: grid;
grid-template-columns: repeat(auto-fill, minmax(105px, 1fr));
gap: 0.55rem;
margin-bottom: 1.2rem;
}
.skin-card {
background: rgba(20,17,40,0.6);
border: 1px solid rgba(231,233,255,0.08);
padding: 0.7rem 0.4rem;
border-radius: 3px;
text-align: center;
cursor: pointer;
transition: all 0.15s;
position: relative;
}
.skin-card:hover { border-color: rgba(46,224,255,0.4); transform: translateY(-1px); }
.skin-card.locked { opacity: 0.6; }
.skin-card.selected {
border-color: var(–acid);
box-shadow: 0 0 18px rgba(184,255,46,0.3);
background: rgba(184,255,46,0.06);
}
.skin-preview {
width: 100%;
height: 32px;
border-radius: 16px;
margin-bottom: 0.4rem;
position: relative;
overflow: hidden;
box-shadow: inset 0 0 10px rgba(0,0,0,0.4);
}
.skin-name {
font-size: 0.6rem;
letter-spacing: 0.15em;
text-transform: uppercase;
color: var(–ink);
}
.skin-cost {
font-size: 0.6rem;
color: var(–gold);
margin-top: 0.2rem;
font-family: ‘Major Mono Display’, monospace;
}
.skin-card.selected .skin-cost { color: var(–acid); }
.skin-card.selected .skin-cost::before { content: ‘EQUIPPED’; }
.skin-card.selected .skin-cost-value { display: none; }
.shop-back {
width: min(640px, 95vw);
background: transparent;
border: 1px solid var(–ink);
color: var(–ink);
padding: 0.8rem;
font-family: inherit;
font-size: 0.75rem;
letter-spacing: 0.3em;
text-transform: uppercase;
cursor: pointer;
border-radius: 2px;
margin-bottom: 1rem;
}
.shop-back:hover { background: var(–ink); color: var(–bg); }
.shop-section-title {
width: min(640px, 95vw);
font-family: ‘Major Mono Display’, monospace;
font-size: 0.8rem;
letter-spacing: 0.3em;
color: var(–dim);
text-transform: uppercase;
margin-bottom: 0.6rem;
border-bottom: 1px solid rgba(231,233,255,0.08);
padding-bottom: 0.4rem;
}

/* ===== BOOST INDICATOR ===== */
.boost-bar {
position: fixed;
bottom: 1rem; left: 1rem;
z-index: 5;
background: rgba(13,10,28,0.55);
backdrop-filter: blur(8px);
-webkit-backdrop-filter: blur(8px);
border: 1px solid rgba(231,233,255,0.08);
padding: 0.5rem 0.8rem;
border-radius: 2px;
pointer-events: none;
font-size: 0.7rem;
letter-spacing: 0.2em;
color: var(–dim);
text-transform: uppercase;
}
.boost-bar.active { color: var(–gold); border-color: var(–gold); }

/* ===== MOBILE JOYSTICK ===== */
.joystick {
position: fixed;
left: 4vw;
bottom: 5vh;
width: 140px;
height: 140px;
z-index: 6;
display: none;
touch-action: none;
-webkit-user-select: none;
user-select: none;
}
.joystick.visible { display: block; }
.joystick-base {
position: absolute;
inset: 0;
border-radius: 50%;
background: rgba(13,10,28,0.45);
backdrop-filter: blur(8px);
-webkit-backdrop-filter: blur(8px);
border: 1px solid rgba(231,233,255,0.15);
box-shadow: 0 0 30px rgba(46,224,255,0.08);
}
.joystick-knob {
position: absolute;
left: 50%;
top: 50%;
width: 60px;
height: 60px;
margin-left: -30px;
margin-top: -30px;
border-radius: 50%;
background: radial-gradient(circle at 35% 30%, rgba(184,255,46,0.95), rgba(46,224,255,0.7));
border: 1px solid rgba(255,255,255,0.3);
box-shadow: 0 0 20px rgba(184,255,46,0.5);
transition: transform 0.05s ease-out;
pointer-events: none;
}
.boost-btn {
position: fixed;
right: 5vw;
bottom: 7vh;
width: 100px;
height: 100px;
border-radius: 50%;
background: rgba(255,216,77,0.15);
backdrop-filter: blur(8px);
-webkit-backdrop-filter: blur(8px);
border: 1px solid var(–gold);
color: var(–gold);
font-family: ‘Major Mono Display’, monospace;
font-size: 0.85rem;
letter-spacing: 0.2em;
z-index: 6;
display: none;
touch-action: none;
-webkit-user-select: none;
user-select: none;
cursor: pointer;
box-shadow: 0 0 20px rgba(255,216,77,0.2);
}
.boost-btn.visible { display: block; }
.boost-btn.active {
background: rgba(255,216,77,0.35);
box-shadow: 0 0 35px rgba(255,216,77,0.6);
transform: scale(0.96);
}

/* On true mobile, push HUD/minimap a bit to leave room for joystick */
@media (max-width: 768px), (pointer: coarse) {
.hud-value { font-size: 1.1rem; }
.hud-card { padding: 0.4rem 0.7rem; }
.leaderboard { min-width: 140px; padding: 0.5rem 0.7rem; font-size: 0.7rem; }
.lb-row { font-size: 0.7rem; }
.minimap-wrap { display: none !important; }
.boost-bar { display: none !important; }
}
</style>

</head>
<body>

<canvas id="game"></canvas>

<!-- HUD -->

<div class="hud" id="hud" style="display:none">
  <div class="hud-card">
    <span class="hud-label">Length</span>
    <span class="hud-value" id="lengthVal">10</span>
  </div>
  <div class="hud-card">
    <span class="hud-label">Rank</span>
    <span class="hud-value" id="rankVal" style="color:var(--cyan)">—</span>
  </div>
</div>

<div class="leaderboard" id="lbBox" style="display:none">
  <div class="lb-title">Leaderboard</div>
  <div id="lbList"></div>
</div>

<div class="minimap-wrap" id="mmWrap" style="display:none">
  <canvas id="minimap" width="140" height="140"></canvas>
</div>

<div class="boost-bar" id="boostBar" style="display:none">
  <kbd style="background:rgba(231,233,255,0.08);border:1px solid rgba(231,233,255,0.15);padding:1px 6px;border-radius:2px;color:var(--ink);font-family:inherit">SPACE</kbd> /
  <kbd style="background:rgba(231,233,255,0.08);border:1px solid rgba(231,233,255,0.15);padding:1px 6px;border-radius:2px;color:var(--ink);font-family:inherit">CLICK</kbd>
  to boost
</div>

<!-- MOBILE JOYSTICK -->

<div class="joystick" id="joystick">
  <div class="joystick-base"></div>
  <div class="joystick-knob" id="joystickKnob"></div>
</div>
<button class="boost-btn" id="boostBtn">BOOST</button>

<!-- MENU -->

<div class="menu" id="menu">
  <h1 class="title">SLITHER<br>ARENA</h1>
  <div class="subtitle">— eat · grow · cut off ·  survive —</div>
  <div class="panel">
    <div class="field">
      <label>Your call sign</label>
      <input id="nameInput" type="text" maxlength="14" placeholder="anonymous worm" value="" />
    </div>
    <div class="field">
      <label>Bot difficulty</label>
      <div class="diff-row">
        <button class="diff-btn active" data-d="easy">Easy</button>
        <button class="diff-btn" data-d="medium">Medium</button>
        <button class="diff-btn" data-d="hard">Hard</button>
      </div>
    </div>
    <button class="play-btn" id="playBtn">Enter Arena</button>
    <div class="menu-btn-row">
      <button class="menu-btn" id="upgradesBtn">Upgrades</button>
      <button class="menu-btn" id="shopBtn">Skins</button>
    </div>
    <div class="score-pill">
      Score Bank <span id="menuScoreDisplay">0</span>
    </div>
    <label class="sound-toggle">
      <input type="checkbox" id="soundToggle" checked />
      <span>Sound FX</span>
    </label>
    <div class="hint" id="hintText">
      Move with the <kbd>MOUSE</kbd>. Hold <kbd>SPACE</kbd> or <kbd>CLICK</kbd> to boost.<br>
      Boosting burns length. Cut snakes off — they crash, you feast.
    </div>
  </div>
</div>

<!-- UPGRADES OVERLAY -->

<div class="shop-overlay" id="upgradesOverlay">
  <div class="shop-header">
    <div class="shop-title">UPGRADES</div>
    <div>
      <span class="shop-score-label">Score</span>
      <span class="shop-score" id="upgradesScore">0</span>
    </div>
  </div>
  <div class="shop-section-title">Permanent boosts</div>
  <div class="upgrade-grid" id="upgradeGrid"></div>
  <button class="shop-back" id="upgradesBack">← Back to Menu</button>
</div>

<!-- SKINS SHOP OVERLAY -->

<div class="shop-overlay" id="shopOverlay">
  <div class="shop-header">
    <div class="shop-title">SKINS</div>
    <div>
      <span class="shop-score-label">Score</span>
      <span class="shop-score" id="shopScore">0</span>
    </div>
  </div>
  <div class="shop-section-title">Tap to equip · locked skins cost score</div>
  <div class="skins-grid" id="skinsGrid"></div>
  <button class="shop-back" id="shopBack">← Back to Menu</button>
</div>

<!-- DEATH -->

<div class="death" id="death">
  <div class="death-title">YOU DIED</div>
  <div class="death-stats">Final Length</div>
  <div class="death-score" id="finalScore">0</div>
  <div class="death-earned">Score earned <span id="scoreEarned">+0</span></div>
  <div class="death-row">
    <button class="death-btn primary" id="respawnBtn">Try again</button>
    <button class="death-btn" id="deathMenuBtn">Menu</button>
    <button class="death-btn" id="deathShopBtn">Shop</button>
  </div>
</div>

<script>
// ============================================================
// SLITHER ARENA — single-file canvas snake.io clone
// ============================================================

const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
const mmCanvas = document.getElementById('minimap');
const mmCtx = mmCanvas.getContext('2d');

// Detect touch device
const IS_TOUCH = ('ontouchstart' in window) || (navigator.maxTouchPoints > 0);

let W = 0, H = 0;
// Render at 1x (or capped DPR) — supersampling at full devicePixelRatio on retina/mobile
// was the #1 cause of low FPS. Capping to 1 trades a touch of crispness for huge FPS gains.
const RENDER_DPR = Math.min(devicePixelRatio || 1, 1);
function resize() {
  W = canvas.width = Math.floor(window.innerWidth * RENDER_DPR);
  H = canvas.height = Math.floor(window.innerHeight * RENDER_DPR);
  canvas.style.width = window.innerWidth + 'px';
  canvas.style.height = window.innerHeight + 'px';
  ctx.setTransform(RENDER_DPR, 0, 0, RENDER_DPR, 0, 0);
}
window.addEventListener('resize', resize);
resize();

// ---------- World ----------
const WORLD_R = 2800;             // arena radius (a touch bigger to fit chonky bots)
const FOOD_COUNT = 480;
const BOT_COUNT = 14;
const SEG_SPACING = 6;            // distance between segments
const BASE_SPEED = 2.2;
const BOOST_SPEED = 4.0;
const TURN_RATE = 0.09;           // radians per frame for player
const FOOD_BASE_RADIUS = 4;

// Bot start-length range (per user request: 1..1500)
const BOT_MIN_LEN = 1;
const BOT_MAX_LEN = 1500;

const NAMES = [
  'viper','noodle','asp','glitch','fang','coil','hiss','slick',
  'venom','cobra','python','mamba','grimm','shadow','luna','zero',
  'pixel','rune','blaze','frost','echo','rogue','sable','ember'
];

const SKIN_PALETTES = [
  ['#ff3eaa','#ff8ad1'], ['#2ee0ff','#7ff3ff'], ['#b8ff2e','#dfff8a'],
  ['#ffd84d','#ffe999'], ['#a06bff','#cda8ff'], ['#ff6b3e','#ffaa86'],
  ['#3effa9','#a6ffd7'], ['#ff3e3e','#ff8a8a'], ['#3e7bff','#86a8ff']
];

let difficulty = 'easy';
let playerName = '';
let snakes = [];   // includes player at index 0
let foods = [];
let camera = { x: 0, y: 0 };
let mouse = { x: 0, y: 0 };
let keys = {};
let mouseDown = false;
let gameRunning = false;
let frame = 0;
let bgGradient = null; // cached so we don't rebuild a radial gradient every frame

// ============================================================
// PERSISTENT PROGRESSION — score bank, upgrades, skins
// Saved to localStorage so they survive deaths and reloads.
// ============================================================
const SAVE_KEY = 'slither_arena_save_v1';

const UPGRADE_DEFS = [
  {
    id: 'speed',
    name: 'Base Speed',
    desc: 'Move faster at all times. +5% per level.',
    maxLevel: 8,
    baseCost: 3,
    costGrowth: 1.6,
    effect: (lvl) => 1 + lvl * 0.05
  },
  {
    id: 'sprintSpeed',
    name: 'Sprint Speed',
    desc: 'Boost goes faster. +6% per level.',
    maxLevel: 8,
    baseCost: 4,
    costGrowth: 1.6,
    effect: (lvl) => 1 + lvl * 0.06
  },
  {
    id: 'startLength',
    name: 'Starting Length',
    desc: 'Spawn longer. +4 segments per level.',
    maxLevel: 10,
    baseCost: 2,
    costGrowth: 1.55,
    effect: (lvl) => lvl * 4
  },
  {
    id: 'scoreMult',
    name: 'Score Multiplier',
    desc: 'Earn more score from your length. +20% per level.',
    maxLevel: 10,
    baseCost: 5,
    costGrowth: 1.7,
    effect: (lvl) => 1 + lvl * 0.20
  },
  {
    id: 'lengthMult',
    name: 'Length Multiplier',
    desc: 'Food grows you more. +10% per level.',
    maxLevel: 10,
    baseCost: 5,
    costGrowth: 1.7,
    effect: (lvl) => 1 + lvl * 0.10
  }
];

// Skins: id, name, palette, cost (0 = free / default unlocked)
const SKINS = [
  { id: 'acid',     name: 'Acid',     palette: ['#b8ff2e','#dfff8a'], cost: 0 },
  { id: 'magenta',  name: 'Magenta',  palette: ['#ff3eaa','#ff8ad1'], cost: 5 },
  { id: 'cyan',     name: 'Cyan',     palette: ['#2ee0ff','#7ff3ff'], cost: 5 },
  { id: 'gold',     name: 'Gold',     palette: ['#ffd84d','#ffe999'], cost: 8 },
  { id: 'violet',   name: 'Violet',   palette: ['#a06bff','#cda8ff'], cost: 8 },
  { id: 'ember',    name: 'Ember',    palette: ['#ff6b3e','#ffaa86'], cost: 10 },
  { id: 'mint',     name: 'Mint',     palette: ['#3effa9','#a6ffd7'], cost: 10 },
  { id: 'crimson',  name: 'Crimson',  palette: ['#ff3e3e','#ff8a8a'], cost: 15 },
  { id: 'azure',    name: 'Azure',    palette: ['#3e7bff','#86a8ff'], cost: 15 },
  { id: 'void',     name: 'Void',     palette: ['#1a1030','#5a4a8a'], cost: 25 },
  { id: 'sunset',   name: 'Sunset',   palette: ['#ff3eaa','#ffd84d'], cost: 35 },
  { id: 'plasma',   name: 'Plasma',   palette: ['#b8ff2e','#ff3eaa'], cost: 50 }
];

const DEFAULT_SAVE = {
  score: 0,
  upgrades: { speed: 0, sprintSpeed: 0, startLength: 0, scoreMult: 0, lengthMult: 0 },
  unlockedSkins: ['acid'],
  equippedSkin: 'acid'
};

let save = loadSave();

function loadSave() {
  try {
    const raw = localStorage.getItem(SAVE_KEY);
    if (!raw) return { ...DEFAULT_SAVE, upgrades: { ...DEFAULT_SAVE.upgrades }, unlockedSkins: [...DEFAULT_SAVE.unlockedSkins] };
    const parsed = JSON.parse(raw);
    // Merge so newly-added fields get defaults
    return {
      score: parsed.score ?? 0,
      upgrades: { ...DEFAULT_SAVE.upgrades, ...(parsed.upgrades || {}) },
      unlockedSkins: parsed.unlockedSkins?.length ? parsed.unlockedSkins : [...DEFAULT_SAVE.unlockedSkins],
      equippedSkin: parsed.equippedSkin || 'acid'
    };
  } catch (e) {
    return { ...DEFAULT_SAVE, upgrades: { ...DEFAULT_SAVE.upgrades }, unlockedSkins: [...DEFAULT_SAVE.unlockedSkins] };
  }
}
function persistSave() {
  try { localStorage.setItem(SAVE_KEY, JSON.stringify(save)); } catch (e) {}
}
function upgradeCost(def, currentLevel) {
  return Math.ceil(def.baseCost * Math.pow(def.costGrowth, currentLevel));
}
function getEquippedPalette() {
  const sk = SKINS.find(s => s.id === save.equippedSkin) || SKINS[0];
  return sk.palette;
}

// Mobile input state
const joystick = {
  active: false,
  dir: 0,           // angle in radians
  magnitude: 0,     // 0..1
  origin: { x: 0, y: 0 },
  pointerId: null
};
let boostHeld = false; // from boost button on mobile, or space/click on desktop

// ============================================================
// AUDIO — synthesized SFX via WebAudio (no external files)
// ============================================================
const SFX = {
  ctx: null,
  enabled: true,
  master: null,
  init() {
    if (this.ctx) return;
    try {
      const AC = window.AudioContext || window.webkitAudioContext;
      this.ctx = new AC();
      this.master = this.ctx.createGain();
      this.master.gain.value = 0.4;
      this.master.connect(this.ctx.destination);
    } catch (e) {
      this.enabled = false;
    }
  },
  resume() {
    if (this.ctx && this.ctx.state === 'suspended') this.ctx.resume();
  },
  // Quick blip — for eating
  eat() {
    if (!this.enabled || !this.ctx) return;
    const t = this.ctx.currentTime;
    const osc = this.ctx.createOscillator();
    const gain = this.ctx.createGain();
    osc.type = 'square';
    osc.frequency.setValueAtTime(660, t);
    osc.frequency.exponentialRampToValueAtTime(1320, t + 0.08);
    gain.gain.setValueAtTime(0.0001, t);
    gain.gain.exponentialRampToValueAtTime(0.18, t + 0.005);
    gain.gain.exponentialRampToValueAtTime(0.0001, t + 0.1);
    osc.connect(gain).connect(this.master);
    osc.start(t);
    osc.stop(t + 0.12);
  },
  // Bigger chomp — for big-value pellets (from kills)
  bigEat() {
    if (!this.enabled || !this.ctx) return;
    const t = this.ctx.currentTime;
    const osc = this.ctx.createOscillator();
    const gain = this.ctx.createGain();
    osc.type = 'sawtooth';
    osc.frequency.setValueAtTime(220, t);
    osc.frequency.exponentialRampToValueAtTime(880, t + 0.12);
    gain.gain.setValueAtTime(0.0001, t);
    gain.gain.exponentialRampToValueAtTime(0.22, t + 0.01);
    gain.gain.exponentialRampToValueAtTime(0.0001, t + 0.18);
    osc.connect(gain).connect(this.master);
    osc.start(t);
    osc.stop(t + 0.2);
  },
  // Looping boost hum — call boostStart / boostStop
  boostNode: null,
  boostGain: null,
  boostStart() {
    if (!this.enabled || !this.ctx || this.boostNode) return;
    const t = this.ctx.currentTime;
    const osc = this.ctx.createOscillator();
    const lfo = this.ctx.createOscillator();
    const lfoGain = this.ctx.createGain();
    const gain = this.ctx.createGain();
    osc.type = 'sawtooth';
    osc.frequency.value = 90;
    lfo.frequency.value = 18;
    lfoGain.gain.value = 14;
    lfo.connect(lfoGain).connect(osc.frequency);
    gain.gain.setValueAtTime(0.0001, t);
    gain.gain.exponentialRampToValueAtTime(0.09, t + 0.05);
    osc.connect(gain).connect(this.master);
    osc.start(t);
    lfo.start(t);
    this.boostNode = osc;
    this.boostLfo = lfo;
    this.boostGain = gain;
  },
  boostStop() {
    if (!this.boostNode || !this.ctx) return;
    const t = this.ctx.currentTime;
    this.boostGain.gain.cancelScheduledValues(t);
    this.boostGain.gain.setValueAtTime(this.boostGain.gain.value, t);
    this.boostGain.gain.exponentialRampToValueAtTime(0.0001, t + 0.08);
    this.boostNode.stop(t + 0.1);
    this.boostLfo.stop(t + 0.1);
    this.boostNode = null;
    this.boostLfo = null;
    this.boostGain = null;
  },
  // Death — descending noise + thump
  death() {
    if (!this.enabled || !this.ctx) return;
    const t = this.ctx.currentTime;
    // tonal whoosh down
    const osc = this.ctx.createOscillator();
    const g = this.ctx.createGain();
    osc.type = 'sawtooth';
    osc.frequency.setValueAtTime(440, t);
    osc.frequency.exponentialRampToValueAtTime(55, t + 0.7);
    g.gain.setValueAtTime(0.0001, t);
    g.gain.exponentialRampToValueAtTime(0.25, t + 0.02);
    g.gain.exponentialRampToValueAtTime(0.0001, t + 0.8);
    osc.connect(g).connect(this.master);
    osc.start(t);
    osc.stop(t + 0.85);
    // noise burst
    const bufSize = this.ctx.sampleRate * 0.5;
    const buf = this.ctx.createBuffer(1, bufSize, this.ctx.sampleRate);
    const data = buf.getChannelData(0);
    for (let i = 0; i < bufSize; i++) data[i] = (Math.random() * 2 - 1) * (1 - i / bufSize);
    const noise = this.ctx.createBufferSource();
    const ng = this.ctx.createGain();
    const filter = this.ctx.createBiquadFilter();
    filter.type = 'lowpass';
    filter.frequency.setValueAtTime(1200, t);
    filter.frequency.exponentialRampToValueAtTime(120, t + 0.5);
    ng.gain.value = 0.3;
    noise.buffer = buf;
    noise.connect(filter).connect(ng).connect(this.master);
    noise.start(t);
  },
  // Kill confirm — when YOU kill a bot
  kill() {
    if (!this.enabled || !this.ctx) return;
    const t = this.ctx.currentTime;
    for (let i = 0; i < 3; i++) {
      const osc = this.ctx.createOscillator();
      const g = this.ctx.createGain();
      osc.type = 'triangle';
      const f = 440 + i * 220;
      osc.frequency.setValueAtTime(f, t + i * 0.05);
      g.gain.setValueAtTime(0.0001, t + i * 0.05);
      g.gain.exponentialRampToValueAtTime(0.18, t + i * 0.05 + 0.01);
      g.gain.exponentialRampToValueAtTime(0.0001, t + i * 0.05 + 0.12);
      osc.connect(g).connect(this.master);
      osc.start(t + i * 0.05);
      osc.stop(t + i * 0.05 + 0.15);
    }
  }
};

// ---------- Helpers ----------
function rand(a, b) { return a + Math.random() * (b - a); }
function randInt(a, b) { return Math.floor(rand(a, b + 1)); }
function dist2(ax, ay, bx, by) { const dx = ax-bx, dy = ay-by; return dx*dx+dy*dy; }
function angleDiff(a, b) {
  let d = b - a;
  while (d > Math.PI) d -= Math.PI*2;
  while (d < -Math.PI) d += Math.PI*2;
  return d;
}

// Random bot starting length — biased toward shorter, but with a decent spread
// across the new 1..1500 range. Most bots small/medium, a few sizable.
function randomBotLength() {
  const t = Math.random();
  const skewed = Math.pow(t, 1.9); // milder skew than before (range is smaller now)
  return Math.max(BOT_MIN_LEN, Math.floor(BOT_MIN_LEN + skewed * (BOT_MAX_LEN - BOT_MIN_LEN)));
}

// Hard ceiling on total food, including kill-drops and boost trails.
// Without this, long sessions with lots of bot-on-bot kills slowly leak food.
const FOOD_HARD_CAP = FOOD_COUNT + 100;

// ---------- Food ----------
function spawnFood(x, y, value=1, color=null) {
  if (foods.length >= FOOD_HARD_CAP) return;
  if (x === undefined) {
    const a = Math.random() * Math.PI * 2;
    const r = Math.sqrt(Math.random()) * (WORLD_R - 30);
    x = Math.cos(a) * r;
    y = Math.sin(a) * r;
  }
  const palette = SKIN_PALETTES[randInt(0, SKIN_PALETTES.length-1)];
  foods.push({
    x, y, value,
    color: color || palette[0],
    glow: palette[1],
    pulse: Math.random() * Math.PI * 2,
    r: FOOD_BASE_RADIUS + Math.min(value * 0.4, 6)
  });
}

function initFood() {
  foods = [];
  for (let i = 0; i < FOOD_COUNT; i++) spawnFood();
}

// ---------- Snake ----------
// Returns a random world position at least `minDistFromPlayer` away from the player's head
// (and not directly in front of the player), with a fallback after several attempts.
function safeSpawnPos(minDistFromPlayer = 600) {
  const player = snakes[0];
  for (let tries = 0; tries < 30; tries++) {
    const a = Math.random() * Math.PI * 2;
    const r = Math.sqrt(Math.random()) * (WORLD_R * 0.7);
    const x = Math.cos(a) * r;
    const y = Math.sin(a) * r;
    if (!player || !player.alive) return { x, y };
    const ph = player.segments[0];
    const dx = x - ph.x, dy = y - ph.y;
    const d = Math.sqrt(dx*dx + dy*dy);
    if (d < minDistFromPlayer) continue;
    // Also avoid spawning directly in front of the player's facing direction
    const angToSpawn = Math.atan2(dy, dx);
    const facing = Math.abs(angleDiff(player.dir, angToSpawn));
    if (d < minDistFromPlayer * 1.6 && facing < 0.7) continue;
    return { x, y };
  }
  // Fallback: opposite side of the arena from the player
  if (player && player.alive) {
    const ph = player.segments[0];
    const away = Math.atan2(-ph.y, -ph.x);
    return { x: Math.cos(away) * WORLD_R * 0.6, y: Math.sin(away) * WORLD_R * 0.6 };
  }
  return { x: 0, y: 0 };
}

function createSnake(opts) {
  let x, y;
  if (opts.x !== undefined && opts.y !== undefined) {
    x = opts.x; y = opts.y;
  } else {
    const pos = safeSpawnPos(opts.minDistFromPlayer ?? 600);
    x = pos.x; y = pos.y;
  }
  const dir = Math.random() * Math.PI * 2;
  const palette = opts.palette || SKIN_PALETTES[randInt(0, SKIN_PALETTES.length-1)];
  const startLen = opts.startLen || 14;
  const segs = [];
  for (let i = 0; i < startLen; i++) {
    segs.push({ x: x - Math.cos(dir)*i*SEG_SPACING, y: y - Math.sin(dir)*i*SEG_SPACING });
  }
  const s = {
    isPlayer: !!opts.isPlayer,
    name: opts.name || NAMES[randInt(0, NAMES.length-1)],
    segments: segs,
    dir,
    targetDir: dir,
    speed: BASE_SPEED,
    boosting: false,
    alive: true,
    palette,
    length: startLen,
    growth: 0,
    boostFuel: 0,
    radius: 8,
    turnRate: opts.turnRate || 0.07,
    aiState: { mode: 'wander', target: null, timer: 0, aggression: 0, foresight: 0 }
  };
  setSnakeRadius(s);
  return s;
}

function setSnakeRadius(s) {
  // Grows slowly with length. Cap so 5000-length giants don't fill the screen.
  s.radius = 7 + Math.min(s.length * 0.06, 22);
}

// ---------- AI ----------
function configureAI(s, diff) {
  if (diff === 'easy') {
    s.turnRate = 0.05;
    s.aiState.aggression = 0.05;
    s.aiState.foresight = 35;       // how far ahead the bot "sees" its own trajectory
    s.aiState.lookAhead = 90;       // food sight radius
    s.aiState.reactNoise = 0.35;    // jitter on steering (higher = sloppier)
    s.aiState.smartCutoff = false;
    s.aiState.predictFrames = 0;    // doesn't anticipate enemy motion
    s.aiState.probes = 1;           // only one forward danger probe
    s.aiState.lateralProbes = false;
    s.aiState.fleeBoost = false;
    s.aiState.dangerR = 60;
  } else if (diff === 'medium') {
    s.turnRate = 0.075;
    s.aiState.aggression = 0.35;
    s.aiState.foresight = 75;
    s.aiState.lookAhead = 240;
    s.aiState.reactNoise = 0.12;
    s.aiState.smartCutoff = false;
    s.aiState.predictFrames = 15;   // anticipates a bit ahead of enemy heads
    s.aiState.probes = 2;
    s.aiState.lateralProbes = true; // checks for hazards off to the sides
    s.aiState.fleeBoost = true;
    s.aiState.dangerR = 90;
  } else {
    s.turnRate = 0.12;
    s.aiState.aggression = 0.8;
    s.aiState.foresight = 150;
    s.aiState.lookAhead = 480;
    s.aiState.reactNoise = 0.02;
    s.aiState.smartCutoff = true;
    s.aiState.predictFrames = 35;   // strong lookahead — actually intercepts
    s.aiState.probes = 3;
    s.aiState.lateralProbes = true;
    s.aiState.fleeBoost = true;
    s.aiState.dangerR = 130;
  }
}

function aiThink(s) {
  const head = s.segments[0];
  const ai = s.aiState;

  // ---- Build probe points along the bot's projected path & off to the sides ----
  // Each probe checks: how close is the nearest other-snake segment (with prediction)
  // and is there an arena wall there. The most threatening probe drives a "flee" angle.
  const baseAhead = ai.foresight + s.radius * 1.5;
  const probes = [];
  // Forward probes at increasing distances
  for (let k = 1; k <= ai.probes; k++) {
    const d = baseAhead * k / ai.probes;
    probes.push({ x: head.x + Math.cos(s.dir) * d, y: head.y + Math.sin(s.dir) * d, ang: s.dir, weight: 1.0 });
  }
  // Slight lateral probes — catches threats that aren't directly ahead
  if (ai.lateralProbes) {
    const sideD = baseAhead * 0.7;
    const sideOff = 0.5; // radians ≈ 28°
    probes.push({ x: head.x + Math.cos(s.dir + sideOff) * sideD, y: head.y + Math.sin(s.dir + sideOff) * sideD, ang: s.dir + sideOff, weight: 0.7 });
    probes.push({ x: head.x + Math.cos(s.dir - sideOff) * sideD, y: head.y + Math.sin(s.dir - sideOff) * sideD, ang: s.dir - sideOff, weight: 0.7 });
  }

  let dangerAngle = null;
  let dangerWeight = 0;
  const dangerRsq = ai.dangerR * ai.dangerR;

  // Arena wall check (use furthest forward probe)
  {
    const p = probes[ai.probes - 1];
    const distFromCenter = Math.sqrt(p.x*p.x + p.y*p.y);
    if (distFromCenter > WORLD_R - 50) {
      dangerAngle = Math.atan2(-head.y, -head.x);
      dangerWeight = 1.1; // wall override anything
    }
  }

  // Check each probe against every other snake (with motion prediction)
  for (const other of snakes) {
    if (!other.alive || other === s) continue;

    // Early-out: if 'other' is too far for ANY probe to be threatened, skip it.
    // Use the furthest probe (the head itself plus baseAhead) as the reference.
    const oh = other.segments[0];
    const otherReach = other.segments.length * SEG_SPACING + ai.dangerR + 20;
    const farDx = head.x - oh.x, farDy = head.y - oh.y;
    const farLimit = otherReach + baseAhead;
    if (farDx*farDx + farDy*farDy > farLimit*farLimit) continue;

    // Predict where this enemy will be in `predictFrames` frames.
    // We don't predict each segment — we shift the *sample point* check by an offset
    // along the enemy's facing. This is a cheap approximation that makes hard bots
    // actually anticipate head-on collisions instead of grazing into oncoming heads.
    const predAdvance = ai.predictFrames * (other.boosting ? BOOST_SPEED : BASE_SPEED);
    const predOx = Math.cos(other.dir) * predAdvance;
    const predOy = Math.sin(other.dir) * predAdvance;

    // For huge snakes, sample sparser for perf
    const step = Math.max(3, Math.floor(other.segments.length / 50));
    for (let i = 0; i < other.segments.length; i += step) {
      const seg = other.segments[i];
      // The head (i==0) gets prediction; tail segments roughly stay put as the body trails
      const sx = i === 0 ? seg.x + predOx : seg.x;
      const sy = i === 0 ? seg.y + predOy : seg.y;
      for (const p of probes) {
        const d2 = dist2(p.x, p.y, sx, sy);
        if (d2 < dangerRsq) {
          const w = (1 - Math.sqrt(d2) / ai.dangerR) * p.weight;
          if (w > dangerWeight) {
            dangerWeight = w;
            // Flee directly away from the threat
            dangerAngle = Math.atan2(head.y - sy, head.x - sx);
          }
        }
      }
    }
  }

  // ---- Food seeking (only when not in immediate danger) ----
  let bestFood = null;
  if (dangerWeight < 0.5) {
    let bestScore = Infinity;
    const sightR2 = ai.lookAhead * ai.lookAhead;
    for (const f of foods) {
      const d2 = dist2(head.x, head.y, f.x, f.y);
      if (d2 > sightR2) continue;
      const score = d2 / (1 + f.value * 0.5);
      if (score < bestScore) {
        bestScore = score;
        bestFood = f;
      }
    }
  }

  // ---- Hard-AI smart cutoff: intercept (lead-aim) a smaller nearby snake ----
  let cutoffAngle = null;
  if (ai.smartCutoff && s.length > 25 && dangerWeight < 0.4) {
    let bestTarget = null;
    let bestDist = Infinity;
    for (const other of snakes) {
      if (!other.alive || other === s) continue;
      const oh = other.segments[0];
      const d = Math.sqrt(dist2(head.x, head.y, oh.x, oh.y));
      // Only intercept if we're at least 90% of their size — don't suicide on giants
      if (d < 380 && d < bestDist && other.length < s.length * 1.15) {
        bestTarget = other;
        bestDist = d;
      }
    }
    if (bestTarget) {
      const oh = bestTarget.segments[0];
      // Lead-aim: project target forward proportional to closing distance
      const leadFactor = Math.min(140, bestDist * 0.6);
      const px = oh.x + Math.cos(bestTarget.dir) * leadFactor;
      const py = oh.y + Math.sin(bestTarget.dir) * leadFactor;
      cutoffAngle = Math.atan2(py - head.y, px - head.x);
    }
  }

  // ---- Decide ----
  let desired;
  let wantBoost = false;
  if (dangerWeight > 0.3) {
    desired = dangerAngle;
    // Boost-flee when seriously threatened and big enough to spare a segment
    if (ai.fleeBoost && dangerWeight > 0.75 && s.length > 25) wantBoost = true;
  } else if (cutoffAngle !== null && Math.random() < ai.aggression) {
    desired = cutoffAngle;
    wantBoost = s.length > 30 && Math.random() < 0.35;
  } else if (bestFood) {
    desired = Math.atan2(bestFood.y - head.y, bestFood.x - head.x);
  } else {
    if (ai.timer <= 0) {
      ai.wanderDir = s.dir + rand(-1, 1);
      ai.timer = randInt(60, 180);
    }
    desired = ai.wanderDir || s.dir;
    ai.timer--;
  }
  s.boosting = wantBoost;

  desired += rand(-ai.reactNoise, ai.reactNoise);
  s.targetDir = desired;
}

// ---------- Update ----------
function updateSnake(s) {
  if (!s.alive) return;

  const diff = angleDiff(s.dir, s.targetDir);
  const turn = s.isPlayer ? TURN_RATE : s.turnRate;
  if (Math.abs(diff) < turn) s.dir = s.targetDir;
  else s.dir += Math.sign(diff) * turn;

  let speed = BASE_SPEED;
  let boostSpeed = BOOST_SPEED;
  if (s.isPlayer) {
    speed = BASE_SPEED * UPGRADE_DEFS.find(u => u.id === 'speed').effect(save.upgrades.speed);
    boostSpeed = BOOST_SPEED * UPGRADE_DEFS.find(u => u.id === 'sprintSpeed').effect(save.upgrades.sprintSpeed);
  }
  if (s.boosting && s.length > 10) {
    speed = boostSpeed;
    s.boostFuel += 0.04;
    if (s.boostFuel >= 1) {
      s.boostFuel = 0;
      if (s.length > 10) {
        s.length--;
        const tail = s.segments[s.segments.length - 1];
        spawnFood(tail.x + rand(-6,6), tail.y + rand(-6,6), 1, s.palette[0]);
        s.segments.pop();
      }
    }
  } else {
    s.boostFuel = 0;
  }

  const nx = s.segments[0].x + Math.cos(s.dir) * speed;
  const ny = s.segments[0].y + Math.sin(s.dir) * speed;

  s.segments.unshift({ x: nx, y: ny });

  const want = s.length + (s.growth > 0 ? 1 : 0);
  if (s.growth > 0) s.growth--;
  while (s.segments.length > want) s.segments.pop();

  for (let i = 1; i < s.segments.length; i++) {
    const p = s.segments[i-1];
    const c = s.segments[i];
    const dx = c.x - p.x, dy = c.y - p.y;
    const d = Math.sqrt(dx*dx + dy*dy);
    if (d > SEG_SPACING) {
      const t = SEG_SPACING / d;
      c.x = p.x + dx * t;
      c.y = p.y + dy * t;
    }
  }

  setSnakeRadius(s);

  const head = s.segments[0];
  if (head.x*head.x + head.y*head.y > WORLD_R*WORLD_R) {
    killSnake(s);
  }
}

function killSnake(s, killer) {
  if (!s.alive) return;
  s.alive = false;

  // Hard cap on food drops to prevent the foods array from ballooning past
  // FOOD_COUNT after killing giant bots — that was a major frame-rate killer
  // (every collision check, AI scan, and draw iterates foods).
  const headroom = Math.max(0, (FOOD_COUNT + 60) - foods.length);
  const maxDrops = Math.min(80, headroom); // never drop more than 80 from one death
  const totalSegs = s.segments.length;
  // Distribute the drops evenly along the body, regardless of body length.
  const dropEvery = maxDrops > 0 ? Math.max(1, Math.floor(totalSegs / maxDrops)) : totalSegs + 1;
  const valueScale = 2 + Math.min(s.length / 500, 6);

  let dropped = 0;
  for (let i = 0; i < totalSegs && dropped < maxDrops; i += dropEvery) {
    const seg = s.segments[i];
    const offX = rand(-8, 8);
    const offY = rand(-8, 8);
    spawnFood(seg.x + offX, seg.y + offY, valueScale + Math.random()*2, s.palette[0]);
    dropped++;
  }

  if (!s.isPlayer) {
    // Player got the kill?
    if (killer && killer.isPlayer) SFX.kill();
    setTimeout(() => {
      const idx = snakes.indexOf(s);
      if (idx >= 0) {
        // Mid-game respawns: NOT randomized sizes. Always small, and spawned
        // well away from the player so they can't appear on top of you.
        const newBot = createSnake({
          startLen: 14,
          minDistFromPlayer: 900
        });
        newBot.name = NAMES[randInt(0, NAMES.length-1)];
        configureAI(newBot, difficulty);
        snakes[idx] = newBot;
      }
    }, 1500);
  } else {
    SFX.death();
    onPlayerDeath();
  }
}

// ---------- Collision ----------
function checkCollisions() {
  // food eating
  const lenMult = UPGRADE_DEFS.find(u => u.id === 'lengthMult').effect(save.upgrades.lengthMult);
  for (const s of snakes) {
    if (!s.alive) continue;
    const h = s.segments[0];
    const hr2 = (s.radius + 8) ** 2;
    for (let i = foods.length - 1; i >= 0; i--) {
      const f = foods[i];
      if (dist2(h.x, h.y, f.x, f.y) < hr2) {
        // Player gets the length-multiplier bonus; bots use raw value.
        const gain = s.isPlayer ? Math.max(1, Math.round(f.value * lenMult)) : f.value;
        s.length += gain;
        s.growth += gain;
        foods.splice(i, 1);
        if (s.isPlayer) {
          if (f.value >= 2) SFX.bigEat();
          else SFX.eat();
        }
        if (foods.length < FOOD_COUNT) spawnFood();
      }
    }
  }

  // head vs other snakes' bodies
  for (const s of snakes) {
    if (!s.alive) continue;
    const h = s.segments[0];
    for (const other of snakes) {
      if (!other.alive) continue;
      if (other === s) continue;
      // Early-out: if 'other' can't possibly reach 's' head, skip its segment loop.
      // An other-snake's body extends at most segments.length * SEG_SPACING from its head.
      const oh = other.segments[0];
      const headDx = h.x - oh.x, headDy = h.y - oh.y;
      const reach = other.segments.length * SEG_SPACING + s.radius + other.radius + 4;
      if (headDx*headDx + headDy*headDy > reach*reach) continue;

      const start = 2;
      // For huge snakes, sample sparser body collisions to keep frame budget.
      // Stepped up from /400 to /200 — gameplay difference is negligible.
      const step = Math.max(1, Math.floor(other.segments.length / 200));
      for (let i = start; i < other.segments.length; i += step) {
        const seg = other.segments[i];
        const r = s.radius + other.radius * 0.7;
        if (dist2(h.x, h.y, seg.x, seg.y) < r*r) {
          killSnake(s, other);
          break;
        }
      }
      if (!s.alive) break;
    }
  }
}

// ---------- Render ----------
function worldToScreen(x, y) {
  return {
    x: (x - camera.x) + window.innerWidth / 2,
    y: (y - camera.y) + window.innerHeight / 2
  };
}

function drawGrid() {
  const cell = 80;
  const offX = -camera.x % cell;
  const offY = -camera.y % cell;
  ctx.strokeStyle = 'rgba(95,98,150,0.08)';
  ctx.lineWidth = 1;
  ctx.beginPath();
  for (let x = (offX + window.innerWidth/2) % cell - cell; x < window.innerWidth + cell; x += cell) {
    ctx.moveTo(x, 0); ctx.lineTo(x, window.innerHeight);
  }
  for (let y = (offY + window.innerHeight/2) % cell - cell; y < window.innerHeight + cell; y += cell) {
    ctx.moveTo(0, y); ctx.lineTo(window.innerWidth, y);
  }
  ctx.stroke();
}

let arenaGradient = null;
function drawArena() {
  const c = worldToScreen(0, 0);
  ctx.save();
  if (!arenaGradient) {
    // Pre-build once. We position the gradient relative to (0,0) world; we'll translate.
    arenaGradient = ctx.createRadialGradient(0, 0, WORLD_R * 0.85, 0, 0, WORLD_R + 200);
    arenaGradient.addColorStop(0, 'rgba(255,62,170,0)');
    arenaGradient.addColorStop(0.5, 'rgba(255,62,170,0.08)');
    arenaGradient.addColorStop(1, 'rgba(255,62,170,0)');
  }
  ctx.translate(c.x, c.y);
  ctx.beginPath();
  ctx.arc(0, 0, WORLD_R, 0, Math.PI*2);
  ctx.strokeStyle = arenaGradient;
  ctx.lineWidth = 200;
  ctx.stroke();

  ctx.beginPath();
  ctx.arc(0, 0, WORLD_R, 0, Math.PI*2);
  ctx.strokeStyle = 'rgba(255,62,170,0.5)';
  ctx.lineWidth = 2;
  ctx.shadowColor = 'rgba(255,62,170,0.8)';
  ctx.shadowBlur = 20;
  ctx.stroke();
  ctx.restore();
}

function drawFood() {
  // Cheap food rendering: solid disc + a translucent halo, no per-food radial gradients.
  // Pulse phase advances on a global counter instead of per-food to skip work.
  const minX = camera.x - window.innerWidth/2 - 30;
  const maxX = camera.x + window.innerWidth/2 + 30;
  const minY = camera.y - window.innerHeight/2 - 30;
  const maxY = camera.y + window.innerHeight/2 + 30;
  const pulseBase = Math.sin(frame * 0.05);
  ctx.save();
  ctx.globalCompositeOperation = 'lighter'; // additive glow without gradients
  for (const f of foods) {
    if (f.x < minX || f.x > maxX || f.y < minY || f.y > maxY) continue;
    const s = worldToScreen(f.x, f.y);
    const pulse = 1 + (pulseBase * 0.5 + Math.sin(f.pulse) * 0.5) * 0.15;
    const r = f.r * pulse;
    // soft halo (one fillStyle, no gradient)
    ctx.fillStyle = f.color;
    ctx.globalAlpha = 0.35;
    ctx.beginPath();
    ctx.arc(s.x, s.y, r * 2, 0, Math.PI*2);
    ctx.fill();
    // bright core
    ctx.globalAlpha = 1;
    ctx.fillStyle = f.glow;
    ctx.beginPath();
    ctx.arc(s.x, s.y, r * 0.9, 0, Math.PI*2);
    ctx.fill();
  }
  ctx.restore();
}

function drawSnake(snake) {
  if (!snake.alive) return;
  const segs = snake.segments;
  const [c1, c2] = snake.palette;

  ctx.lineCap = 'round';
  ctx.lineJoin = 'round';

  // For huge snakes, draw only the segments near the camera to keep things fast
  const viewMargin = 200;
  const minX = camera.x - window.innerWidth/2 - viewMargin;
  const maxX = camera.x + window.innerWidth/2 + viewMargin;
  const minY = camera.y - window.innerHeight/2 - viewMargin;
  const maxY = camera.y + window.innerHeight/2 + viewMargin;

  // Build visible chain (we still need contiguous runs to keep the stroke continuous)
  ctx.beginPath();
  let started = false;
  for (let i = segs.length - 1; i >= 0; i--) {
    const seg = segs[i];
    const inside = seg.x >= minX && seg.x <= maxX && seg.y >= minY && seg.y <= maxY;
    if (!inside) {
      // close current run if we were drawing
      started = false;
      continue;
    }
    const s = worldToScreen(seg.x, seg.y);
    if (!started) { ctx.moveTo(s.x, s.y); started = true; }
    else ctx.lineTo(s.x, s.y);
  }
  // Heavy shadowBlur on long stroked paths is the most expensive draw call in the game.
  // Use a strong glow only for the player; bots get a cheap inner highlight only.
  if (snake.isPlayer) {
    ctx.shadowColor = c1;
    ctx.shadowBlur = snake.boosting ? 18 : 8;
  } else {
    ctx.shadowBlur = 0;
  }
  ctx.strokeStyle = c1;
  ctx.lineWidth = snake.radius * 2;
  ctx.stroke();

  ctx.shadowBlur = 0;
  ctx.strokeStyle = c2;
  ctx.lineWidth = snake.radius * 0.9;
  ctx.stroke();

  // Head
  const head = worldToScreen(segs[0].x, segs[0].y);
  // Skip drawing if head is way offscreen
  if (head.x < -100 || head.x > window.innerWidth + 100 || head.y < -100 || head.y > window.innerHeight + 100) return;

  ctx.fillStyle = c2;
  ctx.beginPath();
  ctx.arc(head.x, head.y, snake.radius, 0, Math.PI*2);
  ctx.fill();

  const eyeOff = snake.radius * 0.5;
  const eyeR = snake.radius * 0.35;
  const perp = snake.dir + Math.PI/2;
  const ex1 = head.x + Math.cos(snake.dir)*snake.radius*0.3 + Math.cos(perp)*eyeOff;
  const ey1 = head.y + Math.sin(snake.dir)*snake.radius*0.3 + Math.sin(perp)*eyeOff;
  const ex2 = head.x + Math.cos(snake.dir)*snake.radius*0.3 - Math.cos(perp)*eyeOff;
  const ey2 = head.y + Math.sin(snake.dir)*snake.radius*0.3 - Math.sin(perp)*eyeOff;
  ctx.fillStyle = '#fff';
  ctx.beginPath(); ctx.arc(ex1, ey1, eyeR, 0, Math.PI*2); ctx.fill();
  ctx.beginPath(); ctx.arc(ex2, ey2, eyeR, 0, Math.PI*2); ctx.fill();
  ctx.fillStyle = '#0a0a0a';
  ctx.beginPath(); ctx.arc(ex1 + Math.cos(snake.dir)*eyeR*0.4, ey1 + Math.sin(snake.dir)*eyeR*0.4, eyeR*0.55, 0, Math.PI*2); ctx.fill();
  ctx.beginPath(); ctx.arc(ex2 + Math.cos(snake.dir)*eyeR*0.4, ey2 + Math.sin(snake.dir)*eyeR*0.4, eyeR*0.55, 0, Math.PI*2); ctx.fill();

  if (!snake.isPlayer) {
    ctx.fillStyle = 'rgba(255,255,255,0.6)';
    ctx.font = '11px "Space Mono", monospace';
    ctx.textAlign = 'center';
    ctx.fillText(snake.name + ' · ' + snake.length, head.x, head.y - snake.radius - 8);
  }
}

function drawMinimap() {
  const size = 140;
  mmCtx.clearRect(0, 0, size, size);
  mmCtx.fillStyle = 'rgba(46,224,255,0.05)';
  mmCtx.beginPath();
  mmCtx.arc(size/2, size/2, size/2 - 2, 0, Math.PI*2);
  mmCtx.fill();
  mmCtx.strokeStyle = 'rgba(255,62,170,0.4)';
  mmCtx.lineWidth = 1;
  mmCtx.stroke();

  const scale = (size/2 - 4) / WORLD_R;
  for (const s of snakes) {
    if (!s.alive) continue;
    const h = s.segments[0];
    const mx = size/2 + h.x * scale;
    const my = size/2 + h.y * scale;
    mmCtx.fillStyle = s.isPlayer ? '#b8ff2e' : s.palette[0];
    mmCtx.beginPath();
    // Bigger snakes show up bigger on minimap
    const dotR = s.isPlayer ? 3 : Math.min(1.6 + s.length / 800, 4);
    mmCtx.arc(mx, my, dotR, 0, Math.PI*2);
    mmCtx.fill();
  }
}

// ---------- Game loop ----------
// Fixed-timestep accumulator: game logic always runs at TARGET_FPS regardless
// of the display's refresh rate or browser throttling. On mobile especially,
// requestAnimationFrame is often capped at 30 Hz, which makes everything feel
// half-speed. By stepping the simulation in fixed slices we decouple it from rAF.
const TARGET_FPS = 60;
const FIXED_DT_MS = 1000 / TARGET_FPS;
const MAX_FRAME_MS = 250; // cap to avoid spiral-of-death after a tab pause
let _lastTickTime = 0;
let _accumulator = 0;

function stepSimulation() {
  // One fixed step of game logic (was the body of tick() before).
  frame++;

  const player = snakes[0];
  if (player && player.alive) {
    if (IS_TOUCH && joystick.active) {
      player.targetDir = joystick.dir;
    } else {
      const sw = window.innerWidth, sh = window.innerHeight;
      const dx = mouse.x - sw/2;
      const dy = mouse.y - sh/2;
      if (Math.abs(dx) + Math.abs(dy) > 4) {
        player.targetDir = Math.atan2(dy, dx);
      }
    }
    const wantBoost = (keys[' '] || mouseDown || boostHeld) && player.length > 10;
    if (wantBoost && !player.boosting) SFX.boostStart();
    if (!wantBoost && player.boosting) SFX.boostStop();
    player.boosting = wantBoost;
  }

  for (let i = 1; i < snakes.length; i++) {
    // Throttle AI: each bot thinks on alternating frames (offset by index).
    if (snakes[i].alive && ((frame + i) & 1) === 0) aiThink(snakes[i]);
  }

  for (const s of snakes) updateSnake(s);
  checkCollisions();

  if (player && player.alive) {
    camera.x += (player.segments[0].x - camera.x) * 0.15;
    camera.y += (player.segments[0].y - camera.y) * 0.15;
  }
}

function renderFrame() {
  ctx.clearRect(0, 0, window.innerWidth, window.innerHeight);
  if (!bgGradient || bgGradient._w !== window.innerWidth || bgGradient._h !== window.innerHeight) {
    bgGradient = ctx.createRadialGradient(
      window.innerWidth/2, window.innerHeight/2, 100,
      window.innerWidth/2, window.innerHeight/2, Math.max(window.innerWidth, window.innerHeight)
    );
    bgGradient.addColorStop(0, '#0d0a1c');
    bgGradient.addColorStop(1, '#07060d');
    bgGradient._w = window.innerWidth;
    bgGradient._h = window.innerHeight;
  }
  ctx.fillStyle = bgGradient;
  ctx.fillRect(0, 0, window.innerWidth, window.innerHeight);

  drawGrid();
  drawArena();
  drawFood();

  for (let i = 1; i < snakes.length; i++) drawSnake(snakes[i]);
  if (snakes[0]) drawSnake(snakes[0]);

  const player = snakes[0];
  if (player) {
    if (!renderFrame._lengthEl) {
      renderFrame._lengthEl = document.getElementById('lengthVal');
      renderFrame._rankEl = document.getElementById('rankVal');
      renderFrame._lbList = document.getElementById('lbList');
      renderFrame._boostBar = document.getElementById('boostBar');
    }
    renderFrame._lengthEl.textContent = player.length;

    if ((frame % 6) === 0) {
      const ranked = [];
      for (const s of snakes) if (s.alive) ranked.push(s);
      ranked.sort((a, b) => b.length - a.length);
      const rank = ranked.indexOf(player);
      renderFrame._rankEl.textContent = player.alive ? '#' + (rank + 1) : '—';
      const lbList = renderFrame._lbList;
      lbList.innerHTML = '';
      const top = ranked.length > 8 ? ranked.slice(0, 8) : ranked;
      for (let i = 0; i < top.length; i++) {
        const s = top[i];
        const div = document.createElement('div');
        div.className = 'lb-row' + (s.isPlayer ? ' me' : '');
        div.innerHTML = `<span class="rank">${i+1}.</span><span class="name">${s.name}</span><span class="score">${s.length}</span>`;
        lbList.appendChild(div);
      }
    }
    renderFrame._boostBar.classList.toggle('active', player.boosting);
  }

  if (frame % 3 === 0) drawMinimap();
}

function tick(now) {
  if (!gameRunning) return;
  if (!_lastTickTime) _lastTickTime = now;
  let delta = now - _lastTickTime;
  _lastTickTime = now;
  if (delta > MAX_FRAME_MS) delta = MAX_FRAME_MS;
  _accumulator += delta;

  // Run as many fixed steps as needed to catch up to real time.
  // Cap iterations so a single rAF call can't run away (e.g. 5 steps max = 83ms of sim).
  let steps = 0;
  while (_accumulator >= FIXED_DT_MS && steps < 5) {
    stepSimulation();
    _accumulator -= FIXED_DT_MS;
    steps++;
  }
  // Drop leftover if we hit the step cap (prevents spiral after slow frames)
  if (steps >= 5) _accumulator = 0;

  renderFrame();
  requestAnimationFrame(tick);
}

// ---------- Player death ----------
function onPlayerDeath() {
  const finalLen = snakes[0].length;
  document.getElementById('finalScore').textContent = finalLen;

  // Award score: 1 per 5 length, times score multiplier upgrade.
  const scoreMult = UPGRADE_DEFS.find(u => u.id === 'scoreMult').effect(save.upgrades.scoreMult);
  const earned = Math.floor((finalLen / 5) * scoreMult);
  save.score += earned;
  persistSave();
  document.getElementById('scoreEarned').textContent = '+' + earned;

  document.getElementById('death').classList.add('show');
  SFX.boostStop();
  boostHeld = false;
  // CRITICAL: stop the simulation. Previously the tick loop kept running with all
  // 14 bots, food, collisions, and DOM leaderboard rebuilds happening behind the
  // death overlay — that's what made lag "increase when you die".
  gameRunning = false;
}

// ---------- Start / Restart ----------
function startGame() {
  document.getElementById('menu').classList.add('hidden');
  document.getElementById('death').classList.remove('show');
  document.getElementById('hud').style.display = 'block';
  document.getElementById('lbBox').style.display = 'block';
  document.getElementById('mmWrap').style.display = 'block';
  document.getElementById('boostBar').style.display = 'block';

  // Mobile controls only on touch devices
  if (IS_TOUCH) {
    document.getElementById('joystick').classList.add('visible');
    document.getElementById('boostBtn').classList.add('visible');
  }

  // Init / resume audio (browsers require a user gesture)
  SFX.init();
  SFX.resume();

  initFood();
  snakes = [];

  // Apply persistent upgrades
  const startLenBonus = UPGRADE_DEFS.find(u => u.id === 'startLength').effect(save.upgrades.startLength);
  const skinPalette = getEquippedPalette();

  const player = createSnake({
    isPlayer: true,
    name: playerName || 'you',
    palette: skinPalette,
    x: 0, y: 0,
    startLen: 16 + startLenBonus
  });
  snakes.push(player);

  for (let i = 0; i < BOT_COUNT; i++) {
    // Initial bots: random length in 1..1500 (per user request), with linear distribution
    // but spawned far from the player so a giant doesn't appear next to you.
    const bot = createSnake({
      startLen: randomBotLength(),
      minDistFromPlayer: 800
    });
    configureAI(bot, difficulty);
    snakes.push(bot);
  }

  camera.x = 0; camera.y = 0;
  gameRunning = true;
  _lastTickTime = 0;
  _accumulator = 0;
  requestAnimationFrame(tick);
}

// ---------- Input: Mouse / Keyboard ----------
canvas.addEventListener('mousemove', e => {
  mouse.x = e.clientX;
  mouse.y = e.clientY;
});
canvas.addEventListener('mousedown', () => mouseDown = true);
canvas.addEventListener('mouseup',   () => mouseDown = false);
window.addEventListener('keydown', e => { keys[e.key] = true; });
window.addEventListener('keyup',   e => { keys[e.key] = false; });

// ---------- Input: Touch / Joystick ----------
const joystickEl = document.getElementById('joystick');
const knobEl = document.getElementById('joystickKnob');
const boostBtnEl = document.getElementById('boostBtn');

const JOY_RADIUS = 50; // max knob travel from center

function setKnob(dx, dy) {
  knobEl.style.transform = `translate(${dx}px, ${dy}px)`;
}

function joystickStart(e) {
  e.preventDefault();
  const touch = e.changedTouches ? e.changedTouches[0] : e;
  const rect = joystickEl.getBoundingClientRect();
  joystick.origin.x = rect.left + rect.width / 2;
  joystick.origin.y = rect.top + rect.height / 2;
  joystick.active = true;
  joystick.pointerId = touch.identifier !== undefined ? touch.identifier : 'mouse';
  updateJoystick(touch.clientX, touch.clientY);
}

function joystickMove(e) {
  if (!joystick.active) return;
  let touch = null;
  if (e.changedTouches) {
    for (let i = 0; i < e.changedTouches.length; i++) {
      if (e.changedTouches[i].identifier === joystick.pointerId) {
        touch = e.changedTouches[i]; break;
      }
    }
  } else {
    touch = e;
  }
  if (!touch) return;
  e.preventDefault();
  updateJoystick(touch.clientX, touch.clientY);
}

function joystickEnd(e) {
  if (!joystick.active) return;
  if (e.changedTouches) {
    let matched = false;
    for (let i = 0; i < e.changedTouches.length; i++) {
      if (e.changedTouches[i].identifier === joystick.pointerId) { matched = true; break; }
    }
    if (!matched) return;
  }
  joystick.active = false;
  joystick.magnitude = 0;
  joystick.pointerId = null;
  setKnob(0, 0);
}

function updateJoystick(cx, cy) {
  let dx = cx - joystick.origin.x;
  let dy = cy - joystick.origin.y;
  const dist = Math.sqrt(dx*dx + dy*dy);
  if (dist > JOY_RADIUS) {
    dx = dx * JOY_RADIUS / dist;
    dy = dy * JOY_RADIUS / dist;
  }
  setKnob(dx, dy);
  if (dist > 4) {
    joystick.dir = Math.atan2(dy, dx);
    joystick.magnitude = Math.min(1, dist / JOY_RADIUS);
  } else {
    joystick.magnitude = 0;
  }
}

joystickEl.addEventListener('touchstart', joystickStart, { passive: false });
joystickEl.addEventListener('touchmove',  joystickMove,  { passive: false });
joystickEl.addEventListener('touchend',   joystickEnd,   { passive: false });
joystickEl.addEventListener('touchcancel', joystickEnd,  { passive: false });

// Boost button
function boostStart(e) {
  e.preventDefault();
  boostHeld = true;
  boostBtnEl.classList.add('active');
}
function boostEnd(e) {
  e.preventDefault();
  boostHeld = false;
  boostBtnEl.classList.remove('active');
}
boostBtnEl.addEventListener('touchstart', boostStart, { passive: false });
boostBtnEl.addEventListener('touchend',   boostEnd,   { passive: false });
boostBtnEl.addEventListener('touchcancel', boostEnd,  { passive: false });
// Also let it work as a click for desktop testing
boostBtnEl.addEventListener('mousedown', boostStart);
boostBtnEl.addEventListener('mouseup',   boostEnd);
boostBtnEl.addEventListener('mouseleave', boostEnd);

// Prevent the canvas from stealing touch input while joystick is the input source.
// We still allow touch on the canvas for non-mobile-control fallback, but only when joystick isn't engaged.
canvas.addEventListener('touchmove', e => {
  if (joystick.active) return;
  if (e.touches[0]) { mouse.x = e.touches[0].clientX; mouse.y = e.touches[0].clientY; }
  e.preventDefault();
}, { passive: false });
canvas.addEventListener('touchstart', e => {
  if (joystick.active) return;
  if (e.touches[0]) { mouse.x = e.touches[0].clientX; mouse.y = e.touches[0].clientY; }
}, { passive: false });

// ---------- Menu wiring ----------
document.querySelectorAll('.diff-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.diff-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    difficulty = btn.dataset.d;
  });
});
document.getElementById('playBtn').addEventListener('click', () => {
  playerName = document.getElementById('nameInput').value.trim() || 'you';
  startGame();
});
document.getElementById('respawnBtn').addEventListener('click', () => {
  startGame();
});

// ---------- Death overlay: menu + shop buttons ----------
function returnToMenu() {
  gameRunning = false;
  document.getElementById('death').classList.remove('show');
  document.getElementById('hud').style.display = 'none';
  document.getElementById('lbBox').style.display = 'none';
  document.getElementById('mmWrap').style.display = 'none';
  document.getElementById('boostBar').style.display = 'none';
  document.getElementById('joystick').classList.remove('visible');
  document.getElementById('boostBtn').classList.remove('visible');
  document.getElementById('menu').classList.remove('hidden');
  SFX.boostStop();
  boostHeld = false;
  refreshMenuScore();
}
document.getElementById('deathMenuBtn').addEventListener('click', returnToMenu);
document.getElementById('deathShopBtn').addEventListener('click', () => {
  returnToMenu();
  openShop();
});

// ---------- Menu score display ----------
function refreshMenuScore() {
  document.getElementById('menuScoreDisplay').textContent = save.score;
}
refreshMenuScore();

// ---------- Upgrades panel ----------
function openUpgrades() {
  renderUpgrades();
  document.getElementById('upgradesOverlay').classList.add('show');
  document.getElementById('menu').classList.add('hidden');
}
function closeUpgrades() {
  document.getElementById('upgradesOverlay').classList.remove('show');
  document.getElementById('menu').classList.remove('hidden');
  refreshMenuScore();
}
function renderUpgrades() {
  document.getElementById('upgradesScore').textContent = save.score;
  const grid = document.getElementById('upgradeGrid');
  grid.innerHTML = '';
  for (const def of UPGRADE_DEFS) {
    const lvl = save.upgrades[def.id] || 0;
    const maxed = lvl >= def.maxLevel;
    const cost = maxed ? 0 : upgradeCost(def, lvl);
    const canAfford = save.score >= cost;

    const card = document.createElement('div');
    card.className = 'upgrade-card';
    card.innerHTML = `
      <div class="upgrade-info">
        <div class="upgrade-name">${def.name}</div>
        <div class="upgrade-desc">${def.desc}</div>
        <div class="upgrade-level">Lv ${lvl} / ${def.maxLevel}</div>
      </div>
      <button class="upgrade-buy ${maxed ? 'maxed' : ''}" ${(!canAfford || maxed) ? 'disabled' : ''}>
        ${maxed ? 'MAXED' : 'Buy · ' + cost}
      </button>
    `;
    if (!maxed) {
      card.querySelector('.upgrade-buy').addEventListener('click', () => buyUpgrade(def.id));
    }
    grid.appendChild(card);
  }
}
function buyUpgrade(id) {
  const def = UPGRADE_DEFS.find(u => u.id === id);
  const lvl = save.upgrades[id] || 0;
  if (lvl >= def.maxLevel) return;
  const cost = upgradeCost(def, lvl);
  if (save.score < cost) return;
  save.score -= cost;
  save.upgrades[id] = lvl + 1;
  persistSave();
  SFX.eat();
  renderUpgrades();
}
document.getElementById('upgradesBtn').addEventListener('click', openUpgrades);
document.getElementById('upgradesBack').addEventListener('click', closeUpgrades);

// ---------- Skins shop ----------
function openShop() {
  renderShop();
  document.getElementById('shopOverlay').classList.add('show');
  document.getElementById('menu').classList.add('hidden');
}
function closeShop() {
  document.getElementById('shopOverlay').classList.remove('show');
  document.getElementById('menu').classList.remove('hidden');
  refreshMenuScore();
}
function renderShop() {
  document.getElementById('shopScore').textContent = save.score;
  const grid = document.getElementById('skinsGrid');
  grid.innerHTML = '';
  for (const skin of SKINS) {
    const unlocked = save.unlockedSkins.includes(skin.id);
    const equipped = save.equippedSkin === skin.id;
    const card = document.createElement('div');
    card.className = 'skin-card' + (equipped ? ' selected' : '') + (!unlocked ? ' locked' : '');
    const grad = `linear-gradient(135deg, ${skin.palette[0]}, ${skin.palette[1]})`;
    card.innerHTML = `
      <div class="skin-preview" style="background:${grad}"></div>
      <div class="skin-name">${skin.name}</div>
      <div class="skin-cost"><span class="skin-cost-value">${unlocked ? (equipped ? '' : 'OWNED') : (skin.cost + ' SCORE')}</span></div>
    `;
    card.addEventListener('click', () => onSkinTap(skin.id));
    grid.appendChild(card);
  }
}
function onSkinTap(id) {
  const skin = SKINS.find(s => s.id === id);
  if (!skin) return;
  const unlocked = save.unlockedSkins.includes(id);
  if (unlocked) {
    save.equippedSkin = id;
    persistSave();
    SFX.eat();
    renderShop();
  } else {
    if (save.score >= skin.cost) {
      save.score -= skin.cost;
      save.unlockedSkins.push(id);
      save.equippedSkin = id;
      persistSave();
      SFX.bigEat();
      renderShop();
    }
    // Can't afford: silently ignored (button is greyed via .locked styling).
  }
}
document.getElementById('shopBtn').addEventListener('click', openShop);
document.getElementById('shopBack').addEventListener('click', closeShop);

// Sound toggle
document.getElementById('soundToggle').addEventListener('change', (e) => {
  SFX.enabled = e.target.checked;
  if (!SFX.enabled) SFX.boostStop();
});

// Update hint text on mobile
if (IS_TOUCH) {
  document.getElementById('hintText').innerHTML =
    'Drag the <strong style="color:var(--acid)">JOYSTICK</strong> to steer. Tap & hold <strong style="color:var(--gold)">BOOST</strong> to dash.<br>Boosting burns length. Cut snakes off to feast.';
}

document.getElementById('nameInput').addEventListener('keydown', e => {
  if (e.key === 'Enter') document.getElementById('playBtn').click();
});

</script>

</body>
</html>
