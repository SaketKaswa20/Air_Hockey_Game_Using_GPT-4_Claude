const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

const userPaddle = {
  x: 30,
  y: canvas.height / 2 - 50,
  width: 10,
  height: 100,
};

const cpuPaddle = {
  x: canvas.width - 40,
  y: canvas.height / 2 - 50,
  width: 10,
  height: 100,
};

const puck = {
  x: canvas.width / 2,
  y: canvas.height / 2,
  radius: 10,
  dx: 2,
  dy: 2,
};

let userScore = 0;
let cpuScore = 0;
const userScoreDisplay = document.getElementById('userScore');
const cpuScoreDisplay = document.getElementById('cpuScore');
const startButton = document.getElementById('startButton');
let gameStarted = false;

function drawPaddle(x, y, width, height) {
  ctx.fillStyle = 'white';
  ctx.fillRect(x, y, width, height);
}

function drawPuck(x, y, radius) {
  ctx.beginPath();
  ctx.arc(x, y, radius, 0, Math.PI * 2);
  ctx.fillStyle = 'white';
  ctx.fill();
  ctx.closePath();
}

function drawScore(score, x, y) {
  ctx.font = '30px Arial';
  ctx.fillText(score, x, y);
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  drawPaddle(userPaddle.x, userPaddle.y, userPaddle.width, userPaddle.height);
  drawPaddle(cpuPaddle.x, cpuPaddle.y, cpuPaddle.width, cpuPaddle.height);
  drawPuck(puck.x, puck.y, puck.radius);
  drawScore(userScore, userScore.x, userScore.y);
  drawScore(cpuScore, cpuScore.x, cpuScore.y);
}

function resetPuck() {
  puck.x = canvas.width / 2;
  puck.y = canvas.height / 2;
  puck.dx = 2;
  puck.dy = 2; 
}

function gameLoop() {
  if (!gameStarted) {
    return;
  }

  // Update puck position
  puck.x += puck.dx;
  puck.y += puck.dy;

  // Collision detection for top and bottom walls
  if (puck.y - puck.radius < 0 || puck.y + puck.radius > canvas.height) {
    puck.dy = -puck.dy;
  }

  // Scoring and game over conditions
  if (puck.x - puck.radius < 0) {
    cpuScore++;
    cpuScoreDisplay.textContent = `CPU: ${cpuScore}`;
    resetPuck();
  } else if (puck.x + puck.radius > canvas.width) {
    userScore++;
    userScoreDisplay.textContent = `User: ${userScore}`;
    resetPuck();
  }

  // Collision detection for user and CPU paddles
  if (
    (puck.x - puck.radius < userPaddle.x + userPaddle.width &&
      puck.y > userPaddle.y &&
      puck.y < userPaddle.y + userPaddle.height) ||
    (puck.x + puck.radius > cpuPaddle.x &&
      puck.y > cpuPaddle.y &&
      puck.y < cpuPaddle.y + cpuPaddle.height)
  ) {
    puck.dx = -puck.dx;
  }

  // Improved AI for CPU paddle
  if (puck.dx > 0) {
    if (cpuPaddle.y + cpuPaddle.height / 2 < puck.y - 35) {
      cpuPaddle.y += 4;
    } else if (cpuPaddle.y + cpuPaddle.height / 2 > puck.y + 35) {
      cpuPaddle.y -= 4;
    }
  } else {
    if (cpuPaddle.y + cpuPaddle.height / 2 < puck.y + 35) {
      cpuPaddle.y += 4;
    } else if (cpuPaddle.y + cpuPaddle.height / 2 > puck.y - 35) {
      cpuPaddle.y -= 4;
    }
  }

  // Keep CPU paddle within canvas bounds
  if (cpuPaddle.y < 0) {
    cpuPaddle.y = 0;
  } else if (cpuPaddle.y + cpuPaddle.height > canvas.height) {
    cpuPaddle.y = canvas.height - cpuPaddle.height;
  }

  draw();
  requestAnimationFrame(gameLoop);
}

startButton.addEventListener('click', () => {
  gameStarted = true;
  gameLoop();
});
