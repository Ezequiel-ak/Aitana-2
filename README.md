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

<script src="https://cdn.jsdelivr.net/npm/three@0.158.0/build/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.158.0/examples/js/controls/OrbitControls.js"></script>

<script>

let scene,camera,renderer,planets=[],controls,starField;

function startUniverse(){

document.getElementById("intro").style.display="none";
document.getElementById("name").style.display="block";
document.getElementById("music").play();

scene=new THREE.Scene();

camera=new THREE.PerspectiveCamera(75,window.innerWidth/window.innerHeight,0.1,20000);
camera.position.set(0,200,2500);

renderer=new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth,window.innerHeight);
renderer.setPixelRatio(window.devicePixelRatio);
document.body.appendChild(renderer.domElement);

/* CONTROL TIPO VIDEOJUEGO */
controls=new THREE.OrbitControls(camera,renderer.domElement);
controls.enableDamping=true;
controls.dampingFactor=0.05;
controls.minDistance=400;
controls.maxDistance=10000;

/* ESTRELLAS PROFUNDAS */
createStars();

/* AGUJERO NEGRO */
createBlackHole();

/* PLANETAS */
createPlanets();

/* VIAJE INICIAL */
animateIntro();
}

function createStars(){
const geometry=new THREE.BufferGeometry();
const vertices=[];
for(let i=0;i<20000;i++){
vertices.push(
THREE.MathUtils.randFloatSpread(12000),
THREE.MathUtils.randFloatSpread(12000),
THREE.MathUtils.randFloatSpread(12000)
);
}
geometry.setAttribute('position',new THREE.Float32BufferAttribute(vertices,3));
const material=new THREE.PointsMaterial({color:0xffffff,size:1.2});
starField=new THREE.Points(geometry,material);
scene.add(starField);
}

function createBlackHole(){

/* NÚCLEO NEGRO */
const coreGeo=new THREE.SphereGeometry(300,64,64);
const coreMat=new THREE.MeshBasicMaterial({color:0x000000});
const core=new THREE.Mesh(coreGeo,coreMat);
scene.add(core);

/* DISCO FUEGO INTERESTELAR */
const ringGeo=new THREE.TorusGeometry(550,120,64,300);
const ringMat=new THREE.MeshBasicMaterial({color:0xff5500});
const ring=new THREE.Mesh(ringGeo,ringMat);
ring.rotation.x=Math.PI/2;
ring.name="disk";
scene.add(ring);

/* LUZ INTENSA */
const light=new THREE.PointLight(0xffaa55,4,8000);
scene.add(light);
}

function createPlanets(){
const loader=new THREE.TextureLoader();

for(let i=1;i<=9;i++){

const texture=loader.load("foto"+i+".jpg");
const geo=new THREE.SphereGeometry(120,64,64);
const mat=new THREE.MeshStandardMaterial({map:texture});
const planet=new THREE.Mesh(geo,mat);

planet.userData={
angle:Math.random()*Math.PI*2,
distance:1500+(i*500),
speed:0.0004+(i*0.0001)
};

scene.add(planet);
planets.push(planet);
}
}

function animateIntro(){
let speed=50;
function fly(){
if(camera.position.z>1200){
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

/* MOVIMIENTO ESTRELLAS */
starField.rotation.y+=0.0003;

/* ROTAR DISCO */
const disk=scene.getObjectByName("disk");
if(disk)disk.rotation.z+=0.03;

/* ORBITA PLANETAS */
planets.forEach(p=>{
p.userData.angle+=p.userData.speed;
p.position.x=Math.cos(p.userData.angle)*p.userData.distance;
p.position.z=Math.sin(p.userData.angle)*p.userData.distance;
});

/* ACTUALIZAR CONTROLES */
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
