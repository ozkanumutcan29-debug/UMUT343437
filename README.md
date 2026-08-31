<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Gün Batımı Adası</title>

<style>
*{box-sizing:border-box}
html,body{
 margin:0;width:100%;height:100%;overflow:hidden;
 background:#07111d;color:white;
 font-family:Arial,sans-serif;
 touch-action:none
}
#game{position:fixed;inset:0}

#loading{
 position:fixed;inset:0;z-index:100;
 display:grid;place-items:center;
 background:linear-gradient(#07182b,#794e43);
}
.loadingBox{
 padding:28px;text-align:center;
 background:#0009;border:1px solid #ffffff33;
 border-radius:22px
}

#hud{position:fixed;inset:0;pointer-events:none}
#stats{
 position:absolute;top:12px;left:12px;
 padding:11px 14px;
 background:#07121ddd;
 border:1px solid #ffffff25;
 border-radius:15px;
 line-height:1.55;font-weight:bold
}

#buttons{
 position:absolute;right:12px;top:12px;
 display:flex;flex-wrap:wrap;
 justify-content:flex-end;gap:7px;
 max-width:330px;pointer-events:auto
}

button{
 color:white;background:#10243add;
 border:1px solid #ffffff35;
 border-radius:12px;
 padding:10px 12px;
 font-weight:bold
}

#crosshair{
 position:absolute;left:50%;top:50%;
 width:20px;height:20px;
 transform:translate(-50%,-50%);
 opacity:.85
}
#crosshair:before,#crosshair:after{
 content:"";position:absolute;background:white
}
#crosshair:before{width:2px;height:20px;left:9px}
#crosshair:after{height:2px;width:20px;top:9px}

#joystick{
 position:absolute;left:18px;bottom:22px;
 width:140px;height:140px;
 border-radius:50%;
 background:#ffffff12;
 border:2px solid #ffffff25;
 pointer-events:auto
}
#stick{
 position:absolute;left:44px;top:44px;
 width:52px;height:52px;
 border-radius:50%;
 background:#ffffff42;
 border:1px solid #ffffff55
}

.action{
 position:absolute;
 width:64px;height:64px;
 border-radius:50%;
 border:1px solid #ffffff40;
 background:#12283ddd;
 color:white;
 pointer-events:auto;
 font-size:10px;font-weight:bold
}
#fire{right:95px;bottom:160px;background:#762f2bdd}
#jump{right:22px;bottom:160px}
#crouch{right:95px;bottom:84px}
#prone{right:22px;bottom:84px}

#hint{
 position:absolute;left:12px;bottom:8px;
 font-size:11px;opacity:.7
}

#win{
 position:fixed;inset:0;z-index:200;
 display:none;place-items:center;
 background:#000b
}
.winbox{
 padding:30px;
 border-radius:22px;
 background:#10263df5;
 text-align:center;
 border:1px solid #ffffff30
}
</style>
</head>

<body>

<div id="game"></div>

<div id="loading">
 <div class="loadingBox">
  <h2>🌅 Gün Batımı Adası</h2>
  <p>3D ada hazırlanıyor...</p>
 </div>
</div>

<div id="hud">

 <div id="stats">
  ❤️ Can: <span id="hp">10 / 10</span><br>
  🐍 Kalan yılan: <span id="snakes">20</span><br>
  🔫 Mermi: ∞
 </div>

 <div id="buttons">
  <button id="quality">HDR</button>
  <button id="camera">Bakış: 3</button>
  <button id="time">🌅 Zaman</button>
 </div>

 <div id="crosshair"></div>

 <div id="joystick">
  <div id="stick"></div>
 </div>

 <button id="fire" class="action">ATEŞ</button>
 <button id="jump" class="action">ZIPLA</button>
 <button id="crouch" class="action">EĞİL</button>
 <button id="prone" class="action">UZAN</button>

 <div id="hint">
  Joystick: hareket • ekranı sürükle: kamera
 </div>

</div>

<div id="win">
 <div class="winbox">
  <h1>🏆 KAZANDIN!</h1>
  <p>20 yılanın tamamını yendin.</p>
  <button onclick="location.reload()">Tekrar Oyna</button>
 </div>
</div>


<script type="module">

import * as THREE from
"https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js";


/* =========================
   SAHNE
========================= */

const scene=new THREE.Scene();

