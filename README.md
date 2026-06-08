# escapesite
<!DOCTYPE html>
<html lang="nl">
<head>
  <meta charset="UTF-8">
  <title>Escape Portal</title>
  <style>
    body {
      margin: 0;
      min-height: 100vh;
      background: #101010;
      color: #e8e8e8;
      font-family: Arial, sans-serif;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .box {
      width: 90%;
      max-width: 600px;
      padding: 30px;
      background: #1b1b1b;
      border: 2px solid #444;
      border-radius: 12px;
      text-align: center;
    }

    h1 {
      margin-top: 0;
    }

    #timer {
      font-size: 48px;
      margin: 20px 0;
      font-weight: bold;
    }

    input {
      padding: 12px;
      width: 70%;
      max-width: 300px;
      font-size: 18px;
      text-align: center;
      text-transform: uppercase;
    }

    button {
      padding: 12px 18px;
      margin-top: 12px;
      font-size: 16px;
      cursor: pointer;
    }

    #missionArea {
      display: none;
    }

    #message {
      min-height: 24px;
      margin-top: 15px;
    }

    .unlocked {
      display: none;
      margin-top: 25px;
      padding: 15px;
      background: #263326;
      border: 1px solid #5c8a5c;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <div class="box">
    <h1>Escape Portal</h1>

    <div id="startArea">
      <p>Druk op start om de missie te beginnen.</p>
      <button onclick="startMission()">Start</button>
    </div>

    <div id="missionArea">
      <div id="timer">60:00</div>

      <p>Voer een code in:</p>
      <input id="codeInput" type="text" placeholder="CODE">
      <br>
      <button onclick="checkCode()">Ontgrendel</button>

      <p id="message"></p>

      <div id="secret1" class="unlocked">
        <h2>Code 1 ontgrendeld</h2>
        <p>Hier komt later je eerste filmpje of bericht.</p>
      </div>

      <div id="secret2" class="unlocked">
        <h2>Code 2 ontgrendeld</h2>
        <p>Hier komt later je tweede filmpje of bericht.</p>
      </div>
    </div>
  </div>

  <script>
    const totalMinutes = 60;
    let timerInterval;

    const codes = {
      "TEST1": "secret1",
      "TEST2": "secret2"
    };

    function startMission() {
      if (!localStorage.getItem("startTime")) {
        localStorage.setItem("startTime", Date.now());
      }

      document.getElementById("startArea").style.display = "none";
      document.getElementById("missionArea").style.display = "block";

      updateTimer();
      timerInterval = setInterval(updateTimer, 1000);
    }

    function updateTimer() {
      const startTime = Number(localStorage.getItem("startTime"));
      const now = Date.now();
      const elapsedSeconds = Math.floor((now - startTime) / 1000);
      const totalSeconds = totalMinutes * 60;
      const remaining = Math.max(totalSeconds - elapsedSeconds, 0);

      const minutes = Math.floor(remaining / 60);
      const seconds = remaining % 60;

      document.getElementById("timer").textContent =
        String(minutes).padStart(2, "0") + ":" + String(seconds).padStart(2, "0");

      if (remaining <= 0) {
        clearInterval(timerInterval);
        document.getElementById("message").textContent = "De tijd is verstreken.";
      }
    }

    function checkCode() {
      const input = document.getElementById("codeInput").value.trim().toUpperCase();
      const message = document.getElementById("message");

      if (codes[input]) {
        const elementId = codes[input];
        document.getElementById(elementId).style.display = "block";
        localStorage.setItem(elementId, "unlocked");
        message.textContent = "Code juist.";
        document.getElementById("codeInput").value = "";
      } else {
        message.textContent = "Code onjuist.";
      }
    }

    function restoreProgress() {
      if (localStorage.getItem("startTime")) {
        startMission();
      }

      Object.values(codes).forEach(function(elementId) {
        if (localStorage.getItem(elementId) === "unlocked") {
          document.getElementById(elementId).style.display = "block";
        }
      });
    }

    restoreProgress();
  </script>
</body>
</html>
