<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Danger Assessment — WAST → DA</title>
  <style>
    :root{
      --bg:#f4f7fb; --card:#ffffff; --muted:#666; --accent:#0b5fff;
      --danger:#7a0b0b; --success:#0b3d91;
    }
    *{box-sizing:border-box}
    body{font-family:Inter,system-ui,Segoe UI,Roboto,Arial,sans-serif;background:var(--bg);margin:0;padding:28px;color:#111}
    .container{max-width:760px;margin:0 auto}
    .card{background:var(--card);border-radius:12px;box-shadow:0 8px 30px rgba(12,24,40,.06);padding:22px;margin-bottom:16px}
    h1{margin:0 0 6px;font-size:1.35rem}
    p.lead{margin:0 0 12px;color:var(--muted)}
    .consent{display:flex;gap:12px;align-items:center;margin-bottom:14px}
    .progressWrap{height:12px;background:#e6eefc;border-radius:999px;overflow:hidden;margin-bottom:18px}
    .progressFill{height:100%;width:0%;background:linear-gradient(90deg,#0b5fff,#3ea0ff);transition:width .35s ease}
    .questionCard{padding:18px;border-radius:10px;background:#fbfdff;border:1px solid rgba(11,95,255,0.04)}
    .qText{font-weight:700;margin-bottom:12px}
    .choices{display:flex;gap:12px;align-items:center;flex-wrap:wrap}
    .btnRow{display:flex;gap:8px;justify-content:flex-end;margin-top:16px}
    button{background:var(--accent);color:#fff;border:0;padding:10px 14px;border-radius:10px;cursor:pointer;font-weight:600}
    button.secondary{background:#eef5ff;color:var(--accent)}
    button.ghost{background:transparent;color:var(--accent);border:1px solid rgba(11,95,255,0.08)}
    button[disabled]{opacity:.5;cursor:not-allowed}
    .muted{color:var(--muted);font-size:.95rem}
    .result{padding:16px;border-radius:10px;margin-top:12px}
    .low{background:#f0f9ff;color:var(--success)}
    .elev{background:#fff7ed;color:#7a4b00}
    .high{background:#fff0f6;color:#6b1846}
    .ext{background:#fff2f2;color:var(--danger)}
    .tiny{font-size:.88rem;color:var(--muted)}
    label.radioLabel{display:inline-flex;gap:8px;align-items:center;padding:10px 12px;border-radius:8px;border:1px solid #e6eefc}
    .qMeta{margin-top:10px;color:var(--muted);font-size:.92rem}
    .brief-desc{background:#fbfdff;border-radius:8px;padding:12px;margin-bottom:12px;border:1px solid rgba(11,95,255,0.04)}

    /* modal */
    .modalBackdrop{position:fixed;inset:0;background:rgba(2,6,23,0.6);display:flex;align-items:center;justify-content:center;padding:20px;z-index:9999}
    .modal{max-width:760px;width:100%;background:#fff;border-radius:12px;padding:18px;box-shadow:0 12px 40px rgba(2,6,23,0.15);max-height:80vh;overflow:auto}
    .modal h3{margin-top:0}
    .modalClose{float:right;background:transparent;border:0;font-weight:700;color:var(--muted);cursor:pointer}

    @media(min-width:720px){ .questionCard{padding:24px} }
  </style>
</head>
<body>
  <div class="container">
    <div class="card" role="main" aria-labelledby="title">
      <h1 id="title">Danger Assessment — WAST → DA</h1>
      <p class="lead">Client-side educational prototype. Not a substitute for professional advice. If you are in immediate danger, call emergency services now.</p>

      <!-- Brief descriptions displayed at the start so users can contextualize up front -->
      <div class="brief-desc" aria-hidden="false">
        <strong>What these assessments measure (brief):</strong>
        <p class="tiny" style="margin:8px 0 0">The <strong>WAST</strong> (Women Abuse Screening Tool) is a short survey that screens for patterns of tension and abuse in a relationship. It helps identify possible or likely abuse but is not a diagnostic instrument.</p>
        <p class="tiny" style="margin:6px 0 0">The <strong>Danger Assessment (DA)</strong> evaluates factors associated with risk of serious or lethal violence. It uses yes/no items to estimate danger levels and inform safety planning.</p>
        <div style="margin-top:8px"><button id="learnMoreBtn" class="ghost" type="button">Learn more about these assessments</button></div>
      </div>

      <div class="consent">
        <label style="display:flex;gap:8px;align-items:center">
          <input id="consent" type="checkbox" />
          <span class="tiny">I understand this is not a substitute for professional advice and I want to continue.</span>
        </label>
      </div>

      <div id="app" style="display:none">
        <div class="progressWrap" aria-hidden="true"><div id="progressFill" class="progressFill"></div></div>

        <div class="questionCard" id="questionCard">
          <div class="qText" id="qText">Question text</div>

          <div id="specialArea" class="qMeta" style="display:none"></div>

          <div class="choices" id="choices">
            <!-- radios generated dynamically -->
          </div>

          <div class="qMeta" id="qMeta" style="display:none"></div>

          <div class="btnRow">
            <button id="nextBtn" disabled>Next</button>
            <button id="resetBtn" class="secondary" type="button">Reset</button>
          </div>

          <div id="resultBox" style="display:none"></div>
        </div>

        <div class="tiny" style="margin-top:12px">
          <strong>Note:</strong> This tool shows final WAST and DA results together only after both instruments are completed. WAST wording and scoring follow the validated instrument by Brown et al. (1996).
        </div>
      </div>

      <div id="noConsent" class="tiny">
        <p><strong>Note:</strong> You must check the box above to continue. If you are in immediate danger, call emergency services now.</p>
      </div>
    </div>

    <div class="card">
      <h2 style="margin-top:0">Resources & Safety</h2>
      <p class="muted">US National Domestic Violence Hotline: <strong>1-800-799-7233</strong> (TTY 1-800-787-3224). Visit <a href="https://www.thehotline.org" target="_blank" rel="noopener">thehotline.org</a>.</p>
      <p class="muted">If outside the U.S., contact local emergency services or search for domestic violence helplines in your area.</p>
    </div>

    <footer class="tiny">
      <p>Prototype &copy; Educational demo. No data stored or transmitted. For official guidance and training, consult the WAST publications and DA manual.</p>
    </footer>
  </div>

  <!-- Modal used to show comprehensive descriptions when requested -->
  <div id="infoModal" style="display:none">
    <div class="modalBackdrop" role="dialog" aria-modal="true">
      <div class="modal" id="modalContent">
        <button class="modalClose" id="closeModal">✕</button>
        <h3 id="modalTitle">Assessment details</h3>
        <div id="modalBody" style="margin-top:8px"></div>
      </div>
    </div>
  </div>

  <script>
    // ------------------- WAST and DA setup -------------------
    const wastItems = [
      "1. In general, how would you describe your relationship? (A lot of tension / Some tension / No tension)",
      "2. Do you and your partner work out arguments with: (Great difficulty / Some difficulty / No difficulty)",
      "3. Do arguments ever result in you feeling down or bad about yourself? (Often / Sometimes / Never)",
      "4. Do arguments ever result in hitting, kicking or pushing? (Often / Sometimes / Never)",
      "5. Do you ever feel frightened by what your partner says or does? (Often / Sometimes / Never)",
      "6. Has your partner ever abused you physically? (Often / Sometimes / Never)",
      "7. Has your partner ever abused you emotionally? (Often / Sometimes / Never)",
      "8. Has your partner ever abused you sexually? (Often / Sometimes / Never)"
    ];

    const daQuestions = [
      "1. Has the abuse increased in severity or frequency over the past year?",
      "2. Has he ever used a weapon (gun, knife) against you or threatened you with a weapon?",
      "3. Has he ever tried to choke you or strangle you?",
      "4. Has he ever threatened or tried to kill you?",
      "5. Has he ever forced you to have sex when you did not want to?",
      "6. Is he violently or constantly jealous of you?",
      "7. Has he ever broken your bones or beaten you so badly you thought you might die?",
      "8. Has he ever been violent toward your children?",
      "9. Does he control most of your daily activities (where you go, who you see)?",
      "10. Does he ever force you to do things you don't want to do that are degrading?",
      "11. Does he try to isolate you from family or friends?",
      "12. Has he ever threatened to physically harm your children?",
      "13. Has he ever attempted suicide or threatened suicide in relation to your relationship?",
      "14. Do you believe he is capable of killing you?",
      "15. Has he ever attempted to kill you or someone close to you?",
      "16. Is he currently unemployed or recently lost his job and the stress increased violence?",
      "17. Has he ever been apart from you and then come back and been violent?",
      "18. Does he use drugs or alcohol in a way that increases the violence?",
      "19. Has he ever been arrested for domestic violence?",
      "20. Do you live with him now?"
    ];

    const totalItems = wastItems.length + daQuestions.length;
    const wastAnswers = Array(wastItems.length).fill(null);
    const daAnswers = Array(daQuestions.length).fill(null);
    let q3Threat = false;
    let overallIdx = 0;
    let stage = 'WAST';

    const consent = document.getElementById('consent');
    const app = document.getElementById('app');
    const noConsent = document.getElementById('noConsent');
    const qText = document.getElementById('qText');
    const choices = document.getElementById('choices');
    const nextBtn = document.getElementById('nextBtn');
    const resetBtn = document.getElementById('resetBtn');
    const progressFill = document.getElementById('progressFill');
    const specialArea = document.getElementById('specialArea');
    const qMeta = document.getElementById('qMeta');
    const resultBox = document.getElementById('resultBox');
    const learnMoreBtn = document.getElementById('learnMoreBtn');
    const infoModal = document.getElementById('infoModal');
    const modalBody = document.getElementById('modalBody');
    const modalTitle = document.getElementById('modalTitle');
    const closeModal = document.getElementById('closeModal');

    consent.addEventListener('change', e=>{
      const ok = e.target.checked;
      app.style.display = ok ? 'block' : 'none';
      noConsent.style.display = ok ? 'none' : 'block';
      if(ok){ overallIdx = 0; stage = 'WAST'; renderCurrent(); }
    });

    learnMoreBtn.addEventListener('click',()=>{ showModal('overview'); });
    closeModal.addEventListener('click',()=>{ hideModal(); });

    function showModal(section){
      infoModal.style.display='block';
      modalBody.innerHTML='';
      if(section==='overview'){
        modalTitle.textContent='About the WAST and Danger Assessment (detailed)';
        modalBody.innerHTML = `
          <h4>WAST — What it measures</h4>
          <p>The Women Abuse Screening Tool (WAST) is a brief screening instrument developed to identify relationship tension and potential abuse. It asks about conflict, how arguments are resolved, and the emotional and physical consequences of arguments. Scores suggest risk categories (No/Low risk, Possible abuse, Likely abuse) but are not a clinical diagnosis. If your results indicate possible or likely abuse, consider seeking support from a trained professional.</p>

          <h4>Danger Assessment (DA) — What it measures</h4>
          <p>The Danger Assessment asks about specific behaviors and risk factors tied to severe or lethal intimate partner violence (e.g., weapon use, strangulation, threats to kill, escalation in frequency). Items are weighted in scoring because some factors correlate strongly with lethal outcomes. The DA categories (Variable, Elevated, High, Extreme danger) are intended to assist safety planning — higher scores indicate higher risk and may warrant immediate safety actions and professional intervention.</p>

          <h4>Important notes</h4>
          <p>These tools are educational and screening tools. They do not replace clinical assessment. If you are in immediate danger, call emergency services. If you are concerned after completing the assessment, contact local support services or the national hotline provided on this page.</p>
        `;
      } else if(section==='results'){
        modalTitle.textContent='Interpreting your results — Comprehensive guide';
        modalBody.innerHTML = `
          <h4>WAST score interpretation (comprehensive)</h4>
          <p>The WAST yields a numerical score (range 8–24). Lower scores indicate fewer reported tensions or incidents; higher scores indicate more frequent or severe issues. Typical breakpoints used here are:</p>
          <ul>
            <li><strong>No / Low risk:</strong> responses largely indicating no tension or conflict resolution — recommends monitoring and routine supports.</li>
            <li><strong>Possible abuse:</strong> signs of conflict and some harmful behaviors — consider discussing concerns with a counselor or advocate.</li>
            <li><strong>Likely abuse:</strong> repeated or severe patterns — professional intervention and safety planning recommended.</li>
          </ul>

          <h4>Danger Assessment (DA) scoring — details</h4>
          <p>The DA assigns weights to certain items (for example, weapon use, strangulation, prior attempts to kill) because these are strong predictors of severe harm. The numeric score translates into categories used to guide urgency:</p>
          <ul>
            <li><strong>Variable danger (lower scores):</strong> Some risk factors present; consider creating a safety plan and accessing support.</li>
            <li><strong>Elevated danger:</strong> Multiple concerning factors; reach out to local services and consider additional safety measures.</li>
            <li><strong>High danger:</strong> Several high-risk indicators present — prioritize immediate safety planning and professional help.</li>
            <li><strong>Extreme danger:</strong> Very high likelihood of severe or lethal violence — contact emergency services if in immediate danger and engage specialized domestic violence services.</li>
          </ul>

          <h4>Next steps and resources</h4>
          <p>Higher scores do not mean blame; they indicate a need for protective action. Consider contacting hotlines, local shelters, and healthcare or advocacy professionals who can help tailor a safety plan.</p>
        `;
      }
    }

    function hideModal(){ infoModal.style.display='none'; }

    function updateProgress(){
      const pct = Math.round(((overallIdx) / (totalItems - 1)) * 100);
      progressFill.style.width = pct + '%';
    }

    function renderCurrent(){
      resultBox.style.display = 'none';
      qMeta.style.display = 'none';
      specialArea.style.display = 'none';
      choices.innerHTML = '';
      nextBtn.disabled = true;

      if(overallIdx < wastItems.length){
        stage = 'WAST';
        const localIdx = overallIdx;
        qText.textContent = wastItems[localIdx];
        createWastRadios(wastAnswers[localIdx], val=>{ wastAnswers[localIdx]=Number(val); nextBtn.disabled=false; });
        nextBtn.textContent = 'Next';
        updateProgress();
      } else {
        stage = 'DA';
        const daIdx = overallIdx - wastItems.length;
        qText.textContent = daQuestions[daIdx];
        createYesNoRadios(daAnswers[daIdx], val=>{ daAnswers[daIdx]=val; nextBtn.disabled=false; });
        if(daIdx === 2){
          specialArea.style.display = 'block';
          specialArea.innerHTML = '';
          const specialLabel = document.createElement('label');
          specialLabel.style.display='inline-flex'; specialLabel.style.gap='8px';
          const chk = document.createElement('input');
          chk.type='checkbox'; chk.id='q3ThreatChk'; chk.checked=q3Threat;
          chk.addEventListener('change', e=>{ q3Threat=e.target.checked; });
          specialLabel.appendChild(chk);
          specialLabel.append('Has he ever threatened to kill you? (check if applicable)');
          specialArea.appendChild(specialLabel);
        }
        nextBtn.textContent = (overallIdx===totalItems-1)?'Finish':'Next';
        updateProgress();
      }
    }

    function createWastRadios(currentValue,onChange){
      const opts=[
        {val:'3',label:'3 — Highest (A lot / Great difficulty / Often)'},
        {val:'2',label:'2 — Moderate (Some / Some difficulty / Sometimes)'},
        {val:'1',label:'1 — Lowest (No / No difficulty / Never)'}
      ];
      opts.forEach(o=>{
        const lab=document.createElement('label'); lab.className='radioLabel';
        const inp=document.createElement('input'); inp.type='radio'; inp.name='answer'; inp.value=o.val;
        lab.appendChild(inp); lab.append(' '+o.label); choices.appendChild(lab);
        if(Number(currentValue)===Number(o.val)) inp.checked=true;
      });
      choices.querySelectorAll('input[name="answer"]').forEach(r=>r.addEventListener('change',()=>onChange(r.value)));
    }

    function createYesNoRadios(currentValue,onChange){
      const yes=document.createElement('label'); yes.className='radioLabel';
      const y=document.createElement('input'); y.type='radio'; y.name='answer'; y.value='yes';
      yes.appendChild(y); yes.append(' Yes');
      const no=document.createElement('label'); no.className='radioLabel';
      const n=document.createElement('input'); n.type='radio'; n.name='answer'; n.value='no';
      no.appendChild(n); no.append(' No');
      choices.append(yes,no);
      if(currentValue==='yes') y.checked=true; if(currentValue==='no') n.checked=true;
      choices.querySelectorAll('input[name="answer"]').forEach(r=>r.addEventListener('change',()=>onChange(r.value)));
    }

    nextBtn.addEventListener('click',()=>{
      if(overallIdx< wastItems.length && wastAnswers[overallIdx]==null) return;
      if(overallIdx>=wastItems.length){
        const daIdx=overallIdx-wastItems.length;
        if(daAnswers[daIdx]==null) return;
      }
      if(overallIdx===totalItems-1){ showCombinedResults(); return; }
      overallIdx++; renderCurrent();
    });

    resetBtn.addEventListener('click',()=>{
      if(!confirm('Reset all answers?')) return;
      wastAnswers.fill(null); daAnswers.fill(null); q3Threat=false; overallIdx=0; stage='WAST'; renderCurrent(); progressFill.style.width='0%';
    });

    function computeWAST(){
      let total=0; wastAnswers.forEach(x=> total+=(Number(x)||0));
      let category=''; if(total<=13) category='No / Low risk'; else if(total<=17) category='Possible abuse'; else category='Likely abuse';
      return {total,category};
    }

    function computeDA(){
      let baseYes=daAnswers.filter(x=>x==='yes').length;
      function y(q){return daAnswers[q-1]==='yes';}
      let score=baseYes;
      if(y(2))score+=4; if(y(3))score+=4; if(y(4))score+=3; if(y(5))score+=2; if(y(6))score+=2; if(y(7))score+=2; if(y(8))score+=1; if(y(9))score+=1;
      if(q3Threat)score-=3;
      let category='',cls='',expl='';
      if(score<8){category='Variable danger';cls='low';expl='Your score falls in the Variable danger range.';}
      else if(score<=13){category='Elevated danger';cls='elev';expl='Your score indicates Elevated danger.';}
      else if(score<=17){category='High danger';cls='high';expl='Your score indicates High danger.';}
      else{category='Extreme danger';cls='ext';expl='Your score indicates Extreme danger.';}
      return {score,baseYes,category,cls,expl};
    }

    function showCombinedResults(){
      const wast=computeWAST(); const da=computeDA();
      resultBox.style.display='block'; resultBox.className='result '+da.cls;
      resultBox.innerHTML=`
        <div style="font-weight:700;font-size:1.05rem">Results</div>
        <div style="margin-top:10px;font-weight:700">WAST — Score: ${wast.total} — ${wast.category}</div>
        <div style="margin-top:6px" class="tiny">WAST range: 8–24. (Brown et al., 1996)</div>
        <div style="margin-top:12px;font-weight:700">Danger Assessment — Score: ${da.score} — ${da.category}</div>
        <div style="margin-top:8px">${da.expl}</div>
        <div style="margin-top:8px" class="tiny">Base yes count: ${da.baseYes}</div>
        <div style="margin-top:12px;font-weight:700">Resources: If in immediate danger call emergency services. US National Domestic Violence Hotline: 1-800-799-7233.</div>
        <div style="margin-top:12px"><button id="printBtn" class="secondary">Print / Save Result</button> <button id="viewDetailsBtn" class="ghost" type="button">View comprehensive description of results</button></div>

        <!-- Local resource finder appended below results -->
        <div id="localResourcesCard" class="card" style="margin-top:20px">
          <h2 style="margin-top:0">Find Local Support Resources</h2>
          <p class="muted">Enter your ZIP code or let us use your location to find nearby shelters and support services.</p>
          <div style="display:flex;gap:8px;flex-wrap:wrap;align-items:center">
            <input type="text" id="zipInput" placeholder="Enter ZIP code" style="flex:1;min-width:120px;padding:8px;border:1px solid #ccc;border-radius:6px" />
            <button id="zipSearchBtn">Search by ZIP</button>
            <button id="locSearchBtn">Use my location</button>
          </div>
          <div id="resourcesResults" style="margin-top:12px"></div>
        </div>
      `;
      progressFill.style.width='100%';
      document.getElementById('printBtn').addEventListener('click',()=>window.print());
      document.getElementById('viewDetailsBtn').addEventListener('click',()=>{ showModal('results'); });
      resultBox.scrollIntoView({behavior:'smooth'});
      stage='DONE';
      setupResourceFinder();
    }

    function setupResourceFinder(){
      const zipInput=document.getElementById('zipInput');
      const zipSearchBtn=document.getElementById('zipSearchBtn');
      const locSearchBtn=document.getElementById('locSearchBtn');
      const resultsDiv=document.getElementById('resourcesResults');

      zipSearchBtn.addEventListener('click',()=>{
        const zip=zipInput.value.trim();
        if(!zip){resultsDiv.innerHTML='<p class="tiny">Please enter a ZIP code.</p>';return;}
        fetchResourcesByZip(zip);
      });

      locSearchBtn.addEventListener('click',()=>{
        if(!navigator.geolocation){resultsDiv.innerHTML='<p class="tiny">Geolocation not supported.</p>';return;}
        resultsDiv.innerHTML='<p class="tiny">Locating...</p>';
        navigator.geolocation.getCurrentPosition(
          pos=>fetchResourcesByCoords(pos.coords.latitude,pos.coords.longitude),
          err=>{resultsDiv.innerHTML='<p class="tiny">Location permission denied.</p>';} 
        );
      });

      async function fetchResourcesByZip(zip){
        resultsDiv.innerHTML='<p class="tiny">Searching resources near '+zip+'...</p>';
        try{
          // Example: Using findhelp.org search API
          const resp=await fetch(`https://api.findhelp.org/api/v1/search/?postal=${zip}&limit=5`);
          if(!resp.ok)throw new Error('Search failed');
          const data=await resp.json();
          showResources(data, resultsDiv);
        }catch(e){
          resultsDiv.innerHTML='<p class="tiny">Unable to fetch resources. Please try later.</p>';
        }
      }

      async function fetchResourcesByCoords(lat,lon){
        resultsDiv.innerHTML='<p class="tiny">Searching resources near your location...</p>';
        try{
          const resp=await fetch(`https://api.findhelp.org/api/v1/search/?latitude=${lat}&longitude=${lon}&limit=5`);
          if(!resp.ok)throw new Error('Search failed');
          const data=await resp.json();
          showResources(data, resultsDiv);
        }catch(e){
          resultsDiv.innerHTML='<p class="tiny">Unable to fetch resources. Please try later.</p>';
        }
      }

      function showResources(data, container){
        if(!data || !data.results || !data.results.length){ container.innerHTML='<p class="tiny">No resources found nearby.</p>'; return; }
        const items=data.results.map(r=>`
          <div style="padding:8px 0;border-bottom:1px solid #eee">
            <strong>${r.name||'Unnamed Resource'}</strong><br/>
            ${r.address1||''} ${r.city||''}<br/>
            ${r.phone_number?'<a href="tel:'+r.phone_number+'">'+r.phone_number+'</a><br/>':''}
            ${r.url?'<a href="'+r.url+'" target="_blank" rel="noopener">Website</a>':''}
          </div>`).join('');
        container.innerHTML='<div>'+items+'</div>';
      }
    }

    // Close modal if backdrop clicked
    document.addEventListener('click', (e)=>{
      if(e.target && e.target.classList && e.target.classList.contains('modalBackdrop')){
        hideModal();
      }
    });

    // Ensure renderCurrent defined earlier works
    renderCurrent();

  </script>
</body>
</html>
