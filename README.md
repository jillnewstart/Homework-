<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>作業小冒險</title>

<style>
* {
  box-sizing: border-box;
  -webkit-tap-highlight-color: transparent;
}

body {
  margin: 0;
  font-family:
    -apple-system,
    BlinkMacSystemFont,
    "Noto Sans TC",
    "Microsoft JhengHei",
    sans-serif;
  background: linear-gradient(180deg, #fff8e8, #eaf7ff);
  color: #4a4038;
  min-height: 100vh;
}

.container {
  max-width: 700px;
  margin: auto;
  padding: 20px 14px 50px;
}

h1 {
  text-align: center;
  font-size: 34px;
  margin: 10px 0 5px;
}

.subtitle {
  text-align: center;
  color: #8b7b6d;
  margin-bottom: 20px;
}

.tabs {
  display: flex;
  gap: 7px;
  overflow-x: auto;
  padding-bottom: 8px;
  margin-bottom: 15px;
}

.tab {
  flex: 0 0 auto;
  border: none;
  background: white;
  border-radius: 18px;
  padding: 10px 14px;
  font-size: 15px;
  box-shadow: 0 3px 10px rgba(0,0,0,.08);
}

.tab.active {
  background: #ffd66b;
  font-weight: bold;
}

.game {
  display: none;
  background: rgba(255,255,255,.9);
  border-radius: 28px;
  padding: 20px 15px 25px;
  box-shadow: 0 8px 25px rgba(0,0,0,.08);
}

.game.active {
  display: block;
}

.game h2 {
  text-align: center;
  margin: 5px 0 5px;
}

.description {
  text-align: center;
  color: #8c8178;
  margin-bottom: 18px;
}

button {
  cursor: pointer;
  font-family: inherit;
}

.main-btn {
  border: none;
  background: #ffb84d;
  color: white;
  font-size: 19px;
  font-weight: bold;
  padding: 13px 25px;
  border-radius: 30px;
  box-shadow: 0 5px 0 #df9432;
}

.main-btn:active {
  transform: translateY(4px);
  box-shadow: 0 1px 0 #df9432;
}

/* =========================
   1. 轉盤
========================= */

.wheel-area {
  display: flex;
  justify-content: center;
  margin: 15px 0 20px;
}

.pointer {
  position: absolute;
  z-index: 10;
  top: -9px;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 0;
  border-left: 17px solid transparent;
  border-right: 17px solid transparent;
  border-top: 34px solid #ff6b6b;
  filter: drop-shadow(0 3px 2px rgba(0,0,0,.2));
}

.wheel-wrapper {
  position: relative;
  width: min(330px, 82vw);
  aspect-ratio: 1;
}

.wheel {
  position: relative;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  border: 8px solid white;
  box-shadow: 0 7px 20px rgba(0,0,0,.15);

  /*
    7格。
    第0格中心固定在正上方。
  */
  background:
    conic-gradient(
      from -115.714deg,
      #ffd166 0deg 51.428deg,
      #ff9f9f 51.428deg 102.856deg,
      #8fd694 102.856deg 154.284deg,
      #8ecae6 154.284deg 205.712deg,
      #cdb4db 205.712deg 257.14deg,
      #ffb4a2 257.14deg 308.568deg,
      #a8dadc 308.568deg 360deg
    );

  transition: transform 4s cubic-bezier(.12,.8,.18,1);
}

.wheel-number {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 48px;
  height: 48px;
  margin-left: -24px;
  margin-top: -24px;

  display: flex;
  justify-content: center;
  align-items: center;

  font-size: 27px;
  font-weight: 900;
  color: white;
  text-shadow: 0 2px 3px rgba(0,0,0,.25);

  /*
    讓 JS 用 transform 放到各扇形中心。
  */
}

.wheel-center {
  position: absolute;
  z-index: 5;
  width: 70px;
  height: 70px;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  border-radius: 50%;
  background: white;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 28px;
  box-shadow: 0 3px 10px rgba(0,0,0,.15);
}

.result {
  min-height: 80px;
  margin: 15px 0;
  padding: 15px;
  border-radius: 20px;
  background: #fff8df;
  text-align: center;
  font-size: 20px;
  font-weight: bold;
}

/* =========================
   2. 翻牌
========================= */

.cards {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;
  max-width: 450px;
  margin: 20px auto;
}

.card {
  height: 120px;
  perspective: 800px;
  cursor: pointer;
}

.card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  transition: transform .6s;
  transform-style: preserve-3d;
}

.card.flipped .card-inner {
  transform: rotateY(180deg);
}