const camera=new THREE.PerspectiveCamera(
 68,
 innerWidth/innerHeight,
 .1,
 700
);

const renderer=new THREE.WebGLRenderer({
 antialias:true,
 powerPreference:"high-performance"
});

renderer.setSize(
 innerWidth,
 innerHeight
);

renderer.setPixelRatio(
 Math.min(devicePixelRatio,1.8)
);

renderer.outputColorSpace=
 THREE.SRGBColorSpace;

renderer.toneMapping=
 THREE.ACESFilmicToneMapping;

renderer.toneMappingExposure=1.2;

document
 .getElementById("game")
 .appendChild(renderer.domElement);


/* =========================
   IŞIK
========================= */

const hemi=
 new THREE.HemisphereLight(
  0xb9dcff,
  0x38532e,
  1.4
 );

scene.add(hemi);


const sun=
 new THREE.DirectionalLight(
  0xffad63,
  2.5
 );

sun.position.set(
 -100,
 80,
 -100
);

scene.add(sun);


/* =========================
   MALZEMELER
========================= */

function mat(color,roughness=.85){

 return new THREE.MeshStandardMaterial({
  color:color,
  roughness:roughness
 });

}

const sand=mat(0xd9bd83);
const grass=mat(0x48783e);
const trunk=mat(0x765039);
const leaves=mat(0x2e6c38);


/* =========================
   DENİZ
========================= */

const waterMat=
 new THREE.MeshPhysicalMaterial({

  color:0x08789a,

  roughness:.16,

  metalness:.05,

  transparent:true,

  opacity:.9
 });


const sea=
 new THREE.Mesh(
  new THREE.CircleGeometry(
   160,
   160
  ),
  waterMat
 );

sea.rotation.x=
 -Math.PI/2;

sea.position.y=-1;

scene.add(sea);


/* =========================
   ADA
========================= */

const islandRadius=65;


const island=
 new THREE.Mesh(
  new THREE.CylinderGeometry(
   islandRadius,
   islandRadius*1.04,
   2,
   112
  ),
  sand
 );

island.position.y=0;

scene.add(island);


const islandGrass=
 new THREE.Mesh(
  new THREE.CylinderGeometry(
   58,
   60,
   1.1,
   112
  ),
  grass
 );

islandGrass.position.y=1;

scene.add(islandGrass);


/* KUMSAL */

const beach=
 new THREE.Mesh(
  new THREE.TorusGeometry(
   60,
   4,
   16,
   112
  ),
  sand
 );

beach.rotation.x=
 -Math.PI/2;

beach.position.y=1.08;

scene.add(beach);


/* =========================
   PALMİYELER
========================= */

function createPalm(
 x,z,scale=1
){

 const group=
  new THREE.Group();

 group.position.set(
  x,
  1,
  z
 );


 const stem=
  new THREE.Mesh(
   new THREE.CylinderGeometry(
    .38*scale,
    .7*scale,
    8*scale,
    14
   ),
   trunk
  );

 stem.position.y=
  4*scale;

 group.add(stem);


 const crown=
  new THREE.Group();

 crown.position.y=
  8*scale;


 for(let i=0;i<12;i++){

  const a=
   i*Math.PI*2/12;

  const leaf=
   new THREE.Mesh(
    new THREE.BoxGeometry(
     .14*scale,
     .09*scale,
     6.5*scale
    ),
    leaves
   );

  leaf.position.set(
   Math.sin(a)*2.7*scale,
   0,
   Math.cos(a)*2.7*scale
  );

  leaf.rotation.y=a;

  leaf.rotation.x=-.16;

  crown.add(leaf);
 }

 group.add(crown);

 scene.add(group);
}


for(let i=0;i<36;i++){

 const a=
  Math.random()*Math.PI*2;

 const r=
  16+Math.random()*40;

 createPalm(
  Math.cos(a)*r,
  Math.sin(a)*r,
  .7+Math.random()*.6
 );
}


/* =========================
   ÇİMEN
========================= */

for(let i=0;i<400;i++){

 const a=
  Math.random()*Math.PI*2;

 const r=
  Math.sqrt(Math.random())*55;

 const blade=
  new THREE.Mesh(
   new THREE.ConeGeometry(
    .05,
    .25+Math.random()*.35,
    4
   ),
   mat(0x70a050)
  );

 blade.position.set(
  Math.cos(a)*r,
  1.22,
  Math.sin(a)*r
 );

 scene.add(blade);
}


