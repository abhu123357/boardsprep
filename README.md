<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Class 10 Board Exam Study Planner</title>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
<link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css" rel="stylesheet">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
/* --- Base Styles --- */
body { font-family:"Poppins",sans-serif; margin:0; background:#0b0f24; color:white; transition: all 0.3s; }
#sidebar { position:fixed; left:0; top:0; width:220px; height:100%; background:#111a3b; padding-top:60px; z-index:1000; transition: all 0.3s; }
#sidebar a { padding:15px 20px; display:block; color:#00eaff; font-size:18px; text-decoration:none; cursor:pointer; }
#sidebar a:hover { background: rgba(0,234,255,0.2); }
h1 { text-align:center; margin-top:20px; color:#00eaff; text-shadow:0 0 10px #00eaff; font-size:28px; }
.section { display:none; padding:20px; max-width:900px; margin:auto; transition: all 0.3s; }
.active-section { display:block; }

.day-box { background: rgba(255,255,255,0.07); margin:18px 0; padding:20px; border-radius:12px; }
.day-title { font-size:18px; font-weight:600; color:#00eaff; }
.chapter { background: rgba(255,255,255,0.12); padding:12px; border-radius:10px; margin:10px 0; cursor:pointer; }
.chapter-header { font-size:16px; font-weight:600; display:flex; justify-content:space-between; align-items:center; }
.chapter-left { display:flex; align-items:center; }
.chapter-header i { font-size:20px; margin-right:10px; color:#00eaff; }
.chapter-date { font-size:13px; background: rgba(255,255,255,0.1); padding:4px 8px; border-radius:6px; color:#ffda79; }
.day-panel { max-height:0; overflow:hidden; transition: max-height 0.35s; margin-top:8px; }
.day-btn { padding:5px 10px; margin:4px; border-radius:6px; background:rgba(255,255,255,0.15); border:none; color:white; cursor:pointer; transition:0.2s; font-size:14px; }
.day-btn.completed { background:#26ff9a; color:black; font-weight:600; }
.day-btn:hover { background:#00eaff; color:black; }

#timerDisplay { font-size:40px; margin:30px 0; text-align:center; }
.timer-buttons { text-align:center; margin-bottom:50px; }
.timer-buttons button { font-size:16px; padding:10px 20px; margin:8px; border:none; border-radius:6px; cursor:pointer; background:#00eaff; color:black; transition:0.2s; }
.timer-buttons button:hover { background:#26ff9a; }

#progressBarContainer { width:90%; background:rgba(255,255,255,0.1); margin:30px auto; border-radius:20px; padding:3px; }
#progressBar { height:25px; width:0%; background:#26ff9a; border-radius:15px; transition:0.5s; }
#stats { font-size:16px; margin-top:10px; text-align:center; }

.subject-select { text-align:center; margin-bottom:20px; }
.subject-select button { margin:5px; padding:6px 12px; font-size:14px; cursor:pointer; border:none; border-radius:6px; background:#00eaff; color:black; }
.subject-select button.active { background:#26ff9a; color:black; font-weight:600; }

/* Day/Night mode toggle */
#modeToggle { position:fixed; top:15px; right:15px; font-size:24px; cursor:pointer; color:#ffda79; z-index:2000; }

/* --- Responsive --- */
@media(max-width:768px){
    #sidebar{ width:60px; padding-top:20px;}
    #sidebar a{ font-size:14px; padding:12px; text-align:center;}
    h1{ font-size:22px;}
    .day-title{ font-size:16px;}
    .chapter-header{ font-size:14px;}
    #timerDisplay{ font-size:30px;}
}
</style>
</head>
<body>

<!-- Day/Night mode toggle -->
<i id="modeToggle" class="fa-solid fa-moon"></i>

<!-- Sidebar -->
<div id="sidebar">
    <a onclick="showSection('plannerSection')"><i class="fa-solid fa-book"></i> Planner</a>
    <a onclick="showSection('timerSection')"><i class="fa-solid fa-clock"></i> Timer</a>
    <a onclick="showSection('progressSection')"><i class="fa-solid fa-chart-simple"></i> Progress</a>
</div>

<!-- Sections -->
<div id="plannerSection" class="section active-section">
    <h1>📘 Study Planner</h1>
    <div class="subject-select">
        <button class="subject-btn active" onclick="switchSubject('Maths')">Maths</button>
        <button class="subject-btn" onclick="switchSubject('Science')">Science</button>
        <button class="subject-btn" onclick="switchSubject('SSC')">SSC</button>
        <button class="subject-btn" onclick="switchSubject('Computer')">Computer Apps</button>
    </div>
    <div id="planner"></div>
</div>

<div id="timerSection" class="section">
    <h1>⏱ Study Timer (10 Hours)</h1>
    <div id="timerDisplay">10:00:00</div>
    <div class="timer-buttons">
        <button id="startBtn">Start</button>
        <button id="pauseBtn">Pause</button>
        <button id="resetBtn">Reset</button>
    </div>
</div>

<div id="progressSection" class="section">
    <h1>📊 Progress Tracker</h1>
    <div id="progressBarContainer">
        <div id="progressBar"></div>
    </div>
    <div id="stats"></div>
</div>

<script>
// === Day/Night mode ===
let nightMode = false;
const modeToggle = document.getElementById('modeToggle');
modeToggle.onclick = ()=>{
    nightMode = !nightMode;
    if(nightMode){
        document.body.style.background="#f0f0f0";
        document.body.style.color="#111a3b";
        document.querySelectorAll('.chapter').forEach(c=>c.style.background="rgba(0,0,0,0.05)");
        modeToggle.className="fa-solid fa-sun";
    } else {
        document.body.style.background="#0b0f24";
        document.body.style.color="white";
        document.querySelectorAll('.chapter').forEach(c=>c.style.background="rgba(255,255,255,0.12)");
        modeToggle.className="fa-solid fa-moon";
    }
}

// === Section switch ===
function showSection(sectionId){
    document.querySelectorAll('.section').forEach(s=>s.classList.remove('active-section'));
    document.getElementById(sectionId).classList.add('active-section');
}

// === Subjects & Chapters with dates from today for Maths ===
const today = new Date();
function formatDate(d){ return d.toDateString(); }

const subjectsData = {
    "Maths": {
        chapters:[
            {name:"Real Numbers", difficulty:"easy"},
            {name:"Polynomials", difficulty:"easy"},
            {name:"Pair of Linear Equations", difficulty:"medium"},
            {name:"Quadratic Equations", difficulty:"hard"},
            {name:"Arithmetic Progressions", difficulty:"easy"},
            {name:"Triangles", difficulty:"medium"},
            {name:"Coordinate Geometry", difficulty:"easy"},
            {name:"Intro to Trigonometry", difficulty:"hard"},
            {name:"Applications of Trigonometry", difficulty:"medium"},
            {name:"Circles", difficulty:"easy"},
            {name:"Areas Related to Circles", difficulty:"medium"},
            {name:"Surface Areas & Volumes", difficulty:"hard"},
            {name:"Statistics", difficulty:"easy"},
            {name:"Probability", difficulty:"easy"}
        ],
        start: new Date("2025-12-08")
    },
    "Science": {
        chapters:[
            {name:"Chemical Reactions", difficulty:"medium"},
            {name:"Acids, Bases & Salts", difficulty:"medium"},
            {name:"Metals & Non-metals", difficulty:"hard"},
            {name:"Carbon & Its Compounds", difficulty:"hard"},
            {name:"Periodic Table", difficulty:"easy"},
            {name:"Life Processes", difficulty:"medium"},
            {name:"Control & Coordination", difficulty:"medium"},
            {name:"Heredity & Evolution", difficulty:"hard"},
            {name:"Light", difficulty:"easy"},
            {name:"Electricity", difficulty:"medium"},
            {name:"Magnetic Effects", difficulty:"medium"},
            {name:"Sources of Energy", difficulty:"easy"},
            {name:"Environmental Issues", difficulty:"easy"}
        ],
        start: new Date("2025-12-21")
    },
    "SSC": {
        chapters:[
            {name:"Geography", difficulty:"medium"},
            {name:"History", difficulty:"medium"},
            {name:"Economics", difficulty:"hard"},
            {name:"Civics", difficulty:"easy"}
        ],
        start: new Date("2025-12-25")
    },
    "Computer": {
        chapters:[
            {name:"Networking", difficulty:"medium"},
            {name:"HTML", difficulty:"medium"},
            {name:"Cyber Ethics", difficulty:"hard"}
        ],
        start: new Date("2026-01-01")
    }
};

const difficultyDays = { easy:2, medium:3, hard:4 };
let currentSubject = "Maths";
let progress = JSON.parse(localStorage.getItem("chapterProgress")) || {};

// Switch Subject
function switchSubject(subject){
    currentSubject = subject;
    document.querySelectorAll('.subject-btn').forEach(b=>b.classList.remove('active'));
    document.querySelectorAll('.subject-btn').forEach(b=>{if(b.textContent===subject)b.classList.add('active')});
    buildPlanner();
}

// Build Planner
function buildPlanner(){
    const plannerDiv = document.getElementById("planner");
    plannerDiv.innerHTML="";
    let data = subjectsData[currentSubject];
    let startDate = new Date(data.start);
    let chapters = data.chapters;

    let schedule = [];
    chapters.forEach(ch=>{
        let days = difficultyDays[ch.difficulty];
        for(let i=0;i<days;i++){
            schedule.push({...ch, dayIndex:i+1});
        }
    });

    schedule.forEach((ch,i)=>{
        let dayBox = document.createElement("div");
        dayBox.className="day-box";

        let date = new Date(startDate);
        date.setDate(date.getDate()+i);
        let dateStr = formatDate(date);

        dayBox.innerHTML=`<div class="day-title">Day ${i+1} ⭐ — <span style="color:#ffda79">${dateStr}</span></div>`;

        let chapDiv = document.createElement("div");
        chapDiv.className="chapter";
        let btnId = `${currentSubject}-${ch.name.replace(/\s+/g,'_')}-day${ch.dayIndex}`;
        chapDiv.innerHTML = `
            <div class="chapter-header">
                <div class="chapter-left"><i class="fa-solid fa-book"></i> ${ch.name}</div>
                <div class="chapter-date">${dateStr}</div>
            </div>
            <div class="day-panel">
                <button class="day-btn" id="${btnId}">Day ${ch.dayIndex}</button>
            </div>
        `;

        let panel = chapDiv.querySelector(".day-panel");
        let btn = chapDiv.querySelector(".day-btn");
        if(progress[btn.id]) btn.classList.add("completed");

        chapDiv.addEventListener("click",e=>{
            if(e.target.classList.contains("day-btn")) return;
            panel.style.maxHeight = panel.style.maxHeight?null:panel.scrollHeight+"px";
        });

        btn.onclick = ()=>{
            btn.classList.toggle("completed");
            progress[btn.id]=btn.classList.contains("completed");
            localStorage.setItem("chapterProgress",JSON.stringify(progress));
            updateProgressBar();
        };

        dayBox.appendChild(chapDiv);
        plannerDiv.appendChild(dayBox);
    });

    updateProgressBar();
}

buildPlanner();

// === Timer (10 hours) ===
let timerDisplay = document.getElementById("timerDisplay");
let startBtn = document.getElementById("startBtn");
let pauseBtn = document.getElementById("pauseBtn");
let resetBtn = document.getElementById("resetBtn");

let totalTime = 10*60*60; // 10 hours
let remaining = totalTime;
let timerInterval = null;

function formatTimer(sec){
    let h = Math.floor(sec/3600);
    let m = Math.floor((sec%3600)/60);
    let s = sec%60;
    return `${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
}
timerDisplay.textContent = formatTimer(remaining);

startBtn.onclick = ()=>{
    if(timerInterval) return;
    timerInterval = setInterval(()=>{
        if(remaining<=0){ clearInterval(timerInterval); timerInterval=null; alert("Time's up!"); remaining=totalTime; timerDisplay.textContent=formatTimer(remaining);}
        else { remaining--; timerDisplay.textContent=formatTimer(remaining); }
    },1000);
};
pauseBtn.onclick = ()=>{ if(timerInterval){ clearInterval(timerInterval); timerInterval=null; } };
resetBtn.onclick = ()=>{ clearInterval(timerInterval); timerInterval=null; remaining=totalTime; timerDisplay.textContent=formatTimer(remaining); };

// === Progress Bar ===
function updateProgressBar(){
    let total = Object.keys(progress).length;
    let completed = Object.values(progress).filter(v=>v).length;
    let percent = total?Math.round((completed/total)*100):0;
    document.getElementById("progressBar").style.width = percent+"%";
    document.getElementById("stats").innerHTML = `Completed ${completed} of ${total} day-tasks (${percent}%)`;
}
updateProgressBar();
</script>

</body>
</html>

