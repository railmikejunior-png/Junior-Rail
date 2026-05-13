<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Madagascar Kartz Inspired Game</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background: #222;
      font-family: Arial, sans-serif;
      color: white;
    }canvas {
  display: block;
  margin: auto;
  background: linear-gradient(#5ecbff, #5fd35f);
}

#ui {
  position: absolute;
  top: 10px;
  left: 10px;
  font-size: 22px;
  background: rgba(0,0,0,0.5);
  padding: 10px;
  border-radius: 10px;
}

  </style>
</head>
<body>
  <div id="ui">Speed: <span id="speed">0</span></div>
  <canvas id="gameCanvas" width="1000" height="600"></canvas>  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const speedText = document.getElementById('speed');

    const keys = {};

    document.addEventListener('keydown', (e) => {
      keys[e.key.toLowerCase()] = true;
    });

    document.addEventListener('keyup', (e) => {
      keys[e.key.toLowerCase()] = false;
    });

    const kart = {
      x: 500,
      y: 300,
      width: 60,
      height: 35,
      speed: 0,
      maxSpeed: 8,
      acceleration: 0.2,
      friction: 0.05,
      angle: 0,
      turnSpeed: 0.05,
      color: '#ff4444'
    };

    const track = {
      x: 80,
      y: 80,
      width: 840,
      height: 440
    };

    function update() {
      if (keys['w'] || keys['arrowup']) {
        kart.speed += kart.acceleration;
      }

      if (keys['s'] || keys['arrowdown']) {
        kart.speed -= kart.acceleration;
      }

      if (keys['a'] || keys['arrowleft']) {
        kart.angle -= kart.turnSpeed * (kart.speed / 2);
      }

      if (keys['d'] || keys['arrowright']) {
        kart.angle += kart.turnSpeed * (kart.speed / 2);
      }

      if (kart.speed > kart.maxSpeed) kart.speed = kart.maxSpeed;
      if (kart.speed < -kart.maxSpeed / 2) kart.speed = -kart.maxSpeed / 2;

      kart.speed *= 0.98;

      if (Math.abs(kart.speed) < 0.02) {
        kart.speed = 0;
      }

      kart.x += Math.cos(kart.angle) * kart.speed;
      kart.y += Math.sin(kart.angle) * kart.speed;

      // Track collision
      if (
        kart.x < track.x ||
        kart.x > track.x + track.width ||
        kart.y < track.y ||
        kart.y > track.y + track.height
      ) {
        kart.speed *= 0.9;
      }

      speedText.textContent = Math.round(Math.abs(kart.speed) * 20);
    }

    function drawTrack() {
      // Grass
      ctx.fillStyle = '#5fd35f';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // Road
      ctx.fillStyle = '#555';
      ctx.fillRect(track.x, track.y, track.width, track.height);

      // Inner grass
      ctx.fillStyle = '#5fd35f';
      ctx.fillRect(250, 180, 500, 240);

      // Track borders
      ctx.strokeStyle = 'white';
      ctx.lineWidth = 6;
      ctx.strokeRect(track.x, track.y, track.width, track.height);
      ctx.strokeRect(250, 180, 500, 240);

      // Finish line
      for (let i = 0; i < 10; i++) {
        ctx.fillStyle = i % 2 === 0 ? 'white' : 'black';
        ctx.fillRect(120 + i * 10, 80, 10, 50);
      }
    }

    function drawKart() {
      ctx.save();
      ctx.translate(kart.x, kart.y);
      ctx.rotate(kart.angle);

      // Body
      ctx.fillStyle = kart.color;
      ctx.fillRect(-kart.width / 2, -kart.height / 2, kart.width, kart.height);

      // Driver head
      ctx.fillStyle = '#ffe0bd';
      ctx.beginPath();
      ctx.arc(0, -5, 10, 0, Math.PI * 2);
      ctx.fill();

      // Wheels
      ctx.fillStyle = 'black';
      ctx.fillRect(-28, -20, 10, 10);
      ctx.fillRect(18, -20, 10, 10);
      ctx.fillRect(-28, 10, 10, 10);
      ctx.fillRect(18, 10, 10, 10);

      ctx.restore();
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawTrack();
      drawKart();
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
