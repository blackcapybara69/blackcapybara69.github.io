<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Loading Screen Game</title>
<style>
    body {
        margin: 0;
        padding: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        background: #111;
        color: #fff;
        font-family: Arial, sans-serif;
        flex-direction: column;
        overflow: hidden;
    }
    #loadingBarContainer {
        width: 80%;
        height: 30px;
        border: 2px solid #fff;
        margin-top: 20px;
        position: relative;
        overflow: hidden;
    }
    #loadingBar {
        height: 100%;
        width: 0;
        background: lime;
        transition: width 0.1s;
    }
    #loadingText {
        font-size: 24px;
        margin-bottom: 10px;
    }
    #scoreText {
        font-size: 20px;
        margin-top: 10px;
    }
    #gameCanvas {
        display: none;
        margin-top: 20px;
        background: #222;
        border: 2px solid #fff;
    }
</style>
</head>
<body>
<div id="loadingText">Loading Game...</div>
<div id="loadingBarContainer">
    <div id="loadingBar"></div>
</div>
<div id="scoreText">Score: 0</div>
<canvas id="gameCanvas" width="400" height="300" tabindex="0"></canvas>
<script>
let progress = 0;
const loadingBar = document.getElementById('loadingBar');
const loadingText = document.getElementById('loadingText');
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const scoreText = document.getElementById('scoreText');

let playerX = 180, playerY = 130, playerSize = 40;
let keys = {};

let dvdX = 50, dvdY = 50, dvdWidth = 100, dvdHeight = 50;
let dvdDX = 2.5, dvdDY = 2;
let score = 0;

canvas.focus();
canvas.addEventListener('keydown', e => keys[e.key] = true);
canvas.addEventListener('keyup', e => keys[e.key] = false);

function simulateLoading() {
    progress += Math.random() * 3 + 0.5;
    if (progress >= 100) {
        progress = 100;
        loadingText.innerText = 'Game Loaded! Enjoy!';
        return;
    }
    loadingBar.style.width = progress + '%';
    requestAnimationFrame(simulateLoading);
}

function startMiniGame() {
    loadingText.style.display = 'none';
    loadingBar.parentNode.style.display = 'none';
    canvas.style.display = 'block';
    canvas.focus();
    score = 0;
    scoreText.innerText = 'Score: 0';
    requestAnimationFrame(gameLoop);
}

function resetGame() {
    playerX = 180;
    playerY = 130;
    dvdX = 50;
    dvdY = 50;
    dvdDX = 2.5;
    dvdDY = 2;
    score = 0;
    scoreText.innerText = 'Score: 0';
}

function gameLoop() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    if(keys['ArrowLeft']) playerX -= 4;
    if(keys['ArrowRight']) playerX += 4;
    if(keys['ArrowUp']) playerY -= 4;
    if(keys['ArrowDown']) playerY += 4;

    playerX = Math.max(0, Math.min(canvas.width - playerSize, playerX));
    playerY = Math.max(0, Math.min(canvas.height - playerSize, playerY));

    ctx.fillStyle = 'lime';
    ctx.fillRect(playerX, playerY, playerSize, playerSize);

    dvdX += dvdDX;
    dvdY += dvdDY;

    let bounced = false;

    if(dvdX + dvdWidth > canvas.width || dvdX < 0) { dvdDX *= -1; bounced = true; }
    if(dvdY + dvdHeight > canvas.height || dvdY < 0) { dvdDY *= -1; bounced = true; }

    if(bounced) {
        score += 1;
        scoreText.innerText = 'Score: ' + score;
    }

    if(playerX < dvdX + dvdWidth && playerX + playerSize > dvdX && playerY < dvdY + dvdHeight && playerY + playerSize > dvdY) {
        alert('You touched the red square! Game will restart.');
        resetGame();
    }

    ctx.fillStyle = 'red';
    ctx.fillRect(dvdX, dvdY, dvdWidth, dvdHeight);

    requestAnimationFrame(gameLoop);
}

simulateLoading();
setTimeout(startMiniGame, 500);
</script>
</body>
</html>
