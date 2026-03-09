<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>マイクラ</title>
<style>
body{margin:0;overflow:hidden;background:#87ceeb;font-family:sans-serif;}
#crosshair{position:fixed;left:50%;top:50%;transform:translate(-50%,-50%);color:white;font-size:24px;pointer-events:none;z-index:10;display:none;}
#msg{position:fixed;top:10px;left:50%;transform:translateX(-50%);background:#0008;color:white;padding:8px 16px;border-radius:6px;z-index:10;display:none;}
#loadingUI{position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.8);display:none;justify-content:center;align-items:center;z-index:300;color:white;flex-direction:column;}
#progressBarContainer{position:fixed;bottom:160px;left:50%;transform:translateX(-50%);width:200px;height:6px;background:#0008;display:none;z-index:10;border:1px solid #fff4;}
#progressBar{width:0%;height:100%;background:#ffcc00;}
#netUI{position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.85);display:flex;justify-content:center;align-items:center;z-index:200;color:white;}
#menuContent{background:rgba(20,20,20,0.95);padding:40px;border-radius:15px;text-align:center;border:2px solid #444;width:380px;}
#menuContent h1{margin-top:0;font-size:42px;color:#4caf50;text-shadow:2px 2px #000;}
input{font-size:18px;padding:12px;width:85%;margin:10px 0;border-radius:5px;border:none;background:#333;color:white;text-align:center;outline:none;}
.large-btn{font-size:20px;padding:12px 24px;margin:10px 0;cursor:pointer;background:#4caf50;color:white;border:none;border-radius:8px;font-weight:bold;width:100%;transition:0.2s;}
.large-btn:hover{background:#66bb6a;}
.join-btn{background:#2196f3;}
#skinSelect{margin:15px 0;display:flex;gap:10px;justify-content:center;}
.skin-btn{width:35px;height:35px;border:3px solid transparent;cursor:pointer;border-radius:4px;}
.skin-btn.selected{border-color:white;box-shadow:0 0 12px white;}
#gameUI{position:fixed;bottom:20px;left:50%;transform:translateX(-50%);display:none;flex-direction:column;align-items:flex-start;gap:4px;z-index:100;pointer-events:none;}
#healthBar{display:flex;gap:1px;margin-left:2px;}
.heart{width:18px;height:18px;color:#ff0000;font-size:22px;line-height:18px;text-shadow:2px 2px #000;display:inline-block;}
.heart.empty{color:#333;}
#hotbar{display:flex;background:rgba(0,0,0,0.4);border:2px solid #333;padding:2px;pointer-events:auto;}
.slot{width:40px;height:40px;border:2px solid #555;display:flex;justify-content:center;align-items:center;background:rgba(255,255,255,0.1);position:relative;}
.slot.active{border:4px solid #ffffff; margin:-2px; z-index:1; background:rgba(255,255,255,0.2);}
.icon-box{width:20px;height:20px;}
.count{position:absolute;bottom:1px;right:2px;font-size:11px;font-weight:bold;color:white;text-shadow:1px 1px #000;}
#hostInfo{position:fixed;top:15px;right:15px;background:#000a;padding:12px;border-radius:8px;color:white;display:none;z-index:50;border:1px solid #555;}
#log{position:fixed;bottom:10px;left:10px;color:white;font-size:11px;background:#0008;padding:6px;border-radius:4px;max-width:250px;max-height:100px;overflow:auto;pointer-events:none;z-index:50;}
.settings-box { background: #222; padding: 15px; border-radius: 8px; margin: 10px 0; font-size: 16px; display: flex; flex-direction: column; align-items: center; gap: 10px; border: 1px solid #444; }
.settings-row { display: flex; align-items: center; justify-content: center; gap: 15px; width: 100%; }
.settings-box input[type="checkbox"] { width: 22px; height: 22px; margin: 0; cursor: pointer; }
.settings-box input[type="range"] { width: 60%; cursor: pointer; }
</style>
</head>
<body>

<div id="loadingUI">
    <div style="font-size:24px;margin-bottom:10px;">ワールドを読み込み中...</div>
    <div id="loadProgressText">0%</div>
</div>

<div id="netUI">
  <div id="menuContent">
    <h1>マイクラ</h1>
    <div id="commonSettings">
        <input id="userNameInput" placeholder="名前を入力" maxlength="10">
        <div id="skinSelect">
            <div class="skin-btn selected" style="background:#00acc1" onclick="setMySkin(0x00acc1, this)"></div>
            <div class="skin-btn" style="background:#e53935" onclick="setMySkin(0xe53935, this)"></div>
            <div class="skin-btn" style="background:#43a047" onclick="setMySkin(0x43a047, this)"></div>
            <div class="skin-btn" style="background:#fb8c00" onclick="setMySkin(0xfb8c00, this)"></div>
        </div>
    </div>
    <div id="mainUI">
        <button class="large-btn" onclick="showHostSettings()">ホストになる</button>
        <button class="large-btn join-btn" onclick="showJoin()">参加する</button>
    </div>
    <div id="hostSettingsUI" style="display:none">
        <h2 style="color:#ffcc00;margin-bottom:5px;font-size:20px;">ホスト設定</h2>
        <div class="settings-box">
            <div class="settings-row">
                <input type="checkbox" id="infiniteModeInput">
                <label for="infiniteModeInput" style="cursor:pointer">アイテム無限</label>
            </div>
            <div class="settings-row">
                <label>マップの広さ: <span id="chunkSizeDisplay">3</span></label>
                <input type="range" id="chunkSizeInput" min="3" max="8" value="3" oninput="document.getElementById('chunkSizeDisplay').innerText=this.value">
            </div>
        </div>
        <button class="large-btn" onclick="startHost()">ワールド作成</button>
        <button onclick="backToMenu()" style="background:none;border:none;color:#aaa;cursor:pointer;margin-top:10px;">戻る</button>
    </div>
    <div id="joinUI" style="display:none">
        <h2 style="color:#2196f3;margin-bottom:5px;font-size:20px;">サーバーへ接続</h2>
        <input id="hostIdInput" placeholder="4桁のIDを入力" maxlength="4">
        <button class="large-btn join-btn" onclick="joinHost()">接続</button>
        <button onclick="backToMenu()" style="background:none;border:none;color:#aaa;cursor:pointer;margin-top:10px;">戻る</button>
    </div>
  </div>
</div>

<div id="hostInfo">サーバーID: <span id="myId">----</span></div>
<div id="crosshair">+</div>
<div id="msg">クリックして開始</div>
<div id="progressBarContainer"><div id="progressBar"></div></div>

<div id="gameUI">
  <div id="healthBar">
      <span class="heart" id="h0">♥</span><span class="heart" id="h1">♥</span><span class="heart" id="h2">♥</span><span class="heart" id="h3">♥</span><span class="heart" id="h4">♥</span>
      <span class="heart" id="h5">♥</span><span class="heart" id="h6">♥</span><span class="heart" id="h7">♥</span><span class="heart" id="h8">♥</span><span class="heart" id="h9">♥</span>
  </div>
  <div id="hotbar">
    <div class="slot active" id="slot0"><div class="icon-box" style="background:#4caf50"></div><div class="count" id="count0">0</div></div>
    <div class="slot" id="slot1"><div class="icon-box" style="background:#8b5a2b"></div><div class="count" id="count1">0</div></div>
    <div class="slot" id="slot2"><div class="icon-box" style="background:#888888"></div><div class="count" id="count2">0</div></div>
    <div class="slot" id="slot3"><div class="icon-box" style="background:#ddd;text-align:center;color:black;font-size:14px;line-height:20px;font-weight:bold;">T</div></div>
    <div class="slot" id="slot4"><div class="icon-box" style="background:#aaa;text-align:center;color:black;font-size:14px;line-height:20px;font-weight:bold;">S</div></div>
    <div class="slot"></div><div class="slot"></div><div class="slot"></div><div class="slot"></div>
  </div>
</div>

<div id="log"></div>

<script src="https://cdn.jsdelivr.net/npm/three@0.160/build/three.min.js"></script>
<script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>
<script>
// ===== 定数と変数 =====
let CHUNK=3; 
const RENDER=2;
const PLAYER_H=1.6, GRAVITY=-15, JUMP=6, MOVE_SPEED=5;
const REACH=5, blockHardness={ grass: 1.0, dirt: 1.0, stone: 3.0, bedrock: Infinity };

let selectedSlot = 0;
let mySkinColor = 0x00acc1;
let myName = "Player";
let isInfiniteMode = false;
let myHealth = 10;
let lastDamageTime = 0;
let knockbackVel = new THREE.Vector3();
let swingTimer = 0;
let damagedFlag = false;

const inventory = [{ type: "grass", count: 0 }, { type: "dirt", count: 0 }, { type: "stone", count: 0 }, { type: "pickaxe", count: Infinity }, { type: "shovel", count: Infinity }];

function setMySkin(color, el) { mySkinColor = color; document.querySelectorAll('.skin-btn').forEach(b => b.classList.remove('selected')); el.classList.add('selected'); }
function showHostSettings() { document.getElementById("mainUI").style.display="none"; document.getElementById("hostSettingsUI").style.display="block"; }
function showJoin() { document.getElementById("mainUI").style.display="none"; document.getElementById("joinUI").style.display="block"; }
function backToMenu() { document.getElementById("mainUI").style.display="block"; document.getElementById("hostSettingsUI").style.display="none"; document.getElementById("joinUI").style.display="none"; }

// ===== Three.js 設定 =====
const scene=new THREE.Scene();
scene.background=new THREE.Color(0x87ceeb);
const camera=new THREE.PerspectiveCamera(75,window.innerWidth/window.innerHeight,0.1,1000);
scene.add(camera);
const renderer=new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth,window.innerHeight);
renderer.shadowMap.enabled=true;
document.body.appendChild(renderer.domElement);

window.addEventListener('resize', () => {
    camera.aspect = window.innerWidth / window.innerHeight; camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
});

scene.add(new THREE.AmbientLight(0xffffff,0.7));
const sun=new THREE.DirectionalLight(0xffffff,0.8);
sun.position.set(20,40,20); scene.add(sun);

const mats = {
    grass: new THREE.MeshLambertMaterial({ color: 0x4caf50 }),
    dirt: new THREE.MeshLambertMaterial({ color: 0x8b5a2b }),
    stone: new THREE.MeshLambertMaterial({ color: 0x888888 }),
    bedrock: new THREE.MeshLambertMaterial({ color: 0x222222 }),
    wood: new THREE.MeshLambertMaterial({ color: 0x6d4c41 }),
    iron: new THREE.MeshLambertMaterial({ color: 0xeeeeee }),
    skin: new THREE.MeshLambertMaterial({ color: 0xffcc99 }),
    hair: new THREE.MeshLambertMaterial({ color: 0x4d3319 }),
    pants: new THREE.MeshLambertMaterial({ color: 0x3c44aa }),
    eye: new THREE.MeshBasicMaterial({ color: 0x000000 })
};

const selectionGroup = new THREE.Group();
const selectionGeo = new THREE.EdgesGeometry(new THREE.BoxGeometry(1.001, 1.001, 1.001));
const selectionMat = new THREE.LineBasicMaterial({ color: 0x000000 });
selectionGroup.add(new THREE.LineSegments(selectionGeo, selectionMat));
scene.add(selectionGroup);

const handGroup = new THREE.Group();
camera.add(handGroup);
const handMesh = new THREE.Mesh(new THREE.BoxGeometry(0.12, 0.12, 0.4), mats.skin);
handMesh.position.set(0.45, -0.45, -0.6);
handGroup.add(handMesh);

function updateHeldItem() {
    handGroup.children.forEach(c => { if(c !== handMesh) handGroup.remove(c); });
    const item = inventory[selectedSlot];
    if (isInfiniteMode || item.count > 0 || item.count === Infinity) {
        let model;
        if(item.type === "pickaxe") {
            model = new THREE.Group();
            const p1 = new THREE.Mesh(new THREE.BoxGeometry(0.04, 0.3, 0.04), mats.wood);
            const p2 = new THREE.Mesh(new THREE.BoxGeometry(0.3, 0.05, 0.05), mats.iron); p2.position.y = 0.1;
            model.add(p1, p2);
        } else if(item.type === "shovel") {
            model = new THREE.Group();
            const s1 = new THREE.Mesh(new THREE.BoxGeometry(0.04, 0.3, 0.04), mats.wood);
            const s2 = new THREE.Mesh(new THREE.BoxGeometry(0.1, 0.1, 0.01), mats.iron); s2.position.y = 0.12;
            model.add(s1, s2);
        } else {
            model = new THREE.Mesh(new THREE.BoxGeometry(0.25, 0.25, 0.25), mats[item.type]);
        }
        model.position.set(0.45, -0.3, -0.7); model.rotation.set(0.5, 0, 0);
        handGroup.add(model);
    }
}

const blocks=new Map();
const boxGeo=new THREE.BoxGeometry(1,1,1);
function addBlock(x,y,z,type,send=true){
  const k=`${x},${y},${z}`; if(blocks.has(k)) return;
  const mesh=new THREE.Mesh(boxGeo, mats[type]); mesh.position.set(x+0.5,y+0.5,z+0.5); scene.add(mesh); blocks.set(k,{mesh,type});
  if(send){ const d = {type:"addBlock",x,y,z,blockType:type}; if(isHost) sendToAll(d); else sendToHost(d); }
}
function removeBlock(x,y,z,send=true){
  const k=`${x},${y},${z}`; const b=blocks.get(k); if(!b) return;
  scene.remove(b.mesh); blocks.delete(k);
  if(send){ const d = {type:"break",x,y,z}; if(isHost) sendToAll(d); else sendToHost(d); }
}

function updateUI() { 
    inventory.forEach((s, i) => { const el = document.getElementById(`count${i}`); if(el) el.textContent = (isInfiniteMode || s.count === Infinity) ? "∞" : s.count; }); 
    for(let i=0; i<10; i++) { const h = document.getElementById(`h${i}`); if(myHealth > i) h.classList.remove("empty"); else h.classList.add("empty"); }
}

let isHost=false, peer=null, myId=null;
const connections={}; const otherPlayers={}; const playerMeshes={};
let worldLoaded=false;
function log(msg){const l=document.getElementById("log");l.innerHTML+=msg+"<br>";l.scrollTop=l.scrollHeight;}

function startHost(){
    myName = document.getElementById("userNameInput").value.trim() || "Host";
    isInfiniteMode = document.getElementById("infiniteModeInput").checked;
    CHUNK = parseInt(document.getElementById("chunkSizeInput").value);
    const id4 = Math.floor(1000 + Math.random() * 9000).toString();
    if(peer) peer.destroy();
    peer = new Peer(id4);
    peer.on("open", id => { isHost = true; myId = id; document.getElementById("myId").textContent = id; startGame(); });
    peer.on("connection", conn => {
        conn.on("open", () => {
            connections[conn.peer] = conn;
            const world=[]; for(const [k,v] of blocks){ const [x,y,z]=k.split(",").map(Number); world.push({x,y,z,type:v.type}); }
            conn.send({type:"world", world, settings: {isInfiniteMode}});
        });
        conn.on("data", data => handleData(data, conn.peer));
        conn.on("close", () => removeOtherPlayer(conn.peer));
    });
}

function joinHost(){
    const id = document.getElementById("hostIdInput").value.trim();
    if(id.length !== 4) return alert("4桁のIDを入力してください");
    myName = document.getElementById("userNameInput").value.trim() || "Player";
    document.getElementById("myId").textContent = id;
    document.getElementById("loadingUI").style.display = "flex"; // 読み込み開始表示
    if(peer) peer.destroy();
    peer = new Peer();
    peer.on("open", selfId => {
        myId = selfId;
        const conn = peer.connect(id, {reliable:true});
        conn.on("open", () => { connections[id] = conn; });
        conn.on("data", data => handleData(data, id));
        conn.on("error", () => { alert("接続に失敗しました"); location.reload(); });
    });
}

async function handleData(data, senderId) {
    if(data.id === myId) return;
    if(data.type === "world") {
        if(data.settings) isInfiniteMode = data.settings.isInfiniteMode;
        // 古いブロックを削除
        for(const m of blocks.values()) scene.remove(m.mesh); blocks.clear();
        
        // 【重要】分割して読み込み（フリーズ防止）
        const total = data.world.length;
        for(let i=0; i<total; i++) {
            const v = data.world[i];
            addBlock(v.x, v.y, v.z, v.type, false);
            if(i % 300 === 0) { // 300個ごとに1フレーム空ける
                document.getElementById("loadProgressText").innerText = Math.floor((i/total)*100) + "%";
                await new Promise(r => setTimeout(r, 16)); 
            }
        }
        document.getElementById("loadingUI").style.display = "none";
        worldLoaded = true; 
        startGame();
        updateUI(); updateHeldItem();
    } else if(data.type === "player") {
        const pId = data.id || senderId;
        otherPlayers[pId] = data;
        if(!playerMeshes[pId]) createOtherPlayer(pId, data.name, data.color);
        if(data.damaged) flashPlayerRed(pId);
        if(isHost) sendToAll(data, senderId);
    } else if(data.type === "addBlock") {
        addBlock(data.x, data.y, data.z, data.blockType, false);
        if(isHost) sendToAll(data, senderId);
    } else if(data.type === "break") {
        removeBlock(data.x, data.y, data.z, false);
        if(isHost) sendToAll(data, senderId);
    } else if(data.type === "attack") {
        if(data.target === myId) takeDamage(data.dmg, data.attackerPos);
        if(isHost) sendToAll(data, senderId);
    }
}

function flashPlayerRed(id) {
    const m = playerMeshes[id]; if(!m) return;
    m.group.traverse(c => { if(c.isMesh && c.material && c.material.emissive) c.material.emissive.set(0xff0000); });
    setTimeout(() => { if(m && m.group) m.group.traverse(c => { if(c.isMesh && c.material && c.material.emissive) c.material.emissive.set(0x000000); }); }, 200);
}

function sendToAll(data, excludePeer=null){ for(let id in connections) { if(id !== excludePeer) connections[id].send(data); } }
function sendToHost(data){ const c = Object.values(connections)[0]; if(c) c.send(data); }

function takeDamage(amount, attackerPos = null) {
    if(myHealth <= 0) return;
    myHealth -= amount;
    lastDamageTime = performance.now();
    damagedFlag = true;
    if(attackerPos) {
        const dir = new THREE.Vector3().subVectors(camera.position, attackerPos).normalize();
        knockbackVel.add(dir.multiplyScalar(8));
        velocityY = 4;
    }
    updateUI();
    if(myHealth <= 0) { 
        log("死亡しました！"); 
        setTimeout(() => { 
            myHealth = 10; velocityY = 0;
            camera.position.set(0.5, 2.5, 0.5); playerY = 2.5 + PLAYER_H;
            updateUI(); 
        }, 1000); 
    }
}

function startGame() {
    document.getElementById("netUI").style.display="none"; document.getElementById("hostInfo").style.display="block";
    document.getElementById("gameUI").style.display="flex"; document.getElementById("crosshair").style.display="block";
    updateUI(); updateHeldItem();
    camera.position.set(0.5, 2.5, 0.5); playerY = 2.5 + PLAYER_H;
    if(isHost) { generateWorld(); worldLoaded = true; }
}

function generateWorld(){ 
    const limit = RENDER * CHUNK;
    for(let x=-limit; x<=limit; x++){ 
        for(let z=-limit; z<=limit; z++){ 
            [{y:-1,type:"grass"},{y:-2,type:"dirt"},{y:-3,type:"stone"},{y:-4,type:"stone"},{y:-5,type:"stone"},{y:-6,type:"bedrock"}].forEach(l=>addBlock(x,l.y,z,l.type,false)); 
        } 
    } 
}

function createOtherPlayer(peerId, name, shirtColor){
    if(playerMeshes[peerId]) return;
    const group = new THREE.Group();
    group.userData.peerId = peerId;
    const shirtMat = new THREE.MeshLambertMaterial({color: shirtColor});
    const T = 0.25;

    const hitbox = new THREE.Mesh(new THREE.BoxGeometry(1.0, 1.9, 1.0), new THREE.MeshBasicMaterial({ visible: false }));
    hitbox.position.y = 0.95; hitbox.userData.isHitbox = true; hitbox.userData.peerId = peerId;
    group.add(hitbox);

    const body = new THREE.Mesh(new THREE.BoxGeometry(0.45, 0.75, T), shirtMat); body.position.y = 1.125; group.add(body);
    const headJoint = new THREE.Group();
    headJoint.add(new THREE.Mesh(new THREE.BoxGeometry(0.4, 0.4, 0.4), mats.skin.clone()));
    const hair = new THREE.Mesh(new THREE.BoxGeometry(0.42, 0.15, 0.42), mats.hair.clone()); hair.position.y = 0.15; headJoint.add(hair);
    const eyeG = new THREE.BoxGeometry(0.08, 0.04, 0.01);
    const eL = new THREE.Mesh(eyeG, mats.eye.clone()); eL.position.set(-0.1, 0, -0.201);
    const eR = new THREE.Mesh(eyeG, mats.eye.clone()); eR.position.set(0.1, 0, -0.201);
    headJoint.add(eL, eR); headJoint.position.y = 1.7; group.add(headJoint);
    const legLJoint = new THREE.Group(); const legL = new THREE.Mesh(new THREE.BoxGeometry(0.22, 0.75, T), mats.pants.clone()); legL.position.y = -0.375; legLJoint.add(legL); legLJoint.position.set(-0.115, 0.75, 0); group.add(legLJoint);
    const legRJoint = new THREE.Group(); const legR = new THREE.Mesh(new THREE.BoxGeometry(0.22, 0.75, T), mats.pants.clone()); legR.position.y = -0.375; legRJoint.add(legR); legRJoint.position.set(0.115, 0.75, 0); group.add(legRJoint);
    const armLJoint = new THREE.Group(); const armL = new THREE.Mesh(new THREE.BoxGeometry(0.22, 0.75, T), shirtMat.clone()); armL.position.y = -0.375; armLJoint.add(armL); armLJoint.position.set(-0.335, 1.5, 0); group.add(armLJoint);
    const armRJoint = new THREE.Group(); const armR = new THREE.Mesh(new THREE.BoxGeometry(0.22, 0.75, T), shirtMat.clone()); armR.position.y = -0.375; armRJoint.add(armR); armRJoint.position.set(0.335, 1.5, 0); group.add(armRJoint);
    const label = createNameLabel(name); label.position.y = 2.1; group.add(label);
    scene.add(group); playerMeshes[peerId] = {group, head: headJoint, legL: legLJoint, legR: legRJoint, armL: armLJoint, armR: armRJoint};
}
function createNameLabel(text) {
    const canvas = document.createElement("canvas"); canvas.width = 256; canvas.height = 64; const ctx = canvas.getContext("2d"); ctx.fillStyle = "rgba(0,0,0,0.5)"; ctx.fillRect(0,0,256,64); ctx.fillStyle = "white"; ctx.font = "bold 32px Arial"; ctx.textAlign = "center"; ctx.fillText(text, 128, 45);
    const sprite = new THREE.Sprite(new THREE.SpriteMaterial({map:new THREE.CanvasTexture(canvas)})); sprite.scale.set(1.2, 0.3, 1); return sprite;
}
function removeOtherPlayer(id) { if(playerMeshes[id]){ scene.remove(playerMeshes[id].group); delete playerMeshes[id]; delete otherPlayers[id]; } }

// ===== メインループ =====
let playerY=PLAYER_H+2, velocityY=0, onGround=false, prevTime=performance.now();
let yaw=0, pitch=0, locked=false;
const raycaster=new THREE.Raycaster(); let breaking=null;

function checkStuck(x, y, z) {
    const ix = Math.floor(x), iz = Math.floor(z);
    return blocks.has(`${ix},${Math.floor(y-PLAYER_H+0.1)},${iz}`) || blocks.has(`${ix},${Math.floor(y-0.1)},${iz}`);
}

function animate(){
  requestAnimationFrame(animate);
  const now = performance.now(), delta = Math.min((now-prevTime)/1000, 0.1); prevTime=now;
  if(!worldLoaded) { renderer.render(scene, camera); return; } // ワールド未読込時は描画のみ

  let isMining = false; 
  if(locked && myHealth > 0){
    if(myHealth < 10 && now - lastDamageTime > 5000) { if(Math.floor(now/2000) > Math.floor(prevTime/2000)) { myHealth = Math.min(10, myHealth+1); updateUI(); } }
    
    const fwd=new THREE.Vector3(-Math.sin(yaw),0,-Math.cos(yaw)), rt=new THREE.Vector3(Math.cos(yaw),0,-Math.sin(yaw)), move=new THREE.Vector3();
    if(keys["KeyW"]) move.add(fwd); if(keys["KeyS"]) move.sub(fwd); if(keys["KeyA"]) move.sub(rt); if(keys["KeyD"]) move.add(rt);
    if(move.length()>0) move.normalize().multiplyScalar(MOVE_SPEED*delta);
    move.add(knockbackVel.clone().multiplyScalar(delta)); knockbackVel.multiplyScalar(0.85);
    
    let nx=camera.position.x+move.x, nz=camera.position.z+move.z;
    if(!checkStuck(nx, playerY, camera.position.z)) camera.position.x=nx;
    if(!checkStuck(camera.position.x, playerY, nz)) camera.position.z=nz;

    if(checkStuck(camera.position.x, playerY, camera.position.z)) {
        const ix = Math.floor(camera.position.x), iz = Math.floor(camera.position.z);
        const fx = camera.position.x - ix, fz = camera.position.z - iz;
        if(Math.abs(fx-0.5) > Math.abs(fz-0.5)) camera.position.x += (fx < 0.5 ? -0.1 : 0.1);
        else camera.position.z += (fz < 0.5 ? -0.1 : 0.1);
    }
    
    const preVelY = velocityY; velocityY += GRAVITY * delta; playerY += velocityY * delta;
    if(blocks.has(`${Math.floor(camera.position.x)},${Math.floor(playerY-PLAYER_H)},${Math.floor(camera.position.z)}`)){ 
        if(preVelY < -12) takeDamage(Math.floor(Math.abs(preVelY)-10));
        velocityY=0; playerY=Math.floor(playerY-PLAYER_H)+1+PLAYER_H; onGround=true; 
    } else onGround=false;
    if(keys["Space"] && onGround){velocityY=JUMP; onGround=false;}
    if(playerY < -30) takeDamage(10);

    if(swingTimer > 0) { swingTimer -= delta * 5; handGroup.rotation.x = -Math.sin(swingTimer * Math.PI) * 1.2; } 
    else { handGroup.rotation.x = 0; }

    raycaster.setFromCamera(new THREE.Vector2(0,0),camera);
    const hits=raycaster.intersectObjects([...blocks.values()].map(b=>b.mesh));
    if(hits.length>0 && hits[0].distance<=REACH){
      const hit=hits[0].object; selectionGroup.visible = true; selectionGroup.position.copy(hit.position);
      let tK = null, tD = null; for(const [k,v] of blocks){ if(v.mesh === hit){ tK = k; tD = v; break; } }
      if(mouseDown && tK && tD.type !== "bedrock") {
        isMining = true;
        const [tx, ty, tz] = tK.split(",").map(Number);
        if(!breaking || breaking.x !== tx || breaking.y !== ty || breaking.z !== tz) { breaking = {x:tx, y:ty, z:tz, type:tD.type, progress:0}; document.getElementById("progressBarContainer").style.display="block"; }
        let speed = (inventory[selectedSlot].type === "pickaxe" && tD.type === "stone") || (inventory[selectedSlot].type === "shovel" && (tD.type === "grass" || tD.type === "dirt")) ? 2.0 : 1.0;
        breaking.progress += delta * speed; const ratio = breaking.progress / blockHardness[tD.type];
        document.getElementById("progressBar").style.width = Math.min(ratio*100, 100) + "%";
        if(ratio >= 1) { const invSlot = inventory.find(inv => inv.type === tD.type); if(invSlot) { invSlot.count++; updateUI(); updateHeldItem(); } removeBlock(tx, ty, tz); cancelBreaking(); }
        handGroup.rotation.x = -Math.abs(Math.sin(now * 0.012)) * 0.6;
      } else { if(!mouseDown) cancelBreaking(); }
    } else { selectionGroup.visible = false; cancelBreaking(); }
  }

  for(const id in playerMeshes){
    const p=otherPlayers[id]; const m=playerMeshes[id]; if(!p) continue;
    m.group.position.lerp(new THREE.Vector3(p.x, p.y-1.6, p.z), 0.2); m.group.rotation.y = p.yaw; m.head.rotation.x = p.pitch;
    const s = Math.sin(now * 0.012);
    if(p.moving){ m.legL.rotation.x = s * 0.7; m.legR.rotation.x = -s * 0.7; } else { m.legL.rotation.x = m.legR.rotation.x = 0; }
    if(p.digging){ m.armR.rotation.x = 0 + Math.abs(Math.sin(now * 0.015)) * 1.0; m.armL.rotation.x = 0; }
    else if(p.moving){ m.armL.rotation.x = -s * 0.7; m.armR.rotation.x = s * 0.7; }
    else { m.armL.rotation.x = m.armR.rotation.x = 0; }
  }
  camera.position.y = playerY; if(peer && myId) sendPlayerState(isMining); renderer.render(scene,camera);
}
animate();
function cancelBreaking() { breaking = null; document.getElementById("progressBarContainer").style.display = "none"; }
const keys={}; let mouseDown=false;
document.body.addEventListener("click",(e)=>{ if(worldLoaded && e.target.tagName !== "BUTTON" && e.target.tagName !== "INPUT") renderer.domElement.requestPointerLock(); });
document.addEventListener("pointerlockchange",()=>{ locked=document.pointerLockElement===renderer.domElement; document.getElementById("msg").style.display=(locked || !worldLoaded)?"none":"block"; });
document.addEventListener("mousemove",e=>{ if(!locked) return; yaw-=e.movementX*0.002; pitch-=e.movementY*0.002; pitch=Math.max(-Math.PI/2,Math.min(Math.PI/2,pitch)); camera.rotation.order="YXZ"; camera.rotation.y=yaw; camera.rotation.x=pitch; });
document.addEventListener("keydown",e=>{ keys[e.code]=true; if(e.code.startsWith("Digit")){ let n=parseInt(e.code.replace("Digit",""))-1; if(n>=0&&n<5) setSlot(n); } });
document.addEventListener("keyup",e=>keys[e.code]=false);
function setSlot(index) { document.getElementById("slot"+selectedSlot).classList.remove("active"); selectedSlot=index; document.getElementById("slot"+selectedSlot).classList.add("active"); updateHeldItem(); }
document.addEventListener("wheel", e => { if(!locked) return; let n=selectedSlot+(e.deltaY>0?1:-1); if(n<0) n=4; if(n>4) n=0; setSlot(n); });

document.addEventListener("mousedown", e => {
    if(!locked) return;
    if(e.button === 0) {
        mouseDown = true; swingTimer = 1.0;
        raycaster.setFromCamera(new THREE.Vector2(0,0), camera);
        raycaster.far = 5.0; 
        const playerObjects = Object.values(playerMeshes).map(m => m.group);
        const pHits = raycaster.intersectObjects(playerObjects, true);
        if(pHits.length > 0) {
            let targetId = null;
            let obj = pHits[0].object;
            while(obj) { if(obj.userData.peerId) { targetId = obj.userData.peerId; break; } obj = obj.parent; }
            if(targetId && targetId !== myId) {
                const atk = {type:"attack", target:targetId, dmg:1, attackerPos:camera.position};
                if(isHost) handleData(atk, myId); else sendToHost(atk);
            }
        }
    }
    if(e.button === 2) placeBlock();
});
document.addEventListener("mouseup", e => { if(e.button === 0) mouseDown = false; });
document.addEventListener("contextmenu", e => e.preventDefault());

function placeBlock() {
    const item = inventory[selectedSlot]; if (!isInfiniteMode && (item.count <= 0 || item.count === Infinity && selectedSlot > 2)) return; if (selectedSlot > 2) return; 
    raycaster.setFromCamera(new THREE.Vector2(0,0),camera);
    const hits=raycaster.intersectObjects([...blocks.values()].map(b=>b.mesh));
    if(hits.length>0 && hits[0].distance<=REACH){
        const hit = hits[0]; const pos = hit.object.position.clone().add(hit.face.normal);
        const bx = Math.floor(pos.x), by = Math.floor(pos.y), bz = Math.floor(pos.z);
        if(bx === Math.floor(camera.position.x) && bz === Math.floor(camera.position.z) && (by === Math.floor(playerY-0.5) || by === Math.floor(playerY+0.5))) return;
        addBlock(bx, by, bz, item.type); if(!isInfiniteMode) item.count--; updateUI(); updateHeldItem();
    }
}
function sendPlayerState(digging){ 
    const isMoving = (keys["KeyW"] || keys["KeyS"] || keys["KeyA"] || keys["KeyD"]) && locked;
    const d = {type:"player",id:myId,x:camera.position.x,y:playerY,z:camera.position.z,yaw,pitch,color:mySkinColor,name:myName,moving:isMoving,digging:digging || swingTimer > 0, damaged: damagedFlag}; 
    damagedFlag = false; if(isHost) sendToAll(d); else sendToHost(d); 
}
</script>
</body>
</html>
