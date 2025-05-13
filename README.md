<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no" />
<title>لعبة حية بسيطة</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Cairo&display=swap');
  body {
    margin: 0; padding: 0;
    background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
    font-family: 'Cairo', sans-serif;
    color: #ffffff;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 600px;
    max-height: 600px;
    overflow: hidden;
  }
  #game-container {
    position: relative;
    width: 340px;
    max-width: 100vw;
    height: 580px;
    background: #121f3d;
    border-radius: 20px;
    box-shadow: 0 8px 20px rgba(0,0,0,0.7);
    overflow: hidden;
    padding: 15px;
    box-sizing: border-box;
  }
  #scoreboard {
    font-size: 22px;
    margin-bottom: 10px;
    text-align: center;
    user-select: none;
  }
  #ball {
    position: absolute;
    width: 60px;
    height: 60px;
    background: radial-gradient(circle at top left, #ff6f61, #d84315);
    border-radius: 50%;
    box-shadow: 0 0 15px #ff6f61aa;
    cursor: pointer;
    user-select: none;
    transition: box-shadow 0.2s ease;
  }
  #ball:active {
    box-shadow: 0 0 30px #ff3b1fcc;
  }
  #message {
    color: #ffcc00;
    font-size: 20px;
    text-align: center;
    margin-top: 5px;
    min-height: 28px;
  }
  #restart-button {
    margin-top: 15px;
    width: 100%;
    background-color: #ff6f61;
    border: none;
    border-radius: 15px;
    padding: 12px 0;
    font-size: 20px;
    color: white;
    cursor: pointer;
    user-select: none;
    display: none;
    transition: background-color 0.3s ease;
  }
  #restart-button:hover {
    background-color: #ff3b1f;
  }
  @media (max-width: 350px) {
    #game-container {
      width: 90vw;
      height: 550px;
      padding: 10px;
    }
    #scoreboard {
      font-size: 18px;
    }
    #ball {
      width: 50px;
      height: 50px;
    }
    #restart-button {
      font-size: 18px;
      padding: 10px 0;
    }
  }
</style>
</head>
<body>
  <div id="game-container" role="main" aria-label="لعبة حية بسيطة">
    <div id="scoreboard" aria-live="polite">النقاط: 0</div>
    <div id="message" aria-live="assertive"></div>
    <div id="ball" tabindex="0" aria-label="كرة للضغط عليها"></div>
    <button id="restart-button" aria-label="إعادة تشغيل اللعبة">إعادة اللعب</button>
  </div>

<script>
  (() => {
    const container = document.getElementById('game-container');
    const ball = document.getElementById('ball');
    const scoreBoard = document.getElementById('scoreboard');
    const message = document.getElementById('message');
    const restartBtn = document.getElementById('restart-button');

    let score = 0;
    let speedX = 3 + Math.random() * 2;
    let speedY = 3 + Math.random() * 2;
    let posX = 100;
    let posY = 100;
    let animationId = null;
    let gameRunning = true;

    const ballSize = ball.offsetWidth;
    const containerWidth = container.clientWidth;
    const containerHeight = container.clientHeight;

    function updateScore() {
      scoreBoard.textContent = 'النقاط: ' + score;
    }

    function showMessage(text, duration=1500) {
      message.textContent = text;
      setTimeout(() => {
        if (message.textContent === text) {
          message.textContent = '';
        }
      }, duration);
    }

    function moveBall() {
      posX += speedX;
      posY += speedY;

      // Bounce off walls
      if (posX + ballSize > containerWidth) {
        posX = containerWidth - ballSize;
        speedX = -speedX;
      } else if (posX < 0) {
        posX = 0;
        speedX = -speedX;
      }
      if (posY + ballSize > containerHeight) {
        posY = containerHeight - ballSize;
        speedY = -speedY;
      } else if (posY < 0) {
        posY = 0;
        speedY = -speedY;
      }

      ball.style.left = posX + 'px';
      ball.style.top = posY + 'px';

      if(gameRunning) {
        animationId = requestAnimationFrame(moveBall);
      }
    }

    ball.addEventListener('click', () => {
      if (!gameRunning) return;
      score++;
      updateScore();
      showMessage('نجحت في الضغط على الكرة!');
      // Increase speed slightly for challenge
      speedX *= 1.05;
      speedY *= 1.05;
    });

    ball.addEventListener('keydown', (e) => {
      if (e.key === ' ' || e.key === 'Enter') {
        e.preventDefault();
        ball.click();
      }
    });

    function endGame() {
      gameRunning = false;
      cancelAnimationFrame(animationId);
      message.textContent = 'انتهت اللعبة! نقاطك: ' + score;
      restartBtn.style.display = 'block';
    }

    restartBtn.addEventListener('click', () => {
      score = 0;
      updateScore();
      message.textContent = '';
      restartBtn.style.display = 'none';
      speedX = 3 + Math.random() * 2;
      speedY = 3 + Math.random() * 2;
      posX = 100;
      posY = 100;
      gameRunning = true;
      moveBall();
      ball.focus();
    });

    // End game after 30 seconds
    setTimeout(() => {
      endGame();
    }, 30000);

    updateScore();
    moveBall();
    ball.setAttribute('tabindex','0');
  })();
</script>
</body>
</html>

