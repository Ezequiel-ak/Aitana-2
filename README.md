# Aitana-2
Mi universo
<html>
<head>
<meta charset="UTF-8">
<title>Universo Luna Aitana</title>
<style>
body{margin:0;overflow:hidden;background:black;font-family:Arial;}
#inicio{
position:absolute;
width:100%;height:100%;
display:flex;flex-direction:column;
justify-content:center;align-items:center;
background:black;color:white;z-index:10;
text-align:center;
}
button{
padding:20px 40px;font-size:22px;
background:white;border:none;
border-radius:10px;cursor:pointer;
}
h1{margin-bottom:30px;}
canvas{display:block;}
</style>
</head>
<body>

<div id="inicio">
<h1>¿Quieres conocer mi universo?</h1>
<button onclick="abrirUniverso()">Toca para abrir</button>
</div>

<audio id="musica" src="musica.mp3"></audio>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

<script>

let scene,camera,renderer;
let fotos=[];
let angle=0;
let viaje=true;

function abrirUniverso(){
document.getElementById("inicio").style.display="none";
document.getElementById("musica").play();
init();
animate();
}

function init(){

scene=new THREE.Scene();
scene.fog=new THREE.FogExp2(0x000000,0.00025);

camera=new THREE.PerspectiveCamera(60,window.innerWidth/window.innerHeight,1,20000);
camera.position.set(0,2000,8000);

renderer=new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth,window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio);
document.body.appendChild(renderer.domElement);

window.addEventListener('resize',()=>{
camera.aspect=window.innerWidth/window.innerHeight;
camera.updateProjectionMatrix();
renderer.setSize(window.innerWidth,window.innerHeight);
});

crearNebulosa();
crearEstrellas();
crearAgujeroNegro();
crearFotos();
crearNombre();
activarZoom();
}

function crearNebulosa(){
const geometry=new THREE.SphereGeometry(15000,64,64);
const texture=new THREE.TextureLoader().load(
"https://threejs.org/examples/textures/planets/starfield.jpg"
);
const material=new THREE.MeshBasicMaterial({
map:texture,
side:THREE.BackSide
});
const mesh=new THREE.Mesh(geometry,material);
scene.add(mesh);
}

function crearEstrellas(){
const geometry=new THREE.BufferGeometry();
let vertices=[];
for(let i=0;i<15000;i++){
vertices.push(
THREE.MathUtils.randFloatSpread(20000),
THREE.MathUtils.randFloatSpread(20000),
THREE.MathUtils.randFloatSpread(20000)
);
}
geometry.setAttribute('position',
new THREE.Float32BufferAttribute(vertices,3));
const material=new THREE.PointsMaterial({
color:0xffffff,size:2
});
const stars=new THREE.Points(geometry,material);
scene.add(stars);
}

let disco;
function crearAgujeroNegro(){

const coreGeo=new THREE.SphereGeometry(600,64,64);
const coreMat=new THREE.MeshBasicMaterial({color:0x000000});
const core=new THREE.Mesh(coreGeo,coreMat);
scene.add(core);

const ringGeo=new THREE.TorusGeometry(900,200,32,200);
const ringMat=new THREE.MeshBasicMaterial({
color:0xffaa33,
transparent:true,
opacity:0.9
});
disco=new THREE.Mesh(ringGeo,ringMat);
disco.rotation.x=Math.PI/2;
scene.add(disco);

const glowGeo=new THREE.SphereGeometry(1300,64,64);
const glowMat=new THREE.MeshBasicMaterial({
color:0xff8800,
transparent:true,
opacity:0.15
});
const glow=new THREE.Mesh(glowGeo,glowMat);
scene.add(glow);
}

function crearFotos(){
const loader=new THREE.TextureLoader();
for(let i=1;i<=9;i++){
let tex=loader.load("foto"+i+".jpg");
tex.minFilter=THREE.LinearFilter;
let mat=new THREE.MeshBasicMaterial({
map:tex,
side:THREE.DoubleSide
});
let geo=new THREE.PlaneGeometry(800,800);
let mesh=new THREE.Mesh(geo,mat);
scene.add(mesh);
fotos.push(mesh);
}
}

function crearNombre(){
const loader=new THREE.FontLoader();
loader.load(
"https://threejs.org/examples/fonts/helvetiker_regular.typeface.json",
function(font){
const geo=new THREE.TextGeometry("Luna Aitana",{
font:font,size:300,height:20
});
const mat=new THREE.MeshBasicMaterial({color:0xffffff});
const text=new THREE.Mesh(geo,mat);
text.position.set(-1500,0,0);
scene.add(text);
text.userData.orbit=true;
fotos.push(text);
});
}

function activarZoom(){
document.addEventListener('wheel',e=>{
camera.position.z+=e.deltaY*0.5;
});
let startDist;
document.addEventListener('touchmove',function(e){
if(e.touches.length==2){
let dx=e.touches[0].clientX-e.touches[1].clientX;
let dy=e.touches[0].clientY-e.touches[1].clientY;
let dist=Math.sqrt(dx*dx+dy*dy);
if(startDist){
camera.position.z+=(startDist-dist)*2;
}
startDist=dist;
}
});
}

function animate(){
requestAnimationFrame(animate);

angle+=0.002;
if(disco)disco.rotation.z+=0.01;

if(viaje){
camera.position.z-=30;
if(camera.position.z<=3000)viaje=false;
}

for(let i=0;i<fotos.length;i++){
let radius=2000+i*300;
fotos[i].position.x=Math.cos(angle+i)*radius;
fotos[i].position.z=Math.sin(angle+i)*radius;
fotos[i].lookAt(0,0,0);
}

renderer.render(scene,camera);
}

</script>
</body>
</html>
