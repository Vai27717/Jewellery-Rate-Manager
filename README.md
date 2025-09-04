<!doctype html>
<html lang="mr">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Estimate • Invoice • Expenses — Fixed Single File</title>
<style>
  :root{--bg:#071022;--card:#0e1726;--muted:#9bb0c7;--text:#e8f6ff;--accent:#60a5fa;--ok:#22c55e;}
  body{margin:0;font-family:system-ui,Segoe UI,Roboto,Arial;background:linear-gradient(180deg,#071022,#0b1222);color:var(--text)}
  .wrap{max-width:1100px;margin:18px auto;padding:14px}
  header{display:flex;justify-content:space-between;align-items:center}
  h1{margin:0;font-size:20px}
  .tabs{display:flex;gap:8px;margin:12px 0;flex-wrap:wrap}
  .tabbtn{padding:8px 12px;border-radius:10px;border:0;background:rgba(255,255,255,.04);cursor:pointer;color:var(--text)}
  .tabbtn[aria-selected="true"]{background:var(--accent);color:#021023}
  .card{background:var(--card);padding:14px;border-radius:12px;box-shadow:0 8px 32px rgba(0,0,0,.45)}
  label{display:block;font-size:13px;color:var(--muted);margin-bottom:6px}
  input,select,textarea,button{font:inherit}
  input,select,textarea{width:100%;padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,.06);background:#071125;color:var(--text)}
  textarea{min-height:80px}
  .row{display:grid;grid-template-columns:repeat(12,1fr);gap:10px}
  .row> *{grid-column:span 12}
  @media(min-width:760px){.row.cols-2> *{grid-column:span 6}.row.cols-3> *{grid-column:span 4}}
  table{width:100%;border-collapse:collapse;margin-top:8px}
  th,td{padding:8px;border-bottom:1px dashed rgba(255,255,255,.06);text-align:left;vertical-align:middle}
  .btn{padding:8px 10px;border-radius:8px;border:0;background:rgba(255,255,255,.04);color:var(--text);cursor:pointer}
  .btn.primary{background:var(--ok);color:#06220f}
  .right{display:flex;justify-content:flex-end}
  .kv{display:flex;justify-content:space-between;background:#071125;padding:8px;border-radius:8px;margin-top:6px}
  .hidden{display:none}
  .small{font-size:13px;color:var(--muted)}
  .pill{padding:4px 8px;border-radius:999px;background:#071125;color:var(--muted);font-size:12px}
</style>
</head>
<body>
  <div class="wrap">
    <header>
      <div>
        <h1>Estimate • Invoice • Expenses</h1>
        <div class="small">Fixed build — all buttons bound on DOMContentLoaded</div>
      </div>
      <div style="display:flex;gap:8px;align-items:center">
        <button class="btn" id="btnBackup">Backup</button>
        <button class="btn" id="btnRestore">Restore</button>
        <button class="btn" id="btnPrint">Print</button>
      </div>
    </header>

    <nav class="tabs" role="tablist" aria-label="Main tabs">
      <button class="tabbtn" data-tab="estimate" aria-selected="true">Estimate</button>
      <button class="tabbtn" data-tab="invoice">Invoice</button>
      <button class="tabbtn" data-tab="expenses">Expenses</button>
      <button class="tabbtn" data-tab="reports">Reports</button>
      <button class="tabbtn" data-tab="pick">Pick</button>
      <button class="tabbtn" data-tab="whats">WhatsApp Preview</button>
      <button class="tabbtn" data-tab="settings">Settings</button>
    </nav>

    <!-- ESTIMATE -->
    <section id="tab-estimate" class="card tab" role="tabpanel">
      <div class="row cols-2">
        <div>
          <label>Estimate No.</label>
          <input id="estNo" readonly>
          <div class="small">Format: <span class="pill">EST-0000</span></div>
        </div>
        <div>
          <label>Date</label>
          <input id="estDate" type="date">
        </div>
        <div>
          <label>Client / Company</label>
          <input id="estClient" placeholder="Client name">
        </div>
        <div>
          <label>Contact (Picker)</label>
          <div style="display:flex;gap:8px;align-items:center">
            <input id="estContact" placeholder="Name • Phone">
            <button class="btn" id="estPick">Pick</button>
          </div>
        </div>
      </div>

      <table class="items" id="estItems">
        <thead><tr><th>Item</th><th>Description</th><th>Qty</th><th>Unit</th><th>Rate</th><th>Amount</th><th></th></tr></thead>
        <tbody></tbody>
      </table>
      <div class="right" style="margin-top:8px"><button class="btn" id="estAdd">+ Add Item</button></div>

      <div class="row cols-3" style="margin-top:10px">
        <div>
          <label>Discount %</label>
          <input id="estDisc" type="number" step="0.01" value="0">
        </div>
        <div>
          <label>Currency</label>
          <input id="estCurr" value="₹">
        </div>
        <div>
          <label>Notes (this Estimate)</label>
          <input id="estNotes" placeholder="Optional">
        </div>
      </div>

      <div style="display:grid;grid-template-columns:1fr 320px;gap:12px;margin-top:12px;align-items:start">
        <div></div>
        <div>
          <div class="kv"><span>Sub Total</span><strong id="estSub">₹0.00</strong></div>
          <div class="kv"><span>Discount</span><strong id="estDiscAmt">₹0.00</strong></div>
          <div class="kv"><span>Final Amount</span><strong id="estFinal">₹0.00</strong></div>
        </div>
      </div>

      <div style="display:flex;gap:8px;justify-content:flex-end;margin-top:12px">
        <button class="btn primary" id="estSave">Save Estimate</button>
        <button class="btn" id="estClear">Clear</button>
      </div>

      <div style="margin-top:14px">
        <h3 style="margin:0 0 8px">Saved Estimates</h3>
        <table class="items" id="tblEstimates"><thead><tr><th>No</th><th>Date</th><th>Client</th><th>Total</th><th>Actions</th></tr></thead><tbody></tbody></table>
      </div>
    </section>

    <!-- INVOICE -->
    <section id="tab-invoice" class="card tab hidden" role="tabpanel">
      <div class="row cols-2">
        <div>
          <label>Invoice No.</label>
          <input id="invNo" readonly>
          <div class="small">Format: <span class="pill">INV-0000</span></div>
        </div>
        <div>
          <label>Date</label>
          <input id="invDate" type="date">
        </div>
        <div>
          <label>Bill To (Client)</label>
          <input id="invClient" placeholder="Client name">
        </div>
        <div>
          <label>Contact (Picker)</label>
          <div style="display:flex;gap:8px;align-items:center">
            <input id="invContact" placeholder="Name • Phone">
            <button class="btn" id="invPick">Pick</button>
          </div>
        </div>
      </div>

      <table class="items" id="invItems">
        <thead><tr><th>Item</th><th>Description</th><th>Qty</th><th>Unit</th><th>Rate</th><th>Amount</th><th></th></tr></thead>
        <tbody></tbody>
      </table>
      <div class="right" style="margin-top:8px"><button class="btn" id="invAdd">+ Add Item</button></div>

      <div class="row cols-3" style="margin-top:10px">
        <div>
          <label>Discount %</label>
          <input id="invDisc" type="number" step="0.01" value="0">
        </div>
        <div>
          <label>Advance</label>
          <input id="invAdvance" type="number" step="0.01" value="0">
        </div>
        <div>
          <label>Currency</label>
          <input id="invCurr" value="₹">
        </div>
      </div>

      <div style="display:grid;grid-template-columns:1fr 320px;gap:12px;margin-top:12px;align-items:start">
        <div>
          <label>Notes (this Invoice)</label>
          <textarea id="invNotes"></textarea>
        </div>
        <div>
          <div class="kv"><span>Sub Total</span><strong id="invSub">₹0.00</strong></div>
          <div class="kv"><span>Discount</span><strong id="invDiscAmt">₹0.00</strong></div>
          <div class="kv"><span>Advance</span><strong id="invAdvAmt">₹0.00</strong></div>
          <div class="kv"><span>Final Amount</span><strong id="invFinal">₹0.00</strong></div>
        </div>
      </div>

      <div style="display:flex;gap:8px;justify-content:flex-end;margin-top:12px">
        <button class="btn primary" id="invSave">Save Invoice</button>
        <button class="btn" id="invClear">Clear</button>
      </div>

      <div style="margin-top:14px">
        <h3 style="margin:0 0 8px">Saved Invoices</h3>
        <table class="items" id="tblInvoices"><thead><tr><th>No</th><th>Date</th><th>Client</th><th>Total</th><th>Actions</th></tr></thead><tbody></tbody></table>
      </div>
    </section>

    <!-- EXPENSES -->
    <section id="tab-expenses" class="card tab hidden" role="tabpanel">
      <div class="row cols-3">
        <div>
          <label>Invoice No.</label>
          <select id="expInv"></select>
          <div class="small">Invoice Total: <strong id="expInvTotal">₹0.00</strong></div>
        </div>
        <div>
          <label>Date</label>
          <input id="expDate" type="date">
        </div>
        <div>
          <label>Category</label>
          <div style="display:flex;gap:8px">
            <select id="expCat"></select>
            <button class="btn" id="catAdd">Add</button>
            <button class="btn" id="catDel">Delete</button>
          </div>
        </div>
      </div>

      <div class="row cols-3" style="margin-top:8px">
        <div><label>Amount</label><input id="expAmt" type="number" step="0.01"></div>
        <div><label>Note</label><input id="expNote"></div>
        <div class="right" style="align-items:end"><button class="btn" id="expAdd">+ Add Expense</button></div>
      </div>

      <div style="display:grid;grid-template-columns:1fr 360px;gap:12px;margin-top:12px">
        <div>
          <h3>Expenses (Selected Invoice)</h3>
          <table class="items" id="tblExpByInv"><thead><tr><th>Date</th><th>Category</th><th>Amount</th><th>Note</th><th></th></tr></thead><tbody></tbody></table>
        </div>
        <div>
          <h3>Summary</h3>
          <div class="kv"><span>Invoice Total</span><strong id="sumInv">₹0.00</strong></div>
          <div class="kv"><span>Total Expenses</span><strong id="sumExp">₹0.00</strong></div>
          <div class="kv"><span>Balance</span><strong id="sumBal">₹0.00</strong></div>
        </div>
      </div>
    </section>

    <!-- REPORTS -->
    <section id="tab-reports" class="card tab hidden" role="tabpanel">
      <div style="display:flex;gap:8px;align-items:center;margin-bottom:12px">
        <input id="repFrom" type="date">
        <input id="repTo" type="date">
        <input id="repSearch" placeholder="Client or Invoice">
        <button class="btn" id="repRun">Run</button>
        <button class="btn" id="repCSV">CSV</button>
      </div>
      <table class="items" id="tblReport"><thead><tr><th>Invoice</th><th>Date</th><th>Client</th><th>Invoice Total</th><th>Total Expenses</th><th>Balance</th></tr></thead><tbody></tbody></table>
    </section>

    <!-- PICK -->
    <section id="tab-pick" class="card tab hidden" role="tabpanel">
      <h3>Contact Picker</h3>
      <div style="display:flex;gap:8px;align-items:center">
        <button class="btn" id="btnPickSample">Pick Contact</button>
        <input id="pickedResult" placeholder="Picked contact will appear here">
      </div>
      <div class="small" style="margin-top:8px">Contact Picker mobile-only (Chrome Android). Desktop gets prompts.</div>
    </section>

    <!-- WHATSAPP -->
    <section id="tab-whats" class="card tab hidden" role="tabpanel">
      <h3>WhatsApp Preview</h3>
      <div class="row cols-3">
        <div>
          <label>Type</label>
          <select id="waType">
            <option value="estimate">Estimate</option>
            <option value="invoice">Invoice</option>
          </select>
        </div>
        <div>
          <label>Select Ref</label>
          <select id="waRef"></select>
        </div>
        <div style="display:flex;align-items:end">
          <button class="btn" id="waOpen">Open WhatsApp</button>
        </div>
      </div>
      <div style="margin-top:10px">
        <textarea id="waText" style="width:100%;min-height:200px"></textarea>
      </div>
    </section>

    <!-- SETTINGS -->
    <section id="tab-settings" class="card tab hidden" role="tabpanel">
      <div class="row cols-3">
        <div><label>Business Name</label><input id="bizName"></div>
        <div><label>Website</label><input id="bizWeb"></div>
        <div><label>Notify Email</label><input id="notifyEmail"></div>
      </div>
      <div style="margin-top:10px"><label>Permanent Notes</label><textarea id="bizNotes"></textarea></div>
      <div style="display:flex;gap:8px;justify-content:flex-end;margin-top:10px">
        <button class="btn primary" id="saveSettings">Save</button>
        <button class="btn" id="resetAll">Reset</button>
      </div>
    </section>
  </div>

<script>
document.addEventListener('DOMContentLoaded', () => {
  // Short helpers
  const $ = s => document.querySelector(s);
  const $$ = s => Array.from(document.querySelectorAll(s));
  const KEY = 'ei_sf_fixed_v1';

  // DB
  function defaultDB(){ return {
    counters: { est: 0, inv: 0 }, // start at 0 -> displays EST-0000 / INV-0000
    settings: { name: 'Vaibhav Enterprises', web: 'http://www.vaibhaventerprises.info', notify: 'dalvivaibhav4562@gmail.com', notes: '' },
    estimates: [], invoices: [], expenses: [], cats: ['Material','Transport','Labour']
  }; }
  let DB;
  try { DB = JSON.parse(localStorage.getItem(KEY)) || defaultDB(); } catch(e){ DB = defaultDB(); }

  function saveDB(){ localStorage.setItem(KEY, JSON.stringify(DB)); }

  // Utilities
  const pad4 = n => String(n).padStart(4,'0');
  const nextEstNo = () => `EST-${pad4(DB.counters.est)}`;
  const nextInvNo = () => `INV-${pad4(DB.counters.inv)}`;
  const today = () => new Date().toISOString().slice(0,10);
  const fmt = (n,curr='₹') => curr + (isNaN(n)?'0.00':Number(n).toLocaleString(undefined,{minimumFractionDigits:2,maximumFractionDigits:2}));

  // Tabs
  $$('.tabbtn').forEach(btn => btn.addEventListener('click', () => {
    $$('.tabbtn').forEach(b=>b.setAttribute('aria-selected','false'));
    btn.setAttribute('aria-selected','true');
    const t = btn.dataset.tab;
    $$('.tab').forEach(sec => sec.classList.add('hidden'));
    document.getElementById('tab-'+t).classList.remove('hidden');
    if(t === 'expenses') renderExpInvOptions();
    if(t === 'whats') refreshWARefs();
    if(t === 'reports') renderReport();
  }));

  // Initial values
  $('#estDate').value = today();
  $('#invDate').value = today();
  $('#expDate').value = today();
  $('#estNo').value = nextEstNo();
  $('#invNo').value = nextInvNo();

  // Item row builder (works for both)
  function makeItemRow(values){
    const tr = document.createElement('tr');
    tr.innerHTML = `
      <td><input class="it-item" placeholder="Item"></td>
      <td><input class="it-desc" placeholder="Description"></td>
      <td><input type="number" class="it-qty" value="1" step="0.01"></td>
      <td><input class="it-unit" placeholder="nos"></td>
      <td><input type="number" class="it-rate" value="0" step="0.01"></td>
      <td class="it-amt">0.00</td>
      <td><button class="btn del">Delete</button></td>
    `;
    if(values){
      tr.querySelector('.it-item').value = values.item || '';
      tr.querySelector('.it-desc').value = values.desc || '';
      tr.querySelector('.it-qty').value = values.qty || 1;
      tr.querySelector('.it-unit').value = values.unit || '';
      tr.querySelector('.it-rate').value = values.rate || 0;
    }
    const qty = tr.querySelector('.it-qty'), rate = tr.querySelector('.it-rate'), amt = tr.querySelector('.it-amt');
    function recalc(){ const q = parseFloat(qty.value||0); const r = parseFloat(rate.value||0); amt.textContent = (q*r).toFixed(2); computeAllTotals(); }
    qty.addEventListener('input', recalc);
    rate.addEventListener('input', recalc);
    tr.querySelector('.del').addEventListener('click', ()=>{ tr.remove(); computeAllTotals(); });
    recalc();
    return tr;
  }

  const estTbody = $('#estItems tbody');
  const invTbody = $('#invItems tbody');
  // ensure initial rows exist
  if(estTbody.children.length === 0) estTbody.appendChild(makeItemRow());
  if(invTbody.children.length === 0) invTbody.appendChild(makeItemRow());

  // Add item buttons
  $('#estAdd').addEventListener('click', ()=> estTbody.appendChild(makeItemRow()));
  $('#invAdd').addEventListener('click', ()=> invTbody.appendChild(makeItemRow()));

  // Totals
  function subtotal(tbody){
    return Array.from(tbody.querySelectorAll('.it-amt')).map(td => parseFloat(td.textContent||0)).reduce((a,b)=>a+b,0);
  }
  function computeTotalsEstimate(){
    const sub = subtotal(estTbody);
    const discPct = parseFloat($('#estDisc').value||0);
    const discAmt = sub * discPct / 100;
    $('#estSub').textContent = fmt(sub,$('#estCurr').value||'₹');
    $('#estDiscAmt').textContent = fmt(discAmt,$('#estCurr').value||'₹');
    $('#estFinal').textContent = fmt(Math.max(0, sub-discAmt),$('#estCurr').value||'₹');
    return { sub, discAmt, final: Math.max(0, sub-discAmt), curr: $('#estCurr').value||'₹' };
  }
  function computeTotalsInvoice(){
    const sub = subtotal(invTbody);
    const discPct = parseFloat($('#invDisc').value||0);
    const adv = parseFloat($('#invAdvance').value||0);
    const discAmt = sub * discPct / 100;
    const curr = $('#invCurr').value||'₹';
    $('#invSub').textContent = fmt(sub,curr);
    $('#invDiscAmt').textContent = fmt(discAmt,curr);
    $('#invAdvAmt').textContent = fmt(adv,curr);
    $('#invFinal').textContent = fmt(Math.max(0, sub - discAmt - adv),curr);
    return { sub, discAmt, advance: adv, final: Math.max(0, sub - discAmt - adv), curr };
  }
  function computeAllTotals(){ computeTotalsEstimate(); computeTotalsInvoice(); }
  $('#estDisc').addEventListener('input', computeTotalsEstimate);
  $('#estCurr').addEventListener('input', computeTotalsEstimate);
  $('#invDisc').addEventListener('input', computeTotalsInvoice);
  $('#invAdvance').addEventListener('input', computeTotalsInvoice);
  $('#invCurr').addEventListener('input', computeTotalsInvoice);

  // Collect items
  function collectItems(tbody){
    return Array.from(tbody.querySelectorAll('tr')).map(tr=>{
      const item = tr.querySelector('.it-item')?.value?.trim();
      if(!item) return null;
      return {
        item,
        desc: tr.querySelector('.it-desc').value.trim(),
        qty: +tr.querySelector('.it-qty').value || 0,
        unit: tr.querySelector('.it-unit').value.trim(),
        rate: +tr.querySelector('.it-rate').value || 0,
        amt: parseFloat(tr.querySelector('.it-amt').textContent||0)
      };
    }).filter(Boolean);
  }

  // Save Estimate
  $('#estSave').addEventListener('click', ()=>{
    const totals = computeTotalsEstimate();
    const obj = {
      no: $('#estNo').value.trim() || nextEstNo(),
      date: $('#estDate').value || today(),
      client: $('#estClient').value.trim(),
      contact: $('#estContact').value.trim(),
      items: collectItems(estTbody),
      totals: { sub: totals.sub, discAmt: totals.discAmt, finalAmt: totals.final },
      curr: totals.curr,
      notes: $('#estNotes').value.trim()
    };
    DB.estimates.push(obj);
    // increment counter AFTER saving so next displayed number changes
    DB.counters.est = DB.counters.est + 1;
    saveDB();
    renderEstimates();
    $('#estNo').value = nextEstNo();
    alert('Estimate saved ✅');
  });

  // Save Invoice
  $('#invSave').addEventListener('click', ()=>{
    const totals = computeTotalsInvoice();
    const obj = {
      no: $('#invNo').value.trim() || nextInvNo(),
      date: $('#invDate').value || today(),
      client: $('#invClient').value.trim(),
      contact: $('#invContact').value.trim(),
      items: collectItems(invTbody),
      totals: { sub: totals.sub, discAmt: totals.discAmt, advance: totals.advance, finalAmt: totals.final },
      curr: totals.curr,
      notes: $('#invNotes').value.trim()
    };
    DB.invoices.push(obj);
    DB.counters.inv = DB.counters.inv + 1;
    saveDB();
    renderInvoices();
    renderExpInvOptions();
    refreshWARefs();
    $('#invNo').value = nextInvNo();
    alert('Invoice saved ✅');
  });

  // Clear buttons
  $('#estClear').addEventListener('click', ()=>{ $('#estClient').value=''; $('#estContact').value=''; $('#estNotes').value=''; estTbody.innerHTML=''; estTbody.appendChild(makeItemRow()); computeTotalsEstimate(); });
  $('#invClear').addEventListener('click', ()=>{ $('#invClient').value=''; $('#invContact').value=''; $('#invNotes').value=''; invTbody.innerHTML=''; invTbody.appendChild(makeItemRow()); computeTotalsInvoice(); });

  // Render saved lists
  function renderEstimates(){
    const tb = $('#tblEstimates tbody'); tb.innerHTML = '';
    DB.estimates.forEach((e,i)=>{
      const tr = document.createElement('tr');
      tr.innerHTML = `<td>${e.no}</td><td>${e.date}</td><td>${e.client||'-'}</td><td>${fmt(e.totals.finalAmt,e.curr)}</td>
        <td>
          <button class="btn load" data-i="${i}" data-type="est">Load</button>
          <button class="btn del" data-i="${i}" data-type="est">Delete</button>
        </td>`;
      tb.appendChild(tr);
    });
    tb.querySelectorAll('button.load').forEach(btn => btn.addEventListener('click', (ev)=>{
      const idx = +ev.currentTarget.dataset.i; loadEstimate(DB.estimates[idx]);
    }));
    tb.querySelectorAll('button.del').forEach(btn => btn.addEventListener('click', (ev)=>{
      const idx = +ev.currentTarget.dataset.i; if(!confirm('Delete estimate?')) return;
      DB.estimates.splice(idx,1); saveDB(); renderEstimates(); refreshWARefs();
    }));
  }

  function loadEstimate(e){
    $('#estNo').value = e.no; $('#estDate').value = e.date; $('#estClient').value = e.client; $('#estContact').value = e.contact; $('#estNotes').value = e.notes||''; $('#estCurr').value = e.curr||'₹';
    estTbody.innerHTML = ''; e.items.forEach(it => estTbody.appendChild(makeItemRow(it))); computeTotalsEstimate();
  }

  function renderInvoices(){
    const tb = $('#tblInvoices tbody'); tb.innerHTML = '';
    DB.invoices.forEach((e,i)=>{
      const tr = document.createElement('tr');
      tr.innerHTML = `<td>${e.no}</td><td>${e.date}</td><td>${e.client||'-'}</td><td>${fmt(e.totals.finalAmt,e.curr)}</td>
        <td>
          <button class="btn load" data-i="${i}" data-type="inv">Load</button>
          <button class="btn del" data-i="${i}" data-type="inv">Delete</button>
        </td>`;
      tb.appendChild(tr);
    });
    tb.querySelectorAll('button.load').forEach(btn => btn.addEventListener('click', (ev)=>{
      const idx = +ev.currentTarget.dataset.i; loadInvoice(DB.invoices[idx]);
    }));
    tb.querySelectorAll('button.del').forEach(btn => btn.addEventListener('click', (ev)=>{
      const idx = +ev.currentTarget.dataset.i; if(!confirm('Delete invoice?')) return;
      DB.invoices.splice(idx,1); saveDB(); renderInvoices(); renderExpInvOptions(); renderReport(); refreshWARefs();
    }));
  }

  function loadInvoice(e){
    $('#invNo').value = e.no; $('#invDate').value = e.date; $('#invClient').value = e.client; $('#invContact').value = e.contact; $('#invNotes').value = e.notes||''; $('#invCurr').value = e.curr||'₹';
    invTbody.innerHTML = ''; e.items.forEach(it => invTbody.appendChild(makeItemRow(it))); computeTotalsInvoice();
  }

  // Expenses — categories
  function renderCats(){ $('#expCat').innerHTML = DB.cats.map(c => `<option value="${c}">${c}</option>`).join(''); }
  $('#catAdd').addEventListener('click', ()=>{
    const v = prompt('New category name'); if(!v) return;
    DB.cats.push(v.trim()); saveDB(); renderCats();
  });
  $('#catDel').addEventListener('click', ()=>{
    const v = $('#expCat').value; if(!v) return; if(!confirm('Delete '+v+'?')) return;
    DB.cats = DB.cats.filter(c=>c!==v); saveDB(); renderCats();
  });

  // Expenses for invoice
  function renderExpInvOptions(){
    const sel = $('#expInv'); const prev = sel.value;
    sel.innerHTML = '<option value="">— Select Invoice —</option>' + DB.invoices.map(i => `<option value="${i.no}">${i.no} • ${i.client||''}</option>`).join('');
    sel.value = DB.invoices.find(i=>i.no===prev) ? prev : '';
    updateExpInvTotal(); renderExpensesByInv();
  }
  function updateExpInvTotal(){
    const no = $('#expInv').value; const inv = DB.invoices.find(i=>i.no===no);
    $('#expInvTotal').textContent = inv ? fmt(inv.totals.finalAmt,inv.curr) : '₹0.00';
  }
  $('#expInv').addEventListener('change', ()=>{ updateExpInvTotal(); renderExpensesByInv(); });

  $('#expAdd').addEventListener('click', ()=>{
    const invNo = $('#expInv').value; if(!invNo){ alert('Select invoice'); return; }
    const rec = { invNo, date: $('#expDate').value||today(), cat: $('#expCat').value||'General', amt: +$('#expAmt').value||0, note: $('#expNote').value.trim() };
    if(rec.amt <= 0){ alert('Amount must be > 0'); return; }
    DB.expenses.push(rec); saveDB(); $('#expAmt').value=''; $('#expNote').value=''; renderExpensesByInv(); renderReport();
  });

  function renderExpensesByInv(){
    const tb = $('#tblExpByInv tbody'); tb.innerHTML = '';
    const no = $('#expInv').value; const inv = DB.invoices.find(i=>i.no===no); const curr = inv ? inv.curr : '₹';
    const list = DB.expenses.filter(e => e.invNo === no);
    list.forEach((e, idx) => {
      const tr = document.createElement('tr');
      tr.innerHTML = `<td>${e.date}</td><td>${e.cat}</td><td>${fmt(e.amt,curr)}</td><td>${e.note||''}</td><td><button class="btn del" data-i="${idx}">Delete</button></td>`;
      tb.appendChild(tr);
    });
    tb.querySelectorAll('.del').forEach(b => b.addEventListener('click', (ev)=>{
      const idx = +ev.currentTarget.dataset.i;
      const list = DB.expenses.filter(e => e.invNo === no);
      const rec = list[idx];
      const realIdx = DB.expenses.indexOf(rec);
      if(realIdx >= 0){ DB.expenses.splice(realIdx, 1); saveDB(); renderExpensesByInv(); renderReport(); }
    }));
    const invTotal = inv ? inv.totals.finalAmt : 0;
    const expTotal = list.reduce((a,b)=>a+b.amt,0);
    $('#sumInv').textContent = fmt(invTotal,curr);
    $('#sumExp').textContent = fmt(expTotal,curr);
    $('#sumBal').textContent = fmt(invTotal-expTotal,curr);
  }

  // Reports
  $('#repRun').addEventListener('click', renderReport);
  $('#repCSV').addEventListener('click', ()=>{
    const rows = [['Invoice','Date','Client','Invoice Total','Total Expenses','Balance']];
    DB.invoices.forEach(inv => {
      const expSum = DB.expenses.filter(e=>e.invNo===inv.no).reduce((a,b)=>a+b.amt,0);
      rows.push([inv.no,inv.date,inv.client||'',inv.totals.finalAmt,expSum,inv.totals.finalAmt-expSum]);
    });
    const csv = rows.map(r=>r.map(v => `"${String(v).replace(/"/g,'""')}"`).join(',')).join('\n');
    const blob = new Blob([csv],{type:'text/csv'}); const url = URL.createObjectURL(blob);
    const a = document.createElement('a'); a.href = url; a.download = 'report.csv'; a.click(); URL.revokeObjectURL(url);
  });

  function renderReport(){
    const tb = $('#tblReport tbody'); tb.innerHTML = '';
    const from = $('#repFrom').value ? new Date($('#repFrom').value) : null;
    const to = $('#repTo').value ? new Date($('#repTo').value) : null;
    const q = ($('#repSearch').value||'').toLowerCase();
    DB.invoices.forEach(inv => {
      const dt = new Date(inv.date);
      if(from && dt < from) return;
      if(to && dt > to) return;
      if(q && !(inv.no + inv.client).toLowerCase().includes(q)) return;
      const exp = DB.expenses.filter(e=>e.invNo===inv.no).reduce((a,b)=>a+b.amt,0);
      const tr = document.createElement('tr');
      tr.innerHTML = `<td>${inv.no}</td><td>${inv.date}</td><td>${inv.client||''}</td><td>${fmt(inv.totals.finalAmt,inv.curr)}</td><td>${fmt(exp,inv.curr)}</td><td>${fmt(inv.totals.finalAmt-exp,inv.curr)}</td>`;
      tb.appendChild(tr);
    });
  }

  // WhatsApp message builder + preview
  function buildMessage(obj, kind='Invoice'){
    const lines = [];
    lines.push(DB.settings.name || 'Vaibhav Enterprises'); lines.push('');
    lines.push(`${kind} No.: ${obj.no}`); lines.push(`Customer Name: ${obj.client||''}`); lines.push(`Contact No.: ${obj.contact||''}`); lines.push(`Date: ${obj.date}`); lines.push('');
    lines.push('Item Details:');
    obj.items.forEach(it => {
      lines.push(`• ${it.item} ${it.desc?('- '+it.desc):''}`);
      lines.push(`   Qty: ${it.qty} ${it.unit||''}, Rate: ${fmt(it.rate,obj.curr)}, Amount: ${fmt(it.amt,obj.curr)}`);
    });
    lines.push(''); const sub = obj.totals.sub ?? obj.items.reduce((a,b)=>a+(+b.amt||0),0);
    if(kind === 'Invoice') lines.push(`Advance: ${fmt(obj.totals.advance || 0,obj.curr)}`);
    lines.push(`Subtotal: ${fmt(sub,obj.curr)}`); lines.push(`Final Amount: ${fmt(obj.totals.finalAmt,obj.curr)}`); lines.push(''); lines.push('For more information, visit our website:'); lines.push(DB.settings.web || 'http://www.vaibhaventerprises.info'); lines.push('');
    // Accept link (mailto fallback for file://)
    let accept;
    if(location.protocol === 'file:'){ accept = `mailto:${DB.settings.notify}?subject=Accept%20${encodeURIComponent(kind)}%20${encodeURIComponent(obj.no)}&body=Customer%20accepted%20${encodeURIComponent(kind)}%20${encodeURIComponent(obj.no)}`; }
    else { accept = location.origin + location.pathname + `?accept=1&type=${kind.toLowerCase()}&no=${encodeURIComponent(obj.no)}`; }
    lines.push('Accept: ' + accept);
    return lines.join('\n');
  }

  function refreshWARefs(){
    const type = $('#waType').value;
    const sel = $('#waRef');
    const list = type === 'estimate' ? DB.estimates : DB.invoices;
    sel.innerHTML = list.map((x,i)=>`<option value="${i}">${x.no} • ${x.client||''}</option>`).join('');
    if(list.length){ $('#waText').value = buildMessage(list[0], type === 'estimate' ? 'Estimate' : 'Invoice'); }
    else $('#waText').value = '';
  }
  $('#waType').addEventListener('change', refreshWARefs);
  $('#waRef').addEventListener('change', ()=> {
    const type = $('#waType').value; const idx = +$('#waRef').value; const list = type === 'estimate' ? DB.estimates : DB.invoices;
    if(list[idx]) $('#waText').value = buildMessage(list[idx], type === 'estimate' ? 'Estimate' : 'Invoice'); else $('#waText').value = '';
  });
  $('#waOpen').addEventListener('click', ()=> {
    const txt = $('#waText').value.trim(); if(!txt) { alert('No message'); return; }
    const url = 'https://wa.me/?text=' + encodeURIComponent(txt);
    window.open(url,'_blank');
  });

  // Contact Picker
  async function pickContactTo(el){
    if(navigator.contacts && navigator.contacts.select){
      try{
        const res = await navigator.contacts.select(['name','tel','email'], {multiple:false});
        if(res && res.length){ const p = res[0]; const name = (p.name && p.name[0]) || ''; const tel = (p.tel && p.tel[0]) || ''; el.value = name + (tel ? (' • '+tel) : ''); }
      }catch(e){ alert('Contact picker denied / not supported'); }
    } else {
      const n = prompt('Contact name'); if(!n) return; const t = prompt('Phone or email (optional)'); el.value = n + (t ? (' • '+t) : '');
    }
  }
  $('#estPick').addEventListener('click', ()=> pickContactTo($('#estContact')));
  $('#invPick').addEventListener('click', ()=> pickContactTo($('#invContact')));
  $('#btnPickSample').addEventListener('click', async ()=>{
    const temp = document.createElement('input'); await pickContactTo(temp); $('#pickedResult').value = temp.value || '';
  });

  // Settings
  function loadSettingsUI(){ $('#bizName').value = DB.settings.name || ''; $('#bizWeb').value = DB.settings.web || ''; $('#notifyEmail').value = DB.settings.notify || ''; $('#bizNotes').value = DB.settings.notes || ''; }
  $('#saveSettings').addEventListener('click', ()=> {
    DB.settings.name = $('#bizName').value.trim() || 'Vaibhav Enterprises';
    DB.settings.web = $('#bizWeb').value.trim() || 'http://www.vaibhaventerprises.info';
    DB.settings.notify = $('#notifyEmail').value.trim() || 'dalvivaibhav4562@gmail.com';
    DB.settings.notes = $('#bizNotes').value || '';
    saveDB(); alert('Settings saved');
  });
  $('#resetAll').addEventListener('click', ()=> { if(confirm('Delete ALL local data?')){ localStorage.removeItem(KEY); location.reload(); } });

  // Backup / Restore / Print
  $('#btnBackup').addEventListener('click', ()=>{
    const blob = new Blob([JSON.stringify(DB,null,2)], {type:'application/json'}); const url = URL.createObjectURL(blob);
    const a = document.createElement('a'); a.href = url; a.download = 'ei-backup.json'; a.click(); URL.revokeObjectURL(url);
  });
  $('#btnRestore').addEventListener('click', ()=>{
    const inp = document.createElement('input'); inp.type='file'; inp.accept='application/json';
    inp.onchange = async ()=> { const f = inp.files[0]; if(!f) return; const txt = await f.text(); try{ DB = JSON.parse(txt); saveDB(); alert('Restored'); location.reload(); }catch(e){ alert('Invalid file'); } };
    inp.click();
  });
  $('#btnPrint').addEventListener('click', ()=> window.print());

  // Accept flow (if opened via hosted URL with ?accept=1...)
  (function handleAcceptURL(){
    const p = new URLSearchParams(location.search);
    if(!p.get('accept')) return;
    document.body.innerHTML = `<div style="padding:30px;color:#fff;font-family:inherit;background:#071022;min-height:100vh;display:flex;align-items:center;justify-content:center"><div style="max-width:760px;background:#0f1726;padding:20px;border-radius:10px"><h2>Thank you</h2><p>Response recorded. Click notify to send email to business.</p><p><button id="notifyBtn" style="padding:10px;border-radius:8px">Notify</button></p></div></div>`;
    document.getElementById('notifyBtn').addEventListener('click', ()=> {
      const mail = DB.settings.notify || 'dalvivaibhav4562@gmail.com';
      const subject = encodeURIComponent('Customer has responded to estimate/invoice');
      const body = encodeURIComponent('Customer accepted. Reference from link.');
      window.location.href = `mailto:${mail}?subject=${subject}&body=${body}`;
    });
  })();

  // initial render
  function boot(){
    renderCats(); renderEstimates(); renderInvoices(); renderExpInvOptions(); renderReport(); loadSettingsUI(); computeAllTotals();
    console.log('App initialized'); // helpful to check console if user reports buttons not working
  }
  boot();
});
</script>
</body>
</html>
