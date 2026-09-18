<!DOCTYPE html>
<html lang="sk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Elektrotechnický Test (Podľa označených odpovedí)</title>
  <style>
    :root {
      --primary-color: #0056b3;
      --bg-color: #f4f6f8;
      --card-bg: #ffffff;
      --text-color: #333333;
      --correct-bg: #d4edda;
      --correct-border: #28a745;
      --incorrect-bg: #f8d7da;
      --incorrect-border: #dc3545;
    }

    body {
      font-family: Arial, sans-serif;
      background-color: var(--bg-color);
      color: var(--text-color);
      margin: 0;
      padding: 20px;
    }

    .container {
      max-width: 800px;
      margin: 0 auto;
    }

    h1 {
      text-align: center;
      color: var(--primary-color);
    }

    .question-card {
      background-color: var(--card-bg);
      border-radius: 8px;
      padding: 20px;
      margin-bottom: 20px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }

    .question-title {
      font-weight: bold;
      font-size: 1.1em;
      margin-bottom: 5px;
    }

    .norm-tag {
      font-size: 0.85em;
      color: #666;
      margin-bottom: 15px;
    }

    .options-list {
      list-style-type: none;
      padding: 0;
      margin: 0;
    }

    .option-item {
      margin-bottom: 10px;
      padding: 10px;
      border: 1px solid #ddd;
      border-radius: 5px;
      cursor: pointer;
      transition: background-color 0.2s;
    }

    .option-item:hover {
      background-color: #f0f0f0;
    }

    .option-item label {
      cursor: pointer;
      display: flex;
      align-items: center;
      width: 100%;
    }

    .option-item input[type="radio"] {
      margin-right: 10px;
    }

    .correct-answer {
      background-color: var(--correct-bg) !important;
      border-color: var(--correct-border) !important;
      font-weight: bold;
    }

    .wrong-answer {
      background-color: var(--incorrect-bg) !important;
      border-color: var(--incorrect-border) !important;
    }

    .btn-submit {
      display: block;
      width: 100%;
      padding: 15px;
      background-color: var(--primary-color);
      color: #fff;
      border: none;
      border-radius: 5px;
      font-size: 1.1em;
      font-weight: bold;
      cursor: pointer;
      margin-top: 20px;
    }

    .btn-submit:hover {
      background-color: #004085;
    }

    #result-container {
      margin-top: 20px;
      padding: 15px;
      background-color: var(--card-bg);
      border-radius: 8px;
      text-align: center;
      font-size: 1.2em;
      font-weight: bold;
      display: none;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
  </style>
</head>
<body>

<div class="container">
  <h1>Elektrotechnický Test</h1>
  <form id="quiz-form">
    <div id="questions-wrapper"></div>
    <button type="button" class="btn-submit" onclick="evaluateQuiz()">Vyhodnotiť test</button>
  </form>
  <div id="result-container"></div>
</div>

<script>
  // Dáta otázok s presne namapovanými farebne označenými odpoveďami z fotiek
  const quizData = [
    {
      id: 1,
      question: "Zamestnávateľ je povinný urobiť opatrenia na zaistenie BOZP",
      norm: "Zákon 124/2006 Z. z.",
      options: [
        "A) so zreteľom na všetky okolnosti vykonávanej práce",
        "B) so zreteľom na nebezpečné práce",
        "C) so zreteľom na vybrané okolnosti vykonávanej práce"
      ],
      correct: 0 // A (Označené na fotke 1)
    },
    {
      id: 2,
      question: "Koľko ochranných opatrení musí byť uplatnených v každej časti inštalácie",
      norm: "STN 33 2000-4-41:2019",
      options: [
        "A) min. základná a doplnková ochrana, pričom sa musia zohľadniť podmienky vonkajších vplyvov",
        "B) min. dva alebo viac ochranných opatrení, pričom sa musia zohľadniť podmienky vonkajších vplyvov",
        "C) jedno alebo viac ochranných opatrení, pričom sa musia zohľadniť podmienky vonkajších vplyvov"
      ],
      correct: 2 // C (Označené na fotke 1)
    },
    {
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
        "A) sa musí merať na spotrebiči s triedou ochrany II , ktoré nie je trvalo pripojené k napájacej sieti",
        "B) sa musí merať na spotrebiči s triedou ochrany I a triedou ochrany II , ktoré nie je trvalo pripojené k napájacej sieti",
        "C) sa musí merať na zariadení, ktoré má ochranné pospájanie a ktoré nie je trvalo pripojené k napájacej sieti"
      ],
      correct: 1 // B (Označené na fotke 10)
    },
    {
      id: 30,
      question: "Elektrické spotrebiče, alebo predlžovacie prívody zaradené do skupiny C podľa oblasti ich používania sú:",
      norm: "STN 33 1610:2025",
      options: [
        "A) spotrebiče a/alebo predlžovacie prívody používané vo vnútorných verejne prístupných priestoroch (napr. zdravotnícke objekty, objekty sociálnych služieb)",
        "B) spotrebiče a/alebo predlžovacie prívody používané vo vnútorných priestoroch (napr. obchodnej činnosti, spotrebiče v kuchynkách a pod.)",
        "C) spotrebiče a/alebo predlžovacie prívody používané vo vonkajšom prostredí (napr. na stavbách, pri poľnohospodárskych prácach a pod.)"
      ],
      correct: 1 // B (Označené na fotke 10)
    }
  ];

  function renderQuiz() {
    const wrapper = document.getElementById('questions-wrapper');
    wrapper.innerHTML = '';

    quizData.forEach((item, index) => {
      const card = document.createElement('div');
      card.className = 'question-card';
      card.id = `q-card-${index}`;

      let optionsHTML = '';
      item.options.forEach((opt, optIndex) => {
        optionsHTML += `
          <li class="option-item" id="opt-container-${index}-${optIndex}">
            <label>
              <input type="radio" name="question-${index}" value="${optIndex}">
              ${opt}
            </label>
          </li>
        `;
      });

      card.innerHTML = `
        <div class="question-title">${index + 1}. ${item.question}</div>
        <div class="norm-tag">${item.norm}</div>
        <ul class="options-list">
          ${optionsHTML}
        </ul>
      `;

      wrapper.appendChild(card);
    });
  }

  function evaluateQuiz() {
    let score = 0;

    quizData.forEach((item, index) => {
      const selectedOption = document.querySelector(`input[name="question-${index}"]:checked`);
      
      item.options.forEach((_, optIndex) => {
        const el = document.getElementById(`opt-container-${index}-${optIndex}`);
        el.classList.remove('correct-answer', 'wrong-answer');
      });

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
    resultDiv.innerHTML = `Dosiahli ste skóre: ${score} z ${quizData.length} bodov (${Math.round((score / quizData.length) * 100)}%).`;
    resultDiv.scrollIntoView({ behavior: 'smooth' });
  }

  window.onload = renderQuiz;
</script>

</body>
</html>


            
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
            