.card-front,
.card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  border-radius: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.card-front {
  background: linear-gradient(135deg,#a78bfa,#7c83fd);
  color: white;
  font-size: 42px;
  box-shadow: 0 5px 12px rgba(0,0,0,.12);
}

.card-back {
  background: #fff4cf;
  transform: rotateY(180deg);
  font-size: 38px;
  font-weight: bold;
}

/* =========================
   3. 綵帶迷宮
========================= */

.ribbon-maze {
  position: relative;
  height: 390px;
  margin: 10px auto;
  max-width: 600px;
  overflow: hidden;
  border-radius: 25px;
  background:
    radial-gradient(circle at 20% 20%, #ffffff 0 5%, transparent 6%),
    linear-gradient(135deg,#eef9ff,#fff5e9);
}

.ribbon {
  position: absolute;
  height: 35px;
  border-radius: 20px;
  border: 3px solid white;
  box-shadow: 0 4px 10px rgba(0,0,0,.15);
  transform-origin: left center;
  cursor: pointer;
  transition: .5s;
}

.ribbon span {
  position: absolute;
  left: 15px;
  top: 50%;
  transform: translateY(-50%);
  color: white;
  font-size: 17px;
  font-weight: bold;
}

.ribbon:nth-child(1) {
  width: 85%;
  top: 45px;
  left: 5%;
  background: #ff8fab;
  transform: rotate(8deg);
}

.ribbon:nth-child(2) {
  width: 80%;
  top: 100px;
  left: 12%;
  background: #7bdff2;
  transform: rotate(-7deg);
}

.ribbon:nth-child(3) {
  width: 88%;
  top: 165px;
  left: 3%;
  background: #95d5b2;
  transform: rotate(9deg);
}

.ribbon:nth-child(4) {
  width: 75%;
  top: 225px;
  left: 17%;
  background: #cdb4db;
  transform: rotate(-10deg);
}

.ribbon:nth-child(5) {
  width: 84%;
  top: 285px;
  left: 6%;
  background: #ffb4a2;
  transform: rotate(6deg);
}

.ribbon:nth-child(6) {
  width: 72%;
  top: 335px;
  left: 20%;
  background: #ffd166;
  transform: rotate(-6deg);
}

.ribbon.selected {
  animation: cutRibbon .7s forwards;
}

@keyframes cutRibbon {
  0% {
    transform: scale(1) rotate(var(--angle));
    opacity: 1;
  }
  50% {
    transform: scale(1.08) rotate(var(--angle));
  }
  100% {
    transform: translateX(500px) rotate(var(--angle));
    opacity: 0;
  }
}

/* =========================
   4. 打怪
========================= */

.monster {
  text-align: center;
  font-size: 100px;
  margin: 10px 0;
  transition: .2s;
}

.monster.hit {
  animation: hit .35s;
}

@keyframes hit {
  0% { transform: translateX(0) rotate(0); }
  25% { transform: translateX(-15px) rotate(-8deg); }
  50% { transform: translateX(15px) rotate(8deg); }
  100% { transform: translateX(0) rotate(0); }
}

.hp-box {
  max-width: 450px;
  margin: auto;
}

.hp-label {
  text-align: center;
  margin-bottom: 7px;
}

.hp-bar {
  height: 25px;
  background: #eee;
  border-radius: 20px;
  overflow: hidden;
}

.hp {
  height: 100%;
  width: 100%;
  background: #ff6b6b;
  border-radius: 20px;
  transition: width .4s;
}

.attack-buttons {
  display: flex;
  justify-content: center;
  gap: 10px;
  flex-wrap: wrap;
  margin-top: 20px;
}

.attack-btn {
  border: none;
  background: #ffcf56;
  border-radius: 20px;
  padding: 12px 18px;
  font-size: 17px;
  font-weight: bold;
}

/* =========================
   5. 火車
========================= */

.train-map {
  position: relative;
  height: 220px;
  margin: 20px 0;
  background: #e9f8ff;
  border-radius: 25px;
  overflow: hidden;
}

.track {
  position: absolute;
  left: 5%;
  right: 5%;
  top: 125px;
  height: 10px;
  background: #999;
  border-radius: 10px;
}

.stations {
  position: absolute;
  left: 5%;
  right: 5%;
  top: 103px;
  display: flex;
  justify-content: space-between;
}

.station {
  width: 34px;
  height: 34px;
  background: white;
  border: 4px solid #777;
  border-radius: 50%;
}

.train {
  position: absolute;
  font-size: 55px;
  top: 65px;
  left: 2%;
  transition: left .7s cubic-bezier(.2,.8,.2,1);
}

/* =========================
   6. 小動物
========================= */

.pet {
  text-align: center;
  font-size: 130px;
  margin: 15px 0;
  transition: .4s;
}

.pet-message {
  text-align: center;
  font-size: 20px;
  min-height: 35px;
}

.food-buttons {
  display: flex;
  justify-content: center;
  gap: 10px;
  flex-wrap: wrap;
  margin: 20px 0;
}

.food-btn {
  border: none;
  background: #fff1b8;
  border-radius: 18px;
  padding: 12px 16px;
  font-size: 25px;
}

/* 通用 */

.reset-btn {
  display: block;
  margin: 15px auto 0;
  border: none;
  background: #eee;
  color: #666;
  padding: 9px 16px;
  border-radius: 20px;
}

.hidden {
  display: none !important;
}

@media(max-width:480px) {
  h1 {
    font-size: 29px;
  }

  .game {
    padding: 17px 10px 22px;
  }

  .card {
    height: 105px;
  }

  .ribbon-maze {
    height: 380px;
  }
}
</style>
</head>

<body>

<div class="container">

  <h1>🌈 作業小冒險</h1>
  <div class="subtitle">今天要用哪一種方式完成任務呢？</div>

  <div class="tabs">
    <button class="tab active" onclick="showGame('wheel',this)">🎡 轉盤</button>
    <button class="tab" onclick="showGame('cards',this)">🃏 翻牌</button>
    <button class="tab" onclick="showGame('ribbonGame',this)">✂️ 迷宮</button>
    <button class="tab" onclick="showGame('monsterGame',this)">🐉 打怪</button>
    <button class="tab" onclick="showGame('trainGame',this)">🚂 火車</button>
    <button class="tab" onclick="showGame('petGame',this)">🐣 養成</button>
  </div>

  <!-- ======================
       1. 轉盤
  ======================= -->

  <section id="wheel" class="game active">
    <h2>🎡 幸運轉盤</h2>
    <div class="description">轉一轉，看看這次要寫幾個字！</div>

    <div class="wheel-area">
      <div class="wheel-wrapper">

        <div class="pointer"></div>

        <div class="wheel" id="wheelElement">
          <div class="wheel-number">0</div>
          <div class="wheel-number">1</div>
          <div class="wheel-number">2</div>
          <div class="wheel-number">3</div>
          <div class="wheel-number">4</div>
          <div class="wheel-number">5</div>
          <div class="wheel-number">6</div>

          <div class="wheel-center">✏️</div>
        </div>

      </div>
    </div>

    <div style="text-align:center;">
      <button class="main-btn" onclick="spinWheel()">🎡 開始轉！</button>
    </div>

    <div class="result" id="wheelResult">
      🌟 準備好了嗎？
    </div>
  </section>


  <!-- ======================
       2. 翻牌
  ======================= -->

  <section id="cards" class="game">
    <h2>🃏 神秘翻牌</h2>
    <div class="description">選一張卡片，看看今天的任務！</div>

    <div class="cards" id="cardContainer"></div>

    <div class="result" id="cardResult">
      🍀 選一張神秘卡片吧！
    </div>

    <button class="reset-btn" onclick="createCards()">
      🔄 重新洗牌
    </button>
  </section>


  <!-- ======================
       3. 綵帶迷宮
  ======================= -->

  <section id="ribbonGame" class="game">
    <h2>✂️ 神秘迷宮</h2>
    <div class="description">
      選一條路，剪開綵帶看看終點藏著什麼！
    </div>

    <div class="ribbon-maze" id="ribbonMaze"></div>

    <div class="result" id="ribbonResult">
      🗺️ 哪一條路會通往寶藏？
    </div>

    <button class="reset-btn" onclick="createRibbons()">
      🔄 重新開始
    </button>
  </section>


  <!-- ======================
       4. 打怪
  ======================= -->

  <section id="monsterGame" class="game">
    <h2>🐉 打怪任務</h2>
    <div class="description">
      每完成一小段作業，就攻擊一次！
    </div>

    <div class="monster" id="monster">🐲</div>

    <div class="hp-box">
      <div class="hp-label">
        ❤️ 怪獸體力：<span id="hpText">10</span> / 10
      </div>

      <div class="hp-bar">
        <div class="hp" id="hpBar"></div>
      </div>
    </div>

    <div class="attack-buttons">
      <button class="attack-btn" onclick="attack(1)">
        ✏️ 寫 1 段
      </button>

      <button class="attack-btn" onclick="attack(2)">
        ✏️ 寫 2 段
      </button>

      <button class="attack-btn" onclick="attack(3)">
        ✏️ 寫 3 段
      </button>
    </div>

    <div class="result" id="monsterResult">
      💪 加油！打敗怪獸就過關！
    </div>

    <button class="reset-btn" onclick="resetMonster()">
      🔄 重新開始
    </button>
  </section>


  <!-- ======================
       5. 火車
  ======================= -->

  <section id="trainGame" class="game">
    <h2>🚂 作業小火車</h2>
    <div class="description">
      完成一小段，就讓火車前進一站！
    </div>

    <div class="train-map">

      <div class="track"></div>

      <div class="stations">
        <div class="station"></div>
        <div class="station"></div>
        <div class="station"></div>
        <div class="station"></div>
        <div class="station"></div>
      </div>

      <div class="train" id="train">
        🚂
      </div>

    </div>

    <div class="result" id="trainResult">
      🏁 火車準備出發！
    </div>

    <div style="text-align:center;">
      <button class="main-btn" onclick="trainForward()">
        ✏️ 我完成一段了！
      </button>
    </div>

    <button class="reset-btn" onclick="resetTrain()">
      🔄 回到起點
    </button>
  </section>


  <!-- ======================
       6. 小動物
  ======================= -->

  <section id="petGame" class="game">
    <h2>🐣 小動物養成</h2>
    <div class="description">
      完成作業，就來餵餵你的小夥伴！
    </div>

    <div class="pet" id="pet">
      🥚
    </div>

    <div class="pet-message" id="petMessage">
      完成第一個任務，就可以孵化小蛋！
    </div>

    <div class="food-buttons">

      <button class="food-btn" onclick="feedPet('🍎')">
        🍎
      </button>

      <button class="food-btn" onclick="feedPet('🥕')">
        🥕
      </button>

      <button class="food-btn" onclick="feedPet('🍓')">
        🍓
      </button>

      <button class="food-btn" onclick="feedPet('🌽')">
        🌽
      </button>

    </div>

    <div class="result" id="petResult">
      🌱 今天要讓小夥伴長大一點嗎？
    </div>

    <button class="reset-btn" onclick="resetPet()">
      🔄 重新開始
    </button>
  </section>

</div>


<script>

/* =====================================================
   共用設定
===================================================== */

const encouragements = [
  "🌈 你可以的！",
  "⭐ 小小一步，也很厲害！",
  "💪 加油！",
  "🐾 勇敢往前走！",
  "🎉 這次一定可以！",
  "❤️ 慢慢來，你做得到！"
];

function randomEncouragement() {
  return encouragements[
    Math.floor(Math.random() * encouragements.length)
  ];
}

function showResult(element, number) {

  if (number === 0) {

    element.innerHTML =
      "🍀 幸運休息站！<br>" +
      "<strong>休息 1 分鐘 🎉</strong>";

  } else {

    element.innerHTML =
      randomEncouragement() +
      "<br><strong>這次寫 " +
      number +
      " 個字！✏️</strong>";

  }
}


/* =====================================================
   分頁
===================================================== */

function showGame(id, button) {

  document.querySelectorAll(".game")
    .forEach(game => game.classList.remove("active"));

  document.querySelectorAll(".tab")
    .forEach(tab => tab.classList.remove("active"));

  document.getElementById(id)
    .classList.add("active");

  button.classList.add("active");
}


/* =====================================================
   1. 轉盤
===================================================== */

const wheel = document.getElementById("wheelElement");
const wheelNumbers =
  document.querySelectorAll(".wheel-number");

const sector = 360 / 7;

/*
  每個數字放在自己的扇形中心。
  0 的中心 = 正上方。
*/
wheelNumbers.forEach((number, index) => {

  const angle =
    -90 + index * sector;

  const radius = 39;

  const x =
    Math.cos(angle * Math.PI / 180) * radius;

  const y =
    Math.sin(angle * Math.PI / 180) * radius;

  number.style.transform =
    `translate(${x * 1.8}px, ${y * 1.8}px)`;
});


let wheelRotation = 0;
let spinning = false;

function spinWheel() {

  if (spinning) return;

  spinning = true;

  const number =
    Math.floor(Math.random() * 7);

  /*
    每一格 51.428°。
    轉到指定數字時，
    讓該數字的中心回到正上方。
  */

  const targetRotation =
    wheelRotation
    - number * sector
    + 360 * 5;

  wheelRotation = targetRotation;

  wheel.style.transform =
    `rotate(${wheelRotation}deg)`;

  setTimeout(() => {

    showResult(
      document.getElementById("wheelResult"),
      number
    );

    spinning = false;

  }, 4100);
}


/* =====================================================
   2. 神