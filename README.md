# workout-timer
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>筋トレ計測＆インターバルタイマー</title>
  <style>
    body {
      font-family: sans-serif;
      max-width: 480px;
      margin: 40px auto;
      padding: 0 16px;
      background: #f5f7fa;
      color: #222;
    }
    .card {
      background: #fff;
      border-radius: 12px;
      padding: 20px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.08);
      margin-bottom: 16px;
    }
    label { display: block; font-size: 14px; margin-top: 10px; }
    input {
      width: 100%;
      padding: 8px;
      margin-top: 4px;
      box-sizing: border-box;
      border: 1px solid #ccc;
      border-radius: 6px;
      font-size: 16px;
    }
    .timer {
      font-size: 48px;
      font-weight: bold;
      text-align: center;
      margin: 16px 0;
      color: #0b5ed7;
    }
    .status { text-align: center; font-size: 16px; margin-bottom: 12px; }
    button {
      width: 100%;
      padding: 12px;
      font-size: 16px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      margin-top: 8px;
      background: #0b5ed7;
      color: #fff;
    }
    button.secondary { background: #6c757d; }
    ul { padding-left: 20px; }
  </style>
</head>
<body>
  <h2>筋トレ計測＆インターバル</h2>

  <div class="card">
    <label>種目名
      <input id="exercise" type="text" value="ベンチプレス" />
    </label>
    <label>重量（kg）
      <input id="weight" type="number" value="60" step="2.5" />
    </label>
    <label>目標回数（回）
      <input id="reps" type="number" value="10" min="1" />
    </label>
    <label>目標セット数
      <input id="targetSets" type="number" value="3" min="1" />
    </label>
    <label>インターバル（秒）
      <input id="restSec" type="number" value="60" min="5" />
    </label>
  </div>

  <div class="card">
    <div class="status" id="setInfo">完了セット: 0 / 3</div>
    <div class="timer" id="timerDisplay">01:00</div>
    <button id="completeSetBtn">1セット完了 → 休憩スタート</button>
    <button id="resetBtn" class="secondary">リセット</button>
  </div>

  <div class="card">
    <h3>本日の記録</h3>
    <ul id="logList"></ul>
  </div>

  <script>
    let completedSets = 0;
    let timerId = null;
    let remainingSec = 60;

    const timerDisplay = document.getElementById("timerDisplay");
    const setInfo = document.getElementById("setInfo");
    const logList = document.getElementById("logList");

    function formatTime(sec) {
      const m = String(Math.floor(sec / 60)).padStart(2, "0");
      const s = String(sec % 60).padStart(2, "0");
      return `${m}:${s}`;
    }

    function updateSetInfo() {
      const target = Number(document.getElementById("targetSets").value) || 1;
      setInfo.textContent = `完了セット: ${completedSets} / ${target}`;
    }

    document.getElementById("restSec").addEventListener("input", (e) => {
      if (timerId !== null) return;
      remainingSec = Math.max(1, Number(e.target.value) || 60);
      timerDisplay.textContent = formatTime(remainingSec);
    });

    document.getElementById("targetSets").addEventListener("input", updateSetInfo);

    document.getElementById("completeSetBtn").addEventListener("click", () => {
      const exercise = document.getElementById("exercise").value || "種目未設定";
      const weight = document.getElementById("weight").value;
      const reps = document.getElementById("reps").value;
      const rest = Math.max(1, Number(document.getElementById("restSec").value) || 60);

      completedSets += 1;
      updateSetInfo();

      const li = document.createElement("li");
      li.textContent = `${completedSets}セット目: ${exercise} ${weight}kg × ${reps}回`;
      logList.appendChild(li);

      clearInterval(timerId);
      remainingSec = rest;
      timerDisplay.textContent = formatTime(remainingSec);

      timerId = setInterval(() => {
        remainingSec -= 1;
        timerDisplay.textContent = formatTime(Math.max(0, remainingSec));
        if (remainingSec <= 0) {
          clearInterval(timerId);
          timerId = null;
          alert("休憩終了！次のセットへ行きましょう！");
        }
      }, 1000);
    });

    document.getElementById("resetBtn").addEventListener("click", () => {
      clearInterval(timerId);
      timerId = null;
      completedSets = 0;
      remainingSec = Math.max(1, Number(document.getElementById("restSec").value) || 60);
      timerDisplay.textContent = formatTime(remainingSec);
      updateSetInfo();
      logList.innerHTML = "";
    });

    updateSetInfo();
    timerDisplay.textContent = formatTime(remainingSec);
  </script>
</body>
</html>
