# dino-vocabulary-lab
An interactive Jurassic Park-themed English vocabulary lab where students explore vocabulary through fun games, challenges, and activities.

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Dino Vocabulary Lab | Daily Routines</title>
<style>
*{box-sizing:border-box}
body{margin:0;padding:24px 14px;min-height:100vh;background:linear-gradient(145deg,#092e23,#031711);color:#f5f0df;font-family:Arial,sans-serif}
main{max-width:760px;margin:auto}
header{padding:18px 0 24px}
.brand{color:#d7f36b;font-weight:bold;letter-spacing:2px;font-size:.8rem}
h1{font-size:clamp(2.4rem,8vw,4.5rem);line-height:.95;margin:14px 0;color:#f5f0df}
h1 span{color:#d7f36b}
header p{line-height:1.6;color:#c9dfce}
.card{background:#f5f0df;color:#183729;border-radius:22px;padding:clamp(18px,4vw,30px)}
.top{display:flex;justify-content:space-between;gap:10px;flex-wrap:wrap;font-weight:bold}
.tag{background:#dce9c4;border-radius:30px;padding:8px 12px;font-size:.8rem}
.track{height:8px;background:#d8dfd0;border-radius:20px;margin:18px 0 24px;overflow:hidden}
#progress{height:100%;width:0;background:#56833f;transition:width .2s}
#question{font-size:clamp(1.3rem,4vw,1.8rem);line-height:1.25;margin-bottom:8px}
#hint{color:#58705e;margin-bottom:20px;line-height:1.5}
.choices{display:grid;grid-template-columns:1fr 1fr;gap:12px}
.choice{padding:15px;border:2px solid #d3ddce;border-radius:13px;background:#fffdf5;color:#183729;font-weight:bold;font-size:1rem;text-align:left;cursor:pointer;min-height:60px}
.choice:hover:not(:disabled){border-color:#56833f}
.choice:disabled{cursor:default}
.choice.right{background:#e0f0ce;border-color:#4c8a43}
.choice.wrong{background:#ffe0d8;border-color:#c65a48}
#feedback{min-height:48px;font-weight:bold;line-height:1.5;margin-top:15px}
.buttons{display:flex;justify-content:space-between;gap:10px;flex-wrap:wrap;margin-top:10px}
button.action{border:0;border-radius:30px;padding:13px 18px;font-weight:bold;cursor:pointer}
#next{background:#123d2b;color:#fff}
#restart{background:#e0e5d8;color:#234531}
#next:disabled{opacity:.4;cursor:not-allowed}
#finish{display:none;text-align:center;padding:20px 0}
#finish h2{font-size:2rem}
#finish p{line-height:1.6;color:#526b58}
@media(max-width:540px){.choices{grid-template-columns:1fr}}
</style>
</head>
<body>
<main>
<header>
<div class="brand">◆ DINO VOCABULARY LAB · MISSION 01</div>
<h1>Daily<br><span>Routines</span></h1>
<p>Explore everyday English vocabulary. Choose the correct answer and complete your jungle field mission!</p>
</header>

<section class="card" id="game">
<div class="top"><span class="tag" id="counter">QUESTION 1 / 9</span><span>Score: <span id="score">0</span>/9</span></div>
<div class="track"><div id="progress"></div></div>
<h2 id="question"></h2>
<p id="hint"></p>
<div class="choices" id="choices"></div>
<div id="feedback" aria-live="polite"></div>
<div class="buttons">
<button class="action" id="restart">↻ Restart</button>
<button class="action" id="next" disabled>Next question →</button>
</div>
</section>

<section class="card" id="finish">
<div style="font-size:3.5rem" id="emoji">🦖</div>
<h2 id="resultTitle">Mission complete!</h2>
<p id="resultText"></p>
<button class="action" id="again" style="background:#123d2b;color:white">Play again ↻</button>
</section>
</main>

<script>
const questions=[
{q:"You stop sleeping in the morning. You…",hint:"The first step of your morning.",a:["Wake up","Go to bed","Have dinner","Do homework"],c:"Wake up",e:"Wake up means to stop sleeping."},
{q:"You use a toothbrush to…",hint:"A healthy habit for your teeth.",a:["Brush your teeth","Get dressed","Take a nap","Go jogging"],c:"Brush your teeth",e:"We brush our teeth to keep them clean."},
{q:"You put on your clothes. You…",hint:"You do this before leaving home.",a:["Get dressed","Go to sleep","Have lunch","Set an alarm"],c:"Get dressed",e:"Get dressed means to put on your clothes."},
{q:"You eat your first meal of the day. You…",hint:"Usually eaten in the morning.",a:["Have breakfast","Have dinner","Do exercise","Go to bed"],c:"Have breakfast",e:"Breakfast is the morning meal."},
{q:"You travel to class to learn. You…",hint:"A typical weekday activity for students.",a:["Go to school","Go to bed","Have a shower","Watch TV"],c:"Go to school",e:"You go to school to study."},
{q:"You complete school assignments at home. You…",hint:"You may do this after class.",a:["Do homework","Have breakfast","Wake up","Get dressed"],c:"Do homework",e:"Homework is schoolwork completed outside class."},
{q:"You run, stretch, or ride a bike to stay fit. You…",hint:"Physical activity keeps you healthy.",a:["Exercise","Have dinner","Go to sleep","Brush your teeth"],c:"Exercise",e:"Exercise means doing physical activity."},
{q:"You eat the evening meal. You…",hint:"Often eaten with family at night.",a:["Have dinner","Have breakfast","Go to school","Wake up"],c:"Have dinner",e:"Dinner is the evening meal."},
{q:"At night, you lie down to sleep. You…",hint:"The final routine of the day.",a:["Go to bed","Get up","Get dressed","Have lunch"],c:"Go to bed",e:"Go to bed means to get into bed to sleep."}
];
let i=0,points=0,locked=false;
const el=id=>document.getElementById(id);
function show(){
locked=false;const d=questions[i];
el("counter").textContent=`QUESTION ${i+1} / ${questions.length}`;
el("score").textContent=points;el("progress").style.width=(i/questions.length*100)+"%";
el("question").textContent=d.q;el("hint").textContent="FIELD CLUE: "+d.hint;
el("feedback").textContent="";el("next").disabled=true;
el("next").textContent=i===questions.length-1?"See results →":"Next question →";
el("choices").innerHTML="";
d.a.slice().sort(()=>Math.random()-.5).forEach(answer=>{
const b=document.createElement("button");b.className="choice";b.textContent=answer;
b.onclick=()=>choose(b,answer,d);el("choices").appendChild(b);
});
}
function choose(btn,answer,d){
if(locked)return;locked=true;
[...el("choices").children].forEach(b=>{b.disabled=true;if(b.textContent===d.c)b.classList.add("right")});
if(answer===d.c){points++;el("feedback").textContent="✓ Correct! "+d.e;el("feedback").style.color="#287332"}
else{btn.classList.add("wrong");el("feedback").textContent="Not quite. "+d.e;el("feedback").style.color="#a33d2d"}
el("score").textContent=points;el("next").disabled=false;el("progress").style.width=((i+1)/questions.length*100)+"%";
}
function result(){
el("game").style.display="none";el("finish").style.display="block";
const pct=Math.round(points/questions.length*100);
el("emoji").textContent=pct>=80?"🏆":pct>=50?"🧭":"🦕";
el("resultTitle").textContent=pct>=80?"Excellent, explorer!":pct>=50?"Good work, ranger!":"Keep practicing!";
el("resultText").textContent=`You scored ${points} out of 9 (${pct}%). ${pct>=80?"You mastered the daily routines vocabulary!":"Review the words and try the mission again."}`;
}
function reset(){i=0;points=0;el("game").style.display="block";el("finish").style.display="none";show()}
el("next").onclick=()=>{if(!locked)return;if(i<questions.length-1){i++;show()}else result()};
el("restart").onclick=reset;el("again").onclick=reset;show();
</script>
</body>
</html>
