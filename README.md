```html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>اختبار تحويل الوحدات</title>

<style>
body {
    font-family: 'Segoe UI', Tahoma;
    background: linear-gradient(135deg, #ffecd2, #fcb69f);
    margin: 0;
}

.container {
    max-width: 900px;
    margin: 30px auto;
    background: rgba(255,255,255,0.95);
    padding: 30px;
    border-radius: 20px;
    box-shadow: 0 10px 40px rgba(0,0,0,0.2);
}

h1 { text-align: center; color: #d35400; }

.top-bar {
    display: flex;
    justify-content: space-between;
    margin-bottom: 20px;
    font-weight: bold;
    color: #e67e22;
}

.progress-bar {
    height: 10px;
    background: #eee;
    border-radius: 10px;
    overflow: hidden;
    margin-bottom: 20px;
}

.progress {
    height: 100%;
    width: 0%;
    background: linear-gradient(to right, #ff7e5f, #feb47b);
}

.option {
    background: #fff7f0;
    border: 1px solid #f0c8a0;
    padding: 12px;
    border-radius: 10px;
    margin-bottom: 10px;
    cursor: pointer;
}

.option:hover { background: #ffe0cc; }

.correct { background: #d4edda !important; }
.incorrect { background: #f8d7da !important; }

.rationale { display: none; font-size: 0.9rem; }

button {
    padding: 10px 15px;
    border: none;
    border-radius: 10px;
    background: #e67e22;
    color: white;
    cursor: pointer;
}

button:disabled { background: #ccc; cursor: not-allowed; }
</style>
</head>

<body>

<div class="container">
<h1>📏 اختبار تحويل الوحدات</h1>

<div class="top-bar">
<div id="score">0 / 5</div>
<div id="timer">⏱️ 5:00</div>
</div>

<div class="progress-bar">
<div class="progress" id="progress"></div>
</div>

<div id="quiz"></div>

<div id="resultScreen" style="display:none; text-align:center;">
<h2 id="finalScore"></h2>
<button onclick="restartQuiz()">🔁 إعادة المحاولة</button>
</div>
</div>

<script>
const quizData = [
{
question:"جزء 1 طوله 4.8 cm و جزء 2 طوله 35 mm. المجموع؟",
hint:"حوّل mm إلى cm",
correct:1,
options:[
{text:"39.8 cm",rationale:"خطأ"},
{text:"8.3 cm",rationale:"صحيح"},
{text:"83 cm",rationale:"خطأ"},
{text:"5.15 cm",rationale:"خطأ"}
]
},
{
question:"12.4 cm نقصنا 46 mm كم الباقي mm؟",
hint:"حوّل أولاً",
correct:2,
options:[
{text:"7.8 mm",rationale:"خطأ"},
{text:"12 mm",rationale:"خطأ"},
{text:"78 mm",rationale:"صحيح"},
{text:"170 mm",rationale:"خطأ"}
]
},
{
question:"2.5 cm و 48 mm كم المجموع mm؟",
hint:"حوّل cm إلى mm",
correct:2,
options:[
{text:"50.5 mm",rationale:"خطأ"},
{text:"7.3 mm",rationale:"خطأ"},
{text:"73 mm",rationale:"صحيح"},
{text:"23 mm",rationale:"خطأ"}
]
},
{
question:"0.24 متر كم؟",
hint:"1m=100cm",
correct:0,
options:[
{text:"24 cm و 240 mm",rationale:"صحيح"},
{text:"2.4 cm",rationale:"خطأ"},
{text:"240 cm",rationale:"خطأ"},
{text:"24 mm",rationale:"خطأ"}
]
},
{
question:"72 mm نصفها كم cm؟",
hint:"اقسم ثم حوّل",
correct:1,
options:[
{text:"36 cm",rationale:"خطأ"},
{text:"3.6 cm",rationale:"صحيح"},
{text:"7.2 cm",rationale:"خطأ"},
{text:"0.36 cm",rationale:"خطأ"}
]
}
];

let current=0;
let time=300;
let timer=null;
let started=false;
let answers=new Array(quizData.length).fill(null);

const quiz=document.getElementById("quiz");

quiz.innerHTML=`
<div style="text-align:center;">
<h2>جاهز؟</h2>
<button onclick="startQuiz()">▶️ ابدأ</button>
</div>
`;

function formatTime(s){
let m=Math.floor(s/60);
let sec=s%60;
return m+":"+(sec<10?"0"+sec:sec);
}

function startQuiz(){
if(!started){started=true;startTimer();}
loadQuestion();
}

function startTimer(){
if(timer) return;
timer=setInterval(()=>{
time--;
document.getElementById("timer").innerText="⏱️ "+formatTime(time);
if(time<=0) endQuiz();
},1000);
}

function loadQuestion(){
const q=quizData[current];

quiz.innerHTML=`
<div><strong>(${current+1})</strong> ${q.question}</div>
<div>${q.hint}</div>

${q.options.map((opt,i)=>`
<div class="option" onclick="selectAnswer(${i})">
${opt.text}
<div class="rationale">${opt.rationale}</div>
</div>
`).join("")}

<div style="display:flex;justify-content:space-between;margin-top:15px;">
<button onclick="prevQuestion()" ${current===0?'disabled':''}>⬅️ السابق</button>
<button onclick="nextQuestion()">${current===quizData.length-1?'إنهاء':'التالي ➡️'}</button>
</div>
`;

document.getElementById("progress").style.width=(current/quizData.length)*100+"%";

if(answers[current]!=null) showResult(answers[current]);
}

function selectAnswer(i){
if(!started){started=true;startTimer();}
answers[current]=i;
showResult(i);
}

function showResult(i){
const q=quizData[current];
const opts=document.querySelectorAll(".option");

opts.forEach((el,idx)=>{
el.classList.remove("correct","incorrect");

if(idx===q.correct) el.classList.add("correct");
else if(idx===i) el.classList.add("incorrect");

el.querySelector(".rationale").style.display="block";
});
}

function nextQuestion(){
if(current<quizData.length-1){current++;loadQuestion();}
else endQuiz();
}

function prevQuestion(){
if(current>0){current--;loadQuestion();}
}

function endQuiz(){
clearInterval(timer);

let score=0;
quizData.forEach((q,i)=>{
if(answers[i]===q.correct) score++;
});

quiz.style.display="none";
document.getElementById("resultScreen").style.display="block";
document.getElementById("finalScore").innerText=`🎉 نتيجتك: ${score} / ${quizData.length}`;
}

function restartQuiz(){
current=0;
time=300;
timer=null;
started=false;
answers.fill(null);

quiz.style.display="block";
document.getElementById("resultScreen").style.display="none";
document.getElementById("timer").innerText="⏱️ 5:00";
document.getElementById("score").innerText="0 / 5";

quiz.innerHTML=`
<div style="text-align:center;">
<h2>جاهز؟</h2>
<button onclick="startQuiz()">▶️ ابدأ</button>
</div>
`;
}
</script>

</body>
</html>
```
