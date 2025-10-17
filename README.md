# Danger-Assessment
<!-- This initial project is going to be me using chat gpt to turn the Danger Assessment into a website. -->
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Danger Assessment — Educational Prototype</title>
  <style>
    /* Simple accessible styling */
    :root{--bg:#f7f9fc;--card:#fff;--muted:#666;}
    body{font-family:Inter,system-ui,Segoe UI,Roboto,Arial,sans-serif;background:var(--bg);margin:0;padding:24px;color:#111}
    .container{max-width:860px;margin:0 auto}
    .card{background:var(--card);border-radius:12px;box-shadow:0 6px 24px rgba(12,24,40,.06);padding:20px;margin-bottom:16px}
    h1{margin:0 0 6px;font-size:1.6rem}
    p.lead{margin:0 0 12px;color:var(--muted)}
    .consent{display:flex;gap:12px;align-items:center;margin-bottom:16px}
    label.switch{display:flex;gap:8px;align-items:center}
    .questions{display:grid;grid-template-columns:1fr;gap:10px;margin-top:10px}
    .q{display:flex;gap:12px;align-items:flex-start;padding:10px;border-radius:8px}
    .q .text{flex:1}
    .controls{display:flex;gap:8px;flex-wrap:wrap;margin-top:12px}
    button{background:#0b5fff;color:#fff;border:0;padding:10px 14px;border-radius:8px;cursor:pointer}
    button.secondary{background:#e6eefc;color:#0b3d91}
    .result{margin-top:14px;padding:12px;border-radius:8px}
    .low{background:#f0f9ff;color:#0b3d91}
    .elev{background:#fff7ed;color:#7a4b00}
    .high{background:#fff0f6;color:#6b1846}
    .ext{background:#fff2f2;color:#7a0b0b}
    .muted{color:var(--muted);font-size:.95rem}
    .hotline{font-weight:600}
    footer{margin-top:18px;font-size:.9rem;color:var(--muted)}
    .tiny{font-size:.86rem;color:var(--muted)}
    @media(min-width:760px){ .questions{grid-template-columns:1fr 1fr} }
  </style>
</head>
<body>
  <div class="container">
    <div class="card" role="main" aria-labelledby="title">
      <h1 id="title">Danger Assessment — Educational Prototype</h1>
      <p class="lead">This is a **client-side educational demo** implementing the 20-item Danger Assessment (DA) with the official weighted scoring system. It does <strong>not</strong> send your answers anywhere. If you are in immediate danger call emergency services now.</p>

      <div class="consent" aria-hidden="false">
        <label class="switch">
          <input type="checkbox" id="consent" />
          <span class="tiny">I understand this is not a substitute for professional advice and I want to continue.</span>
        </label>
      </div>

      <div id="formArea" style="display:none">
        <p class="muted">Answer the following items with Yes/No. Some items carry extra points in official scoring (this tool applies those weights automatically). If a question feels distressing, consider pausing and seeking support.</p>

        <form id="daForm" autocomplete="off" novalidate>
          <div class="questions" id="questions">
            <!-- We'll inject the 20 questions via JS -->
          </div>

          <div style="margin-top:10px;">
            <button type="button" id="calcBtn">Calculate Score</button>
            <button type="button" id="resetBtn" class="secondary">Reset</button>
            <button type="button" id="printBtn" class="secondary">Print / Save Result</button>
          </div>

          <div id="resultBox" class="result" style="display:none;margin-top:12px;"></div>

          <div style="margin-top:12px" class="tiny">
            <p><strong>Scoring rules (applied here):</strong> Count of "Yes" answers (Q1–Q20), plus: <em>+4</em> for Q2 and Q3; <em>+3</em> for Q4; <em>+2</em> for Q5, Q6, Q7; <em>+1</em> for Q8 and Q9; subtract <em>3</em> if Q3a (threat to kill) is checked. Categories: &lt;8 variable, 8–13 elevated, 14–17 high, 18+ extreme.</p>
            <p class="muted">This implementation is educational — official scoring and use require training. If you plan to use this in practice, consult certified DA training and local advocates.</p>
          </div>

        </form>
      </div>

      <div id="noConsent" class="tiny" style="display:block">
        <p><strong>Note:</strong> You must check the box above to continue. If you are in immediate danger, call <span class="hotline">Emergency services</span> now.</p>
      </div>

    </div>

    <div class="card">
      <h2 style="margin-top:0">Resources & Safety</h2>
      <p class="muted">If you or someone you know is in danger, please contact local emergency services. The U.S. National Domestic Violence Hotline is <strong>1-800-799-7233</strong> (TTY 1-800-787-3224) and is available 24/7. Visit <a href="https://www.thehotline.org" target="_blank" rel="noopener">thehotline.org</a>.</p>
      <p class="muted">For non-U.S. resources, contact your local health or social services or search for domestic violence helplines in your area.</p>
    </div>

    <footer class="tiny">
      <p>Prototype &copy; Educational demo. No data stored or transmitted. For official guidance and training on the Danger Assessment, consult the DA manual and accredited training materials.</p>
    </footer>
  </div>

  <script>
    // Questions array (20 items). Wording simplified for display; keep meaning identical to DA.
    // IMPORTANT: If you plan to publish this site publicly, review phrasing with a trained advocate.
    const questions = [
      "1. Has the abuse increased in severity or frequency over the past year?",
      "2. Has he ever used a weapon (gun, knife) against you or you were threatened with a weapon?",
      "3. Has he ever tried to choke you or strangle you?",
      "3a. (If yes to Q3) Has he ever threatened to kill you? (Check if applicable)",
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

    // Render questions
    const qContainer = document.getElementById('questions');
    questions.forEach((qText, idx) => {
      // For Q3a we want a checkbox that affects scoring separately, but allow it to be shown grouped.
      const id = 'q' + (idx+1);
      const wrapper = document.createElement('div');
      wrapper.className = 'q';
      const label = document.createElement('label');
      label.setAttribute('for', id);
      label.className = 'text';
      label.innerHTML = `<div style="font-weight:600">${qText}</div>`;
      const input = document.createElement('input');
      input.type = (idx === 3 ? 'checkbox' : 'radio'); // placeholder, we'll build better UI below
      // Instead of radio for yes/no we create two radios per question
      label.innerHTML = `<div class="text"><div style="font-weight:600">${qText}</div></div>`;
      wrapper.appendChild(label);

      // create yes/no radios except for Q3a (index 3) which is a single checkbox
      if (idx === 3) {
        const chk = document.createElement('input');
        chk.type = 'checkbox';
        chk.id = 'q3a';
        chk.name = 'q3a';
        chk.setAttribute('aria-label', qText);
        const span = document.createElement('span');
        span.style.marginLeft = '8px';
        span.appendChild(chk);
        span.append(' Yes (check if applicable)');
        wrapper.appendChild(span);
      } else {
        const yesId = id + '_yes';
        const noId = id + '_no';
        const yes = document.createElement('input');
        yes.type = 'radio';
        yes.name = id;
        yes.id = yesId;
        yes.value = 'yes';
        const yesLabel = document.createElement('label');
        yesLabel.setAttribute('for', yesId);
        yesLabel.style.marginRight = '10px';
        yesLabel.appendChild(yes);
        yesLabel.append(' Yes');

        const no = document.createElement('input');
        no.type = 'radio';
        no.name = id;
        no.id = noId;
        no.value = 'no';
        const noLabel = document.createElement('label');
        noLabel.setAttribute('for', noId);
        noLabel.style.marginLeft = '8px';
        noLabel.appendChild(no);
        noLabel.append(' No');

        wrapper.appendChild(yesLabel);
        wrapper.appendChild(noLabel);
      }

      qContainer.appendChild(wrapper);
    });

    // Show form only after consent is checked
    document.getElementById('consent').addEventListener('change', function(e){
      const ok = e.target.checked;
      document.getElementById('formArea').style.display = ok ? 'block' : 'none';
      document.getElementById('noConsent').style.display = ok ? 'none' : 'block';
      window.scrollTo({top:0,behavior:'smooth'});
    });

    // Reset handler
    document.getElementById('resetBtn').addEventListener('click', function(){
      document.querySelectorAll('input[type=radio]').forEach(r=>r.checked=false);
      document.querySelectorAll('input[type=checkbox]').forEach(c=>c.checked=false);
      document.getElementById('resultBox').style.display = 'none';
      document.getElementById('resultBox').innerHTML = '';
    });

    // Print/save
    document.getElementById('printBtn').addEventListener('click', function(){
      window.print();
    });

    // Calculation logic using official weights
    function calculateScore(){
      // Map indexes to question numbers (1-based)
      // Questions Q1..Q20 are in the questions array index 0..19
      // Q3a is index 3 (the 4th item in our array text list); we treat it separately
      // But importantly official DA scoring: add total number of yes responses Q1..Q20
      // then add weighted points:
      // +4 for Q2 and Q3
      // +3 for Q4
      // +2 for Q5, Q6, Q7
      // +1 for Q8 and Q9
      // subtract 3 if Q3a is checked
      let baseYesCount = 0;
      for(let i=0;i<20;i++){
        // skip index 3 (that's Q3a placeholder text) - our true Q3 is index 2
        if(i===3) continue;
        const qName = 'q' + (i+1);
        const radios = document.getElementsByName(qName);
        if(radios.length){
          for(const r of radios){
            if(r.checked && r.value === 'yes') baseYesCount++;
          }
        }
      }
      // Find yes for each specific question by index mapping:
      function isYes(questionNumber){
        // questionNumber is 1..20 referring to the DA questions
        // Our HTML mapping: qN maps to element name 'qN' except 3a handled separately
        if(questionNumber === 3) { // DA Q3 corresponds to our array index 2
          const radios = document.getElementsByName('q3');
          return [...radios].some(r => r.checked && r.value === 'yes');
        }
        if(questionNumber === 3.1) { // custom for Q3a
          const q3a = document.getElementById('q3a');
          return q3a && q3a.checked;
        }
        const name = 'q' + questionNumber;
        const radios = document.getElementsByName(name);
        return [...radios].some(r => r.checked && r.value === 'yes');
      }

      // Weighted scoring
      let score = baseYesCount;
      // +4 for Q2 and Q3
      if(isYes(2)) score += 4;
      if(isYes(3)) score += 4;
      // +3 for Q4
      if(isYes(4)) score += 3;
      // +2 for Q5, Q6, Q7
      if(isYes(5)) score += 2;
      if(isYes(6)) score += 2;
      if(isYes(7)) score += 2;
      // +1 for Q8 and Q9
      if(isYes(8)) score += 1;
      if(isYes(9)) score += 1;
      // subtract 3 if Q3a is checked
      if(isYes(3.1)) score -= 3;

      return {score, baseCount: baseYesCount};
    }

    // Calculate button
    document.getElementById('calcBtn').addEventListener('click', function(e){
      const res = calculateScore();
      const score = res.score;
      const box = document.getElementById('resultBox');
      box.style.display = 'block';
      box.innerHTML = '';
      let category = '';
      let className = '';
      let expl = '';

      if(score < 8){
        category = 'Variable danger';
        className = 'low';
        expl = 'Your score falls in the Variable danger range. Continue to seek safety information and support. Consider consulting an advocate to discuss next steps.';
      } else if(score >=8 && score <=13){
        category = 'Elevated danger';
        className = 'elev';
        expl = 'Your score indicates Elevated danger. Consider contacting local domestic violence resources and making a safety plan with a trained advocate.';
      } else if(score >=14 && score <=17){
        category = 'High danger';
        className = 'high';
        expl = 'Your score indicates High danger. Seek immediate support from trained professionals and consider contacting emergency services if you feel at risk.';
      } else {
        category = 'Extreme danger';
        className = 'ext';
        expl = 'Your score indicates Extreme danger. This suggests a very high risk. Contact emergency services if you are in immediate danger, and reach a domestic violence advocate now.';
      }

      box.className = 'result ' + className;
      box.innerHTML = `
        <div style="font-weight:700;font-size:1.05rem">Score: ${score} — ${category}</div>
        <div style="margin-top:8px">${expl}</div>
        <div style="margin-top:8px" class="tiny">Base yes count (Q1–Q20): ${res.baseCount}. Weighted scoring applied per official DA guidance.</div>
        <div style="margin-top:8px;font-weight:700">Resources: If in immediate danger call emergency services. US National Domestic Violence Hotline: 1-800-799-7233 (thehotline.org).</div>
      `;
      box.scrollIntoView({behavior:'smooth'});
    });
  </script>
</body>
</html>
