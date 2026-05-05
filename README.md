<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>اختبار تحويل الوحدات</title>

<!-- MathJax (only used for equations if needed) -->
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

<style>
body {
    font-family: 'Segoe UI', Tahoma;
    background: linear-gradient(135deg, #fff3e0, #ffe0b2);
    margin: 0;
    padding: 20px;
}

.container {
    max-width: 850px;
    margin: auto;
    background: white;
    padding: 30px;
    border-radius: 16px;
    box-shadow: 0 8px 25px rgba(0,0,0,0.1);
}

h1 {
    text-align: center;
    color: #d35400;
}

.score {
    text-align: center;
    font-size: 1.2rem;
    margin-bottom: 20px;
    color: #e67e22;
    font-weight: bold;
}

.question-block {
    margin-bottom: 30px;
    border-bottom: 1px solid #eee;
    padding-bottom: 20px;
}

.question-text {
    font-weight: bold;
    margin-bottom: 10px;
}

.hint {
    color: #888;
    font-style: italic;
    margin-bottom: 10px;
}

.option {
    background: #fff8f2;
    border: 1px solid #f0c8a0;
    padding: 12px;
    border-radius: 10px;
    margin-bottom: 8px;
    cursor: pointer;
    transition: 0.25s;
}

.option:hover {
    background: #ffe0cc;
    transform: scale(1.02);
}

.correct {
    background: #d4edda !important;
    border-color: #27ae60;
}

.incorrect {
    background: #f8d7da !important;
    border-color: #c0392b;
}

.rationale {
    display: none;
    margin-top: 8px;
    font-size: 0.9rem;
}
</style>
</head>

<body>

<div class="container">
    <h1>📏 اختبار تحويل الوحدات</h1>
    <div class="score" id="score">النتيجة: 0 / 5</div>
    <div id="quiz"></div>
</div>

<script>
const quizData = [
{
question: "إذا كان لديك قطعة مستقيمة تتكون من جزأين، طول الجزء الأول 4.8 cm وطول الجزء الثاني 35 mm. ما هو الطول الإجمالي بالسنتيمتر؟",
hint: "حوّل 35 mm إلى cm أولاً.",
correct: 1,
options: [
{ text: "39.8 cm", rationale: "لم توحّد الوحدات." },
{ text: "8.3 cm", rationale: "صحيح! 35 mm = 3.5 cm ثم نجمع." },
{ text: "83 cm", rationale: "هذا بالملليمتر." },
{ text: "5.15 cm", rationale: "خطأ في الحساب." }
]
},
{
question: "قطعة خشبية طولها 12.4 cm. إذا قطعنا منها جزءاً طوله 46 mm، فكم يتبقى بالملليمتر؟",
hint: "حوّل 12.4 cm إلى mm أولاً.",
correct: 2,
options: [
{ text: "7.8 mm", rationale: "هذه بالسنتيمتر." },
{ text: "12 mm", rationale: "خطأ في الحساب." },
{ text: "78 mm", rationale: "صحيح! 124 - 46 = 78." },
{ text: "170 mm", rationale: "قمت بالجمع بدلاً من الطرح." }
]
},
{
question: "إذا كانت AC = 2.5 cm و CB = 48 mm، ما طول AB بالملليمتر؟",
hint: "حوّل 2.5 cm إلى mm.",
correct: 2,
options: [
{ text: "50.5 mm", rationale: "خطأ في الحساب." },
{ text: "7.3 mm", rationale: "هذه بالسنتيمتر." },
{ text: "73 mm", rationale: "صحيح! 25 + 48 = 73." },
{ text: "23 mm", rationale: "طرحت بدل الجمع." }
]
},
{
question: "إذا كان طول كتاب 0.24 متر، كم يساوي بالسنتيمتر والملليمتر؟",
hint: "1 m = 100 cm = 1000 mm",
correct: 0,
options: [
{ text: "24 cm, 240 mm", rationale: "صحيح!" },
{ text: "2.4 cm, 24 mm", rationale: "خطأ في التحويل." },
{ text: "240 cm, 2400 mm", rationale: "مبالغة ×10." },
{ text: "24 cm, 2.4 mm", rationale: "خطأ في الوحدات." }
]
},
{
question: "قطعة طولها 72 mm، ما طول نصفها بالسنتيمتر؟",
hint: "اقسم على 2 ثم حوّل.",
correct: 1,
options: [
{ text: "36 cm", rationale: "هذا بالملليمتر." },
{ text: "3.6 cm", rationale: "صحيح!" },
{ text: "7.2 cm", rationale: "هذا الطول الكامل." },
{ text: "0.36 cm", rationale: "خطأ في الفاصلة." }
]
}
];

let score = 0;
let answered = 0;

const quizContainer = document.getElementById("quiz");

quizData.forEach((q, i) => {
    const block = document.createElement("div");
    block.className = "question-block";

    block.innerHTML = `
        <div class="question-text">${i+1}. ${q.question}</div>
        <div class="hint">💡 ${q.hint}</div>
        ${q.options.map((opt, j) => `
            <div class="option" onclick="selectAnswer(${i}, ${j})" id="q${i}o${j}">
                ${opt.text}
                <div class="rationale">${opt.rationale}</div>
            </div>
        `).join("")}
    `;

    quizContainer.appendChild(block);
});

function selectAnswer(qIndex, optIndex) {
    const q = quizData[qIndex];
    const options = document.querySelectorAll(`[id^="q${qIndex}o"]`);

    if (options[0].style.pointerEvents === "none") return;

    options.forEach((el, i) => {
        el.style.pointerEvents = "none";

        if (i === q.correct) {
            el.classList.add("correct");
        } else if (i === optIndex) {
            el.classList.add("incorrect");
        }

        el.querySelector(".rationale").style.display = "block";
    });

    if (optIndex === q.correct) score++;
    answered++;

    document.getElementById("score").innerText = `النتيجة: ${score} / ${quizData.length}`;
}
</script>

</body>
</html>