/* =========================
   KARAKTER
========================= */

const player=
 new THREE.Group();

scene.add(player);

player.position.set(
 0,1,25
);


/* gömlek */

const body=
 new THREE.Mesh(
  new THREE.BoxGeometry(
   1.05,
   1.4,
   .66
  ),
  mat(0x277fbd)
 );

body.position.y=2.1;

player.add(body);


/* pantolon */

const pants=
 new THREE.Mesh(
  new THREE.BoxGeometry(
   1.08,
   .9,
   .68
  ),
  mat(0x3f7338)
 );

pants.position.y=1;

player.add(pants);


/* bacaklar */

for(
 const x of [-.26,.26]
){

 const leg=
  new THREE.Mesh(
   new THREE.BoxGeometry(
    .33,
    .86,
    .36
   ),
   mat(0x3f7338)
  );

 leg.position.set(
  x,
  .36,
  0
 );

 player.add(leg);
}


/* kafa */

const head=
 new THREE.Mesh(
  new THREE.SphereGeometry(
   .5,
   24,
   18
  ),
  mat(0xb97e58)
 );

head.position.y=3.22;

player.add(head);


/* saç */

const hair=
 new THREE.Mesh(
  new THREE.SphereGeometry(
   .53,
   24,
   16,
   0,
   Math.PI*2,
   0,
   Math.PI*.55
  ),
  mat(0x181411)
 );

hair.position.y=3.33;

player.add(hair);


/* gözler */

for(
 const x of [-.16,.16]
){

 const eye=
  new THREE.Mesh(
   new THREE.SphereGeometry(
    .09,
    12,
    10
   ),
   mat(0xffffff)
  );

 eye.position.set(
  x,
  3.22,
  .44
 );

 player.add(eye);


 const pupil=
  new THREE.Mesh(
   new THREE.SphereGeometry(
    .04,
    8,
    8
   ),
   mat(0x111111)
  );

 pupil.position.set(
  x,
  3.22,
  .51
 );

 player.add(pupil);
}


/* =========================
   YILANLAR
========================= */

const snakes=[];


function createSnake(){

 const snake=
  new THREE.Group();


 const snakeColor=
  [
   0x65532e,
   0x4c6839,
   0x75482f,
   0x565b42
  ][
   Math.floor(
    Math.random()*4
   )
  ];


 const bodyMat=
  mat(snakeColor,.72);


 /* KAFA */

 const head=
  new THREE.Mesh(
   new THREE.SphereGeometry(
    .52,
    20,
    14
   ),
   bodyMat
  );

 head.scale.set(
  1.2,
  .82,
  1.4
 );

 snake.add(head);


 /* GÖVDE */

 for(let i=1;i<8;i++){

  const segment=
   new THREE.Mesh(
    new THREE.SphereGeometry(
     .36,
     16,
     11
    ),
    bodyMat
   );

  segment.position.z=
   i*.5;

  segment.scale.set(
   1.08,
   .82,
   1.08
  );

  snake.add(segment);
 }


 /* GÖZLER */

 for(
  const x of [-.2,.2]
 ){

  const eye=
   new THREE.Mesh(
    new THREE.SphereGeometry(
     .085,
     12,
     10
    ),
    mat(0xffffff)
   );

  eye.position.set(
   x,
   -.08,
   -.45
  );

  snake.add(eye);


  const pupil=
   new THREE.Mesh(
    new THREE.SphereGeometry(
     .04,
     8,
     8
    ),
    mat(0x050505)
   );

  pupil.position.set(
   x,
   -.08,
   -.51
  );

  snake.add(pupil);
 }


 /* DİKKAT:
    Yılan artık çimlerin
    ÜSTÜNDE duruyor.
 */

 const a=
  Math.random()*Math.PI*2;

 const r=
  10+Math.random()*43;


 snake.position.set(
  Math.cos(a)*r,
  1.55,
  Math.sin(a)*r
 );


 snake.userData={
  alive:true,
  speed:1.1+
   Math.random()*.9,
  direction:
   new THREE.Vector3(),
  change:
   Math.random()*2
 };


 scene.add(snake);

 snakes.push(snake);
}


for(let i=0;i<20;i++)
 createSnake();


/* =========================
   GÜNEŞ / AY
========================= */

