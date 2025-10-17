# Danger-Assessment
<!-- This initial project is going to be me using chat gpt to turn the Danger Assessment into a website. -->
<!--doctype html-->
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Danger Assessment — One Question at a Time</title>
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
    .choices{display:flex;gap:12px;align-items:center}
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
      <h1 id="title">Danger Assessment — One Question at a Time</h1>
      <p class="lead">Client-side educational prototype implementing the DA scoring. This tool is not a substitute for professional advice. If you are in immediate danger, call emergency services now.</p>

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
            <label class="radioLabel"><input type="radio" name="answer" value="yes"> Yes</label>
            <label class="radioLabel"><input type="radio" name="answer" value="no"> No</label>
          </div>

          <div class="qMeta" id="qMeta" style="display:none"></div>

          <div class="btnRow">
            <button id="nextBtn" disabled>Next</button>
            <button id="resetBtn" class="secondary" type="button">Reset</button>
          </div>

          <div id="resultBox" style="display:none"></div>
        </div>

        <div class="tiny" style="margin-top:12px">
          <strong>Scoring rules (applied):</strong> Count "Yes" responses (Q1–Q20), then add weights: +4 for Q2 & Q3; +3 for Q4; +2 for Q5,Q6,Q7; +1 for Q8,Q9; subtract 3 if Q3 "threatened to kill" checkbox is checked. Categories: &lt;8 variable, 8–13 elevated, 14–17 high, 18+ extreme.
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
      <p>Prototype &copy; Educational demo. No data stored or transmitted. For official guidance and training on the Danger Assessment, consult the DA manual and accredited training materials.</p>
    </footer>
  </div>

  <script>
    // Official 20 DA items (wording kept consistent with your permission)
    const questions = [
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

    // We'll keep a parallel array for answers (null / 'yes' / 'no')
    const answers = Array(questions.length).fill(null);
    // Q3 "threat to kill" checkbox state (separate adjustment)
    let q3Threat = false;

    let idx = 0; // current question index 0..19

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
      if(ok) renderQuestion();
    });

    function updateProgress(){
      const pct = Math.round((idx / questions.length) * 100);
      progressFill.style.width = pct + '%';
    }

    function renderQuestion(){
      // Reset UI
      resultBox.style.display = 'none';
      qMeta.style.display = 'none';
      specialArea.style.display = 'none';
      choices.innerHTML = '';
      nextBtn.disabled = true;

      // Show question
      qText.textContent = questions[idx];

      // Create Yes/No radios
      const yesId = 'ans_yes';
      const noId = 'ans_no';

      const yesLabel = document.createElement('label');
      yesLabel.className = 'radioLabel';
      const yesInput = document.createElement('input');
      yesInput.type = 'radio';
      yesInput.name = 'answer';
      yesInput.value = 'yes';
      yesInput.id = yesId;
      yesLabel.appendChild(yesInput);
      yesLabel.append(' Yes');

      const noLabel = document.createElement('label');
      noLabel.className = 'radioLabel';
      const noInput = document.createElement('input');
      noInput.type = 'radio';
      noInput.name = 'answer';
      noInput.value = 'no';
      noInput.id = noId;
      noLabel.appendChild(noInput);
      noLabel.append(' No');

      choices.appendChild(yesLabel);
      choices.appendChild(noLabel);

      // If user previously answered, restore selection
      if(answers[idx] === 'yes') yesInput.checked = true;
      if(answers[idx] === 'no') noInput.checked = true;

      // Special handling for Q3 (index 2): show an extra checkbox for "threat to kill"
      if(idx === 2){
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

      // Add event listeners to radios to enable Next
      const radios = choices.querySelectorAll('input[name="answer"]');
      radios.forEach(r=>{
        r.addEventListener('change', () => {
          // store answer
          answers[idx] = r.value;
          nextBtn.disabled = false;
        });
      });

      // Update progress
      updateProgress();

      // Change button text to Submit if last question
      nextBtn.textContent = (idx === questions.length - 1) ? 'Submit' : 'Next';
    }

    nextBtn.addEventListener('click', ()=>{
      // Require an answer (except we already enforce enabled only when answered)
      if(!answers[idx]){
        nextBtn.disabled = true;
        return;
      }
      // If last -> compute result
      if(idx === questions.length -1){
        showResults();
        return;
      }
      // Advance
      idx++;
      renderQuestion();
    });

    resetBtn.addEventListener('click', ()=>{
      if(!confirm('Reset all answers?')) return;
      for(let i=0;i<answers.length;i++) answers[i]=null;
      q3Threat = false;
      idx = 0;
      renderQuestion();
      progressFill.style.width = '0%';
    });

    function showResults(){
      // Compute base yes count across Q1..Q20
      let baseYes = 0;
      for(let i=0;i<questions.length;i++){
        if(answers[i] === 'yes') baseYes++;
      }

      // Weighted scoring per rules
      // Q numbering: 1-based
      function answeredYes(qnum){
        // qnum 1..20 maps to index qnum-1
        return answers[qnum-1] === 'yes';
      }
      let score = baseYes;
      if(answeredYes(2)) score += 4;
      if(answeredYes(3)) score += 4;
      if(answeredYes(4)) score += 3;
      if(answeredYes(5)) score += 2;
      if(answeredYes(6)) score += 2;
      if(answeredYes(7)) score += 2;
      if(answeredYes(8)) score += 1;
      if(answeredYes(9)) score += 1;
      if(q3Threat) score -= 3;

      // Determine category
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

      // Show result
      resultBox.style.display = 'block';
      resultBox.className = 'result ' + cls;
      resultBox.innerHTML = `
        <div style="font-weight:700;font-size:1.05rem">Score: ${score} — ${category}</div>
        <div style="margin-top:8px">${expl}</div>
        <div style="margin-top:8px" class="tiny">Base yes count (Q1–Q20): ${baseYes}. Weighted scoring applied per official DA guidance.</div>
        <div style="margin-top:12px;font-weight:700">Resources: If in immediate danger call emergency services. US National Domestic Violence Hotline: 1-800-799-7233 (thehotline.org).</div>
        <div style="margin-top:12px"><button id="printBtn" class="secondary">Print / Save Result</button></div>
      `;
      // set progress to full
      progressFill.style.width = '100%';
      // wire print button
      document.getElementById('printBtn').addEventListener('click', ()=>window.print());
      // scroll to result
      resultBox.scrollIntoView({behavior:'smooth'});
      // allow user to still reset if they want
    }

    // Init: when page loaded, disable Next until consent
    document.addEventListener('DOMContentLoaded', ()=>{
      // nothing until consent checked
    });
  </script>
</body>
</html>
