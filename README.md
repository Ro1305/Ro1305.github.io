<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Fitness Tracker</title>
<style>
*{box-sizing:border-box;margin:0;padding:0}
:root{
 --bg0:#1a1a1a;--bg1:#242424;--bg2:#2e2e2e;
 --border:rgba(255,255,255,0.1);--border2:rgba(255,255,255,0.2);
 --text1:#f0f0f0;--text2:#a0a0a0;--text3:#666;
}
body{font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',sans-serif;color:var(--text1);background:var(--bg0);min-height:100vh}
.app{max-width:600px;margin:0 auto;padding:0.75rem}
.tabs{display:flex;gap:4px;margin-bottom:1rem;border-bottom:0.5px solid var(--border);padding-bottom:0}
.tab{flex:1;padding:8px 4px;font-size:13px;cursor:pointer;border:none;background:none;color:var(--text2);border-bottom:2px solid transparent;margin-bottom:-0.5px;text-align:center}
.tab.active{color:var(--text1);border-bottom:2px solid var(--text1);font-weight:500}
.section{display:none}.section.active{display:block}
.cal-header{display:flex;align-items:center;justify-content:space-between;margin-bottom:0.75rem}
.cal-nav{background:none;border:0.5px solid var(--border);border-radius:8px;padding:6px 14px;cursor:pointer;font-size:16px;color:var(--text1);min-width:44px;min-height:44px}
.view-toggle{display:flex;gap:6px}
.vt-btn{background:none;border:0.5px solid var(--border);border-radius:8px;padding:5px 10px;cursor:pointer;font-size:12px;color:var(--text2)}
.vt-btn.active{background:var(--bg2);color:var(--text1);border-color:var(--border2)}
.cal-grid{display:grid;grid-template-columns:repeat(7,1fr);gap:3px}
.cal-dow{text-align:center;font-size:11px;color:var(--text2);padding:3px 0;font-weight:500}
.cal-day{min-height:52px;border:0.5px solid var(--border);border-radius:6px;padding:3px;cursor:pointer;background:var(--bg1)}
.cal-day:active{background:var(--bg2)}
.cal-day.empty{background:none;border-color:transparent;cursor:default}
.cal-day.today{border-color:rgba(255,255,255,0.4)}
.cal-day .day-num{font-size:11px;color:var(--text2)}
.cal-day.today .day-num{font-weight:500;color:var(--text1)}
.sport-badge{font-size:9px;padding:1px 4px;border-radius:3px;margin-top:2px;text-align:center;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.planned{opacity:0.55}
.week-list{display:flex;flex-direction:column;gap:6px}
.week-day-row{display:flex;align-items:stretch;gap:8px;min-height:52px;cursor:pointer;border:0.5px solid var(--border);border-radius:8px;padding:8px;background:var(--bg1)}
.week-day-row:active{background:var(--bg2)}
.week-day-row.today{border-color:rgba(255,255,255,0.4)}
.week-date{min-width:44px;display:flex;flex-direction:column;align-items:center;justify-content:center}
.week-dow{font-size:11px;color:var(--text2);font-weight:500}
.week-num{font-size:18px;font-weight:500;color:var(--text1)}
.week-day-row.today .week-num{color:#60a5fa}
.week-entries{flex:1;display:flex;flex-wrap:wrap;gap:4px;align-items:center}
.week-badge{font-size:11px;padding:3px 8px;border-radius:8px}
.week-empty{font-size:12px;color:var(--text3);font-style:italic}
.modal-bg{display:none;position:fixed;inset:0;background:rgba(0,0,0,0.6);z-index:100;align-items:flex-end;justify-content:center}
.modal-bg.open{display:flex}
.modal{background:var(--bg1);border-radius:12px 12px 0 0;padding:1.25rem;width:100%;max-width:600px;border-top:0.5px solid var(--border);padding-bottom:calc(1.25rem + env(safe-area-inset-bottom,0px))}
.modal h3{font-size:15px;font-weight:500;margin-bottom:1rem;color:var(--text1)}
.field{margin-bottom:10px}
.field label{font-size:12px;color:var(--text2);display:block;margin-bottom:3px}
.field select,.field input{width:100%;font-size:15px;min-height:40px;background:var(--bg2);color:var(--text1);border:0.5px solid var(--border);border-radius:8px;padding:0 10px}
.modal-actions{display:flex;gap:8px;margin-top:1rem}
.btn-del{background:none;border:0.5px solid #A32D2D;color:#f87171;border-radius:8px;padding:10px 14px;cursor:pointer;font-size:14px;min-height:44px}
.btn-save{flex:1;background:var(--text1);border:none;color:var(--bg0);border-radius:8px;padding:10px 14px;cursor:pointer;font-size:14px;min-height:44px}
.btn-cancel{background:none;border:0.5px solid var(--border);border-radius:8px;padding:10px 14px;cursor:pointer;font-size:14px;color:var(--text2);min-height:44px}
.btn-add{width:100%;background:none;border:1px dashed var(--border2);color:var(--text2);border-radius:8px;padding:12px;cursor:pointer;margin-bottom:1rem;font-size:13px}
.entry-item{display:flex;justify-content:space-between;align-items:center;background:var(--bg2);padding:10px 14px;border-radius:8px;margin-bottom:8px;cursor:pointer}
.stat-grid{display:grid;grid-template-columns:repeat(2,1fr);gap:10px;margin-bottom:1rem}
.stat-card{background:var(--bg1);border-radius:8px;padding:0.85rem}
.stat-card .sport-icon{font-size:20px;margin-bottom:4px}
.stat-card .sport-name{font-size:12px;color:var(--text2);margin-bottom:3px}
.stat-card .km{font-size:20px;font-weight:500}
.stat-card .sessions{font-size:11px;color:var(--text2);margin-top:2px}
.period-filter{display:flex;gap:6px;margin-bottom:1rem}
.pf-btn{flex:1;background:none;border:0.5px solid var(--border);border-radius:8px;padding:7px 4px;cursor:pointer;font-size:12px;color:var(--text2);min-height:36px}
.pf-btn.active{background:var(--bg2);color:var(--text1);border-color:var(--border2)}
.legend{display:flex;flex-wrap:wrap;gap:6px;margin-bottom:0.75rem}
.legend-item{display:flex;align-items:center;gap:4px;font-size:11px;color:var(--text2)}
.legend-dot{width:8px;height:8px;border-radius:50%;flex-shrink:0}
.instr-list{display:flex;flex-direction:column;gap:10px}
.instr-card{background:var(--bg1);border:0.5px solid var(--border);border-radius:12px;padding:0.85rem 1rem}
.instr-card h3{font-size:14px;font-weight:500;margin-bottom:6px;display:flex;align-items:center;gap:6px}
.instr-card textarea{width:100%;font-size:13px;color:var(--text1);background:var(--bg2);border:0.5px solid var(--border);border-radius:8px;padding:8px;resize:vertical;min-height:80px;font-family:-apple-system,sans-serif}
.save-msg{font-size:11px;color:#4ade80;margin-top:3px;display:none}
</style>
</head>
<body>

<div class="app">
 <div class="tabs">
   <button class="tab active" onclick="showTab('calendar')">Calendrier</button>
   <button class="tab" onclick="showTab('stats')">Stats</button>
   <button class="tab" onclick="showTab('instructions')">Instructions</button>
 </div>

 <div id="calendar" class="section active">
   <div class="cal-header">
     <button class="cal-nav" onclick="changeMonth(-1)">&#8592;</button>
     <span id="month-label" style="font-size:15px;font-weight:500"></span>
     <button class="cal-nav" onclick="changeMonth(1)">&#8594;</button>
   </div>
   <div style="display:flex;justify-content:flex-end;margin-bottom:8px;">
     <div class="view-toggle">
       <button class="vt-btn active" id="vt-month" onclick="setView('month')">Mois</button>
       <button class="vt-btn" id="vt-week" onclick="setView('week')">Semaine</button>
     </div>
   </div>
   <div class="legend" id="legend"></div>
   <div id="cal-container"></div>
 </div>

 <div id="stats" class="section">
   <div class="period-filter">
     <button class="pf-btn active" onclick="setPeriod('all',this)">Tout</button>
     <button class="pf-btn" onclick="setPeriod('month',this)">Ce mois</button>
     <button class="pf-btn" onclick="setPeriod('week',this)">Semaine</button>
   </div>
   <div class="stat-grid" id="stat-grid"></div>
 </div>

 <div id="instructions" class="section">
   <div class="instr-list" id="instr-list"></div>
 </div>
</div>

<div class="modal-bg" id="modal-bg" onclick="handleBgClick(event)">
 <div class="modal">
   <h3 id="modal-title">Séances</h3>
   
   <div id="modal-list-view">
     <div id="entries-list"></div>
     <button class="btn-add" onclick="showForm()">+ Ajouter un sport</button>
     <button class="btn-cancel" style="width:100%" onclick="closeModal()">Fermer</button>
   </div>

   <div id="modal-form-view" style="display:none">
     <div class="field">
       <label>Sport</label>
       <select id="m-sport"></select>
     </div>
     <div class="field">
       <label>Type</label>
       <select id="m-type">
         <option value="done">Réalisée</option>
         <option value="planned">Planifiée</option>
       </select>
     </div>
     <div id="km-field" class="field">
       <label>Distance (km)</label>
       <input type="number" id="m-km" min="0" step="0.1" placeholder="0" inputmode="decimal">
     </div>
     <div class="field">
       <label>Notes</label>
       <input type="text" id="m-notes" placeholder="Optionnel">
     </div>
     <div class="modal-actions">
       <button class="btn-del" id="btn-del" onclick="deleteEntry()">Suppr.</button>
       <button class="btn-cancel" onclick="hideForm()">Annuler</button>
       <button class="btn-save" onclick="saveEntry()">Enregistrer</button>
     </div>
   </div>
 </div>
</div>

<script>
const SPORTS=[
 {id:'run',name:'Course',icon:'🏃',color:'#D85A30',hasKm:true},
 {id:'bike',name:'Velo',icon:'🚴',color:'#1D9E75',hasKm:true},
 {id:'swim',name:'Natation',icon:'🏊',color:'#378ADD',hasKm:true},
 {id:'gym',name:'Muscu',icon:'💪',color:'#7F77DD',hasKm:false},
 {id:'walk',name:'Marche',icon:'🚶',color:'#BA7517',hasKm:true},
 {id:'climb',name:'Escalade',icon:'🧗',color:'#A32D2D',hasKm:false}
];

let entries={},instructions={};
let currentDate=new Date();
let editKey=null, editIdx=null, statPeriod='all', calView='month', weekOffset=0;

function save(){try{localStorage.setItem('ft2_e',JSON.stringify(entries));localStorage.setItem('ft2_i',JSON.stringify(instructions));}catch(e){}}
function load(){try{const e=localStorage.getItem('ft2_e');if(e)entries=JSON.parse(e);const i=localStorage.getItem('ft2_i');if(i)instructions=JSON.parse(i);}catch(e){}}

function key(d){return d.toISOString().slice(0,10)}
function today(){return key(new Date())}

function showTab(id){
 document.querySelectorAll('.tab').forEach((t,i)=>{t.classList.toggle('active',['calendar','stats','instructions'][i]===id)});
 document.querySelectorAll('.section').forEach(s=>s.classList.remove('active'));
 document.getElementById(id).classList.add('active');
 if(id==='stats')renderStats();
 if(id==='instructions')renderInstructions();
}

function setView(v){
 calView=v;
 document.getElementById('vt-month').classList.toggle('active',v==='month');
 document.getElementById('vt-week').classList.toggle('active',v==='week');
 if(v==='week')weekOffset=0;
 renderCalendar();
}

const MONTHS=['Janvier','Fevrier','Mars','Avril','Mai','Juin','Juillet','Aout','Septembre','Octobre','Novembre','Decembre'];
const DAYS=['Lun','Mar','Mer','Jeu','Ven','Sam','Dim'];

function changeMonth(d){
 if(calView==='month'){currentDate=new Date(currentDate.getFullYear(),currentDate.getMonth()+d,1);}
 else{weekOffset+=d;}
 renderCalendar();
}

function getWeekDates(offset){
 const now=new Date();
 const dow=(now.getDay()+6)%7;
 const mon=new Date(now);mon.setDate(now.getDate()-dow+offset*7);mon.setHours(0,0,0,0);
 return Array.from({length:7},(_,i)=>{const d=new Date(mon);d.setDate(mon.getDate()+i);return d;});
}

function renderCalendar(){
 const todayKey=today();
 document.getElementById('legend').innerHTML=SPORTS.map(s=>`<span class="legend-item"><span class="legend-dot" style="background:${s.color}"></span>${s.name}</span>`).join('');
 const container=document.getElementById('cal-container');
 const navLabel=document.getElementById('month-label');

 if(calView==='month'){
   const y=currentDate.getFullYear(),m=currentDate.getMonth();
   navLabel.textContent=MONTHS[m]+' '+y;
   const firstDow=(new Date(y,m,1).getDay()+6)%7;
   const daysInMonth=new Date(y,m+1,0).getDate();
   let html=`<div class="cal-grid">${DAYS.map(d=>`<div class="cal-dow">${d}</div>`).join('')}`;
   for(let i=0;i<firstDow;i++)html+=`<div class="cal-day empty"></div>`;
   for(let d=1;d<=daysInMonth;d++){
     const k=key(new Date(y,m,d));
     const isToday=k===todayKey;
     const dayE=entries[k]||[];
     let inner=`<div class="day-num">${d}</div>`;
     dayE.forEach(e=>{
       const sp=SPORTS.find(s=>s.id===e.sport);if(!sp)return;
       const label=sp.hasKm&&e.km>0?e.km+'k':sp.name.slice(0,3);
       inner+=`<div class="sport-badge ${e.type==='planned'?'planned':''}" style="background:${sp.color}22;color:${sp.color}">${label}</div>`;
     });
     html+=`<div class="cal-day${isToday?' today':''}" onclick="openModal('${k}')">${inner}</div>`;
   }
   html+=`</div>`;
   container.innerHTML=html;
 } else {
   const days=getWeekDates(weekOffset);
   const fmt=d=>d.toLocaleDateString('fr-FR',{day:'numeric',month:'short'});
   navLabel.textContent=fmt(days[0])+' - '+fmt(days[6]);
   let html=`<div class="week-list">`;
   days.forEach(d=>{
     const k=key(d);
     const isToday=k===todayKey;
     const dow=DAYS[(d.getDay()+6)%7];
     const dayE=entries[k]||[];
     let badges=dayE.length?dayE.map(e=>{
       const sp=SPORTS.find(s=>s.id===e.sport);if(!sp)return'';
       const label=sp.hasKm&&e.km>0?sp.name+' '+e.km+'km':sp.name;
       return`<span class="week-badge ${e.type==='planned'?'planned':''}" style="background:${sp.color}22;color:${sp.color}">${label}</span>`;
     }).join(''):`<span class="week-empty">Aucune seance</span>`;
     html+=`<div class="week-day-row${isToday?' today':''}" onclick="openModal('${k}')">
       <div class="week-date"><div class="week-dow">${dow}</div><div class="week-num">${d.getDate()}</div></div>
       <div class="week-entries">${badges}</div>
     </div>`;
   });
   html+=`</div>`;
   container.innerHTML=html;
 }
}

function openModal(k){
 editKey=k;
 const d=new Date(k+'T12:00:00');
 document.getElementById('modal-title').textContent=d.toLocaleDateString('fr-FR',{weekday:'long',day:'numeric',month:'long'});
 renderEntriesList();
 hideForm();
 document.getElementById('modal-bg').classList.add('open');
}

function renderEntriesList(){
 const list=document.getElementById('entries-list');
 const dayE=entries[editKey]||[];
 list.innerHTML=dayE.map((e,i)=>{
   const sp=SPORTS.find(s=>s.id===e.sport);
   return `<div class="entry-item" onclick="showForm(${i})">
     <div style="color:${sp.color};font-size:14px;font-weight:500">
       ${sp.icon} ${sp.name} ${sp.hasKm && e.km ? '· '+e.km+'km' : ''}
       ${e.type==='planned' ? '<span style="opacity:0.5;font-weight:400;font-size:11px"> (Planifié)</span>' : ''}
     </div>
     <div style="color:var(--text3);font-size:12px">modifier ❯</div>
   </div>`;
 }).join('');
}

function showForm(idx = null){
 editIdx = idx;
 const sel=document.getElementById('m-sport');
 sel.innerHTML=SPORTS.map(s=>`<option value="${s.id}">${s.icon} ${s.name}</option>`).join('');
 
 if(idx !== null){
   const e = entries[editKey][idx];
   sel.value = e.sport;
   document.getElementById('m-type').value = e.type || 'done';
   document.getElementById('m-km').value = e.km || '';
   document.getElementById('m-notes').value = e.notes || '';
   document.getElementById('btn-del').style.display = 'inline-block';
 } else {
   document.getElementById('m-type').value = 'done';
   document.getElementById('m-km').value = '';
   document.getElementById('m-notes').value = '';
   document.getElementById('btn-del').style.display = 'none';
 }
 
 updateKmField();
 sel.onchange = updateKmField;
 document.getElementById('modal-list-view').style.display='none';
 document.getElementById('modal-form-view').style.display='block';
}

function hideForm(){
 document.getElementById('modal-list-view').style.display='block';
 document.getElementById('modal-form-view').style.display='none';
 editIdx = null;
}

function updateKmField(){
 const sp=SPORTS.find(s=>s.id===document.getElementById('m-sport').value);
 document.getElementById('km-field').style.display=sp&&sp.hasKm?'block':'none';
}

function handleBgClick(e){if(e.target===document.getElementById('modal-bg'))closeModal();}
function closeModal(){document.getElementById('modal-bg').classList.remove('open');editKey=null;}

function saveEntry(){
 if(!editKey)return;
 const sp=SPORTS.find(s=>s.id===document.getElementById('m-sport').value);
 const newEntry = {
   sport:document.getElementById('m-sport').value,
   type:document.getElementById('m-type').value,
   km:sp&&sp.hasKm?parseFloat(document.getElementById('m-km').value)||0:0,
   notes:document.getElementById('m-notes').value
 };
 
 if(!entries[editKey]) entries[editKey] = [];
 if(editIdx !== null) entries[editKey][editIdx] = newEntry;
 else entries[editKey].push(newEntry);
 
 save(); hideForm(); renderEntriesList(); renderCalendar();
}

function deleteEntry(){
 if(editKey && editIdx !== null){
   entries[editKey].splice(editIdx, 1);
   if(entries[editKey].length === 0) delete entries[editKey];
   save(); hideForm(); renderEntriesList(); renderCalendar();
 }
}

function setPeriod(p,btn){
 statPeriod=p;
 document.querySelectorAll('.pf-btn').forEach(b=>b.classList.remove('active'));
 btn.classList.add('active');renderStats();
}

function renderStats(){
 const now=new Date();
 const stats={};
 SPORTS.forEach(s=>{stats[s.id]={km:0,sessions:0};});
 Object.entries(entries).forEach(([k,arr])=>{
   if(!arr||!arr.length)return;
   const d=new Date(k+'T12:00:00');
   if(statPeriod==='week'){
     const wd=new Date(now);wd.setDate(now.getDate()-((now.getDay()+6)%7));wd.setHours(0,0,0,0);
     if(d<wd)return;
   } else if(statPeriod==='month'){
     if(d.getFullYear()!==now.getFullYear()||d.getMonth()!==now.getMonth())return;
   }
   arr.forEach(e=>{
     if(e.type==='done'&&stats[e.sport]!==undefined){
       stats[e.sport].km+=e.km||0;stats[e.sport].sessions++;
     }
   });
 });
 document.getElementById('stat-grid').innerHTML=SPORTS.map(s=>{
   const st=stats[s.id];
   const mainStat=s.hasKm
     ? `<div class="km" style="color:${s.color}">${st.km.toFixed(1)} km</div>`
     : `<div class="km" style="color:${s.color}">${st.sessions} fois</div>`;
   return`<div class="stat-card">
     <div class="sport-icon">${s.icon}</div>
     <div class="sport-name">${s.name}</div>
     ${mainStat}
     <div class="sessions">${st.sessions} seance${st.sessions!==1?'s':''}</div>
   </div>`;
 }).join('');
}

function renderInstructions(){
 document.getElementById('instr-list').innerHTML=SPORTS.map(s=>`
   <div class="instr-card">
     <h3><span style="font-size:15px">${s.icon}</span><span style="color:${s.color}">${s.name}</span></h3>
     <textarea id="instr-${s.id}" placeholder="Instructions, exercices, conseils pour ${s.name}..."
       oninput="autoSaveInstr('${s.id}')">${instructions[s.id]||''}</textarea>
     <div class="save-msg" id="msg-${s.id}">Enregistre</div>
   </div>`).join('');
}

let saveTimers={};
function autoSaveInstr(id){
 clearTimeout(saveTimers[id]);
 saveTimers[id]=setTimeout(()=>{
   instructions[id]=document.getElementById('instr-'+id).value;
   save();
   const msg=document.getElementById('msg-'+id);
   msg.style.display='block';
   setTimeout(()=>{msg.style.display='none';},1500);
 },800);
}

load();renderCalendar();
</script>
</body>
</html>
