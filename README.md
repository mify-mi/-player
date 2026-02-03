<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>Typing Master Stage</title>
<style>
body {
  background:#111;
  color:#0f0;
  font-family:monospace;
  text-align:center;
}
#text {
  font-size:28px;
  margin:20px;
  word-break:break-all;
}
.correct { color:#0f0; }
.wrong { color:#f33; }
#stage { font-size:18px; }
</style>
</head>

<body>

<h1>⌨️ TYPING MASTER</h1>
<p id="stage">Stage 1</p>
<p>Time: <span id="time"></span>s</p>

<div id="text"></div>
<input id="input" autofocus />

<p>
Score: <span id="score">0</span> /
Combo: <span id="combo">0</span>
</p>

<div id="result"></div>

<script>
const wordPool = [
  "javascript","function","variable","closure","prototype",
  "async await","document object model","event listener",
  "frontend development","typing speed test"
];

let stage = 1;
let time
