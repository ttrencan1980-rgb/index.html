<!DOCTYPE html>
<html lang="sk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Revízny technik - Testovacia aplikácia</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 20px;
      background-color: #f4f6f9;
      color: #333;
    }

    #lock-screen {
      max-width: 420px;
      margin: 60px auto;
      padding: 30px;
      background: #ffffff;
      border-radius: 12px;
      box-shadow: 0 4px 20px rgba(0,0,0,0.15);
      text-align: center;
    }

    #lock-screen h2 {
      margin-top: 0;
      color: #0056b3;
    }

    #lock-screen input {
      width: 100%;
      padding: 12px;
      margin: 18px 0;
      border: 2px solid #ddd;
      border-radius: 8px;
      font-size: 1.2em;
      text-align: center;
      box-sizing: border-box;
      outline: none;
    }

    #lock-screen input:focus {
      border-color: #0056b3;
    }

    #lock-screen button {
      width: 100%;
      padding: 12px;
      background-color: #0056b3;
      color: white;
      border: none;
      border-radius: 8px;
      font-size: 1.1em;
      font-weight: bold;
      cursor: pointer;
    }

    #lock-screen button:hover {
      background-color: #003d80;
    }

    .error-msg {
      color: #d9534f;
      margin-top: 15px;
      font-weight: bold;
      display: none;
    }

    #app-content {
      display: none;
      max-width: 900px;
      margin: 0 auto;
      background: #ffffff;
      padding: 25px;
      border-radius: 12px;
      box-shadow: 0 2px 10px rgba(0,0,0,0.08);
    }

    .header-title {
      border-bottom: 2px solid #0056b3;
      padding-bottom: 10px;
      margin-bottom: 25px;
      color: #0056b3;
    }

    .categories-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 20px;
      margin-top: 20px;
    }

    .category-card {
      border: 1px solid #e0e0e0;
      border-radius: 8px;
      padding: 15px;
      background: #fafafa;
    }

    .category-card h3 {
      margin-top: 0;
      color: #0056b3;
    }

    .test-list {
      list-style: none;
      padding: 0;
      margin: 0;
    }

    .test-list li {
      padding: 8px 0;
      border-bottom: 1px solid #eee;
    }
  </style>
</head>
<body>

  <div id="lock-screen">
    <h2>Prístup k testom</h2>
    <p>Pre vstup do aplikácie zadajte aktuálny týždenný kód od správcu.</p>
    
    <input type="password" id="access-code-input" placeholder="Zadajte 4-miestny kód">
    <button onclick="checkWeeklyCode()">Odomknúť aplikáciu</button>
    
    <div id="error-message" class="error-msg">Nesprávny kód! Vyžiadajte si platný kód pre tento týždeň.</div>
  </div>

  <div id="app-content">
    <h1 class="header-title">Aplikácia pre elektrotechnikov a revíznych technikov</h1>
    
    <div class="categories-grid">
      <div class="category-card">
        <h3>1. Revízny technik (RT)</h3>
        <ul class="test-list">
          <li>Test 1 (12 otázok)</li>
          <li>Test 2 (12 otázok)</li>
          <li>Test 3 (12 otázok)</li>
        </ul>
      </div>

      <div class="category-card">
        <h3>2. Projektant (Proj)</h3>
        <ul class="test-list">
          <li>Test 1 (12 otázok)</li>
          <li>Test 2 (12 otázok)</li>
          <li>Test 3 (12 otázok)</li>
        </ul>
      </div>

      <div class="category-card">
        <h3>3. Bleskozvody (Ochrana pred bleskom)</h3>
        <ul class="test-list">
          <li>Test 1 (12 otázok)</li>
          <li>Test 2 (12 otázok)</li>
          <li>Test 3 (12 otázok)</li>
        </ul>
      </div>
    </div>
  </div>

  <script>
    const MASTER_CODE = "2206"; // Tvoj trvalý hlavný kód

    // Nový stabilný matematický vzorec pre generovanie kódov
    function getWeeklyCode() {
      const now = new Date();
      const d = new Date(Date.UTC(now.getFullYear(), now.getMonth(), now.getDate()));
      const dayNum = d.getUTCDay() || 7;
      d.setUTCDate(d.getUTCDate() + 4 - dayNum);
      const yearStart = new Date(Date.UTC(d.getUTCFullYear(), 0, 1));
      const weekNo = Math.ceil((((d - yearStart) / 86400000) + 1) / 7);
      const year = d.getUTCFullYear();

      // Mathematický vzorec (Hash kód na 4 cifry)
      let rawVal = (weekNo * 7393 + year * 17) % 9000 + 1000;
      return String(rawVal);
    }

    const correctWeeklyCode = getWeeklyCode();

    function checkWeeklyCode() {
      const userInput = document.getElementById("access-code-input").value.trim();
      
      if (userInput === correctWeeklyCode || userInput === MASTER_CODE) {
        document.getElementById("lock-screen").style.display = "none";
        document.getElementById("app-content").style.display = "block";
      } else {
        document.getElementById("error-message").style.display = "block";
      }
    }

    document.getElementById("access-code-input").addEventListener("keypress", function(event) {
      if (event.key === "Enter") {
        checkWeeklyCode();
      }
    });
  </script>

</body>
</html>
