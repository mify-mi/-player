<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>三人麻雀 本格簡略</title>
<style>
body { background:#0a5; color:#fff; font-family:sans-serif; text-align:center; }
.hand span {
  display:inline-block;
  background:#fff;
  color:#000;
  border:1px solid #000;
  padding:6px;
  margin:2px;
  cursor:pointer;
}
button { margin:5px; }
</style>
</head>

<body>
<h1>🀄 三人麻雀（本格簡略）</h1>
<p id="info"></p>

<div>
<h3>あなた</h3>
<div id="hand" class="hand"></div>
<button onclick="draw()">ツモ</button>
<button onclick="declareReach()">リーチ</button>
</div>

<script>
// ===== 牌生成 =====
let wall=[];
for(let i=1;i<=9;i++) for(let j=0;j<4;j++) wall.push(i);
for(let j=0;j<4;j++) wall.push("北"); // 北抜き
wall.sort(()=>Math.random()-0.5);

// ===== プレイヤー =====
const players=[
 {hand:[], melds:[], reach:false, score:25000},
 {hand:[], melds:[], score:25000},
 {hand:[], melds:[], score:25000}
];

let turn=0;

// 配牌
for(let r=0;r<13;r++) players.forEach(p=>p.hand.push(wall.pop()));
players.forEach(p=>p.hand.sort());

// ===== UI =====
function render(){
 const h=document.getElementById("hand");
 h.innerHTML="";
 players[0].hand.forEach((t,i)=>{
   const s=document.createElement("span");
   s.textContent=t;
   s.onclick=()=>discard(i);
   h.appendChild(s);
 });
}
render();

// ===== ツモ =====
function draw(){
 if(turn!==0) return;
 const t=wall.pop();
 if(t==="北"){
   players[0].score+=1000;
   document.getElementById("info").textContent="北抜き！+1000点";
   return;
 }
 players[0].hand.push(t);
 players[0].hand.sort();
 render();

 if(isAgari(players[0].hand)){
   finishHand();
 }
}

// ===== 捨て =====
function discard(i){
 const tile=players[0].hand.splice(i,1)[0];
 turn=1;
 cpuTurn(1,tile);
 render();
}

// ===== CPU =====
function cpuTurn(n,discarded){
 const p=players[n];
 p.hand.push(wall.pop());
 p.hand.sort();

 // ポン判定
 const cnt=p.hand.filter(t=>t===discarded).length;
 if(cnt>=2){
   p.melds.push([discarded,discarded,discarded]);
   p.hand=p.hand.filter(t=>t!==discarded);
 }

 // 捨て
 p.hand.splice(Math.floor(Math.random()*p.hand.length),1);
 turn=(n+1)%3;
 if(turn!==0) cpuTurn(turn);
}

// ===== アガリ判定 =====
function isAgari(hand){
 hand=hand.slice().sort();
 for(let i=0;i<hand.length;i++){
   if(hand[i]===hand[i+1]){
     const rest=hand.slice();
     rest.splice(i,2);
     if(canForm(rest)) return true;
   }
 }
 return false;
}

function canForm(arr){
 if(arr.length===0) return true;
 const t=arr[0];
 // 刻子
 if(arr.filter(x=>x===t).length>=3){
   const r=arr.slice();
   r.splice(0,3);
   if(canForm(r)) return true;
 }
 // 順子
 if(typeof t==="number" &&
    arr.includes(t+1) && arr.includes(t+2)){
   const r=arr.slice();
   r.splice(r.indexOf(t+2),1);
   r.splice(r.indexOf(t+1),1);
   r.splice(0,1);
   if(canForm(r)) return true;
 }
 return false;
}

// ===== リーチ =====
function declareReach(){
 if(isTenpai(players[0].hand)){
   players[0].reach=true;
   players[0].score-=1000;
   document.getElementById("info").textContent="リーチ！";
 }
}

function isTenpai(hand){
 for(let i=1;i<=9;i++){
   const test=hand.concat(i);
   if(isAgari(test)) return true;
 }
 return false;
}

// ===== 点数計算 =====
function finishHand(){
 let han=0;
 if(players[0].reach) han++;
 if(players[0].hand.every(t=>t!==1&&t!==9)) han++; // タンヤオ

 let point=han>=3?4000:han*1000;
 players[0].score+=point;

 document.getElementById("info").innerHTML=
   `🎉 アガリ！ ${han}翻 ${point}点`;
}
</script>
</body>
</html>