const sunDisk=
 new THREE.Mesh(
  new THREE.SphereGeometry(
   5,
   32,
   32
  ),
  new THREE.MeshBasicMaterial({
   color:0xff9d2e
  })
 );

scene.add(sunDisk);


const moon=
 new THREE.Mesh(
  new THREE.SphereGeometry(
   3.7,
   32,
   32
  ),
  mat(0xe8e8df,1)
 );

scene.add(moon);


/* yıldızlar */

const starGeo=
 new THREE.BufferGeometry();

const positions=[];


for(let i=0;i<600;i++){

 const r=
  190+Math.random()*90;

 const a=
  Math.random()*Math.PI*2;

 const b=
  Math.random()*Math.PI*.5;


 positions.push(
  Math.cos(a)*r*Math.cos(b),
  45+Math.sin(b)*r,
  Math.sin(a)*r*Math.cos(b)
 );
}


starGeo.setAttribute(
 "position",
 new THREE.Float32BufferAttribute(
  positions,
  3
 )
);


const stars=
 new THREE.Points(
  starGeo,
  new THREE.PointsMaterial({
   color:0xffffff,
   size:.8,
   transparent:true,
   opacity:0
  })
 );

scene.add(stars);


/* =========================
   OYUNCU DEĞİŞKENLERİ
========================= */

let hp=10;

let remaining=20;

let yaw=0;

let pitch=-.18;

let cameraMode=3;

let velocityY=0;

let grounded=true;

let crouched=false;

let prone=false;

let swimming=false;

let shootCooldown=0;

let damageCooldown=0;

let gameTime=.72;

let moveX=0;

let moveY=0;

const keys={};


/* =========================
   KLAVYE
========================= */

addEventListener(
 "keydown",
 e=>{

  keys[e.key.toLowerCase()]=true;

  if(e.code==="Space")
   jump();

  if(e.key.toLowerCase()==="f")
   shoot();

  if(
   ["1","2","3"].includes(e.key)
  ){

   cameraMode=
    Number(e.key);

  }
 }
);


addEventListener(
 "keyup",
 e=>{
  keys[e.key.toLowerCase()]=false;
 }
);


/* =========================
   ZIPLAMA
========================= */

function jump(){

 if(
  grounded &&
  !swimming &&
  !prone
 ){

  velocityY=7;

  grounded=false;
 }
}


/* =========================
   ATEŞ
========================= */

function shoot(){

 if(
  shootCooldown>0 ||
  remaining===0
 )
 return;


 shootCooldown=.2;


 const origin=
  camera.position.clone();


 const direction=
  new THREE.Vector3(
   0,
   0,
   -1
  )
  .applyQuaternion(
   camera.quaternion
  )
  .normalize();


 let target=null;

 let best=Infinity;


 for(
  const snake of snakes
 ){

  if(
   !snake.userData.alive
  )
   continue;


  const vector=
   snake.position
   .clone()
   .sub(origin);


  const distance=
   vector.dot(direction);


  if(distance<0)
   continue;


  const point=
   origin.clone()
   .addScaledVector(
    direction,
    distance
   );


  const miss=
   point.distanceTo(
    snake.position
   );


  if(
   miss<1.7 &&
   distance<best
  ){

   target=snake;

   best=distance;
  }
 }


 if(target){

  target.userData.alive=false;

  target.visible=false;

  remaining--;

  document
   .getElementById("snakes")
   .textContent=
   remaining;


  if(remaining===0){

   document
    .getElementById("win")
    .style.display=
    "grid";
  }
 }
}


/* =========================
   YILANLAR
========================= */

function updateSnakes(dt){

 for(
  const snake of snakes
 ){

  if(
   !snake.userData.alive
  )
   continue;


  const vector=
   player.position
   .clone()
   .sub(
    snake.position
   );

  vector.y=0;


  const distance=
   vector.length();


  if(distance<14){

   vector.normalize();


   snake.userData.direction.lerp(
    vector,
    dt*2.7
   );


   if(
    distance<2.6 &&
    damageCooldown<=0
   ){

    damageCooldown=5;

    hp--;


    document
     .getElementById("hp")
     .textContent=
     hp+" / 10";


    if(hp<=0){

     hp=10;

     player.position.set(
      0,
      1,
      25
     );


     document
      .getElementById("hp")
      .textContent=
      "10 / 10";
    }
   }

  }else{

   snake.userData.change-=dt;


   if(
    snake.userData.change<=0
   ){

    snake.userData.change=
     2+Math.random()*3;


    snake.userData.direction.set(
     Math.random()-.5,
     0,
     Math.random()-.5
    ).normalize();
   }
  }


  snake.position.addScaledVector(
   snake.userData.direction,
   snake.userData.speed*dt
  );


  snake.position.x=
   Math.max(
    -57,
    Math.min(
     57,
     snake.position.x
    )
   );


  snake.position.z=
   Math.max(
    -57,
    Math.min(
     57,
     snake.position.z
    )
   );


  snake.position.y=
   1.55+
   Math.sin(
    performance.now()*.006
   )*.035;


  snake.rotation.y=
   Math.atan2(
    snake.userData.direction.x,
    snake.userData.direction.z
   );
 }
}


