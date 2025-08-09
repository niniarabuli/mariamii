# mariamii
birthday 
<div id="game">
  <h1>Mini Minecraft Hello Kitty Adventure</h1>
  <canvas id="gameCanvas" width="320" height="480" style="border:1px solid #000;"></canvas>
  <div id="controls">
    <button onclick="move('up')">⬆️</button><br>
    <button onclick="move('left')">⬅️</button>
    <button onclick="move('down')">⬇️</button>
    <button onclick="move('right')">➡️</button>
  </div>
  <p id="message"></p>
</div>
<div id="game">
  <h1>Mini Minecraft Hello Kitty Adventure</h1>
  <canvas id="gameCanvas" width="320" height="480" style="border:1px solid #000;"></canvas>
  <div id="controls">
    <button onclick="move('up')">⬆️</button><br>
    <button onclick="move('left')">⬅️</button>
    <button onclick="move('down')">⬇️</button>
    <button onclick="move('right')">➡️</button>
  </div>
  <p id="message"></p>
</div>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

const tileSize = 32;
const mapWidth = 10;
const mapHeight = 15;

const playerImg = new Image();
playerImg.src = 'https://i.imgur.com/8F6NQYu.png'; // Hello Kitty pixel art

const cakeImg = new Image();
cakeImg.src = 'https://i.imgur.com/cYp17yY.png'; // cake pixel art

// Simple map: 0=grass, 1=tree(obstacle), 2=water(obstacle), 3=goal (cake)
const gameMap = [
  0,0,0,0,0,0,0,0,0,0,
  0,1,0,0,0,1,0,0,0,0,
  0,0,0,2,2,0,0,1,0,0,
  0,0,1,0,0,0,0,0,0,0,
  0,0,0,0,1,0,0,0,2,0,
  0,1,0,0,0,0,1,0,0,0,
  0,0,0,1,0,0,0,0,0,0,
  0,0,0,0,0,1,0,0,1,0,
  0,2,0,0,0,0,0,0,0,0,
  0,0,1,0,0,0,2,0,0,0,
  0,0,0,0,1,0,0,0,1,0,
  0,1,0,0,0,0,0,0,0,0,
  0,0,0,1,0,0,0,1,0,0,
  0,0,0,0,0,0,0,0,0,0,
  3,0,0,0,0,0,0,0,0,0,
];

let playerPos = {x:4, y:0};
let gameWon = false;

function drawTile(type, x, y) {
  const colors = {
    0: '#228B22', // grass
    1: '#006400', // tree
    2: '#1E90FF', // water
    3: '#FFD700', // gold for cake tile background
  };
  ctx.fillStyle = colors[type];
  ctx.fillRect(x*tileSize, y*tileSize, tileSize, tileSize);

  if(type === 3) {
    ctx.drawImage(cakeImg, x*tileSize+4, y*tileSize+4, tileSize-8, tileSize-8);
  }
  if(type === 1) {
    // simple tree shape
    ctx.fillStyle = '#013220';
    ctx.fillRect(x*tileSize+10, y*tileSize+10, 12, 20);
    ctx.beginPath();
    ctx.arc(x*tileSize+16, y*tileSize+10, 15, 0, Math.PI * 2);
    ctx.fillStyle = '#014421';
    ctx.fill();
  }
  if(type === 2) {
    // water ripple
    ctx.fillStyle = '#00BFFF';
    ctx.beginPath();
    ctx.arc(x*tileSize+16, y*tileSize+16, 14, 0, Math.PI * 2);
    ctx.fill();
  }
}

function drawPlayer() {
  ctx.drawImage(playerImg, playerPos.x*tileSize+4, playerPos.y*tileSize+4, tileSize-8, tileSize-8);
}

function drawMap() {
  for(let y=0; y<mapHeight; y++) {
    for(let x=0; x<mapWidth; x++) {
      drawTile(gameMap[y*mapWidth + x], x, y);
    }
  }
}

function canMove(x, y) {
  if(x < 0 || y < 0 || x >= mapWidth || y >= mapHeight) return false;
  if(gameMap[y*mapWidth + x] === 1) return false; // tree blocks
  if(gameMap[y*mapWidth + x] === 2) return false; // water blocks
  return true;
}

function move(dir) {
  if(gameWon) return;
  let newX = playerPos.x;
  let newY = playerPos.y;
  if(dir === 'up') newY--;
  else if(dir === 'down') newY++;
  else if(dir === 'left') newX--;
  else if(dir === 'right') newX++;

  if(canMove(newX, newY)) {
    playerPos.x = newX;
    playerPos.y = newY;
    playSound('move');
    draw();
    checkWin();
  }
}

function checkWin() {
  if(gameMap[playerPos.y*mapWidth + playerPos.x] === 3) {
    gameWon = true;
    document.getElementById('message').textContent = '🎉 Happiest birthday to the most special girl Mariam ❤️ 🎉';
    playSound('win');
    confetti();
  }
}

function playSound(type) {
  let audio;
  if(type === 'move') {
    audio = new Audio('https://freesound.org/data/previews/66/66717_634166-lq.mp3');
  } else if(type === 'win') {
    audio = new Audio('https://freesound.org/data/previews/331/331912_3248244-lq.mp3');
  }
  if(audio) audio.play();
}

function confetti() {
  const duration = 3 * 1000;
  const animationEnd = Date.now() + duration;
  const colors = ['#bb0000', '#ffffff', '#ff0000'];

  (function frame() {
    confettiCtx.clearRect(0, 0, canvas.width, canvas.height);
    if(Date.now() > animationEnd) {
      confettiCtx.clearRect(0, 0, canvas.width, canvas.height);
      return;
    }
    // simple confetti drawing here
    for(let i=0; i<30; i++) {
      const x = Math.random() * canvas.width;
      const y = Math.random() * canvas.height;
      const size = Math.random() * 6 + 4;
      confettiCtx.fillStyle = colors[Math.floor(Math.random() * colors.length)];
      confettiCtx.fillRect(x, y, size, size/3);
    }
    requestAnimationFrame(frame);
  })();
}

function draw() {
  drawMap();
  drawPlayer();
}

let confettiCanvas = document.createElement('canvas');
confettiCanvas.width = canvas.width;
confettiCanvas.height = canvas.height;
confettiCanvas.style.position = 'absolute';
confettiCanvas.style.left = canvas.offsetLeft + 'px';
confettiCanvas.style.top = canvas.offsetTop + 'px';
document.getElementById('game').appendChild(confettiCanvas);
const confettiCtx = confettiCanvas.getContext('2d');

playerImg.onload = function() {
  draw();
};
