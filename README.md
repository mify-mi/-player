<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>本格タイピングゲーム</title>
<style>
body {
  font-family: monospace;
  background:#111;
  color:#0f0;
  text-align:center;
}
#word {
  font-size:32px;
  margin:20px;
}
.correct { color:#0f0; }
.wrong { color:#f33; }
#stats { margin-top:20px; }
</style>
</head>

<body>

<h1>⌨️ TYPING MASTER</h1>
<p>制限時間：<span id="time">60</span>s</p>

<div id="word"></div>

<input id="input" autofocus autocomplete="off" />

<div id="stats">
  <p>Score: <span id="score">0</span></p>
  <p>Combo: <span id="combo">0</span></p>
</div>

<div id="result"></div>

<script>
const words = [
  "javascript","function","variable","object","closure",
  "prototype","callback","promise","async","await"
];

let current = "";
let index = 0;
let score = 0;
let combo = 0;
let total = 0;
let correct = 0;
let time = 60;
let timer;

// 効果音
const hit = new Audio("https://actions.google.com/sounds/v1/cartoon/wood_plank_flicks.ogg");
const miss = new Audio("https://actions.google.com/sounds/v1/cartoon/clang_and_wobble.ogg");

function nextWord(){
  current = words[Math.floor(Math.random()*words.length)];
  index = 0;
  renderWord();
}

function renderWord(){
  const html = current.split("").map((c,i)=>{
    if(i < index) return `<span class="correct">${c}</span>`;
    return c;
  }).join("");
  document.getElementById("word").innerHTML = html;
}

document.getElementById("input").addEventListener("keydown", e=>{
  if(time<=0) return;
  if(!timer){
    timer = setInterval(()=>{
      time--;
      document.getElementById("time").textContent = time;
      if(time<=0) endGame();
    },1000);
  }

  const key = e.key;
  total++;

  if(key === current[index]){
    index++;
    correct++;
    combo++;
    score += 10 * combo;
    hit.currentTime = 0;
    hit.play();
    if(index === current.length) nextWord();
  }else{
    combo = 0;
    miss.currentTime = 0;
    miss.play();
  }

  document.getElementById("score").textContent = score;
  document.getElementById("combo").textContent = combo;
  renderWord();
});

function endGame(){
  clearInterval(timer);
  document.getElementById("input").disabled = true;
  const wpm = Math.floor(correct / 5);
  const acc = Math.floor((correct / total) * 100);
  document.getElementById("result").innerHTML = `
    <h2>結果</h2>
    WPM: ${wpm}<br>
    正確率: ${acc}%<br>
    スコア: ${score}
  `;
}

nextWord();
</script>

</body>
</html>
