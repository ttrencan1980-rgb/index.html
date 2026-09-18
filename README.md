<!DOCTYPE html>
<html lang="sk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Elektro Testy App</title>
    <!-- Nastavenia pre aplikáciu na ploche telefónu -->
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="theme-color" content="#2563eb">
    <style>
        * { box-sizing: border-box; }
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
        <h1>⚡ Elektro Testy App</h1>
        <p>Vyberte si kategóriu a test</p>
    </header>

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

<script>
    // Generovanie tlačidiel pre 12 testov v každej kategórii
    const testsPerCategory = 12;

    function renderCategory(containerId, prefix, titlePrefix) {
        const container = document.getElementById(containerId);
        for (let i = 1; i <= testsPerCategory; i++) {
            const link = document.createElement('a');
            link.href = `${prefix}-${i}.html`;
            link.className = 'btn-test';
            link.innerHTML = `
                <span>${titlePrefix} ${i}</span>
                <span class="arrow">▶</span>
            `;
            container.appendChild(link);
        }
    }

    // Vytvorenie zoznamov pre všetky 3 okruhy
    renderCategory('list-rt', 'test-rt', 'Test RT');
    renderCategory('list-proj', 'test-proj', 'Test PROJ');
    renderCategory('list-lps', 'test-lps', 'Test LPS');
</script>

</body>
</html>
