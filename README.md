<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simple 1v1 JS Example</title>
    <style>
        canvas {
            border: 1px solid black;
            background-color: #f1f1f1;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="600" height="400"></canvas>
    <script src="game.js"></script>

// Get the canvas element and its context
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

// Player object
const player = {
    x: 50,
    y: 50,
    width: 20,
    height: 20,
    color: 'blue',
    speed: 5,
    dx: 0,
    dy: 0
};

// Handle keyboard input
document.addEventListener('keydown', (e) => {
    if (e.key === 'ArrowRight' || e.key === 'd') {
        player.dx = player.speed;
    } else if (e.key === 'ArrowLeft' || e.key === 'a') {
        player.dx = -player.speed;
    } else if (e.key === 'ArrowUp' || e.key === 'w') {
        player.dy = -player.speed;
    } else if (e.key === 'ArrowDown' || e.key === 's') {
        player.dy = player.speed;
    }
});

document.addEventListener('keyup', (e) => {
    if (
        e.key === 'ArrowRight' || e.key === 'd' ||
        e.key === 'ArrowLeft' || e.key === 'a'
    ) {
        player.dx = 0;
    } else if (
        e.key === 'ArrowUp' || e.key === 'w' ||
        e.key === 'ArrowDown' || e.key === 's'
    ) {
        player.dy = 0;
    }
});

// Draw player function
function drawPlayer() {
    ctx.fillStyle = player.color;
    ctx.fillRect(player.x, player.y, player.width, player.height);
}

// Update player position
function updatePlayer() {
    player.x += player.dx;
    player.y += player.dy;

    // Boundary detection
    if (player.x + player.width > canvas.width || player.x < 0) {
        player.dx = 0;
        player.x = Math.max(0, Math.min(canvas.width - player.width, player.x));
    }
    if (player.y + player.height > canvas.height || player.y < 0) {
        player.dy = 0;
        player.y = Math.max(0, Math.min(canvas.height - player.height, player.y));
    }
}

// Main game loop
function gameLoop() {
    ctx.clearRect(0, 0, canvas.width, canvas.height); // Clear canvas
    drawPlayer();
    updatePlayer();
    requestAnimationFrame(gameLoop); // Loop
}

// Start the game loop
gameLoop();
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

// Game objects
const player = {
    x: 50, y: 50, width: 20, height: 20, color: 'blue', speed: 5, dx: 0, dy: 0
};
const bullets = [];
const target = {
    x: 400, y: 200, radius: 15, color: 'red', hit: false
};

// --- Input Handling ---
const keys = {};
document.addEventListener('keydown', (e) => keys[e.key] = true);
document.addEventListener('keyup', (e) => keys[e.key] = false);

canvas.addEventListener('click', shoot);

function shoot(event) {
    // Calculate direction towards mouse click
    const rect = canvas.getBoundingClientRect();
    const mouseX = event.clientX - rect.left;
    const mouseY = event.clientY - rect.top;

    const angle = Math.atan2(mouseY - (player.y + player.height / 2), mouseX - (player.x + player.width / 2));
    const speed = 7;

    bullets.push({
        x: player.x + player.width / 2,
        y: player.y + player.height / 2,
        radius: 4,
        dx: Math.cos(angle) * speed,
        dy: Math.sin(angle) * speed
    });
}

// --- Drawing Functions ---
function drawPlayer() {
    ctx.fillStyle = player.color;
    ctx.fillRect(player.x, player.y, player.width, player.height);
}

function drawBullets() {
    ctx.fillStyle = 'black';
    bullets.forEach(bullet => {
        ctx.beginPath();
        ctx.arc(bullet.x, bullet.y, bullet.radius, 0, Math.PI * 2);
        ctx.fill();
    });
}

function drawTarget() {
    ctx.fillStyle = target.hit ? 'green' : target.color;
    ctx.beginPath();
    ctx.arc(target.x, target.y, target.radius, 0, Math.PI * 2);
    ctx.fill();
}

// --- Update Functions ---
function updatePlayer() {
    player.dx = 0;
    player.dy = 0;

    if (keys['ArrowRight'] || keys['d']) player.dx = player.speed;
    if (keys['ArrowLeft'] || keys['a']) player.dx = -player.speed;
    if (keys['ArrowUp'] || keys['w']) player.dy = -player.speed;
    if (keys['ArrowDown'] || keys['s']) player.dy = player.speed;

    player.x += player.dx;
    player.y += player.dy;
    
    // Boundary detection (same as before)
    player.x = Math.max(0, Math.min(canvas.width - player.width, player.x));
    player.y = Math.max(0, Math.min(canvas.height - player.height, player.y));
}

function updateBullets() {
    for (let i = bullets.length - 1; i >= 0; i--) {
        const bullet = bullets[i];
        bullet.x += bullet.dx;
        bullet.y += bullet.dy;

        // Remove bullets outside canvas
        if (bullet.x < 0 || bullet.x > canvas.width || bullet.y < 0 || bullet.y > canvas.height) {
            bullets.splice(i, 1);
            continue;
        }

        // Check collision with target (Distance formula)
        const dist = Math.sqrt(Math.pow(bullet.x - target.x, 2) + Math.pow(bullet.y - target.y, 2));
        if (dist < bullet.radius + target.radius) {
            target.hit = true; // Mark target as hit
            bullets.splice(i, 1); // Remove bullet
            // In a real game, you would signal a score update to the server here
        }
    }
}

// --- Main Game Loop ---
function gameLoop() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    drawTarget();
    drawPlayer();
    drawBullets();
    
    updatePlayer();
    updateBullets();

    requestAnimationFrame(gameLoop);
}

// Start the game
gameLoop();
