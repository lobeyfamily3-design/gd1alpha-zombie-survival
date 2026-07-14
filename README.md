<!DOCTYPE html>
<html>
<head>
<title>Zombie Survival - Gd 1 Alpha</title>

<style>
body{
    margin:0;
    background:#111;
    color:white;
    font-family:Arial;
    text-align:center;
    overflow:hidden;
}

canvas{
    background:#222;
    border:3px solid white;
}

#creator{
    position:absolute;
    top:10px;
    left:50%;
    transform:translateX(-50%);
    background:black;
    border:2px solid cyan;
    padding:8px 20px;
    border-radius:10px;
    color:cyan;
    font-size:20px;
}

#ui{
    font-size:20px;
}

button{
    padding:10px;
    margin:5px;
    cursor:pointer;
}
</style>
</head>

<body>

<div id="creator">
Made with Gd 1 Alpha
</div>

<h1>🧟 Zombie Survival</h1>

<div id="ui">
❤️ Health: 100 |
🔫 Ammo: 30 |
💰 Coins: 0 |
🧟 Kills: 0 |
🌊 Wave: 1
</div>

<button onclick="upgradeGun()">Upgrade Gun (50 Coins)</button>

<br>

<canvas id="game" width="800" height="500"></canvas>


<script>

const canvas=document.getElementById("game");
const ctx=canvas.getContext("2d");


let player={
x:400,
y:250,
speed:5,
health:100,
ammo:30,
damage:1
};


let zombies=[];
let bullets=[];


let coins=0;
let kills=0;
let wave=1;


let keys={};
let mouse={
x:400,
y:250
};



document.addEventListener("keydown",e=>{
keys[e.key.toLowerCase()]=true;
});


document.addEventListener("keyup",e=>{
keys[e.key.toLowerCase()]=false;
});


canvas.addEventListener("mousemove",e=>{
let r=canvas.getBoundingClientRect();
mouse.x=e.clientX-r.left;
mouse.y=e.clientY-r.top;
});


canvas.addEventListener("click",()=>{

if(player.ammo<=0)return;

let a=Math.atan2(
mouse.y-player.y,
mouse.x-player.x
);


bullets.push({
x:player.x,
y:player.y,
dx:Math.cos(a)*10,
dy:Math.sin(a)*10
});


player.ammo--;

});



function spawnZombie(){

let boss = wave%5==0;

zombies.push({

x:Math.random()*800,
y:Math.random()*500,

hp:boss?10:2,

speed:boss?0.8:1.5,

size:boss?50:25,

boss:boss

});

}



setInterval(()=>{

if(zombies.length<5+wave*2)
spawnZombie();

},800);



function upgradeGun(){

if(coins>=50){

coins-=50;
player.damage++;

}

}



function update(){


// movement

if(keys["w"])player.y-=player.speed;
if(keys["s"])player.y+=player.speed;
if(keys["a"])player.x-=player.speed;
if(keys["d"])player.x+=player.speed;



// bullets

bullets.forEach(b=>{

b.x+=b.dx;
b.y+=b.dy;

});


// zombies

zombies.forEach(z=>{

let a=Math.atan2(
player.y-z.y,
player.x-z.x
);

z.x+=Math.cos(a)*z.speed;
z.y+=Math.sin(a)*z.speed;


if(
Math.abs(z.x-player.x)<z.size &&
Math.abs(z.y-player.y)<z.size
){

player.health-=0.3;

}

});



// hit detection

for(let i=zombies.length-1;i>=0;i--){

for(let j=bullets.length-1;j>=0;j--){

let z=zombies[i];
let b=bullets[j];


if(
Math.abs(z.x-b.x)<z.size &&
Math.abs(z.y-b.y)<z.size
){

z.hp-=player.damage;

bullets.splice(j,1);


if(z.hp<=0){

zombies.splice(i,1);

kills++;
coins+=5;


if(kills%10==0)
wave++;

}

break;

}

}

}



if(player.health<=0){

alert(
"Game Over!\nKills: "+kills
);

location.reload();

}


document.getElementById("ui").innerHTML=

"❤️ Health: "+Math.floor(player.health)+
" | 🔫 Ammo: "+player.ammo+
" | 💰 Coins: "+coins+
" | 🧟 Kills: "+kills+
" | 🌊 Wave: "+wave;


}



function draw(){

ctx.clearRect(0,0,800,500);


// player

ctx.fillStyle="cyan";

ctx.fillRect(
player.x-15,
player.y-15,
30,
30
);


// zombies

zombies.forEach(z=>{

ctx.fillStyle=z.boss?"purple":"green";

ctx.fillRect(
z.x-z.size/2,
z.y-z.size/2,
z.size,
z.size
);

});


// bullets

ctx.fillStyle="yellow";

bullets.forEach(b=>{

ctx.fillRect(
b.x,
b.y,
6,
6
);

});

}



function loop(){

update();
draw();

requestAnimationFrame(loop);

}

loop();

</script>

</body>
</html>
