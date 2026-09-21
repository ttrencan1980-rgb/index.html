<!DOCTYPE html>
<html lang="sk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Aplikácia Elektro Testy</title>
  <style>
    * { 
      box-sizing: border-box; 
    }
    body { 
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; 
      background-color: #f8fafc; 
      margin: 0; 
      padding: 16px; 
      display: flex; 
      flex-direction: column; 
      align-items: center; 
    }
    .container { 
      max-width: 600px; 
      width: 100%; 
    }
    header { 
      text-align: center; 
      margin-bottom: 24px; 
    }
    h1 { 
      color: #0f172a; 
      font-size: 24px; 
      margin: 0 0 6px 0; 
    }
    p { 
      color: #64748b; 
      font-size: 14px; 
      margin: 0; 
    }

    /* Štýly pre zámok / prihlasovacie okno */
    .lock-screen {
      background: #ffffff;
      border-radius: 16px;
      padding: 30px 20px;
      text-align: center;
      box-shadow: 0 4px 15px rgba(0,0,0,0.08);
      border: 1px solid #e2e8f0;
      margin-bottom: 20px;
    }
    .lock-screen h2 {
      margin-top: 0;
      color: #0f172a;
    }
    .pass-input {
      padding: 12px;
      font-size: 18px;
      letter-spacing: 4px;
      font-weight: bold;
      border: 1.5px solid #cbd5e1;
      border-radius: 8px;
      width: 100%;
      max-width: 200px;
      margin-bottom: 12px;
      text-align: center;
    }
    .btn-unlock {
      background-color: #2563eb;
      color: white;
      border: none;
      padding: 12px 24px;
      font-size: 15px;
      font-weight: 600;
      border-radius: 8px;
      cursor: pointer;
      transition: background 0.2s;
    }
    .btn-unlock:hover {
      background-color: #1d4ed8;
    }
    .error-msg {
      color: #ef4444;
      font-size: 13px;
      margin-top: 10px;
      display: none;
    }
    .key-placeholder {
      margin-top: 20px;
      font-size: 14px;
      color: #64748b;
      font-weight: 600;
      letter-spacing: 2px;
    }

    /* Štýly pre sekcie a kategórie */
    .category-card {
      background: #ffffff;
      border-radius: 16px;
      padding: 20px;
      margin-bottom: 20px;
      box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05), 0 2px 4px -1px rgba(0, 0, 0, 0.03);
      border: 1px solid #e2e8f0;
    }
    .category-header {
      display: flex;
      align-items: center;
      margin-bottom: 16px;
      padding-bottom: 10px;
      border-bottom: 2px solid #f1f5f9;
    }
    .category-title {
      font-size: 18px;
      font-weight: 700;
      color: #1e293b;
      margin: 0;
    }
    .badge-cat {
      font-size: 11px;
      font-weight: 700;
      text-transform: uppercase;
      padding: 4px 8px;
      border-radius: 6px;
      margin-right: 10px;
    }
    .cat-rt { background-color: #dbeafe; color: #1e40af; }
    .cat-proj { background-color: #fef3c7; color: #92400e; }
    .cat-lps { background-color: #dcfce7; color: #166534; }

    /* Mriežka tlačidiel pre testy */
    .grid {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 10px;
    }
    @media (max-width: 400px) {
      .grid {
        grid-template-columns: 1fr;
      }
    }
    .btn-test {
      display: flex;
      align-items: center;
      justify-content: space-between;
      background-color: #f8fafc;
      color: #334155;
      text-decoration: none;
      padding: 12px 14px;
      border-radius: 10px;
      font-weight: 600;
      font-size: 14px;
      border: 1px solid #cbd5e1;
      transition: all 0.15s ease;
    }
    .btn-test:hover {
      background-color: #f1f5f9;
      border-color: #94a3b8;
    }
    .btn-test:active {
      background-color: #e2e8f0;
      transform: scale(0.97);
    }
    .arrow {
      color: #94a3b8;
      font-size: 12px;
    }
  </style>
</head>
<body>

<div class="container">
  <header>
    <h1>⚡ Aplikácia Elektro Testy</h1>
    <p>Vyberte si kategóriu a test</p>
  </header>

  <!-- FORMULÁR NA ZADANIE HESLA -->
  <div id="lock-screen" class="lock-screen">
    <h2>🔒 Prístup je zamknutý</h2>
    <p style="margin-bottom: 15px;">Zadajte 4-miestny kód pre tento týždeň:</p>
    <input type="password" id="pass-input" class="pass-input" maxlength="4" placeholder="••••">
    <br>
    <button onclick="checkPassword()" class="btn-unlock">Odomknúť testy</button>
    <div id="error-msg" class="error-msg">Nesprávny kód! Skúste znova.</div>
    <br>
    <div class="key-placeholder">🔑 Týždenný kód: ****</div>
  </div>

  <!-- OBSAH TESTOV (Skrytý až do odomknutia) -->
  <div id="content-screen" style="display: none;">

    <!-- 1. KATEGÓRIA: REVÍZNY TECHNIK (RT) -->
    <div class="category-card">
      <div class="category-header">
        <span class="badge-cat cat-rt">§ 24</span>
        <h2 class="category-title">Revízny Technik (RT)</h2>
      </div>
      <div class="grid" id="list-rt"></div>
    </div>

    <!-- 2. KATEGÓRIA: PROJEKTANT (PROJ) -->
    <div class="category-card">
      <div class="category-header">
        <span class="badge-cat cat-proj">Projektovanie</span>
        <h2 class="category-title">Projektant (PROJ)</h2>
      </div>
      <div class="grid" id="list-proj"></div>
    </div>

    <!-- 3. KATEGÓRIA: OCHRANA PRED BLESKOM (LPS) -->
    <div class="category-card">
      <div class="category-header">
        <span class="badge-cat cat-lps">STN EN 62305</span>
        <h2 class="category-title">Bleskozvody (LPS)</h2>
      </div>
      <div class="grid" id="list-lps"></div>
    </div>

  </div>
</div>

<script>
  // TAJNÉ ZÁKLADNÉ ČÍSLO (Môžeš si ho zmeniť na akékoľvek číslo, napr. 2000, 5500...)
  const TAJNE_BISLO = 1000;

  // Funkcia na výpočet čísla aktuálneho týždňa v roku
  function getWeekNumber(d) {
    d = new Date(Date.UTC(d.getFullYear(), d.getMonth(), d.getDate()));
    d.setUTCDate(d.getUTCDate() + 4 - (d.getUTCDay() || 7));
    var yearStart = new Date(Date.UTC(d.getUTCFullYear(), 0, 1));
    return Math.ceil((((d - yearStart) / 86400000) + 1) / 7);
  }

  // Vypočíta 4-miestny kód: TAJNE_BISLO + číslo týždňa
  function getWeeklyPassword() {
    const tyzden = getWeekNumber(new Date());
    return String(TAJNE_BISLO + tyzden);
  }

  // Kontrola po načítaní stránky
  window.onload = function() {
    const savedPass = localStorage.getItem("elektro_test_auth");
    if (savedPass === getWeeklyPassword()) {
      unlockContent();
    }
  };

  function checkPassword() {
    const inputPass = document.getElementById("pass-input").value.trim();
    if (inputPass === getWeeklyPassword()) {
      localStorage.setItem("elektro_test_auth", getWeeklyPassword());
      unlockContent();
    } else {
      document.getElementById("error-msg").style.display = "block";
    }
  }

  function unlockContent() {
    document.getElementById("lock-screen").style.display = "none";
    document.getElementById("content-screen").style.display = "block";
    renderAllCategories();
  }

  // Generovanie tlačidiel pre 12 testov v každej sekcii
  const testyPerKategoria = 12;

  function renderCategory(containerId, prefix, titlePrefix) {
    const container = document.getElementById(containerId);
    if (!container) return;
    container.innerHTML = '';

    for (let i = 1; i <= testyPerKategoria; i++) {
      const link = document.createElement('a');
      link.href = `${prefix}-${i}.html`;
      link.className = 'btn-test';
      link.innerHTML = `<span>${titlePrefix} ${i}</span> <span class="arrow">▶</span>`;
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
