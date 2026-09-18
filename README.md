     <!DOCTYPE html>
<html lang="sk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Revízny technik aplikácia</title>
    <style>
        :root {
            --bg-color: #f4f6f9;
            --card-bg: #ffffff;
            --primary: #1e3a8a;
            --primary-hover: #1d4ed8;
            --success: #15803d;
            --success-bg: #dcfce7;
            --danger: #b91c1c;
            --danger-bg: #fee2e2;
            --text: #1f2937;
            --border: #e5e7eb;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
        }

        .container {
            max-width: 750px;
            width: 100%;
            background: var(--card-bg);
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.08);
            padding: 30px;
        }

        header {
            border-bottom: 2px solid var(--border);
            padding-bottom: 15px;
            margin-bottom: 25px;
            text-align: center;
        }

        h1 {
            color: var(--primary);
            margin: 0 0 5px 0;
            font-size: 24px;
        }

        .subtitle {
            color: #6b7280;
            font-size: 14px;
        }

        .progress-bar {
            height: 8px;
            background-color: #e5e7eb;
            border-radius: 4px;
            overflow: hidden;
            margin-bottom: 20px;
        }

        .progress-fill {
            height: 100%;
            background-color: var(--primary);
            width: 0%;
            transition: width 0.3s ease;
        }

        .status-container {
            display: flex;
            justify-content: space-between;
            font-weight: 600;
            margin-bottom: 20px;
            font-size: 14px;
        }

        .question-text {
            font-size: 18px;
            font-weight: 600;
            margin-bottom: 20px;
            line-height: 1.4;
        }

        .options-grid {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .option-btn {
            background-color: #fff;
            border: 2px solid var(--border);
            border-radius: 8px;
            padding: 14px 18px;
            text-align: left;
            font-size: 15px;
            cursor: pointer;
            transition: all 0.2s ease;
            display: flex;
            align-items: flex-start;
            gap: 10px;
        }

        .option-btn:hover:not(:disabled) {
            border-color: var(--primary);
            background-color: #eff6ff;
        }

        .option-btn.correct {
            border-color: var(--success);
            background-color: var(--success-bg);
            color: var(--success);
            font-weight: 600;
        }

        .option-btn.incorrect {
            border-color: var(--danger);
            background-color: var(--danger-bg);
            color: var(--danger);
            font-weight: 600;
        }

        .controls {
            margin-top: 25px;
            display: flex;
            justify-content: flex-end;
        }

        .next-btn {
            background-color: var(--primary);
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 6px;
            font-size: 15px;
            font-weight: 600;
            cursor: pointer;
            display: none;
        }

        .next-btn:hover {
            background-color: var(--primary-hover);
        }

        .result-screen {
            text-align: center;
            display: none;
        }

        .score-box {
            font-size: 42px;
            font-weight: bold;
            color: var(--primary);
            margin: 20px 0;
        }

        .restart-btn {
            background-color: var(--primary);
            color: white;
            border: none;
            padding: 14px 28px;
            border-radius: 6px;
            font-size: 16px;
            cursor: pointer;
        }
    </style>
</head>
<body>

<div class="container">
    <header>
        <h1>Revízny technik aplikácia</h1>
        <div class="subtitle">Elektrotechnika a normy STN – Test Tomáš (40 otázok)</div>
    </header>

    <div id="quiz-screen">
        <div class="progress-bar">
            <div class="progress-fill" id="progress"></div>
        </div>
        
        <div class="status-container">
            <span id="question-count">Otázka 1 / 40</span>
            <span id="score-count">Skóre: 0</span>
        </div>

        <div class="question-text" id="question">Načítavam otázku...</div>

        <div class="options-grid" id="options">
            <!-- Možnosti budú vygenerované cez JS -->
        </div>

        <div class="controls">
            <button class="next-btn" id="next-btn" onclick="nextQuestion()">Ďalšia otázka</button>
        </div>
    </div>

    <div class="result-screen" id="result-screen">
        <h2>Test bol dokončený!</h2>
        <p>Vaše dosiahnuté skóre:</p>
        <div class="score-box" id="final-score">0 / 40</div>
        <p id="feedback-text"></p>
        <button class="restart-btn" onclick="restartQuiz()">Spustiť test znova</button>
    </div>
</div>

<script>
const quizData = [
    { q: "1) Kolektívne ochranné zariadenia", options: ["a) sú základným a jediným prvkom ochranných zariadení", "b) majú byť uprednostňované pred individuálnymi", "c) používajú sa vtedy, ak individuálnymi ochrannými zariadeniami nie je možné zaručiť minimálne riziko poškodenia zdravia"], answer: 1 },
    { q: "2) Revízny technik", options: ["a) nemôže riadiť činnosť na VTZ elektrickom", "b) môže len vykonávať revízie v rozsahu osvedčenia", "c) môže riadiť a vykonávať činnosť na VTZ elektrickom a vykonávať revízie v rozsahu osvedčenia"], answer: 2 },
    { q: "3) Aké ochranné opatrenie je najčastejšie uplatňované v elektrických inštaláciách", options: ["a) samočinné odpojenie od napájania", "b) dvojitá alebo zosilnená izolácia", "c) elektrické oddelenie"], answer: 0 },
    { q: "4) Akým spôsobom môže byť dosiahnutá ochrana pri jednej poruche", options: ["a) ďalšou ochranou nezávislou od ochrany pred preťažením", "b) ďalšou ochranou nezávislou od základnej ochrany", "c) ďalšou ochranou nezávislou od ochrany RCD"], answer: 1 },
    { q: "5) Ako je špecifikovaná doplnková ochrana", options: ["a) ako zvýšená ochrana živej časti", "b) časť ochranného opatrenia za určitých podmienok vonkajších vplyvov a v určitých osobitných priestoroch", "c) ako kombinácia opatrenia na ochranu pred nebezpečným dotykovým napätím a nezávislého opatrenia na ochranu pri poruche"], answer: 1 },
    { q: "6) Akú izoláciu musí spĺňať PEN vodič v spoločnom obložení", options: ["a) musí mať rovnakú izoláciu ako krajné vodiče", "b) musí mať polovičnú izoláciu ako krajné vodiče", "c) musí mať zdvojenú izoláciu oproti krajným vodičom"], answer: 0 },
    { q: "7) Akú podmienku musí u sieti TN spĺňať impedancia slučky za rešpektovania bezpečnostného súčiniteľa", options: ["a) Zs ≤ Ia x U0", "b) Zs x Ia ≤ U0", "c) Zs x Ia ≥ U0"], answer: 1 },
    { q: "8) Kedy sa môže použiť ochranné opatrenie polohou (umiestnenie mimo dosah)", options: ["a) v inštaláciách ktoré sú prístupné osobám bez elektrotechnickej kvalifikácie", "b) v inštaláciách ktoré sú prístupné len znalým, alebo poučeným osobám", "c) v inštaláciách ktoré sú prístupné laikom"], answer: 1 },
    { q: "9) Aké sú hranice bezpečného malého napätia krytov izolovaných od živých častí, v priestoroch zvlášť nebezpečných, ak dochádza pri obsluhe k dotyku častí zariadenia", options: ["a) 12 V striedavé a 25 V jednosmerné napätie", "b) 30 V striedavé a 50 V jednosmerné napätie", "c) 20 V striedavé a 60 V jednosmerné napätie"], answer: 0 },
    { q: "10) Čo musí byť splnené pri ochrannom opatrení elektrickým oddelením", options: ["a) Oddelené obvody musia byť napájané zo zdroja aspoň s jednoduchým oddelením, pričom jeho napätie nepresahuje 750 V", "b) Oddelené obvody musia byť napájané zo zdroja aspoň s jednoduchým oddelením, pričom jeho napätie nepresahuje 500 V", "c) Oddelené obvody musia byť napájané zo zdroja aspoň s jednoduchým oddelením, pričom jeho napätie nepresahuje 250 V"], answer: 1 },
    { q: "11) Istenie vedenia svetelného obvodu musí istiť obvod proti preťaženiu a skratu s menovitým prúdom istiaceho prvku maximálne", options: ["a) 20 A", "b) 25 A", "c) 16 A"], answer: 2 },
    { q: "12) Samostatný istený obvod sa musí zriadiť pre pevne inštalované spotrebiče s príkonom od", options: ["a) 3000 VA", "b) 1200 VA", "c) 2000 VA"], answer: 2 },
    { q: "13) Aký minimálny stupeň ochrany musi mať elektroinštalačné kanály na pracovnom stroji", options: ["a) IP44", "b) IP20", "c) vhodný na ich použitie"], answer: 2 },
    { q: "14) Koľko vodičov ochranného obvodu pracovného stroja sa môže pripojiť k jednej svorke", options: ["a) je to dané prierezom vodičov", "b) ľubovoľný počet", "c) jeden ochranný vodič"], answer: 2 },
    { q: "15) Aký spôsob overenia spojitosti ochranného obvodu pracovného stroja sa odporúča v zmysle STN EN 60204-12019", options: ["a) meraním odporu medzi svorkou PE a príslušnými bodmi, ktoré sú súčasťou ochranného pospájania", "b) meraním odporu medzi svorkou PEN a príslušnými bodmi, ktoré sú súčasťou ochranného pospájania", "c) meraním odporu medzi svorkou PE v rozvádzači, z ktorého je stroj napojený a príslušnými bodmi, ktoré sú súčasťou ochranného pospájania"], answer: 0 },
    { q: "16) Akú požiadavku musí spĺňať hlavný vypínač, ak je ako hlavný vypínač použitý istič vhodný pre odpojenie", options: ["a) nemôže sa použiť", "b) dodávať sa s prostriedkami umožňujúcimi zablokovanie v polohe VYPNUTÉ", "c) môže sa použiť iba s vhodnou charakteristikou"], answer: 1 },
    { q: "17) Ako musia byť istené všetky neuzemnené vodiče obvodov miestneho osvetlenia na pracovnom stroji", options: ["a) zariadeniami na ochranu pred nadprúdom, spolu so zariadeniami pre ostatné obvody", "b) zariadeniami na ochranu pred skratom, oddelenými od zariadení, ktoré chránia ostatné obvody", "c) zariadeniami na ochranu pred nadprúdom, oddelenými od zariadení, ktoré chránia ostatné obvody"], answer: 2 },
    { q: "18) Elektrické zariadenie umiestnené v zóne 1 – plavárne musí mať min. krytie", options: ["a) IP X6", "b) IP X5", "c) IP X4"], answer: 2 },
    { q: "19) V zdravotníckych priestoroch skupín 1 a 2", options: ["a) Všetky vodiče PEN použité v jednej miestnosti musia byť pripojené na rovnakú prípojnicu ochranného uzemnenia", "b) Všetky vodiče doplnkového ochranného pospájania použité v jednej miestnosti musia byť pripojené na rovnakú prípojnicu ochranného pospájania", "c) Všetky vodiče PEN použité v jednej miestnosti musia vzájomne prepojiť"], answer: 1 },
    { q: "20) Obvody so zásuvkami do 32 A na staveniskách", options: ["a) sa musia chrániť prúdovým chráničom s vypínacím prúdom do 30 mA len v prípade ak nie je možné dosiahnuť dotykové napätie 25 V", "b) sa nemusia chrániť prúdovým chráničom s vypínacím prúdom do 30 mA ak je použitá ochrana SELV", "c) sa musia chrániť prúdovým chráničom s vypínacím prúdom do 30 mA len v prípade ak nie je možné dosiahnuť dotykové napätie 60 V"], answer: 1 },
    { q: "21) V poľnohospodárskych objektoch z dôvodu ochrany pred požiarom sa musí inštalovať prúdový chránič s vypínacím prúdom do", options: ["a) 0,1 A", "b) 0,5 A", "c) 0,3 A"], answer: 2 },
    { q: "22) Pri vedeniach do 1 kV vzdialenosť živej časti vedenia od hladiny riek (nie plavebné toky) pri normálnom vodnom stave je min.", options: ["a) 5 m", "b) 4 m", "c) 6 m"], answer: 2 },
    { q: "23) V distribučných sieťach TN je dovolený čas odpojenia vo výnimočných prípadoch nepresahujúci", options: ["a) 30 s", "b) 2 s", "c) 5 s"], answer: 2 },
    { q: "24) V distribučných sieťach TN je dovolený čas odpojenia nepresahujúci", options: ["a) 0,4s", "b) 2s", "c) 5s"], answer: 2 },
    { q: "25) Ktoré ochranné opatrenie je možné použiť na strane jednosmerného napätia u FVE zdrojov", options: ["a) elektrické oddelenie", "b) malé napätie (SELV a PELV)", "c) samočinné odpojenie od napájania"], answer: 1 },
    { q: "26) Koľko zvodov je potrebné pri neizolovanom LPS v zmysle STN EN 62305-3", options: ["a) doporučujú sa použiť minimálne dva zvody", "b) musia sa použiť minimálne dva zvody", "c) pre objekt s obvodom max 40m a výškou do 30m môže sa použiť jeden zvod"], answer: 1 },
    { q: "27) Aký typ uzemňovacej sústavy sa uprednostňuje v stavbách, kde sú inštalované elektronické systémy", options: ["a) A", "b) C", "c) B"], answer: 2 },
    { q: "28) Pri ekvipotencionálnom pospojovaní sa využíva zapojenie do hviezdy S, alebo zapojenie mrežové M. Ako musí byť zapojený elektronický systém do siete vyrovnania potenciálov pri konfigurácii S", options: ["a) iba jednou prípojnicou na vyrovnanie potenciálu", "b) dvoma prípojnicami na vyrovnanie potenciálu", "c) max dvoma prípojnicami na vyrovnanie potenciálu"], answer: 0 },
    { q: "29) Pre aké prípady je vhodný návrh umiestnenia zachytávacej sústavy metódou ochranného uhla", options: ["a) pre rovinné plochy", "b) pre všetky", "c) pre jednoduché tvary budov obmedzených výškou budovy určených triedou LPS"], answer: 2 },
    { q: "30) Normy rady STN EN 62305 využívajú v celom rozsahu skrátené názvy. Čo znamená označenie LEMP", options: ["a) Elektromagnetický impulz vyvolaný bleskom", "b) Zóna ochrany pred bleskom", "c) Úroveň ochrany pred bleskom"], answer: 0 },
    { q: "31) Hodnota izolačného odporu spotrebiča s triedou ochrany I držaných počas prevádzky v ruke musí byť minimálne", options: ["a) 1 MΩ", "b) 1,5 MΩ", "c) 2 MΩ"], answer: 2 },
    { q: "32) Elektrické spotrebiče, alebo predlžovacie prívody zaradené do skupiny C podľa oblasti ich používania sú:", options: ["a) Spotrebiče a/alebo predlžovacie prívody používané vo vnútorných verejne prístupných priestoroch (napr. zdravotnícke objekty, objekty sociálnych služieb)", "b) Spotrebiče a/alebo predlžovacie prívody používané vo vnútorných priestoroch (napr. obchodnej činnosti, spotrebiče v kuchynkách a pod.)", "c) Spotrebiče a/alebo predlžovacie prívody používané vo vonkajšom prostredí (napr. na stavbách, pri poľnohospodárskych prácach a pod.)"], answer: 1 },
    { q: "33) Pravidelná revízia predlžovacieho prívodu bez mechanického namáhania zaradeného do skupiny E podľa STN 33 1630 sa vykonáva", options: ["a) 1 x za 6 mesiacov", "b) 1 x za 12 mesiacov", "c) 1 x za 24 mesiacov"], answer: 2 },
    { q: "34) Hodnota izolačného odporu spotrebiča s triedou ochrany III držaných počas prevádzky v ruke musí byť minimálne", options: ["a) 1 MΩ", "b) 1,5 MΩ", "c) 0,25 MΩ"], answer: 2 },
    { q: "35) Pravidelná revízia elektrických spotrebičov alebo predlžovacích prívodov bez mechanického namáhania zaradených do skupiny B podľa STN 33 1630 sa vykonáva", options: ["a) 1 x za 6 mesiacov", "b) 1 x za 12 mesiacov", "c) 1 x za 24 mesiacov"], answer: 0 },
    { q: "36) Spojitosť ochranného vodiča sa meria:", options: ["a) Medzi ochrannou uzemňovacou svorkou zariadenia (prípadne kontaktu v sieťovej vidlici) a každou prístupnou vodivou časťou, ktorá je priamo pripojená k ochrannej zemi.", "b) Medzi svorkou prívodu stredného vodiča spotrebiča (prípadne kontaktu v sieťovej vidlici) a každou fázou spotrebiča.", "c) Medzi svorkou prívodu fázy zariadenia (prípadne kontaktu v sieťovej vidlici) a každou prístupnou vodivou časťou, ktorá je priamo pripojená k ochrannej zemi."], answer: 0 },
    { q: "37) Prehliadka (inspection) je", options: ["a) realizácia opatrení v el. inštalácii, ktoré preukážu jej spôsobilosť", "b) súbor všetkých opatrení, ktorými sa kontroluje súlad kompletnej el. inštalácie s harmonizačnými dokumentami", "c) kontrola el. inštalácie s použitím všetkých zmyslov za účelom zistenia správnosti výberu a zhotovenia el. inštalácie"], answer: 2 },
    { q: "38) Revízia (verification) je", options: ["a) realizácia opatrení v el. inštalácii, ktoré preukážu jej spôsobilosť", "b) súbor všetkých opatrení, ktorými sa kontroluje súlad kompletnej el. inštalácie s harmonizačnými dokumentami", "c) súbor, kontrola el. inštalácie použitím všetkých zmyslov potrebných na zistenie správnosti výberu a zhotovenia el. inštalácie"], answer: 1 },
    { q: "39) Kedy sa nesmie prevádzkovať revízia systému ochranných opatrení LPMS", options: ["a) ak hrozí búrka", "b) ak je bleskozvod nový", "c) ak je bleskozvod starší ako 10 rokov"], answer: 0 },
    { q: "40) Kedy sa musí prevádzkovať revízia systému ochranných opatrení LPMS", options: ["a) počas búrky", "b) v priebehu inštalácie SPM, po inštalácii SPM, periodicky, po akýchkoľvek zmenách súčastí patriacich k SPM, po zásahu blesku do stavby nie je potrebná", "c) v priebehu inštalácie SPM, po inštalácii SPM, periodicky, po akýchkoľvek zmenách súčastí patriacich k SPM, prípadne po zásahu blesku do stavby"], answer: 2 },
];

let currentQuestion = 0;
let score = 0;
let answered = false;

function loadQuestion() {
    answered = false;
    const data = quizData[currentQuestion];
    
    document.getElementById("question-count").innerText = `Otázka ${currentQuestion + 1} / ${quizData.length}`;
    document.getElementById("score-count").innerText = `Skóre: ${score}`;
    document.getElementById("question").innerText = data.q;
    document.getElementById("progress").style.width = `${((currentQuestion) / quizData.length) * 100}%`;
    document.getElementById("next-btn").style.display = "none";

    const optionsContainer = document.getElementById("options");
    optionsContainer.innerHTML = "";

    data.options.forEach((opt, index) => {
        const button = document.createElement("button");
        button.className = "option-btn";
        button.innerText = opt;
        button.onclick = () => selectOption(index, button);
        optionsContainer.appendChild(button);
    });
}

function selectOption(selectedIndex, btn) {
    if (answered) return;
    answered = true;

    const data = quizData[currentQuestion];
    const buttons = document.querySelectorAll(".option-btn");

    if (selectedIndex === data.answer) {
        btn.classList.add("correct");
        score++;
        document.getElementById("score-count").innerText = `Skóre: ${score}`;
    } else {
        btn.classList.add("incorrect");
        buttons[data.answer].classList.add("correct");
    }

    buttons.forEach(b => b.disabled = true);
    document.getElementById("next-btn").style.display = "block";
}

function nextQuestion() {
    currentQuestion++;
    if (currentQuestion < quizData.length) {
        loadQuestion();
    } else {
        showResults();
    }
}

function showResults() {
    document.getElementById("quiz-screen").style.display = "none";
    document.getElementById("result-screen").style.display = "block";
    document.getElementById("final-score").innerText = `${score} / ${quizData.length}`;
    
    const percentage = (score / quizData.length) * 100;
    let feedback = "";
    if (percentage >= 90) {
        feedback = "Vynikajúci výsledok! Ste skvele pripravený.";
    } else if (percentage >= 75) {
        feedback = "Veľmi dobrý výsledok. Niekoľko detailov ešte môžete prejsť.";
    } else {
        feedback = "Odporúčame zopakovať si naskenované materiály a skúsiť test znova.";
    }
    document.getElementById("feedback-text").innerText = feedback;
}

function restartQuiz() {
    currentQuestion = 0;
    score = 0;
    document.getElementById("quiz-screen").style.display = "block";
    document.getElementById("result-screen").style.display = "none";
    loadQuestion();
}

// Spustenie pri načítaní
loadQuestion();
</script>

</body>
</html>

 id: 3,
      question: "Ako sa dá definovať základné pravidlo ochrany pred úrazom elektrickým prúdom",
      norm: "STN EN 61140:2018",
      options: [
        "A) nebezpečné živé časti smú byť prístupné a prístupné vodivé časti smú byť nebezpečnými živými časťami, buď pri normálnych podmienkach, alebo v stave s jednou poruchou",
        "B) nebezpečné živé časti nesmú byť prístupné a prístupné vodivé časti nesmú byť nebezpečnými živými časťami, buď pri normálnych podmienkach, alebo v stave s jednou poruchou",
        "C) bezpečné neživé časti nesmú byť prístupné a prístupné nevodivé časti nesmú byť nebezpečnými živými časťami..."
      ],
      correct: 1 // B (Označené na fotke 1)
    },
    {
      id: 4,
      question: "Čím je zaistené ochranné opatrenie pomocou ochrany dvojitou alebo zosilnenou izoláciou",
      norm: "STN EN 61140:2018",
      options: [
        "A) základná ochrana sa zabezpečuje základnou izoláciou nebezpečných živých častí a ochrana pri poruche sa zabezpečuje prístrojmi RCD",
        "B) základná ochrana sa zabezpečuje základnou izoláciou nebezpečných živých častí a ochrana pri poruche sa zabezpečuje prídavnou izoláciou",
        "C) základná ochrana sa zabezpečuje samočinným odpojením a ochrana pri poruche sa zabezpečuje prídavnou izoláciou"
      ],
      correct: 1 // B (Označené na fotke 2)
    },
    {
      id: 5,
      question: "Aká je maximálna doba odpojenia pre koncové obvody neprekračujúce 32 A v sieťach TN 3x230/400V AC",
      norm: "STN 33 2000-4-41:2019",
      options: [
        "A) 0,2 s",
        "B) 0,4 s",
        "C) 5 s"
      ],
      correct: 1 // B (Označené na fotke 2)
    },
    {
      id: 6,
      question: "Musí mať obvod napájajúci jednotlivé zariadenia triedy ochrany II ochranný vodič vedený ku každému bodu inštalácie a každému bodu pripojenia",
      norm: "STN 33 2000-4-41:2019",
      options: [
        "A) nie",
        "B) áno",
        "C) nie ak zariadenia triedy ochrany II sú pripojené na obvod cez zásuvku"
      ],
      correct: 1 // B (Označené na fotke 2)
    },
    {
      id: 7,
      question: "Medzi prostriedky základnej ochrany patrí",
      norm: "STN EN 61140:2018",
      options: [
        "A) ochrana RCD",
        "B) ochrana pred preťažením",
        "C) izolácia"
      ],
      correct: 2 // C (Označené na fotke 3)
    },
    {
      id: 8,
      question: "Akú hodnotu nesmie u ochrany nevodivým okolím prekročiť odpor izolujúcich podláh a stien v každom bode merania pri napätí inštalácie do 500V",
      norm: "STN 33 2000-4-41:2019",
      options: [
        "A) 50 kΩ",
        "B) 150 kΩ",
        "C) 100 kΩ"
      ],
      correct: 0 // A (Označené na fotke 3)
    },
    {
      id: 9,
      question: "Môžu byť živé časti elektricky oddeleného obvodu spojené s iným obvodom alebo so zemou",
      norm: "STN 33 2000-4-41:2019",
      options: [
        "A) nesmú",
        "B) môžu ak sa nespoja s PE vodičom",
        "C) môžu"
      ],
      correct: 0 // A (Označené na fotke 3)
    },
    {
      id: 10,
      question: "Zvislá inštalačná zóna šírky 200 mm zóna zvislá okenná (ZS-o) je",
      norm: "STN 33 2130:1983",
      options: [
        "A) od 300 mm do 500 mm vedľa okenného hrubej stavby",
        "B) od 200 mm do 400 mm vedľa okenného hrubej stavby",
        "C) od 100 mm do 300 mm vedľa okenného otvoru hrubej stavby"
      ],
      correct: 2 // C (Označené na fotke 4)
    },
    {
      id: 11,
      question: "Úbytok napätia pre svetelný odber v rozvode medzi rozvádzačom za elektromerom a spotrebičom nemá byť väčší ako",
      norm: "STN 33 2130:1983",
      options: [
        "A) 4%",
        "B) 3%",
        "C) 2%"
      ],
      correct: 2 // C (Označené na fotke 4)
    },
    {
      id: 12,
      question: "Aké krytie je prípustné u rozvádzača pre vnútorné inštalácie do miest prístupných laickej obsluhe",
      norm: "STN EN 61439-3:2012",
      options: [
        "A) IP20",
        "B) IPXXB",
        "C) IP2XC"
      ],
      correct: 2 // C (Označené na fotke 4)
    },
    {
      id: 13,
      question: "Ako musí byť zaistené ovládanie z viac než jednej ovládacej jednotky obsluhy",
      norm: "STN EN 60204-1:2019",
      options: [
        "A) blokovaním",
        "B) musia sa zabezpečiť také opatrenia, aby aktivovanie príkazov z rôznych ovládacích jednotiek nespôsobilo nebezpečnú situáciu",
        "C) nie je to možné"
      ],
      correct: 1 // B (Označené na fotke 5)
    },
    {
      id: 14,
      question: "Ktorý údaj nemusí byť uvedený na štítku rozvádzača, pokiaľ je uvedený v technickej dokumentácii výrobcu",
      norm: "STN EN 61439-1:2012",
      options: [
        "A) označenie príslušnej normy IEC 61439-X",
        "B) menovitý prúd rozvádzača",
        "C) meno výrobcu rozvádzača alebo ochranná známka"
      ],
      correct: 1 // B (Označené na fotke 5)
    },
    {
      id: 15,
      question: "Aký účel má použitie obvodu PELV u pracovného stroja",
      norm: "STN EN 60204-1:2019",
      options: [
        "A) ochrana osôb pred úrazom el. prúdom pri dotyku neživých častí vypnutím stroja v prípade poškodenia základnej izolácie",
        "B) zabráni zapnutiu stroja v prípade poškodenia základnej izolácie živej časti obvodu",
        "C) ochrana osôb pred úrazom el. prúdom pri dotyku neživých častí a pri obmedzenom plošnom dotyku živých častí"
      ],
      correct: 2 // C (Označené na fotke 5)
    },
    {
      id: 16,
      question: "Aké musí spĺňať podmienky kryt rozvádzača, ktorý je použitý ako časť ochranného vodiča",
      norm: "STN EN 61439-1:2012",
      options: [
        "A) ak je z nevodivého materiálu musí sa izolovať od ochranného vodiča",
        "B) ak je z vodivého materiálu musí sa izolovať od ochranného vodiča",
        "C) ak je z vodivého materiálu nemusí sa izolovať od ochranného vodiča"
      ],
      correct: 2 // C (Označené na fotke 6)
    },
    {
      id: 17,
      question: "Elektrické zariadenie umiestnené v zóne 2 – plavárne musí mať min. krytie",
      norm: "STN 33 2000-7-702:2019",
      options: [
        "A) IP X4 pri umiestnení vo vnútri",
        "B) IP X3 pri umiestnení vo vnútri",
        "C) IP X2 pri umiestnení vo vnútri"
      ],
      correct: 0 // A (Označené na fotke 6)
    },
    {
      id: 18,
      question: "Ak sú zásuvky v zdravotníckych priestoroch napájané zo záložného napájacieho zdroja",
      norm: "STN 33 2000-7-710:2013",
      options: [
        "A) musia byť ľahko identifikovateľné",
        "B) musia byť oddelené od ostatných",
        "C) musia mať krytie minimálne IP44"
      ],
      correct: 0 // A (Označené na fotke 6)
    },
    {
      id: 19,
      question: "Obvody so zásuvkami do 32 A na staveniskách",
      norm: "STN 33 2000-7-704:2018",
      options: [
        "A) sa musia chrániť prúdovým chráničom s vypínacím prúdom do 30 mA len v prípade ak nie je možné dosiahnuť dotykové napätie 60 V",
        "B) sa musia chrániť prúdovým chráničom s vypínacím prúdom do 30 mA len v prípade ak nie je možné dosiahnuť dotykové napätie 25 V",
        "C) sa nemusia chrániť prúdovým chráničom s vypínacím prúdom do 30 mA ak je použitá ochrana SELV"
      ],
      correct: 2 // C (Označené na fotke 7)
    },
    {
      id: 20,
      question: "Tepelné žiariče v poľnohospodárskych objektoch sa musia inštalovať od hospodárskych zvierat a od horľavého materiálu, ak výrobca nešpecifikoval väčšiu vzdialenosť zariadenia v návode na použitie aspoň vo vzdialenosti",
      norm: "STN 33 2000-7-705:2007",
      options: [
        "A) 0,5 m",
        "B) 1,0 m",
        "C) 0,8 m"
      ],
      correct: 1 // B (Označené na fotke 7)
    },
    {
      id: 21,
      question: "V križovatkovom poli môže byť",
      norm: "STN 33 3300:1983",
      options: [
        "A) najviac 2 spojky pre AlFe do 50 mm²",
        "B) najviac 1 spojka",
        "C) nesmie byť spojka"
      ],
      correct: 1 // B (Označené na fotke 7)
    },
    {
      id: 22,
      question: "Pri prípojkách s holými vodičmi do 1 kV vzdialenosť živej časti vedenia nad schodnou časťou budovy je min.",
      norm: "STN 33 3300:1983",
      options: [
        "A) 3 m",
        "B) 4 m",
        "C) 2 m"
      ],
      correct: 0 // A (Označené na fotke 8)
    },
    {
      id: 23,
      question: "Pri revízii DC strany FVE je potrebné vykonať pre všetky FVE",
      norm: "STN EN 62446-1:2016",
      options: [
        "A) Skúšky kategórie 1",
        "B) Skúšky kategórie 2",
        "C) Dodatkové skúšky"
      ],
      correct: 0 // A (Označené na fotke 8)
    },
    {
      id: 24,
      question: "Aké riziko je povolené tam, kde údery blesku zahrňujú straty na ľudských životoch",
      norm: "STN EN 62305-2:2013",
      options: [
        "A) 10⁻⁵",
        "B) 10⁻⁴",
        "C) 10⁻³"
      ],
      correct: 0 // A (Označené na fotke 8)
    },
    {
      id: 25,
      question: "Aký je prípustný vrcholový uhol pre vyšetrenie zachytávacej sústavy pri metóde pomocou ochranného uhlu",
      norm: "STN EN 62305-3:2012",
      options: [
        "A) podľa triedy LPS a výšky objektu",
        "B) podľa triedy LEMPS a výšky objektu",
        "C) podľa triedy LEMP a výšky objektu"
      ],
      correct: 0 // A (Označené na fotke 9)
    },
    {
      id: 26,
      question: "Koľko zvodov je nutné inštalovať u izolovaného (oddialeného) LPS, ak je zberacia sústava tvorená zo zavesených lán",
      norm: "STN EN 62305-3:2012",
      options: [
        "A) na každé lano 1 zvod",
        "B) na každú konštrukciu 2 zvody",
        "C) na každú konštrukciu 1 zvod"
      ],
      correct: 2 // C (Označené na fotke 9)
    },
    {
      id: 27,
      question: "Čo zahŕňa výpočet pri oceňovaní rizika stavby",
      norm: "STN EN 62305-2:2013",
      options: [
        "A) vlastnú stavbu, stavebnú inštaláciu, obsah v stavbe, osoby v stavbe alebo osoby stojace v zóne do 3m od vonkajška stavby, prostredie ovplyvnené poškodením stavby",
        "B) vlastnú stavbu, stavebnú inštaláciu, obsah v stavbe, osoby v stavbe alebo osoby stojace v zóne do 3m od vonkajška stavby, prostredie ovplyvnené poškodením stavby, pripojené inžinierske siete k stavbe",
        "C) vlastnú stavbu, stavebnú inštaláciu, obsah v stavbe, osoby nachádzajúce sa v stavbe, prostredie ovplyvnené poškodením stavby, pripojené inžinierske siete k stavbe"
      ],
      correct: 1 // B (Označené na fotke 9)
    },
    {
      id: 28,
      question: "Skúšobné napätie el. inštalácie s menovitým napätím FELV je",
      norm: "STN 33 2000-6:2018",
      options: [
        "A) 250 V AC",
        "B) 500 V DC",
        "C) 250 V DC"
      ],
      correct: 1 // B (Označené na fotke 10)
    },
    {
      id: 29,
      question: "Meranie prúdu v ochrannom vodiči",
      norm: "STN EN 50699:2022",
      options: [
        "A) sa musí merať na spotrebiči s triedou ochrany II 

      const correctEl = document.getElementById(`opt-container-${index}-${item.correct}`);
      correctEl.classList.add('correct-answer');

      if (selectedOption) {
        const userChoice = parseInt(selectedOption.value);
        if (userChoice === item.correct) {
          score++;
        } else {
          const wrongEl = document.getElementById(`opt-container-${index}-${userChoice}`);
          wrongEl.classList.add('wrong-answer');
        }
      }
    });

    const resultDiv = document.getElementById('result-container');
    resultDiv.style.display = 'block';
    resultDiv.innerHTML = `Dosiahli ste skóre: ${score} z ${quizData.length} bodov (${Math.round((score / quizData.length) * letter: "A", text: "elektrická sieť, ktorá má iba jeden priamo uzemnený bod zdroja a neživé časti inštalácie sú spojené s uzemňovačmi, ktoré sú elektricky nezávislé od uzemňovača napájacej siete.", status: "correct" },
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
            question: "Symbolom U0 pri vyhodnocovaní im
