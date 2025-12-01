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

    /* Local Resources Finder Styles */
    .location-btn{
      display:inline-flex;align-items:center;gap:8px;
      background:var(--accent);color:#fff;border:0;
      padding:12px 20px;border-radius:10px;cursor:pointer;font-weight:600;
      transition:opacity .2s ease;
    }
    .location-btn:hover{opacity:.9}
    .location-btn:disabled{opacity:.5;cursor:not-allowed}
    .location-btn svg{width:18px;height:18px}
    .privacy-note{font-size:.82rem;color:var(--muted);margin-bottom:12px;display:flex;align-items:flex-start;gap:6px}
    .privacy-note svg{flex-shrink:0;width:14px;height:14px;margin-top:2px}
    .resources-grid{display:grid;gap:12px;margin-top:16px}
    @media(min-width:600px){.resources-grid{grid-template-columns:repeat(2,1fr)}}
    .resource-card{
      background:#fbfdff;border:1px solid rgba(11,95,255,0.06);
      border-radius:10px;padding:14px;position:relative;
      transition:box-shadow .2s ease;
    }
    .resource-card:hover{box-shadow:0 4px 12px rgba(12,24,40,.08)}
    .resource-badge{
      display:inline-block;font-size:.75rem;font-weight:600;
      padding:3px 8px;border-radius:6px;margin-bottom:8px;
    }
    .badge-shelter{background:#e0f2fe;color:#0369a1}
    .badge-legal{background:#f3e8ff;color:#7c3aed}
    .badge-counseling{background:#dcfce7;color:#15803d}
    .badge-emergency{background:#fee2e2;color:#b91c1c}
    .badge-support{background:#ffedd5;color:#c2410c}
    .badge-hotline{background:#fef3c7;color:#b45309}
    .resource-name{font-weight:700;font-size:1.02rem;margin-bottom:6px;color:#111}
    .resource-address{font-size:.9rem;color:var(--muted);margin-bottom:4px}
    .resource-phone{font-size:.9rem;color:#111;margin-bottom:8px}
    .resource-phone a{color:var(--accent);text-decoration:none}
    .resource-distance{
      position:absolute;top:12px;right:12px;
      font-size:.78rem;background:#e6eefc;color:var(--accent);
      padding:3px 8px;border-radius:999px;font-weight:600;
    }
    .resource-actions{display:flex;gap:8px;margin-top:10px}
    .resource-actions a{
      font-size:.85rem;padding:6px 10px;border-radius:6px;
      text-decoration:none;font-weight:600;
    }
    .resource-actions .directions{background:#eef5ff;color:var(--accent)}
    .resource-actions .call-btn{background:var(--accent);color:#fff}
    .loading-spinner{
      display:inline-block;width:18px;height:18px;
      border:2px solid rgba(255,255,255,.3);border-top-color:#fff;
      border-radius:50%;animation:spin 1s linear infinite;
    }
    @keyframes spin{to{transform:rotate(360deg)}}
    .fade-in{animation:fadeIn .3s ease}
    @keyframes fadeIn{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:translateY(0)}}
    .error-msg{background:#fff2f2;color:var(--danger);padding:12px;border-radius:8px;font-size:.9rem}
    .fallback-resources{margin-top:16px}
    .fallback-resources h4{margin:0 0 10px;font-size:.95rem}
    .fallback-resources ul{margin:0;padding-left:20px}
    .fallback-resources li{margin-bottom:6px}
    .fallback-resources a{color:var(--accent)}

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

    <!-- Local Resources Finder Card -->
    <div class="card" id="localResourcesCard">
      <h2 style="margin-top:0">Find Local Resources Near You</h2>
      <p class="muted">Share your location to find nearby shelters, counseling services, legal aid, and support resources.</p>
      
      <div class="privacy-note">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
        </svg>
        <span>Your location is used only to find nearby resources and is never stored or transmitted to any server.</span>
      </div>

      <button id="shareLocationBtn" class="location-btn" aria-label="Share my location to find local resources">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
        </svg>
        <span id="locationBtnText">Share My Location</span>
      </button>

      <div id="resourcesResults"></div>
    </div>

    <footer class="tiny">
      <p>Prototype &copy; Educational demo. No data stored or transmitted. For official guidance and training, consult the WAST publications and DA manual.</p>
    </footer>
  </div>

  <!-- Modal used to show comprehensive descriptions when requested -->
  <div id="infoModal" style="display:none" aria-hidden="true">
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

    // DOM references
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

    // Consent checkbox toggles the app
    consent.addEventListener('change', e=>{
      const ok = e.target.checked;
      app.style.display = ok ? 'block' : 'none';
      noConsent.style.display = ok ? 'none' : 'block';
      if(ok){
        overallIdx = 0;
        stage = 'WAST';
        renderCurrent();
      }
    });

    // Modal handlers
    learnMoreBtn.addEventListener('click', ()=> showModal('overview'));
    closeModal.addEventListener('click', hideModal);

    function showModal(section){
      modalBody.innerHTML = '';
      if(section === 'overview'){
        modalTitle.textContent = 'About the WAST and Danger Assessment (detailed)';
        modalBody.innerHTML = `
          <h4>WAST — What it measures</h4>
          <p>The Women Abuse Screening Tool (WAST) is a brief screening instrument developed to identify relationship tension and potential abuse. It asks about conflict, how arguments are resolved, and the emotional and physical consequences of arguments. Scores suggest risk categories (No/Low risk, Possible abuse, Likely abuse) but are not a clinical diagnosis. If your results indicate possible or likely abuse, consider seeking support from a trained professional.</p>

          <h4>Danger Assessment (DA) — What it measures</h4>
          <p>The Danger Assessment asks about specific behaviors and risk factors tied to severe or lethal intimate partner violence (e.g., weapon use, strangulation, threats to kill, escalation in frequency). Items are weighted in scoring because some factors correlate strongly with lethal outcomes. The DA categories (Variable, Elevated, High, Extreme danger) are intended to assist safety planning — higher scores indicate higher risk and may warrant immediate safety actions and professional intervention.</p>

          <h4>Important notes</h4>
          <p>These tools are educational and screening tools. They do not replace clinical assessment. If you are in immediate danger, call emergency services. If you are concerned after completing the assessment, contact local support services or the national hotline provided on this page.</p>
        `;
      } else if(section === 'results'){
        modalTitle.textContent = 'Interpreting your results — Comprehensive guide';
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
      infoModal.style.display = 'block';
      infoModal.setAttribute('aria-hidden', 'false');
    }

    function hideModal(){
      infoModal.style.display = 'none';
      infoModal.setAttribute('aria-hidden', 'true');
    }

    // Close modal when clicking backdrop
    document.addEventListener('click', (e)=>{
      if(e.target && e.target.classList && e.target.classList.contains('modalBackdrop')){
        hideModal();
      }
    });

    // Progress update
    function updateProgress(){
      const pct = Math.round(((overallIdx) / (totalItems - 1)) * 100);
      progressFill.style.width = pct + '%';
    }

    // Render current question (WAST then DA)
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

    // Radio builders
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

    // Navigation buttons
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

    // Scoring
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

    // Show combined results
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
      `;
      progressFill.style.width='100%';
      document.getElementById('printBtn').addEventListener('click',()=>window.print());
      document.getElementById('viewDetailsBtn').addEventListener('click',()=> showModal('results'));
      resultBox.scrollIntoView({behavior:'smooth'});
      stage='DONE';
    }

    // ------------------- LOCAL RESOURCES FINDER -------------------
    
    // Curated database of IPV resources by region (sample data - expandable)
    const resourceDatabase = [
      // National resources (always shown as fallback)
      {
        name: "National Domestic Violence Hotline",
        type: "hotline",
        address: "Available 24/7 nationwide",
        city: "National", state: "US", zip: "",
        phone: "1-800-799-7233",
        lat: 0, lng: 0,
        website: "https://www.thehotline.org",
        national: true
      },
      {
        name: "National Sexual Assault Hotline (RAINN)",
        type: "hotline",
        address: "Available 24/7 nationwide",
        city: "National", state: "US", zip: "",
        phone: "1-800-656-4673",
        lat: 0, lng: 0,
        website: "https://www.rainn.org",
        national: true
      },
      {
        name: "National Child Abuse Hotline",
        type: "hotline",
        address: "Available 24/7 nationwide",
        city: "National", state: "US", zip: "",
        phone: "1-800-422-4453",
        lat: 0, lng: 0,
        website: "https://www.childhelp.org",
        national: true
      },
      // Sample regional resources (expandable based on real data)
      // New York Area
      {
        name: "Safe Horizon",
        type: "shelter",
        address: "2 Lafayette St",
        city: "New York", state: "NY", zip: "10007",
        phone: "1-800-621-4673",
        lat: 40.7128, lng: -74.0060,
        website: "https://www.safehorizon.org"
      },
      {
        name: "NYC Family Justice Centers",
        type: "legal",
        address: "350 Jay St",
        city: "Brooklyn", state: "NY", zip: "11201",
        phone: "718-250-5113",
        lat: 40.6932, lng: -73.9868,
        website: "https://www1.nyc.gov/site/ocdv/programs/family-justice-centers.page"
      },
      {
        name: "Sanctuary for Families",
        type: "shelter",
        address: "PO Box 1406",
        city: "New York", state: "NY", zip: "10027",
        phone: "212-349-6009",
        lat: 40.7589, lng: -73.9851,
        website: "https://sanctuaryforfamilies.org"
      },
      // Los Angeles Area
      {
        name: "Peace Over Violence",
        type: "counseling",
        address: "1015 Wilshire Blvd, Suite 200",
        city: "Los Angeles", state: "CA", zip: "90017",
        phone: "213-955-9090",
        lat: 34.0522, lng: -118.2594,
        website: "https://www.peaceoverviolence.org"
      },
      {
        name: "Jenesse Center",
        type: "shelter",
        address: "P.O. Box 90837",
        city: "Los Angeles", state: "CA", zip: "90009",
        phone: "323-563-4000",
        lat: 33.9425, lng: -118.2551,
        website: "https://jenesse.org"
      },
      {
        name: "1736 Family Crisis Center",
        type: "shelter",
        address: "2116 Arlington Ave",
        city: "Los Angeles", state: "CA", zip: "90018",
        phone: "310-379-3620",
        lat: 34.0367, lng: -118.3126,
        website: "https://www.1736familycrisiscenter.org"
      },
      // Chicago Area
      {
        name: "Metropolitan Family Services",
        type: "counseling",
        address: "1 N Dearborn St, Suite 1000",
        city: "Chicago", state: "IL", zip: "60602",
        phone: "312-986-4000",
        lat: 41.8819, lng: -87.6278,
        website: "https://www.metrofamily.org"
      },
      {
        name: "Apna Ghar",
        type: "shelter",
        address: "4350 N Broadway",
        city: "Chicago", state: "IL", zip: "60613",
        phone: "773-334-4663",
        lat: 41.9612, lng: -87.6579,
        website: "https://www.apnaghar.org"
      },
      {
        name: "Sarah's Inn",
        type: "shelter",
        address: "P.O. Box 6360",
        city: "Oak Park", state: "IL", zip: "60302",
        phone: "708-386-3305",
        lat: 41.8850, lng: -87.7845,
        website: "https://www.sarahsinn.org"
      },
      // Houston Area
      {
        name: "Houston Area Women's Center",
        type: "shelter",
        address: "1010 Waugh Dr",
        city: "Houston", state: "TX", zip: "77019",
        phone: "713-528-2121",
        lat: 29.7604, lng: -95.3698,
        website: "https://www.hawc.org"
      },
      {
        name: "Aid to Victims of Domestic Abuse",
        type: "shelter",
        address: "P.O. Box 526227",
        city: "Houston", state: "TX", zip: "77052",
        phone: "713-224-9911",
        lat: 29.7556, lng: -95.3591,
        website: "https://www.avda-tx.org"
      },
      // Phoenix Area
      {
        name: "Arizona Coalition to End Sexual & Domestic Violence",
        type: "support",
        address: "2800 N Central Ave, Suite 1570",
        city: "Phoenix", state: "AZ", zip: "85004",
        phone: "602-279-2900",
        lat: 33.4758, lng: -112.0740,
        website: "https://www.acesdv.org"
      },
      {
        name: "New Life Center",
        type: "shelter",
        address: "P.O. Box 16527",
        city: "Phoenix", state: "AZ", zip: "85011",
        phone: "623-932-4404",
        lat: 33.4484, lng: -112.0740,
        website: "https://www.newlifectr.org"
      },
      // Seattle Area
      {
        name: "New Beginnings",
        type: "shelter",
        address: "P.O. Box 75125",
        city: "Seattle", state: "WA", zip: "98175",
        phone: "206-522-9472",
        lat: 47.6062, lng: -122.3321,
        website: "https://www.newbegin.org"
      },
      {
        name: "LifeWire",
        type: "counseling",
        address: "P.O. Box 6398",
        city: "Bellevue", state: "WA", zip: "98008",
        phone: "425-746-1940",
        lat: 47.6101, lng: -122.2015,
        website: "https://www.lifewire.org"
      },
      // Miami Area
      {
        name: "The Lodge / Victim Response Inc.",
        type: "shelter",
        address: "P.O. Box 430728",
        city: "Miami", state: "FL", zip: "33143",
        phone: "305-693-1170",
        lat: 25.7617, lng: -80.1918,
        website: "https://victimresponse.org"
      },
      {
        name: "Switchboard of Miami - Domestic Violence",
        type: "hotline",
        address: "Miami-Dade County",
        city: "Miami", state: "FL", zip: "",
        phone: "305-358-4357",
        lat: 25.7617, lng: -80.1918,
        website: "https://www.switchboardmiami.org"
      },
      // Denver Area
      {
        name: "SafeHouse Denver",
        type: "shelter",
        address: "P.O. Box 18327",
        city: "Denver", state: "CO", zip: "80218",
        phone: "303-318-9989",
        lat: 39.7392, lng: -104.9903,
        website: "https://safehouse-denver.org"
      },
      {
        name: "Rose Andom Center",
        type: "support",
        address: "1330 Fox St",
        city: "Denver", state: "CO", zip: "80204",
        phone: "720-337-4400",
        lat: 39.7383, lng: -105.0073,
        website: "https://roseandomcenter.org"
      },
      // Atlanta Area
      {
        name: "Partnership Against Domestic Violence",
        type: "shelter",
        address: "P.O. Box 170086",
        city: "Atlanta", state: "GA", zip: "30317",
        phone: "404-873-1766",
        lat: 33.7490, lng: -84.3880,
        website: "https://www.padv.org"
      },
      {
        name: "Georgia Coalition Against Domestic Violence",
        type: "support",
        address: "114 New St, Suite B",
        city: "Decatur", state: "GA", zip: "30030",
        phone: "404-209-0280",
        lat: 33.7748, lng: -84.2963,
        website: "https://gcadv.org"
      },
      // Boston Area
      {
        name: "REACH Beyond Domestic Violence",
        type: "shelter",
        address: "P.O. Box 540024",
        city: "Waltham", state: "MA", zip: "02454",
        phone: "781-891-0724",
        lat: 42.3765, lng: -71.2356,
        website: "https://reachma.org"
      },
      {
        name: "Casa Myrna",
        type: "shelter",
        address: "P.O. Box 18019",
        city: "Boston", state: "MA", zip: "02118",
        phone: "617-521-0100",
        lat: 42.3601, lng: -71.0589,
        website: "https://www.casamyrna.org"
      }
    ];

    // Calculate distance between two points using Haversine formula
    function calculateDistance(lat1, lon1, lat2, lon2) {
      const R = 3959; // Earth's radius in miles
      const dLat = (lat2 - lat1) * Math.PI / 180;
      const dLon = (lon2 - lon1) * Math.PI / 180;
      const a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) *
                Math.sin(dLon/2) * Math.sin(dLon/2);
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
      return R * c;
    }

    // Get badge class based on resource type
    function getBadgeClass(type) {
      const badges = {
        'shelter': 'badge-shelter',
        'legal': 'badge-legal',
        'counseling': 'badge-counseling',
        'emergency': 'badge-emergency',
        'support': 'badge-support',
        'hotline': 'badge-hotline'
      };
      return badges[type] || 'badge-support';
    }

    // Get type label
    function getTypeLabel(type) {
      const labels = {
        'shelter': 'Shelter',
        'legal': 'Legal Services',
        'counseling': 'Counseling',
        'emergency': 'Emergency',
        'support': 'Support Services',
        'hotline': 'Hotline'
      };
      return labels[type] || 'Resource';
    }

    // Format phone for tel: link
    function formatPhoneLink(phone) {
      return phone.replace(/[^0-9]/g, '');
    }

    // Find resources near coordinates
    function findNearbyResources(lat, lng, maxDistance = 100) {
      const nearby = [];
      const national = [];
      
      resourceDatabase.forEach(resource => {
        if (resource.national) {
          national.push({...resource, distance: null});
        } else {
          const distance = calculateDistance(lat, lng, resource.lat, resource.lng);
          if (distance <= maxDistance) {
            nearby.push({...resource, distance: distance});
          }
        }
      });
      
      // Sort by distance
      nearby.sort((a, b) => a.distance - b.distance);
      
      // Return nearby resources first, then national hotlines
      return [...nearby.slice(0, 6), ...national];
    }

    // Render resource card HTML
    function renderResourceCard(resource, index) {
      const distanceHtml = resource.distance !== null 
        ? `<span class="resource-distance">${resource.distance.toFixed(1)} mi</span>` 
        : '';
      
      const directionsUrl = resource.lat && resource.lng && !resource.national
        ? `https://www.google.com/maps/dir/?api=1&destination=${resource.lat},${resource.lng}`
        : null;
      
      return `
        <div class="resource-card fade-in" style="animation-delay:${index * 100}ms">
          <span class="resource-badge ${getBadgeClass(resource.type)}">${getTypeLabel(resource.type)}</span>
          ${distanceHtml}
          <div class="resource-name">${resource.name}</div>
          <div class="resource-address">${resource.address}</div>
          ${resource.city ? `<div class="resource-address">${resource.city}, ${resource.state} ${resource.zip}</div>` : ''}
          <div class="resource-phone">
            <a href="tel:${formatPhoneLink(resource.phone)}">${resource.phone}</a>
          </div>
          <div class="resource-actions">
            <a href="tel:${formatPhoneLink(resource.phone)}" class="call-btn">Call Now</a>
            ${directionsUrl ? `<a href="${directionsUrl}" target="_blank" rel="noopener" class="directions">Get Directions</a>` : ''}
            ${resource.website ? `<a href="${resource.website}" target="_blank" rel="noopener" class="directions">Website</a>` : ''}
          </div>
        </div>
      `;
    }

    // Show fallback resources when location fails
    function showFallbackResources(resultsDiv, errorMessage) {
      const nationalResources = resourceDatabase.filter(r => r.national);
      
      resultsDiv.innerHTML = `
        <div class="error-msg" style="margin-bottom:16px">
          <strong>Unable to get your location:</strong> ${errorMessage}
        </div>
        <div class="fallback-resources">
          <h4>National Resources Available 24/7</h4>
          <div class="resources-grid">
            ${nationalResources.map((r, i) => renderResourceCard(r, i)).join('')}
          </div>
          <div style="margin-top:16px">
            <h4>Find Local Resources Online</h4>
            <ul>
              <li><a href="https://www.thehotline.org/get-help/" target="_blank" rel="noopener">Find local resources via TheHotline.org</a></li>
              <li><a href="https://www.domesticshelters.org/help" target="_blank" rel="noopener">Search shelters at DomesticShelters.org</a></li>
              <li><a href="https://www.womenslaw.org/find-help/advocates-and-shelters" target="_blank" rel="noopener">Legal help at WomensLaw.org</a></li>
              <li><a href="https://www.rainn.org/get-help" target="_blank" rel="noopener">Sexual assault resources at RAINN.org</a></li>
            </ul>
          </div>
        </div>
      `;
    }

    // Display resources on the page
    function displayResources(resources, resultsDiv, userLocation) {
      if (resources.length === 0) {
        showFallbackResources(resultsDiv, 'No resources found in your area.');
        return;
      }

      const localResources = resources.filter(r => !r.national);
      const nationalResources = resources.filter(r => r.national);

      let html = '';
      
      if (localResources.length > 0) {
        html += `
          <h4 style="margin:0 0 12px;font-size:.95rem">Resources Near You</h4>
          <div class="resources-grid">
            ${localResources.map((r, i) => renderResourceCard(r, i)).join('')}
          </div>
        `;
      }

      if (nationalResources.length > 0) {
        html += `
          <h4 style="margin:${localResources.length > 0 ? '20px' : '0'} 0 12px;font-size:.95rem">National Hotlines (24/7)</h4>
          <div class="resources-grid">
            ${nationalResources.map((r, i) => renderResourceCard(r, i + localResources.length)).join('')}
          </div>
        `;
      }

      html += `
        <div class="fallback-resources" style="margin-top:20px">
          <h4>Additional Online Resources</h4>
          <ul>
            <li><a href="https://www.thehotline.org/get-help/" target="_blank" rel="noopener">More resources via TheHotline.org</a></li>
            <li><a href="https://www.domesticshelters.org/help" target="_blank" rel="noopener">Search more shelters at DomesticShelters.org</a></li>
            <li><a href="https://www.womenslaw.org/find-help/advocates-and-shelters" target="_blank" rel="noopener">Legal help at WomensLaw.org</a></li>
          </ul>
        </div>
      `;

      resultsDiv.innerHTML = html;
    }

    // Initialize the location-based resource finder
    function initResourceFinder() {
      const shareLocationBtn = document.getElementById('shareLocationBtn');
      const locationBtnText = document.getElementById('locationBtnText');
      const resultsDiv = document.getElementById('resourcesResults');

      shareLocationBtn.addEventListener('click', () => {
        // Check if geolocation is supported
        if (!navigator.geolocation) {
          showFallbackResources(resultsDiv, 'Geolocation is not supported by your browser.');
          return;
        }

        // Show loading state
        shareLocationBtn.disabled = true;
        locationBtnText.innerHTML = '<span class="loading-spinner"></span> Locating...';

        // Request location
        navigator.geolocation.getCurrentPosition(
          (position) => {
            const lat = position.coords.latitude;
            const lng = position.coords.longitude;
            
            // Update button to show success
            locationBtnText.textContent = 'Location Found';
            
            // Find and display nearby resources
            const nearbyResources = findNearbyResources(lat, lng);
            displayResources(nearbyResources, resultsDiv, {lat, lng});
            
            // Scroll to results
            resultsDiv.scrollIntoView({behavior: 'smooth', block: 'start'});
            
            // Reset button after a moment
            setTimeout(() => {
              shareLocationBtn.disabled = false;
              locationBtnText.innerHTML = `
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" style="width:18px;height:18px">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
                </svg>
                Update Location
              `;
            }, 2000);
          },
          (error) => {
            // Handle different error types
            let errorMessage = 'Unable to retrieve your location.';
            switch(error.code) {
              case error.PERMISSION_DENIED:
                errorMessage = 'Location permission was denied. Please enable location access in your browser settings.';
                break;
              case error.POSITION_UNAVAILABLE:
                errorMessage = 'Location information is unavailable.';
                break;
              case error.TIMEOUT:
                errorMessage = 'Location request timed out. Please try again.';
                break;
            }
            
            showFallbackResources(resultsDiv, errorMessage);
            
            // Reset button
            shareLocationBtn.disabled = false;
            locationBtnText.innerHTML = `
              <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" style="width:18px;height:18px">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
              </svg>
              Try Again
            `;
          },
          {
            enableHighAccuracy: true,
            timeout: 10000,
            maximumAge: 300000 // Cache for 5 minutes
          }
        );
      });
    }

    // Initialize the resource finder when DOM is ready
    document.addEventListener('DOMContentLoaded', initResourceFinder);
  </script>
</body>
</html>