/* =========================
   OYUNCU
========================= */

function updatePlayer(dt){

 const forward=
  new THREE.Vector3(
   Math.sin(yaw),
   0,
   Math.cos(yaw)
  );


 const right=
  new THREE.Vector3(
   Math.cos(yaw),
   0,
   -Math.sin(yaw)
  );


 let x=moveX;

 let y=moveY;


 if(keys.w)y-=1;
 if(keys.s)y+=1;
 if(keys.a)x-=1;
 if(keys.d)x+=1;


 const direction=
  new THREE.Vector3()
  .addScaledVector(
   right,
   x
  )
  .addScaledVector(
   forward,
   y
  );


 if(direction.length()>1)
  direction.normalize();


 const speed=
  prone ? 1.8 :
  crouched ? 3 :
  5.2;


 player.position.addScaledVector(
  direction,
  speed*dt
 );


 const distance=
  Math.hypot(
   player.position.x,
   player.position.z
  );


 swimming=
  distance>53;


 if(swimming){

  player.position.y=.35;

  velocityY=0;

  grounded=false;

 }else{

  velocityY-=17*dt;

  player.position.y+=
   velocityY*dt;


  if(
   player.position.y<1
  ){

   player.position.y=1;

   velocityY=0;

   grounded=true;
  }
 }


 const max=
  swimming ? 148 : 61;


 if(distance>max){

  player.position.x*=
   max/distance;

  player.position.z*=
   max/distance;
 }


 player.rotation.y=yaw;


 player.scale.y=
  prone ? .42 :
  crouched ? .72 :
  1;


 updateCamera();
}


/* =========================
   KAMERA
========================= */

function updateCamera(){

 const target=
  player.position.clone();


 target.y+=
  prone ? .6 :
  crouched ? 1.4 :
  2;


 let distance;


 if(cameraMode===1)
  distance=4.5;

 else if(cameraMode===2)
  distance=-4.5;

 else
  distance=7.5;


 const pos=
  target.clone();


 pos.x+=
  Math.sin(yaw)*distance;


 pos.z+=
  Math.cos(yaw)*distance;


 pos.y+=
  2.1+pitch*4;


 camera.position.lerp(
  pos,
  .17
 );


 camera.lookAt(
  target
 );
}


/* =========================
   JOYSTICK
========================= */

const joystick=
 document.getElementById(
  "joystick"
 );

const stick=
 document.getElementById(
  "stick"
 );

let joyActive=false;


joystick.addEventListener(
 "pointerdown",
 e=>{

  joyActive=true;

  joystick.setPointerCapture(
   e.pointerId
  );

  joystickMove(e);
 }
);


joystick.addEventListener(
 "pointermove",
 e=>{

  if(joyActive)
   joystickMove(e);
 }
);


joystick.addEventListener(
 "pointerup",
 resetJoystick
);


joystick.addEventListener(
 "pointercancel",
 resetJoystick
);


function joystickMove(e){

 const rect=
  joystick.getBoundingClientRect();


 let dx=
  e.clientX-
  (
   rect.left+
   rect.width/2
  );


 let dy=
  e.clientY-
  (
   rect.top+
   rect.height/2
  );


 const length=
  Math.hypot(dx,dy);


 if(length>45){

  dx=dx/length*45;

  dy=dy/length*45;
 }


 stick.style.transform=
  `translate(${dx}px,${dy}px)`;


 moveX=dx/45;

 moveY=dy/45;
}


function resetJoystick(){

 joyActive=false;

 moveX=0;

 moveY=0;

 stick.style.transform=
  "translate(0,0)";
}


