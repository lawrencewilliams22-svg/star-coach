<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>STAR Method Interview Coach</title>
<link href="https://fonts.googleapis.com/css2?family=Outfit:wght@400;500;600;700;800&family=JetBrains+Mono:wght@500;700&display=swap" rel="stylesheet">
<style>
@keyframes spin { to { transform: rotate(360deg); } }
@keyframes fadeUp { from { opacity:0; transform:translateY(10px); } to { opacity:1; transform:translateY(0); } }
@keyframes pulse { 0%,100%{opacity:.6} 50%{opacity:1} }

*{margin:0;padding:0;box-sizing:border-box;}
body{min-height:100vh;background:#0e0e10;color:#e8e8e8;font-family:'Outfit',system-ui,sans-serif;padding:20px 14px;}
.wrap{max-width:740px;margin:0 auto;}
.hdr{display:flex;justify-content:space-between;align-items:center;margin-bottom:22px;}
.logo{font-size:22px;font-weight:800;letter-spacing:.15em;font-family:'JetBrains Mono',monospace;}
.logo em{font-style:normal;font-weight:400;font-size:15px;color:#2A7D6C;margin-left:4px;letter-spacing:.05em;}
.counter{font-size:11px;font-weight:600;color:#2A7D6C;background:#2A7D6C16;padding:4px 12px;border-radius:16px;border:1px solid #2A7D6C30;}

.q-card{background:#18181c;border:1px solid #252528;border-radius:14px;padding:20px 24px;margin-bottom:16px;animation:fadeUp .45s cubic-bezier(.22,1,.36,1);}
.q-top{display:flex;justify-content:space-between;align-items:center;margin-bottom:12px;}
.cat-pill{font-size:11px;font-weight:600;color:#aaa;background:#ffffff08;padding:4px 11px;border-radius:16px;border:1px solid #ffffff10;}
.skip-btn{font-size:11px;font-weight:600;color:#777;background:0;border:1px solid #333;border-radius:16px;padding:4px 13px;cursor:pointer;}
.skip-btn:hover{color:#bbb;border-color:#555;}
.q-text{font-size:18px;font-weight:500;line-height:1.5;color:#f0f0f0;font-style:italic;}

.q-mini{background:#18181c;border:1px solid #252528;border-radius:12px;padding:14px 20px;margin-bottom:16px;}
.q-mini p{font-size:15px;font-style:italic;color:#ccc;line-height:1.45;margin-top:8px;}

.prog{display:flex;gap:6px;margin-bottom:16px;}
.prog-step{flex:1;display:flex;flex-direction:column;align-items:center;gap:5px;padding:10px 6px;border-radius:10px;border:1.5px solid #252528;background:0;cursor:pointer;transition:all .25s;}
.prog-letter{width:30px;height:30px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:13px;font-weight:700;font-family:'JetBrains Mono',monospace;transition:all .25s;}
.prog-label{font-size:10px;font-weight:500;letter-spacing:.02em;transition:color .25s;}

.in-card{background:#18181c;border:1.5px solid #252528;border-radius:14px;padding:20px 24px;margin-bottom:14px;transition:border-color .3s;}
.in-hdr{display:flex;justify-content:space-between;align-items:center;margin-bottom:8px;}
.in-title-row{display:flex;align-items:center;gap:10px;}
.in-letter{width:34px;height:34px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:15px;font-weight:700;color:#fff;font-family:'JetBrains Mono',monospace;}
.in-title{font-size:19px;font-weight:700;}
.tip-btn{font-size:11px;font-weight:600;padding:4px 13px;border-radius:16px;border:1px solid #333;cursor:pointer;background:0;transition:all .2s;}
.in-prompt{font-size:13px;color:#999;line-height:1.55;margin-bottom:12px;}
.tip-popup{display:flex;align-items:flex-start;gap:8px;padding:10px 14px;border-radius:8px;border:1px solid;margin-bottom:12px;font-size:12px;color:#ccc;line-height:1.45;}
textarea{width:100%;background:#0e0e10;border:1px solid #252528;border-radius:10px;padding:14px;font-size:14px;line-height:1.65;color:#e8e8e8;resize:vertical;outline:0;font-family:'Outfit',system-ui,sans-serif;min-height:110px;}
textarea:focus{border-color:#ffffff20;}
textarea::placeholder{color:#444;}
.in-foot{display:flex;justify-content:space-between;align-items:center;margin-top:12px;flex-wrap:wrap;gap:8px;}
.wc-label{font-size:11px;color:#555;}
.nav-btns{display:flex;gap:8px;}

button{font-family:'Outfit',system-ui,sans-serif;}
.btn-back{font-size:12px;font-weight:600;color:#999;background:0;border:1px solid #333;border-radius:8px;padding:9px 18px;cursor:pointer;}
.btn-back:hover{border-color:#555;color:#ccc;}
.btn-next{font-size:12px;font-weight:600;color:#fff;border:0;border-radius:8px;padding:9px 22px;cursor:pointer;transition:opacity .2s;}
.btn-sec{font-size:12px;font-weight:600;color:#999;background:0;border:1px solid #333;border-radius:8px;padding:11px 20px;cursor:pointer;}
.btn-sec:hover{border-color:#555;color:#ccc;}
.btn-pri{font-size:12px;font-weight:600;color:#fff;background:#2A7D6C;border:0;border-radius:8px;padding:11px 22px;cursor:pointer;}
.btn-pri:hover{background:#33947f;}
.btn-coach{font-size:13px;font-weight:700;color:#fff;background:#D94F30;border:0;border-radius:8px;padding:12px 28px;cursor:pointer;}
.btn-coach:hover{background:#e06040;}
.btn-coach:disabled{opacity:.6;cursor:default;}

.footer-hint{text-align:center;font-size:11px;color:#444;}
.fb-actions{display:flex;justify-content:flex-end;gap:10px;margin-top:20px;}

/* Review */
.rev-card{background:#18181c;border:1px solid #252528;border-radius:14px;padding:24px;animation:fadeUp .35s ease;}
.rev-title{font-size:20px;font-weight:700;color:#f0f0f0;margin-bottom:4px;}
.rev-sub{font-size:13px;color:#888;margin-bottom:20px;}
.rev-sect{margin-bottom:18px;padding-bottom:18px;border-bottom:1px solid #1f1f22;}
.rev-sect:last-child{border-bottom:0;}
.rev-sect-hdr{display:flex;align-items:center;gap:8px;margin-bottom:6px;}
.rev-sect-hdr .wc{font-size:10px;color:#555;margin-left:auto;}
.rev-text{font-size:13px;color:#bbb;line-height:1.6;padding-left:36px;white-space:pre-wrap;}
.rev-text em{color:#555;}

/* Feedback */
.fb-card{background:#18181c;border:1px solid #252528;border-radius:14px;padding:24px;animation:fadeUp .35s ease;}
.fb-top{display:flex;align-items:center;gap:20px;margin-bottom:24px;padding-bottom:20px;border-bottom:1px solid #1f1f22;}
.score-big{width:72px;height:72px;border-radius:50%;border:3px solid;display:flex;flex-direction:column;align-items:center;justify-content:center;flex-shrink:0;}
.score-num{font-size:26px;font-weight:800;font-family:'JetBrains Mono',monospace;line-height:1;}
.score-of{font-size:11px;color:#666;font-weight:500;}
.fb-top-right{flex:1;}
.fb-title{font-size:20px;font-weight:700;margin-bottom:8px;}
.strengths{display:flex;flex-wrap:wrap;gap:6px;}
.strength-pill{font-size:11px;font-weight:500;color:#2A7D6C;background:#2A7D6C14;padding:3px 10px;border-radius:12px;border:1px solid #2A7D6C28;}

.fb-sect{padding-bottom:16px;margin-bottom:16px;border-bottom:1px solid #1f1f22;}
.fb-sect:last-child{border-bottom:0;margin-bottom:0;padding-bottom:0;}
.fb-sect-hdr{display:flex;align-items:center;gap:8px;margin-bottom:6px;}
.fb-sect-hdr .label{font-size:13px;font-weight:700;color:#ddd;}
.fb-sect-hdr .score{font-size:12px;font-weight:700;margin-left:auto;font-family:'JetBrains Mono',monospace;}
.fb-you{font-size:12px;color:#777;line-height:1.5;margin-bottom:6px;padding-left:34px;font-style:italic;white-space:pre-wrap;}
.fb-comment{font-size:13px;color:#bbb;line-height:1.55;padding-left:34px;}

.improved-box{background:#0e1a14;border:1px solid #2A7D6C30;border-radius:10px;padding:16px 18px;margin-top:24px;margin-bottom:16px;}
.improved-box h3{font-size:13px;font-weight:700;color:#2A7D6C;margin-bottom:8px;}
.improved-box p{font-size:13px;color:#bbb;line-height:1.6;font-style:italic;}

.tip-box2{background:#1a1610;border:1px solid #C49A2A30;border-radius:10px;padding:16px 18px;margin-bottom:8px;}
.tip-box2 h3{font-size:13px;font-weight:700;color:#C49A2A;margin-bottom:6px;}
.tip-box2 p{font-size:13px;color:#bbb;line-height:1.55;}

.spinner{display:inline-block;width:16px;height:16px;border:2px solid #ffffff44;border-top:2px solid #fff;border-radius:50%;animation:spin .8s linear infinite;vertical-align:middle;margin-right:8px;}

.error-box{background:#18181c;border:1px solid #252528;border-radius:14px;padding:24px;}
.error-text{font-size:14px;color:#D94F30;margin-bottom:16px;}
.self-check{background:#0e0e10;border-radius:10px;padding:16px;}
.self-check h3{font-size:14px;font-weight:700;color:#e8e8e8;margin-bottom:12px;}
.self-check label{display:flex;align-items:center;gap:8px;font-size:13px;color:#aaa;margin-bottom:8px;cursor:pointer;}
.self-check input{accent-color:#2A7D6C;}

.letter{width:26px;height:26px;border-radius:50%;display:inline-flex;align-items:center;justify-content:center;font-size:12px;font-weight:700;color:#fff;font-family:'JetBrains Mono',monospace;flex-shrink:0;}

@media(max-width:500px){
  .q-text{font-size:16px;}
  .prog-step{padding:8px 4px;}
  .in-card{padding:16px 18px;}
  .fb-top{flex-direction:column;text-align:center;}
  .strengths{justify-content:center;}
}
</style>
</head>
<body>
<div class="wrap" id="app"></div>

<script>
const QUESTIONS=[
{cat:"Teamwork",q:"Tell me about a time you had to work closely with someone whose personality was very different from yours."},
{cat:"Problem Solving",q:"Describe a situation where you had to solve a problem with limited information or resources."},
{cat:"Leadership",q:"Give me an example of a time you took the initiative on a project or task without being asked."},
{cat:"Adaptability",q:"Tell me about a time when you had to adapt to a significant change at work or school."},
{cat:"Conflict",q:"Describe a disagreement you had with a coworker, classmate, or supervisor. How did you handle it?"},
{cat:"Failure",q:"Tell me about a time you made a mistake. What did you do to fix it?"},
{cat:"Communication",q:"Give an example of a time you had to explain something complex to someone unfamiliar with the topic."},
{cat:"Time Management",q:"Describe a time you had to juggle multiple priorities with tight deadlines."},
{cat:"Customer Service",q:"Tell me about a time you went above and beyond to help someone."},
{cat:"Creativity",q:"Describe a situation where you came up with a new or unconventional approach to solve a problem."},
{cat:"Pressure",q:"Tell me about a time you had to perform well under significant pressure."},
{cat:"Growth",q:"Give an example of a skill you taught yourself. What motivated you and how did you go about it?"},
{cat:"Teamwork",q:"Tell me about a time a group project didn't go as planned. What role did you play in getting it back on track?"},
{cat:"Accountability",q:"Describe a situation where you had to own up to something that went wrong."},
{cat:"Problem Solving",q:"Tell me about a time you identified a problem before it became urgent. What did you do?"},
{cat:"Adaptability",q:"Give an example of a time you had to learn something new very quickly to accomplish a goal."},
{cat:"Leadership",q:"Describe a time you motivated others to complete a task or reach a goal."},
{cat:"Communication",q:"Tell me about a time you had to deliver difficult news or feedback to someone."},
];

const STEPS=[
{key:"situation",letter:"S",label:"Situation",color:"#D94F30",prompt:"Set the scene. Where were you? What was happening?",ph:"During my junior year, our group project partner dropped the class two days before a major presentation...",tip:"Keep it to 1–2 sentences. Just enough context for the interviewer to follow."},
{key:"task",letter:"T",label:"Task",color:"#C49A2A",prompt:"What was YOUR specific responsibility or challenge?",ph:"I needed to take over their section on market analysis while still completing my own research portion...",tip:"Focus on YOUR role. What was specifically on your shoulders?"},
{key:"action",letter:"A",label:"Action",color:"#2A7D6C",prompt:"What did YOU do? Walk through the specific steps you took.",ph:"I reorganized our timeline, divided the dropped section into smaller tasks, scheduled two extra team meetings...",tip:"Use 'I' not 'we.' Be specific about YOUR individual actions."},
{key:"result",letter:"R",label:"Result",color:"#1B5E8A",prompt:"What was the outcome? Quantify if possible. What did you learn?",ph:"We delivered on time and received an A-. The professor praised the depth of analysis. I learned that proactive communication...",tip:"Numbers make results memorable. Even approximate figures beat vague outcomes."},
];

const ICONS={"Teamwork":"👥","Problem Solving":"🧩","Leadership":"🚀","Adaptability":"🔄","Conflict":"⚡","Failure":"📈","Communication":"💬","Time Management":"⏰","Customer Service":"🤝","Creativity":"💡","Pressure":"🎯","Growth":"🌱","Accountability":"✊"};

function shuffle(a){const b=[...a];for(let i=b.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[b[i],b[j]]=[b[j],b[i]];}return b;}
function wc(s){return s.trim()?s.trim().split(/\s+/).length:0;}
function esc(s){const d=document.createElement('div');d.textContent=s;return d.innerHTML;}
function scoreColor(s){return s>=8?"#2A7D6C":s>=5?"#C49A2A":"#D94F30";}
function scoreLabel(s){return s>=8?"Strong":s>=5?"Getting There":"Needs Work";}

let queue=shuffle(QUESTIONS),qi=0,step=0,ans={situation:"",task:"",action:"",result:""},phase="write",feedback=null,done=0,tipOpen=false,loading=false,errorMsg=null;
const app=document.getElementById('app');
const curQ=()=>queue[qi%queue.length];

function render(){
  if(phase==="feedback")return renderFeedback();
  if(phase==="review")return renderReview();
  renderWrite();
}

function renderWrite(){
  const cq=curQ(),cs=STEPS[step],filled=STEPS.filter(s=>ans[s.key].trim()).length;
  app.innerHTML=`
  <div class="hdr"><div class="logo">STAR<em>Coach</em></div><div>${done>0?`<span class="counter">${done} practiced</span>`:''}</div></div>
  <div class="q-card">
    <div class="q-top"><span class="cat-pill">${ICONS[cq.cat]||"📋"} ${cq.cat}</span><button class="skip-btn" onclick="skipQ()">Skip ↻</button></div>
    <div class="q-text">"${esc(cq.q)}"</div>
  </div>
  <div class="prog">
    ${STEPS.map((st,i)=>{
      const active=i===step,has=ans[st.key].trim().length>0;
      return `<button class="prog-step" style="border-color:${active?st.color:has?st.color+'55':'#252528'};background:${active?st.color+'12':'transparent'}" onclick="goStep(${i})">
        <span class="prog-letter" style="background:${active||has?st.color:'#252528'};color:${active||has?'#fff':'#555'};transform:scale(${active?1.12:1})">${has&&!active?'✓':st.letter}</span>
        <span class="prog-label" style="color:${active?st.color:has?'#bbb':'#555'};font-weight:${active?700:500}">${st.label}</span>
      </button>`;
    }).join('')}
  </div>
  <div class="in-card" style="border-color:${cs.color}40">
    <div class="in-hdr">
      <div class="in-title-row">
        <span class="in-letter" style="background:${cs.color}">${cs.letter}</span>
        <h3 class="in-title" style="color:${cs.color}">${cs.label}</h3>
      </div>
      <button class="tip-btn" style="background:${tipOpen?cs.color+'18':'transparent'};color:${tipOpen?cs.color:'#777'};border-color:${tipOpen?cs.color+'40':'#333'}" onclick="toggleTip()">💡 Tip</button>
    </div>
    <p class="in-prompt">${cs.prompt}</p>
    ${tipOpen?`<div class="tip-popup" style="background:${cs.color}0C;border-color:${cs.color}30"><span>💡</span><span>${cs.tip}</span></div>`:''}
    <textarea id="ta" placeholder="${esc(cs.ph)}" oninput="onInput(this.value)" rows="5">${esc(ans[cs.key])}</textarea>
    <div class="in-foot">
      <span class="wc-label">${wc(ans[cs.key])} words${wc(ans[cs.key])>0&&wc(ans[cs.key])<8?' — add more detail':''}${wc(ans[cs.key])>=75?' — consider tightening up':''}</span>
      <div class="nav-btns">
        ${step>0?'<button class="btn-back" onclick="prevStep()">← Back</button>':''}
        <button class="btn-next" style="background:${cs.color};opacity:${ans[cs.key].trim()?1:0.45}" onclick="nextStep()">${step<3?'Next →':'Review & Get Feedback ✓'}</button>
      </div>
    </div>
  </div>
  <p class="footer-hint">Step ${step+1} of 4 · ${filled}/4 filled · Practice makes interviews feel natural</p>`;
  setTimeout(()=>{const ta=document.getElementById('ta');if(ta){ta.focus();ta.selectionStart=ta.value.length;}},50);
}

function renderReview(){
  const cq=curQ();
  app.innerHTML=`
  <div class="hdr"><div class="logo">STAR<em>Coach</em></div></div>
  <div class="q-mini"><span class="cat-pill">${ICONS[cq.cat]||"📋"} ${cq.cat}</span><p>"${esc(cq.q)}"</p></div>
  <div class="rev-card">
    <h2 class="rev-title">Review Your Response</h2>
    <p class="rev-sub">Read through your full answer. When ready, submit for AI coaching feedback.</p>
    ${STEPS.map(st=>`
      <div class="rev-sect">
        <div class="rev-sect-hdr">
          <span class="letter" style="background:${st.color}">${st.letter}</span>
          <span class="label" style="font-size:13px;font-weight:700;color:#ddd">${st.label}</span>
          <span class="wc">${wc(ans[st.key])} words</span>
        </div>
        <div class="rev-text">${ans[st.key].trim()?esc(ans[st.key]):'<em>No response entered</em>'}</div>
      </div>`).join('')}
  </div>
  <div class="fb-actions">
    <button class="btn-sec" onclick="editResp()">✏️ Edit</button>
    <button class="btn-coach" id="coachBtn" onclick="getFeedback()" ${loading?'disabled':''}>${loading?'<span class="spinner"></span>Analyzing your response...':'🎤 Get Coaching Feedback'}</button>
  </div>`;
}

function renderFeedback(){
  const cq=curQ(),fb=feedback;
  let html=`
  <div class="hdr"><div class="logo">STAR<em>Coach</em></div>${done>0?`<span class="counter">${done} practiced</span>`:''}</div>
  <div class="q-mini"><span class="cat-pill">${ICONS[cq.cat]||"📋"} ${cq.cat}</span><p>"${esc(cq.q)}"</p></div>`;

  if(fb){
    const sc=fb.overall_score;
    html+=`<div class="fb-card">
      <div class="fb-top">
        <div class="score-big" style="border-color:${scoreColor(sc)}">
          <span class="score-num" style="color:${scoreColor(sc)}">${sc}</span><span class="score-of">/10</span>
        </div>
        <div class="fb-top-right">
          <h2 class="fb-title" style="color:${scoreColor(sc)}">${scoreLabel(sc)}</h2>
          <div class="strengths">${(fb.strengths||[]).map(s=>`<span class="strength-pill">✓ ${esc(s)}</span>`).join('')}</div>
        </div>
      </div>
      <div>
        ${STEPS.map(st=>{
          const sfb=fb[st.key+'_feedback'];
          if(!sfb)return '';
          return `<div class="fb-sect">
            <div class="fb-sect-hdr">
              <span class="letter" style="background:${st.color}">${st.letter}</span>
              <span class="label">${st.label}</span>
              <span class="score" style="color:${scoreColor(sfb.score)}">${sfb.score}/10</span>
            </div>
            <div class="fb-you">${esc(ans[st.key].trim()||'(left blank)')}</div>
            <div class="fb-comment">${esc(sfb.comment)}</div>
          </div>`;
        }).join('')}
      </div>
      ${fb.improved_example?`<div class="improved-box"><h3>💎 Here's how it could sound polished:</h3><p>${esc(fb.improved_example)}</p></div>`:''}
      ${fb.one_thing_to_try?`<div class="tip-box2"><h3>🎯 One thing to try next time:</h3><p>${esc(fb.one_thing_to_try)}</p></div>`:''}
    </div>`;
  } else if(errorMsg){
    html+=`<div class="error-box">
      <p class="error-text">${esc(errorMsg)}</p>
      <div class="self-check"><h3>Self-Check Your Response:</h3>
        ${["My Situation is brief (1–2 sentences)","My Task clarifies MY specific role","My Action uses 'I' and describes specific steps","My Result includes a measurable outcome or lesson","The full response takes about 1–2 minutes aloud"].map(c=>`<label><input type="checkbox"> ${c}</label>`).join('')}
      </div>
    </div>`;
  }
  html+=`<div class="fb-actions"><button class="btn-sec" onclick="editResp()">✏️ Edit & Resubmit</button><button class="btn-pri" onclick="nextQuestion()">Next Question →</button></div>`;
  app.innerHTML=html;
}

function onInput(v){ans[STEPS[step].key]=v;}
function goStep(i){step=i;tipOpen=false;render();}
function toggleTip(){tipOpen=!tipOpen;render();}
function nextStep(){if(step<3){step++;tipOpen=false;}else phase="review";render();}
function prevStep(){if(step>0){step--;tipOpen=false;render();}}
function editResp(){phase="write";step=0;feedback=null;errorMsg=null;render();}
function skipQ(){qi=(qi+1)%queue.length;if(qi===0)queue=shuffle(QUESTIONS);step=0;ans={situation:"",task:"",action:"",result:""};phase="write";feedback=null;errorMsg=null;tipOpen=false;render();}
function nextQuestion(){done++;qi=(qi+1)%queue.length;if(qi===0)queue=shuffle(QUESTIONS);step=0;ans={situation:"",task:"",action:"",result:""};phase="write";feedback=null;errorMsg=null;tipOpen=false;render();}

async function getFeedback(){
  loading=true;errorMsg=null;render();
  const cq=curQ();
  const sys=`You are a supportive career coach helping young adults (ages 18-25) practice behavioral interview responses using the STAR method. Your tone is warm, encouraging, and direct — like a mentor who genuinely wants them to succeed. Never be harsh. Always start with something they did well.

Evaluate this STAR response. Respond ONLY with raw JSON (no markdown, no backticks):
{"overall_score":<1-10>,"strengths":["...","..."],"situation_feedback":{"score":<1-10>,"comment":"..."},"task_feedback":{"score":<1-10>,"comment":"..."},"action_feedback":{"score":<1-10>,"comment":"..."},"result_feedback":{"score":<1-10>,"comment":"..."},"improved_example":"A polished 3-4 sentence version keeping their story","one_thing_to_try":"One specific actionable tip"}

If a section is blank or <5 words, score it 1. Be specific — reference their actual words. Keep comments concise.`;

  const msg=`Interview Question: "${cq.q}"\n\nSTAR Response:\nSituation: ${ans.situation||"(left blank)"}\nTask: ${ans.task||"(left blank)"}\nAction: ${ans.action||"(left blank)"}\nResult: ${ans.result||"(left blank)"}`;

  try{
    const res=await fetch("https://api.anthropic.com/v1/messages",{
      method:"POST",headers:{"Content-Type":"application/json"},
      body:JSON.stringify({model:"claude-sonnet-4-20250514",max_tokens:1000,system:sys,messages:[{role:"user",content:msg}]})
    });
    const data=await res.json();
    const text=(data.content||[]).map(b=>b.type==="text"?b.text:"").join("");
    const clean=text.replace(/```json|```/g,"").trim();
    feedback=JSON.parse(clean);
    phase="feedback";
  }catch(e){
    console.error(e);
    errorMsg="Couldn't get AI feedback right now. Use the self-check below to review your response, then try again.";
    phase="feedback";
  }finally{loading=false;render();}
}

render();
</script>
</body>
</html>
