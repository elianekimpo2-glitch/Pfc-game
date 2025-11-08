# Pfc-game
Jeux de paris 
<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pierre-Feuille-Ciseaux - Congo Bet Pro</title>
<style>
  body {
    font-family: 'Arial', sans-serif;
    background: radial-gradient(circle at top, #0c0c0c, #1a1a1a);
    color: white;
    text-align: center;
    margin: 0;
    overflow-x: hidden;
  }

  h1 {
    margin-top: 20px;
    font-size: 2.5em;
    color: #FFD700;
    text-shadow: 0 0 15px #FFD700;
  }

  .tokens, .bet-section {
    margin-top: 20px;
    font-size: 1.3em;
  }

  input[type="number"] {
    padding: 8px;
    font-size: 1em;
    width: 100px;
    margin-left: 10px;
    border-radius: 10px;
    border: 2px solid #FFD700;
    text-align: center;
    background: #1a1a1a;
    color: #FFD700;
  }

  .game-container {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 40px;
    gap: 80px;
  }

  .player, .computer {
    width: 120px;       /* réduit la taille */
    height: 120px;      /* réduit la taille */
    font-size: 4em;     /* réduit l’emplacement de l’emoji */
    display: flex;
    justify-content: center;
    align-items: center;
    border: 4px solid #FFD700;
    border-radius: 50%;
    box-shadow: 0 0 20px #FFD700;
    transition: transform 0.5s, box-shadow 0.5s;
    background: #111;
  }

  .choices {
    margin-top: 40px;
  }

  .choices button {
    font-size: 2em;
    margin: 10px;
    padding: 20px 40px;
    border-radius: 25px;
    border: none;
    cursor: pointer;
    transition: transform 0.2s, box-shadow 0.2s;
    background: linear-gradient(45deg, #FFD700, #FFAA00);
    color: #000;
    font-weight: bold;
    text-shadow: 1px 1px #000;
  }

  .choices button:hover {
    transform: scale(1.2);
    box-shadow: 0 0 30px #FFD700;
  }

  .result {
    margin-top: 30px;
    font-size: 1.7em;
    color: #00FF00;
    text-shadow: 0 0 10px #00FF00;
    min-height: 40px;
  }

  .lights {
    position: absolute;
    width: 100%;
    height: 100%;
    pointer-events: none;
    overflow: hidden;
  }

  .light {
    position: absolute;
    border-radius: 50%;
    opacity: 0.8;
    animation: moveLight 3s linear infinite;
  }

  @keyframes moveLight {
    0% { transform: translateY(0) translateX(0);}
    50% { transform: translateY(300px) translateX(200px);}
    100% { transform: translateY(0) translateX(400px);}
  }
</style>
</head>
<body>

<h1>Pierre-Feuille-Ciseaux - Congo Bet Pro</h1>

<div class="tokens">Vos jetons : <span id="tokens">1000</span></div>

<div class="bet-section">
  Mise : <input type="number" id="betAmount" value="300" min="10" step="10"> jetons
</div>

<div class="game-container">
  <div>
    <div class="player" id="playerEmoji">❓</div>
    <div>Vous</div>
  </div>
  <div>
    <div class="computer" id="computerEmoji">❓</div>
    <div>Ordinateur</div>
  </div>
</div>

<div class="choices">
  <button onclick="play('✊')">✊ Pierre</button>
  <button onclick="play('✋')">✋ Feuille</button>
  <button onclick="play('✌️')">✌️ Ciseaux</button>
</div>

<div class="result" id="result"></div>

<div class="lights" id="lights"></div>

<audio id="bgMusic" src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" loop autoplay></audio>
<audio id="winSound" src="https://www.soundjay.com/button/beep-07.mp3"></audio>
<audio id="loseSound" src="https://www.soundjay.com/button/beep-10.mp3"></audio>
<audio id="drawSound" src="https://www.soundjay.com/button/button-16.mp3"></audio>

<script>
let tokens = 1000;
const tokenDisplay = document.getElementById('tokens');

function play(userChoice) {
  let bet = parseInt(document.getElementById('betAmount').value);
  if (isNaN(bet) || bet <= 0) bet = 300; // valeur par défaut 300
  if (bet > tokens) {
    alert("Vous n'avez pas assez de jetons pour cette mise !");
    return;
  }

  const player = document.getElementById('playerEmoji');
  const computer = document.getElementById('computerEmoji');

  // Animation “ordinateur qui réfléchit”
  computer.textContent = '❓';
  player.style.transform = "rotate(-20deg) scale(1.2)";
  computer.style.transform = "rotate(20deg) scale(1.2)";

  setTimeout(() => {
    // Déterminer le résultat avec 55% chance pour l'utilisateur
    const rand = Math.random();
    let computerChoice;
    if (rand < 0.55) {
      // utilisateur gagne
      if (userChoice === '✊') computerChoice = '✌️';
      else if (userChoice === '✋') computerChoice = '✊';
      else computerChoice = '✋';
    } else if (rand < 0.775) {
      // égalité (~22.5%)
      computerChoice = userChoice;
    } else {
      // utilisateur perd (~22.5%)
      if (userChoice === '✊') computerChoice = '✋';
      else if (userChoice === '✋') computerChoice = '✌️';
      else computerChoice = '✊';
    }

    player.textContent = userChoice;
    computer.textContent = computerChoice;
    player.style.transform = "rotate(0deg) scale(1)";
    computer.style.transform = "rotate(0deg) scale(1)";

    // Résultat et jetons
    let resultText = "";
    if (userChoice === computerChoice) {
      resultText = "Égalité !";
      document.getElementById('drawSound').play();
      createLights('#FFFF00');
    } else if (
      (userChoice === '✊' && computerChoice === '✌️') ||
      (userChoice === '✋' && computerChoice === '✊') ||
      (userChoice === '✌️' && computerChoice === '✋')
    ) {
      resultText = `Vous gagnez ! +${bet} jetons`;
      tokens += bet;
      document.getElementById('winSound').play();
      createLights('#00FF00');
    } else {
      resultText = `Vous perdez ! -${bet} jetons`;
      tokens -= bet;
      document.getElementById('loseSound').play();
      createLights('#FF0000');
    }

    tokenDisplay.textContent = tokens;
    document.getElementById('result').textContent = resultText;

  }, 800); // temps de réflexion ordinateur
}

// Effet lumières
function createLights(color) {
  const lightsContainer = document.getElementById('lights');
  for (let i = 0; i < 15; i++) {
    const light = document.createElement('div');
    light.className = 'light';
    light.style.background = color;
    light.style.left = Math.random() * window.innerWidth + 'px';
    light.style.top = Math.random() * window.innerHeight + 'px';
    light.style.width = Math.random() * 15 + 10 + 'px';
    light.style.height = light.style.width;
    light.style.opacity = Math.random();
    lightsContainer.appendChild(light);
    setTimeout(() => light.remove(), 2000);
  }
}
</script>

</body>
</html>
