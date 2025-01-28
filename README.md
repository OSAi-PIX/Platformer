<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced Platformer Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #87CEEB;
        }

        #game-container {
            position: relative;
            width: 100vw;
            height: 100vh;
            background-color: #e0e0e0;
        }

        #player {
            position: absolute;
            width: 50px;
            height: 50px;
            background-color: red;
            border-radius: 5px;
            transition: left 0.1s, bottom 0.1s;
        }

        .platform {
            position: absolute;
            width: 200px;
            height: 20px;
            background-color: green;
        }

        .enemy {
            position: absolute;
            width: 50px;
            height: 50px;
            background-color: black;
            border-radius: 5px;
        }

        .health-bar {
            position: absolute;
            top: 10px;
            left: 10px;
            width: 200px;
            height: 20px;
            background-color: #333;
            border-radius: 5px;
        }

        .health-bar div {
            height: 100%;
            width: 100%;
            background-color: red;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <div id="game-container">
        <div id="player"></div>
        <div class="platform" style="left: 100px; bottom: 50px;"></div>
        <div class="platform" style="left: 300px; bottom: 150px;"></div>
        <div class="platform" style="left: 500px; bottom: 250px;"></div>
        <div class="platform" style="left: 700px; bottom: 350px;"></div>
        <div class="platform" style="left: 900px; bottom: 450px;"></div>
        <div class="enemy" style="left: 350px; bottom: 150px;"></div>
        <div class="enemy" style="left: 650px; bottom: 250px;"></div>
        <div class="health-bar">
            <div id="health"></div>
        </div>
    </div>

    <script>
        const player = document.getElementById('player');
        const gameContainer = document.getElementById('game-container');
        const healthBar = document.getElementById('health');

        const playerSpeed = 5;
        const gravity = 0.8;
        const jumpStrength = 15;

        let playerX = 100, playerY = 500; 
        let velocityY = 0;
        let isJumping = false;
        let isFalling = false;
        let health = 100;
        let keys = {};

        const platforms = [
            { x: 100, y: 50, width: 200, height: 20 },
            { x: 300, y: 150, width: 200, height: 20 },
            { x: 500, y: 250, width: 200, height: 20 },
            { x: 700, y: 350, width: 200, height: 20 },
            { x: 900, y: 450, width: 200, height: 20 },
        ];

        const enemies = [
            { x: 350, y: 150, width: 50, height: 50, direction: 1 },
            { x: 650, y: 250, width: 50, height: 50, direction: -1 }
        ];

        function updatePlayerPosition() {
            if (keys['ArrowLeft']) {
                playerX -= playerSpeed;
            }
            if (keys['ArrowRight']) {
                playerX += playerSpeed;
            }

            if (isJumping) {
                velocityY = -jumpStrength;
                isJumping = false;
            }

            velocityY += gravity;
            playerY += velocityY;

            if (playerY > gameContainer.offsetHeight - 50) {
                playerY = gameContainer.offsetHeight - 50;
                isFalling = false;
                velocityY = 0;
            }

            player.style.left = playerX + 'px';
            player.style.bottom = playerY + 'px';
        }

        function detectCollisions() {
            isFalling = true;

            // Check for platform collisions
            for (const platform of platforms) {
                if (
                    playerX + 50 > platform.x &&
                    playerX < platform.x + platform.width &&
                    playerY <= platform.y + platform.height &&
                    playerY + 50 > platform.y
                ) {
                    playerY = platform.y + platform.height;
                    isFalling = false;
                    velocityY = 0;
                }
            }

            // Check for enemy collisions
            for (const enemy of enemies) {
                if (
                    playerX + 50 > enemy.x &&
                    playerX < enemy.x + enemy.width &&
                    playerY + 50 > enemy.y &&
                    playerY < enemy.y + enemy.height
                ) {
                    takeDamage();
                }
            }
        }

        function takeDamage() {
            health -= 10;
            healthBar.style.width = `${health}%`;
            if (health <= 0) {
                alert("Game Over!");
                resetGame();
            }
        }

        function moveEnemies() {
            for (const enemy of enemies) {
                enemy.x += enemy.direction * 2;
                if (enemy.x <= 0 || enemy.x >= gameContainer.offsetWidth - 50) {
                    enemy.direction *= -1;
                }
                const enemyElement = document.querySelectorAll('.enemy')[enemies.indexOf(enemy)];
                enemyElement.style.left = `${enemy.x}px`;
                enemyElement.style.bottom = `${enemy.y}px`;
            }
        }

        function gameLoop() {
            updatePlayerPosition();
            detectCollisions();
            moveEnemies();
            requestAnimationFrame(gameLoop);
        }

        document.addEventListener('keydown', (e) => {
            keys[e.key] = true;

            if (e.key === ' ' && !isJumping && !isFalling) {
                isJumping = true;
            }
        });

        document.addEventListener('keyup', (e) => {
            keys[e.key] = false;
        });

        function resetGame() {
            playerX = 100;
            playerY = 500;
            velocityY = 0;
            health = 100;
            healthBar.style.width = '100%';
        }

        gameLoop();
    </script>
</body>
</html>
