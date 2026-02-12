# Aitana-2
Mi universo
<html>
<head>
<meta charset="UTF-8">
<title>Universo Luna Aitana</title>
<style>
body{
margin:0;
overflow:hidden;
background:black;
font-family:Arial;
}
#inicio{
position:absolute;
width:100%;
height:100%;
display:flex;
flex-direction:column;
justify-content:center;
align-items:center;
background:black;
color:white;
z-index:10;
}
button{
padding:20px 40px;
font-size:22px;
background:white;
border:none;
border-radius:10px;
cursor:pointer;
}
h1{
margin-bottom:30px;
}
canvas{
display:block;
}
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

let scene, camera, renderer;
let fotos = [];
let angle = 0;

function abrirUniverso(){

document.getElementById("inicio").style.display="none";
document.getElementById("musica").play();

init();
animate();
}

function init(){

scene = new THREE.Scene();

camera = new THREE.PerspectiveCamera(
60,
window.innerWidth/window.innerHeight,
1,
10000
);

camera.position.z = 2000;

renderer = new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth,window.innerHeight);
document.body.appendChild(renderer.domElement);

window.addEventListener('resize',()=>{
camera.aspect=window.innerWidth/window.innerHeight;
camera.updateProjectionMatrix();
renderer.setSize(window.innerWidth,window.innerHeight);
});

const light = new THREE.PointLight(0xffffff,2);
light.position.set(0,0,0);
scene.add(light);

crearEstrellas();
crearSol();
crearFotos();
}

function crearEstrellas(){

const geometry = new THREE.BufferGeometry();
const vertices = [];

for(let i=0;i<10000;i++){
vertices.push(
THREE.MathUtils.randFloatSpread(10000),
THREE.MathUtils.randFloatSpread(10000),
THREE.MathUtils.randFloatSpread(10000)
);
}

geometry.setAttribute(
'position',
new THREE.Float32BufferAttribute(vertices,3)
);

const material = new THREE.PointsMaterial({
color:0xffffff,
size:3
});

const stars = new THREE.Points(geometry,material);
scene.add(stars);
}

function crearSol(){

const geometry = new THREE.SphereGeometry(300,64,64);
const material = new THREE.MeshBasicMaterial({
color:0xff8800
});
const sol = new THREE.Mesh(geometry,material);
scene.add(sol);
}

function crearFotos(){

const loader = new THREE.TextureLoader();

for(let i=1;i<=9;i++){

let texture = loader.load("foto"+i+".jpg");

let material = new THREE.MeshBasicMaterial({
map:texture,
side:THREE.DoubleSide
});

let geometry = new THREE.PlaneGeometry(500,500);
let mesh = new THREE.Mesh(geometry,material);

scene.add(mesh);
fotos.push(mesh);
}
}

function animate(){

requestAnimationFrame(animate);

angle += 0.002;

for(let i=0;i<fotos.length;i++){

let radius = 1200 + i*150;

fotos[i].position.x = Math.cos(angle+i)*radius;
fotos[i].position.z = Math.sin(angle+i)*radius;
fotos[i].lookAt(0,0,0);
}

renderer.render(scene,camera);
}

</script>
</body>
</html>
