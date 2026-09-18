# index.html
Test_RT
<!DOCTYPE html>
<html lang="sk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>test-LPS-1</title>
    <style>
        :root {
            --bg-color: #f4f6f9;
            --card-bg: #ffffff;
            --text-color: #1a202c;
            --norm-color: #718096;
            --correct-bg: #86efac;
            --correct-border: #22c55e;
            --incorrect-bg: #fca5a5;
            --incorrect-border: #ef4444;
            --neutral-border: #e2e8f0;
            --btn-bg: #2563eb;
            --btn-hover: #1d4ed8;
        }

        body {
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 98vh;
        }

        .presentation-container {
            width: 100%;
            max-width: 1100px;
            background: var(--card-bg);
            border-radius: 16px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.08);
            padding: 40px;
            box-sizing: border-box;
            display: flex;
            flex-direction: column;
            min-height: 650px;
            justify-content: space-between;
        }

        .header {
            border-bottom: 2px solid var(--neutral-border);
            padding-bottom: 15px;
            margin-bottom: 25px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .test-title {
            font-size: 1.5rem;
            font-weight: 700;
            color: var(--btn-bg);
            margin: 0;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .counter {
            font-size: 1.2rem;
            font-weight: 600;
            color: var(--norm-color);
            background: #edf2f7;
            padding: 6px 16px;
            border-radius: 20px;
        }

        .question-title {
            font-size: 1.8rem;
            font-weight: 700;
            line-height: 1.3;
            margin: 0 0 10px 0;
        }

        .norm-reference {
            font-size: 1.1rem;
            color: var(--norm-color);
            font-weight: 500;
            margin-bottom: 30px;
        }

        .options-container {
            display: flex;
            flex-direction: column;
            gap: 16px;
            margin-bottom: 30px;
        }

        .option {
            display: flex;
            align-items: center;
            padding: 18px 24px;
            border-radius: 12px;
            border: 2px solid var(--neutral-border);
            font-size: 1.25rem;
            line-height: 1.4;
            transition: all 0.2s ease;
            background-color: #fff;
        }

        .option-letter {
            font-weight: 700;
            margin-right: 20px;
            min-width: 30px;
        }

        .option.correct {
            background-color: var(--correct-bg);
            border-color: var(--correct-border);
            color: #064e3b;
            font-weight: 600;
        }

        .option.incorrect {
            background-color: var(--incorrect-bg);
            border-color: var(--incorrect-border);
            color: #7f1d1d;
        }

        .controls {
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-top: 2px solid var(--neutral-border);
            padding-top: 25px;
        }

        .btn {
            background-color: var(--btn-bg);
            color: white;
            border: none;
            padding: 14px 28px;
            font-size: 1.1rem;
            font-weight: 600;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.2s ease;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .btn:hover {
            background-color: var(--btn-hover);
        }

        .btn:disabled {
            background-color: #cbd5e1;
            cursor: not-allowed;
        }

        .btn-secondary {
            background-color: #e2e8f0;
            color: #475569;
        }

        .btn-secondary:hover {
            background-color: #cbd5e1;
        }
    </style>
</head>
<body>

<div class="presentation-container">
    <div>
        <div class="header">
            <h2 class="test-title">test-LPS-1</h2>
            <span class="counter" id="counter">Otázka 1 z 30</span>
        </div>

        <h1 class="question-title" id="question">Načítavam otázku...</h1>
        <div class="norm-reference" id="norm">---</div>

        <div class="options-container" id="options">
            <!-- Možnosti budú vložené dynamicky -->
        </div>
    </div>

    <div class="controls">
        <button class="btn btn-secondary" id="prevBtn" onclick="prevQuestion()">&#8592; Predchádzajúca</button>
        <button class="btn" id="nextBtn" onclick="nextQuestion()">Nasledujúca &#8594;</button>
    </div>
</div>

<script>
    const quizData = [
        {
            question: "Označenie rozvodnej siete TT znamená",
            norm: "STN 33 2000-1:2009",
            options: [
                { letter: "A", text: "elektrická sieť, ktorá má iba jeden priamo uzemnený bod zdroja a neživé časti inštalácie sú spojené s uzemňovačmi, ktoré sú elektricky nezávislé od uzemňovača napájacej siete.", status: "correct" },
                { letter: "B", text: "elektrická striedavá sieť s neuzemneným bodom zdroja. Neživé časti el. spotrebičov sú spojené so samostatnými uzemňovačmi nezávislými od zdroja", status: "neutral" },
                { letter: "C", text: "elektrická striedavá sieť s uzemneným bodom zdroja, pričom jeden bod je priamo uzemnený pri zdroji a neživé časti inštalácie sú spojené s týmto bodom prostredníctvom ochranných vodičov", status: "neutral" }
            ]
        },
        {
            question: "Zariadenie s impulzným výdržným napätím zodpovedajúcim kategórii prepätia IV je zariadenie",
            norm: "STN 33 2000-4-443:2017",
            options: [
                { letter: "A", text: "vhodné na použitie v napájacom bode elektrických inštalácií alebo v jeho blízkosti, napríklad pred hlavným domovým rozvádzačom", status: "correct" },
                { letter: "B", text: "určené na použitie v pevných elektrických inštaláciách v hlavnom domovom rozvádzači a za ním, s vysokým stupňom pohotovosti", status: "neutral" },
                { letter: "C", text: "vhodné na pripojenie na pevné elektrické inštalácie zabezpečujúce normálny stupeň pohotovosti zvyčajne vyžadovaný pre elektrické spotrebiče", status: "incorrect" }
            ]
        },
        {
            question: "Obsah projektovej dokumentácie ktorá sa predkladá k stavebnému konaniu určuje",
            norm: "vyhláška 453/2000 Z. z.",
            options: [
                { letter: "A", text: "vyhláška č. 508/2009 Z. z.", status: "neutral" },
                { letter: "B", text: "zákon č. 124/2006 Z. z.", status: "neutral" },
                { letter: "C", text: "vyhláška č. 453/2000 Z. z.", status: "correct" }
            ]
        },
        {
            question: "VTZ elektrické zaradené do skupiny A je",
            norm: "vyhláška 508/2009 Z. z.",
            options: [
                { letter: "A", text: "elektrická inštalácia v priestore s vonkajším vplyvom vody AD3 až AD8 vrátane ochrany pred účinkami atmosférickej elektriny", status: "neutral" },
                { letter: "B", text: "elektrická inštalácia v priestore s vonkajším vplyvom vody z iného zdroja ako z dažďa AD2 až AD8 vrátane ochrany pred účinkami atmosférickej elektriny", status: "neutral" },
                { letter: "C", text: "elektrická inštalácia v priestore s vonkajším vplyvom vody z iného zdroja ako z dažďa AD3 až AD8 vrátane ochrany pred účinkami atmosférickej elektriny", status: "correct" }
            ]
        },
        {
            question: "Elektrické zariadenia v priestoroch prístupných laikom a osobám poučeným musia mať vo všeobecnosti stupeň ochrany krytom aspoň",
            norm: "STN 33 2000-4-41:2019",
            options: [
                { letter: "A", text: "IP3X", status: "neutral" },
                { letter: "B", text: "IP2X", status: "correct" },
                { letter: "C", text: "IP4X", status: "neutral" }
            ]
        },
        {
            question: "Vyrábať, montovať na mieste budúcej prevádzky a rekonštruovať vyhradené technické zariadenie možno iba podľa konštrukčnej dokumentácie, ku ktorej bolo vydané odborné stanovisko OPOm podľa § 14 ods. 1 písm. d) zákona, ktorým sa posudzuje konštrukčná dokumentácia",
            norm: "vyhláška 508/2009 Z. z.",
            options: [
                { letter: "A", text: "pri VTZ elektrickom sa odborné stanovisko k dokumentácii vyžaduje okrem skupiny C", status: "neutral" },
                { letter: "B", text: "pri VTZ elektrickom sa odborné stanovisko k dokumentácii nevyžaduje", status: "neutral" },
                { letter: "C", text: "pri VTZ elektrickom sa odborné stanovisko k dokumentácii vyžaduje iba na technické zariadenie skupiny A", status: "correct" }
            ]
        },
        {
            question: "Označenie rozvodnej siete TN znamená",
            norm: "STN 33 2000-1:2009",
            options: [
                { letter: "A", text: "elektrická striedavá sieť s uzemneným bodom zdroja. Jeden bod priamo uzemnený pri zdroji neživé časti el. spotrebičov sú spojené s uzemňovačmi elektricky nezávislými od uzemňovacej siete", status: "neutral" },
                { letter: "B", text: "elektrická striedavá sieť s uzemneným bodom zdroja. Má iba jeden bod priamo uzemnený pri zdroji, neživé časti inštalácie sú spojené s týmto bodom prostredníctvom ochranných vodičov", status: "correct" },
                { letter: "C", text: "elektrická striedavá sieť s uzemneným bodom zdroja. Jeden bod priamo uzemnený pri zdroji neživé časti el. spotrebičov sú spojené so samostatnými uzemňovačmi nezávislými od zdroja", status: "neutral" }
            ]
        },
        {
            question: "V jednosmerných obvodoch vodiče",
            norm: "STN 33 2000-1:2009",
            options: [
                { letter: "A", text: "PEL resp. PEM nie sú živými vodičmi aj keď prenášajú pracovný prúd", status: "correct" },
                { letter: "B", text: "PEL resp. PEM nie sú živými vodičmi neprenášajú pracovný prúd", status: "neutral" },
                { letter: "C", text: "PEL resp. PEM sú živými vodičmi, prenášajú pracovný prúd", status: "incorrect" }
            ]
        },
        {
            question: "Symbolom U0 pri vyhodnocovaní impedančnej slučky označujeme",
            norm: "STN 33 2000-4-41:2019",
            options: [
                { letter: "A", text: "menovité združené napätie medzi krajnými vodičmi vo voltoch (V)", status: "neutral" },
                { letter: "B", text: "menovité napätie krajného vodiča proti zemi vo voltoch (V)", status: "correct" },
                { letter: "C", text: "menovité napätie krajného vodiča proti zemi vo voltoch (V) namerané pri zdroji v čase merania impedančnej slučky", status: "incorrect" }
            ]
        },
        {
            question: "Signálne svietidlo červené znamená",
            norm: "STN EN 60204-1:2019",
            options: [
                { letter: "A", text: "príkaz na činnosť", status: "neutral" },
                { letter: "B", text: "nebezpečenstvo", status: "correct" },
                { letter: "C", text: "výstrahu - varovanie", status: "neutral" }
            ]
        },
        {
            question: "Normy rady STN EN 62305 využívajú v celom rozsahu skrátené názvy. Čo znamená označenie LPL",
            norm: "STN EN 62305-1:2012",
            options: [
                { letter: "A", text: "Zóna ochrany pred bleskom", status: "neutral" },
                { letter: "B", text: "Systém ochrany pred bleskom", status: "neutral" },
                { letter: "C", text: "Úroveň ochrany pred bleskom", status: "correct" }
            ]
        },
        {
            question: "Normy rady STN EN 62305 využívajú v celom rozsahu skrátené názvy. Čo znamená označenie LEMP",
            norm: "STN EN 62305-1:2012",
            options: [
                { letter: "A", text: "Elektromagnetický impulz vyvolaný bleskom", status: "correct" },
                { letter: "B", text: "Úroveň ochrany pred bleskom", status: "neutral" },
                { letter: "C", text: "Zóna ochrany pred bleskom", status: "neutral" }
            ]
        },
        {
            question: "Ako je charakterizovaný čas trvania blesku T",
            norm: "STN EN 62305-1:2012",
            options: [
                { letter: "A", text: "čas, za ktorý v bode zásahu tečie bleskový prúd", status: "correct" },
                { letter: "B", text: "čas, za ktorý bleskový prúd preteká do zeme", status: "neutral" },
                { letter: "C", text: "čas za ktorý trvá bleskový prúd", status: "neutral" }
            ]
        },
        {
            question: "Normy rady STN EN 62305 využívajú v celom rozsahu skrátené názvy. Čo znamená označenie LPZ",
            norm: "STN EN 62305-1:2012",
            options: [
                { letter: "A", text: "Zóna ochrany pred bleskom", status: "correct" },
                { letter: "B", text: "Systém ochrany pred bleskom", status: "neutral" },
                { letter: "C", text: "Úroveň ochrany pred bleskom", status: "neutral" }
            ]
        },
        {
            question: "Ako sú označované základné typy škôd pri výpočtoch",
            norm: "STN EN 62305-2:2013",
            options: [
                { letter: "A", text: "D1 ľudské životy , D2 verejné služby , D3 porucha elektrických a elektronických systémov", status: "neutral" },
                { letter: "B", text: "D1 ľudské životy , D2 verejné služby , D3 kultúrne dedičstvo , D4 ekonomické", status: "incorrect" },
                { letter: "C", text: "D1 úraz živých bytostí , D2 hmotná škoda , D3 porucha elektrických a elektronických systémov", status: "correct" }
            ]
        },
        {
            question: "Aké riziko je povolené tam, kde údery blesku zahrňujú straty na ľudských životoch",
            norm: "STN EN 62305-2:2013",
            options: [
                { letter: "A", text: "10⁻⁵", status: "correct" },
                { letter: "B", text: "10⁻⁴", status: "neutral" },
                { letter: "C", text: "10⁻³", status: "neutral" }
            ]
        },
        {
            question: "Ako je definované riziko R a čo táto hodnota predstavuje",
            norm: "STN EN 62305-2:2013",
            options: [
                { letter: "A", text: "Pravdepodobné priemerné ročné straty", status: "correct" },
                { letter: "B", text: "Pravdepodobné priemerné straty po dobu životnosti", status: "incorrect" },
                { letter: "C", text: "Pravdepodobné priemerné straty za 100 rokov", status: "neutral" }
            ]
        },
        {
            question: "Aké základné typy strát rozoznávame na chránenom objekte",
            norm: "STN EN 62305-2:2013",
            options: [
                { letter: "A", text: "L1 úraz živých bytostí , L2 hmotná škoda , L3 porucha elektrických a elektronických systémov", status: "neutral" },
                { letter: "B", text: "L1 strata ľudských životov , L2 strata služby pre verejnosť , L3 strata kultúrneho dedičstva , L4 strata ekonomickej hodnoty", status: "correct" },
                { letter: "C", text: "L1 strata ľudských životov , L2 strata služby pre verejnosť , L3 strata kultúrneho dedičstva , L4 strata elektrických a elektronických systémov", status: "incorrect" }
            ]
        },
        {
            question: "Aké sú prípustné metódy pre stanovenie umiestnenia zachytávacej sústavy",
            norm: "STN EN 62305-3:2012",
            options: [
                { letter: "A", text: "metóda výšky budovy a jej umiestnenia", status: "neutral" },
                { letter: "B", text: "metóda valivej gule, ochranného uhla, mriežkovej sústavy", status: "correct" },
                { letter: "C", text: "iba metóda valivej gule", status: "neutral" }
            ]
        },
        {
            question: "Koľko zvodov je nutné inštalovať u neizolovaného (neoddialeného) LPS triedy III",
            norm: "STN EN 62305-3:2012",
            options: [
                { letter: "A", text: "na každých 10m", status: "neutral" },
                { letter: "B", text: "na každých 15m", status: "correct" },
                { letter: "C", text: "na každých 20m", status: "neutral" }
            ]
        },
        {
            question: "Pre aké prípady je vhodný návrh umiestnenia zachytávacej sústavy metódou mriežkovej sústavy",
            norm: "STN EN 62305-3:2012",
            options: [
                { letter: "A", text: "pre všetky", status: "neutral" },
                { letter: "B", text: "pre rovinné plochy", status: "correct" },
                { letter: "C", text: "pre jednoduché tvary budov obmedzených výškou budovy určenej triedou LPS", status: "neutral" }
            ]
        },
        {
            question: "Kde sa vykoná ekvipotenciálne pospájanie proti blesku vonkajších vodivých častí stavby",
            norm: "STN EN 62305-3:2012",
            options: [
                { letter: "A", text: "sa musí urobiť pri vonkajších kovových častiach čo možno najbližšie k vstupu chránenej stavby", status: "correct" },
                { letter: "B", text: "sa doporučuje urobiť pri vonkajších kovových častiach čo najďalej od vstupu chránenej stavby", status: "neutral" },
                { letter: "C", text: "sa musí urobiť pri vonkajších kovových častiach čo najďalej od vstupu chránenej stavby", status: "neutral" }
            ]
        },
        {
            question: "K zabráneniu nebezpečných rozdielov potenciálov medzi všetkými zariadeniami vo vnútri vnútorných LPZ je využívaná mriežková sústava. Aká je typická šírka ôk tejto mriežkovej sústavy",
            norm: "STN EN 62305-4:2013",
            options: [
                { letter: "A", text: "5 m", status: "correct" },
                { letter: "B", text: "15 m", status: "neutral" },
                { letter: "C", text: "10 m", status: "neutral" }
            ]
        },
        {
            question: "Vhodné uzemnenie a pospájanie je založené na kompletnom systéme uzemnení. Čo tento systém zahrňuje",
            norm: "STN EN 62305-4:2013",
            options: [
                { letter: "A", text: "kompletný systém spája uzemňovaciu sústavu a zachytávaciu sústavu", status: "neutral" },
                { letter: "B", text: "kompletný systém spája uzemňovaciu sústavu a sieť pospájania", status: "correct" },
                { letter: "C", text: "kompletný systém spája uzemňovaciu sústavu a zvodovú sústavu", status: "incorrect" }
            ]
        },
        {
            question: "Ako musí byť vykonané ekvipotencionálne pospájanie tam, kde je definovaná zóna ochrany (LPZ) pred bleskom",
            norm: "STN EN 62305-4:2013",
            options: [
                { letter: "A", text: "nemusí sa vykonať", status: "neutral" },
                { letter: "B", text: "cez prípojnice vyrovnania potenciálov čo najbližšie k vstupnému bodu LPZ", status: "correct" },
                { letter: "C", text: "cez prípojnice vyrovnania potenciálov čo najďalej od vstupného bodu LPZ", status: "neutral" }
            ]
        },
        {
            question: "Aké základné ochranné opatrenia v LPMS sa dajú zahrnúť medzi základné opatrenia proti LEMP",
            norm: "STN EN 62305-4:2013",
            options: [
                { letter: "A", text: "uzemnenie a pospájanie, magnetické tienenie a trasy vedení, koordinovaná LPS ochrana", status: "incorrect" },
                { letter: "B", text: "uzemnenie a pospájanie, magnetické tienenie a trasy vedení, koordinovaná SPD ochrana", status: "correct" },
                { letter: "C", text: "uzemnenie a pospájanie, magnetické tienenie a trasy vedení, koordinovaná LPL ochrana", status: "neutral" }
            ]
        },
        {
            question: "Po akých úsekoch by mali byť uzemnené alebo spojené s povrchovým alebo tyčovým zemničom nadzemné kovové potrubia mimo technologických zariadení",
            norm: "STN EN 62305-3:2012",
            options: [
                { letter: "A", text: "20 m", status: "neutral" },
                { letter: "B", text: "40 m", status: "neutral" },
                { letter: "C", text: "30 m", status: "correct" }
            ]
        },
        {
            question: "Akú metódu možno použiť pre stanovenie umiestnenia zachytávacej sústavy u stavieb s nebezpečenstvom výbuchu",
            norm: "STN EN 62305-3:2012",
            options: [
                { letter: "A", text: "metóda valivej gule", status: "correct" },
                { letter: "B", text: "metóda oddialeného LPS", status: "neutral" },
                { letter: "C", text: "metóda mriežkovej sústavy", status: "neutral" }
            ]
        },
        {
            question: "Vyžadujú nádrže a potrubia uložené v zemi inštaláciu zachytávacej sústavy",
            norm: "STN EN 62305-3:2012",
            options: [
                { letter: "A", text: "nie ak plášť nádrže z ocele hrúbky min 5 mm", status: "correct" },
                { letter: "B", text: "nie ak plášť nádrže z hliníka hrúbky min 5 mm", status: "neutral" },
                { letter: "C", text: "nie ak plášť nádrže z medi hrúbky min 5 mm", status: "neutral" }
            ]
        },
        {
            question: "Aké ďalšie dodatočné ochranné opatrenia sú nutné pri stavbách, ktoré majú definované priestory so zónami 2 a 22",
            norm: "STN EN 62305-3:2012",
            options: [
                { letter: "A", text: "nie sú nevyhnutné žiadne ďalšie dodatočné ochranné opatrenia", status: "correct" },
                { letter: "B", text: "je potrebná inštalácia oddeľovacích iskrišiek", status: "incorrect" },
                { letter: "C", text: "je potrebná inštalácia oddeľovacích iskrišiek a SPD", status: "neutral" }
            ]
        }
    ];

    let currentQuestionIndex = 0;

    function renderQuestion() {
        const q = quizData[currentQuestionIndex];
        
        document.getElementById("counter").innerText = `Otázka ${currentQuestionIndex + 1} z ${quizData.length}`;
        document.getElementById("question").innerText = q.question;
        document.getElementById("norm").innerText = q.norm;

        const optionsContainer = document.getElementById("options");
        optionsContainer.innerHTML = "";

        q.options.forEach(opt => {
            const div = document.createElement("div");
            div.className = `option ${opt.status}`;
            div.innerHTML = `<span class="option-letter">${opt.letter})</span> <span>${opt.text}</span>`;
            optionsContainer.appendChild(div);
        });

        document.getElementById("prevBtn").disabled = currentQuestionIndex === 0;
        document.getElementById("nextBtn").disabled = currentQuestionIndex === quizData.length - 1;
    }

    function nextQuestion() {
        if (currentQuestionIndex < quizData.length - 1) {
            currentQuestionIndex++;
            renderQuestion();
        }
    }

    function prevQuestion() {
        if (currentQuestionIndex > 0) {
            currentQuestionIndex--;
            renderQuestion();
        }
    }

    document.addEventListener('keydown', function(event) {
        if (event.key === "ArrowRight") {
            nextQuestion();
        } else if (event.key === "ArrowLeft") {
            prevQuestion();
        }
    });

    renderQuestion();
</script>

</body>
</html>
