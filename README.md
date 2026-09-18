<!DOCTYPE html>
<html lang="sk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>test_proj_1</title>
    <style>
        :root {
            --bg-color: #f1f5f9;
            --card-bg: #ffffff;
            --text-color: #0f172a;
            --norm-color: #64748b;
            --correct-bg: #dcfce7;
            --correct-border: #22c55e;
            --correct-text: #14532d;
            --incorrect-bg: #fee2e2;
            --incorrect-border: #ef4444;
            --incorrect-text: #7f1d1d;
            --neutral-border: #cbd5e1;
            --btn-bg: #2563eb;
            --btn-hover: #1d4ed8;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            padding: 12px;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            min-height: 100vh;
        }

        .quiz-container {
            width: 100%;
            max-width: 500px;
            background: var(--card-bg);
            border-radius: 14px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.06);
            padding: 16px;
            display: flex;
            flex-direction: column;
            gap: 14px;
        }

        .header {
            border-bottom: 2px solid #f1f5f9;
            padding-bottom: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .test-title {
            font-size: 1.1rem;
            font-weight: 700;
            color: var(--btn-bg);
            text-transform: uppercase;
        }

        .counter {
            font-size: 0.85rem;
            font-weight: 600;
            color: var(--norm-color);
            background: #f1f5f9;
            padding: 4px 10px;
            border-radius: 12px;
        }

        .stats-bar {
            display: flex;
            justify-content: space-between;
            background: #f8fafc;
            padding: 8px 12px;
            border-radius: 8px;
            font-size: 0.85rem;
            font-weight: 600;
            color: #334155;
        }

        .question-title {
            font-size: 1.05rem;
            font-weight: 600;
            line-height: 1.4;
            color: var(--text-color);
        }

        .norm-reference {
            font-size: 0.8rem;
            color: var(--norm-color);
            font-weight: 500;
            margin-top: -6px;
        }

        .options-container {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .option {
            display: flex;
            align-items: flex-start;
            padding: 12px 14px;
            border-radius: 10px;
            border: 1.5px solid var(--neutral-border);
            font-size: 0.95rem;
            line-height: 1.35;
            background-color: #fff;
            cursor: pointer;
            user-select: none;
            -webkit-tap-highlight-color: transparent;
            transition: background-color 0.15s ease, border-color 0.15s ease;
        }

        .option-letter {
            font-weight: 700;
            margin-right: 10px;
            min-width: 20px;
            color: var(--btn-bg);
        }

        .option.correct {
            background-color: var(--correct-bg) !important;
            border-color: var(--correct-border) !important;
            color: var(--correct-text) !important;
            font-weight: 600;
        }

        .option.incorrect {
            background-color: var(--incorrect-bg) !important;
            border-color: var(--incorrect-border) !important;
            color: var(--incorrect-text) !important;
        }

        .option.disabled {
            cursor: default;
        }

        .controls {
            display: flex;
            gap: 10px;
            border-top: 2px solid #f1f5f9;
            padding-top: 12px;
            margin-top: 4px;
        }

        .btn {
            flex: 1;
            background-color: var(--btn-bg);
            color: white;
            border: none;
            padding: 12px 0;
            font-size: 0.95rem;
            font-weight: 600;
            border-radius: 8px;
            cursor: pointer;
            text-align: center;
        }

        .btn:disabled {
            background-color: #cbd5e1;
            cursor: not-allowed;
        }

        .btn-secondary {
            background-color: #e2e8f0;
            color: #334155;
        }

        .btn-reset {
            background-color: #f8fafc;
            color: #64748b;
            border: 1px solid #cbd5e1;
            font-size: 0.8rem;
            padding: 6px 10px;
            border-radius: 6px;
            cursor: pointer;
            margin-top: 5px;
            align-self: center;
        }
    </style>
</head>
<body>

<div class="quiz-container">
    <div class="header">
        <h2 class="test-title">test_proj_1</h2>
        <span class="counter" id="counter">1 / 10</span>
    </div>

    <div class="stats-bar">
        <span>Skóre: <strong id="score">0</strong> / <span id="answered-count">0</span></span>
        <span>Úspešnosť: <strong id="percentage">0 %</strong></span>
    </div>

    <h1 class="question-title" id="question">Načítavam otázku...</h1>
    <div class="norm-reference" id="norm">---</div>

    <div class="options-container" id="options">
        <!-- Možnosti -->
    </div>

    <div class="controls">
        <button class="btn btn-secondary" id="prevBtn" onclick="prevQuestion()">Späť</button>
        <button class="btn" id="nextBtn" onclick="nextQuestion()">Ďalej</button>
    </div>

    <button class="btn-reset" onclick="resetQuiz()">Vynulovať odpovede</button>
</div>

<script>
    const quizData = [
        {
            question: "Elektrické spotrebiče triedy I. sú",
            norm: "STN 33 2000-4-41:2019",
            options: [
                { letter: "A", text: "s privedeným ochranným vodičom. Ochrana pred úrazom el. prúdom je zaistená dvojitou izoláciou a pripojením ochranného vodiča na ochrannú svorku", correct: false },
                { letter: "B", text: "s privedeným ochranným vodičom. Ochrana pred úrazom el. prúdom je zaistená dvojitou izoláciou a pripojením neživej časti k ochrannému vodiču", correct: false },
                { letter: "C", text: "s privedeným ochranným vodičom. Ochrana pred úrazom el. prúdom je zaistená základnou izoláciou a pripojením neživej časti k ochrannému vodiču", correct: true }
            ]
        },
        {
            question: "Čo je to schéma",
            norm: "STN EN 61082-1:2015",
            options: [
                { letter: "A", text: "uvedenie informácií pomocou výkresov, kde pre vyznačenie predmetov a ich vzájomných vzťahov používame grafické značky", correct: true },
                { letter: "B", text: "uvedenie informácií pomocou výkresov, kde sú znázornené objekty prevažne v mierke k vzájomnému vzťahu", correct: false },
                { letter: "C", text: "uvedenie informácií pomocou výkresov, kde sú vyznačené vzťahy medzi dvoma a viac premennými veličinami a operáciami", correct: false }
            ]
        },
        {
            question: "Signálne svietidlo červené znamená",
            norm: "STN EN 60204-1:2019",
            options: [
                { letter: "A", text: "príkaz na činnosť", correct: false },
                { letter: "B", text: "výstrahu - varovanie", correct: false },
                { letter: "C", text: "nebezpečenstvo", correct: true }
            ]
        },
        {
            question: "Označenie rozvodnej siete TN-S znamená",
            norm: "STN 33 2000-4-41:2019",
            options: [
                { letter: "A", text: "elektrická striedavá sieť s uzemneným bodom zdroja. Neživé časti el. spotrebičov sú spojené s uzemňovačmi elektricky nezávislými od uzemňovacej siete. Funkcia neutrálneho a ochranného vodiča je zlúčená do jedného kombinovaného vodiča PEN", correct: false },
                { letter: "B", text: "elektrická striedavá sieť s uzemneným bodom zdroja. Neživé časti el. spotrebičov sú s týmto bodom spojené. Funkcia neutrálneho a ochranného vodiča je zlúčená do jedného kombinovaného vodiča PEN", correct: false },
                { letter: "C", text: "elektrická striedavá sieť s uzemneným bodom zdroja. Neživé časti el. spotrebičov sú s týmto bodom spojené cez ochranný vodič PE. Funkcia neutrálneho a ochranného vodiča je samostatná", correct: true }
            ]
        },
        {
            question: "Čo je to diagram",
            norm: "STN EN 61082-1:2015",
            options: [
                { letter: "A", text: "uvedenie informácií pomocou výkresov, kde sú vyznačené vzťahy medzi dvoma a viac premennými veličinami a operáciami", correct: true },
                { letter: "B", text: "uvedenie informácií pomocou výkresov, kde sú znázornené objekty prevažne v mierke k vzájomnému vzťahu", correct: false },
                { letter: "C", text: "uvedenie informácií pomocou výkresov, kde pre vyznačenie predmetov a ich vzájomných vzťahov používame grafické značky", correct: false }
            ]
        },
        {
            question: "VTZ elektrické zaradené do skupiny A je",
            norm: "vyhláška 508/2009 Z. z.",
            options: [
                { letter: "A", text: "elektrická inštalácia v objekte určenom na zhromažďovanie viac ako 300 osôb v jednom priestore vrátane ochrany pred účinkami atmosférickej elektriny", correct: false },
                { letter: "B", text: "elektrická inštalácia v objekte určenom na zhromažďovanie viac ako 250 osôb v jednom priestore vrátane ochrany pred účinkami atmosférickej elektriny", correct: true },
                { letter: "C", text: "elektrická inštalácia v objekte určenom na zhromažďovanie viac ako 200 osôb v jednom priestore vrátane ochrany pred účinkami atmosférickej elektriny", correct: false }
            ]
        },
        {
            question: "Autorizovaným stavebným inžinierom je ten, kto je zapísaný v zozname autorizovaných stavebných inžinierov",
            norm: "zákon 237/2000 Z. z.",
            options: [
                { letter: "A", text: "v zmysle zákona č. 237/2000 Z. z. oprávnenou osobou", correct: true },
                { letter: "B", text: "v zmysle zákona č. 124/2006 Z. z. oprávnenou osobou", correct: false },
                { letter: "C", text: "v zmysle vyhlášky č. 508/2009 Z. z. oprávnenou osobou", correct: false }
            ]
        },
        {
            question: "Vyrábať, montovať na mieste budúcej prevádzky a rekonštruovať vyhradené technické zariadenie možno iba podľa konštrukčnej dokumentácie, ku ktorej bolo vydané odborné stanovisko OPOm podľa § 14 ods. 1 písm. d) zákona, ktorým sa posudzuje konštrukčná dokumentácia",
            norm: "vyhláška 508/2009 Z. z.",
            options: [
                { letter: "A", text: "pri VTZ elektrickom sa odborné stanovisko k dokumentácii vyžaduje okrem skupiny C", correct: false },
                { letter: "B", text: "pri VTZ elektrickom sa odborné stanovisko k dokumentácii nevyžaduje", correct: false },
                { letter: "C", text: "pri VTZ elektrickom sa odborné stanovisko k dokumentácii vyžaduje iba na technické zariadenie skupiny A", correct: true }
            ]
        },
        {
            question: "V zmysle zákona č. 25/2025 Z. z. generálny projektant zodpovedá",
            norm: "zákon 25/2025 Z. z.",
            options: [
                { letter: "A", text: "za použiteľnosť projektovej dokumentácie a bezpečnosť práce počas realizácie stavby", correct: false },
                { letter: "B", text: "za úplnosť projektovej dokumentácie a bezpečnosť práce počas realizácie stavby", correct: false },
                { letter: "C", text: "za komplexnosť a úplnosť projektovej dokumentácie a jej použiteľnosť", correct: true }
            ]
        },
        {
            question: "Podkladom pre konanie na stavebnom úrade je okrem stavebného zámeru aj správa o prerokovaní stavebného zámeru. Kto ju vypracuje?",
            norm: "zákon 25/2025 Z. z.",
            options: [
                { letter: "A", text: "stavebník, alebo ním poverený projektant", correct: true },
                { letter: "B", text: "stavebný inšpektorát", correct: false },
                { letter: "C", text: "stavebný úrad", correct: false }
            ]
        }
    ];

    let currentQuestionIndex = 0;
    let userAnswers = new Array(quizData.length).fill(null);

    function renderQuestion() {
        const q = quizData[currentQuestionIndex];
        
        document.getElementById("counter").innerText = `${currentQuestionIndex + 1} / ${quizData.length}`;
        document.getElementById("question").innerText = q.question;
        document.getElementById("norm").innerText = q.norm;

        const optionsContainer = document.getElementById("options");
        optionsContainer.innerHTML = "";

        const selectedIndex = userAnswers[currentQuestionIndex];

        q.options.forEach((opt, idx) => {
            const div = document.createElement("div");
            div.className = "option";
            
            if (selectedIndex !== null) {
                div.classList.add("disabled");
                if (opt.correct) {
                    div.classList.add("correct");
                } else if (idx === selectedIndex) {
                    div.classList.add("incorrect");
                }
            } else {
                div.onclick = () => selectOption(idx);
            }

            div.innerHTML = `<span class="option-letter">${opt.letter})</span> <span>${opt.text}</span>`;
            optionsContainer.appendChild(div);
        });

        document.getElementById("prevBtn").disabled = currentQuestionIndex === 0;
        document.getElementById("nextBtn").disabled = currentQuestionIndex === quizData.length - 1;

        updateStats();
    }

    function selectOption(index) {
        if (userAnswers[currentQuestionIndex] === null) {
            userAnswers[currentQuestionIndex] = index;
            renderQuestion();
        }
    }

    function updateStats() {
        let score = 0;
        let answeredCount = 0;

        userAnswers.forEach((ans, qIdx) => {
            if (ans !== null) {
                answeredCount++;
                if (quizData[qIdx].options[ans].correct) {
                    score++;
                }
            }
        });

        const percentage = answeredCount > 0 ? Math.round((score / answeredCount) * 100) : 0;

        document.getElementById("score").innerText = score;
        document.getElementById("answered-count").innerText = answeredCount;
        document.getElementById("percentage").innerText = `${percentage} %`;
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

    function resetQuiz() {
        if (confirm("Naozaj chcete vynulovať všetky odpovede a začať znova?")) {
            userAnswers = new Array(quizData.length).fill(null);
            currentQuestionIndex = 0;
            renderQuestion();
        }
    }

    document.addEventListener('keydown', function(event) {
        if (event.key === "ArrowRight") nextQuestion();
        if (event.key === "ArrowLeft") prevQuestion();
    });

    renderQuestion();
</script>

</body>
</html>
