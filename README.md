# faxue-zhixue
法学智学iPhone学习应用

<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<meta name="theme-color" content="#243a63">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="default">
<meta name="apple-mobile-web-app-title" content="法学智学">
<title>法学智学</title>
<style>
:root{--blue:#243a63;--bg:#f4f6fa;--card:#fff;--muted:#718096}
*{box-sizing:border-box}body{margin:0;background:var(--bg);font:16px -apple-system,BlinkMacSystemFont,"PingFang SC",sans-serif;color:#202a3a;padding-bottom:82px}
header{background:var(--blue);color:white;padding:26px 20px 22px;border-radius:0 0 22px 22px}
header h1{margin:0 0 7px;font-size:25px}header p{margin:0;color:#dce5f4;font-size:13px}
main{padding:16px;max-width:680px;margin:auto}.panel{display:none}.panel.active{display:block}
h2{font-size:20px;margin:8px 0 14px}.muted{color:var(--muted);font-size:13px}
.stats{display:grid;grid-template-columns:1fr 1fr;gap:10px}.card{background:var(--card);border-radius:15px;padding:15px;margin-bottom:11px;box-shadow:0 2px 10px #182c4d08}
.num{font-size:26px;font-weight:750;margin:5px 0}.bar{height:7px;background:#e8edf5;border-radius:8px;overflow:hidden;margin:10px 0}.bar span{display:block;height:100%;background:#5578b5;border-radius:8px}
input,textarea,select{width:100%;border:1px solid #dce2ec;border-radius:10px;padding:12px;font:inherit;background:#fff;margin:5px 0 12px;color:#202a3a}
textarea{min-height:90px;resize:vertical}button{border:0;border-radius:10px;padding:11px 14px;font:inherit;cursor:pointer}
.primary{background:var(--blue);color:white;width:100%;font-weight:600}.secondary{background:#e9eef7;color:var(--blue)}.danger{background:#fff0f0;color:#a33}
.task{display:flex;align-items:center;gap:10px}.task input[type=checkbox]{width:21px;height:21px;margin:0;flex-shrink:0}.task .words{flex:1;overflow-wrap:anywhere}.done{text-decoration:line-through;color:#8893a3}
.row{display:flex;align-items:center;justify-content:space-between;gap:8px}.row button{padding:8px 10px;font-size:13px}
nav{position:fixed;bottom:0;left:0;right:0;background:#fff;border-top:1px solid #e5e9f0;display:grid;grid-template-columns:repeat(4,1fr);padding:8px 3px max(8px,env(safe-area-inset-bottom));z-index:5}
nav button{background:transparent;color:#778398;padding:8px 1px;font-size:12px}nav button.active{color:var(--blue);font-weight:700}nav span{display:block;font-size:19px;margin-bottom:3px}
label{font-size:13px;color:#5d687a} .empty{color:var(--muted);font-size:13px;padding:8px 0}
</style>
</head>
<body>
<header><h1>⚖️ 法学智学</h1><p>让学习、竞赛与成长有迹可循</p></header>
<main>
<section id="home" class="panel active">
<h2>学习总览</h2>
<div class="stats">
<div class="card"><div class="muted">今日任务完成</div><div class="num" id="summary">0/0</div></div>
<div class="card"><div class="muted">学习方向</div><div class="num">4 项</div><div class="muted">六级 · 专业课 · 法考 · 考公</div></div>
</div>
<div class="card"><div class="row"><b>今日任务</b><button class="secondary" onclick="show('study')">管理任务</button></div><div id="homeTasks"></div></div>
<div class="card"><b>学习进度</b><div id="homeProgress"></div></div>
</section>
<section id="study" class="panel">
<h2>学习中心</h2>
<div class="card">
<label>任务名称</label><input id="taskName" placeholder="例如：复习民法总则">
<label>学习类别</label><select id="taskType"><option>专业课</option><option>英语六级</option><option>法考</option><option>考公</option></select>
<button class="primary" onclick="addTask()">＋ 添加任务</button>
</div>
<div id="tasks"></div>
<div class="card"><b>学习进度调整</b><div id="progress"></div></div>
</section>
<section id="moot" class="panel">
<h2>模拟法庭</h2>
<div class="card">
<label>案件名称</label><input id="caseName" placeholder="填写案件名称">
<label>争议焦点</label><textarea id="caseIssues" placeholder="填写案件争议焦点"></textarea>
<label>证据与待办事项</label><textarea id="caseEvidence" placeholder="证据目录、待补充材料、自由辩论问题……"></textarea>
<button class="primary" onclick="saveCase()">保存案件</button>
</div><div id="cases"></div>
</section>
<section id="project" class="panel">
<h2>项目管理</h2>
<div class="card">
<label>项目名称</label><input id="projectName" placeholder="例如：挑战杯项目">
<label>截止日期（可选）</label><input id="projectDate" type="date">
<label>进度备注</label><textarea id="projectNote" placeholder="阶段任务、分工、材料清单……"></textarea>
<button class="primary" onclick="addProject()">＋ 添加项目</button>
</div><div id="projects"></div>
</section>
</main>
<nav>
<button class="active" data-page="home" onclick="show('home')"><span>⌂</span>首页</button>
<button data-page="study" onclick="show('study')"><span>▤</span>学习</button>
<button data-page="moot" onclick="show('moot')"><span>⚖</span>模拟法庭</button>
<button data-page="project" onclick="show('project')"><span>▣</span>项目</button>
</nav>
<script>
const defaults={tasks:[
{id:1,name:"完成六级词汇学习",type:"英语六级",done:false},
{id:2,name:"复习刑法分论课堂笔记",type:"专业课",done:false},
{id:3,name:"整理模拟法庭辩论问题",type:"专业课",done:true}
],progress:{"英语六级":35,"专业课":60,"法考":15,"考公":10},cases:[],projects:[]};
let data;
try{data=JSON.parse(localStorage.getItem("faxueData"))||defaults}catch(e){data=defaults}
function persist(){try{localStorage.setItem("faxueData",JSON.stringify(data))}catch(e){alert("浏览器无法保存数据，请检查存储空间。")}render()}
function esc(s){return String(s??"").replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]))}
function show(id){document.querySelectorAll(".panel").forEach(x=>x.classList.toggle("active",x.id===id));document.querySelectorAll("nav button").forEach(x=>x.classList.toggle("active",x.dataset.page===id));window.scrollTo(0,0);render()}
function addTask(){let n=document.getElementById("taskName").value.trim();if(!n)return alert("请填写任务名称");data.tasks.unshift({id:Date.now(),name:n,type:document.getElementById("taskType").value,done:false});document.getElementById("taskName").value="";persist()}
function toggleTask(id){let t=data.tasks.find(x=>x.id===id);if(t)t.done=!t.done;persist()}
function deleteItem(key,id){if(confirm("确定删除这条记录吗？")){data[key]=data[key].filter(x=>x.id!==id);persist()}}
function saveCase(){let name=document.getElementById("caseName").value.trim();if(!name)return alert("请填写案件名称");data.cases.unshift({id:Date.now(),name,issues:document.getElementById("caseIssues").value,evidence:document.getElementById("caseEvidence").value});["caseName","caseIssues","caseEvidence"].forEach(id=>document.getElementById(id).value="");persist()}
function addProject(){let name=document.getElementById("projectName").value.trim();if(!name)return alert("请填写项目名称");data.projects.unshift({id:Date.now(),name,date:document.getElementById("projectDate").value,note:document.getElementById("projectNote").value});["projectName","projectDate","projectNote"].forEach(id=>document.getElementById(id).value="");persist()}
function progressHTML(){return Object.entries(data.progress).map(([k,v])=>`<div style="margin-top:12px"><div class="row"><span>${esc(k)}</span><b>${v}%</b></div><div class="bar"><span style="width:${v}%"></span></div><input aria-label="${esc(k)}进度" type="range" min="0" max="100" step="5" value="${v}" onchange="data.progress['${k}']=+this.value;persist()"></div>`).join("")}
function taskHTML(t){return `<div class="card task"><input type="checkbox" ${t.done?"checked":""} onchange="toggleTask(${t.id})"><div class="words"><div class="${t.done?"done":""}">${esc(t.name)}</div><div class="muted">${esc(t.type)}</div></div><button class="danger" onclick="deleteItem('tasks',${t.id})">删除</button></div>`}
function render(){
document.getElementById("summary").textContent=data.tasks.filter(t=>t.done).length+"/"+data.tasks.length;
document.getElementById("homeTasks").innerHTML=data.tasks.length?data.tasks.slice(0,4).map(taskHTML).join(""):'<div class="empty">暂无任务，请到学习中心添加。</div>';
document.getElementById("tasks").innerHTML=data.tasks.length?data.tasks.map(taskHTML).join(""):'<div class="empty">还没有任务。</div>';
document.getElementById("progress").innerHTML=progressHTML();
document.getElementById("homeProgress").innerHTML=Object.entries(data.progress).map(([k,v])=>`<div style="margin-top:12px"><div class="row"><span>${esc(k)}</span><b>${v}%</b></div><div class="bar"><span style="width:${v}%"></span></div></div>`).join("");
document.getElementById("cases").innerHTML=data.cases.length?data.cases.map(c=>`<div class="card"><div class="row"><b>${esc(c.name)}</b><button class="danger" onclick="deleteItem('cases',${c.id})">删除</button></div><p><b>争议焦点</b><br>${esc(c.issues).replace(/\n/g,"<br>")}</p><p><b>证据与待办</b><br>${esc(c.evidence).replace(/\n/g,"<br>")}</p></div>`).join(""):'<div class="empty">暂无案件记录。</div>';
document.getElementById("projects").innerHTML=data.projects.length?data.projects.map(p=>`<div class="card"><div class="row"><b>${esc(p.name)}</b><button class="danger" onclick="deleteItem('projects',${p.id})">删除</button></div><p class="muted">截止日期：${esc(p.date||"未设置")}</p><p>${esc(p.note).replace(/\n/g,"<br>")}</p></div>`).join(""):'<div class="empty">暂无项目，请添加你的第一个项目。</div>';
}
render();
</script>
</body>
</html>
