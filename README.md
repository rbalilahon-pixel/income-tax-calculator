<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Philippine Income Tax Calculator</title>
  <style>
    :root{
      --bg:#0f172a; --card:#0b1020; --accent:#7c3aed; --glass: rgba(255,255,255,0.04);
      --glass-2: rgba(255,255,255,0.03);
      --muted: #9aa4c0;
    }
    *{box-sizing:border-box;font-family:Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial}
    body{
      margin:0; min-height:100vh; background: radial-gradient(1000px 600px at 10% 10%, rgba(124,58,237,0.12), transparent), linear-gradient(180deg,#071033 0%, #051026 100%); color:#e6eef8; display:flex; align-items:center; justify-content:center; padding:32px;
    }
    .container{width:100%; max-width:980px; display:grid; grid-template-columns:1fr 380px; gap:28px;}
    .card{background:linear-gradient(180deg,var(--glass), var(--glass-2)); border-radius:14px; padding:22px; box-shadow:0 8px 30px rgba(2,6,23,0.6); border:1px solid rgba(255,255,255,0.03)}
    header{display:flex; justify-content:space-between; align-items:center; gap:12px}
    h1{margin:0;font-size:20px; letter-spacing:-0.3px}
    p.lead{margin:6px 0 0; color:var(--muted); font-size:13px}

    .form-row{display:flex; gap:12px; margin-top:18px}
    label{font-size:13px; color:var(--muted); display:block}
    input[type=number]{width:100%; padding:12px 14px; border-radius:10px; background:#061029; border:1px solid rgba(255,255,255,0.03); color:inherit; font-size:15px}
    .btn{display:inline-flex; align-items:center; gap:8px; padding:10px 14px; border-radius:10px; background:linear-gradient(90deg,var(--accent), #4f46e5); border:none; color:white; cursor:pointer; font-weight:600}
    .btn.secondary{background:transparent; border:1px solid rgba(255,255,255,0.04); color:var(--muted); font-weight:600}
    .summary{margin-top:18px; display:grid; gap:8px}
    .stat{display:flex; justify-content:space-between; padding:10px; background:rgba(255,255,255,0.02); border-radius:10px}
    .stat small{color:var(--muted)}
    .examples{font-size:13px; color:var(--muted); margin-top:12px}

    /* right column */
    .panel{position:relative}
    .panel .result-amount{font-size:34px; margin:0; letter-spacing:-0.6px}
    .panel .breakdown{margin-top:12px; display:grid; gap:8px}
    .table{width:100%; border-collapse:collapse; margin-top:12px}
    .table th, .table td{padding:8px 10px; text-align:left; font-size:13px}
    .table thead th{color:var(--muted); font-weight:600}
    .note{color:var(--muted); font-size:13px; margin-top:10px}

    footer{margin-top:14px; color:var(--muted); font-size:13px}

    /* responsive */
    @media (max-width:900px){.container{grid-template-columns:1fr;}}

    .chip{display:inline-block; padding:6px 8px; border-radius:999px; background:rgba(255,255,255,0.02); font-size:13px}

  </style>
</head>
<body>
  <div class="container">
    <div class="card">
      <header>
        <div>
          <h1>Philippine Income Tax Calculator</h1>
          <p class="lead">Enter net taxable income (₱) below. Calculation follows the table used for the income tax brackets.</p>
        </div>
        <div class="chip">Income Tax = Basic tax + Rate</div>
      </header>

      <div class="form-row" style="margin-top:18px;">
        <div style="flex:1">
          <label for="income">Net Taxable Income (PHP)</label>
          <!-- required: use HTML input tag instead of prompt -->
          <input id="income" type="number" inputmode="decimal" min="0" step="0.01" value="180000" />
        </div>
        <div style="width:140px; display:flex; flex-direction:column; gap:8px">
          <button id="compute" class="btn" aria-label="Compute tax">Compute</button>
          <button id="reset" class="btn secondary">Reset</button>
        </div>
      </div>

      <div class="summary">
        <div class="stat"><div><small>Basic tax</small><div id="basicLabel">—</div></div><div id="basic" style="font-weight:700">—</div></div>
        <div class="stat"><div><small>Rate portion</small><div id="rateLabel">—</div></div><div id="ratePart" style="font-weight:700">—</div></div>
        <div class="stat"><div><small>Total income tax</small><div class="note">(rounded to the nearest peso)</div></div><div id="total" style="font-weight:800; font-size:18px">—</div></div>
      </div>

      <div class="examples">
        Quick examples: <button class="btn secondary" data-sample="180000">₱180,000</button> <button class="btn secondary" data-sample="320000">₱320,000</button> <button class="btn secondary" data-sample="520000">₱520,000</button> <button class="btn secondary" data-sample="930000">₱930,000</button> <button class="btn secondary" data-sample="2400000">₱2,400,000</button> <button class="btn secondary" data-sample="10000000">₱10,000,000</button>
      </div>

      <footer>Made with ❤️ — enter a value and press Compute or use the quick example buttons.</footer>
    </div>

    <aside class="card panel">
      <div>
        <h2 style="margin:0">Result</h2>
        <p class="note">Shows the computed basic tax, the rate portion and the total tax.</p>

        <div style="margin-top:14px;">
          <div class="result">
            <div style="display:flex; justify-content:space-between; align-items:center">
              <div>
                <small class="note">Net Taxable Income</small>
                <div id="displayIncome" style="font-weight:700; font-size:18px">—</div>
              </div>
              <div style="text-align:right">
                <small class="note">Income Tax</small>
                <div id="displayTax" class="result-amount">—</div>
              </div>
            </div>

            <div class="breakdown" id="breakdown"></div>

            <table class="table" aria-hidden="false">
              <thead>
                <tr><th>Bracket</th><th>Formula</th></tr>
              </thead>
              <tbody>
                <tr><td>Up to ₱250,000</td><td>0%</td></tr>
                <tr><td>₱250,000 — ₱400,000</td><td>20% of the excess over ₱250,000</td></tr>
                <tr><td>₱400,000 — ₱800,000</td><td>₱30,000 + 25% of the excess over ₱400,000</td></tr>
                <tr><td>₱800,000 — ₱2,000,000</td><td>₱130,000 + 30% of the excess over ₱800,000</td></tr>
                <tr><td>₱2,000,000 — ₱8,000,000</td><td>₱490,000 + 32% of the excess over ₱2,000,000</td></tr>
                <tr><td>Over ₱8,000,000</td><td>₱2,410,000 + 35% of the excess over ₱8,000,000</td></tr>
              </tbody>
            </table>

          </div>
        </div>
      </div>
    </aside>
  </div>

  <script>
    // Helper to format currency (PHP) and round to nearest peso
    const fmt = v => new Intl.NumberFormat('en-PH', {style:'currency', currency:'PHP', maximumFractionDigits:0}).format(Math.round(v));

    const incomeEl = document.getElementById('income');
    const computeBtn = document.getElementById('compute');
    const resetBtn = document.getElementById('reset');
    const basicEl = document.getElementById('basic');
    const ratePartEl = document.getElementById('ratePart');
    const totalEl = document.getElementById('total');
    const displayIncome = document.getElementById('displayIncome');
    const displayTax = document.getElementById('displayTax');

    function calculateTax(income){
      // parse as number, ensure >=0
      income = Number(income) || 0;
      let basic = 0, ratePart = 0, total = 0;

      if(income <= 250000){
        basic = 0; ratePart = 0; total = 0;
      } else if(income <= 400000){
        basic = 0; ratePart = 0.20 * (income - 250000); total = basic + ratePart;
      } else if(income <= 800000){
        basic = 30000; ratePart = 0.25 * (income - 400000); total = basic + ratePart;
      } else if(income <= 2000000){
        basic = 130000; ratePart = 0.30 * (income - 800000); total = basic + ratePart;
      } else if(income <= 8000000){
        basic = 490000; ratePart = 0.32 * (income - 2000000); total = basic + ratePart;
      } else {
        basic = 2410000; ratePart = 0.35 * (income - 8000000); total = basic + ratePart;
      }

      // Round to nearest peso per examples
      return {income, basic: Math.round(basic), ratePart: Math.round(ratePart), total: Math.round(total)};
    }

    function render(v){
      const {income, basic, ratePart, total} = calculateTax(v);
      basicEl.textContent = fmt(basic);
      ratePartEl.textContent = fmt(ratePart);
      totalEl.textContent = fmt(total);
      displayIncome.textContent = fmt(income);
      displayTax.textContent = fmt(total);

      const breakdown = document.getElementById('breakdown');
      breakdown.innerHTML = '';
      const rows = [
        ['Taxable Income', fmt(income)],
        ['Basic tax', fmt(basic)],
        ['Rate portion', fmt(ratePart)],
        ['Total (rounded)', fmt(total)]
      ];
      rows.forEach(r=>{
        const div = document.createElement('div');
        div.style.display='flex'; div.style.justifyContent='space-between'; div.style.padding='8px'; div.style.background='rgba(255,255,255,0.01)'; div.style.borderRadius='8px';
        div.innerHTML = `<div style="color:var(--muted); font-size:13px">${r[0]}</div><div style="font-weight:700">${r[1]}</div>`;
        breakdown.appendChild(div);
      });
    }

    computeBtn.addEventListener('click', ()=> render(incomeEl.value));
    incomeEl.addEventListener('keydown', (e)=>{ if(e.key === 'Enter') render(incomeEl.value); });
    resetBtn.addEventListener('click', ()=>{ incomeEl.value = 0; render(0); });

    // quick example buttons
    document.querySelectorAll('[data-sample]').forEach(b=>{
      b.addEventListener('click', ()=>{
        incomeEl.value = b.getAttribute('data-sample');
        render(incomeEl.value);
      });
    });

    // initialize with default example
    render(incomeEl.value);
  </script>
</body>
</html>
