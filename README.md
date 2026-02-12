# Aitana-2
Mi universo
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Universo Luna Aitana</title>
<style>
body{margin:0;overflow:hidden;background:black;font-family:Arial}
#intro{
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
padding:15px 30px;
font-size:18px;
border:none;
border-radius:30px;
background:linear-gradient(45deg,#ff6a00,#ff0000);
color:white;
cursor:pointer;
}
#name{
position:absolute;
top:20px;
width:100%;
text-align:center;
font-size:26px;
color:white;
display:none;
z-index:5;
}
</style>
</head>
<body>

<div id="intro">
<h1>¿Quieres conocer mi universo?</h1>
<button onclick="startUniverse()">Tocar para abrir</button>
</div>

<div id="name">Luna Aitana</div>
<audio id="music" src="musica.mp3"></audio>

<script type="module">

import * as THREE from "https://cdn.jsdelivr.net/npm/three@0.158.0/build/three.module.js";
import { OrbitControls } from "https://cdn.jsdelivr.net/npm/three@0.158.0/examples/jsm/controls/OrbitControls.js";

let scene, camera, renderer, controls, planets=[], starField;

window.startUniverse = function(){

document.getElementById("intro").style.display="none";
document.getElementById("name").style.display="block";
document.getElementById("music").play();

scene=new THREE.Scene();

camera=new THREE.PerspectiveCamera(75,window.innerWidth/window.innerHeight,0.1,50000);
camera.position.set(0,200,4000);

renderer=new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth,window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio);
document.body.appendChild(renderer.domElement);

controls=new OrbitControls(camera,renderer.domElement);
controls.enableDamping=true;
controls.enableZoom=true;
controls.minDistance=100;
controls.maxDistance=40000;

createStars();
createBlackHole();
createPlanets();
animateIntro();
}

function createStars(){
const geometry=new THREE.BufferGeometry();
const vertices=[];
for(let i=0;i<25000;i++){
vertices.push(
THREE.MathUtils.randFloatSpread(20000),
THREE.MathUtils.randFloatSpread(20000),
THREE.MathUtils.randFloatSpread(20000)
);
}
geometry.setAttribute("position",new THREE.Float32BufferAttribute(vertices,3));
const material=new THREE.PointsMaterial({color:0xffffff,size:1.2});
starField=new THREE.Points(geometry,material);
scene.add(starField);
}

function createBlackHole(){

const core=new THREE.Mesh(
new THREE.SphereGeometry(400,64,64),
new THREE.MeshBasicMaterial({color:0x000000})
);
scene.add(core);

const disk=new THREE.Mesh(
new THREE.TorusGeometry(800,200,64,300),
new THREE.MeshBasicMaterial({color:0xff5500})
);
disk.rotation.x=Math.PI/2;
disk.name="disk";
scene.add(disk);

const light=new THREE.PointLight(0xffaa55,6,20000);
scene.add(light);
}

function createPlanets(){
const loader=new THREE.TextureLoader();
for(let i=1;i<=9;i++){
const texture=loader.load("foto"+i+".jpg");
const planet=new THREE.Mesh(
new THREE.SphereGeometry(200,64,64),
new THREE.MeshStandardMaterial({map:texture})
);
planet.userData={
angle:Math.random()*Math.PI*2,
distance:3000+(i*800),
speed:0.0003+(i*0.0001)
};
scene.add(planet);
planets.push(planet);
}
}

function animateIntro(){
let speed=80;
function fly(){
if(camera.position.z>1500){
camera.position.z-=speed;
requestAnimationFrame(fly);
renderer.render(scene,camera);
}else{
animate();
}
}
fly();
}

function animate(){
requestAnimationFrame(animate);

starField.rotation.y+=0.0004;

const disk=scene.getObjectByName("disk");
if(disk)disk.rotation.z+=0.02;

planets.forEach(p=>{
p.userData.angle+=p.userData.speed;
p.position.x=Math.cos(p.userData.angle)*p.userData.distance;
p.position.z=Math.sin(p.userData.angle)*p.userData.distance;
});

controls.update();
renderer.render(scene,camera);
}

window.addEventListener("resize",()=>{
camera.aspect=window.innerWidth/window.innerHeight;
camera.updateProjectionMatrix();
renderer.setSize(window.innerWidth,window.innerHeight);
});

</script>
</body>
</html>
