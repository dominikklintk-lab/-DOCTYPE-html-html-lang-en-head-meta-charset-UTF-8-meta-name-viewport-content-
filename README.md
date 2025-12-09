# -DOCTYPE-html-html-lang-en-head-meta-charset-UTF-8-meta-name-viewport-content-<!DOCTYPE html>
<html lang="da">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>BeamNG Computerpoliti — Stor DB</title>
<style>
  *{box-sizing:border-box;margin:0;padding:0;font-family:system-ui,-apple-system,Segoe UI,Roboto,sans-serif}
  body{background:#071020;color:#e6eef6;display:flex;align-items:center;justify-content:center;min-height:100vh;padding:12px}
  .app{width:100%;max-width:480px;background:#032036;border-radius:14px;border:1px solid #123344;overflow:hidden;box-shadow:0 8px 30px rgba(0,0,0,.6)}
  .bar{display:flex;justify-content:space-between;padding:10px 14px;font-size:13px;color:#9fc7df;background:linear-gradient(#022233,#021a27)}
  .header{display:flex;justify-content:space-between;align-items:center;padding:12px 14px;border-bottom:1px solid rgba(255,255,255,.03)}
  .logo{display:flex;align-items:center;gap:10px}
  .logo-icon{width:44px;height:44;border-radius:10px;background:#0b5f9d;display:flex;align-items:center;justify-content:center;font-weight:700}
  .logo-text{font-size:14px}
  .main{padding:12px;display:flex;flex-direction:column;gap:10px}
  .tabs{display:flex;gap:8px}
  .tab{flex:1;padding:8px;border-radius:999px;background:#022b3a;text-align:center;color:#95c3d9;font-weight:600;cursor:pointer}
  .tab.active{background:#0ea5e9;color:#022;box-shadow:0 6px 18px rgba(14,165,233,.12)}
  .search-row{display:flex;gap:8px}
  input[type="search"]{flex:1;padding:10px;border-radius:999px;border:1px solid rgba(255,255,255,.04);background:#02121a;color:#e6eef6}
  button{padding:9px 12px;border-radius:999px;border:none;background:#16a34a;color:white;font-weight:700;cursor:pointer}
  .result{margin-top:6px;background:#021a26;border-radius:12px;padding:10px;border:1px solid rgba(255,255,255,.03);display:none;flex-direction:column;gap:8px}
  .title-row{display:flex;justify-content:space-between;align-items:center}
  .title{font-weight:800}
  .badge{padding:5px 8px;border-radius:999px;font-size:12px}
  .ok{background:#0a5f2a;color:#dfffe0}
  .warn{background:#7f1d1d;color:#ffe0e0}
  .grid{display:grid;grid-template-columns:1fr 1fr;gap:8px}
  .field{font-size:13px;color:#bfe0f0}
  .notes{font-size:13px;color:#9fbecf;border-top:1px dashed rgba(255,255,255,.03);padding-top:8px}
  .footer{padding:10px 12px;font-size:12px;color:#7ea8bd;display:flex;justify-content:space-between}
  .bigsearch-hint{font-size:12px;color:#7ea8bd}
  .import-area{margin-top:10px;background:#011218;padding:8px;border-radius:8px;border:1px dashed rgba(255,255,255,.02);font-size:12px;color:#7ea8bd}
</style>
</head>
<body>
  <div class="app" role="application" aria-label="BeamNG Computerpoliti">
    <div class="bar">BeamNG · COMPUTERPOLITI <span id="signal">Signal: ★★★★☆</span></div>
    <div class="header">
      <div class="logo">
        <div class="logo-icon">POL</div>
        <div class="logo-text">COMPUTERPOLITI — Stor DB</div>
      </div>
      <div class="hdr-right">
        <div><strong>ENHED</strong> P-21</div>
        <div style="font-size:12px;color:#8fbfdb">Scene: Free Roam</div>
      </div>
    </div>

    <div class="main">
      <div class="tabs">
        <div class="tab active" data-tab="plate">Søg nr.plade</div>
        <div class="tab" data-tab="model">Søg model</div>
        <div class="tab" data-tab="list">Vis liste</div>
      </div>

      <div class="search-controls">
        <div class="search-row">
          <input id="searchInput" type="search" placeholder="Indtast nummerplade eller model (fx POL-01 eller ETK 800)" />
          <button id="searchBtn">SLÅ OP</button>
        </div>
        <div class="bigsearch-hint">Du kan søge delvist: fx "ETK" eller "POL-"</div>
      </div>

      <div class="result" id="resultCard" aria-live="polite">
        <div class="title-row">
          <div class="title" id="resultTitle">Resultat</div>
          <div id="resultTag" class="badge ok">OK</div>
        </div>
        <div class="grid" id="resultGrid"></div>
        <div class="notes" id="resultNotes"></div>
      </div>

      <div class="import-area" id="importArea">
        <strong>Tilføj / rediger database</strong>
        <div style="margin-top:6px">
          Du kan direkte ændre `fakeDB` i HTML-filen for at tilføje alle dine biler/numre. Vil du have, kan jeg lave en import-funktion til CSV næste.
        </div>
      </div>

      <div class="footer">
        <div>BRUGER: <strong>Betjent #492</strong></div>
        <div>SYSTEM: BNG-CP v2.0</div>
      </div>
    </div>
  </div>

<script>
/*
  Stor, udvidelig fakeDB med mange eksempler.
  Struktur per entry:
  key: søgenøgle (typisk nummerplade i uppercase eller model lowercase)
  value: {
    type: "vehicle",
    plate: "POL-01",
    model: "Sunburst Police",
    make: "Hirochi",
    color: "Hvid/blå",
    owner: "BeamNG Politi / Civili",
    status: "service|ok|wanted|impounded",
    tags: ["patrulje","mistænkt"],
    notes: "Tekst..."
  }
*/
const fakeDB = {
  // Patruljevogne / tjeneste
  "POL-01": {type:"vehicle", plate:"POL-01", model:"Sunburst Police", make:"Hirochi", color:"Hvid/blå", owner:"BeamNG Politi", status:"service", tags:["patrulje"], notes:"Standard patruljebil. Udstyr: sirene, blålys, radio."},
  "POL-02": {type:"vehicle", plate:"POL-02", model:"Gavril Cruiser", make:"Gavril", color:"Sort/hvid", owner:"BeamNG Politi", status:"service", tags:["pursuit"], notes:"Hurtig patrulje — velegnet til jagt."},

  // Almindelige civile
  "BNG-101": {type:"vehicle", plate:"BNG-101", model:"ETK 800", make:"ETK", color:"Sort", owner:"Lokal borger", status:"ok", tags:["civil"], notes:"Daglig commuter."},
  "BNG-102": {type:"vehicle", plate:"BNG-102", model:"ETK 200", make:"ETK", color:"Sølv", owner:"Firma A", status:"ok", tags:["civil"], notes:"Firma bil."},
  "CIV-22": {type:"vehicle", plate:"CIV-22", model:"Gavril D-Series", make:"Gavril", color:"Hvid", owner:"Ukendt", status:"wanted", tags:["mistænkt","flugt"], notes:"Forbundet med flugtforsøg — høj fart observeret."},
  "CIV-33": {type:"vehicle", plate:"CIV-33", model:"Ibishu Pigeon", make:"Ibishu", color:"Rød", owner:"Jonas", status:"ok", tags:["civil"], notes:"Let kørsel i byen."},
  "CIV-44": {type:"vehicle", plate:"CIV-44", model:"Ibishu Covet", make:"Ibishu", color:"Hvid", owner:"Anna", status:"ok", tags:["civil"], notes:"Ingen kendte sager."},

  // Sports / performance
  "SPT-001": {type:"vehicle", plate:"SPT-001", model:"SBR4 GT", make:"Hirochi", color:"Gul", owner:"RacerX", status:"ok", tags:["racer"], notes:"Modificeret til bane."},
  "SPT-002": {type:"vehicle", plate:"SPT-002", model:"Gavril Truckster", make:"Gavril", color:"Grøn", owner:"Transport ApS", status:"ok", tags:["heavy"], notes:"Lastbil, 2 aksler."},

  // Klassiker / offroad
  "OFF-10": {type:"vehicle", plate:"OFF-10", model:"Gavril Roamer", make:"Gavril", color:"Brun", owner:"Eventyr ApS", status:"ok", tags:["offroad"], notes:"God offroad kapabilitet."},
  "OFF-11": {type:"vehicle", plate:"OFF-11", model:"Gavril D-Series 4x4", make:"Gavril", color:"Khaki", owner:"Forsyning", status:"ok", tags:["offroad","4x4"], notes:"Service køretøj."},

  // Mistænkte / høj risiko
  "WNT-77": {type:"vehicle", plate:"WNT-77", model:"ETK K-Series", make:"ETK", color:"Mat sort", owner:"Ukendt", status:"wanted", tags:["mistænkt"], notes:"Mistænkt for væbnet flugt. OBS: bevæbnet fører mulig."},
  "WNT-88": {type:"vehicle", plate:"WNT-88", model:"Hirochi Sunburst", make:"Hirochi", color:"Blå", owner:"Ukendt", status:"wanted", tags:["mistænkt","farlig"], notes:"Flere flugtforsøg — kontakt backup."},

  // Flere eksempler på populære BeamNG-modeller (du kan supplere med flere)
  "ETK-800": {type:"vehicle", plate:"ETK-800", model:"ETK 800", make:"ETK", color:"Hvid", owner:"Borger", status:"ok", tags:["civil"], notes:"Kompakt familiebil."},
  "ETK-230": {type:"vehicle", plate:"ETK-230", model:"ETK 230", make:"ETK", color:"Rød", owner:"Borger", status:"ok", tags:["civil"], notes:"Sportsudgave."},
  "HIRO-SBR4": {type:"vehicle", plate:"HIRO-SBR4", model:"SBR4", make:"Hirochi", color:"Sort", owner:"Racer", status:"ok", tags:["racer"], notes:"Performance coupé."},
  "IBI-COVET": {type:"vehicle", plate:"IBI-COVET", model:"Covet", make:"Ibishu", color:"Hvid", owner:"Mads", status:"ok", tags:["civil"], notes:"Lille bybil."},
  "GAV-D": {type:"vehicle", plate:"GAV-D", model:"D-Series", make:"Gavril", color:"Hvid", owner:"Transport", status:"ok", tags:["transport"], notes:"Let arbejdsvogn."},

  // Fiktive/rollespil entries - du kan ændre
  "RPG-900": {type:"vehicle", plate:"RPG-900", model:"Custom Hotrod", make:"ModShop", color:"Flamme", owner:"Rolle1", status:"ok", tags:["custom"], notes:"Showcar."},
  "RPG-911": {type:"vehicle", plate:"RPG-911", model:"Armoured Van", make:"Gavril", color:"Sort", owner:"Security Ltd", status:"impounded", tags:["sikkerhed"], notes:"Henlagt i kommuneopbevaring."},

  // Ekstra "mock" entries for demonstration
  "BNG-200": {type:"vehicle", plate:"BNG-200", model:"Sunburst Coupe", make:"Hirochi", color:"Sølv", owner:"Civ", status:"ok", tags:["civil"], notes:"Ingen aktive sager."},
  "BNG-201": {type:"vehicle", plate:"BNG-201", model:"ETK 4x4", make:"ETK", color:"Grå", owner:"Civ", status:"ok", tags:["4x4"], notes:"Offroad-udgave."},
  "POL-RES": {type:"vehicle", plate:"POL-RES", model:"Reserve Van", make:"Gavril", color:"Hvid", owner:"Politi Depot", status:"service", tags:["patrulje"], notes:"Reserve enhed."},

  // Pladsholder — tilføj dine egne under
  // "DIT-PLATE": {type:"vehicle", plate:"DIT-PLATE", model:"Model navn", make:"Fabrikant", color:"Farve", owner:"Ejer", status:"ok", tags:["tag1"], notes:"Din note"}
};

// Utility: indeksér også på modelnavn (lowercase) for hurtig søgning
function searchDB(query, mode){
  const q = query.trim().toLowerCase();
  if(!q) return null;
  const results = [];
  for(const key in fakeDB){
    const v = fakeDB[key];
    if(mode === 'plate'){
      if(v.plate && v.plate.toLowerCase().includes(q)) results.push(v);
    } else if(mode === 'model'){
      if((v.model||'').toLowerCase().includes(q) || (v.make||'').toLowerCase().includes(q)) results.push(v);
    } else { // generel del-søgning
      if((v.plate||'').toLowerCase().includes(q) || (v.model||'').toLowerCase().includes(q) || (v.make||'').toLowerCase().includes(q) || (v.owner||'').toLowerCase().includes(q)) results.push(v);
    }
  }
  return results.length ? results : null;
}

const tabs = document.querySelectorAll('.tab');
const input = document.getElementById('searchInput');
const btn = document.getElementById('searchBtn');
const resCard = document.getElementById('resultCard');
const resTitle = document.getElementById('resultTitle');
const resTag = document.getElementById('resultTag');
const resGrid = document.getElementById('resultGrid');
const resNotes = document.getElementById('resultNotes');

let activeTab = 'plate';
tabs.forEach(t => {
  t.addEventListener('click', () => {
    tabs.forEach(x=>x.classList.remove('active'));
    t.classList.add('active');
    activeTab = t.dataset.tab;
    input.placeholder = activeTab==='plate' ? 'Søg nummerplade (fx POL-01)' : activeTab==='model' ? 'Søg model eller fabrikant (fx ETK eller SBR4)' : 'Leave empty and try "Vis liste"';
    resCard.style.display='none';
    input.value='';
  });
});

function renderList(results){
  resGrid.innerHTML='';
  resNotes.textContent='';
  if(!results || results.length===0){
    resTitle.textContent='Ingen fund';
    resTag.textContent='INTET FUND';
    resTag.className='badge ok';
    resGrid.innerHTML='<div class="field">Ingen poster matcher din søgning.</div>';
    resCard.style.display='flex';
    return;
  }
  if(results.length===1){
    renderSingle(results[0]);
    return;
  }
  resTitle.textContent = `${results.length} resultater`;
  resTag.textContent = 'LISTE';
  resTag.className = 'badge ok';
  resGrid.innerHTML = '';
  results.forEach(v=>{
    const el = document.createElement('div');
    el.className='field';
    el.innerHTML = `<strong>${v.plate}</strong><br><small>${v.model} — ${v.make} — ${v.color}</small>`;
    el.style.cursor='pointer';
    el.style.padding='6px';
    el.style.borderBottom='1px solid rgba(255,255,255,.02)';
    el.addEventListener('click', ()=> renderSingle(v));
    resGrid.appendChild(el);
  });
  resNotes.textContent = 'Tryk på en række for detaljer.';
  resCard.style.display='flex';
}

function renderSingle(v){
  resGrid.innerHTML='';
  resNotes.textContent='';
  resTitle.textContent = (v.plate ? v.plate + ' · ' : '') + (v.model||'Ukendt model');
  resTag.className = 'badge ' + (v.status==='wanted' ? 'warn' : 'ok');
  resTag.textContent = v.status ? v.status.toUpperCase() : 'OK';
  const rows = [
    {label:'Nummerplade', value:v.plate||'–'},
    {label:'Model', value:v.model||'–'},
    {label:'Mærke', value:v.make||'–'},
    {label:'Farve', value:v.color||'–'},
    {label:'Ejer', value:v.owner||'–'},
    {label:'Tags', value:(v.tags||[]).join(', ')||'–'}
  ];
  rows.forEach(r=>{
    const d = document.createElement('div');
    d.className='field';
    d.innerHTML = `<span style="font-size:11px;color:#7fb6cf">${r.label}</span><br><span style="font-size:14px">${r.value}</span>`;
    resGrid.appendChild(d);
  });
  resNotes.textContent = v.notes||'Ingen noter.';
  resCard.style.display='flex';
}

// Search handler
btn.addEventListener('click', ()=>{
  const q = input.value.trim();
  if(activeTab==='list'){
    // Vis alle som liste (eller filter hvis q angivet)
    const all = Object.values(fakeDB);
    const filtered = q ? all.filter(v=> (v.plate||'').toLowerCase().includes(q.toLowerCase()) || (v.model||'').toLowerCase().includes(q.toLowerCase())) : all;
    renderList(filtered);
    return;
  }
  if(!q){
    renderList(null);
    return;
  }
  const mode = activeTab==='plate' ? 'plate' : activeTab==='model' ? 'model' : 'any';
  const results = searchDB(q, mode);
  if(!results){
    renderList(null);
  } else {
    renderList(results);
  }
});

// Enter key support
input.addEventListener('keydown', (e)=>{
  if(e.key === 'Enter') btn.click();
});

// initial
input.placeholder = 'Søg nummerplade (fx POL-01)';
</script>
</body>
</html>
