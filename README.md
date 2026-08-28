<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Capybara Clicker</title>

    <style>
        * {
            box-sizing: border-box;
            user-select: none;
        }

        body {
            margin: 0;
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #b8e6ff, #d9ffd0);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .game {
            width: 95%;
            max-width: 500px;
            text-align: center;
        }

        h1 {
            margin-bottom: 8px;
            color: #333;
        }

        .stats {
            display: flex;
            justify-content: space-around;
            background: white;
            border-radius: 20px;
            padding: 12px;
            margin-bottom: 20px;
            box-shadow: 0 5px 15px #0002;
            font-size: 18px;
            font-weight: bold;
        }

        .character-zone {
            position: relative;
            height: 380px;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        #capybara {
            width: 270px;
            max-width: 75%;
            cursor: pointer;
            transition: transform 0.15s ease;
            filter: drop-shadow(0 10px 8px #0002);
        }

        #capybara.eat {
            animation: eat 0.45s ease;
        }

        #capybara.happy {
            animation: happy 0.7s ease;
        }

        @keyframes eat {
            0% {
                transform: scale(1) rotate(0deg);
            }
            25% {
                transform: scale(1.12) rotate(-4deg);
            }
            50% {
                transform: scale(0.94) rotate(4deg);
            }
            75% {
                transform: scale(1.08) rotate(-2deg);
            }
            100% {
                transform: scale(1) rotate(0deg);
            }
        }

        @keyframes happy {
            0% {
                transform: scale(1);
            }
            30% {
                transform: scale(1.12) translateY(-10px);
            }
            60% {
                transform: scale(1.05) translateY(0);
            }
            100% {
                transform: scale(1);
            }
        }

        .cookie {
            position: absolute;
            width: 75px;
            height: 75px;
            background: #c98242;
            border-radius: 50%;
            border: 6px solid #9b5c2c;
            cursor: pointer;
            box-shadow: 0 5px 10px #0003;

            left: 50%;
            bottom: 35px;
            transform: translateX(-50%);

            transition:
                opacity 0.25s ease,
                transform 0.35s ease;
        }

        .cookie::before,
        .cookie::after {
            content: "";
            position: absolute;
            width: 10px;
            height: 10px;
            background: #613719;
            border-radius: 50%;
        }

        .cookie::before {
            top: 18px;
            left: 18px;
            box-shadow:
                28px 4px #613719,
                10px 28px #613719,
                40px 35px #613719;
        }

        .cookie::after {
            top: 45px;
            left: 27px;
            box-shadow: 20px -20px #613719;
        }

        .cookie.disappear {
            opacity: 0;
            transform: translateX(-50%) scale(0.3) rotate(30deg);
        }

        .plus {
            position: absolute;
            left: 50%;
            bottom: 110px;
            transform: translateX(-50%);
            font-size: 30px;
            font-weight: bold;
            color: #27a844;
            pointer-events: none;
            animation: plusAnimation 0.8s ease-out forwards;
            z-index: 10;
        }

        @keyframes plusAnimation {
            0% {
                opacity: 0;
                transform: translate(-50%, 20px) scale(0.7);
            }

            20% {
                opacity: 1;
                transform: translate(-50%, 0) scale(1.15);
            }

            100% {
                opacity: 0;
                transform: translate(-50%, -70px) scale(1);
            }
        }

        .panda {
            margin-top: 10px;
        }

        #panda {
            width: 100px;
        }

        .level-bar {
            width: 100%;
            height: 15px;
            background: #ffffffaa;
            border-radius: 20px;
            overflow: hidden;
            margin-top: 8px;
        }

        #progress {
            height: 100%;
            width: 0%;
            background: #6acb5b;
            transition: width 0.3s ease;
        }

        .hearts {
            margin-top: 8px;
            font-size: 25px;
            letter-spacing: 3px;
        }

        .message {
            min-height: 30px;
            font-size: 18px;
            font-weight: bold;
            color: #555;
        }
    </style>
</head>

<body>

<div class="game">

    <h1>🐹 Capybara Clicker</h1>

    <div class="stats">
        <div>🍪 <span id="score">0</span></div>
        <div>⭐ Niveau <span id="level">1</span></div>
    </div>

    <div class="character-zone">

        <img
            id="capybara"
            src="capybara.png"
            alt="Capybara"
        >

        <div
            id="cookie"
            class="cookie"
            onclick="eatCookie()"
        ></div>

    </div>

    <div class="message" id="message">
        Donne un cookie au capybara ! 🍪
    </div>

    <div>
        Progression vers le niveau suivant :
        <div class="level-bar">
            <div id="progress"></div>
        </div>
    </div>

    <div class="hearts" id="hearts">
        ❤️
    </div>

    <div class="panda">
        <img
            id="panda"
            src="panda.png"
            alt="Panda"
        >
    </div>

</div>

<script>

    let score = 0;
    let level = 1;
    let hearts = 1;

    // Nombre de cookies nécessaires pour passer au niveau suivant
    let cookiesForNextLevel = 10;

    const capybara = document.getElementById("capybara");
    const cookie = document.getElementById("cookie");

    function eatCookie() {

        // Évite plusieurs clics pendant la disparition
        if (cookie.classList.contains("disappear")) {
            return;
        }

        // Pour l'instant chaque cookie donne +1
        // Tu peux changer cette valeur plus tard
        const gain = 1;

        score += gain;

        // Affichage du score
        document.getElementById("score").textContent = score;

        // Animation du capybara
        capybara.classList.remove("eat");
        capybara.classList.remove("happy");

        void capybara.offsetWidth;

        capybara.classList.add("eat");

        // Création du +1 ou +2
        showPlus(gain);

        // Cookie disparaît doucement
        cookie.classList.add("disappear");

        // Vérification du niveau
        checkLevel();

        // Le capybara devient heureux
        document.getElementById("message").textContent =
            "Miam ! 😋 Le capybara est heureux ! ❤️";

        capybara.classList.add("happy");

        // Nouveau cookie après l'animation
        setTimeout(() => {

            cookie.classList.remove("disappear");

        }, 450);
    }


    function showPlus(amount) {

        const plus = document.createElement("div");

        plus.className = "plus";
        plus.textContent = "+" + amount;

        document.querySelector(".character-zone").appendChild(plus);

        setTimeout(() => {
            plus.remove();
        }, 800);
    }


    function checkLevel() {

        const previousLevel = level;

        level = Math.floor(score / cookiesForNextLevel) + 1;

        // Si un nouveau niveau est atteint
        if (level > previousLevel) {

            hearts++;

            document.getElementById("level").textContent = level;

            document.getElementById("hearts").textContent =
                "❤️".repeat(hearts);

            document.getElementById("message").textContent =
                "🎉 Nouveau niveau ! +1 ❤️";

            capybara.classList.remove("happy");

            void capybara.offsetWidth;

            capybara.classList.add("happy");

        } else {

            document.getElementById("level").textContent = level;
        }

        updateProgress();
    }


    function updateProgress() {

        const currentLevelScore =
            score % cookiesForNextLevel;

        const percentage =
            (currentLevelScore / cookiesForNextLevel) * 100;

        document.getElementById("progress").style.width =
            percentage + "%";
    }


    updateProgress();

</script>

</body>
</html>
