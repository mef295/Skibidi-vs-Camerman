# Skibidi-vs-Camerman
const canvas = document.createElement("canvas");
const ctx = canvas.getContext("2d");
document.body.appendChild(canvas);
canvas.width = 800;
canvas.height = 600;

document.addEventListener("keydown", movePlayer);
document.addEventListener("click", shootBullet);

let player = { x: 375, y: 500, width: 50, height: 50, speed: 10 };
let bullets = [];
let enemies = [];
let score = 0;
let gameOver = false;

function movePlayer(e) {
    if (e.key === "ArrowLeft" && player.x > 0) player.x -= player.speed;
    if (e.key === "ArrowRight" && player.x < canvas.width - player.width) player.x += player.speed;
}

function shootBullet() {
    bullets.push({ x: player.x + player.width / 2 - 5, y: player.y, width: 10, height: 20, speed: 5 });
}

function spawnEnemy() {
    let x = Math.random() * (canvas.width - 50);
    enemies.push({ x, y: 0, width: 50, height: 50, speed: 2 });
}

function update() {
    bullets.forEach(bullet => bullet.y -= bullet.speed);
    enemies.forEach(enemy => enemy.y += enemy.speed);
    
    bullets = bullets.filter(b => b.y > 0);
    enemies = enemies.filter(e => e.y < canvas.height);

    bullets.forEach((bullet, bIndex) => {
        enemies.forEach((enemy, eIndex) => {
            if (
                bullet.x < enemy.x + enemy.width &&
                bullet.x + bullet.width > enemy.x &&
                bullet.y < enemy.y + enemy.height &&
                bullet.y + bullet.height > enemy.y
            ) {
                bullets.splice(bIndex, 1);
                enemies.splice(eIndex, 1);
                score++;
            }
        });
    });

    enemies.forEach(enemy => {
        if (
            player.x < enemy.x + enemy.width &&
            player.x + player.width > enemy.x &&
            player.y < enemy.y + enemy.height &&
            player.y + player.height > enemy.y
        ) {
            gameOver = true;
        }
    });
}

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = "black";
    ctx.fillRect(player.x, player.y, player.width, player.height);
    ctx.fillStyle = "red";
    bullets.forEach(bullet => ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height));
    ctx.fillStyle = "purple";
    enemies.forEach(enemy => ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height));
    ctx.fillStyle = "white";
    ctx.fillText(`Score: ${score}`, 10, 20);
    if (gameOver) ctx.fillText("Game Over! Press F5 to Restart", 300, 300);
}

function loop() {
    update();
    draw();
    if (!gameOver) requestAnimationFrame(loop);
}

setInterval(spawnEnemy, 1000);
loop();

