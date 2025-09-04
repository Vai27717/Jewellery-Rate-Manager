<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Estimate & Invoice — Single File</title>
<style>
  :root{--bg:#f3f7fb;--card:#ffffff;--muted:#6b7280;--text:#0f172a;--accent:#0ea5e9;--danger:#dc2626}
  body{font-family:Segoe UI,Roboto,Arial;margin:0;background:var(--bg);color:var(--text);-webkit-font-smoothing:antialiased}
  .wrap{max-width:1100px;margin:18px auto;padding:14px}
  header{display:flex;justify-content:space-between;align-items:center;margin-bottom:12px}
  h1{margin:0;font-size:20px}
  .tabs{display:flex;gap:8px;flex-wrap:wrap}
  .tabbtn{background:#e6eef8;border:0;padding:8px 10px;border-radius:8px;cursor:pointer}
  .tabbtn[aria-selected="true"]{background:var(--accent);color:#fff}
  .card{background:var(--card);padding:12px;border-radius:10px;box-shadow:0 8px 20px rgba(2,6,23,0.06)}
  .row{display:grid;grid-template-columns:repeat(12,1fr);gap:8px}
  .col-6{grid-column:span 6}
  .col-4{grid-column:span 4}
  .col-3{grid-column:span 3}
  label{display:block;font-size:13px;color:var(--muted);margin-bottom:6px}
  input,select,textarea,button{font:inherit}
  input,select,textarea{width:100%;padding:8px;border-radius:8px;border:1px solid #e6eef8;background:#fff}
  textarea{min-height:80px;resize:vertical}
  table{width:100%;border-collapse:collapse;margin-top:8px}
  th,td{padding:8px;border:1px solid #eef3f8;text-align:left;vertical-align:top}
  th{background:#fff;color:black}
  .it-amt{color:var(--danger);font-weight:700;text-align:right}
  .right{display:flex;justify-content:flex-end;gap:8px}
  .btn{padding:8px 10px;border-radius:8px;border:0;background:#e6eef8;cursor:pointer}
  .btn.primary{background:#10b981;color:white}
  .small{font-size:13px;color:var(--muted)}
  /* Saved tables: white bg, black text */
  .saved-table th,.saved-table td{background:white;color:black}
  .saved-table button{color:black;background:#f3f4f6;border:1px solid #e5e7eb;padding:6px;border-radius:6px}
  /* Responsive: stack item & description */
  @media(max-width:640px){
    table.items thead{display:none}
    table.items, table.items tbody, table.items tr, table.items td{display:block;width:100%}
    table.items td{border:none;padding:6px 0}
    table.items td .cell-label{display:block;font-size:12px;color:var(--muted);margin-bottom:6px}
  }
</style>
</head>
<body>
<div class="wrap">
  <header>
    <div>
      <h1>Estimate & Invoice</h1>
      <div class="small">Simple single-file app — mobile friendly</div>
    </div>
    <div class="tabs" role="tablist">
      <button class="tabbtn" data-tab="estimate" aria-selected="true">Estimate</button>
      <button class="tabbtn" data-tab="invoice">Invoice</button>
      <button class="tabbtn" data-tab="expenses">Expenses</button>
      <button class="tabbtn" data-tab="reports">Reports</button>
      <button class="tabbtn" data-tab="whats">WhatsApp Preview</button>
      <button class="tabbtn" data-tab="settings">Settings</button>
    </div>
  </header>

  <!-- ESTIMATE -->
  <section id="estimate" class="card" role="tabpanel">
    <div class="row" style="align-items:end">
      <div class="col-6">
        <label>Estimate No.</label>
        <input id="estNo" readonly>
        <div class="small">Format: EST-0000</div>
      </div>
      <div class="col-6">
        <label>Date</label>
        <input id="estDate" type="date">
      </div>
      <div class="col-6">
        <label>Client / Company</label>
        <input id="estClient">
      </div>
      <div class="col-6">
        <label>Contact</label>
        <input id="estContact" placeholder="Name • Phone">
      </div>
    </div>

    <table class="items" id="estItems">
      <thead><tr><th style="width:40%">Item / Description</th><th style="width:10%">Qty</th><th style="width:10%">Unit</th><th style="width:20%">Rate</th><th style="width:20%">Amount</th><th></th></tr></thead>
      <tbody></tbody>
    </table>
    <div class="right" style="margin-top:8px"><button class="btn" id="estAdd">+ Add Item</button></div>

    <div class="row" style="margin-top:10px;align-items:end">
      <div class="col-4">
        <label>Discount ₹</label>
        <input id="estDisc" type="number" value="0" step="0.01">
      </div>
      <div class="col-4">
        <label>Currency</label>
        <input id="estCurr" value="₹">
      </div>
      <div class="col-4">
        <label>Notes</label>
        <input id="estNotes">
      </div>
    </div>

    <div style="display:grid;grid-template-columns:1fr 320px;gap:12px;margin-top:12px">
      <div></div>
      <div>
        <div style="display:flex;justify-content:space-between;padding:8px"><span>Sub Total</span><strong id="estSub">₹0.00</strong></div>
        <div style="display:flex;justify-content:space-between;padding:8px"><span>Discount</span><strong id="estDiscAmt">₹0.00</strong></div>
        <div style="display:flex;justify-content:space-between;padding:8px"><span>Final Amount</span><strong id="estFinal">₹0.00</strong></div>
      </div>
    </div>

    <div class="right" style="margin-top:12px">
      <button class="btn primary" id="estSave">Save Estimate</button>
      <button class="btn" id="estPDF">Export PDF</button>
    </div>

    <h3 style="margin-top:18px">Saved Estimates</h3>
    <table id="tblEstimates" class="saved-table"><thead><tr><th>No</th><th>Date</th><th>Client</th><th>Total</th><th>Actions</th></tr></thead><tbody></tbody></table>
  </section>

  <!-- INVOICE -->
  <section id="invoice" class="card" role="tabpanel" style="display:none;margin-top:14px">
    <div class="row" style="align-items:end">
      <div class="col-6">
        <label>Invoice No.</label>
        <input id="invNo" readonly><div class="small">Format: INV-0000</div>
      </div>
      <div class="col-6">
        <label>Date</label>
        <input id="invDate" type="date">
      </div>
      <div class="col-6">
        <label>Client</label><input id="invClient">
      </div>
      <div class="col-6">
        <label>Contact</label><input id="invContact">
      </div>
    </div>

    <table class="items" id="invItems"><thead><tr><th style="width:40%">Item / Description</th><th>Qty</th><th>Unit</th><th>Rate</th><th>Amount</th><th></th></tr></thead><tbody></tbody></table>
    <div class="right" style="margin-top:8px"><button class="btn" id="invAdd">+ Add Item</button></div>

    <div class="row" style="margin-top:10px;align-items:end">
      <div class="col-4"><label>Discount ₹</label><input id="invDisc" type="number" value="0" step="0.01"></div>
      <div class="col-4"><label>Advance</label><input id="invAdvance" type="number" value="0" step="0.01"></div>
      <div class="col-4"><label>Currency</label><input id="invCurr" value="₹"></div>
    </div>

    <div style="display:grid;grid-template-columns:1fr 320px;gap:12px;margin-top:12px">
      <div><label>Invoice Notes</label><textarea id="invNotes"></textarea></div>
      <div>
        <div style="display:flex;justify-content:space-between;padding:8px"><span>Sub Total</span><strong id="invSub">₹0.00</strong></div>
        <div style="display:flex;justify-content:space-between;padding:8px"><span>Discount</span><strong id="invDiscAmt">₹0.00</strong></div>
        <div style="display:flex;justify-content:space-between;padding:8px"><span>Advance</span><strong id="invAdvAmt">₹0.00</strong></div>
        <div style="display:flex;justify-content:space-between;padding:8px"><span>Final Amount</span><strong id="invFinal">₹0.00</strong></div>
      </div>
    </div>

    <div class="right" style="margin-top:12px">
      <button class="btn primary" id="invSave">Save Invoice</button>
      <button class="btn" id="invPDF">Export PDF</button>
    </div>

    <h3 style="margin-top:18px">Saved Invoices</h3>
    <table id="tblInvoices" class="saved-table"><thead><tr><th>No</th><th>Date</th><th>Client</th><th>Total</th><th>Actions</th></tr></thead><tbody></tbody></table>
  </section>

  <!-- EXPENSES -->
  <section id="expenses" class="card" role="tabpanel" style="display:none;margin-top:14px">
    <div class="row" style="align-items:end">
      <div class="col-4">
        <label>Invoice No. (optional)</label>
        <select id="expInv"><option value="">-- none --</option></select>
      </div>
      <div class="col-4"><label>Date</label><input id="expDate" type="date"></div>
      <div class="col-4"><label>Category</label><input id="expCat" placeholder="Category"></div>
    </div>
    <div class="row" style="margin-top:8px;align-items:end">
      <div class="col-4"><label>Amount</label><input id="expAmt" type="number" step="0.01"></div>
      <div class="col-4"><label>Note</label><input id="expNote"></div>
      <div class="col-4 right"><button class="btn" id="expAdd">+ Add Expense</button></div>
    </div>

    <h3 style="margin-top:12px">Expenses (selected invoice)</h3>
    <table id="tblExpByInv" class="saved-table"><thead><tr><th>Date</th><th>Category</th><th>Amount</th><th>Note</th><th></th></tr></thead><tbody></tbody></table>
  </section>

  <!-- REPORTS -->
  <section id="reports" class="card" role="tabpanel" style="display:none;margin-top:14px">
    <h3>Reports</h3>
    <div style="display:flex;gap:8px"><input id="repFrom" type="date"><input id="repTo" type="date"><input id="repSearch" placeholder="Client or Invoice"><button class="btn" id="repRun">Run</button></div>
    <table id="tblReport" class="saved-table" style="margin-top:8px"><thead><tr><th>Invoice</th><th>Date</th><th>Client</th><th>Invoice Total</th><th>Total Expenses</th><th>Balance</th></tr></thead><tbody></tbody></table>
  </section>

  <!-- WHATSAPP PREVIEW -->
  <section id="whats" class="card" role="tabpanel" style="display:none;margin-top:14px">
    <h3>WhatsApp Preview</h3>
    <div style="display:flex;gap:8px;align-items:center"><select id="waType"><option value="estimate">Estimate</option><option value="invoice">Invoice</option></select><select id="waRef"></select><button class="btn" id="waOpen">Open WhatsApp</button></div>
    <textarea id="waText" style="width:100%;min-height:180px;margin-top:8px"></textarea>
  </section>

  <!-- SETTINGS -->
  <section id="settings" class="card" role="tabpanel" style="display:none;margin-top:14px">
    <h3>Settings</h3>
    <div class="row">
      <div class="col-4"><label>Business Name</label><input id="bizName"></div>
      <div class="col-4"><label>Website</label><input id="bizWeb"></div>
      <div class="col-4"><label>Notify Email</label><input id="notifyEmail"></div>
    </div>
    <div style="margin-top:8px"><label>Permanent Notes</label><textarea id="bizNotes"></textarea></div>
    <div class="right" style="margin-top:8px"><button class="btn primary" id="saveSettings">Save</button><button class="btn" id="resetAll">Reset All</button></div>
  </section>

</div>

<script>
// Simple single-file app JS
(function(){
  // helpers
  const $ = s => document.querySelector(s);
  const $$ = s => Array.from(document.querySelectorAll(s));
  const KEY = 'bm_sf_v2';

  // default DB
  function defaultDB(){ return {
    counters: { est: 0, inv: 0 },
    settings: { name:'Vaibhav Enterprises', web:'http://www.vaibhaventerprises.info', notify:'dalvivaibhav4562@gmail.com', notes:'' },
    estimates: [], invoices: [], expenses: []
  };}

  let DB;
  try{ DB = JSON.parse(localStorage.getItem(KEY)) || defaultDB(); }catch(e){ DB = defaultDB(); }
  function saveDB(){ localStorage.setItem(KEY, JSON.stringify(DB)); }

  const pad4 = n=>String(n).padStart(4,'0');
  const nextEstNo = ()=> `EST-${pad4(DB.counters.est)}`;
  const nextInvNo = ()=> `INV-${pad4(DB.counters.inv)}`;
  const today = ()=> new Date().toISOString().slice(0,10);
  const fmt = (n,curr='₹')=> curr + (isNaN(n)?'0.00':Number(n).toLocaleString(undefined,{minimumFractionDigits:2,maximumFractionDigits:2}));

  // Tabs
  $$('.tabbtn').forEach(btn=>{
    btn.addEventListener('click', ()=> {
      $$('.tabbtn').forEach(b=>b.setAttribute('aria-selected','false'));
      btn.setAttribute('aria-selected','true');
      const tab = btn.dataset.tab;
      $$('.card[role="tabpanel"]').forEach(s=>s.style.display='none');
      document.getElementById(tab).style.display = 'block';
      if(tab==='whats') refreshWARefs();
      if(tab==='expenses') renderExpInvOptions();
      if(tab==='reports') renderReport();
    });
  });

  // init fields
  $('#estDate').value = today();
  $('#invDate').value = today();
  $('#expDate').value = today();
  $('#estNo').value = nextEstNo();
  $('#invNo').value = nextInvNo();
  $('#bizName').value = DB.settings.name;
  $('#bizWeb').value = DB.settings.web;
  $('#notifyEmail').value = DB.settings.notify;
  $('#bizNotes').value = DB.settings.notes;

  // Item row builder (Item + Description stacked in first cell)
  function makeItemRow(values){
    const tr = document.createElement('tr');
    tr.innerHTML = `
      <td>
        <div><input class="it-item" placeholder="Item" style="margin-bottom:6px;padding:6px;border-radius:6px;border:1px solid #edf2f7"></div>
        <div><textarea class="it-desc" placeholder="Description" style="width:100%;min-height:60px;padding:6px;border-radius:6px;border:1px solid #edf2f7"></textarea></div>
      </td>
      <td><input type="number" class="it-qty" value="1" min="0" step="0.01" style="width:80px"></td>
      <td><input class="it-unit" style="width:80px"></td>
      <td><input type="number" class="it-rate" value="0" min="0" step="0.01" style="width:120px"></td>
      <td class="it-amt">0.00</td>
      <td><button class="btn del">Delete</button></td>
    `;
    if(values){
      tr.querySelector('.it-item').value = values.item||values.name||'';
      tr.querySelector('.it-desc').value = values.desc||values.description||'';
      tr.querySelector('.it-qty').value = values.qty||1;
      tr.querySelector('.it-unit').value = values.unit||'';
      tr.querySelector('.it-rate').value = values.rate||values.ratePer||0;
      tr.querySelector('.it-amt').textContent = (values.amt||values.amount||0).toFixed? (values.amt||values.amount||0).toFixed(2): (values.amt||values.amount||0);
    }
    // events
    tr.querySelector('.del').addEventListener('click', ()=> { tr.remove(); computeTotals(); });
    ['input','change'].forEach(ev => tr.querySelectorAll('input,textarea').forEach(inp => inp.addEventListener(ev, computeTotals)));
    return tr;
  }

  const estTbody = document.querySelector('#estItems tbody');
  const invTbody = document.querySelector('#invItems tbody');

  if(estTbody.children.length===0) estTbody.appendChild(makeItemRow());
  if(invTbody.children.length===0) invTbody.appendChild(makeItemRow());

  $('#estAdd').addEventListener('click', ()=> estTbody.appendChild(makeItemRow()));
  $('#invAdd').addEventListener('click', ()=> invTbody.appendChild(makeItemRow()));

  // totals helpers
  function subtotal(tbody){
    return Array.from(tbody.querySelectorAll('.it-amt')).map(td=>parseFloat(td.textContent||0)).reduce((a,b)=>a+b,0);
  }

  function computeTotals(){
    // estimate
    Array.from(estTbody.querySelectorAll('tr')).forEach(tr=>{
      const q = parseFloat(tr.querySelector('.it-qty').value||0);
      const r = parseFloat(tr.querySelector('.it-rate').value||0);
      tr.querySelector('.it-amt').textContent = (q*r).toFixed(2);
    });
    const estSub = subtotal(estTbody);
    const estDisc = parseFloat($('#estDisc').value||0);
    const estFinal = Math.max(0, estSub - estDisc);
    $('#estSub').textContent = fmt(estSub,$('#estCurr').value||'₹');
    $('#estDiscAmt').textContent = fmt(estDisc,$('#estCurr').value||'₹');
    $('#estFinal').textContent = fmt(estFinal,$('#estCurr').value||'₹');

    // invoice
    Array.from(invTbody.querySelectorAll('tr')).forEach(tr=>{
      const q = parseFloat(tr.querySelector('.it-qty').value||0);
      const r = parseFloat(tr.querySelector('.it-rate').value||0);
      tr.querySelector('.it-amt').textContent = (q*r).toFixed(2);
    });
    const invSub = subtotal(invTbody);
    const invDisc = parseFloat($('#invDisc').value||0);
    const invAdv = parseFloat($('#invAdvance').value||0);
    const invFinal = Math.max(0, invSub - invDisc - invAdv);
    $('#invSub').textContent = fmt(invSub,$('#invCurr').value||'₹');
    $('#invDiscAmt').textContent = fmt(invDisc,$('#invCurr').value||'₹');
    $('#invAdvAmt').textContent = fmt(invAdv,$('#invCurr').value||'₹');
    $('#invFinal').textContent = fmt(invFinal,$('#invCurr').value||'₹');
  }

  // bind inputs
  ['#estDisc','#estCurr','#invDisc','#invAdvance','#invCurr'].forEach(sel=>{
    const el = document.querySelector(sel);
    if(el) el.addEventListener('input', computeTotals);
  });

  computeTotals();

  // Save estimate
  $('#estSave').addEventListener('click', ()=>{
    const items = collectItems(estTbody);
    if(items.length===0){ alert('Add item'); return; }
    const obj = {
      no: $('#estNo').value.trim() || nextEstNo(),
      date: $('#estDate').value || today(),
      client: $('#estClient').value.trim(),
      contact: $('#estContact').value.trim(),
      items,
      totals: { sub: subtotal(estTbody), discAmt: parseFloat($('#estDisc').value||0), final: parseFloat((subtotal(estTbody) - parseFloat($('#estDisc').value||0)).toFixed(2)) },
      curr: $('#estCurr').value||'₹',
      notes: $('#estNotes').value||''
    };
    DB.estimates.push(obj);
    DB.counters.est += 1;
    saveDB();
    renderEstimates();
    $('#estNo').value = nextEstNo();
    alert('Estimate saved ✅');
  });

  // Save invoice
  $('#invSave').addEventListener('click', ()=>{
    const items = collectItems(invTbody);
    if(items.length===0){ alert('Add item'); return; }
    const obj = {
      no: $('#invNo').value.trim() || nextInvNo(),
      date: $('#invDate').value || today(),
      client: $('#invClient').value.trim(),
      contact: $('#invContact').value.trim(),
      items,
      totals: { sub: subtotal(invTbody), discAmt: parseFloat($('#invDisc').value||0), advance: parseFloat($('#invAdvance').value||0), final: parseFloat((subtotal(invTbody) - parseFloat($('#invDisc').value||0) - parseFloat($('#invAdvance').value||0)).toFixed(2)) },
      curr: $('#invCurr').value||'₹',
      notes: $('#invNotes').value||''
    };
    DB.invoices.push(obj);
    DB.counters.inv += 1;
    saveDB();
    renderInvoices();
    renderExpInvOptions();
    $('#invNo').value = nextInvNo();
    alert('Invoice saved ✅');
  });

  // collectItems utility
  function collectItems(tbody){
    return Array.from(tbody.querySelectorAll('tr')).map(tr=>{
      const name = tr.querySelector('.it-item').value.trim();
      if(!name) return null;
      return {
        item: name,
        desc: tr.querySelector('.it-desc').value.trim(),
        qty: parseFloat(tr.querySelector('.it-qty').value||0),
        unit: tr.querySelector('.it-unit').value.trim(),
        rate: parseFloat(tr.querySelector('.it-rate').value||0),
        amt: parseFloat(tr.querySelector('.it-amt').textContent||0)
      };
    }).filter(Boolean);
  }

  // render lists
  function renderEstimates(){
    const tb = $('#tblEstimates tbody'); tb.innerHTML = '';
    DB.estimates.forEach((e,i)=>{
      const tr = document.createElement('tr');
      tr.innerHTML = `<td>${e.no}</td><td>${e.date}</td><td>${e.client||''}</td><td>${fmt(e.totals.final||e.totals.finalAmt|| e.totals.final, e.curr||'₹')}</td><td><button class="btn load" data-i="${i}">Load</button> <button class="btn del" data-i="${i}">Delete</button></td>`;
      tb.appendChild(tr);
    });
    tb.querySelectorAll('.load').forEach(b=>b.addEventListener('click', ev=>{
      const i = +ev.currentTarget.dataset.i;
      loadEstimate(DB.estimates[i]);
    }));
    tb.querySelectorAll('.del').forEach(b=>b.addEventListener('click', ev=>{
      const i = +ev.currentTarget.dataset.i;
      if(confirm('Delete estimate?')){ DB.estimates.splice(i,1); saveDB(); renderEstimates(); refreshWARefs(); }
    }));
  }

  function loadEstimate(e){
    $('#estNo').value = e.no; $('#estDate').value = e.date; $('#estClient').value = e.client; $('#estContact').value = e.contact; $('#estCurr').value = e.curr||'₹'; $('#estNotes').value = e.notes||'';
    estTbody.innerHTML=''; e.items.forEach(it => estTbody.appendChild(makeItemRow({item:it.item, desc:it.desc, qty:it.qty, unit:it.unit, rate:it.rate, amt:it.amt})));
    computeTotals();
  }

  function renderInvoices(){
    const tb = $('#tblInvoices tbody'); tb.innerHTML = '';
    DB.invoices.forEach((inv,i)=>{
      const tr = document.createElement('tr');
      tr.innerHTML = `<td>${inv.no}</td><td>${inv.date}</td><td>${inv.client||''}</td><td>${fmt(inv.totals.final||inv.totals.finalAmt||inv.totals.final,inv.curr||'₹')}</td><td><button class="btn load" data-i="${i}">Load</button> <button class="btn del" data-i="${i}">Delete</button></td>`;
      tb.appendChild(tr);
    });
    tb.querySelectorAll('.load').forEach(b=>b.addEventListener('click', ev=>{ loadInvoice(DB.invoices[+ev.currentTarget.dataset.i]); }));
    tb.querySelectorAll('.del').forEach(b=>b.addEventListener('click', ev=>{ const i=+ev.currentTarget.dataset.i; if(confirm('Delete invoice?')){ DB.invoices.splice(i,1); saveDB(); renderInvoices(); renderExpInvOptions(); renderReport(); refreshWARefs(); }}));
  }

  function loadInvoice(inv){
    $('#invNo').value = inv.no; $('#invDate').value = inv.date; $('#invClient').value = inv.client; $('#invContact').value = inv.contact; $('#invCurr').value = inv.curr||'₹'; $('#invNotes').value = inv.notes||''; $('#invDisc').value = inv.totals.discAmt||0; $('#invAdvance').value = inv.totals.advance||0;
    invTbody.innerHTML=''; inv.items.forEach(it => invTbody.appendChild(makeItemRow({item:it.item, desc:it.desc, qty:it.qty, unit:it.unit, rate:it.rate, amt:it.amt})));
    computeTotals();
  }

  // Expenses
  function renderExpInvOptions(){
    const sel = $('#expInv'); sel.innerHTML = '<option value="">-- none --</option>' + DB.invoices.map(i=>`<option value="${i.no}">${i.no} • ${i.client||''}</option>`).join('');
    renderExpensesForSelected();
  }
  function renderExpensesForSelected(){
    const tb = $('#tblExpByInv tbody'); tb.innerHTML = ''; const no = $('#expInv').value;
    const list = DB.expenses.filter(e=>e.invNo===no);
    list.forEach((x,idx)=>{ const tr=document.createElement('tr'); tr.innerHTML = `<td>${x.date}</td><td>${x.cat}</td><td>${fmt(x.amt)}</td><td>${x.note||''}</td><td><button class="btn del" data-i="${idx}">Delete</button></td>`; tb.appendChild(tr); });
    tb.querySelectorAll('.del').forEach(b=>b.addEventListener('click', ev=>{ const idx=+ev.currentTarget.dataset.i; const rec = list[idx]; const real = DB.expenses.indexOf(rec); if(real>=0){ DB.expenses.splice(real,1); saveDB(); renderExpensesForSelected(); renderReport(); }}));
  }

  $('#expAdd').addEventListener('click', ()=>{
    const invNo = $('#expInv').value || '';
    const date = $('#expDate').value || today();
    const cat = $('#expCat').value || 'General';
    const amt = parseFloat($('#expAmt').value||0);
    const note = $('#expNote').value||'';
    if(amt<=0){ alert('Enter amount'); return; }
    DB.expenses.push({ invNo, date, cat, amt, note });
    saveDB(); $('#expAmt').value=''; $('#expNote').value=''; renderExpensesForSelected(); renderReport();
  });

  // Reports
  $('#repRun').addEventListener('click', renderReport);
  function renderReport(){
    const tb = $('#tblReport tbody'); tb.innerHTML = '';
    const from = $('#repFrom').value ? new Date($('#repFrom').value) : null;
    const to = $('#repTo').value ? new Date($('#repTo').value) : null;
    const q = ($('#repSearch').value||'').toLowerCase();
    DB.invoices.forEach(inv=>{
      const dt = new Date(inv.date);
      if(from && dt < from) return;
      if(to && dt > to) return;
      if(q && !(inv.no+inv.client).toLowerCase().includes(q)) return;
      const expSum = DB.expenses.filter(e=>e.invNo===inv.no).reduce((a,b)=>a+b.amt,0);
      const tr = document.createElement('tr');
      tr.innerHTML = `<td>${inv.no}</td><td>${inv.date}</td><td>${inv.client||''}</td><td>${fmt(inv.totals.final||inv.totals.finalAmt||inv.totals.final,inv.curr||'₹')}</td><td>${fmt(expSum,inv.curr||'₹')}</td><td>${fmt((inv.totals.final||inv.totals.finalAmt||inv.totals.final)-expSum,inv.curr||'₹')}</td>`;
      tb.appendChild(tr);
    });
  }

  // WhatsApp builder and Accept handling
  function buildMessage(obj, kind='Estimate'){
    const lines = [];
    lines.push(DB.settings.name||'Vaibhav Enterprises');
    lines.push('');
    lines.push(`${kind} No.: ${obj.no}`);
    lines.push(`Customer Name: ${obj.client||''}`);
    lines.push(`Contact No.: ${obj.contact||''}`);
    lines.push(`Date: ${obj.date||''}`);
    lines.push('');
    lines.push('Item Details:');
    (obj.items||[]).forEach(it=>{
      lines.push(`• ${it.item} ${it.desc?('- '+it.desc):''}`);
      lines.push(`   Qty: ${it.qty} ${it.unit||''}, Rate: ${fmt(it.rate,obj.curr)}, Amount: ${fmt(it.amt||0,obj.curr)}`);
    });
    if(obj.totals && typeof obj.totals.discAmt!=='undefined') lines.push(`Discount: ${fmt(obj.totals.discAmt,obj.curr||'₹')}`);
    lines.push(`Subtotal: ${fmt(obj.totals.sub||0,obj.curr||'₹')}`);
    lines.push(`Final Amount: ${fmt(obj.totals.final||0,obj.curr||'₹')}`);
    lines.push('');
    lines.push('For more information, visit our website:');
    lines.push(DB.settings.web || 'http://www.vaibhaventerprises.info');
    // Accept links
    const mail = DB.settings.notify || 'dalvivaibhav4562@gmail.com';
    const subject = encodeURIComponent(`${kind} Accepted: ${obj.no}`);
    const bodyLines = [
      `${kind} Details:`,
      `Estimate No: ${obj.no}`,
      `Work Title: ${obj.notes||''}`,
      `Name: ${obj.client||''}`,
      `Address: ${obj.notes||''}`,
      `Contact: ${obj.contact||''}`,
      `Estimate Date: ${obj.date||''}`,
      `Estimate Amount: ${obj.totals.final||0}`,
      `Customer Response: Accept`
    ];
    const body = encodeURIComponent(bodyLines.join('\n'));
    const mailto = `mailto:${mail}?subject=${subject}&body=${body}`;
    const webAccept = (location.protocol==='file:'? mailto : location.origin + location.pathname + `?accept=1&type=${kind.toLowerCase()}&no=${encodeURIComponent(obj.no)}`);
    lines.push('');
    lines.push(`Accept (email): ${mailto}`);
    lines.push(`Accept (web): ${webAccept}`);
    return lines.join('\n');
  }

  function refreshWARefs(){
    const type = $('#waType').value;
    const sel = $('#waRef'); sel.innerHTML = '';
    const list = type==='estimate' ? DB.estimates : DB.invoices;
    sel.innerHTML = list.map((x,i)=>`<option value="${i}">${x.no} • ${x.client||''}</option>`).join('');
    if(list.length) $('#waText').value = buildMessage(list[0], type==='estimate'?'Estimate':'Invoice'); else $('#waText').value = '';
  }
  $('#waType').addEventListener('change', refreshWARefs);
  $('#waRef').addEventListener('change', ()=>{ const type=$('#waType').value; const idx=+$('#waRef').value; const list = type==='estimate'?DB.estimates:DB.invoices; if(list[idx]) $('#waText').value = buildMessage(list[idx], type==='estimate'?'Estimate':'Invoice'); });

  $('#waOpen').addEventListener('click', ()=>{ const txt = $('#waText').value.trim(); if(!txt){ alert('No message'); return; } window.open('https://wa.me/?text=' + encodeURIComponent(txt),'_blank'); });

  // Accept flow when opened via ?accept=1&type=estimate&no=...
  (function handleAcceptURL(){
    try{
      const p = new URLSearchParams(location.search);
      if(!p.get('accept')) return;
      const type = p.get('type') || 'estimate';
      const no = p.get('no') || '';
      const rec = (type==='estimate' ? DB.estimates.find(x=>x.no===no) : DB.invoices.find(x=>x.no===no));
      const title = rec?.notes || '';
      const name = rec?.client || '';
      const address = rec?.notes || '';
      const contact = rec?.contact || '';
      const date = rec?.date || '';
      const amount = rec?.totals?.final || 0;
      const mail = DB.settings.notify || 'dalvivaibhav4562@gmail.com';
      const subject = encodeURIComponent('Customer has responded to the estimate/invoice');
      const bodyLines = [
        'Estimate Details:',
        `Estimate No: ${no || ''}`,
        `Work Title: ${title}`,
        `Name: ${name}`,
        `Address: ${address}`,
        `Contact: ${contact}`,
        `Estimate Date: ${date}`,
        `Estimate Amount: ${amount}`,
        'Customer Response: Accept'
      ];
      const body = encodeURIComponent(bodyLines.join('\n'));
      document.body.innerHTML = `<div style="padding:30px;color:#022432;background:#fff;min-height:100vh"><h2>Thank you — your response was recorded</h2><p style="margin-top:6px">Click the button below to notify the business owner via email.</p><p style="margin-top:18px"><button id="notifyBtn" style="padding:10px 14px;border-radius:8px;border:0;background:#22c55e;color:#06220f">Notify Owner by Email</button></p></div>`;
      document.getElementById('notifyBtn').addEventListener('click', ()=>{ window.location.href = `mailto:${mail}?subject=${subject}&body=${body}`; });
    }catch(e){ console.error('Accept flow error', e); }
  })();

  // PDF (simple)
  function generatePDF(htmlContent, filename){
    if(window.html2pdf){ html2pdf().from(htmlContent).set({filename}).save(); return; }
    alert('PDF export requires html2pdf library (online).');
  }
  $('#estPDF').addEventListener('click', ()=>{
    const obj = { no: $('#estNo').value, date: $('#estDate').value, client: $('#estClient').value, contact: $('#estContact').value, items: collectItems(estTbody), totals: { sub: subtotal(estTbody), discAmt: parseFloat($('#estDisc').value||0), final: parseFloat((subtotal(estTbody)-parseFloat($('#estDisc').value||0)).toFixed(2)) }, curr: $('#estCurr').value||'₹', notes: $('#estNotes').value||'' };
    const html = `<div><h2>Estimate ${obj.no}</h2><p>${obj.client}</p><pre>${buildMessage(obj,'Estimate')}</pre></div>`;
    generatePDF(html, `${obj.no}.pdf`);
  });
  $('#invPDF').addEventListener('click', ()=>{
    const obj = { no: $('#invNo').value, date: $('#invDate').value, client: $('#invClient').value, contact: $('#invContact').value, items: collectItems(invTbody), totals: { sub: subtotal(invTbody), discAmt: parseFloat($('#invDisc').value||0), advance: parseFloat($('#invAdvance').value||0), final: parseFloat((subtotal(invTbody)-parseFloat($('#invDisc').value||0)-parseFloat($('#invAdvance').value||0)).toFixed(2)) }, curr: $('#invCurr').value||'₹', notes: $('#invNotes').value||'' };
    const html = `<div><h2>Invoice ${obj.no}</h2><p>${obj.client}</p><pre>${buildMessage(obj,'Invoice')}</pre></div>`;
    generatePDF(html, `${obj.no}.pdf`);
  });

  // render adjusts
  renderEstimates(); renderInvoices(); renderExpInvOptions(); renderReport(); refreshWARefs();

  // settings save/reset
  $('#saveSettings').addEventListener('click', ()=>{ DB.settings.name = $('#bizName').value; DB.settings.web = $('#bizWeb').value; DB.settings.notify = $('#notifyEmail').value; DB.settings.notes = $('#bizNotes').value; saveDB(); alert('Saved'); });
  $('#resetAll').addEventListener('click', ()=>{ if(confirm('Delete ALL data?')){ localStorage.removeItem(KEY); location.reload(); } });

  // backup/restore could be added

})(); // IIFE end
</script>
</body>
</html>
