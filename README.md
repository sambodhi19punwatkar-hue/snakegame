<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Nokia Snake Infinity</title>

<style>
  body {
    margin: 0;
    background: radial-gradient(circle, #b7d36a, #8faa4f);
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    font-family: "Segoe UI", monospace;
  }

  .container {
    text-align: center;
  }

  .scoreboard {
    display: flex;
    justify-content: space-between;
    background: rgba(255,255,255,0.25);
    backdrop-filter: blur(6px);
    padding: 10px 18px;
    border-radius: 12px;
    margin-bottom: 12px;
    box-shadow: 0 6px 15px rgba(0,0,0,0.2);
    font-weight: 600;
    color: #243016;
    letter-spacing: 1px;
  }

  .scorebox {
    text-align: left;
  }

  .scorebox span {
    display: block;
    font-size: 22px;
    color: #1b250f;
  }

  canvas {
    background: linear-gradient(#cfe38a, #bcd56d);
    border-radius: 18px;
    border: 8px solid #2e3b1f;
    box-shadow: inset 0 0 20px rgba(0,0,0,0.25),
                0 15px 40px rgba(0,0,0,0.4);
    image-rendering: pixelated;
  }
</style>
</head>

<body>
<div class="container">

  <div class="scoreboard">
    <div class="scorebox">
      SCORE
      <span id="score">0</span>
    </div>
    <div class="scorebox">
      HIGH
      <span id="highScore">0</span>
    </div>
  </div>

  <canvas id="game" width="400" height="400"></canvas>

</div>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

const grid = 20;
const speed = 150;

let snake, food, dx, dy, score;
let highScore = localStorage.getItem("snakeHighScore") || 0;
document.getElementById("highScore").textContent = highScore;

function resetGame() {
  snake = [{ x: 200, y: 200 }];
  dx = grid;
  dy = 0;
  score = 0;
  document.getElementById("score").textContent = score;
  spawnFood();
}

function spawnFood() {
  food = {
    x: Math.floor(Math.random() * 20) * grid,
    y: Math.floor(Math.random() * 20) * grid
  };
}

function drawSnakePart(part, isHead = false) {
  ctx.fillStyle = isHead ? "#1a1a1a" : "#2b2b2b";
  ctx.shadowColor = "rgba(0,0,0,0.5)";
  ctx.shadowBlur = 8;
  ctx.fillRect(part.x, part.y, grid, grid);
  ctx.shadowBlur = 0;

  // eyes 👀
  if (isHead) {
    ctx.fillStyle = "#ffffff";
    ctx.fillRect(part.x + 4, part.y + 5, 4, 4);
    ctx.fillRect(part.x + 12, part.y + 5, 4, 4);

    ctx.fillStyle = "#000";
    ctx.fillRect(part.x + 5, part.y + 6, 2, 2);
    ctx.fillRect(part.x + 13, part.y + 6, 2, 2);
  }
}

function gameLoop() {
  setTimeout(() => {
    requestAnimationFrame(gameLoop);
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    let head = { x: snake[0].x + dx, y: snake[0].y + dy };

    // ♾️ wrap around
    if (head.x < 0) head.x = canvas.width - grid;
    if (head.x >= canvas.width) head.x = 0;
    if (head.y < 0) head.y = canvas.height - grid;
    if (head.y >= canvas.height) head.y = 0;

    // self bite = death
    if (snake.some(p => p.x === head.x && p.y === head.y)) {
      if (score > highScore) {
        highScore = score;
        localStorage.setItem("snakeHighScore", highScore);
        document.getElementById("highScore").textContent = highScore;
      }
      alert("Game Over | Score: " + score);
      resetGame();
      return;
    }

    snake.unshift(head);

    // food eaten
    if (head.x === food.x && head.y === food.y) {
      score++;
      document.getElementById("score").textContent = score;
      spawnFood();
    } else {
      snake.pop();
    }

    // food 🍎
    ctx.fillStyle = "#243016";
    ctx.shadowColor = "#000";
    ctx.shadowBlur = 6;
    ctx.fillRect(food.x, food.y, grid, grid);
    ctx.shadowBlur = 0;

    // snake 🐍
    snake.forEach((part, i) => drawSnakePart(part, i === 0));

  }, speed);
}

document.addEventListener("keydown", e => {
  if (e.key === "ArrowUp" && dy === 0) { dx = 0; dy = -grid; }
  if (e.key === "ArrowDown" && dy === 0) { dx = 0; dy = grid; }
  if (e.key === "ArrowLeft" && dx === 0) { dx = -grid; dy = 0; }
  if (e.key === "ArrowRight" && dx === 0) { dx = grid; dy = 0; }
});

resetGame();
gameLoop();
</script>

</body>
</html
