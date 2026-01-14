<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Sistema Clínico Nutricional</title>

<style>
body{
  background:#e8f0ff;
  font-family:'Segoe UI',sans-serif;
  margin:0;
  text-align:center;
}
header{
  background:linear-gradient(90deg,#007BFF,#003f9e);
  color:white;
  padding:20px;
  font-size:26px;
}
.card{
  background:white;
  width:95%;
  margin:25px auto;
  padding:20px;
  border-radius:15px;
  box-shadow:0 0 15px rgba(0,0,0,.15);
}
input,select,button{
  width:90%;
  padding:10px;
  margin:6px;
  border-radius:8px;
  border:1px solid #ccc;
}
button{
  background:#007BFF;
  color:white;
  border:none;
  cursor:pointer;
}
.update{ background:#28a745; }
.delete{ background:#dc3545; }

table{
  width:95%;
  margin:20px auto;
  border-collapse:collapse;
}
th{
  background:#007BFF;
  color:white;
  padding:10px;
}
td{
  padding:8px;
  border-bottom:1px solid #ddd;
}
tr:nth-child(even){ background:#f2f6ff; }

.img-row{
  display:flex;
  justify-content:center;
  gap:10px;
  margin:10px 0;
}
.img-row img{
  width:90px;
  height:60px;
  border-radius:8px;
  object-fit:cover;
}

footer{
  background:#003366;
  color:white;
  padding:15px;
  margin-top:30px;
}
</style>
</head>

<body>

<header>Sistema de Control Nutricional</header>

<!-- ===== INTRODUCCIÓN ===== -->
<div class="card">
<h2>Introducción</h2>
<table>
<tr><th>Elemento</th><th>Descripción</th></tr>
<tr><td>Dirigido a</td><td>Nutricionistas y profesionales de la salud</td></tr>
<tr><td>Objetivo</td><td>Control y seguimiento del estado nutricional mediante IMC</td></tr>
<tr><td>Funciones</td><td>Registro de pacientes, cálculo IMC y recomendaciones</td></tr>
</table>
</div>

<!-- ===== ADMINISTRADORES ===== -->
<div class="card">
<h2>Administradores</h2>

<input type="hidden" id="adminIndex">
<input id="adminUser" placeholder="Usuario">
<input id="adminPass" placeholder="Contraseña">
<input id="adminNombre" placeholder="Nombre completo">
<select id="adminNivel">
<option>Básico</option>
<option>Medio</option>
<option>Avanzado</option>
</select>

<button onclick="guardarAdmin()">Guardar</button>
<button class="update" onclick="actualizarAdmin()">Actualizar</button>

<table>
<tr><th>Usuario</th><th>Nombre</th><th>Nivel</th><th>Acciones</th></tr>
<tbody id="tablaAdmin"></tbody>
</table>
</div>

<!-- ===== JÓVENES ===== -->
<div class="card">
<h2>Jóvenes</h2>

<input type="hidden" id="jIndex">
<input id="jCurp" placeholder="CURP">
<input id="jNombre" placeholder="Nombre">
<input id="jApellido" placeholder="Apellido">
<input type="date" id="jFecha">
<input id="jDireccion" placeholder="Dirección">

<button onclick="guardarJoven()">Guardar</button>
<button class="update" onclick="actualizarJoven()">Actualizar</button>

<table>
<tr><th>CURP</th><th>Nombre</th><th>Apellido</th><th>Acciones</th></tr>
<tbody id="tablaJovenes"></tbody>
</table>
</div>

<!-- ===== IMC ===== -->
<div class="card">
<h2>Índice de Masa Corporal (IMC)</h2>

<div class="img-row">
<img src="https://images.unsplash.com/photo-1571019613454-1cb2f99b2d8b">
<img src="https://images.unsplash.com/photo-1558611848-73f7eb4001a1">
<img src="https://images.unsplash.com/photo-1599058917212-d750089bc07e">
</div>

<input type="hidden" id="imcIndex">
<input id="imcCurp" placeholder="CURP">
<input type="number" step="0.01" id="peso" placeholder="Peso (kg)">
<input type="number" step="0.01" id="altura" placeholder="Altura (m)">

<button onclick="guardarIMC()">Guardar</button>
<button class="update" onclick="actualizarIMC()">Actualizar</button>

<div id="resultado"></div>

<table>
<tr><th>CURP</th><th>IMC</th><th>Clasificación</th><th>Acciones</th></tr>
<tbody id="tablaIMC"></tbody>
</table>
</div>

<!-- ===== RECOMENDACIONES ===== -->
<div class="card">
<h2>Recomendaciones según IMC</h2>
<table>
<tr><th>IMC</th><th>Estado</th><th>Recomendación</th></tr>
<tr><td>&lt;18.5</td><td>Bajo peso</td><td>Aumentar proteínas y calorías saludables</td></tr>
<tr><td>18.5–24.9</td><td>Normal</td><td>Mantener hábitos saludables</td></tr>
<tr><td>25–29.9</td><td>Sobrepeso</td><td>Reducir azúcares y aumentar ejercicio</td></tr>
<tr><td>≥30</td><td>Obesidad</td><td>Consultar especialista</td></tr>
</table>
</div>

<footer>
⭐LUCIANO DIAZ CASTRO PROYECTO CECYTEM⭐
</footer>

<script>
// ===== DATOS =====
let admins = JSON.parse(localStorage.getItem("admins")) || [];
let jovenes = JSON.parse(localStorage.getItem("jovenes")) || [];
let imcData = JSON.parse(localStorage.getItem("imc")) || [];

// ===== LIMPIAR =====
function limpiarAdmin(){
adminIndex.value=""; adminUser.value=""; adminPass.value="";
adminNombre.value=""; adminNivel.value="Básico";
}
function limpiarJoven(){
jIndex.value=""; jCurp.value=""; jNombre.value="";
jApellido.value=""; jFecha.value=""; jDireccion.value="";
}
function limpiarIMC(){
imcIndex.value=""; imcCurp.value="";
peso.value=""; altura.value=""; resultado.innerHTML="";
}

// ===== ADMIN =====
function guardarAdmin(){
admins.push({u:adminUser.value,p:adminPass.value,n:adminNombre.value,ni:adminNivel.value});
localStorage.setItem("admins",JSON.stringify(admins));
mostrarAdmins(); limpiarAdmin();
}
function editarAdmin(i){
adminIndex.value=i;
adminUser.value=admins[i].u;
adminPass.value=admins[i].p;
adminNombre.value=admins[i].n;
adminNivel.value=admins[i].ni;
}
function actualizarAdmin(){
if(adminIndex.value==="")return;
admins[adminIndex.value]={u:adminUser.value,p:adminPass.value,n:adminNombre.value,ni:adminNivel.value};
localStorage.setItem("admins",JSON.stringify(admins));
mostrarAdmins(); limpiarAdmin();
}
function eliminarAdmin(i){
admins.splice(i,1);
localStorage.setItem("admins",JSON.stringify(admins));
mostrarAdmins();
}
function mostrarAdmins(){
tablaAdmin.innerHTML="";
admins.forEach((a,i)=>{
tablaAdmin.innerHTML+=`<tr>
<td>${a.u}</td><td>${a.n}</td><td>${a.ni}</td>
<td><button onclick="editarAdmin(${i})">Editar</button>
<button class="delete" onclick="eliminarAdmin(${i})">Eliminar</button></td>
</tr>`;
});
}
mostrarAdmins();

// ===== JÓVENES =====
function guardarJoven(){
jovenes.push({c:jCurp.value,n:jNombre.value,a:jApellido.value,f:jFecha.value,d:jDireccion.value});
localStorage.setItem("jovenes",JSON.stringify(jovenes));
mostrarJovenes(); limpiarJoven();
}
function editarJoven(i){
jIndex.value=i;
Object.assign(jCurp,{value:jovenes[i].c});
jNombre.value=jovenes[i].n;
jApellido.value=jovenes[i].a;
jFecha.value=jovenes[i].f;
jDireccion.value=jovenes[i].d;
}
function actualizarJoven(){
if(jIndex.value==="")return;
jovenes[jIndex.value]={c:jCurp.value,n:jNombre.value,a:jApellido.value,f:jFecha.value,d:jDireccion.value};
localStorage.setItem("jovenes",JSON.stringify(jovenes));
mostrarJovenes(); limpiarJoven();
}
function eliminarJoven(i){
jovenes.splice(i,1);
localStorage.setItem("jovenes",JSON.stringify(jovenes));
mostrarJovenes();
}
function mostrarJovenes(){
tablaJovenes.innerHTML="";
jovenes.forEach((j,i)=>{
tablaJovenes.innerHTML+=`<tr>
<td>${j.c}</td><td>${j.n}</td><td>${j.a}</td>
<td><button onclick="editarJoven(${i})">Editar</button>
<button class="delete" onclick="eliminarJoven(${i})">Eliminar</button></td>
</tr>`;
});
}
mostrarJovenes();

// ===== IMC =====
function guardarIMC(){
let imc=(peso.value/(altura.value*altura.value)).toFixed(2);
let clas="",rec="";
if(imc<18.5){clas="Bajo peso";rec="Aumenta proteínas";}
else if(imc<25){clas="Normal";rec="Buen estado";}
else if(imc<30){clas="Sobrepeso";rec="Reduce azúcares";}
else{clas="Obesidad";rec="Consulta médico";}

imcData.push({c:imcCurp.value,p:peso.value,a:altura.value,i:imc,cl:clas});
localStorage.setItem("imc",JSON.stringify(imcData));
resultado.innerHTML=`IMC: ${imc} – ${clas}. ${rec}`;
mostrarIMC(); limpiarIMC();
}
function editarIMC(i){
imcIndex.value=i;
imcCurp.value=imcData[i].c;
peso.value=imcData[i].p;
altura.value=imcData[i].a;
}
function actualizarIMC(){
if(imcIndex.value==="")return;
let imc=(peso.value/(altura.value*altura.value)).toFixed(2);
let clas=imc<18.5?"Bajo peso":imc<25?"Normal":imc<30?"Sobrepeso":"Obesidad";
imcData[imcIndex.value]={c:imcCurp.value,p:peso.value,a:altura.value,i:imc,cl:clas};
localStorage.setItem("imc",JSON.stringify(imcData));
mostrarIMC(); limpiarIMC();
}
function eliminarIMC(i){
imcData.splice(i,1);
localStorage.setItem("imc",JSON.stringify(imcData));
mostrarIMC();
}
function mostrarIMC(){
tablaIMC.innerHTML="";
imcData.forEach((x,i)=>{
tablaIMC.innerHTML+=`<tr>
<td>${x.c}</td><td>${x.i}</td><td>${x.cl}</td>
<td><button onclick="editarIMC(${i})">Editar</button>
<button class="delete" onclick="eliminarIMC(${i})">Eliminar</button></td>
</tr>`;
});
}
mostrarIMC();
</script>

</body>
</html>
