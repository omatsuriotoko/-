# <!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>文化祭日程まとめ</title>
  <style>
    body {
      font-family: sans-serif;
      text-align: center;
      margin: 0;
      background-color: #fff;
      overflow-x: hidden;
    }

    .hidden {
      display: none;
    }

    #virusScreen {
      background-color: black;
      color: red;
      height: 100vh;
      overflow: hidden;
      position: relative;
      padding: 20px;
      animation: flash 0.5s infinite;
    }

    @keyframes flash {
      0% { background-color: black; }
      50% { background-color: red; }
      100% { background-color: black; }
    }

    .flashing {
      font-size: 24px;
      font-weight: bold;
      margin-top: 30px;
      z-index: 10;
      position: relative;
    }

    #popupContainer {
      position: absolute;
      top: 0; left: 0;
      width: 100%;
      height: 100%;
      z-index: 1;
    }

    .popup {
      position: absolute;
      background: yellow;
      border: 2px solid red;
      padding: 6px;
      font-size: 12px;
      z-index: 1;
      animation: shake 0.2s infinite alternate;
    }

    @keyframes shake {
      from { transform: translate(0px, 0px); }
      to { transform: translate(2px, 2px); }
    }

    #countdown {
      font-size: 24px;
      margin-top: 30px;
      z-index: 10;
      position: relative;
      background: red;
      color: white;
      padding: 10px;
      display: inline-block;
    }

    #scheduleTable {
      margin: 20px auto;
      border-collapse: collapse;
      width: 80%;
    }

    #scheduleTable th, #scheduleTable td {
      border: 1px solid #999;
      padding: 8px;
    }

    button {
      padding: 10px 20px;
      margin: 10px;
      font-size: 16px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <div id="intro">
    <h1>文化祭<br>日程まとめサイト</h1>
    <button id="viewSchedule">日程を見る</button>
  </div>

  <div id="virusScreen" class="hidden">
    <div class="flashing">⚠️ スマホがウィルスに感染しました！⚠️</div>
    <audio id="alarm" loop autoplay>
      <source src="https://soundeffect-lab.info/sound/various/mp3/soundeffect-lab-warning4.mp3" type="audio/mpeg">
    </audio>
    <div id="popupContainer"></div>
    <div id="countdown">解除まで: 5</div>
  </div>

  <div id="reveal" class="hidden">
    <h2>演出でした</h2>
    <p>これは文化祭の演出です。スマホに影響はありません。</p>
    <button id="toSchedule">予定表を表示する</button>
  </div>

  <div id="schedule" class="hidden">
    <h2>文化祭日程</h2>
    <div>
      <button onclick="showSchedule(1)">1日目</button>
      <button onclick="showSchedule(2)">2日目</button>
    </div>
    <table id="scheduleTable">
      <thead>
        <tr><th>時間</th><th>内容</th></tr>
      </thead>
      <tbody id="scheduleBody"></tbody>
    </table>
  </div>

  <script>
    const intro = document.getElementById("intro");
    const virusScreen = document.getElementById("virusScreen");
    const reveal = document.getElementById("reveal");
    const schedule = document.getElementById("schedule");
    const viewScheduleBtn = document.getElementById("viewSchedule");
    const toScheduleBtn = document.getElementById("toSchedule");
    const countdown = document.getElementById("countdown");
    const popupContainer = document.getElementById("popupContainer");
    const scheduleBody = document.getElementById("scheduleBody");

    const scheduleData = {
      1: [
        ["9:00", "開会式"],
        ["10:00", "講演会"],
        ["11:30", "展示"],
        ["12:00", "昼休み"],
        ["16:00", "ステージ発表"]
      ],
      2: [
        ["9:00", "模擬店オープン"],
        ["10:30", "発表会"],
        ["13:00", "ダンスコンテスト"],
        ["15:00", "閉会式"]
      ]
    };

    function showSchedule(day) {
      scheduleBody.innerHTML = "";
      scheduleData[day].forEach(row => {
        const tr = document.createElement("tr");
        row.forEach(cell => {
          const td = document.createElement("td");
          td.textContent = cell;
          tr.appendChild(td);
        });
        scheduleBody.appendChild(tr);
      });
    }

    if (localStorage.getItem("visited")) {
      intro.classList.add("hidden");
      schedule.classList.remove("hidden");
      showSchedule(1);
    } else {
      viewScheduleBtn.addEventListener("click", () => {
        intro.classList.add("hidden");
        virusScreen.classList.remove("hidden");

        const msg = new SpeechSynthesisUtterance("警告！スマホがウィルスに感染しました。データが消去されます。");
        window.speechSynthesis.speak(msg);

        // ポップアップを最大50個表示
        let popupCount = 0;
        const maxPopups = 50;
        const popupInterval = setInterval(() => {
          if (popupCount >= maxPopups) {
            clearInterval(popupInterval);
            return;
          }
          const popup = document.createElement("div");
          popup.className = "popup";
          popup.innerText = "ERROR!";
          popup.style.top = Math.random() * 90 + "%";
          popup.style.left = Math.random() * 90 + "%";
          popupContainer.appendChild(popup);
          popupCount++;
        }, 50);

        // カウントダウン処理
        let seconds = 5;
        countdown.innerText = `解除まで: ${seconds}`;
        const interval = setInterval(() => {
          seconds--;
          countdown.innerText = `解除まで: ${seconds}`;
          if (seconds === 0) {
            clearInterval(interval);
            clearInterval(popupInterval);
            virusScreen.classList.add("hidden");
            reveal.classList.remove("hidden");
          }
        }, 1000);
      });

      toScheduleBtn.addEventListener("click", () => {
        reveal.classList.add("hidden");
        schedule.classList.remove("hidden");
        showSchedule(1);
        localStorage.setItem("visited", "true");
      });
    }
  </script>
</body>
</html>
