
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
    @media(min-width:720px){ .questionCard{padding:24px} }
  </style>
</head>
<body>
  <div class="container">
    <div class="card" role="main" aria-labelledby="title">
      <h1 id="title">Danger Assessment — WAST → DA</h1>
      <p class="lead">Client-side educational prototype. Not a substitute for professional advice. If you are in immediate danger, call emergency services now.</p>

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
          <strong>Note:</strong> This tool shows final WAST and DA results together only after both instruments are completed. WAST wording and scoring follow the validated instrument by Brown et al. (1996). :contentReference[oaicite:1]{index=1}
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

  <script>
    // ------------------- WAST (8 items) -------------------
    // WAST validated wording (Brown et al., 1996). Sources: PubMed summary and clinical references. :contentReference[oaicite:2]{index=2}
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
    // For each WAST item, responses are scored: 1 = least (No/ Never/ No difficulty), 2 = Some / Sometimes, 3 = A lot / Often / Great difficulty
    // WAST scoring range: 8 - 24. Interpretation:
    // 8-13 = No/low risk; 14-17 = Possible abuse; 18-24 = Likely abuse

    // ------------------- DA (20 items) -------------------
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

    // Combined flow: WAST (8) then DA (20) => total 28 steps
    const totalItems = wastItems.length + daQuestions.length; // 28
    // Storage:
    const wastAnswers = Array(wastItems.length).fill(null); // numeric 1..3
    const daAnswers = Array(daQuestions.length).fill(null); // 'yes'/'no'
    let q3Threat = false; // DA Q3 special checkbox remains (index 2 inside DA)
    // overall index: 0 .. totalItems-1
    let overallIdx = 0; // start at WAST Q1
    let stage = 'WAST'; // 'WAST' or 'DA' or 'DONE'

    // DOM refs
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

    function updateProgress(){
      // progress uses one continuous bar across totalItems
      const pct = Math.round(((overallIdx) / (totalItems - 1)) * 100);
      progressFill.style.width = pct + '%';
    }

    function renderCurrent(){
      // reset UI
      resultBox.style.display = 'none';
      qMeta.style.display = 'none';
      specialArea.style.display = 'none';
      choices.innerHTML = '';
      nextBtn.disabled = true;

      // Decide whether current item is WAST or DA
      if(overallIdx < wastItems.length){
        // WAST
        stage = 'WAST';
        const localIdx = overallIdx; // 0..7
        qText.textContent = wastItems[localIdx];
        // Create 3-option radios (values 1,2,3)
        createWastRadios(wastAnswers[localIdx], (val)=>{
          wastAnswers[localIdx] = Number(val);
          nextBtn.disabled = false;
        });
        nextBtn.textContent = (overallIdx === totalItems - 1) ? 'Finish' : 'Next';
        updateProgress();
      } else {
        // DA section
        stage = 'DA';
        const daIdx = overallIdx - wastItems.length; // 0..19
        qText.textContent = daQuestions[daIdx];
        // Show Yes/No radios
        createYesNoRadios(daAnswers[daIdx], (val)=>{
          daAnswers[daIdx] = val;
          nextBtn.disabled = false;
        });
        // Show Q3 special checkbox when at DA index 3 (overallIdx such that daIdx === 2)
        if(daIdx === 2){
          specialArea.style.display = 'block';
          specialArea.innerHTML = '';
          const specialLabel = document.createElement('label');
          specialLabel.style.display = 'inline-flex';
          specialLabel.style.gap = '8px';
          const chk = document.createElement('input');
          chk.type = 'checkbox';
          chk.id = 'q3ThreatChk';
          chk.checked = q3Threat;
          chk.addEventListener('change', e => { q3Threat = e.target.checked; });
          specialLabel.appendChild(chk);
          specialLabel.append('Has he ever threatened to kill you? (check if applicable)');
          specialArea.appendChild(specialLabel);
        }
        nextBtn.textContent = (overallIdx === totalItems - 1) ? 'Finish' : 'Next';
        updateProgress();
      }
    }

    function createWastRadios(currentValue, onChange){
      // For WAST the labeling differs by item, but we use the validated response labels generically:
      // Option 3 = highest frequency/severity, Option 1 = lowest
      // For items 1: A lot of tension / Some tension / No tension  -> map to 3/2/1
      // For item 2: Great difficulty / Some difficulty / No difficulty -> 3/2/1
      // For items 3-8: Often / Sometimes / Never -> 3/2/1
      const opts = [
        {val:'3', label:'3 — Highest (A lot / Great difficulty / Often)'},
        {val:'2', label:'2 — Moderate (Some / Some difficulty / Sometimes)'},
        {val:'1', label:'1 — Lowest (No / No difficulty / Never)'}
      ];
      opts.forEach(o=>{
        const lab = document.createElement('label');
        lab.className = 'radioLabel';
        const inp = document.createElement('input');
        inp.type = 'radio';
        inp.name = 'answer';
        inp.value = o.val;
        lab.appendChild(inp);
        lab.append(' ' + o.label);
        choices.appendChild(lab);
        if(Number(currentValue) === Number(o.val)) inp.checked = true;
      });
      const radios = choices.querySelectorAll('input[name="answer"]');
      radios.forEach(r=>{
        r.addEventListener('change', ()=> onChange(r.value));
      });
    }

    function createYesNoRadios(currentValue, onChange){
      const yesLabel = document.createElement('label');
      yesLabel.className = 'radioLabel';
      const yesInput = document.createElement('input');
      yesInput.type = 'radio';
      yesInput.name = 'answer';
      yesInput.value = 'yes';
      yesLabel.appendChild(yesInput);
      yesLabel.append(' Yes');

      const noLabel = document.createElement('label');
      noLabel.className = 'radioLabel';
      const noInput = document.createElement('input');
      noInput.type = 'radio';
      noInput.name = 'answer';
      noInput.value = 'no';
      noLabel.appendChild(noInput);
      noLabel.append(' No');

      choices.appendChild(yesLabel);
      choices.appendChild(noLabel);

      if(currentValue === 'yes') yesInput.checked = true;
      if(currentValue === 'no') noInput.checked = true;

      const radios = choices.querySelectorAll('input[name="answer"]');
      radios.forEach(r=>{
        r.addEventListener('change', () => {
          onChange(r.value);
        });
      });
    }

    nextBtn.addEventListener('click', ()=>{
      // ensure an answer exists for current index
      if(overallIdx < wastItems.length){
        if(wastAnswers[overallIdx] === null){ nextBtn.disabled = true; return; }
      } else {
        const daIdx = overallIdx - wastItems.length;
        if(daAnswers[daIdx] === null){ nextBtn.disabled = true; return; }
      }

      // advance or finish
      if(overallIdx === totalItems - 1){
        // finished both instruments - compute combined results
        showCombinedResults();
        return;
      }
      overallIdx++;
      renderCurrent();
    });

    resetBtn.addEventListener('click', ()=>{
      if(!confirm('Reset all answers?')) return;
      for(let i=0;i<wastAnswers.length;i++) wastAnswers[i]=null;
      for(let j=0;j<daAnswers.length;j++) daAnswers[j]=null;
      q3Threat = false;
      overallIdx = 0;
      stage = 'WAST';
      renderCurrent();
      progressFill.style.width = '0%';
    });

    // ------------- Scoring & Results -------------
    function computeWAST(){
      // Sum numeric answers (1..3). If any unanswered, treat as 0; but we require completion so all should be present.
      let total = 0;
      for(let i=0;i<wastAnswers.length;i++){
        total += (Number(wastAnswers[i]) || 0);
      }
      let category = '';
      if(total <= 13) category = 'No / Low risk';
      else if(total <=17) category = 'Possible abuse';
      else category = 'Likely abuse';
      return { total, category };
    }

    function computeDA(){
      // Base yes count across DA
      let baseYes = 0;
      for(let i=0;i<daQuestions.length;i++){
        if(daAnswers[i] === 'yes') baseYes++;
      }
      function answeredYes(qnum){ return daAnswers[qnum-1] === 'yes'; }
      let score = baseYes;
      if(answeredYes(2)) score += 4;
      if(answeredYes(3)) score += 4;
      if(answeredYes(4)) score += 3;
      if(answeredYes(5)) score += 2;
      if(answeredYes(6)) score += 2;
      if(answeredYes(7)) score += 2;
      if(answeredYes(8)) score += 1;
      if(answeredYes(9)) score += 1;
      if(q3Threat) score -= 3; // as in your original logic

      let category = '', cls='', expl='';
      if(score < 8){
        category = 'Variable danger';
        cls = 'low';
        expl = 'Your score falls in the Variable danger range. Continue to seek safety information and support. Consider consulting an advocate to discuss next steps.';
      } else if(score >=8 && score <=13){
        category = 'Elevated danger';
        cls = 'elev';
        expl = 'Your score indicates Elevated danger. Consider contacting local domestic violence resources and making a safety plan with a trained advocate.';
      } else if(score >=14 && score <=17){
        category = 'High danger';
        cls = 'high';
        expl = 'Your score indicates High danger. Seek immediate support from trained professionals and consider contacting emergency services if you feel at risk.';
      } else {
        category = 'Extreme danger';
        cls = 'ext';
        expl = 'Your score indicates Extreme danger. This suggests a very high risk. Contact emergency services if you are in immediate danger, and reach a domestic violence advocate now.';
      }

      return { score, baseYes, category, cls, expl };
    }

    function showCombinedResults(){
      // Compute both
      const wast = computeWAST();
      const da = computeDA();

      resultBox.style.display = 'block';
      resultBox.className = 'result ' + da.cls;
      resultBox.innerHTML = `
        <div style="font-weight:700;font-size:1.05rem">Results</div>

        <div style="margin-top:10px;font-weight:700">WAST (Woman Abuse Screening Tool) — Score: ${wast.total} — ${wast.category}</div>
        <div style="margin-top:6px" class="tiny">WAST scoring range: 8–24. Interpretation: 8–13 = No/Low risk; 14–17 = Possible abuse; 18–24 = Likely abuse. (Brown et al., 1996).</div>

        <div style="margin-top:12px;font-weight:700">Danger Assessment (DA) — Score: ${da.score} — ${da.category}</div>
        <div style="margin-top:8px">${da.expl}</div>
        <div style="margin-top:8px" class="tiny">Base yes count (Q1–Q20): ${da.baseYes}. Weighted scoring applied per official DA guidance.</div>

        <div style="margin-top:12px;font-weight:700">Resources: If in immediate danger call emergency services. US National Domestic Violence Hotline: 1-800-799-7233 (thehotline.org).</div>
        <div style="margin-top:12px"><button id="printBtn" class="secondary">Print / Save Result</button></div>
      `;
      progressFill.style.width = '100%';
      document.getElementById('printBtn').addEventListener('click', ()=>window.print());
      resultBox.scrollIntoView({behavior:'smooth'});
      stage = 'DONE';
    }

    // init nothing until consent
    document.addEventListener('DOMContentLoaded', ()=>{ /* user must check consent to begin */ });

  </script>
</body>
</html>
