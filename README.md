# Futbol-simulatoru-D3
Futbol oyunudur uzun Şut pas normal Şut ve Turbo Şut vardır 13.1 sürümündedir yakında yeni sürüm gelecektir
```html
<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>3D Futbol Simülatörü - v13.1 Kararlı Sürüm</title>
    <!-- Web'den Gerçekçi İkonlar için FontAwesome Eklendi -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #000; 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            touch-action: none; 
            user-select: none;
            -webkit-user-select: none;
        }

        #canvas-container {
            width: 100%;
            height: 100%;
            display: block;
        }

        /* --- UI ARAYÜZÜ --- */
        #ui-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
        }

        .header-info {
            position: absolute;
            top: 15px;
            left: 15px;
            color: white;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.8);
        }
        .header-info h1 { margin: 0; font-size: 22px; color: #FFD700; display: flex; align-items: center; gap: 8px;}
        .header-info p { margin: 2px 0 0 0; font-size: 13px; font-weight: bold; color: #e0e0e0; }

        .score-board {
            position: absolute;
            top: 15px;
            left: 50%;
            transform: translateX(-50%);
            background: linear-gradient(135deg, #111, #333);
            color: #4CAF50;
            padding: 8px 30px;
            border-radius: 10px;
            font-size: 26px;
            font-weight: 900;
            border: 2px solid #fff;
            box-shadow: 0 4px 10px rgba(0,0,0,0.5);
            text-shadow: 1px 1px 2px #000;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        /* Üst Buton Grubu */
        #top-buttons {
            position: absolute;
            top: 15px;
            right: 15px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            pointer-events: auto;
        }

        .sys-btn {
            background: #2196F3;
            color: #fff;
            border: 2px solid #0b7dda;
            padding: 10px 15px;
            border-radius: 8px;
            font-weight: bold;
            font-size: 13px;
            cursor: pointer;
            box-shadow: 0 4px 6px rgba(0,0,0,0.3);
            transition: transform 0.1s, background 0.2s;
            text-align: left;
            display: flex; align-items: center; gap: 8px;
            width: 170px;
        }
        .sys-btn i { font-size: 16px; width: 20px; text-align: center; }
        .sys-btn:active { transform: scale(0.95); background: #0b7dda; }
        
        #btn-mode { background: #9C27B0; border-color: #6a1b9a; }
        #btn-mode:active { background: #7b1fa2; }

        #btn-var {
            background: #FF0000;
            border-color: #aa0000;
            animation: pulse 2s infinite;
        }
        #btn-var:active { background: #aa0000; }

        @keyframes pulse {
            0% { box-shadow: 0 0 0 0 rgba(255, 0, 0, 0.7); }
            70% { box-shadow: 0 0 0 10px rgba(255, 0, 0, 0); }
            100% { box-shadow: 0 0 0 0 rgba(255, 0, 0, 0); }
        }

        /* --- ŞUT GÜÇ BARI --- */
        #shot-bar-container {
            position: absolute;
            bottom: 220px;
            left: 50%;
            transform: translateX(-50%);
            width: 60%;
            max-width: 300px;
            height: 25px;
            background: rgba(0, 0, 0, 0.7);
            border: 3px solid white;
            border-radius: 15px;
            display: none;
            overflow: hidden;
            box-shadow: 0 0 15px rgba(0,0,0,0.5);
        }
        #shot-bar-fill {
            width: 0%; height: 100%;
            background: linear-gradient(90deg, #4CAF50 0%, #FFEB3B 50%, #F44336 100%);
            transition: width 0.05s linear;
        }
        #shot-bar-text {
            position: absolute; width: 100%; text-align: center; top: 2px;
            font-size: 14px; font-weight: bold; color: white; text-shadow: 1px 1px 2px black;
        }

        /* VAR EKRAN UYARISI */
        #var-overlay {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            border: 10px solid #FF0000;
            box-sizing: border-box;
            display: none;
            pointer-events: auto;
            background: rgba(0,0,0,0.3);
        }
        #var-text {
            position: absolute;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: black;
            color: white;
            padding: 5px 20px;
            font-size: 24px;
            font-weight: bold;
            border: 2px solid white;
            letter-spacing: 2px;
            pointer-events: none;
            display: flex; align-items: center; gap: 10px;
        }
        
        #var-stats {
            position: absolute;
            top: 75px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0, 0, 0, 0.8);
            color: #FFD700;
            padding: 5px 15px;
            font-size: 16px;
            font-weight: bold;
            border-radius: 5px;
            pointer-events: none;
        }

        #var-slider {
            position: absolute;
            bottom: 40px;
            left: 10%;
            width: 80%;
            height: 15px;
            cursor: pointer;
            z-index: 10;
        }

        #freekick-indicator {
            position: absolute;
            top: 30%;
            width: 100%;
            text-align: center;
            color: white;
            font-size: 20px;
            font-weight: bold;
            background: rgba(0,0,0,0.5);
            padding: 10px;
            border-radius: 10px;
            text-shadow: 2px 2px 5px rgba(0,0,0,1);
            display: none;
            pointer-events: none;
            animation: fadeInOut 1.5s infinite alternate;
        }

        @keyframes fadeInOut {
            from { opacity: 0.6; transform: scale(0.95); }
            to { opacity: 1; transform: scale(1.05); }
        }

        /* --- KONTROLLER (JOYSTICK & BUTONLAR) --- */
        #controls-container {
            position: absolute; bottom: 0; left: 0; width: 100%; height: 200px; pointer-events: none;
        }

        #joystick-zone {
            position: absolute; bottom: 30px; left: 30px; width: 140px; height: 140px;
            background: rgba(255, 255, 255, 0.1); border: 3px solid rgba(255, 255, 255, 0.4);
            border-radius: 50%; pointer-events: auto; touch-action: none; cursor: pointer;
        }
        #joystick-knob {
            position: absolute; width: 60px; height: 60px;
            background: radial-gradient(circle, rgba(255,255,255,0.95) 0%, rgba(200,200,200,0.8) 100%);
            border-radius: 50%; top: 50%; left: 50%; transform: translate(-50%, -50%);
            box-shadow: 0 4px 10px rgba(0,0,0,0.5);
        }

        #action-buttons {
            position: absolute; bottom: 30px; right: 30px;
            display: grid; grid-template-columns: 1fr 1fr; gap: 15px; pointer-events: auto;
        }

        .action-btn {
            background: rgba(0, 0, 0, 0.65); color: white; border: 2px solid rgba(255, 255, 255, 0.5);
            border-radius: 50%; width: 70px; height: 70px; font-size: 11px; font-weight: bold;
            display: flex; flex-direction: column; align-items: center; justify-content: center; text-align: center;
            cursor: pointer; backdrop-filter: blur(5px); box-shadow: 0 4px 6px rgba(0,0,0,0.3);
            touch-action: none; gap: 4px;
        }
        .action-btn i { font-size: 20px; }
        .action-btn.active { background: rgba(255, 255, 255, 0.9); color: black; transform: scale(0.9); }
        
        #btn-turbo { 
            background: rgba(220, 20, 60, 0.85); border-color: #ffaaaa; 
            grid-column: span 2; width: 100%; border-radius: 35px; height: 60px;
            flex-direction: row; font-size: 14px;
        }
        #btn-turbo i { font-size: 24px; margin-right: 5px; }
        #btn-turbo.active { background: #ff0000; color: white; }

        #goal-alert {
            position: absolute; top: 25%; left: 50%; transform: translate(-50%, -50%);
            font-size: 80px; font-weight: 900; color: #FFD700;
            text-shadow: 0px 0px 20px #FF0000, 4px 4px 0px #000; display: none; pointer-events: none;
            animation: goalAnim 1s ease-in-out infinite alternate; z-index: 100;
        }

        @keyframes goalAnim {
            0% { transform: translate(-50%, -50%) scale(0.9); opacity: 0.8; }
            100% { transform: translate(-50%, -50%) scale(1.1); opacity: 1; }
        }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
</head>
<body>

    <div id="canvas-container"></div>
    
    <div id="freekick-indicator"><i class="fa-solid fa-hand-pointer"></i> Ekrana dokunarak topun yerini seçin (Frikik)</div>

    <!-- VAR Çerçevesi -->
    <div id="var-overlay">
        <div id="var-text"><i class="fa-solid fa-tv"></i> VAR İNCELEMESİ (TEKRAR OYNATIM)</div>
        <div id="var-stats">Şut Hızı: 0 km/s</div>
        <input type="range" id="var-slider" min="0" max="100" value="100">
    </div>

    <div id="ui-layer">
        <div class="header-info">
            <h1><i class="fa-solid fa-brain"></i> Futbol Pro v13.1</h1>
            <p>Rövaşata, Vole & Kafa Vuruşları Eklendi!</p>
        </div>
        <div class="score-board">
            <i class="fa-regular fa-futbol"></i> <span id="score-val">0</span>
        </div>
        
        <div id="top-buttons">
            <button class="sys-btn" id="btn-camera"><i class="fa-solid fa-video"></i> Kamera: ROBLOX</button>
            <button class="sys-btn" id="btn-mode"><i class="fa-solid fa-running"></i> Mod: Antrenman</button>
            <button class="sys-btn" id="btn-var"><i class="fa-solid fa-magnifying-glass"></i> VAR İNCELE</button>
        </div>

        <div id="shot-bar-container">
            <div id="shot-bar-fill"></div>
            <div id="shot-bar-text">ŞUT GÜCÜ</div>
        </div>

        <div id="controls-container">
            <div id="joystick-zone"><div id="joystick-knob"></div></div>
            <div id="action-buttons">
                <div class="action-btn" id="btn-pass"><i class="fa-solid fa-shoe-prints"></i>Pas</div>
                <div class="action-btn" id="btn-normal"><i class="fa-solid fa-futbol"></i>Normal</div>
                <div class="action-btn" id="btn-long"><i class="fa-solid fa-rocket"></i>Uzun</div>
                <div class="action-btn" id="btn-air"><i class="fa-solid fa-arrow-up-right-dots"></i>Aşırtma</div>
                <div class="action-btn" id="btn-turbo"><i class="fa-solid fa-fire"></i>TURBO ŞUT</div>
            </div>
        </div>
    </div>

    <div id="goal-alert">GOL!</div>

<script>
    // --- 1. THREE.JS KURULUM VE GÖKYÜZÜ ---
    const container = document.getElementById('canvas-container');
    const scene = new THREE.Scene();
    
    // Gökyüzü Gradient
    const skyCanvas = document.createElement('canvas');
    skyCanvas.width = 2; skyCanvas.height = 512;
    const skyCtx = skyCanvas.getContext('2d');
    const skyGrad = skyCtx.createLinearGradient(0,0,0,512);
    skyGrad.addColorStop(0, '#1a5ba8'); 
    skyGrad.addColorStop(0.5, '#4db8ff'); 
    skyGrad.addColorStop(1, '#a8e0ff'); 
    skyCtx.fillStyle = skyGrad;
    skyCtx.fillRect(0,0,2,512);
    const skyTex = new THREE.CanvasTexture(skyCanvas);
    scene.background = skyTex;
    scene.fog = new THREE.FogExp2(0xa8e0ff, 0.007);

    const camera = new THREE.PerspectiveCamera(55, window.innerWidth / window.innerHeight, 0.1, 1000);
    let cameraMode = 0; // 0: Serbest (Roblox) , 1: İç (Omuz Kamerası)
    let isVarMode = false;
    let savedTimeScale = 1; 
    let placingFreeKick = false;

    // YENİ MOD SİSTEMİ
    const gameModes = [
        { id: 'training', name: 'Antrenman', icon: 'fa-running' },
        { id: 'freekick', name: 'Frikik', icon: 'fa-hand-pointer' },
        { id: 'penalty', name: 'Penaltı', icon: 'fa-bullseye' },
        { id: 'match', name: 'Maç', icon: 'fa-users' },
        { id: 'referee', name: 'Hakem', icon: 'fa-user-tie' }
    ];
    let currentModeIndex = 0;
    let gameMode = gameModes[currentModeIndex].id;

    // ROBLOX Tarzı Kamera Değişkenleri
    let camYaw = 0; 
    let camPitch = 0.35; 
    let camRadius = 14; 

    // VAR Tekrar sistemi
    const replayHistory = [];
    const MAX_HISTORY_FRAMES = 360; 

    document.getElementById('btn-camera').addEventListener('click', (e) => {
        if(isVarMode) return; 
        cameraMode = cameraMode === 0 ? 1 : 0;
        e.currentTarget.innerHTML = cameraMode === 0 ? '<i class="fa-solid fa-video"></i> Kamera: ROBLOX' : '<i class="fa-solid fa-camera"></i> Kamera: İÇ (Omuz)';
    });

    document.getElementById('btn-mode').addEventListener('click', (e) => {
        if(isVarMode) return;
        currentModeIndex = (currentModeIndex + 1) % gameModes.length;
        const modeObj = gameModes[currentModeIndex];
        gameMode = modeObj.id;
        
        e.currentTarget.innerHTML = `<i class="fa-solid ${modeObj.icon}"></i> Mod: ${modeObj.name}`;
        
        placingFreeKick = (gameMode === 'freekick');
        document.getElementById('freekick-indicator').style.display = placingFreeKick ? 'block' : 'none';
        
        resetGameForMode();
    });

    const varSlider = document.getElementById('var-slider');

    document.getElementById('btn-var').addEventListener('click', () => {
        isVarMode = !isVarMode;
        const varOverlay = document.getElementById('var-overlay');
        const btnVar = document.getElementById('btn-var');
        const uiControls = document.getElementById('controls-container');
        
        if(isVarMode) {
            savedTimeScale = 0; 
            varOverlay.style.display = 'block';
            uiControls.style.display = 'none'; 
            btnVar.innerHTML = "<i class='fa-solid fa-play'></i> OYUNA DÖN";
            btnVar.style.background = "#4CAF50";
            
            varSlider.max = replayHistory.length > 0 ? replayHistory.length - 1 : 0;
            varSlider.value = varSlider.max;
        } else {
            savedTimeScale = 1; 
            varOverlay.style.display = 'none';
            uiControls.style.display = 'block'; 
            btnVar.innerHTML = "<i class='fa-solid fa-magnifying-glass'></i> VAR İNCELE";
            btnVar.style.background = "#FF0000";
        }
    });

    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;
    container.appendChild(renderer.domElement);

    const ambientLight = new THREE.AmbientLight(0xffffff, 0.65);
    scene.add(ambientLight);

    const dirLight = new THREE.DirectionalLight(0xffffff, 1.0);
    dirLight.position.set(30, 60, 30);
    dirLight.castShadow = true;
    dirLight.shadow.mapSize.width = 2048;
    dirLight.shadow.mapSize.height = 2048;
    dirLight.shadow.camera.left = -50;
    dirLight.shadow.camera.right = 50;
    dirLight.shadow.camera.top = 50;
    dirLight.shadow.camera.bottom = -50;
    scene.add(dirLight);

    // --- 2. SAHA ---
    const pitchWidth = 68;
    const pitchLength = 105;

    function createGrassTexture() {
        const canvas = document.createElement('canvas');
        canvas.width = 512; canvas.height = 512;
        const context = canvas.getContext('2d');
        for (let i = 0; i < 8; i++) {
            context.fillStyle = i % 2 === 0 ? '#3a7d22' : '#418c26'; 
            context.fillRect(0, i * 64, 512, 64);
        }
        const texture = new THREE.CanvasTexture(canvas);
        texture.wrapS = THREE.RepeatWrapping; texture.wrapT = THREE.RepeatWrapping;
        texture.repeat.set(3, 8);
        return texture;
    }

    const pitchGeo = new THREE.PlaneGeometry(pitchWidth * 1.5, pitchLength * 1.5);
    const pitchMat = new THREE.MeshStandardMaterial({ map: createGrassTexture(), roughness: 0.9, metalness: 0.1 });
    const pitch = new THREE.Mesh(pitchGeo, pitchMat);
    pitch.rotation.x = -Math.PI / 2;
    pitch.receiveShadow = true;
    scene.add(pitch);

    function createLine(w, h, x, z) {
        const geo = new THREE.PlaneGeometry(w, h);
        const mat = new THREE.MeshBasicMaterial({ color: 0xffffff });
        const line = new THREE.Mesh(geo, mat);
        line.rotation.x = -Math.PI / 2;
        line.position.set(x, 0.02, z); 
        scene.add(line);
    }
    
    const lineWidth = 0.3;
    createLine(pitchWidth, lineWidth, 0, pitchLength/2); 
    createLine(pitchWidth, lineWidth, 0, -pitchLength/2);
    createLine(lineWidth, pitchLength, pitchWidth/2, 0); 
    createLine(lineWidth, pitchLength, -pitchWidth/2, 0);
    createLine(pitchWidth, lineWidth, 0, 0); 

    // Penaltı Noktaları (Her iki kaleye 11m)
    const penCircleGeo = new THREE.CircleGeometry(0.3, 16);
    const penCircleMat = new THREE.MeshBasicMaterial({ color: 0xffffff });
    const pen1 = new THREE.Mesh(penCircleGeo, penCircleMat); pen1.rotation.x = -Math.PI/2; pen1.position.set(0, 0.02, pitchLength/2 - 11);
    const pen2 = new THREE.Mesh(penCircleGeo, penCircleMat); pen2.rotation.x = -Math.PI/2; pen2.position.set(0, 0.02, -pitchLength/2 + 11);
    scene.add(pen1, pen2);

    const circleGeo = new THREE.RingGeometry(9.0, 9.3, 32);
    const circleMat = new THREE.MeshBasicMaterial({ color: 0xffffff, side: THREE.DoubleSide });
    const circle = new THREE.Mesh(circleGeo, circleMat);
    circle.rotation.x = -Math.PI / 2;
    circle.position.y = 0.02;
    scene.add(circle);

    // --- 3. KALELER ---
    function createGoal(zPos, isRotated) {
        const goalGroup = new THREE.Group();
        const postMat = new THREE.MeshStandardMaterial({ color: 0xffffff, roughness: 0.3, metalness: 0.1 });
        const netMat = new THREE.MeshBasicMaterial({ color: 0xeeeeee, wireframe: true, transparent: true, opacity: 0.8 });

        const postGeo = new THREE.CylinderGeometry(0.08, 0.08, 2.44, 16);
        const leftPost = new THREE.Mesh(postGeo, postMat); leftPost.position.set(-3.66, 1.22, 0); leftPost.castShadow = true;
        const rightPost = new THREE.Mesh(postGeo, postMat); rightPost.position.set(3.66, 1.22, 0); rightPost.castShadow = true;
        
        const topPostGeo = new THREE.CylinderGeometry(0.08, 0.08, 7.32 + 0.16, 16);
        const topPost = new THREE.Mesh(topPostGeo, postMat); topPost.rotation.z = Math.PI / 2; topPost.position.set(0, 2.44, 0); topPost.castShadow = true;

        const supportGeo = new THREE.CylinderGeometry(0.04, 0.04, 2.8, 8);
        const leftSupport = new THREE.Mesh(supportGeo, postMat); leftSupport.position.set(-3.66, 1.22, -1.2); leftSupport.rotation.x = -Math.PI/6;
        const rightSupport = new THREE.Mesh(supportGeo, postMat); rightSupport.position.set(3.66, 1.22, -1.2); rightSupport.rotation.x = -Math.PI/6;
        
        const backBottomGeo = new THREE.CylinderGeometry(0.05, 0.05, 7.32, 8);
        const backBottom = new THREE.Mesh(backBottomGeo, postMat); backBottom.rotation.z = Math.PI/2; backBottom.position.set(0, 0.05, -2.4);

        const backNetGeo = new THREE.PlaneGeometry(7.32, 2.6, 20, 8); 
        const backNet = new THREE.Mesh(backNetGeo, netMat); backNet.position.set(0, 1.22, -2.4);
        
        const topNetGeo = new THREE.PlaneGeometry(7.32, 2.4, 20, 8);
        const topNet = new THREE.Mesh(topNetGeo, netMat); topNet.rotation.x = -Math.PI/2; topNet.position.set(0, 2.44, -1.2);

        const sideNetGeo = new THREE.PlaneGeometry(2.4, 2.44, 8, 8);
        const leftNet = new THREE.Mesh(sideNetGeo, netMat); leftNet.rotation.y = Math.PI / 2; leftNet.position.set(-3.66, 1.22, -1.2);
        const rightNet = new THREE.Mesh(sideNetGeo, netMat); rightNet.rotation.y = -Math.PI / 2; rightNet.position.set(3.66, 1.22, -1.2);

        goalGroup.add(leftPost, rightPost, topPost, leftSupport, rightSupport, backBottom, backNet, topNet, leftNet, rightNet);
        if (isRotated) goalGroup.rotation.y = Math.PI;
        goalGroup.position.set(0, 0, zPos);
        scene.add(goalGroup);
    }
    createGoal(-pitchLength/2, false); 
    createGoal(pitchLength/2, true);

    // --- 3.5 BARAJ DUMMY ---
    const wallGroup = new THREE.Group();
    for(let i=-2; i<=2; i++) {
        const dummyGeo = new THREE.BoxGeometry(0.6, 1.6, 0.3);
        const dummyMat = new THREE.MeshStandardMaterial({color: 0x2196F3}); 
        const dummy = new THREE.Mesh(dummyGeo, dummyMat);
        dummy.position.set(i * 0.75, 0.8, 0);
        dummy.castShadow = true;
        
        const dummyHead = new THREE.Mesh(new THREE.SphereGeometry(0.25), new THREE.MeshStandardMaterial({color: 0xffbcb5}));
        dummyHead.position.set(i * 0.75, 1.85, 0);
        wallGroup.add(dummy, dummyHead);
    }
    scene.add(wallGroup);
    wallGroup.visible = false;

    // --- 4. TOP (GERÇEKÇİ FUTBOL TOPU) ---
    const ballRadius = 0.35;
    const ballGroup = new THREE.Group();

    // Canvas üzerinde klasik altıgen desenli (hexagonal) futbol topu çizimi
    const bCanv = document.createElement('canvas');
    bCanv.width = 512; bCanv.height = 512;
    const bCtx = bCanv.getContext('2d');
    bCtx.fillStyle = '#ffffff'; 
    bCtx.fillRect(0,0,512,512);

    const hexRadius = 45;
    const hexHeight = hexRadius * Math.sqrt(3);
    
    bCtx.lineWidth = 4;
    bCtx.strokeStyle = '#222222';

    // Balpeteği deseni (Honeycomb)
    for(let y = 0; y < 512 + hexHeight; y += hexHeight) {
        for(let x = 0, j=0; x < 512 + hexRadius*3; x += hexRadius * 1.5, j++) {
            let cx = x;
            let cy = y + (j % 2 === 1 ? hexHeight / 2 : 0);
            
            bCtx.beginPath();
            for(let i=0; i<6; i++) {
                let angle = (Math.PI / 3) * i;
                let px = cx + hexRadius * Math.cos(angle);
                let py = cy + hexRadius * Math.sin(angle);
                if(i===0) bCtx.moveTo(px, py);
                else bCtx.lineTo(px, py);
            }
            bCtx.closePath();
            
            // Siyah beyaz desen matematiği (belirli aralıklarla siyah doldur)
            if ((Math.floor(x/15) + Math.floor(y/15)) % 4 === 0) {
                bCtx.fillStyle = '#111111';
                bCtx.fill();
            } else {
                bCtx.fillStyle = '#f0f0f0'; 
                bCtx.fill();
                bCtx.stroke();
            }
        }
    }
    const ballTex = new THREE.CanvasTexture(bCanv);
    
    const ballBaseGeo = new THREE.SphereGeometry(ballRadius, 32, 32);
    const ballBaseMat = new THREE.MeshStandardMaterial({ map: ballTex, roughness: 0.3 });
    const ballBase = new THREE.Mesh(ballBaseGeo, ballBaseMat);
    ballBase.castShadow = true;
    ballGroup.add(ballBase);

    ballGroup.position.set(0, ballRadius + 5, 0); 
    scene.add(ballGroup);

    let ballVelocity = new THREE.Vector3(0, 0, 0);
    let ballPossessed = false;
    let shootCooldown = 0; 

    // --- 5. OYUNCULAR ---
    function createAdvancedCharacter(shirtHex, shortsHex, isReferee = false) {
        const group = new THREE.Group();
        
        const skinMat = new THREE.MeshStandardMaterial({ color: 0xffbcb5, roughness: 0.5 });
        const shirtMat = new THREE.MeshStandardMaterial({ color: shirtHex, roughness: 0.8 });
        const shortsMat = new THREE.MeshStandardMaterial({ color: shortsHex, roughness: 0.8 });
        const shoeMat = new THREE.MeshStandardMaterial({ color: 0x111111, roughness: 0.3 });
        const hairMat = new THREE.MeshStandardMaterial({ color: 0x3e2723, roughness: 0.9 }); 

        const torso = new THREE.Mesh(new THREE.BoxGeometry(0.75, 0.65, 0.4), shirtMat);
        torso.position.y = 1.15; torso.castShadow = true;
        
        const shorts = new THREE.Mesh(new THREE.BoxGeometry(0.76, 0.35, 0.42), shortsMat);
        shorts.position.y = 0.65; shorts.castShadow = true;

        const head = new THREE.Mesh(new THREE.SphereGeometry(0.3, 16, 16), skinMat);
        head.position.y = 1.65; head.castShadow = true;
        const hair = new THREE.Mesh(new THREE.BoxGeometry(0.55, 0.15, 0.55), hairMat);
        hair.position.y = 1.9; hair.castShadow = true;

        const armGeo = new THREE.CylinderGeometry(0.1, 0.1, 0.6);
        const lArm = new THREE.Mesh(armGeo, skinMat); lArm.position.set(-0.5, 1.0, 0); lArm.castShadow = true;
        const rArm = new THREE.Mesh(armGeo, skinMat); rArm.position.set(0.5, 1.0, 0); rArm.castShadow = true;
        const sleeveGeo = new THREE.CylinderGeometry(0.12, 0.12, 0.25);
        const lSleeve = new THREE.Mesh(sleeveGeo, shirtMat); lSleeve.position.set(-0.5, 1.35, 0); lSleeve.castShadow = true;
        const rSleeve = new THREE.Mesh(sleeveGeo, shirtMat); rSleeve.position.set(0.5, 1.35, 0); rSleeve.castShadow = true;

        const legGeo = new THREE.CylinderGeometry(0.13, 0.13, 0.4);
        const leftLeg = new THREE.Mesh(legGeo, skinMat); leftLeg.position.set(-0.2, 0.3, 0); leftLeg.castShadow = true;
        const rightLeg = new THREE.Mesh(legGeo, skinMat); rightLeg.position.set(0.2, 0.3, 0); rightLeg.castShadow = true;

        const shoeGeo = new THREE.BoxGeometry(0.2, 0.15, 0.3);
        const lShoe = new THREE.Mesh(shoeGeo, shoeMat); lShoe.position.set(-0.2, 0.075, 0.05); lShoe.castShadow = true;
        const rShoe = new THREE.Mesh(shoeGeo, shoeMat); rShoe.position.set(0.2, 0.075, 0.05); rShoe.castShadow = true;

        const visor = new THREE.Mesh(new THREE.BoxGeometry(0.35, 0.1, 0.15), new THREE.MeshBasicMaterial({ color: 0x222222 }));
        visor.position.set(0, 1.7, -0.25);

        if (isReferee) {
            const whistle = new THREE.Mesh(new THREE.BoxGeometry(0.05, 0.05, 0.1), new THREE.MeshStandardMaterial({color: 0xaaaaaa, metalness:0.8}));
            whistle.position.set(0, 1.55, -0.32);
            group.add(whistle);
        }

        group.add(torso, shorts, head, hair, lArm, rArm, lSleeve, rSleeve, leftLeg, rightLeg, lShoe, rShoe, visor);
        
        // Animasyon durumlarını kaydetmek için userData'ya eklemeler yapıldı (Rövaşata, Kafa, Vole için)
        group.userData = { 
            leftLeg, rightLeg, lShoe, rShoe, lArm, rArm, lSleeve, rSleeve, 
            walkTime: 0, animState: 'idle', animTimer: 0, animMaxT: 0 
        };
        return group;
    }

    const playerGroup = createAdvancedCharacter(0xe53935, 0xffffff); 
    const arrowHelper = new THREE.ArrowHelper(new THREE.Vector3(0,0,-1), new THREE.Vector3(0, 0.1, -0.8), 3, 0xffff00, 0.8, 0.4);
    arrowHelper.visible = false;
    playerGroup.add(arrowHelper);
    scene.add(playerGroup);

    const refereeGroup = createAdvancedCharacter(0x111111, 0x111111, true); 
    scene.add(refereeGroup);

    const gkGroup = createAdvancedCharacter(0xFFA000, 0x1B5E20);
    scene.add(gkGroup);

    const oppGroup = createAdvancedCharacter(0x1976D2, 0xffffff);
    scene.add(oppGroup);


    // --- 6. FİZİK ---
    const gravity = 0.0035; 
    const friction = 0.988; 
    const airFriction = 0.995;
    const bounceFactor = 0.5; 
    let score = 0;

    // --- 6.5 OYUN MODU SIFIRLAMA MANTIĞI ---
    function resetGameForMode() {
        isGoalProcessing = false;
        shootCooldown = 0;
        wallGroup.visible = false;
        refereeGroup.visible = false;
        gkGroup.visible = false;
        oppGroup.visible = false;
        
        // Rakip Kale (-z yönü)
        const targetGoalZ = -pitchLength/2;

        if (gameMode === 'penalty') {
            ballGroup.position.set(0, ballRadius, targetGoalZ + 11);
            ballVelocity.set(0,0,0);
            ballPossessed = false;
            
            playerGroup.position.set(0, 0, targetGoalZ + 13.5); 
            playerGroup.lookAt(0, 0, targetGoalZ);
            camYaw = Math.PI;

            gkGroup.visible = true;
            gkGroup.position.set(0, 0, targetGoalZ + 0.5);
            gkGroup.rotation.set(0, 0, 0); // Atlayış sıfırlama
            gkGroup.lookAt(0, 0, targetGoalZ);
            
        } else if (gameMode === 'freekick') {
            ballGroup.position.set(0, ballRadius + 10, 0); 
            ballVelocity.set(0,0,0);

            gkGroup.visible = true;
            gkGroup.position.set(0, 0, targetGoalZ + 0.5);
            gkGroup.rotation.set(0, 0, 0);
            gkGroup.lookAt(0, 0, targetGoalZ);
            
        } else if (gameMode === 'match') {
            ballGroup.position.set(0, ballRadius, 0);
            ballVelocity.set(0,0,0);
            ballPossessed = false;
            
            playerGroup.position.set(0, 0, 3);
            playerGroup.lookAt(0,0,0);
            camYaw = Math.PI;

            refereeGroup.visible = true;
            refereeGroup.position.set(-10, 0, 5);
            
            gkGroup.visible = true;
            gkGroup.position.set(0, 0, targetGoalZ + 0.5);
            gkGroup.lookAt(0, 0, targetGoalZ);

            oppGroup.visible = true;
            oppGroup.position.set(0, 0, -5); 
            oppGroup.lookAt(0,0,0);

        } else if (gameMode === 'referee') {
            ballGroup.position.set(0, ballRadius, 0);
            ballVelocity.set(0,0,0);
            ballPossessed = false;
            
            playerGroup.position.set(0, 0, 3);
            playerGroup.lookAt(0,0,0);
            camYaw = Math.PI;

            refereeGroup.visible = true;
            refereeGroup.position.set(-10, 0, 0); 
        } else {
            // Antrenman
            ballGroup.position.set(0, ballRadius, 0); 
            ballVelocity.set(0,0,0);
            playerGroup.position.set(0, 0, 15);
            playerGroup.lookAt(0,0,0); 
            camYaw = 0; 
        }

        if(isVarMode) document.getElementById('btn-var').click();
    }


    // --- 7. ROBLOX KAMERA KONTROLÜ VE EKRANA DOKUNMA ÇAKIŞMA KONTROLÜ ---
    const raycaster = new THREE.Raycaster();
    const mouse = new THREE.Vector2();
    let isOrbiting = false;
    let dragStartX = 0;
    let dragStartY = 0;
    let hasDragged = false;
    let pointerDownTime = 0;

    window.addEventListener('pointerdown', (e) => {
        if(isVarMode || e.target.tagName !== 'CANVAS') return;
        isOrbiting = true;
        hasDragged = false;
        pointerDownTime = Date.now();
        dragStartX = e.clientX;
        dragStartY = e.clientY;
    });

    window.addEventListener('pointermove', (e) => {
        if (isOrbiting && !isVarMode) {
            const deltaX = e.clientX - dragStartX;
            const deltaY = e.clientY - dragStartY;
            
            if (Math.abs(deltaX) > 4 || Math.abs(deltaY) > 4) {
                hasDragged = true;
                camYaw -= deltaX * 0.006; 
                camPitch += deltaY * 0.006;
                camPitch = Math.max(0.05, Math.min(Math.PI / 2 - 0.1, camPitch));
                
                dragStartX = e.clientX;
                dragStartY = e.clientY;
            }
        }
    });

    window.addEventListener('pointerup', (e) => {
        isOrbiting = false;
        const clickDuration = Date.now() - pointerDownTime;
        
        if (!hasDragged && clickDuration < 300 && placingFreeKick && !isVarMode && e.target.tagName === 'CANVAS') {
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;
            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObject(pitch);
            
            if (intersects.length > 0) {
                setupFreeKickPoint(intersects[0].point);
            }
        }
    });

    function setupFreeKickPoint(point) {
        ballGroup.position.set(point.x, ballRadius, point.z);
        ballVelocity.set(0,0,0);
        ballPossessed = false;

        const targetGoal = new THREE.Vector3(0, 0, -pitchLength/2);
        const dirToGoal = new THREE.Vector3().subVectors(targetGoal, point).normalize();

        playerGroup.position.copy(point).sub(dirToGoal.clone().multiplyScalar(2.0));
        playerGroup.lookAt(targetGoal);
        camYaw = Math.atan2(dirToGoal.x, dirToGoal.z);

        const distanceToGoal = point.distanceTo(targetGoal);
        if (distanceToGoal > 11) {
            const wallDist = Math.min(9.15, distanceToGoal / 2);
            wallGroup.position.copy(point).add(dirToGoal.clone().multiplyScalar(wallDist));
            wallGroup.lookAt(targetGoal);
            wallGroup.visible = true;
        } else {
            wallGroup.visible = false; 
        }

        placingFreeKick = false;
        document.getElementById('freekick-indicator').style.display = 'none';
        gkGroup.position.x = 0;
        gkGroup.rotation.z = 0;
    }


    // --- 8. KONTROLLER (JOYSTICK) ---
    let joystickInput = { x: 0, y: 0, active: false };
    const joystickZone = document.getElementById('joystick-zone');
    const joystickKnob = document.getElementById('joystick-knob');
    let joyCenter = { x: 0, y: 0 };
    let isJoyDragging = false;

    function handleJoyStart(clientX, clientY) {
        if(isVarMode || placingFreeKick) return;
        const rect = joystickZone.getBoundingClientRect();
        joyCenter.x = rect.left + rect.width / 2;
        joyCenter.y = rect.top + rect.height / 2;
        isJoyDragging = true; joystickInput.active = true;
        updateJoystick(clientX, clientY);
    }
    function handleJoyMove(clientX, clientY) { if (isJoyDragging && !isVarMode && !placingFreeKick) updateJoystick(clientX, clientY); }
    function handleJoyEnd() {
        isJoyDragging = false; joystickInput.active = false; joystickInput.x = 0; joystickInput.y = 0;
        joystickKnob.style.transform = `translate(-50%, -50%)`;
        joystickKnob.style.left = '50%'; joystickKnob.style.top = '50%';
    }
    function updateJoystick(clientX, clientY) {
        let dx = clientX - joyCenter.x; let dy = clientY - joyCenter.y;
        const maxDist = 50; const distance = Math.sqrt(dx * dx + dy * dy);
        if (distance > maxDist) { dx = (dx / distance) * maxDist; dy = (dy / distance) * maxDist; }
        joystickKnob.style.left = `calc(50% + ${dx}px)`; joystickKnob.style.top = `calc(50% + ${dy}px)`;
        joystickInput.x = dx / maxDist; joystickInput.y = dy / maxDist;
    }

    joystickZone.addEventListener('pointerdown', (e) => { e.preventDefault(); handleJoyStart(e.clientX, e.clientY); });
    window.addEventListener('pointermove', (e) => { if(isJoyDragging) handleJoyMove(e.clientX, e.clientY); });
    window.addEventListener('pointerup', () => { if(isJoyDragging) handleJoyEnd(); });

    // --- 9. ŞUT BARI VE MEKANİĞİ ---
    let chargingShot = null;
    let shotPowerMultiplier = 0; 
    let shotPowerDirection = 1; 
    const shotBarContainer = document.getElementById('shot-bar-container');
    const shotBarFill = document.getElementById('shot-bar-fill');

    function executeShot(type, multiplier) {
        let canShoot = false;
        let hitType = 'ground';
        let dist = playerGroup.position.distanceTo(ballGroup.position);
        let h = ballGroup.position.y;
        
        if (ballPossessed) {
            canShoot = true;
        } else if (gameMode === 'freekick' || gameMode === 'penalty') {
            if (dist < 4.0) canShoot = true;
        } else if (dist < 3.0) {
            // Havadaki vuruşlar için (Rövaşata, Kafa, Vole)
            canShoot = true;
            if (h >= 1.2 && h < 2.2) {
                hitType = 'volley'; // Yere yakın hava topu: Vole
            } else if (h >= 2.2 && h < 4.5) {
                // Yüksek top: Aşırtma/Turbo seçiliyse Rövaşata, yoksa Kafa
                if (type === 'air' || type === 'turbo') hitType = 'bicycle';
                else hitType = 'header';
            }
        }

        if (!canShoot || isVarMode) return;

        // Yönlendirme (Her zaman Kameranın Gösterdiği Yön)
        const dir = new THREE.Vector3(0, 0, -1);
        playerGroup.rotation.set(0, camYaw, 0); // Karakteri kameranın baktığı yöne çevir
        dir.applyQuaternion(playerGroup.quaternion);
        dir.normalize();
        
        // --- Karakter Animasyonlarını Başlat (Rövaşata/Kafa/Vole) ---
        if (hitType !== 'ground') {
            playerGroup.userData.animState = hitType;
            playerGroup.userData.animMaxT = hitType === 'bicycle' ? 0.8 : (hitType === 'volley' ? 0.6 : 0.5);
            playerGroup.userData.animTimer = playerGroup.userData.animMaxT;
        }

        const finalMulti = 0.3 + (multiplier * 0.85);
        let power = 0, height = 0;

        switch(type) {
            case 'pass': power = 0.2; height = 0.015; break;
            case 'normal': power = 0.4; height = 0.1; break;
            case 'long': power = 0.6; height = 0.16; break;
            case 'air': power = 0.35; height = 0.45; break; 
            case 'turbo': power = 0.85; height = 0.04; break; 
        }
        power *= finalMulti; height *= finalMulti;

        // Vuruş stiline göre özel fizik çarpanları
        if (hitType === 'header') {
            power *= 0.6; height = Math.min(height, 0.05); // Kafa vuruşları biraz daha yavaş ve yere doğru gider
        } else if (hitType === 'volley') {
            power *= 1.3; height *= 0.8; // Voleler çok sert ve direkt gider
        } else if (hitType === 'bicycle') {
            power *= 1.6; height *= 1.2; // Rövaşata en sert ve kavisli
        }

        ballVelocity.set(dir.x * power, height, dir.z * power);
        ballPossessed = false; arrowHelper.visible = false;
        shootCooldown = 1.0; 
        
        if (gameMode === 'freekick' || gameMode === 'penalty') {
            wallGroup.visible = false; 
        }
    }

    const bindButton = (id, type) => {
        const btn = document.getElementById(id);
        btn.addEventListener('pointerdown', (e) => {
            e.preventDefault();
            if(isVarMode || placingFreeKick) return;
            
            let dist = playerGroup.position.distanceTo(ballGroup.position);
            // Sadece top bizdeyken veya havadaki top bize doğru gelirken basılı tutmaya izin ver (Hata engelleme)
            if(!ballPossessed && dist > 5.0 && (gameMode !== 'freekick' && gameMode !== 'penalty')) return;
            
            btn.classList.add('active');
            chargingShot = type; shotPowerMultiplier = 0; shotPowerDirection = 1;
            shotBarContainer.style.display = 'block'; shotBarFill.style.width = '0%';
        });
        const endAction = (e) => {
            if(e) e.preventDefault();
            btn.classList.remove('active');
            if(chargingShot === type) {
                executeShot(type, shotPowerMultiplier);
                chargingShot = null; shotBarContainer.style.display = 'none';
            }
        };
        btn.addEventListener('pointerup', endAction);
        btn.addEventListener('pointerleave', endAction);
        btn.addEventListener('pointercancel', endAction);
    };

    bindButton('btn-pass', 'pass'); bindButton('btn-normal', 'normal');
    bindButton('btn-long', 'long'); bindButton('btn-air', 'air'); bindButton('btn-turbo', 'turbo');

    // VAR Slider Event
    varSlider.addEventListener('input', (e) => {
        if (!isVarMode) return;
        const index = parseInt(e.target.value);
        if (replayHistory[index]) {
            const frame = replayHistory[index];
            ballGroup.position.copy(frame.bPos);
            ballGroup.rotation.copy(frame.bRot);
            playerGroup.position.copy(frame.pPos);
            playerGroup.rotation.copy(frame.pRot);
            
            const currentSpeed = frame.bVel.length() * 60 * 3.6;
            document.getElementById('var-stats').innerText = `Şut Hızı: ${Math.round(currentSpeed)} km/s`;
        }
    });

    // --- 10. GOL SİSTEMİ ---
    let isGoalProcessing = false;
    
    function triggerGoal() {
        if(isGoalProcessing) return;
        isGoalProcessing = true;
        score++;
        document.getElementById('score-val').innerText = score;
        const alertBox = document.getElementById('goal-alert');
        alertBox.style.display = 'block';
        
        setTimeout(() => {
            alertBox.style.display = 'none';
            resetGameForMode();
        }, 3000);
    }

    function checkGoal() {
        if (isGoalProcessing) return;
        const goalLineZ = pitchLength/2; 
        const lineWidthHalf = lineWidth / 2; 
        
        if (ballGroup.position.z < -goalLineZ - lineWidthHalf - ballRadius && Math.abs(ballGroup.position.x) < 3.66 - ballRadius && ballGroup.position.y < 2.44) {
            triggerGoal();
        } else if (ballGroup.position.z > goalLineZ + lineWidthHalf + ballRadius && Math.abs(ballGroup.position.x) < 3.66 - ballRadius && ballGroup.position.y < 2.44) {
            triggerGoal();
        }
    }

    // --- 11. OYUN DÖNGÜSÜ & YAPAY ZEKA ---
    const clock = new THREE.Clock();

    function updateCharacterAnim(charGroup, isMoving, timeDelta) {
        
        // Havadaki vuruş (Rövaşata/Kafa/Vole) animasyonları kontrolü
        if (charGroup.userData.animState && charGroup.userData.animState !== 'idle') {
            charGroup.userData.animTimer -= timeDelta;
            let t = charGroup.userData.animTimer;
            let maxT = charGroup.userData.animMaxT || 0.6;
            let progress = 1 - (t / maxT); // 0'dan 1'e doğru artar

            if (t <= 0) {
                // Animasyon Bitti, Resetle
                charGroup.userData.animState = 'idle';
                charGroup.rotation.x = 0;
                charGroup.rotation.z = 0;
                charGroup.position.y = 0;
                charGroup.userData.rightLeg.rotation.x = 0;
            } else {
                if (charGroup.userData.animState === 'bicycle') {
                    // Rövaşata: Geriye doğru 270 derece takla
                    charGroup.rotation.x = -progress * Math.PI * 1.5; 
                    charGroup.position.y = Math.sin(progress * Math.PI) * 1.5; // Zıplama kavis
                } else if (charGroup.userData.animState === 'header') {
                    // Kafa: Zıpla ve öne doğru hafifçe eğilerek vur
                    charGroup.position.y = Math.sin(progress * Math.PI) * 1.0;
                    charGroup.rotation.x = Math.sin(progress * Math.PI) * 0.4;
                } else if (charGroup.userData.animState === 'volley') {
                    // Vole: Yana yat, zıpla, sağ ayağı savur
                    charGroup.rotation.z = Math.sin(progress * Math.PI) * -0.5;
                    charGroup.position.y = Math.sin(progress * Math.PI) * 0.6;
                    charGroup.userData.rightLeg.rotation.x = -Math.sin(progress * Math.PI) * 1.5;
                }
            }
            return; // Özel animasyon oynatılırken normal yürüme animasyonunu durdur
        }

        // Normal Yürüme Animasyonu
        if (isMoving && savedTimeScale > 0) {
            charGroup.userData.walkTime += timeDelta * 15;
            let t = charGroup.userData.walkTime;
            charGroup.userData.leftLeg.position.z = Math.sin(t) * 0.3;
            charGroup.userData.lShoe.position.z = Math.sin(t) * 0.3 + 0.05;
            charGroup.userData.rightLeg.position.z = Math.sin(t + Math.PI) * 0.3;
            charGroup.userData.rShoe.position.z = Math.sin(t + Math.PI) * 0.3 + 0.05;
            charGroup.userData.lArm.position.z = Math.sin(t + Math.PI) * 0.2;
            charGroup.userData.lSleeve.position.z = Math.sin(t + Math.PI) * 0.2;
            charGroup.userData.rArm.position.z = Math.sin(t) * 0.2;
            charGroup.userData.rSleeve.position.z = Math.sin(t) * 0.2;
        } else {
            charGroup.userData.walkTime = 0;
            charGroup.userData.leftLeg.position.z = 0; charGroup.userData.lShoe.position.z = 0.05;
            charGroup.userData.rightLeg.position.z = 0; charGroup.userData.rShoe.position.z = 0.05;
            charGroup.userData.lArm.position.z = 0; charGroup.userData.lSleeve.position.z = 0;
            charGroup.userData.rArm.position.z = 0; charGroup.userData.rSleeve.position.z = 0;
        }
    }

    const wallBox = new THREE.Box3();
    const gkBox = new THREE.Box3();

    function animate() {
        requestAnimationFrame(animate);
        const delta = clock.getDelta() * savedTimeScale;

        if (savedTimeScale > 0) {
            if (replayHistory.length >= MAX_HISTORY_FRAMES) replayHistory.shift();
            replayHistory.push({
                bPos: ballGroup.position.clone(), bRot: ballGroup.rotation.clone(),
                bVel: ballVelocity.clone(), pPos: playerGroup.position.clone(), pRot: playerGroup.rotation.clone()
            });
        }

        if (chargingShot && savedTimeScale > 0) {
            shotPowerMultiplier += shotPowerDirection * delta * 1.5; 
            if (shotPowerMultiplier >= 1) { shotPowerMultiplier = 1; shotPowerDirection = -1; } 
            else if (shotPowerMultiplier <= 0) { shotPowerMultiplier = 0; shotPowerDirection = 1; }
            shotBarFill.style.width = (shotPowerMultiplier * 100) + '%';
        }

        if(shootCooldown > 0 && savedTimeScale > 0) shootCooldown -= delta;

        // 1. OYUNCU HAREKETİ VE OKUN YÖNÜ
        const speed = 16 * delta;
        let isMoving = false;
        
        if (joystickInput.active && !chargingShot && savedTimeScale > 0 && !placingFreeKick) { 
            // Rövaşata/Kafa gibi bir hava topu vuruşu yapılmıyorsa hareket et (Takla atarken kaymayı engeller)
            if (playerGroup.userData.animState === 'idle') {
                const moveX = joystickInput.x;
                const moveZ = joystickInput.y;
                if (moveX !== 0 || moveZ !== 0) {
                    isMoving = true;
                    const moveDir = new THREE.Vector3(moveX, 0, moveZ);
                    moveDir.applyAxisAngle(new THREE.Vector3(0, 1, 0), camYaw);
                    moveDir.normalize();
                    const intensity = Math.min(1, Math.sqrt(moveX**2 + moveZ**2));
                    playerGroup.position.x += moveDir.x * speed * intensity;
                    playerGroup.position.z += moveDir.z * speed * intensity;
                    
                    // Karakter tam olarak hareket ettiği yöne bakar
                    playerGroup.lookAt(playerGroup.position.clone().add(moveDir));
                }
            }
        }
        updateCharacterAnim(playerGroup, isMoving, delta);

        // FRİKİK/PENALTI MODUNDA OKUN (NİŞANIN) KAMERAYLA DÖNMESİ
        if ((gameMode === 'freekick' || gameMode === 'penalty') && savedTimeScale > 0 && !isGoalProcessing) {
            if(shootCooldown <= 0 && !isMoving && playerGroup.userData.animState === 'idle') {
                playerGroup.rotation.set(0, camYaw, 0); // Karakteri kameranın açısına çevir
                arrowHelper.visible = true; // Nişan alabilmen için oku hep göster
            }
        }

        playerGroup.position.x = Math.max(-pitchWidth/2, Math.min(pitchWidth/2, playerGroup.position.x));
        playerGroup.position.z = Math.max(-pitchLength/2, Math.min(pitchLength/2, playerGroup.position.z));


        // --- YAPAY ZEKA ---
        
        // A) HAKEM
        if (refereeGroup.visible && savedTimeScale > 0) {
            let refMoving = false;
            const idealPos = ballGroup.position.clone().add(new THREE.Vector3(0, 0, 10)); 
            idealPos.x *= 0.6; 
            idealPos.x = Math.max(-pitchWidth/2 + 5, Math.min(pitchWidth/2 - 5, idealPos.x)); 
            let distToIdeal = refereeGroup.position.distanceTo(idealPos);

            if (distToIdeal > 2.0 && !isGoalProcessing) {
                refMoving = true;
                let dir = idealPos.sub(refereeGroup.position);
                dir.y = 0; dir.normalize();
                refereeGroup.position.add(dir.multiplyScalar(delta * 9)); 
            }
            refereeGroup.lookAt(ballGroup.position.x, refereeGroup.position.y, ballGroup.position.z);
            updateCharacterAnim(refereeGroup, refMoving, delta);
        }

        // B) KALECİ (PENALTI/MAÇ İÇİN GELİŞMİŞ UÇMA MEKANİĞİ)
        if (gkGroup.visible && savedTimeScale > 0) {
            let gkMoving = false;
            gkGroup.position.z = -pitchLength/2 + 0.5;
            
            // Eğer Frikik/Penaltı modundaysak ve top hızla geliyorsa uçarak (dive) kurtarış yapsın
            if ((gameMode === 'penalty' || gameMode === 'freekick') && ballVelocity.z < -0.2 && ballGroup.position.z < -pitchLength/2 + 28) {
                // Topun gideceği yeri tahmin et
                const targetX = Math.max(-3.5, Math.min(3.5, ballGroup.position.x + (ballVelocity.x * 12))); 
                const diffX = targetX - gkGroup.position.x;
                
                if (Math.abs(diffX) > 0.1) {
                    gkMoving = true;
                    // Kaleci Köşeye Uçar
                    gkGroup.position.x += Math.sign(diffX) * delta * 16; 
                    // Uçma Animasyonu (Karakteri Eğ)
                    gkGroup.rotation.z = Math.sign(diffX) * -1.2; 
                }
            } else {
                // Normal Maç Takibi veya Eski Haline Dönüş
                gkGroup.rotation.z = 0; // Ayağa kalk
                const targetX = Math.max(-3.5, Math.min(3.5, ballGroup.position.x));
                const diffX = targetX - gkGroup.position.x;
                
                if (Math.abs(diffX) > 0.5 && (ballVelocity.z < -0.1 || ballGroup.position.distanceTo(gkGroup.position) < 10)) {
                    gkMoving = true;
                    gkGroup.position.x += Math.sign(diffX) * delta * 4; 
                } else if (gameMode === 'penalty' || gameMode === 'freekick') {
                    // Ortaya yavaşça geri dön
                    gkGroup.position.x += (0 - gkGroup.position.x) * delta * 3;
                }
            }

            // Eğer top kaleciye yakınsa her zaman topa baksın
            if (gkGroup.rotation.z === 0) {
                gkGroup.lookAt(ballGroup.position.x, gkGroup.position.y, ballGroup.position.z);
            }
            updateCharacterAnim(gkGroup, gkMoving, delta);
            
            gkBox.setFromObject(gkGroup);
            if (gkBox.containsPoint(ballGroup.position)) {
                ballVelocity.z *= -0.5; 
                ballVelocity.x += (Math.random() - 0.5) * 0.4; 
                ballVelocity.y += 0.15;
                ballGroup.position.z -= ballVelocity.z * 2;
            }
        }

        // C) RAKİP OYUNCU (GELİŞMİŞ MAÇ MODU - ŞUT VE DRİBLİNG YAPIYOR)
        if (oppGroup.visible && savedTimeScale > 0) {
            let oppMoving = false;
            let distToBall = oppGroup.position.distanceTo(ballGroup.position);

            if (distToBall > 1.5 && !isGoalProcessing) {
                // Topu Kovala
                oppMoving = true;
                let dir = ballGroup.position.clone().sub(oppGroup.position);
                dir.y = 0; dir.normalize();
                oppGroup.position.add(dir.multiplyScalar(delta * 13)); 
                oppGroup.lookAt(ballGroup.position.x, oppGroup.position.y, ballGroup.position.z);
            } else if (distToBall <= 1.5 && !isGoalProcessing) {
                // Top rakipte!
                if (shootCooldown <= 0 && ballGroup.position.y < 1.2) {
                    // Senin kalene (+z) yakın mı?
                    if (oppGroup.position.z > 20) {
                        // Şut Çek!
                        let dirToGoal = new THREE.Vector3(0, 0, pitchLength/2).sub(oppGroup.position).normalize();
                        ballVelocity.set(dirToGoal.x * 0.7, 0.12, dirToGoal.z * 0.7); 
                        ballPossessed = false;
                        shootCooldown = 1.2;
                        oppGroup.lookAt(0, oppGroup.position.y, pitchLength/2);
                    } else {
                        // Kaleye doğru dripling yap
                        let dirToGoal = new THREE.Vector3(0, 0, pitchLength/2).sub(oppGroup.position).normalize();
                        // Dripline hızı artırıldı
                        ballVelocity.set(dirToGoal.x * 0.45, 0.02, dirToGoal.z * 0.45);
                        ballPossessed = false;
                        shootCooldown = 0.5;
                        oppGroup.lookAt(0, oppGroup.position.y, pitchLength/2);
                    }
                }
                if (!isMoving) oppGroup.lookAt(ballGroup.position.x, ballGroup.position.y, ballGroup.position.z);
            }
            updateCharacterAnim(oppGroup, oppMoving, delta);
        }

        // 2. TOP FİZİĞİ
        if(savedTimeScale > 0) {
            const distToBall = playerGroup.position.distanceTo(ballGroup.position);

            // Topu ancak top yerdeyken (<1.2) ayakla kontrol edebiliriz
            if (distToBall < 1.6 && ballGroup.position.y < 1.2 && !isGoalProcessing && shootCooldown <= 0) {
                ballPossessed = true; 
                // Normal oyunda oku sadece top bizdeyken göster
                if (gameMode !== 'freekick' && gameMode !== 'penalty') arrowHelper.visible = true;
                if (gameMode === 'freekick' || gameMode === 'penalty') wallGroup.visible = false; 
            }

            if (ballPossessed) {
                const frontDir = new THREE.Vector3(0, 0, -1);
                frontDir.applyQuaternion(playerGroup.quaternion);
                ballGroup.position.x = playerGroup.position.x + frontDir.x * 1.0;
                ballGroup.position.z = playerGroup.position.z + frontDir.z * 1.0;
                ballGroup.position.y = ballRadius;
                
                if (joystickInput.active && !chargingShot) {
                    ballGroup.rotation.x += joystickInput.y * speed;
                    ballGroup.rotation.z -= joystickInput.x * speed;
                }
                ballVelocity.set(0,0,0);
            } else {
                // Top oyuncuda değilse (frikik/penaltı hariç) oku gizle
                if (gameMode !== 'freekick' && gameMode !== 'penalty') arrowHelper.visible = false;
                
                ballVelocity.y -= gravity;
                
                if(ballGroup.position.y > ballRadius) {
                     ballVelocity.x *= airFriction;
                     ballVelocity.z *= airFriction;
                }
                
                ballGroup.position.add(ballVelocity);

                if (ballGroup.position.y <= ballRadius) {
                    ballGroup.position.y = ballRadius;
                    if (Math.abs(ballVelocity.y) > 0.015) ballVelocity.y *= -bounceFactor;
                    else ballVelocity.y = 0;
                    ballVelocity.x *= friction; ballVelocity.z *= friction;
                }

                if(ballGroup.position.y <= ballRadius + 0.1 || ballVelocity.length() > 0.1) {
                    ballGroup.rotation.x += ballVelocity.z * 2.5;
                    ballGroup.rotation.z -= ballVelocity.x * 2.5;
                }

                if (ballGroup.position.x > pitchWidth/2) { ballGroup.position.x = pitchWidth/2; ballVelocity.x *= -0.7; }
                if (ballGroup.position.x < -pitchWidth/2) { ballGroup.position.x = -pitchWidth/2; ballVelocity.x *= -0.7; }
                
                if (wallGroup.visible) {
                    wallBox.setFromObject(wallGroup);
                    if (wallBox.containsPoint(ballGroup.position)) {
                        ballVelocity.z *= -0.4;
                        ballVelocity.x *= -0.4;
                        ballVelocity.y += 0.1; 
                        ballGroup.position.z -= ballVelocity.z * 2;
                    }
                }
                
                // RAKİP KALE (-Z)
                if (ballGroup.position.z < -pitchLength/2 + ballRadius) {
                    if (Math.abs(ballGroup.position.x) < 3.66 && ballGroup.position.y < 2.44) {
                        if (ballGroup.position.z < -pitchLength/2 - 2 + ballRadius) { ballGroup.position.z = -pitchLength/2 - 2 + ballRadius; ballVelocity.z *= -0.3; }
                        if (ballGroup.position.x > 3.66 - ballRadius) { ballGroup.position.x = 3.66 - ballRadius; ballVelocity.x *= -0.3; }
                        if (ballGroup.position.x < -3.66 + ballRadius) { ballGroup.position.x = -3.66 + ballRadius; ballVelocity.x *= -0.3; }
                        if (ballGroup.position.y > 2.44 - ballRadius) { ballGroup.position.y = 2.44 - ballRadius; ballVelocity.y *= -0.3; }
                    } else {
                        ballGroup.position.z = -pitchLength/2 + ballRadius; ballVelocity.z *= -0.3;
                    }
                }

                // KENDİ KALEMİZ (+Z)
                if (ballGroup.position.z > pitchLength/2 - ballRadius) {
                    if (Math.abs(ballGroup.position.x) < 3.66 && ballGroup.position.y < 2.44) {
                        if (ballGroup.position.z > pitchLength/2 + 2 - ballRadius) { ballGroup.position.z = pitchLength/2 + 2 - ballRadius; ballVelocity.z *= -0.3; }
                        if (ballGroup.position.x > 3.66 - ballRadius) { ballGroup.position.x = 3.66 - ballRadius; ballVelocity.x *= -0.3; }
                        if (ballGroup.position.x < -3.66 + ballRadius) { ballGroup.position.x = -3.66 + ballRadius; ballVelocity.x *= -0.3; }
                        if (ballGroup.position.y > 2.44 - ballRadius) { ballGroup.position.y = 2.44 - ballRadius; ballVelocity.y *= -0.3; }
                    } else {
                        ballGroup.position.z = pitchLength/2 - ballRadius; ballVelocity.z *= -0.3;
                    }
                }
                
                checkGoal();
            }
        }

        // 3. ROBLOX KAMERA YÖNETİMİ
        if (!isVarMode) {
            const targetRadius = cameraMode === 0 ? camRadius : 4; 
            const heightOffset = cameraMode === 0 ? 1 : 2.5; 
            
            const camOffsetX = targetRadius * Math.sin(camYaw) * Math.cos(camPitch);
            const camOffsetY = targetRadius * Math.sin(camPitch);
            const camOffsetZ = targetRadius * Math.cos(camYaw) * Math.cos(camPitch);
            
            const targetCamPos = playerGroup.position.clone().add(new THREE.Vector3(camOffsetX, camOffsetY + heightOffset, camOffsetZ));
            camera.position.lerp(targetCamPos, 0.3); 

            if (cameraMode === 0) {
                camera.lookAt(playerGroup.position.clone().add(new THREE.Vector3(0, 1, 0)));
            } else {
                const lookForwardX = -Math.sin(camYaw) * 10;
                const lookForwardZ = -Math.cos(camYaw) * 10;
                camera.lookAt(playerGroup.position.clone().add(new THREE.Vector3(lookForwardX, 1.5, lookForwardZ)));
            }
        } else { 
            const varCamPos = new THREE.Vector3(ballGroup.position.x + 8, 5, ballGroup.position.z + 8);
            camera.position.lerp(varCamPos, 0.15); 
            camera.lookAt(ballGroup.position); 
        }

        renderer.render(scene, camera);
    }

    window.addEventListener('resize', () => {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
    });

    // Başlangıç Kurulumu
    resetGameForMode();
    animate();

</script>
</body>
</html>

