<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>2D Football Game</title>
  <style>
    body {
      margin: 0;
      background: #1b5e20;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      font-family: Arial, sans-serif;
    }
    canvas {
      background: #2e7d32;
      border: 4px solid #ffffff;
    }
  </style>
</head>
<body>
  <canvas id="game" width="800" height="500"></canvas>

  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");

    // Game objects
    const player = {
      x: 100,
      y: 250,
      r: 15,
      speed: 4
    };

    const ball = {
      x: 400,
      y: 250,
      r: 10,
      vx: 0,
      vy: 0
    };

    const goal = {
      x: 760,
      y: 180,
      w: 20,
      h: 140
    };

    let score = 0;
    const keys = {};

    // Input
    window.addEventListener("keydown", e => keys[e.key] = true);
    window.addEventListener("keyup", e => keys[e.key] = false);

    function distance(a, b) {
      return Math.hypot(a.x - b.x, a.y - b.y);
    }

    function update() {
      // Player movement
      if (keys["w"] && player.y > player.r) player.y -= player.speed;
      if (keys["s"] && player.y < canvas.height - player.r) player.y += player.speed;
      if (keys["a"] && player.x > player.r) player.x -= player.speed;
      if (keys["d"] && player.x < canvas.width - player.r) player.x += player.speed;

      // Kick ball
      if (distance(player, ball) < player.r + ball.r) {
        const angle = Math.atan2(ball.y - player.y, ball.x - player.x);
        ball.vx = Math.cos(angle) * 5;
        ball.vy = Math.sin(angle) * 5;
      }

      // Ball physics
      ball.x += ball.vx;
      ball.y += ball.vy;
      ball.vx *= 0.98;
      ball.vy *= 0.98;

      // Wall collision
      if (ball.y < ball.r || ball.y > canvas.height - ball.r) ball.vy *= -1;
      if (ball.x < ball.r) ball.vx *= -1;

      // Goal detection
      if (
        ball.x + ball.r > goal.x &&
        ball.y > goal.y &&
        ball.y < goal.y + goal.h
      ) {
        score++;
        resetBall();
      }
    }

    function resetBall() {
      ball.x = 400;
      ball.y = 250;
      ball.vx = 0;
      ball.vy = 0;
    }

    function drawField() {
      ctx.strokeStyle = "#ffffff";
      ctx.lineWidth = 2;

      // Center line
      ctx.beginPath();
      ctx.moveTo(400, 0);
      ctx.lineTo(400, 500);
      ctx.stroke();

      // Center circle
      ctx.beginPath();
      ctx.arc(400, 250, 60, 0, Math.PI * 2);
      ctx.stroke();

      // Goal
      ctx.strokeRect(goal.x, goal.y, goal.w, goal.h);
    }

    function drawCircle(x, y, r, color) {
      ctx.fillStyle = color;
      ctx.beginPath();
      ctx.arc(x, y, r, 0, Math.PI * 2);
      ctx.fill();
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      drawField();
      drawCircle(player.x, player.y, player.r, "#2196f3");
      drawCircle(ball.x, ball.y, ball.r, "#ffffff");

      ctx.fillStyle = "#ffffff";
      ctx.font = "20px Arial";
      ctx.fillText("Score: " + score, 20, 30);
    }

    function gameLoop() {
      update();
      draw();
      requestAnimationFrame(gameLoop);
    }

    gameLoop();
  </script>
</body>
</html>