/* =========================
   KAMERA DOKUNMATİK
========================= */

let dragging=false;

let lastX=0;

let lastY=0;


renderer.domElement.addEventListener(
 "pointerdown",
 e=>{

  if(
   e.clientX<180 &&
   e.clientY>innerHeight-190
  )
   return;


  dragging=true;

  lastX=e.clientX;

  lastY=e.clientY;
 }
);


renderer.domElement.addEventListener(
 "pointermove",
 e=>{

  if(!dragging)
   return;


  const dx=
   e.clientX-lastX;

  const dy=
   e.clientY-lastY;


  lastX=e.clientX;

  lastY=e.clientY;


  yaw-=dx*.006;

  pitch-=dy*.004;


  pitch=
   Math.max(
    -1.05,
    Math.min(
     .45,
     pitch
    )
   );
 }
);


renderer.domElement.addEventListener(
 "pointerup",
 ()=>dragging=false
);


renderer.domElement.addEventListener(
 "pointercancel",
 ()=>dragging=false
);


/* =========================
   BUTONLAR
========================= */

document
 .getElementById("fire")
 .onclick=shoot;


document
 .getElementById("jump")
 .onclick=jump;


document
 .getElementById("crouch")
 .onclick=()=>{
  crouched=!crouched;

  if(crouched)
   prone=false;
 };


document
 .getElementById("prone")
 .onclick=()=>{
  prone=!prone;

  if(prone)
   crouched=false;
 };


document
 .getElementById("camera")
 .onclick=()=>{

  cameraMode++;

  if(cameraMode>3)
   cameraMode=1;


  document
   .getElementById("camera")
   .textContent=
   "Bakış: "+
   cameraMode;
 };


let hdr=true;


document
 .getElementById("quality")
 .onclick=()=>{

  hdr=!hdr;


  renderer.setPixelRatio(
   Math.min(
    devicePixelRatio,
    hdr?2.1:1.3
   )
  );


  renderer.toneMappingExposure=
   hdr?1.35:1.08;


  document
   .getElementById("quality")
   .textContent=
   hdr?"HDR":"HD";
 };


document
 .getElementById("time")
 .onclick=()=>{
  gameTime=
   (gameTime+.25)%1;
 };


/* =========================
   GECE GÜNDÜZ
========================= */

function updateTime(dt){

 gameTime=
  (gameTime+dt*.005)%1;


 const angle=
  gameTime*Math.PI*2;


 sun.position.set(
  Math.cos(angle)*115,
  Math.sin(angle)*92,
  -75
 );


 sunDisk.position.copy(
  sun.position
 );


 moon.position.set(
  -Math.cos(angle)*105,
  -Math.sin(angle)*75+65,
  65
 );


 const night=
  Math.max(
   0,
   Math.min(
    1,
    (.1-Math.sin(angle))/.45
   )
  );


 stars.material.opacity=
  night*.9;


 moon.visible=
  night>.08;


 const dayColor=
  new THREE.Color(
   0x9b6248
  );


 const nightColor=
  new THREE.Color(
   0x050b18
  );


 scene.background=
  dayColor.lerp(
   nightColor,
   night
  );


 scene.fog.color=
  scene.background;


 hemi.intensity=
  .45+
  Math.max(
   0,
   Math.sin(angle)
  )*1.15;


 sun.intensity=
  .25+
  Math.max(
   0,
   Math.sin(angle)
  )*2.4;
}


/* =========================
   BAŞLAT
========================= */

document
 .getElementById("loading")
 .style.display=
 "none";


let last=
 performance.now();


function loop(now){

 requestAnimationFrame(loop);


 const dt=
  Math.min(
   .033,
   (now-last)/1000
  );


 last=now;


 shootCooldown=
  Math.max(
   0,
   shootCooldown-dt
  );


 damageCooldown=
  Math.max(
   0,
   damageCooldown-dt
  );


 updatePlayer(dt);

 updateSnakes(dt);

 updateTime(dt);


 renderer.render(
  scene,
  camera
 );
}


loop(
 performance.now()
);


/* =========================
   EKRAN
========================= */

addEventListener(
 "resize",
 ()=>{

  camera.aspect=
   innerWidth/innerHeight;

  camera.updateProjectionMatrix();

  renderer.setSize(
   innerWidth,
   innerHeight
  );
 }
);

</script>

</body>
</html>
