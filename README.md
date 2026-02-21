
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="theme-color" content="#2196F3">
<meta name="apple-mobile-web-app-capable" content="yes">
<title>HELIXBAND Monitor</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
<style>
/* ================================================================
   HELIXBAND v2.0 — Todo inline, sin dependencias locales
   ================================================================ */
:root{
  --primary:#2196F3;--primary-dark:#1565C0;
  --danger:#F44336;--warning:#FF9800;--success:#4CAF50;
  --pulse:#E91E63;--temp:#4CAF50;--gsr:#00BCD4;--motion:#9C27B0;--spike:#FF5722;
  --bg:#F0F4F8;--bg-card:#FFFFFF;
  --text:#1A2332;--text2:#6B7280;--border:#E2E8F0;
  --shadow:0 2px 12px rgba(0,0,0,.08);--shadow-lg:0 6px 24px rgba(0,0,0,.14);
  --radius:16px;
}
body.dark{--bg:#0F1117;--bg-card:#1E2230;--text:#F1F5F9;--text2:#94A3B8;--border:#2D3748;}
*,*::before,*::after{margin:0;padding:0;box-sizing:border-box;}
body{font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,sans-serif;
  background:var(--bg);color:var(--text);min-height:100vh;
  transition:background .3s,color .3s;overflow-x:hidden;}

/* LOADING */
#LS{position:fixed;inset:0;background:linear-gradient(135deg,#667eea,#764ba2);
  display:flex;align-items:center;justify-content:center;
  flex-direction:column;gap:16px;color:#fff;text-align:center;z-index:9999;}
#LS h1{font-size:36px;font-weight:800;letter-spacing:2px;}
#LS p{font-size:16px;opacity:.85;}
.ha{font-size:72px;animation:hb 1.2s ease-in-out infinite;}
@keyframes hb{0%,100%{transform:scale(1)}20%{transform:scale(1.15)}40%{transform:scale(.95)}60%{transform:scale(1.1)}}
.fo{animation:fadeOut .6s forwards;}
@keyframes fadeOut{to{opacity:0;visibility:hidden;pointer-events:none}}

/* LAYOUT */
#app{max-width:680px;margin:0 auto;min-height:100vh;}

/* HEADER */
.hdr{background:linear-gradient(135deg,#2196F3,#1565C0);color:#fff;
  padding:18px 20px 14px;position:sticky;top:0;z-index:100;
  box-shadow:0 4px 20px rgba(33,150,243,.35);}
.hdr-top{display:flex;align-items:center;justify-content:space-between;margin-bottom:12px;}
.hlogo{display:flex;align-items:center;gap:10px;}
.hlogo span{font-size:28px;}.hlogo h1{font-size:22px;font-weight:800;letter-spacing:1px;}
.hbtns{display:flex;gap:8px;}
.ib{width:40px;height:40px;border-radius:50%;border:none;
  background:rgba(255,255,255,.2);color:#fff;font-size:18px;cursor:pointer;
  display:flex;align-items:center;justify-content:center;transition:background .2s,transform .15s;}
.ib:hover{background:rgba(255,255,255,.35);transform:scale(1.08);}
.ib:active{transform:scale(.94);}
.cbar{display:flex;align-items:center;gap:8px;
  background:rgba(255,255,255,.15);border-radius:20px;
  padding:7px 14px;font-size:14px;font-weight:500;backdrop-filter:blur(10px);}
.cdot{width:9px;height:9px;border-radius:50%;background:#F44336;animation:blink 2s infinite;}
.cbar.on .cdot{background:#4CAF50;}
@keyframes blink{0%,100%{opacity:1}50%{opacity:.4}}
.ctm{margin-left:auto;font-size:12px;opacity:.8;}

/* CONNECT */
#CS{display:flex;align-items:center;justify-content:center;
  min-height:calc(100vh - 140px);padding:24px;}
.cc{background:var(--bg-card);border-radius:var(--radius);padding:40px 32px;
  text-align:center;box-shadow:var(--shadow-lg);max-width:420px;width:100%;
  animation:slideUp .4s ease;}
@keyframes slideUp{from{opacity:0;transform:translateY(30px)}to{opacity:1;transform:none}}
.cci{font-size:72px;animation:flt 3s ease-in-out infinite;margin-bottom:20px;}
@keyframes flt{0%,100%{transform:translateY(0)}50%{transform:translateY(-10px)}}
.cc h2{font-size:24px;font-weight:700;margin-bottom:8px;}
.cc p{color:var(--text2);margin-bottom:24px;font-size:15px;line-height:1.5;}
.cnote{font-size:12px;color:var(--text2);margin-top:16px;line-height:1.6;}
.cnote strong{color:var(--primary);}

/* BUTTONS */
.btn{display:inline-flex;align-items:center;justify-content:center;gap:8px;
  padding:12px 24px;border-radius:12px;border:none;font-size:16px;font-weight:600;
  cursor:pointer;transition:all .2s;}
.btn:active{transform:scale(.97);}
.btn:disabled{opacity:.5;cursor:not-allowed;}
.btn-p{background:var(--primary);color:#fff;width:100%;padding:16px;font-size:17px;}
.btn-p:hover{background:var(--primary-dark);box-shadow:0 4px 16px rgba(33,150,243,.4);transform:translateY(-1px);}
.btn-s{background:var(--bg);color:var(--text);border:1px solid var(--border);}
.btn-s:hover{background:var(--border);}
.btn-d{background:var(--danger);color:#fff;}
.btn-d:hover{background:#C62828;}

/* SPINNER */
.spin{width:18px;height:18px;border-radius:50%;border:3px solid var(--border);
  border-top-color:var(--primary);animation:sp 1s linear infinite;flex-shrink:0;}
@keyframes sp{to{transform:rotate(360deg)}}
.sscan{display:flex;align-items:center;justify-content:center;gap:10px;
  margin-top:14px;color:var(--text2);font-size:14px;}

/* DATA PANEL */
#DP{padding:16px;animation:fIn .4s ease;}
@keyframes fIn{from{opacity:0;transform:translateY(16px)}to{opacity:1;transform:none}}

/* ALERTS */
#AB{margin-bottom:12px;}
.al{display:flex;align-items:center;gap:12px;padding:12px 16px;border-radius:12px;
  margin-bottom:8px;animation:sIn .3s ease;font-size:14px;font-weight:500;}
@keyframes sIn{from{transform:translateX(-20px);opacity:0}to{opacity:1;transform:none}}
.al-w{background:#FFF3E0;color:#E65100;border-left:4px solid var(--warning);}
.al-d{background:#FFEBEE;color:#C62828;border-left:4px solid var(--danger);}
.al-s{background:#FBE9E7;color:#BF360C;border-left:4px solid var(--spike);}
body.dark .al-w{background:#3E2600;color:#FFB74D;}
body.dark .al-d{background:#3E0000;color:#EF9A9A;}
body.dark .al-s{background:#3E1A00;color:#FF8A65;}

/* CALIB */
.cab{display:flex;align-items:center;gap:10px;background:#FFF8E1;
  border:1px solid #FFD54F;border-radius:12px;padding:12px 16px;
  margin-bottom:12px;font-size:14px;color:#5D4037;}
body.dark .cab{background:#3E3000;border-color:#a07800;color:#FFD54F;}
.cab .spin{border-top-color:var(--warning);}

/* DEBUG */
.dbgw{background:var(--bg-card);border-radius:var(--radius);
  border-left:4px solid #607D8B;box-shadow:var(--shadow);margin-bottom:12px;overflow:hidden;}
.dbgh{display:flex;align-items:center;justify-content:space-between;
  padding:12px 16px;cursor:pointer;user-select:none;font-weight:600;font-size:14px;}
.dbgh:hover{background:rgba(96,125,139,.07);}
.dch{transition:transform .3s;color:var(--text2);font-size:12px;}
.dch.open{transform:rotate(180deg);}
.dbgb{display:none;padding:0 16px 14px;}
.dbgb.open{display:block;}
.dbgr{font-family:'Consolas','Fira Code',monospace;font-size:13px;
  background:#1e1e2e;color:#a6e3a1;border-radius:8px;
  padding:10px 12px;word-break:break-all;margin-bottom:10px;min-height:40px;}
.dbgg{display:grid;grid-template-columns:repeat(auto-fill,minmax(100px,1fr));gap:6px;margin-bottom:8px;}
.df{background:var(--bg);border-radius:8px;padding:6px 10px;font-size:12px;}
.dfl{color:var(--text2);display:block;margin-bottom:2px;font-size:11px;}
.dfv{font-weight:700;font-family:monospace;color:var(--text);}
.dbgc{font-size:12px;color:var(--text2);text-align:right;}

/* METRIC CARDS */
.mc{background:var(--bg-card);border-radius:var(--radius);padding:20px;
  margin-bottom:14px;box-shadow:var(--shadow);border-left:5px solid transparent;
  transition:box-shadow .2s,transform .2s;position:relative;overflow:hidden;}
.mc:hover{box-shadow:var(--shadow-lg);transform:translateY(-2px);}
.mc-p{border-left-color:var(--pulse);}
.mc-t{border-left-color:var(--temp);}
.mc-g{border-left-color:var(--gsr);}
.mc-spike{animation:spkF .6s ease 3;}
@keyframes spkF{0%,100%{box-shadow:var(--shadow)}50%{box-shadow:0 0 0 3px var(--spike),var(--shadow-lg)}}
.mch{display:flex;align-items:center;justify-content:space-between;margin-bottom:16px;}
.mct{display:flex;align-items:center;gap:10px;}
.mci{font-size:26px;}
.mct h3{font-size:16px;font-weight:700;}
.badge{padding:4px 11px;border-radius:20px;font-size:11px;font-weight:700;
  text-transform:uppercase;letter-spacing:.5px;}
.b-n{background:#E8F5E9;color:#2E7D32;}
.b-w{background:#FFF3E0;color:#E65100;}
.b-d{background:#FFEBEE;color:#C62828;}
.b-s{background:#FBE9E7;color:#BF360C;animation:bpls 1s infinite;}
.b-na{background:#ECEFF1;color:#546E7A;}
body.dark .b-n{background:#1B5E20;color:#A5D6A7;}
body.dark .b-w{background:#4E2700;color:#FFB74D;}
body.dark .b-d{background:#4E0000;color:#EF9A9A;}
body.dark .b-na{background:#263238;color:#90A4AE;}
@keyframes bpls{0%,100%{opacity:1}50%{opacity:.6}}
.mv{display:flex;align-items:baseline;gap:8px;margin-bottom:6px;}
.vb{font-size:52px;font-weight:800;line-height:1;color:var(--text);transition:color .3s;}
.vu{font-size:20px;color:var(--text2);font-weight:500;}
.vd{font-size:13px;color:var(--text2);margin-bottom:12px;}
.vd b{font-weight:700;}
.up{color:var(--danger);}.dn{color:var(--success);}.nt{color:var(--text2);}.sa{color:var(--spike);font-weight:900;}
.ss{display:flex;align-items:center;gap:6px;font-size:13px;color:var(--text2);margin-bottom:10px;}
.sdok{font-size:8px;color:var(--success);}.sderr{font-size:8px;color:var(--danger);}

/* GSR bar */
.gt{height:10px;background:var(--bg);border-radius:5px;overflow:hidden;margin-bottom:14px;}
.gf{height:100%;border-radius:5px;width:0%;
  background:linear-gradient(90deg,#00BCD4,#FF9800 55%,#F44336 100%);transition:width .6s ease;}

/* chart */
.cw{margin-top:12px;height:130px;position:relative;}

/* 3D MOTION */
.m3w{background:var(--bg-card);border-radius:var(--radius);padding:20px;
  margin-bottom:14px;box-shadow:var(--shadow);border-left:5px solid var(--motion);}
.m3h{display:flex;align-items:center;justify-content:space-between;margin-bottom:14px;}
.m3t{display:flex;align-items:center;gap:10px;}
.m3t h3{font-size:16px;font-weight:700;}
#tc{width:100%!important;height:300px!important;border-radius:12px;
  background:radial-gradient(ellipse at center,#1a1a2e,#0d0d1a);display:block;}
.axr{display:grid;grid-template-columns:repeat(3,1fr);gap:8px;margin-top:12px;}
.axb{background:var(--bg);border-radius:10px;padding:10px;text-align:center;}
.axl{display:block;font-size:11px;font-weight:700;margin-bottom:3px;text-transform:uppercase;letter-spacing:.8px;}
.axv{font-size:18px;font-weight:800;font-family:monospace;}
.ax-x{color:#FF6B6B;}.ax-y{color:#51CF66;}.ax-z{color:#74C0FC;}
.msr{display:flex;align-items:center;gap:8px;margin-top:10px;font-size:13px;color:var(--text2);}
.msb{padding:4px 12px;border-radius:20px;font-size:12px;font-weight:700;}
.ms-q{background:#E8F5E9;color:#2E7D32;}
.ms-m{background:#FFF3E0;color:#E65100;animation:bpls .8s infinite;}
body.dark .ms-q{background:#1B5E20;color:#A5D6A7;}
body.dark .ms-m{background:#4E2700;color:#FFB74D;}

/* INFO BOX */
.infobox{background:linear-gradient(135deg,rgba(33,150,243,.08),rgba(33,150,243,.03));
  border:1px solid rgba(33,150,243,.2);border-radius:12px;
  padding:10px 14px;margin-top:10px;font-size:12px;color:var(--text2);line-height:1.6;}
.infobox strong{color:var(--primary);}

/* ACTIONS */
.abtns{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;margin:16px 0;}
@media(max-width:480px){.abtns{grid-template-columns:1fr;}}

/* SESSION STATS */
.ss-grid{background:var(--bg-card);border-radius:var(--radius);padding:16px;
  display:grid;grid-template-columns:repeat(3,1fr);gap:12px;
  margin-bottom:16px;box-shadow:var(--shadow);text-align:center;}
@media(max-width:480px){.ss-grid{grid-template-columns:1fr;}}
.slbl{display:block;font-size:11px;color:var(--text2);margin-bottom:4px;
  font-weight:600;text-transform:uppercase;letter-spacing:.5px;}
.sval{font-size:20px;font-weight:700;color:var(--text);}

/* FOOTER */
.ftr{text-align:center;padding:20px;color:var(--text2);font-size:13px;line-height:1.8;}

/* MODALS */
.mo{position:fixed;inset:0;background:rgba(0,0,0,.55);display:flex;
  align-items:center;justify-content:center;z-index:500;padding:20px;backdrop-filter:blur(4px);}
.mb{background:var(--bg-card);border-radius:var(--radius);max-width:520px;width:100%;
  max-height:90vh;overflow-y:auto;box-shadow:0 20px 60px rgba(0,0,0,.3);animation:slideUp .3s ease;}
.mhd{display:flex;align-items:center;justify-content:space-between;
  padding:20px;border-bottom:1px solid var(--border);}
.mhd h2{font-size:20px;font-weight:700;}
.mcl{background:none;border:none;font-size:28px;cursor:pointer;color:var(--text2);line-height:1;width:32px;height:32px;}
.mbd{padding:20px;}.mft{padding:16px 20px;border-top:1px solid var(--border);display:flex;gap:10px;justify-content:flex-end;}
.sr{display:flex;align-items:center;justify-content:space-between;
  padding:13px 0;border-bottom:1px solid var(--border);font-size:15px;}
.sr:last-child{border-bottom:none;}
.sr input[type=number]{width:80px;padding:6px 10px;border-radius:8px;
  border:1px solid var(--border);background:var(--bg);color:var(--text);font-size:15px;outline:none;}
.sr input[type=number]:focus{border-color:var(--primary);}
.tgl{position:relative;width:44px;height:24px;}
.tgl input{opacity:0;width:0;height:0;}
.tsl{position:absolute;inset:0;background:#ccc;border-radius:24px;cursor:pointer;transition:.3s;}
.tsl::before{content:'';position:absolute;height:18px;width:18px;left:3px;bottom:3px;
  background:#fff;border-radius:50%;transition:.3s;}
.tgl input:checked+.tsl{background:var(--primary);}
.tgl input:checked+.tsl::before{transform:translateX(20px);}

/* TOAST */
#toast{position:fixed;bottom:24px;left:50%;transform:translateX(-50%) translateY(80px);
  background:#1A2332;color:#fff;padding:12px 24px;border-radius:12px;
  font-size:14px;font-weight:500;z-index:9000;opacity:0;
  transition:all .3s;pointer-events:none;white-space:nowrap;
  box-shadow:0 8px 32px rgba(0,0,0,.3);}
#toast.show{opacity:1;transform:translateX(-50%) translateY(0);}
.hidden{display:none!important;}
</style>
</head>
<body>

<!-- LOADING -->
<div id="LS">
  <div class="ha">💓</div>
  <h1>HELIXBAND</h1>
  <p>Inicializando sistema biométrico…</p>
</div>

<div id="app" class="hidden">
  <!-- HEADER -->
  <header class="hdr">
    <div class="hdr-top">
      <div class="hlogo"><span>🫀</span><h1>HELIXBAND</h1></div>
      <div class="hbtns">
        <button class="ib" id="themeBtn" title="Tema">🌙</button>
        <button class="ib" id="settingsBtn" title="Config">⚙️</button>
      </div>
    </div>
    <div class="cbar" id="cbar">
      <div class="cdot" id="cdot"></div>
      <span id="ctxt">Desconectado</span>
      <span class="ctm" id="ctm"></span>
    </div>
  </header>

  <!-- CONNECT -->
  <section id="CS">
    <div class="cc">
      <div class="cci">📡</div>
      <h2>Conectar Dispositivo</h2>
      <p>Asegúrate de que tu HELIXBAND esté encendido y en modo BLE</p>
      <button class="btn btn-p" id="connectBtn">🔍 Buscar HELIXBAND</button>
      <div class="sscan hidden" id="scanSt">
        <div class="spin"></div><span>Buscando…</span>
      </div>
      <p class="cnote">⚠️ Requiere <strong>Google Chrome</strong> en Android o escritorio.<br>
        En Android activa el permiso de ubicación para BLE.</p>
    </div>
  </section>

  <!-- DATA -->
  <main id="DP" class="hidden">
    <div id="AB"></div>

    <!-- CALIBRACIÓN -->
    <div class="cab" id="calib">
      <div class="spin"></div>
      <span>⏳ El Arduino realiza 3 min de estabilización antes de enviar datos calibrados.</span>
    </div>

    <!-- DEBUG -->
    <div class="dbgw">
      <div class="dbgh" onclick="tgDbg()">
        <span>🛠️ Datos en bruto (debug)</span>
        <span class="dch" id="dbgCh">▼</span>
      </div>
      <div class="dbgb" id="dbgB">
        <div class="dbgr" id="dbgR">Esperando datos del Arduino…</div>
        <div class="dbgg" id="dbgG"></div>
        <div class="dbgc" id="dbgC">Muestras: 0</div>
      </div>
    </div>

    <!-- CARD: BPM -->
    <div class="mc mc-p" id="cardP">
      <div class="mch">
        <div class="mct"><span class="mci">💓</span><h3>Frecuencia Cardíaca</h3></div>
        <span class="badge b-na" id="badgeP">--</span>
      </div>
      <div class="mv"><span class="vb" id="valP">--</span><span class="vu">BPM</span></div>
      <div class="vd" id="dltP">vs baseline: <b class="nt">--</b></div>
      <div class="cw"><canvas id="chP"></canvas></div>
    </div>

    <!-- CARD: TEMPERATURA -->
    <div class="mc mc-t" id="cardT">
      <div class="mch">
        <div class="mct"><span class="mci">🌡️</span><h3>Temperatura — MPU6050</h3></div>
        <span class="badge b-na" id="badgeT">--</span>
      </div>
      <div class="mv"><span class="vb" id="valT">--</span><span class="vu">°C</span></div>
      <div class="vd" id="dltT">vs baseline: <b class="nt">--</b></div>
      <div class="ss" id="stT"><span class="sdok">●</span><span>Esperando…</span></div>
      <div class="infobox">
        💡 <strong>MPU6050</strong> mide temperatura interna del chip.<br>
        Rango esperado: <strong>34–42 °C</strong> (se eleva con uso). La fórmula usada es<br>
        <code>Temp_raw / 340.0 + 36.53</code> según datasheet del MPU6050.
      </div>
      <div class="cw"><canvas id="chT"></canvas></div>
    </div>

    <!-- CARD: GSR SUDORACIÓN -->
    <div class="mc mc-g" id="cardG">
      <div class="mch">
        <div class="mct"><span class="mci">💧</span><h3>Sudoración — GSR</h3></div>
        <span class="badge b-na" id="badgeG">--</span>
      </div>
      <div class="mv"><span class="vb" id="valG">--</span><span class="vu">%</span></div>
      <div class="vd" id="dltG">vs baseline: <b class="nt">--</b></div>
      <div class="gt"><div class="gf" id="gfill"></div></div>
      <div class="cw"><canvas id="chG"></canvas></div>
    </div>

    <!-- CARD: MOVIMIENTO 3D -->
    <div class="m3w" id="card3d">
      <div class="m3h">
        <div class="m3t"><span class="mci">🏃</span><h3>Movimiento 3D — MPU6050</h3></div>
        <span class="badge b-na" id="badgeM">--</span>
      </div>
      <canvas id="tc"></canvas>
      <div class="axr">
        <div class="axb"><span class="axl ax-x">Eje X</span><span class="axv ax-x" id="axX">--</span><span style="font-size:11px;color:var(--text2)"> g</span></div>
        <div class="axb"><span class="axl ax-y">Eje Y</span><span class="axv ax-y" id="axY">--</span><span style="font-size:11px;color:var(--text2)"> g</span></div>
        <div class="axb"><span class="axl ax-z">Eje Z</span><span class="axv ax-z" id="axZ">--</span><span style="font-size:11px;color:var(--text2)"> g</span></div>
      </div>
      <div class="msr">
        <span class="sdok">●</span><span>MPU6050 vía ESP32</span>
        <span class="msb ms-q" id="mst">🧍 Quieto</span>
      </div>
      <div class="infobox">
        💡 El humanoide refleja la <strong>orientación real</strong> del acelerómetro.<br>
        Para ver X/Y/Z necesitas actualizar el Arduino al <strong>formato v2</strong>:<br>
        <code>GSR,BPM,TEMP,MOV,AcX,AcY,AcZ</code> — ver instrucciones abajo.
      </div>
    </div>

    <!-- ACCIONES -->
    <div class="abtns">
      <button class="btn btn-s" id="expBtn">📊 Exportar Excel</button>
      <button class="btn btn-s" id="histBtn">📋 Historial</button>
      <button class="btn btn-d" id="discBtn">🔌 Desconectar</button>
    </div>

    <!-- SESSION -->
    <div class="ss-grid">
      <div><span class="slbl">Duración</span><span class="sval" id="dur">00:00</span></div>
      <div><span class="slbl">Muestras</span><span class="sval" id="samp">0</span></div>
      <div><span class="slbl">Actualización</span><span class="sval" id="upd">--</span></div>
    </div>
  </main>

  <footer class="ftr">
    HELIXBAND v2.0 · Monitor Biométrico BLE<br>
    <span style="font-size:12px">ESP32 · MAX30102 · MPU6050 (Accel+Temp) · GSR</span>
  </footer>
</div>

<!-- MODAL SETTINGS -->
<div class="mo hidden" id="setMod">
  <div class="mb">
    <div class="mhd"><h2>⚙️ Configuración</h2><button class="mcl" onclick="cMod('setMod')">×</button></div>
    <div class="mbd">
      <div class="sr"><span>Alertas activas</span>
        <label class="tgl"><input type="checkbox" id="cA" checked><span class="tsl"></span></label></div>
      <div class="sr"><span>Vibración</span>
        <label class="tgl"><input type="checkbox" id="cV" checked><span class="tsl"></span></label></div>
      <div class="sr"><span>Detectar cambios abruptos ⚡</span>
        <label class="tgl"><input type="checkbox" id="cS" checked><span class="tsl"></span></label></div>
      <div class="sr"><span>Umbral BPM alto</span>
        <input type="number" id="cBpm" value="100" min="70" max="160"></div>
      <div class="sr"><span>Umbral sudoración alto (%)</span>
        <input type="number" id="cGsr" value="80" min="40" max="100"></div>
      <div class="sr"><span>Δ BPM abrupto (BPM/muestra)</span>
        <input type="number" id="cSB" value="15" min="5" max="40"></div>
      <div class="sr"><span>Δ Sudoración abrupta (%)</span>
        <input type="number" id="cSG" value="20" min="5" max="50"></div>
      <div class="sr"><span>Δ Temperatura abrupta (°C)</span>
        <input type="number" id="cST" value="0.5" min="0.1" max="3" step="0.1"></div>
    </div>
    <div class="mft">
      <button class="btn btn-s" onclick="cMod('setMod')">Cancelar</button>
      <button class="btn btn-p" style="width:auto;padding:10px 20px" onclick="saveCfg()">💾 Guardar</button>
    </div>
  </div>
</div>

<!-- MODAL HISTORIAL -->
<div class="mo hidden" id="histMod">
  <div class="mb">
    <div class="mhd"><h2>📋 Historial</h2><button class="mcl" onclick="cMod('histMod')">×</button></div>
    <div class="mbd" id="histBody"><p style="text-align:center;color:var(--text2)">Sin sesiones guardadas</p></div>
    <div class="mft">
      <button class="btn btn-s" onclick="clrHist()">🗑️ Limpiar</button>
      <button class="btn btn-p" style="width:auto;padding:10px 20px" onclick="cMod('histMod')">Cerrar</button>
    </div>
  </div>
</div>

<div id="toast"></div>
<script>
/* ================================================================
   HELIXBAND v2.0 — JavaScript

   FORMATO CSV ARDUINO ACTUAL (12 valores):
     GSR,GSR,BPM,BPM,0,TEMP,MOV,0,0,0,0,TEMP
     [0] [1] [2] [3][4] [5] [6]
     ⚠️  X/Y/Z no se envían → el humanoide usa animación idle

   FORMATO CSV ARDUINO NUEVO (7 valores) ← ACTUALIZAR .ino:
     GSR,BPM,TEMP,MOV,AcX,AcY,AcZ
     [0] [1]  [2] [3]  [4] [5] [6]
     ✅  X/Y/Z reales → humanoide responde al movimiento real

   La app detecta automáticamente cuál formato recibe.
   ================================================================ */

// ── BLE CONFIG ──
const BLE={
  svc:'4fafc201-1fb5-459e-8fcc-c5c9c331914b',
  chr:'beb5483e-36e1-4688-b7f5-ea07361b26a8',
  name:'HELIXBAND'
};

// ── ESTADO GLOBAL ──
const S={
  dev:null,srv:null,chr:null,
  on:false,t0conn:null,t0sess:null,
  n:0, // muestras
  base:{bpm:null,temp:null,gsr:null},
  prev:{bpm:null,temp:null,gsr:null},
  ac:{x:0,y:0,z:1},       // acelerómetro actual
  hasRealAccel:false,      // true si el Arduino envía X/Y/Z reales
  hist:{bpm:[],temp:[],gsr:[],ts:[],ax:[],ay:[],az:[]},
  sess:[],
  cfg:{
    alerts:true,vib:true,spike:true,
    bpmMax:100,gsrMax:80,
    dBpm:15,dGsr:20,dTemp:0.5
  }
};
const HN=60; // puntos en gráficas

// ── CHARTS ──
let chBpm,chTemp,chGsr;

// ── THREE.JS ──
let T={ren:null,scene:null,cam:null,root:null,parts:{},aId:null};

// ════════════════════════════════════════════
// INIT
// ════════════════════════════════════════════
document.addEventListener('DOMContentLoaded',()=>{
  loadCfg();loadSess();
  initCharts();initThree();
  bind();
  setTimeout(()=>{
    document.getElementById('LS').classList.add('fo');
    setTimeout(()=>{
      document.getElementById('LS').style.display='none';
      document.getElementById('app').classList.remove('hidden');
    },650);
  },1800);
});

function bind(){
  document.getElementById('connectBtn').addEventListener('click',connect);
  document.getElementById('discBtn').addEventListener('click',disconnect);
  document.getElementById('themeBtn').addEventListener('click',tgTheme);
  document.getElementById('settingsBtn').addEventListener('click',()=>oMod('setMod'));
  document.getElementById('histBtn').addEventListener('click',openHist);
  document.getElementById('expBtn').addEventListener('click',exportXLSX);
}

// ════════════════════════════════════════════
// THREE.JS — HUMANOIDE
// ════════════════════════════════════════════
function initThree(){
  const cv=document.getElementById('tc');
  const W=cv.parentElement.offsetWidth||640, H=300;

  const ren=new THREE.WebGLRenderer({canvas:cv,antialias:true,alpha:true});
  ren.setSize(W,H); ren.setPixelRatio(Math.min(devicePixelRatio,2));
  ren.shadowMap.enabled=true;

  const scene=new THREE.Scene();
  const cam=new THREE.PerspectiveCamera(50,W/H,0.1,100);
  cam.position.set(0,1.4,5); cam.lookAt(0,1.1,0);

  // Luces
  scene.add(new THREE.AmbientLight(0xffffff,0.55));
  const d=new THREE.DirectionalLight(0xffffff,1);
  d.position.set(3,6,4); d.castShadow=true; scene.add(d);
  const f=new THREE.DirectionalLight(0x4fc3f7,0.4);
  f.position.set(-3,2,-2); scene.add(f);

  // Grid
  scene.add(new THREE.GridHelper(6,12,0x334466,0x1a2233));

  // Materiales
  const mBody=new THREE.MeshPhongMaterial({color:0x1976D2,shininess:80});
  const mHead=new THREE.MeshPhongMaterial({color:0xFFCCB0,shininess:60});
  const mLimb=new THREE.MeshPhongMaterial({color:0x1565C0,shininess:80});
  const mJnt =new THREE.MeshPhongMaterial({color:0xE91E63,shininess:120});
  const mEye =new THREE.MeshPhongMaterial({color:0x111111});
  const mShoe=new THREE.MeshPhongMaterial({color:0x212121});

  function bx(w,h,d,m,x,y,z,parent){
    const mesh=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),m);
    mesh.position.set(x,y,z); mesh.castShadow=true;
    (parent||scene).add(mesh); return mesh;
  }
  function sp(r,m,x,y,z,parent){
    const mesh=new THREE.Mesh(new THREE.SphereGeometry(r,16,16),m);
    mesh.position.set(x,y,z); mesh.castShadow=true;
    (parent||scene).add(mesh); return mesh;
  }
  function grp(x,y,z,parent){
    const g=new THREE.Group(); g.position.set(x,y,z);
    (parent||scene).add(g); return g;
  }

  // ROOT (toda la figura rota con el acelerómetro)
  const root=grp(0,0,0);

  // Torso
  const torso=bx(0.72,0.92,0.36,mBody, 0,1.46,0,root);

  // Cuello + cabeza
  bx(0.18,0.14,0.18,mLimb,0,1.97,0,root);
  const head=sp(0.29,mHead,0,2.34,0,root);
  // Ojos
  sp(0.062,mEye,-0.10,2.38,0.25,root);
  sp(0.062,mEye, 0.10,2.38,0.25,root);
  // Cabello decorativo
  bx(0.60,0.08,0.58,mLimb,0,2.62,0,root);

  // Pelvis
  bx(0.62,0.26,0.30,mBody,0,0.97,0,root);

  // Hombros (joints)
  sp(0.13,mJnt,-0.45,1.90,0,root);
  sp(0.13,mJnt, 0.45,1.90,0,root);

  // Brazo izq
  const aLG=grp(-0.45,1.78,0,root);
  bx(0.18,0.55,0.18,mLimb,0,-0.27,0,aLG);
  sp(0.10,mJnt,0,-0.55,0,aLG);
  const aLFG=grp(0,-0.55,0,aLG);
  bx(0.14,0.50,0.14,mLimb,0,-0.25,0,aLFG);
  sp(0.07,mJnt,0,-0.50,0,aLFG); // mano

  // Brazo der
  const aRG=grp(0.45,1.78,0,root);
  bx(0.18,0.55,0.18,mLimb,0,-0.27,0,aRG);
  sp(0.10,mJnt,0,-0.55,0,aRG);
  const aRFG=grp(0,-0.55,0,aRG);
  bx(0.14,0.50,0.14,mLimb,0,-0.25,0,aRFG);
  sp(0.07,mJnt,0,-0.50,0,aRFG);

  // Caderas
  sp(0.13,mJnt,-0.22,0.86,0,root);
  sp(0.13,mJnt, 0.22,0.86,0,root);

  // Pierna izq
  const lLG=grp(-0.22,0.84,0,root);
  bx(0.23,0.60,0.23,mLimb,0,-0.30,0,lLG);
  sp(0.12,mJnt,0,-0.60,0,lLG);
  const lLFG=grp(0,-0.60,0,lLG);
  bx(0.19,0.55,0.19,mLimb,0,-0.27,0,lLFG);
  bx(0.23,0.10,0.33,mShoe,0,-0.58,0.06,lLFG);

  // Pierna der
  const lRG=grp(0.22,0.84,0,root);
  bx(0.23,0.60,0.23,mLimb,0,-0.30,0,lRG);
  sp(0.12,mJnt,0,-0.60,0,lRG);
  const lRFG=grp(0,-0.60,0,lRG);
  bx(0.19,0.55,0.19,mLimb,0,-0.27,0,lRFG);
  bx(0.23,0.10,0.33,mShoe,0,-0.58,0.06,lRFG);

  // Ejes de referencia
  const ax=new THREE.AxesHelper(0.7); ax.position.set(-2.5,0.01,-2.5); scene.add(ax);

  T={ren,scene,cam,root,
    parts:{torso,head,aLG,aRG,aLFG,aRFG,lLG,lRG,lLFG,lRFG},aId:null};

  let t=0;
  function animate(){
    T.aId=requestAnimationFrame(animate);
    t+=0.018;

    const {x,y,z}=S.ac;
    // Inclinar figura según acelerómetro (suave)
    root.rotation.x+=(Math.max(-0.75,Math.min(0.75,-y*1.3))-root.rotation.x)*0.07;
    root.rotation.z+=(Math.max(-0.75,Math.min(0.75, x*1.3))-root.rotation.z)*0.07;

    // Detectar movimiento real
    const moving=S.hasRealAccel
      ? (Math.abs(x)+Math.abs(y)+Math.abs(z-1)>0.25)
      : false; // con formato viejo solo animación idle

    const amp=moving?0.50:0.04;
    const spd=moving?2.6 :0.5;

    // Respiración idle
    T.parts.torso.scale.y=1+Math.sin(t*0.45)*0.018;

    // Oscilación de brazos y piernas
    T.parts.aLG.rotation.x= Math.sin(t*spd)*amp;
    T.parts.aRG.rotation.x=-Math.sin(t*spd)*amp;
    T.parts.aLFG.rotation.x=Math.max(0, Math.sin(t*spd+.4)*0.28);
    T.parts.aRFG.rotation.x=Math.max(0,-Math.sin(t*spd+.4)*0.28);
    T.parts.lLG.rotation.x=-Math.sin(t*spd)*amp*0.85;
    T.parts.lRG.rotation.x= Math.sin(t*spd)*amp*0.85;
    T.parts.lLFG.rotation.x=Math.max(0, Math.sin(t*spd-.25)*0.30);
    T.parts.lRFG.rotation.x=Math.max(0,-Math.sin(t*spd-.25)*0.30);

    // Rotación lenta de cámara (idle)
    if(!moving){
      cam.position.x=Math.sin(t*0.07)*1.2;
      cam.position.z=5+Math.cos(t*0.07)*0.4;
      cam.lookAt(0,1.2,0);
    } else {
      cam.position.x+=(0-cam.position.x)*0.05;
      cam.position.z+=(5-cam.position.z)*0.05;
      cam.lookAt(0,1.2,0);
    }

    ren.render(scene,cam);
  }
  animate();

  window.addEventListener('resize',()=>{
    const w=cv.parentElement.offsetWidth||640;
    ren.setSize(w,H); cam.aspect=w/H; cam.updateProjectionMatrix();
  });
}

// ════════════════════════════════════════════
// BLUETOOTH
// ════════════════════════════════════════════
async function connect(){
  const btn=document.getElementById('connectBtn');
  const sc=document.getElementById('scanSt');
  try{
    btn.disabled=true; sc.classList.remove('hidden');
    S.dev=await navigator.bluetooth.requestDevice({
      filters:[{name:BLE.name}],optionalServices:[BLE.svc]
    });
    S.srv=await S.dev.gatt.connect();
    const svc=await S.srv.getPrimaryService(BLE.svc);
    S.chr=await svc.getCharacteristic(BLE.chr);
    await S.chr.startNotifications();
    S.chr.addEventListener('characteristicvaluechanged',onData);
    S.dev.addEventListener('gattserverdisconnected',onDisc);
    S.on=true; S.t0conn=Date.now(); S.t0sess=Date.now();
    S.n=0; S.base={bpm:null,temp:null,gsr:null}; S.prev={bpm:null,temp:null,gsr:null};
    S.hasRealAccel=false;
    setConn(true);
    document.getElementById('CS').classList.add('hidden');
    document.getElementById('DP').classList.remove('hidden');
    document.getElementById('calib').style.display='';
    startTimer(); toast('✅ Conectado a HELIXBAND'); vib(200);
  }catch(e){
    toast('❌ '+e.message,4000);
    btn.disabled=false; sc.classList.add('hidden');
  }
}
async function disconnect(){
  if(S.dev&&S.dev.gatt.connected) await S.dev.gatt.disconnect();
  onDisc();
}
function onDisc(){
  saveSess(); S.on=false; setConn(false);
  document.getElementById('CS').classList.remove('hidden');
  document.getElementById('DP').classList.add('hidden');
  document.getElementById('connectBtn').disabled=false;
  document.getElementById('scanSt').classList.add('hidden');
  document.getElementById('calib').style.display='';
  toast('🔌 Desconectado');
}

// ════════════════════════════════════════════
// DATOS — detección automática de formato Arduino
// ════════════════════════════════════════════
function onData(ev){
  const raw=new TextDecoder().decode(ev.target.value).trim();

  // Reemplazar coma decimal europea si la hubiera
  const clean=raw.replace(/,(?=\d{1,3}(?:,|$))/g,',');
  const v=clean.split(',').map(s=>parseFloat(s.trim()));

  if(v.length<4||v.some(isNaN)) return;

  let gsr,bpm,temp,mov,ax=0,ay=0,az=1;

  if(v.length>=12){
    // ── FORMATO ANTIGUO (12 valores) ──
    // GSR,GSR,BPM,BPM,0,TEMP,MOV,0,0,0,0,TEMP
    gsr =v[0];
    bpm =v[2];
    temp=v[5];   // ← temperatura real MPU6050
    mov =v[6];
    // Sin acelerómetro real → idle
    S.hasRealAccel=false;
  } else if(v.length>=7){
    // ── FORMATO NUEVO (7 valores) ──
    // GSR,BPM,TEMP,MOV,AcX,AcY,AcZ
    gsr =v[0];
    bpm =v[1];
    temp=v[2];
    mov =v[3];
    ax  =v[4]; ay=v[5]; az=v[6];
    S.hasRealAccel=true;
  } else {
    // Mínimo 4: GSR,BPM,TEMP,MOV
    gsr=v[0]; bpm=v[1]; temp=v[2]; mov=v[3];
  }

  // Guardar acelerómetro
  S.ac={x:ax,y:ay,z:az};

  // Debug
  updDbg(raw,v);

  // Ocultar calib cuando ya llegan datos válidos
  if(S.n===0) document.getElementById('calib').style.display='none';

  // Baseline (primeras 5 muestras)
  if(S.n<5){
    if(S.base.bpm ===null&&bpm >30)  S.base.bpm =bpm;
    if(S.base.temp===null&&temp>20)  S.base.temp=temp;
    if(S.base.gsr ===null)           S.base.gsr =gsr;
  }

  const dB=bpm >0&&S.base.bpm !==null ? bpm -S.base.bpm  : 0;
  const dT=temp>20&&S.base.temp!==null ? temp-S.base.temp : 0;
  const dG=S.base.gsr!==null           ? gsr -S.base.gsr  : 0;

  // ── Spike detection ──
  const spk=[];
  if(S.cfg.spike&&S.n>=5){
    if(S.prev.bpm !==null&&bpm >30&&Math.abs(bpm -S.prev.bpm )>=S.cfg.dBpm)
      spk.push({msg:`⚡ Cambio brusco BPM: ${S.prev.bpm.toFixed(0)}→${bpm.toFixed(0)}`,card:'cardP'});
    if(S.prev.temp!==null&&temp>20&&Math.abs(temp-S.prev.temp)>=S.cfg.dTemp)
      spk.push({msg:`⚡ Cambio brusco Temp: ${S.prev.temp.toFixed(1)}→${temp.toFixed(1)} °C`,card:'cardT'});
    if(S.prev.gsr !==null&&Math.abs(gsr -S.prev.gsr )>=S.cfg.dGsr)
      spk.push({msg:`⚡ Cambio brusco Sudoración: ${S.prev.gsr.toFixed(0)}→${gsr.toFixed(0)} %`,card:'cardG'});
  }
  S.prev={bpm,temp,gsr};
  S.n++;

  updUI({gsr,dG,bpm,dB,temp,dT,mov,tOK:temp>20&&temp<50,ax,ay,az,spk});
  addHist({bpm,temp,gsr,ax,ay,az});
  chkAlerts({bpm,gsr,temp,tOK:temp>20&&temp<50,spk});

  document.getElementById('samp').textContent=S.n;
  document.getElementById('upd').textContent=new Date().toLocaleTimeString();
}

// ════════════════════════════════════════════
// UI
// ════════════════════════════════════════════
function updUI(d){
  // ── BPM ──
  document.getElementById('valP').textContent=d.bpm>0?Math.round(d.bpm):'--';
  setDlt('dltP',d.dB,'BPM',d.bpm>0,S.cfg.dBpm);
  const pb=d.spk.some(s=>s.card==='cardP')?'s':d.bpm<=0?'na':d.bpm<60?'w':d.bpm>S.cfg.bpmMax?'d':'n';
  setBadge('badgeP',pb,d.bpm<=0?'--':d.bpm<60?'Bajo':d.bpm>S.cfg.bpmMax?'Alto':'Normal');
  if(d.spk.some(s=>s.card==='cardP')) flash('cardP');

  // ── TEMP ──
  if(d.tOK){
    document.getElementById('valT').textContent=d.temp.toFixed(1);
    setDlt('dltT',d.dT,'°C',true,S.cfg.dTemp);
    document.getElementById('stT').innerHTML=
      '<span class="sdok">●</span><span>MPU6050 OK — Chip a '+d.temp.toFixed(1)+' °C</span>';
    // Rango típico chip MPU6050: 34–42 °C
    const tw=d.spk.some(s=>s.card==='cardT')?'s':(d.temp<34||d.temp>42)?'w':'n';
    setBadge('badgeT',tw,d.spk.some(s=>s.card==='cardT')?'⚡Spike':(d.temp<34||d.temp>42)?'Atípica':'Normal');
    if(d.spk.some(s=>s.card==='cardT')) flash('cardT');
  } else {
    document.getElementById('valT').textContent='--';
    document.getElementById('dltT').innerHTML='vs baseline: <b class="nt">--</b>';
    document.getElementById('stT').innerHTML=
      '<span class="sderr">●</span><span>Sin lectura válida (temp='+d.temp.toFixed(1)+'°C fuera de rango)</span>';
    setBadge('badgeT','na','N/A');
  }

  // ── GSR SUDORACIÓN ──
  document.getElementById('valG').textContent=Math.round(d.gsr);
  setDlt('dltG',d.dG,'%',true,S.cfg.dGsr);
  document.getElementById('gfill').style.width=Math.min(100,Math.max(0,d.gsr))+'%';
  const gb=d.spk.some(s=>s.card==='cardG')?'s':d.gsr>S.cfg.gsrMax?'d':d.gsr>60?'w':'n';
  setBadge('badgeG',gb,d.spk.some(s=>s.card==='cardG')?'⚡Spike':d.gsr>S.cfg.gsrMax?'Alta':d.gsr>60?'Elevada':'Normal');
  if(d.spk.some(s=>s.card==='cardG')) flash('cardG');

  // ── MOVIMIENTO 3D ──
  document.getElementById('axX').textContent=d.ax.toFixed(3);
  document.getElementById('axY').textContent=d.ay.toFixed(3);
  document.getElementById('axZ').textContent=d.az.toFixed(3);
  const mov=d.mov===1||(S.hasRealAccel&&(Math.abs(d.ax)+Math.abs(d.ay)+Math.abs(d.az-1)>0.3));
  const mst=document.getElementById('mst');
  mst.textContent=mov?'🏃 Movimiento':'🧍 Quieto';
  mst.className='msb '+(mov?'ms-m':'ms-q');
  setBadge('badgeM',mov?'w':'n',mov?'Activo':'Quieto');
}

function setDlt(id,delta,unit,valid,thr){
  if(!valid){document.getElementById(id).innerHTML='vs baseline: <b class="nt">--</b>';return;}
  const sign=delta>=0?'+':'';
  const isSpk=Math.abs(delta)>=thr;
  const cls=isSpk?'sa':delta>0?'up':delta<0?'dn':'nt';
  const arrow=isSpk?' ⚡':delta>0?' ▲':delta<0?' ▼':'';
  const prec=unit==='°C'?2:1;
  document.getElementById(id).innerHTML=
    `vs baseline: <b class="${cls}">${sign}${delta.toFixed(prec)} ${unit}${arrow}</b>`;
}

function setBadge(id,t,txt){
  const m={'n':'b-n','w':'b-w','d':'b-d','s':'b-s','na':'b-na'};
  const el=document.getElementById(id);
  el.className='badge '+(m[t]||'b-na');
  el.textContent=txt;
}

function flash(id){
  const el=document.getElementById(id);
  el.classList.remove('mc-spike');
  void el.offsetWidth;
  el.classList.add('mc-spike');
  setTimeout(()=>el.classList.remove('mc-spike'),2000);
}

// ════════════════════════════════════════════
// ALERTAS
// ════════════════════════════════════════════
function chkAlerts(d){
  document.getElementById('AB').innerHTML='';
  if(!S.cfg.alerts) return;
  d.spk.forEach(s=>addAl(s.msg,'s'));
  if(d.bpm>0&&d.bpm>S.cfg.bpmMax) addAl(`⚠️ BPM alto: ${Math.round(d.bpm)} LPM`,'w');
  if(d.gsr>S.cfg.gsrMax)          addAl(`🚨 Sudoración alta: ${Math.round(d.gsr)}%`,'d');
  if(d.tOK&&(d.temp<34||d.temp>42)) addAl(`⚠️ Temp. atípica: ${d.temp.toFixed(1)} °C`,'w');
  if(d.spk.length) vib([100,50,100]);
}
function addAl(msg,t){
  const el=document.createElement('div');
  el.className='al al-'+(t==='s'?'s':t==='d'?'d':'w');
  el.innerHTML=`<span style="font-size:18px">${t==='s'?'⚡':t==='d'?'🚨':'⚠️'}</span><span>${msg}</span>`;
  document.getElementById('AB').appendChild(el);
  setTimeout(()=>el.remove(),7000);
}

// ════════════════════════════════════════════
// CHARTS
// ════════════════════════════════════════════
function initCharts(){
  const base={type:'line',options:{
    responsive:true,maintainAspectRatio:false,
    plugins:{legend:{display:false}},
    scales:{y:{beginAtZero:false},x:{display:false}},
    elements:{line:{tension:.4},point:{radius:0}},
    animation:{duration:0}
  }};
  chBpm=new Chart(document.getElementById('chP'),{
    ...base,data:{labels:[],datasets:[{data:[],spanGaps:true,
      borderColor:'#E91E63',backgroundColor:'rgba(233,30,99,.1)',fill:true,borderWidth:2}]}
  });
  chTemp=new Chart(document.getElementById('chT'),{
    ...base,data:{labels:[],datasets:[{data:[],spanGaps:true,
      borderColor:'#4CAF50',backgroundColor:'rgba(76,175,80,.1)',fill:true,borderWidth:2}]}
  });
  chGsr=new Chart(document.getElementById('chG'),{
    type:'line',data:{labels:[],datasets:[{data:[],spanGaps:true,
      borderColor:'#00BCD4',backgroundColor:'rgba(0,188,212,.1)',fill:true,borderWidth:2}]},
    options:{...base.options,scales:{y:{beginAtZero:true,max:100},x:{display:false}}}
  });
}
function addHist(d){
  const t=new Date().toLocaleTimeString();
  S.hist.bpm.push(d.bpm>0?d.bpm:null);
  S.hist.temp.push(d.temp>20?d.temp:null);
  S.hist.gsr.push(d.gsr);
  S.hist.ts.push(t);
  S.hist.ax.push(d.ax);S.hist.ay.push(d.ay);S.hist.az.push(d.az);
  if(S.hist.bpm.length>HN){
    ['bpm','temp','gsr','ts','ax','ay','az'].forEach(k=>S.hist[k].shift());
  }
  chBpm.data.labels=chTemp.data.labels=chGsr.data.labels=S.hist.ts;
  chBpm.data.datasets[0].data=S.hist.bpm;
  chTemp.data.datasets[0].data=S.hist.temp;
  chGsr.data.datasets[0].data=S.hist.gsr;
  chBpm.update();chTemp.update();chGsr.update();
}

// ════════════════════════════════════════════
// DEBUG
// ════════════════════════════════════════════
function tgDbg(){
  document.getElementById('dbgB').classList.toggle('open');
  document.getElementById('dbgCh').classList.toggle('open');
}
function updDbg(raw,v){
  document.getElementById('dbgR').textContent=raw;
  document.getElementById('dbgC').textContent='Muestras recibidas: '+(S.n+1);
  const fmt12=['[0]GSR%','[1]GSR2','[2]BPM','[3]BPM2','[4]0','[5]TEMP','[6]MOV','[7-11]'];
  const fmt7 =['[0]GSR%','[1]BPM','[2]TEMP','[3]MOV','[4]AcX','[5]AcY','[6]AcZ'];
  const fmt=v.length>=12?fmt12:fmt7;
  const vals=v.length>=12
    ? v.slice(0,7).concat([(v.slice(7)||[]).join(',')||'—'])
    : v.slice(0,7);
  document.getElementById('dbgG').innerHTML=fmt.map((l,i)=>`
    <div class="df"><span class="dfl">${l}</span><span class="dfv">${vals[i]!==undefined?vals[i]:'—'}</span></div>
  `).join('');
}

// ════════════════════════════════════════════
// TIMER
// ════════════════════════════════════════════
let tID;
function startTimer(){
  clearInterval(tID);
  tID=setInterval(()=>{
    if(!S.on){clearInterval(tID);return;}
    const ms=Date.now()-S.t0sess;
    document.getElementById('dur').textContent=
      `${String(Math.floor(ms/60000)).padStart(2,'0')}:${String(Math.floor((ms%60000)/1000)).padStart(2,'0')}`;
    document.getElementById('ctm').textContent=
      `${Math.floor((Date.now()-S.t0conn)/1000)}s`;
  },1000);
}

// ════════════════════════════════════════════
// EXPORT EXCEL — 3 hojas profesionales
// ════════════════════════════════════════════
function exportXLSX(){
  if(!S.n){toast('⚠️ Sin datos para exportar');return;}

  const wb=XLSX.utils.book_new();
  const now=new Date();

  /* ── HOJA 1: Datos brutos ── */
  const h1=[
    ['Timestamp','Frec. Cardíaca (BPM)','Temperatura MPU6050 (°C)',
     'Sudoración GSR (%)','Aceleróm. X (g)','Aceleróm. Y (g)','Aceleróm. Z (g)']
  ];
  for(let i=0;i<S.hist.ts.length;i++){
    h1.push([
      S.hist.ts[i],
      S.hist.bpm[i]??'Sin señal',
      S.hist.temp[i]??'Sin señal',
      +S.hist.gsr[i].toFixed(1),
      +(S.hist.ax[i]||0).toFixed(4),
      +(S.hist.ay[i]||0).toFixed(4),
      +(S.hist.az[i]||0).toFixed(4),
    ]);
  }
  const ws1=XLSX.utils.aoa_to_sheet(h1);
  ws1['!cols']=[{wch:12},{wch:22},{wch:24},{wch:22},{wch:20},{wch:20},{wch:20}];
  XLSX.utils.book_append_sheet(wb,ws1,'📊 Datos Sesión');

  /* ── HOJA 2: Resumen estadístico ── */
  function st(arr){
    const a=arr.filter(x=>x!=null&&!isNaN(x));
    if(!a.length) return{min:'--',max:'--',avg:'--',std:'--',med:'--'};
    const mn=Math.min(...a),mx=Math.max(...a);
    const av=a.reduce((s,x)=>s+x,0)/a.length;
    const std=Math.sqrt(a.map(x=>(x-av)**2).reduce((s,x)=>s+x,0)/a.length);
    const s=[...a].sort((a,b)=>a-b);
    const med=s.length%2?s[~~(s.length/2)]:(s[s.length/2-1]+s[s.length/2])/2;
    return{min:mn.toFixed(2),max:mx.toFixed(2),avg:av.toFixed(2),std:std.toFixed(2),med:med.toFixed(2)};
  }
  const ms=Math.floor((Date.now()-S.t0sess)/60000);
  const ss=Math.floor(((Date.now()-S.t0sess)%60000)/1000);
  const h2=[
    ['REPORTE DE SESIÓN — HELIXBAND v2.0'],
    [],
    ['Fecha',now.toLocaleDateString(),'','',''],
    ['Hora de inicio',new Date(S.t0sess).toLocaleTimeString(),'','',''],
    ['Hora de fin',now.toLocaleTimeString(),'','',''],
    ['Duración',`${ms}m ${ss}s`,'','',''],
    ['Total muestras',S.n,'','',''],
    ['Formato Arduino',S.hasRealAccel?'v2 (GSR,BPM,TEMP,MOV,AcX,AcY,AcZ)':'v1 (12 valores, sin X/Y/Z reales)','','',''],
    [],
    ['MÉTRICA','MÍNIMO','MÁXIMO','PROMEDIO','MEDIANA','DESV. STD'],
    ['Frecuencia Cardíaca (BPM)',
      st(S.hist.bpm).min,st(S.hist.bpm).max,st(S.hist.bpm).avg,st(S.hist.bpm).med,st(S.hist.bpm).std],
    ['Temperatura MPU6050 (°C)',
      st(S.hist.temp).min,st(S.hist.temp).max,st(S.hist.temp).avg,st(S.hist.temp).med,st(S.hist.temp).std],
    ['Sudoración GSR (%)',
      st(S.hist.gsr).min,st(S.hist.gsr).max,st(S.hist.gsr).avg,st(S.hist.gsr).med,st(S.hist.gsr).std],
    ['Aceleróm. X (g)',
      st(S.hist.ax).min,st(S.hist.ax).max,st(S.hist.ax).avg,st(S.hist.ax).med,st(S.hist.ax).std],
    ['Aceleróm. Y (g)',
      st(S.hist.ay).min,st(S.hist.ay).max,st(S.hist.ay).avg,st(S.hist.ay).med,st(S.hist.ay).std],
    ['Aceleróm. Z (g)',
      st(S.hist.az).min,st(S.hist.az).max,st(S.hist.az).avg,st(S.hist.az).med,st(S.hist.az).std],
    [],
    ['VALORES BASELINE (calibración app)'],
    ['BPM base',S.base.bpm!==null?S.base.bpm.toFixed(1):'--'],
    ['Temperatura base (°C)',S.base.temp!==null?S.base.temp.toFixed(1):'--'],
    ['GSR base (%)',S.base.gsr!==null?S.base.gsr.toFixed(1):'--'],
    [],
    ['UMBRALES CONFIGURADOS'],
    ['Alerta BPM alto',S.cfg.bpmMax+' BPM'],
    ['Alerta sudoración alta',S.cfg.gsrMax+'%'],
    ['Δ BPM abrupto',S.cfg.dBpm+' BPM/muestra'],
    ['Δ Sudoración abrupta',S.cfg.dGsr+'%/muestra'],
    ['Δ Temperatura abrupta',S.cfg.dTemp+'°C/muestra'],
  ];
  const ws2=XLSX.utils.aoa_to_sheet(h2);
  ws2['!cols']=[{wch:32},{wch:14},{wch:14},{wch:14},{wch:14},{wch:14}];
  XLSX.utils.book_append_sheet(wb,ws2,'📋 Resumen');

  /* ── HOJA 3: Anomalías / Spikes ── */
  const h3=[
    ['Timestamp','Métrica','Valor anterior','Valor nuevo','Diferencia','Umbral','Clasificación']
  ];
  for(let i=1;i<S.hist.ts.length;i++){
    const chk=[
      {k:'bpm', lbl:'BPM (LPM)',       thr:S.cfg.dBpm,  fmt:0},
      {k:'gsr', lbl:'Sudoración (%)',  thr:S.cfg.dGsr,  fmt:1},
      {k:'temp',lbl:'Temperatura (°C)',thr:S.cfg.dTemp, fmt:2},
    ];
    chk.forEach(c=>{
      const cur=S.hist[c.k][i], prv=S.hist[c.k][i-1];
      if(cur==null||prv==null) return;
      const diff=Math.abs(cur-prv);
      if(diff>=c.thr){
        const cls=diff>=c.thr*2?'⚠️ CRÍTICO':'⚡ Abrupto';
        h3.push([
          S.hist.ts[i], c.lbl,
          +prv.toFixed(c.fmt), +cur.toFixed(c.fmt),
          +diff.toFixed(c.fmt), c.thr, cls
        ]);
      }
    });
  }
  if(h3.length>1){
    const ws3=XLSX.utils.aoa_to_sheet(h3);
    ws3['!cols']=[{wch:12},{wch:20},{wch:16},{wch:14},{wch:14},{wch:12},{wch:16}];
    XLSX.utils.book_append_sheet(wb,ws3,'⚡ Anomalías');
  } else {
    const ws3=XLSX.utils.aoa_to_sheet([['Sin anomalías registradas en esta sesión']]);
    XLSX.utils.book_append_sheet(wb,ws3,'⚡ Anomalías');
  }

  const fname=`HELIXBAND_${now.toISOString().slice(0,19).replace(/[T:]/g,'-')}.xlsx`;
  XLSX.writeFile(wb,fname);
  toast('📊 Excel exportado: '+fname);
}

// ════════════════════════════════════════════
// HISTORIAL
// ════════════════════════════════════════════
function saveSess(){
  if(!S.n) return;
  S.sess.unshift({
    date:new Date().toISOString(),dur:Date.now()-S.t0sess,n:S.n,
    bpm:avg(S.hist.bpm.filter(Boolean)),
    temp:avg(S.hist.temp.filter(Boolean)),
    gsr:avg(S.hist.gsr)
  });
  if(S.sess.length>10) S.sess.pop();
  localStorage.setItem('hx_sess',JSON.stringify(S.sess));
}
function loadSess(){
  const x=localStorage.getItem('hx_sess');
  if(x) S.sess=JSON.parse(x);
}
function openHist(){
  const body=document.getElementById('histBody');
  body.innerHTML=S.sess.length
    ? S.sess.map((s,i)=>{
        const d=new Date(s.date),m=Math.floor(s.dur/60000);
        return`<div style="background:var(--bg);border-radius:12px;padding:14px;margin-bottom:10px;">
          <div style="font-weight:700;margin-bottom:8px">
            Sesión ${i+1} — ${d.toLocaleDateString()} ${d.toLocaleTimeString()}
          </div>
          <div style="display:grid;grid-template-columns:1fr 1fr;gap:6px;font-size:14px">
            <span>⏱️ ${m} min</span><span>📊 ${s.n} muestras</span>
            <span>💓 ${s.bpm>0?s.bpm.toFixed(0)+' BPM':'--'}</span>
            <span>🌡️ ${s.temp>0?s.temp.toFixed(1)+' °C':'--'}</span>
            <span>💧 ${s.gsr.toFixed(0)} % GSR</span>
          </div>
        </div>`;
      }).join('')
    : '<p style="text-align:center;color:var(--text2)">Sin sesiones guardadas</p>';
  oMod('histMod');
}
function clrHist(){
  if(!confirm('¿Borrar historial?')) return;
  S.sess=[];localStorage.removeItem('hx_sess');
  cMod('histMod');toast('🗑️ Historial borrado');
}

// ════════════════════════════════════════════
// SETTINGS
// ════════════════════════════════════════════
function saveCfg(){
  S.cfg={
    alerts: document.getElementById('cA').checked,
    vib:    document.getElementById('cV').checked,
    spike:  document.getElementById('cS').checked,
    bpmMax: +document.getElementById('cBpm').value,
    gsrMax: +document.getElementById('cGsr').value,
    dBpm:   +document.getElementById('cSB').value,
    dGsr:   +document.getElementById('cSG').value,
    dTemp:  +document.getElementById('cST').value,
  };
  localStorage.setItem('hx_cfg',JSON.stringify(S.cfg));
  cMod('setMod');toast('✅ Configuración guardada');
}
function loadCfg(){
  const t=localStorage.getItem('hx_theme');
  if(t==='1'){dark=true;document.body.classList.add('dark');document.getElementById('themeBtn').textContent='☀️';}
  const c=localStorage.getItem('hx_cfg');
  if(c) Object.assign(S.cfg,JSON.parse(c));
}
function applyCfgUI(){
  document.getElementById('cA').checked=S.cfg.alerts;
  document.getElementById('cV').checked=S.cfg.vib;
  document.getElementById('cS').checked=S.cfg.spike;
  document.getElementById('cBpm').value=S.cfg.bpmMax;
  document.getElementById('cGsr').value=S.cfg.gsrMax;
  document.getElementById('cSB').value=S.cfg.dBpm;
  document.getElementById('cSG').value=S.cfg.dGsr;
  document.getElementById('cST').value=S.cfg.dTemp;
}

// ════════════════════════════════════════════
// UTILS
// ════════════════════════════════════════════
let dark=false;
function tgTheme(){
  dark=!dark;document.body.classList.toggle('dark',dark);
  document.getElementById('themeBtn').textContent=dark?'☀️':'🌙';
  localStorage.setItem('hx_theme',dark?'1':'0');
}
function setConn(on){
  document.getElementById('cbar').classList.toggle('on',on);
  document.getElementById('ctxt').textContent=on?'Conectado':'Desconectado';
  if(!on) document.getElementById('ctm').textContent='';
}
function oMod(id){if(id==='setMod')applyCfgUI();document.getElementById(id).classList.remove('hidden');}
function cMod(id){document.getElementById(id).classList.add('hidden');}
let tTmr;
function toast(msg,ms=3000){
  const el=document.getElementById('toast');el.textContent=msg;el.classList.add('show');
  clearTimeout(tTmr);tTmr=setTimeout(()=>el.classList.remove('show'),ms);
}
function vib(p){if(S.cfg.vib&&'vibrate'in navigator)navigator.vibrate(p);}
function avg(a){return a.length?a.reduce((s,x)=>s+x,0)/a.length:0;}
</script>
</body>
</html>
