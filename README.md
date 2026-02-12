# Aitana-2
Mi universo
<html>
<head>
<meta charset="UTF-8">
<title>Universo Luna Aitana</title>
<style>
body{margin:0;overflow:hidden;background:black;}
#inicio{
position:absolute;width:100%;height:100%;
display:flex;flex-direction:column;
justify-content:center;align-items:center;
background:black;color:white;z-index:10;
font-family:Arial;text-align:center;
}
button{
padding:20px 40px;font-size:22px;
background:white;border:none;
border-radius:10px;cursor:pointer;
}
h1{margin-bottom:30px;}
</style>
</head>
<body>

<div id="inicio">
<h1>¿Quieres conocer mi universo?</h1>
<button onclick="abrir()">Toca para abrir</button>
</div>

<audio id="musica" src="musica.mp3"></audio>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

<script>

let scene,camera,renderer;
let fotos=[];
let angle=0;
let viaje=true;

function abrir(){
document.getElementById("inicio").style.display="none";
document.getElementById("musica").play();
init();
animate();
}

function init(){

scene=new THREE.Scene();

camera=new THREE.PerspectiveCamera(65,window.innerWidth/window.innerHeight,1,50000);
camera.position.set(0,0,10000);

renderer=new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth,window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio);
document.body.appendChild(renderer.domElement);

window.addEventListener('resize',()=>{
camera.aspect=window.innerWidth/window.innerHeight;
camera.updateProjectionMatrix();
renderer.setSize(window.innerWidth,window.innerHeight);
});

crearEstrellas();
crearNebulosa();
crearAgujeroNegro();
crearFotos();
crearNombre();
activarZoom();
}

function crearEstrellas(){

const geometry=new THREE.BufferGeometry();
let vertices=[];

for(let i=0;i<30000;i++){
let r=THREE.MathUtils.randFloat(5000,20000);
let theta=Math.random()*Math.PI*2;
let phi=Math.random()*Math.PI;
vertices.push(
r*Math.sin(phi)*Math.cos(theta),
r*Math.sin(phi)*Math.sin(theta),
r*Math.cos(phi)
);
}

geometry.setAttribute('position',
new THREE.Float32BufferAttribute(vertices,3));

const material=new THREE.PointsMaterial({
color:0xffffff,size:3
});

const stars=new THREE.Points(geometry,material);
scene.add(stars);
}

function crearNebulosa(){

const geometry=new THREE.BufferGeometry();
let vertices=[];
let colors=[];

for(let i=0;i<8000;i++){
let x=THREE.MathUtils.randFloatSpread(8000);
let y=THREE.MathUtils.randFloatSpread(8000);
let z=THREE.MathUtils.randFloatSpread(8000);
vertices.push(x,y,z);

let color=new THREE.Color();
color.setHSL(0.75,1,0.5+Math.random()*0.5);
colors.push(color.r,color.g,color.b);
}

geometry.setAttribute('position',
new THREE.Float32BufferAttribute(vertices,3));
geometry.setAttribute('color',
new THREE.Float32BufferAttribute(colors,3));

const material=new THREE.PointsMaterial({
size:20,
vertexColors:true,
transparent:true,
opacity:0.4
});

const nebula=new THREE.Points(geometry,material);
scene.add(nebula);
}

let disco;
function crearAgujeroNegro(){

const coreGeo=new THREE.SphereGeometry(900,64,64);
const coreMat=new THREE.MeshBasicMaterial({color:0x000000});
const core=new THREE.Mesh(coreGeo,coreMat);
scene.add(core);

const ringGeo=new THREE.TorusGeometry(1500,300,64,200);
const ringMat=new THREE.MeshBasicMaterial({
color:0xff6600,
transparent:true,
opacity:0.9
});
disco=new THREE.Mesh(ringGeo,ringMat);
disco.rotation.x=Math.PI/2;
scene.add(disco);

const haloGeo=new THREE.SphereGeometry(2500,64,64);
const haloMat=new THREE.MeshBasicMaterial({
color:0xaa33ff,
transparent:true,
opacity:0.15
});
const halo=new THREE.Mesh(haloGeo,haloMat);
scene.add(halo);
}

function crearFotos(){
const loader=new THREE.TextureLoader();
for(let i=1;i<=9;i++){
let tex=loader.load("foto"+i+".jpg");
let mat=new THREE.MeshBasicMaterial({map:tex});
let geo=new THREE.PlaneGeometry(1200,1200);
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
font:font,size:400,height:20
});
const mat=new THREE.MeshBasicMaterial({color:0xffffff});
const text=new THREE.Mesh(geo,mat);
scene.add(text);
fotos.push(text);
});
}

function activarZoom(){
document.addEventListener('wheel',e=>{
camera.position.z+=e.deltaY*0.5;
});
}

function animate(){
requestAnimationFrame(animate);

angle+=0.002;

if(viaje){
camera.position.z-=40;
if(camera.position.z<=6000)viaje=false;
}

if(disco)disco.rotation.z+=0.01;

for(let i=0;i<fotos.length;i++){
let radius=4000+i*600;
fotos[i].position.x=Math.cos(angle+i)*radius;
fotos[i].position.z=Math.sin(angle+i)*radius;
fotos[i].lookAt(0,0,0);
}

renderer.render(scene,camera);
}

</script>
</body>
</html>
