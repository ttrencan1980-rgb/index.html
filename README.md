<!DOCTYPE html>
<html lang="sk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Revízny technik - Testovacia aplikácia</title>
  <style>
    * {
      box-sizing: border-box;
    }
    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
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
      outline: none;
      letter-spacing: 4px;
      font-weight: bold;
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
      transition: background 0.2s;
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

    .key-placeholder {
      margin-top: 20px;
      font-size: 13px;
      color: #64748b;
      font-weight: 600;
      letter-spacing: 2px;
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
      grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
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
      border-bottom: 1px solid #ddd;
      padding-bottom: 8px;
    }

    .grid-tests {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 8px;
      margin-top: 12px;
    }

    .btn-test {
      display: flex;
      align-items: center;
      justify-content: space-between;
      background-color: #ffffff;
      color: #334155;
      text-decoration: none;
      padding: 10px 12px;
      border-radius: 6px;
      font-weight: 600;
      font-size: 13px;
      border: 1px solid #cbd5e1;
      transition: all 0.15s ease;
    }

    .btn-test:hover {
      background-color: #f1f5f9;
      border-color: #0056b3;
      color: #0056b3;
    }
  </style>
</head>
<body>

  <!-- OBRAZOVKA ZAMKNUTIA -->
  <div id="lock-screen">
    <h2>Prístup k testom</h2>
    <p>Pre vstup do aplikácie zadajte aktuálny týždenný kód od správcu.</p>
    
    <input type="password" id="access-code-input" maxlength="4" placeholder="••••">
    <button onclick="checkWeeklyCode()">Odomknúť aplikáciu</button>
    
    <div id="error-message" class="error-msg">Nesprávny kód! Vyžiadajte si platný kód pre tento týždeň.</div>
    <div class="key-placeholder">🔑 Týždenný kód: ****</div>
  </div>

  <!-- HLAVNÝ OBSAH APKY -->
  <div id="app-content">
    <h1 class="header-title">⚡ Aplikácia pre elektrotechnikov a revíznych technikov</h1>
    
    <div class="categories-grid">
      <!-- 1. KATEGÓRIA: RT -->
      <div class="category-card">
        <h3>1. Revízny technik (RT)</h3>
        <div class="grid-tests" id="list-rt"></div>
      </div>

      <!-- 2. KATEGÓRIA: PROJEKTANT -->
      <div class="category-card">
        <h3>2. Projektant (PROJ)</h3>
        <div class="grid-tests" id="list-proj"></div>
      </div>

      <!-- 3. KATEGÓRIA: BLESKOZVODY -->
      <div class="category-card">
        <h3>3. Bleskozvody (LPS)</h3>
        <div class="grid-tests" id="list-lps"></div>
      </div>
    </div>
  </div>

  <script>
    const MASTER_CODE = "2206"; // Tvoj trvalý hlavný kód (funguje VŽDY)

    // Funkcia na výpočet jednoduchého a presného týždenného kódu
    function getWeeklyCode() {
      const now = new Date();
      const d = new Date(Date.UTC(now.getFullYear(), now.getMonth(), now.getDate()));
      const dayNum = d.getUTCDay() || 7;
      d.setUTCDate(d.getUTCDate() + 4 - dayNum);
      const yearStart = new Date(Date.UTC(d.getUTCFullYear(), 0, 1));
      
      // Vypočíta číslo týždňa v roku (1 až 52)
      const weekNo = Math.ceil((((d - yearStart) / 86400000) + 1) / 7);

      // Kód = 1000 + číslo týždňa (napr. pre 39. týždeň je to 1039)
      return String(1000 + weekNo);
    }

    const correctWeeklyCode = getWeeklyCode();

    // Kontrola zapamätaného prihlásenia po načítaní
    window.onload = function() {
      const savedPass = localStorage.getItem("elektro_test_auth");
      if (savedPass === correctWeeklyCode || savedPass === MASTER_CODE) {
        unlockApp();
      }
    };

    function checkWeeklyCode() {
      const userInput = document.getElementById("access-code-input").value.trim();
      
      if (userInput === correctWeeklyCode || userInput === MASTER_CODE) {
        localStorage.setItem("elektro_test_auth", userInput);
        unlockApp();
      } else {
        document.getElementById("error-message").style.display = "block";
      }
    }

    function unlockApp() {
      document.getElementById("lock-screen").style.display = "none";
      document.getElementById("app-content").style.display = "block";
      renderAllCategories();
    }

    // Stlačenie klávesu Enter na klávesnici
    document.getElementById("access-code-input").addEventListener("keypress", function(event) {
      if (event.key === "Enter") {
        checkWeeklyCode();
      }
    });

    // Generovanie tlačidiel pre 12 testov v každej kategórii
    const testyPerKategoria = 12;

    function renderCategory(containerId, prefix, titlePrefix) {
      const container = document.getElementById(containerId);
      if (!container) return;
      container.innerHTML = '';

      for (let i = 1; i <= testyPerKategoria; i++) {
        const link = document.createElement('a');
        link.href = `${prefix}-${i}.html`;
        link.className = 'btn-test';
        link.innerHTML = `<span>${titlePrefix} ${i}</span> <span>▶</span>`;
        container.appendChild(link);
      }
    }

    function renderAllCategories() {
      renderCategory('list-rt', 'test-rt', 'Test RT');
      renderCategory('list-proj', 'test-proj', 'Test PROJ');
      renderCategory('list-lps', 'test-lps', 'Test LPS');
    }
  </script>

</body>
</html>
