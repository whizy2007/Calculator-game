<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>🚀 Dev Whiz Calculator Game</title>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap" rel="stylesheet"/>
  <style>
    body {
      margin: 0;
      padding: 0;
      background-color: #0a0a0a;
      color: #fff;
      font-family: 'Orbitron', sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    .dev-logo {
      margin-top: 20px;
      font-size: 2rem;
      background: linear-gradient(90deg, orange, red, gold);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      animation: pulse 2s infinite;
    }

    @keyframes pulse {
      0%, 100% { transform: scale(1); }
      50% { transform: scale(1.05); }
    }

    .calculator-container {
      background: #111;
      padding: 20px;
      border-radius: 20px;
      box-shadow: 0 0 15px orange;
      max-width: 360px;
      width: 100%;
      text-align: center;
      margin-top: 30px;
    }

    .display {
      background: #000;
      color: #0f0;
      font-size: 2rem;
      padding: 15px;
      border-radius: 10px;
      margin-bottom: 15px;
      width: 100%;
      box-sizing: border-box;
    }

    .buttons {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
    }

    .button {
      padding: 15px;
      background: #222;
      border: none;
      border-radius: 10px;
      font-size: 1.2rem;
      color: #fff;
      cursor: pointer;
      transition: 0.2s;
    }

    .button:hover {
      background: #ff5722;
    }

    .info {
      margin-top: 10px;
    }

    .score, .level, .avatar, .mode {
      margin: 10px 0;
    }

    select {
      padding: 5px;
      background: #000;
      color: #fff;
      border: 1px solid orange;
      border-radius: 5px;
    }

    .target {
      color: #ffa500;
      font-size: 1.2rem;
    }
  </style>
</head>
<body>
  <div class="dev-logo">🚀 Dev Whiz</div>

  <div class="calculator-container">
    <div class="target">🎯 Target: <span id="targetNum"></span></div>
    <input type="text" id="display" class="display" disabled />

    <div class="buttons">
      <button class="button" onclick="press('7')">7</button>
      <button class="button" onclick="press('8')">8</button>
      <button class="button" onclick="press('9')">9</button>
      <button class="button" onclick="press('/')">/</button>
      <button class="button" onclick="press('4')">4</button>
      <button class="button" onclick="press('5')">5</button>
      <button class="button" onclick="press('6')">6</button>
      <button class="button" onclick="press('*')">*</button>
      <button class="button" onclick="press('1')">1</button>
      <button class="button" onclick="press('2')">2</button>
      <button class="button" onclick="press('3')">3</button>
      <button class="button" onclick="press('-')">-</button>
      <button class="button" onclick="press('0')">0</button>
      <button class="button" onclick="press('.')">.</button>
      <button class="button" onclick="checkResult()">=</button>
      <button class="button" onclick="press('+')">+</button>
    </div>

    <div class="info">
      <div class="score">🏅 Score: <span id="score">0</span></div>
      <div class="level">📶 Level: <span id="level">1</span></div>
      <div class="avatar">🧍 Avatar: 
        <select id="avatarSelect" onchange="changeAvatar()">
          <option value="🦸‍♂️">🦸‍♂️</option>
          <option value="🧙‍♂️">🧙‍♂️</option>
          <option value="👩‍🚀">👩‍🚀</option>
        </select>
        <span id="avatar">🦸‍♂️</span>
      </div>
      <div class="mode">🎮 Mode: 
        <select id="gameMode" onchange="changeMode()">
          <option value="normal">Normal</option>
          <option value="timed">Timed</option>
          <option value="limited">Limited Moves</option>
        </select>
      </div>
    </div>
  </div>

  <script>
    let score = parseInt(localStorage.getItem('score')) || 0;
    let level = parseInt(localStorage.getItem('level')) || 1;
    let avatar = localStorage.getItem('avatar') || '🦸‍♂️';
    let mode = localStorage.getItem('mode') || 'normal';
    let display = '';
    let target = generateTarget();
    let moves = 5;
    let timer;

    document.getElementById('score').textContent = score;
    document.getElementById('level').textContent = level;
    document.getElementById('targetNum').textContent = target;
    document.getElementById('avatar').textContent = avatar;
    document.getElementById('avatarSelect').value = avatar;
    document.getElementById('gameMode').value = mode;

    if (mode === 'timed') startTimer();

    function press(value) {
      display += value;
      document.getElementById('display').value = display;
    }

    function checkResult() {
      try {
        const result = eval(display);
        if (Math.abs(result - target) < 0.001) {
          score += 10;
          level++;
          target = generateTarget();
        } else {
          score -= 5;
        }

        if (mode === 'limited') {
          moves--;
          if (moves <= 0) {
            alert('Game Over! Out of moves.');
            resetGame();
            return;
          }
        }

        localStorage.setItem('score', score);
        localStorage.setItem('level', level);
        document.getElementById('score').textContent = score;
        document.getElementById('level').textContent = level;
        document.getElementById('targetNum').textContent = target;
        document.getElementById('display').value = '';
        display = '';
      } catch {
        display = '';
        document.getElementById('display').value = '';
      }
    }

    function generateTarget() {
      return Math.floor(Math.random() * (10 + level * 2)) + 1;
    }

    function changeAvatar() {
      avatar = document.getElementById('avatarSelect').value;
      localStorage.setItem('avatar', avatar);
      document.getElementById('avatar').textContent = avatar;
    }

    function changeMode() {
      mode = document.getElementById('gameMode').value;
      localStorage.setItem('mode', mode);
      if (mode === 'timed') startTimer();
    }

    function startTimer() {
      clearInterval(timer);
      let timeLeft = 20;
      timer = setInterval(() => {
        timeLeft--;
        if (timeLeft <= 0) {
          clearInterval(timer);
          alert('Time up!');
          resetGame();
        }
      }, 1000);
    }

    function resetGame() {
      score = 0;
      level = 1;
      target = generateTarget();
      localStorage.setItem('score', score);
      localStorage.setItem('level', level);
      document.getElementById('score').textContent = score;
      document.getElementById('level').textContent = level;
      document.getElementById('targetNum').textContent = target;
      display = '';
      document.getElementById('display').value = '';
      moves = 5;
    }
  </script>
</body>
</html>
