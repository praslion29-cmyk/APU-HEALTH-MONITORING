# APU-HEALTH-MONITORING

<html>
<head>
<title>B737NG APU Multi-Aircraft Monitoring with Recommendations</title>
<style>
body{font-family:Arial;background:#0f172a;color:white;text-align:center;}
h1{color:#38bdf8;}
.header input{padding:5px;margin:5px;}
.container{display:flex;flex-wrap:wrap;gap:20px;padding:20px;justify-content:center;}
.card{background:#1e293b;padding:20px;border-radius:10px;width:300px;}
.normal{color:#22c55e;font-weight:bold;}
.warning{color:#facc15;font-weight:bold;}
.over{color:#ef4444;font-weight:bold;}
button{padding:10px 20px;font-size:16px;cursor:pointer;margin:5px;}
.healthBox{font-size:20px;font-weight:bold;margin:10px;}
.inputBox{display:flex;justify-content:space-between;margin:5px 0;}
@media print{
button{display:none;}
body{background:white;color:black;}
.card{border:1px solid black;}
}
</style>
</head>

<body>
<h1>B737NG APU Multi-Aircraft Monitoring</h1>

<div class="header">
Aircraft Reg <input type="text" id="reg" placeholder="PK-XXX">
<button onclick="addAircraft()">ADD AIRCRAFT</button>
</div>

<div class="container" id="aircraftContainer">
<!-- Aircraft cards akan muncul di sini -->
</div>

<div style="margin-top:20px;">
<button onclick="checkAll()">CHECK CONDITION</button>
<button onclick="window.print()">PRINT</button>
</div>

<script>
function createCard(reg){
  const container=document.getElementById("aircraftContainer");

  const card=document.createElement("div");
  card.className="card";
  card.id="card-"+reg;

  card.innerHTML=`
  <h3>Aircraft: ${reg}</h3>
  <div class="inputBox">RPM % <input id="rpm-${reg}" value="100"></div><p id="rpmStatus-${reg}"></p><p id="rpmCause-${reg}"></p><p id="rpmRec-${reg}"></p>
  <div class="inputBox">EGT °C <input id="egt-${reg}" value="520"></div><p id="egtStatus-${reg}"></p><p id="egtCause-${reg}"></p><p id="egtRec-${reg}"></p>
  <div class="inputBox">Oil Press psi <input id="oilp-${reg}" value="60"></div><p id="oilpStatus-${reg}"></p><p id="oilpCause-${reg}"></p><p id="oilpRec-${reg}"></p>
  <div class="inputBox">Oil Temp °C <input id="oilt-${reg}" value="85"></div><p id="oiltStatus-${reg}"></p><p id="oiltCause-${reg}"></p><p id="oiltRec-${reg}"></p>
  <div class="inputBox">Vibration <input id="vib-${reg}" value="1.2"></div><p id="vibStatus-${reg}"></p><p id="vibCause-${reg}"></p><p id="vibRec-${reg}"></p>
  <div class="inputBox">Generator Load % <input id="load-${reg}" value="70"></div><p id="loadStatus-${reg}"></p><p id="loadCause-${reg}"></p><p id="loadRec-${reg}"></p>
  <div class="inputBox">Bleed Pressure psi <input id="bleed-${reg}" value="38"></div><p id="bleedStatus-${reg}"></p><p id="bleedCause-${reg}"></p><p id="bleedRec-${reg}"></p>
  <div class="inputBox">Start Time sec <input id="start-${reg}" value="32"></div><p id="startStatus-${reg}"></p><p id="startCause-${reg}"></p><p id="startRec-${reg}"></p>
  <div class="healthBox" id="healthScore-${reg}">Health Score : 100</div>
  `;

  container.appendChild(card);
}

function addAircraft(){
  const reg=document.getElementById("reg").value.trim();
  if(reg) createCard(reg);
  document.getElementById("reg").value="";
}

function checkAll(){
  const container=document.getElementById("aircraftContainer");
  for(let card of container.children){
    const reg=card.id.replace("card-","");
    checkAPU(reg);
  }
}

function checkAPU(reg){
let score=100;

/* RPM */
let rpm=document.getElementById("rpm-"+reg).value;
let rpmStatus=document.getElementById("rpmStatus-"+reg);
let rpmCause=document.getElementById("rpmCause-"+reg);
let rpmRec=document.getElementById("rpmRec-"+reg);
if(rpm>=99 && rpm<=101){rpmStatus.innerHTML="<span class='normal'>NORMAL</span>"; rpmCause.innerText="Governor maintaining rated speed"; rpmRec.innerText="No action required";}
else if(rpm>=97 && rpm<99){rpmStatus.innerHTML="<span class='warning'>LOW RPM</span>"; rpmCause.innerText="Governor drift or compressor efficiency drop"; rpmRec.innerText="Inspect fuel control unit"; score-=5;}
else{rpmStatus.innerHTML="<span class='over'>RPM ABNORMAL</span>"; rpmCause.innerText="Fuel control malfunction"; rpmRec.innerText="Perform APU operational check"; score-=10;}

/* EGT */
let egt=document.getElementById("egt-"+reg).value;
let egtStatus=document.getElementById("egtStatus-"+reg);
let egtCause=document.getElementById("egtCause-"+reg);
let egtRec=document.getElementById("egtRec-"+reg);
if(egt<580){egtStatus.innerHTML="<span class='normal'>NORMAL</span>"; egtCause.innerText="Combustion temperature normal"; egtRec.innerText="No maintenance required";}
else if(egt>=580 && egt<620){egtStatus.innerHTML="<span class='warning'>HIGH</span>"; egtCause.innerText="Compressor fouling or turbine wear"; egtRec.innerText="Consider compressor wash"; score-=5;}
else{egtStatus.innerHTML="<span class='over'>OVER LIMIT</span>"; egtCause.innerText="Overtemperature event"; egtRec.innerText="Perform borescope inspection"; score-=10;}

/* Oil Press */
let oilp=document.getElementById("oilp-"+reg).value;
let oilpStatus=document.getElementById("oilpStatus-"+reg);
let oilpCause=document.getElementById("oilpCause-"+reg);
let oilpRec=document.getElementById("oilpRec-"+reg);
if(oilp>=45 && oilp<=70){oilpStatus.innerHTML="<span class='normal'>NORMAL</span>"; oilpCause.innerText="Lubrication system normal"; oilpRec.innerText="No action required";}
else if(oilp>=35 && oilp<45){oilpStatus.innerHTML="<span class='warning'>LOW</span>"; oilpCause.innerText="Oil pump degradation"; oilpRec.innerText="Check oil filter and pump"; score-=5;}
else{oilpStatus.innerHTML="<span class='over'>CRITICAL</span>"; oilpCause.innerText="Lubrication failure risk"; oilpRec.innerText="Shutdown APU and inspect lubrication system"; score-=10;}

/* Oil Temp */
let oilt=document.getElementById("oilt-"+reg).value;
let oiltStatus=document.getElementById("oiltStatus-"+reg);
let oiltCause=document.getElementById("oiltCause-"+reg);
let oiltRec=document.getElementById("oiltRec-"+reg);
if(oilt<=95){oiltStatus.innerHTML="<span class='normal'>NORMAL</span>"; oiltCause.innerText="Oil temperature normal"; oiltRec.innerText="No action required";}
else if(oilt>95 && oilt<=105){oiltStatus.innerHTML="<span class='warning'>HIGH</span>"; oiltCause.innerText="Cooling inefficiency"; oiltRec.innerText="Check oil cooler"; score-=5;}
else{oiltStatus.innerHTML="<span class='over'>OVER LIMIT</span>"; oiltCause.innerText="Possible bearing overheating"; oiltRec.innerText="Inspect lubrication system"; score-=10;}

/* Vibration */
let vib=document.getElementById("vib-"+reg).value;
let vibStatus=document.getElementById("vibStatus-"+reg);
let vibCause=document.getElementById("vibCause-"+reg);
let vibRec=document.getElementById("vibRec-"+reg);
if(vib<=2){vibStatus.innerHTML="<span class='normal'>NORMAL</span>"; vibCause.innerText="Rotor balanced"; vibRec.innerText="No action required";}
else if(vib>2 && vib<=3){vibStatus.innerHTML="<span class='warning'>HIGH</span>"; vibCause.innerText="Rotor imbalance or bearing wear"; vibRec.innerText="Schedule vibration inspection"; score-=5;}
else{vibStatus.innerHTML="<span class='over'>OVER LIMIT</span>"; vibCause.innerText="Severe mechanical imbalance"; vibRec.innerText="Shutdown APU and inspect rotor assembly"; score-=10;}

/* Generator Load */
let load=document.getElementById("load-"+reg).value;
let loadStatus=document.getElementById("loadStatus-"+reg);
let loadCause=document.getElementById("loadCause-"+reg);
let loadRec=document.getElementById("loadRec-"+reg);
if(load<=90){loadStatus.innerHTML="<span class='normal'>NORMAL</span>"; loadCause.innerText="Generator operating within limits"; loadRec.innerText="No action required";}
else if(load>90 && load<=105){loadStatus.innerHTML="<span class='warning'>HIGH LOAD</span>"; loadCause.innerText="High electrical demand"; loadRec.innerText="Monitor load, check electrical system"; score-=5;}
else{loadStatus.innerHTML="<span class='over'>OVER LIMIT</span>"; loadCause.innerText="Possible generator overload"; loadRec.innerText="Reduce electrical load, inspect generator"; score-=10;}

/* Bleed Pressure */
let bleed=document.getElementById("bleed-"+reg).value;
let bleedStatus=document.getElementById("bleedStatus-"+reg);
let bleedCause=document.getElementById("bleedCause-"+reg);
let bleedRec=document.getElementById("bleedRec-"+reg);
if(bleed>=30){bleedStatus.innerHTML="<span class='normal'>GOOD</span>"; bleedCause.innerText="Bleed system operating normally"; bleedRec.innerText="No action required";}
else if(bleed>=25 && bleed<30){bleedStatus.innerHTML="<span class='warning'>LOW</span>"; bleedCause.innerText="Possible small leak or valve inefficiency"; bleedRec.innerText="Check bleed valves"; score-=5;}
else{bleedStatus.innerHTML="<span class='over'>CRITICAL</span>"; bleedCause.innerText="Bleed system failure risk"; bleedRec.innerText="Inspect bleed system immediately"; score-=10;}

/* Start Time */
let start=document.getElementById("start-"+reg).value;
let startStatus=document.getElementById("startStatus-"+reg);
let startCause=document.getElementById("startCause-"+reg);
let startRec=document.getElementById("startRec-"+reg);
if(start<=60){startStatus.innerHTML="<span class='normal'>NORMAL</span>"; startCause.innerText="Start system healthy"; startRec.innerText="No action required";}
else{startStatus.innerHTML="<span class='over'>OVER TIME</span>"; startCause.innerText="Start system failure risk"; startRec.innerText="Immediate inspection of starter and battery"; score-=10;}

/* Update Health Score */
document.getElementById("healthScore-"+reg).innerText="Health Score : "+score;
}
</script>

