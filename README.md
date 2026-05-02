<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Lendário Parkour X - Lendário Studio</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        body { margin: 0; overflow: hidden; font-family: 'Segoe UI', sans-serif; background: #b8d98d; user-select: none; touch-action: none; }
        canvas { display: block; }

        /* --- SISTEMA DE CONQUISTAS --- */
        #achievement-toast {
            position: fixed;
            top: 20%;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0, 0, 0, 0.8);
            color: #f1c40f;
            padding: 15px 30px;
            border-radius: 50px;
            border: 2px solid #f1c40f;
            font-weight: bold;
            z-index: 3000;
            display: none;
            animation: popIn 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            pointer-events: none;
            white-space: nowrap;
        }

        @keyframes popIn {
            0% { transform: translateX(-50%) scale(0); opacity: 0; }
            100% { transform: translateX(-50%) scale(1); opacity: 1; }
        }

        /* --- SPLASH SCREEN (ABERTURA) --- */
        #splash-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: radial-gradient(circle, #1a0a2e 0%, #000 100%);
            display: flex; align-items: center; justify-content: center;
            z-index: 2000; 
            transition: opacity 2.5s ease-in-out, visibility 2.5s;
        }

        .splash-content { 
            position: relative;
            display: flex; 
            align-items: center; 
            justify-content: center;
            width: 100%;
            height: 100%;
        }

        .center-area { 
            display: flex;
            flex-direction: column;
            align-items: center; 
            text-align: center; 
        }

        /* --- BARRA DE UPDATES --- */
        .updates-sidebar {
            position: absolute;
            right: 20px;
            top: 50%;
            transform: translateY(-50%);
            width: 260px;
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid rgba(0, 210, 255, 0.3);
            border-radius: 15px;
            padding: 20px;
            color: white;
            text-align: left;
            backdrop-filter: blur(10px);
        }

        .updates-sidebar h3 { 
            margin: 0 0 10px 0; 
            color: #00d2ff; 
            font-size: 18px; 
            border-bottom: 2px solid #00d2ff;
            text-transform: uppercase;
        }

        .updates-sidebar ul { padding-left: 15px; margin: 0; font-size: 14px; color: #ccc; list-style-type: '🚀 '; }
        .updates-sidebar li { margin-bottom: 5px; }

        .wait-alert {
            font-size: 11px;
            color: #f1c40f;
            font-weight: bold;
            margin-bottom: 10px;
            display: block;
            text-transform: uppercase;
        }

        .triangle-logo {
            width: 120px; height: 120px;
            position: relative; margin-bottom: 20px;
            background: linear-gradient(135deg, #00d2ff 0%, #9b59b6 100%);
            clip-path: polygon(50% 0%, 0% 100%, 100% 100%, 50% 0%, 50% 20%, 80% 85%, 20% 85%, 50% 20%);
            filter: drop-shadow(0 0 15px #9b59b6);
            animation: logo-reveal 2.5s ease-out forwards;
        }

        .splash-text {
            color: #fff; font-size: clamp(28px, 6vw, 55px); font-weight: 900;
            text-transform: uppercase; letter-spacing: 3px;
            text-shadow: 0 0 5px #9b59b6, 0 0 15px #9b59b6, 0 0 30px #8e44ad;
            animation: text-reveal 3s ease-out forwards;
            opacity: 0;
            margin-bottom: 30px;
        }

        .btn, .enter-btn, #jump-button, #char-selection > div > div {
            transition: transform 0.2s cubic-bezier(0.175, 0.885, 0.32, 1.275), filter 0.2s, box-shadow 0.2s;
            cursor: pointer;
        }

        .btn:hover, .enter-btn:hover, #jump-button:hover, #char-selection > div > div:hover {
            transform: scale(1.1);
            filter: brightness(1.2);
            box-shadow: 0 0 20px rgba(255, 255, 255, 0.4);
        }

        .enter-btn {
            background: #00d2ff;
            color: #000;
            border: none;
            padding: 15px 50px;
            font-size: 20px;
            font-weight: 900;
            border-radius: 50px;
            text-transform: uppercase;
            box-shadow: 0 0 20px rgba(0, 210, 255, 0.5);
        }

        @keyframes logo-reveal {
            0% { transform: scale(0.5) rotate(-10deg); opacity: 0; }
            100% { transform: scale(1) rotate(0deg); opacity: 1; }
        }

        @keyframes text-reveal {
            0% { opacity: 0; transform: translateY(20px); }
            100% { opacity: 1; transform: translateY(0); }
        }

        #rotate-screen {
            display: none; position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: #2980b9; color: white;
            z-index: 1000; text-align: center;
            flex-direction: column; align-items: center; justify-content: center;
        }
        .rotate-icon { font-size: 80px; animation: rotating 2s infinite; margin-bottom: 20px; }
        @keyframes rotating { 0% { transform: rotate(0deg); } 50% { transform: rotate(90deg); } 100% { transform: rotate(0deg); } }
        @media (orientation: portrait) { #rotate-screen { display: flex; } }

        #ui-layer {
            position: absolute; width: 100%; height: 100%; top: 0; left: 0;
            display: flex; align-items: center; justify-content: center;
            flex-direction: column; z-index: 50; padding: 10px; box-sizing: border-box;
        }

        .menu-box {
            background: rgba(45, 79, 26, 0.85); padding: 25px; border-radius: 30px;
            backdrop-filter: blur(8px); border: 4px solid #f1c40f; text-align: center;
            color: white; box-shadow: 0 0 30px rgba(0,0,0,0.5); 
            max-width: 95%; width: 350px; box-sizing: border-box;
            display: flex; flex-direction: column; align-items: center;
        }

        .game-title {
            font-size: clamp(24px, 7vw, 40px); color: #f1c40f; font-weight: 900; margin-bottom: 20px;
            text-shadow: 3px 3px 0px #000; animation: glow 1.5s infinite alternate ease-in-out;
            line-height: 1;
        }

        @keyframes glow { from { transform: scale(1); } to { transform: scale(1.05); } }

        .btn-group { display: flex; flex-direction: column; gap: 12px; width: 100%; align-items: center; }

        .btn {
            background: #e67e22; color: white; border: none; padding: 12px 20px;
            font-size: 18px; border-radius: 50px;
            font-weight: bold; box-shadow: 0 6px #a3520e;
            width: 100%; max-width: 240px; text-transform: uppercase;
            box-sizing: border-box; display: block;
        }
        
        .social-link {
            color: #00d2ff;
            text-decoration: none;
            font-size: 14px;
            display: block;
            margin: 5px 0;
            font-weight: bold;
        }
        .social-link:hover { color: #fff; }

        #hud { position: absolute; bottom: 0; left: 0; width: 100%; height: 100%; pointer-events: none; z-index: 60; display: none; }
        #joystick-area { position: absolute; bottom: 30px; left: 30px; width: 100px; height: 100px; background: rgba(255,255,255,0.15); border-radius: 50%; pointer-events: auto; border: 3px solid rgba(255,255,255,0.4); backdrop-filter: blur(4px); touch-action: none; }
        #joystick-stick { position: absolute; top: 30px; left: 30px; width: 40px; height: 40px; background: #fff; border-radius: 50%; box-shadow: 0 4px 10px rgba(0,0,0,0.3); pointer-events: none; }
        #jump-button { position: absolute; bottom: 30px; right: 30px; width: 90px; height: 90px; background: rgba(255,255,255,0.25); border: 4px solid #fff; border-radius: 50%; color: #fff; display: flex; align-items: center; justify-content: center; font-weight: 900; pointer-events: auto; font-size: 18px; touch-action: none; box-shadow: 0 6px 15px rgba(0,0,0,0.2); text-shadow: 1px 1px 3px rgba(0,0,0,0.5); }
        #zoom-container { position: absolute; top: 15px; right: 15px; background: rgba(0,0,0,0.6); padding: 10px; border-radius: 15px; display: none; flex-direction: column; align-items: center; gap: 5px; pointer-events: auto; z-index: 100; border: 1px solid rgba(255,255,255,0.3); }
        #zoom-container label { color: white; font-size: 12px; font-weight: bold; }
        #zoom-slider { cursor: pointer; width: 100px; }
        #stats { position: absolute; top: 15px; left: 15px; z-index: 30; background: rgba(0,0,0,0.4); padding: 12px 20px; border-radius: 20px; color: white; display: flex; flex-direction: column; gap: 5px; backdrop-filter: blur(5px); border: 2px solid rgba(255,255,255,0.1); }
        .real-heart { color: #ff4757; font-size: 18px; margin-right: 2px; }
        .real-star { color: #f1c40f; margin-right: 8px; }
        #star-counter { font-size: 20px; color: #f1c40f; font-weight: bold; display: flex; align-items: center; }
    </style>
</head>
<body>

    <!-- ELEMENTO DE CONQUISTA -->
    <div id="achievement-toast"></div>

    <!-- MÚSICA DO JOGO -->
    <audio id="game-music" loop crossorigin="anonymous">
        <source src="https://r2---sn-voq5bpacg-28il.googlevideo.com/videoplayback?expire=1777764276&ei=VDP2aca6NsTFkucP_I-yoQU&ip=2600%3A4040%3A29c9%3Ad900%3A9547%3A8a29%3Aac56%3A51e5&id=o-AI4mjSU_pEXgWvTk_fOnBFFZgAfsYRuXXRWLv1PmDjwJ&itag=140&source=youtube&requiressl=yes&xpc=EgVo2aDSNQ%3D%3D&rms=au%2Cau&bui=AbKmrwpOUBAdbrc_X71vClaLOfHUJu0ipQvHPh76bddysue0u3u2Yu8-g8rbBUG5nEil2P1uHw7PwmLL&spc=96Xrv4iQ59Vs46bPoatoNAY03-YLM0RI7KDr30lJaaBnETI4YsMmj-JyH7Oj6iti6hmBAA&vprv=1&svpuc=1&mime=audio%2Fmp4&ns=ymB3zU63K7nq_4t_WETI1SMU&rqh=1&gir=yes&clen=1944448&dur=120.093&lmt=1745415179958301&keepalive=yes&fexp=51565115%2C51565682%2C51887890&c=WEB_EMBEDDED_PLAYER&sefc=1&txp=5432534&n=uirbixo9pUWHWg&sparams=expire%2Cei%2Cip%2Cid%2Citag%2Csource%2Crequiressl%2Cxpc%2Cbui%2Cspc%2Cvprv%2Csvpuc%2Cmime%2Cns%2Crqh%2Cgir%2Cclen%2Cdur%2Clmt&sig=AHEqNM4wRgIhAK6gmQUvirjtaR3mx5glFMTcULKLMCG7mo6X5bbtCpOkAiEA_XGSbMNeDdRETU20xb81cOoPbOq8kc--GD3Tydd_sz4%3D&cms_redirect=yes&cps=108&met=1777742807,&mh=Xf&mip=2804:8c7c:871b:e700:8de3:8ee6:1061:ec93&mm=31&mn=sn-voq5bpacg-28il&ms=au&mt=1777742219&mv=m&mvi=2&pcm2cms=yes&pl=42&lsparams=cps,met,mh,mip,mm,mn,ms,mv,mvi,pcm2cms,pl,rms&lsig=APaTxxMwRQIhAOCbTNpQt8yjqmbE8N2x18xDt0M3n8o5jfMzN_T61KylAiBJlS4X47SOqbySOtUdb39siqRBe4vvkiV7IhzBNyF9oA%3D%3D" type="audio/mp4">
    </audio>

    <audio id="som-magico"><source src="https://assets.mixkit.co/active_storage/sfx/2013/2013-preview.mp3" type="audio/mpeg"></audio>
    <audio id="som-click"><source src="https://www.fesliyanstudios.com/play-mp3/6" type="audio/mpeg"></audio>
    <audio id="som-morte"><source src="https://www.myinstants.com/media/sounds/super-mario-death.mp3" type="audio/mpeg"></audio>
    
    <!-- NOVOS SONS DE BIT (RETRO) -->
    <audio id="som-pulo"><source src="https://assets.mixkit.co/active_storage/sfx/2571/2571-preview.mp3" type="audio/mpeg"></audio>
    <audio id="som-estrela"><source src="https://assets.mixkit.co/active_storage/sfx/2019/2019-preview.mp3" type="audio/mpeg"></audio>
    <audio id="som-passo"><source src="https://www.fesliyanstudios.com/play-mp3/411" type="audio/mpeg"></audio>

    <div id="splash-screen">
        <div class="splash-content">
            <div class="center-area">
                <div class="triangle-logo"></div>
                <div class="splash-text">Lendário Studio<br><span style="font-size: 0.4em; letter-spacing: 10px; color: #00d2ff;">APRESENTA</span></div>
                <button class="enter-btn" onclick="playClick(); iniciarAbertura()">ENTRAR</button>
            </div>

            <div class="updates-sidebar">
                <h3>UPDATES DO JOGO</h3>
                <p style="font-size: 12px; color: #00d2ff; margin-bottom: 2px;">Versão 10.1</p>
                <span class="wait-alert">Espera carregar para poder clicar no botão. Aguarde!</span>
                <ul>
                    <li>Trilha sonora de alta qualidade</li>
                    <li>Novo menu da Lendário Studio</li>
                    <li>Novos sons de clique</li>
                    <li>Mapa otimizado</li>
                    <li>Sistema de som de morte</li>
                </ul>
            </div>
        </div>
    </div>

    <div id="rotate-screen">
        <div class="rotate-icon">📱🔄</div>
        <h2>VIRE A TELA</h2>
        <p>Gire para o modo deitado para jogar!</p>
    </div>

    <div id="ui-layer">
        <div id="main-menu" class="menu-box">
            <h1 class="game-title">LENDÁRIO<br>PARKOUR X</h1>
            <div class="btn-group">
                <button class="btn" onclick="playClick(); openCharSelect()">JOGAR</button>
                <button class="btn" style="background:#3498db;" onclick="playClick(); showInfo()">INFORMAÇÕES</button>
            </div>
        </div>

        <div id="info-menu" class="menu-box" style="display: none; text-align: left;">
            <h2 style="text-align:center">INFORMAÇÕES</h2>
            <p><b>Versão:</b> 10.1</p>
            <p><b>Criador:</b> Lendário Studio</p>
            <p><b>Direitos:</b> © 2026</p>
            <hr style="border: 0; border-top: 1px solid rgba(255,255,255,0.2); margin: 10px 0;">
            <p><b>REDES SOCIAIS:</b></p>
            <a href="https://www.youtube.com/@TheLuanGalaxyYT" target="_blank" class="social-link"><i class="fab fa-youtube"></i> YouTube</a>
            <a href="https://luancraft39.itch.io" target="_blank" class="social-link"><i class="fab fa-itch-io"></i> Itch.io</a>
            <a href="https://lucianotrabalhoemc.wixsite.com/meusite" target="_blank" class="social-link"><i class="fas fa-globe"></i> Site Oficial</a>
            <center style="margin-top: 15px;"><button class="btn" onclick="playClick(); closeInfo()">VOLTAR</button></center>
        </div>

        <div id="char-selection" class="menu-box" style="display: none;">
            <h2>ESCOLHA SUA SKIN</h2>
            <div style="display: flex; gap: 10px; justify-content: center; margin-bottom: 20px;">
                <div onclick="playClick(); selectChar('boy')" style="cursor:pointer; background:rgba(255,255,255,0.1); padding:10px; border-radius:15px;">
                    <canvas id="preview-boy" width="50" height="65"></canvas>
                    <p style="margin:5px 0 0 0; font-size:12px;">MENINO</p>
                </div>
                <div onclick="playClick(); selectChar('girl')" style="cursor:pointer; background:rgba(255,255,255,0.1); padding:10px; border-radius:15px;">
                    <canvas id="preview-girl" width="50" height="65"></canvas>
                    <p style="margin:5px 0 0 0; font-size:12px;">MENINA</p>
                </div>
            </div>
            <button class="btn" id="start-btn" style="display: block; background: rgb(46, 204, 113);" onclick="playClick(); finalizeGameStart()">INICIAR!</button>
        </div>

        <div id="death" class="menu-box" style="display:none">
            <h2>VOCÊ CAIU!</h2>
            <div class="btn-group">
                <button class="btn" onclick="playClick(); continueGame()">CONTINUAR</button>
                <button class="btn" style="background:#7f8c8d;" onclick="playClick(); resetToMenu()">VOLTAR PRO MENU</button>
            </div>
        </div>
    </div>

    <div id="hud">
        <div id="joystick-area"><div id="joystick-stick"></div></div>
        <div id="jump-button">PULO</div>
        <div id="zoom-container">
            <label><i class="fa-solid fa-magnifying-glass"></i> ZOOM</label>
            <input type="range" id="zoom-slider" min="0.4" max="1.2" step="0.1" value="0.8">
        </div>
    </div>

    <div id="stats">
        <div id="hearts"></div>
        <div id="star-counter">
            <i class="fa-solid fa-star real-star"></i> 
            <span id="star-count-text">0</span>
        </div>
    </div>
    <canvas id="game"></canvas>

<script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");
    const music = document.getElementById("game-music");
    const somMagico = document.getElementById("som-magico");
    const somClick = document.getElementById("som-click");
    const somMorte = document.getElementById("som-morte");
    const somPulo = document.getElementById("som-pulo");
    const somEstrela = document.getElementById("som-estrela");
    const somPasso = document.getElementById("som-passo");
    const splash = document.getElementById("splash-screen");
    const zoomSlider = document.getElementById("zoom-slider");
    const zoomContainer = document.getElementById("zoom-container");
    const achievementToast = document.getElementById("achievement-toast");
    
    let musicStarted = false;
    let aberturaIniciada = false;
    let customZoom = 0.8;
    let stepTimer = 0;

    function playClick() {
        somClick.currentTime = 0;
        somClick.volume = 0.5;
        somClick.play().catch(e => {});
    }

    function playMorte() {
        somMorte.currentTime = 0;
        somMorte.play().catch(e => {});
    }

    // Função para mostrar conquistas
    function showAchievement(msg) {
        achievementToast.innerText = "⭐ CONQUISTA: " + msg;
        achievementToast.style.display = "block";
        setTimeout(() => { achievementToast.style.display = "none"; }, 1500);
    }

    function iniciarAbertura() {
        if (aberturaIniciada) return;
        aberturaIniciada = true;
        somMagico.currentTime = 0;
        somMagico.play().catch(e => {});
        splash.style.opacity = '0';
        
        if(!musicStarted) { 
            music.currentTime = 0;
            music.volume = 0.6;
            music.play().catch(e => console.log("Erro de áudio:", e)); 
            musicStarted = true; 
        }
        
        setTimeout(() => { splash.style.visibility = 'hidden'; }, 2000);
    }

    function resize() { canvas.width = window.innerWidth; canvas.height = window.innerHeight; }
    window.addEventListener('resize', resize);
    resize();

    let gameState = 'menu';
    let lives = 10;
    let starsCollected = 0;
    let selectedHero = 'boy';
    let lastX = 0, lastY = 0;
    let platforms = [], stars = [], keys = {};
    let player = { x: 100, y: 0, w: 45, h: 60, vx: 0, vy: 0, speed: 7, jump: 18, onGround: false };
    let camera = { x: 0, y: 0 }; 
    let joystick = { x: 0, active: false, identifier: null };

    window.addEventListener("keydown", (e) => { keys[e.code] = true; });
    window.addEventListener("keyup", (e) => { keys[e.code] = false; });

    const jumpBtn = document.getElementById("jump-button");
    const joyArea = document.getElementById("joystick-area");
    const joyStick = document.getElementById("joystick-stick");

    function handleJump() {
        if(player.onGround && gameState === 'playing') { 
            player.vy = -player.jump; 
            player.onGround = false; 
            somPulo.currentTime = 0;
            somPulo.play().catch(e=>{});
        }
    }

    jumpBtn.addEventListener("pointerdown", (e) => { e.preventDefault(); playClick(); handleJump(); });
    joyArea.addEventListener("pointerdown", (e) => {
        e.preventDefault();
        joystick.active = true;
        joystick.identifier = e.pointerId;
        joyArea.setPointerCapture(e.pointerId);
        moveJoystick(e);
    });

    window.addEventListener("pointermove", (e) => { if(joystick.active && e.pointerId === joystick.identifier) moveJoystick(e); });
    window.addEventListener("pointerup", (e) => {
        if(joystick.active && e.pointerId === joystick.identifier) {
            joystick.active = false; joystick.x = 0; joystick.identifier = null;
            joyStick.style.left = "30px";
        }
    });

    function moveJoystick(e) {
        let rect = joyArea.getBoundingClientRect();
        let centerX = rect.left + rect.width/2;
        let dx = e.clientX - centerX;
        let normX = Math.max(-1, Math.min(1, dx / 40));
        joystick.x = normX;
        joyStick.style.left = (30 + normX * 25) + "px";
    }

    function showInfo() { document.getElementById("main-menu").style.display = "none"; document.getElementById("info-menu").style.display = "block"; }
    function closeInfo() { document.getElementById("info-menu").style.display = "none"; document.getElementById("main-menu").style.display = "flex"; }
    function openCharSelect() { 
        document.getElementById("main-menu").style.display = "none"; 
        document.getElementById("char-selection").style.display = "flex"; 
        drawPixelChar(document.getElementById("preview-boy").getContext("2d"), 'boy', 2, 2); 
        drawPixelChar(document.getElementById("preview-girl").getContext("2d"), 'girl', 2, 2); 
    }
    function selectChar(type) { selectedHero = type; }
    
    function finalizeGameStart() { 
        document.getElementById("char-selection").style.display = "none"; 
        document.getElementById("hud").style.display = "block"; 
        zoomContainer.style.display = "flex";
        starsCollected = 0; updateUI(); resetMap(); 
        lives = 10; updateHearts(); gameState = 'playing'; 
    }

    function resetToMenu() { 
        gameState = 'menu'; 
        document.getElementById("death").style.display = "none"; 
        document.getElementById("hud").style.display = "none"; 
        zoomContainer.style.display = "none"; 
        document.getElementById("main-menu").style.display = "flex";
        resetMap(); 
    }

    function continueGame() { 
        document.getElementById("death").style.display = "none"; 
        player.x = camera.x + 100; player.y = lastY - 150; 
        player.vx = 0; player.vy = 0; gameState = 'playing'; 
    }
    
    function updateHearts() { 
        let h = "";
        for(let i=0; i<lives; i++) h += '<i class="fa-solid fa-heart real-heart"></i>';
        document.getElementById("hearts").innerHTML = h; 
    }
    function updateUI() { document.getElementById("star-count-text").innerText = starsCollected; }

    function drawPixelChar(c, type, x, y) {
        c.save(); c.translate(x, y);
        if(type === 'boy') {
            c.fillStyle = "#5d4037"; c.fillRect(10, 0, 30, 15);
            c.fillStyle = "#ffdbac"; c.fillRect(10, 15, 30, 20);
            c.fillStyle = "#000"; c.fillRect(15, 22, 5, 5); c.fillRect(30, 22, 5, 5);
            c.fillStyle = "#1976d2"; c.fillRect(10, 35, 30, 20);
            c.fillStyle = "#fff"; c.fillRect(20, 40, 10, 5);
            c.fillStyle = "#263238"; c.fillRect(10, 55, 30, 10);
        } else {
            c.fillStyle = "#fbc02d"; c.fillRect(5, 5, 40, 45);
            c.fillStyle = "#d81b60"; c.fillRect(10, 2, 30, 5);
            c.fillStyle = "#ffdbac"; c.fillRect(10, 10, 30, 20);
            c.fillStyle = "#000"; c.fillRect(15, 18, 5, 5); c.fillRect(30, 18, 5, 5);
            c.fillStyle = "#fff"; c.fillRect(17, 18, 2, 2); c.fillRect(32, 18, 2, 2);
            c.fillStyle = "#f06292"; c.fillRect(10, 30, 30, 25);
            c.fillStyle = "#f8bbd0"; c.fillRect(10, 50, 30, 5);
            c.fillStyle = "#880e4f"; c.fillRect(10, 55, 30, 10);
        }
        c.restore();
    }

    function drawMapTexture(c, p) {
        c.fillStyle = "#8d6e3e"; c.fillRect(p.x, p.y, p.w, p.h);
        c.fillStyle = "#46c33d"; c.fillRect(p.x, p.y, p.w, 25);
        for(let i = 0; i <= p.w; i += 25) { c.beginPath(); c.arc(p.x + i, p.y + 25, 15, 0, Math.PI); c.fill(); }
    }

    function drawStar(c, s) {
        if(s.collected) return;
        c.save();
        let time = Date.now() * 0.005;
        let bounce = Math.sin(time) * 10;
        c.fillStyle = "#f1c40f"; c.shadowColor = "yellow"; c.shadowBlur = 10;
        c.beginPath();
        for(let i=0; i<5; i++) {
            c.lineTo(s.x + Math.cos((18+i*72)/180*Math.PI)*15, s.y + bounce + Math.sin((18+i*72)/180*Math.PI)*15);
            c.lineTo(s.x + Math.cos((54+i*72)/180*Math.PI)*7, s.y + bounce + Math.sin((54+i*72)/180*Math.PI)*7);
        }
        c.closePath(); c.fill(); c.restore();
    }

    function resetMap() { 
        platforms = []; stars = []; 
        lastY = (canvas.height / customZoom) * 0.65; 
        platforms.push({ x: 0, y: lastY, w: 800, h: 800 }); 
        lastX = 850; player.x = 100; player.y = lastY - 100; 
        camera.x = 0; camera.y = lastY - (canvas.height / customZoom) / 1.5;
    }

    function update() {
        if(gameState === 'menu') { player.vx = 2.5; }
        else if(gameState === 'playing') {
            if(keys["ArrowRight"] || keys["KeyD"]) player.vx = player.speed;
            else if(keys["ArrowLeft"] || keys["KeyA"]) player.vx = -player.speed;
            else if(joystick.active) player.vx = player.speed * joystick.x;
            else player.vx *= 0.8;
            
            if((keys["Space"] || keys["ArrowUp"] || keys["KeyW"])) handleJump();

            if(player.onGround && Math.abs(player.vx) > 1) {
                stepTimer++;
                if(stepTimer > 20) {
                    somPasso.currentTime = 0;
                    somPasso.volume = 0.3;
                    somPasso.play().catch(e=>{});
                    stepTimer = 0;
                }
            }

            stars.forEach(s => { 
                if(!s.collected && Math.abs(player.x - s.x) < 40 && Math.abs(player.y - s.y) < 60) { 
                    s.collected = true; 
                    starsCollected++; 
                    
                    // Lógica de Conquistas
                    let msg = "Estrela Coletada!";
                    if(starsCollected === 1) msg = "Primeira de Muitas!";
                    if(starsCollected === 5) msg = "Explorador Lendário!";
                    if(starsCollected === 10) msg = "Mestre das Estrelas!";
                    if(starsCollected === 20) msg = "Rei do Parkour!";
                    if(starsCollected > 20 && starsCollected % 10 === 0) msg = starsCollected + " Estrelas!";
                    showAchievement(msg);

                    somEstrela.currentTime = 0;
                    somEstrela.play().catch(e=>{});
                    updateUI(); 
                } 
            });
        }
        player.vy += 0.8; player.x += player.vx; player.y += player.vy;
        player.onGround = false;
        platforms.forEach(p => { if(player.x + player.w > p.x && player.x < p.x + p.w) { if(player.y + player.h > p.y && player.y + player.h < p.y + player.vy + 10 && player.vy >= 0) { player.y = p.y - player.h; player.vy = 0; player.onGround = true; } } });
        camera.x += ((player.x - (canvas.width / customZoom) / 3) - camera.x) * 0.1;
        
        if(lastX < player.x + (canvas.width / customZoom)) { 
            let gap = 170 + Math.random() * 80; let pW = 250 + Math.random() * 100; 
            lastY = Math.max((canvas.height / customZoom) * 0.4, Math.min((canvas.height / customZoom) * 0.7, lastY + (Math.random()-0.5) * 150)); 
            platforms.push({ x: lastX + gap, y: lastY, w: pW, h: 800 }); 
            if(Math.random() > 0.4) stars.push({ x: lastX + gap + pW/2, y: lastY - 60, collected: false });
            lastX += gap + pW; 
        }

        if(player.y > (canvas.height / customZoom) + 800) { 
            if(gameState === 'playing') { 
                playMorte();
                gameState = 'paused'; lives--; updateHearts(); 
                if(lives <= 0) resetToMenu(); else document.getElementById("death").style.display = "flex"; 
            } else if(gameState === 'menu') { player.y = lastY - 100; player.vx = 2.5; }
        }
    }

    function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = "#d4efb5"; ctx.fillRect(0, 0, canvas.width, canvas.height);
        ctx.save(); ctx.scale(customZoom, customZoom); ctx.translate(-camera.x, -camera.y); 
        platforms.forEach(p => drawMapTexture(ctx, p));
        stars.forEach(s => drawStar(ctx, s));
        drawPixelChar(ctx, selectedHero, player.x, player.y);
        ctx.restore();
    }

    zoomSlider.addEventListener("input", (e) => { customZoom = parseFloat(e.target.value); });
    function loop() { update(); draw(); requestAnimationFrame(loop); }
    resetMap();
    loop();
</script>
</body>
</html>
