<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Offline Arcade — TicTacToe & Snake</title>
<style>
  :root{--bg:#0f1724;--card:#0b1220;--accent:#22c55e;--muted:#9aa6b2;color-scheme:dark}
  html,body{height:100%;margin:0;font-family:Inter,system-ui,Segoe UI,Roboto,"Helvetica Neue",Arial;background:linear-gradient(180deg,#071129 0%, #071727 100%);color:#e6f0f2}
  .wrap{max-width:980px;margin:32px auto;padding:20px;}
  header{display:flex;align-items:center;gap:16px;margin-bottom:18px}
  h1{font-size:20px;margin:0}
  nav{margin-left:auto;display:flex;gap:8px}
  button{background:transparent;border:1px solid rgba(255,255,255,0.06);color:inherit;padding:8px 12px;border-radius:8px;cursor:pointer}
  .card{background:rgba(255,255,255,0.02);padding:18px;border-radius:12px;box-shadow:0 6px 18px rgba(2,6,23,0.6);min-height:300px}
  .hidden{display:none}
  .row{display:flex;gap:16px;flex-wrap:wrap}
  .panel{flex:1;min-width:260px}
  /* TicTacToe */
  .ttt-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:8px;width:320px;height:320px;margin:auto}
  .cell{background:rgba(255,255,255,0.02);display:flex;align-items:center;justify-content:center;font-size:64px;cursor:pointer;border-radius:8px}
  .info{text-align:center;margin-top:12px;color:var(--muted)}
  .controls{display:flex;gap:8px;justify-content:center;margin-top:12px}
  /* Snake */
  canvas{background:#071222;border-radius:8px;display:block;margin:8px auto}
  .small{font-size:13px;color:var(--muted);text-align:center}
  footer{margin-top:18px;text-align:center;color:var(--muted);font-size:13px}
  .btn-accent{border-color:rgba(34,197,94,0.12);background:linear-gradient(180deg,rgba(34,197,94,0.06),transparent);color:var(--accent)}
</style>
</head>
<body>
<div class="wrap">
  <header>
    <h1>Offline Arcade — Play & Learn</h1>
    <nav>
      <button id="btn-home">Home</button>
      <button id="btn-ttt">Tic-Tac-Toe</button>
      <button id="btn-snake">Snake</button>
    </nav>
  </header>

  <div id="home" class="card">
    <h2>Welcome!</h2>
    <p class="small">This offline arcade runs locally — perfect for your USB drive. Choose a game above to start.</p>
    <div class="row" style="margin-top:18px">
      <div class="panel">
        <h3>Tic-Tac-Toe</h3>
        <p class="small">Play locally vs another person on the same computer. Click a square to place X or O.</p>
        <div style="text-align:center;margin-top:12px"><button id="play-ttt" class="btn-accent">Play Tic-Tac-Toe</button></div>
      </div>
      <div class="panel">
        <h3>Snake</h3>
        <p class="small">Classic snake. Use arrow keys (or WASD). Eat apples to grow. Avoid hitting walls or yourself.</p>
        <div style="text-align:center;margin-top:12px"><button id="play-snake" class="btn-accent">Play Snake</button></div>
      </div>
    </div>
  </div>

  <div id="ttt" class="card hidden">
    <h2>Tic-Tac-Toe</h2>
    <div class="ttt-grid" id="grid"></div>
    <div class="info" id="ttt-info">Player X's turn</div>
    <div class="controls">
      <button id="ttt-reset">Reset</button>
      <button id="ttt-home">Back Home</button>
    </div>
  </div>

  <div id="snake" class="card hidden">
    <h2>Snake</h2>
    <canvas id="snake-canvas" width="480" height="360"></canvas>
    <div class="small" id="snake-info">Score: 0 — Use ↑ ↓ ← → or W A S D to move</div>
    <div class="controls" style="margin-top:8px">
      <button id="snake-restart">Restart</button>
      <button id="snake-home">Back Home</button>
    </div>
  </div>

  <footer>Made for fun — offline only. No blocking, no proxying.</footer>
</div>

<script>
/* Navigation */
const el = id => document.getElementById(id);
const show = id => { ['home','ttt','snake'].forEach(x=>el(x).classList.add('hidden')); el(id).classList.remove('hidden'); };
el('btn-home').onclick = ()=> show('home');
el('btn-ttt').onclick = ()=> show('ttt');
el('btn-snake').onclick = ()=> show('snake');
el('play-ttt').onclick = ()=> show('ttt');
el('play-snake').onclick = ()=> show('snake');
el('ttt-home').onclick = ()=> show('home');
el('snake-home').onclick = ()=> show('home');

/* --- Tic-Tac-Toe --- */
let tBoard = Array(9).fill('');
let tTurn = 'X';
const tGrid = el('grid');
const tInfo = el('ttt-info');

function drawTTT(){
  tGrid.innerHTML='';
  for(let i=0;i<9;i++){
    const d = document.createElement('div');
    d.className='cell';
    d.textContent = tBoard[i];
    d.addEventListener('click', ()=> {
      if(tBoard[i] || checkWinner()) return;
      tBoard[i]=tTurn;
      tTurn = tTurn==='X' ? 'O' : 'X';
      updateTTT();
    });
    tGrid.appendChild(d);
  }
}
function updateTTT(){
  const w = checkWinner();
  if(w){ tInfo.textContent = w === 'draw' ? "It's a draw!" : `Player ${w} wins!`; }
  else { tInfo.textContent = `Player ${tTurn}'s turn`; }
  drawTTT();
}
function checkWinner(){
  const lines = [[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]];
  for(const [a,b,c] of lines){
    if(tBoard[a] && tBoard[a] === tBoard[b] && tBoard[a] === tBoard[c]) return tBoard[a];
  }
  if(tBoard.every(x=>x)) return 'draw';
  return null;
}
el('ttt-reset').onclick = ()=> { tBoard = Array(9).fill(''); tTurn='X'; updateTTT(); };
drawTTT();

/* --- Snake --- */
const canvas = el('snake-canvas');
const ctx = canvas.getContext('2d');
const scale = 20;
const cols = Math.floor(canvas.width / scale);
const rows = Math.floor(canvas.height / scale);

let snake;
let apple;
let gameInterval;
let speed = 120; // ms

function initSnake(){
  snake = [{x: Math.floor(cols/2), y: Math.floor(rows/2)}];
  snake.dir = {x:1,y:0};
  snake.grow = 0;
  apple = placeApple();
  el('snake-info').textContent = `Score: 0 — Use ↑ ↓ ← → or W A S D to move`;
}

function placeApple(){
  while(true){
    const p = {x: Math.floor(Math.random()*cols), y: Math.floor(Math.random()*rows)};
    if(!snake.some(s=>s.x===p.x && s.y===p.y)) return p;
  }
}

function draw(){
  // clear
  ctx.fillStyle = '#05121a';
  ctx.fillRect(0,0,canvas.width,canvas.height);

  // apple
  ctx.fillStyle = '#e11d48';
  ctx.fillRect(apple.x*scale+4, apple.y*scale+4, scale-8, scale-8);

  // snake
  ctx.fillStyle = '#22c55e';
  for(let i=0;i<snake.length;i++){
    const s = snake[i];
    ctx.fillRect(s.x*scale+1, s.y*scale+1, scale-2, scale-2);
  }
}

function step(){
  const head = {...snake[0]};
  head.x += snake.dir.x;
  head.y += snake.dir.y;

  // wall collision
  if(head.x < 0 || head.x >= cols || head.y < 0 || head.y >= rows){
    gameOver();
    return;
  }
  // self collision
  if(snake.some(seg => seg.x===head.x && seg.y===head.y)){
    gameOver();
    return;
  }

  snake.unshift(head);
  if(head.x === apple.x && head.y === apple.y){
    snake.grow += 1;
    apple = placeApple();
  }
  if(snake.grow>0){
    snake.grow--;
  } else {
    snake.pop();
  }

  el('snake-info').textContent = `Score: ${snake.length-1} — Use ↑ ↓ ← → or W A S D to move`;
  draw();
}

function gameOver(){
  clearInterval(gameInterval);
  el('snake-info').textContent = `Game over — Score: ${snake.length-1}. Press Restart.`;
}

function startSnake(){
  clearInterval(gameInterval);
  initSnake();
  draw();
  gameInterval = setInterval(step, speed);
}

el('snake-restart').onclick = startSnake;

/* Controls */
window.addEventListener('keydown', (e)=>{
  // Snake controls
  const map = {
    ArrowUp: {x:0,y:-1}, ArrowDown: {x:0,y:1}, ArrowLeft: {x:-1,y:0}, ArrowRight: {x:1,y:0},
    w: {x:0,y:-1}, s:{x:0,y:1}, a:{x:-1,y:0}, d:{x:1,y:0},
    W: {x:0,y:-1}, S:{x:0,y:1}, A:{x:-1,y:0}, D:{x:1,y:0}
  };
  if(map[e.key] && snake){
    const dir = map[e.key];
    // prevent 180-degree turn
    if(snake.dir.x + dir.x === 0 && snake.dir.y + dir.y === 0) return;
    snake.dir = dir;
  }
});

/* Start with home visible */
show('home');
startSnake(); // start snake ready to play if user goes to it
</script>
</body>
</html>
