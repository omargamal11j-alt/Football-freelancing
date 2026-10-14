# Football-freelancing
Messi
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>FootLance Legends - أساطير الملاعب</title>
    <!-- PeerJS للعب الجماعي عبر شبكة الواي فاي أو أونلاين -->
    <script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>
    <!-- Tailwind CSS للتصميم العصري -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cairo:wght@400;700;900&display=swap');
        
        * {
            user-select: none;
            -webkit-user-select: none;
            font-family: 'Cairo', sans-serif;
            touch-action: manipulation;
        }

        body {
            background: #060913;
            color: #fff;
            overflow: hidden;
        }

        .glow-blue {
            box-shadow: 0 0 20px rgba(0, 210, 255, 0.5);
        }

        .glow-gold {
            box-shadow: 0 0 25px rgba(255, 215, 0, 0.6);
        }

        canvas {
            border-radius: 12px;
            background: radial-gradient(circle, #1e7032 0%, #0f401b 100%);
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.8), inset 0 0 15px rgba(255,255,255,0.1);
        }

        .touch-btn {
            background: rgba(255, 255, 255, 0.12);
            backdrop-filter: blur(8px);
            border: 2px solid rgba(255, 255, 255, 0.3);
            transition: all 0.1s ease;
        }

        .touch-btn:active {
            transform: scale(0.92);
            background: rgba(0, 210, 255, 0.4);
        }

        .flash-overlay {
            position: absolute;
            inset: 0;
            background: white;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.15s ease-out;
            border-radius: 12px;
        }
    </style>
</head>
<body class="flex flex-col items-center justify-center min-h-screen p-2">

    <!-- 1. شاشة البداية واختيار نمط اللعب -->
    <div id="start-modal" class="fixed inset-0 z-50 flex items-center justify-center bg-black/90 backdrop-blur-md p-4">
        <div class="bg-slate-900 border-2 border-cyan-400 rounded-2xl p-6 max-w-md w-full text-center shadow-2xl glow-blue">
            <h1 class="text-3xl font-black text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 to-yellow-400 mb-1">
                ⚡ FootLance Legends ⚡
            </h1>
            <p class="text-xs text-cyan-200 mb-6">لعبة كرة القدم الأسطورية: ميسي، صورتك الـ VIP، والاحتفال الملحمي بـ "عمر جمال"</p>

            <div class="space-y-4 mb-6">
                <div>
                    <label class="block text-right text-xs text-gray-300 mb-1">اسمك الكروي:</label>
                    <input type="text" id="player-name-input" value="عمر جمال" class="w-full bg-slate-800 border border-slate-600 rounded-lg p-2.5 text-center text-cyan-300 font-bold focus:outline-none focus:border-cyan-400">
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <button onclick="setPlayerType('messi')" id="btn-messi" class="p-3 bg-slate-800 border-2 border-yellow-400 rounded-xl flex flex-col items-center justify-center gap-1 active:scale-95 transition">
                        <span class="text-2xl">🐐</span>
                        <span class="text-xs font-bold text-yellow-400">ليونيل ميسي</span>
                    </button>
                    <button onclick="setPlayerType('vip')" id="btn-vip" class="p-3 bg-slate-800 border-2 border-cyan-400 rounded-xl flex flex-col items-center justify-center gap-1 active:scale-95 transition">
                        <span class="text-2xl">👑</span>
                        <span class="text-xs font-bold text-cyan-400">صورتك الـ VIP</span>
                    </button>
                </div>
            </div>

            <div class="grid grid-cols-1 gap-3">
                <button onclick="startSinglePlayer()" class="w-full py-3 bg-gradient-to-r from-cyan-500 to-blue-600 font-black rounded-xl text-black shadow-lg hover:brightness-110 active:scale-95 transition">
                    🎮 لعب فردي (تحدي الحارس)
                </button>
                <button onclick="showMultiplayerMenu()" class="w-full py-3 bg-gradient-to-r from-yellow-400 to-amber-500 font-black rounded-xl text-black shadow-lg hover:brightness-110 active:scale-95 transition">
                    📡 لعب مع صديق (واي فاي / أونلاين)
                </button>
            </div>
        </div>
    </div>

    <!-- 2. قائمة اللعب الجماعي (Multiplayer) -->
    <div id="multiplayer-modal" class="fixed inset-0 z-50 hidden items-center justify-center bg-black/90 backdrop-blur-md p-4">
        <div class="bg-slate-900 border-2 border-yellow-400 rounded-2xl p-6 max-w-md w-full text-center shadow-2xl glow-gold">
            <h2 class="text-2xl font-black text-yellow-400 mb-4">📡 اللعب مع صديق عبر الشبكة</h2>
            <p class="text-xs text-gray-300 mb-6">تنافس مع زميلك على تليفون تاني في FootLance Legends!</p>

            <div class="space-y-4">
                <div class="bg-slate-800/80 p-4 rounded-xl border border-slate-700">
                    <h3 class="text-sm font-bold text-cyan-300 mb-2">خيار 1: أنشئ غرفة وأعطِ الكود لصديقك</h3>
                    <button onclick="createRoom()" class="w-full py-2 bg-cyan-500 text-slate-950 font-bold rounded-lg mb-2">إنشاء غرفة جديدة 🔑</button>
                    <div id="room-code-display" class="hidden p-2 bg-slate-950 rounded border border-cyan-500 text-yellow-300 font-mono text-lg font-bold select-all"></div>
                </div>

                <div class="bg-slate-800/80 p-4 rounded-xl border border-slate-700">
                    <h3 class="text-sm font-bold text-yellow-300 mb-2">خيار 2: أدخل كود غرفة صديقك</h3>
                    <input type="text" id="join-room-input" placeholder="أدخل كود الغرفة..." class="w-full bg-slate-950 border border-slate-600 rounded-lg p-2 text-center text-white mb-2 font-mono">
                    <button onclick="joinRoom()" class="w-full py-2 bg-yellow-400 text-slate-950 font-bold rounded-lg">انضمام للمباراة ⚽</button>
                </div>
            </div>

            <button onclick="backToMainMenu()" class="mt-6 text-xs text-gray-400 underline">العودة للقائمة الرئيسية</button>
        </div>
    </div>

    <!-- 3. شاشة اللعبة الرئيسية -->
    <div id="game-ui" class="flex flex-col items-center w-full max-w-4xl">
        <div class="w-full flex justify-between items-center bg-slate-900/90 border border-cyan-500/40 px-4 py-2 rounded-xl mb-2 shadow-lg">
            <div class="flex items-center gap-2">
                <span class="text-xs bg-cyan-500 text-black font-bold px-2 py-0.5 rounded">لاعب 1</span>
                <span id="p1-name" class="font-bold text-sm text-cyan-300">عمر جمال</span>
                <span id="p1-score" class="text-xl font-black text-yellow-400 bg-slate-950 px-3 py-0.5 rounded-lg border border-slate-800">0</span>
            </div>

            <div class="text-center">
                <span id="match-status" class="text-xs font-bold text-yellow-400 block">⚡ FootLance Legends</span>
                <span id="network-status" class="text-[10px] text-gray-400">لعب فردي</span>
            </div>

            <div class="flex items-center gap-2">
                <span id="p2-score" class="text-xl font-black text-yellow-400 bg-slate-950 px-3 py-0.5 rounded-lg border border-slate-800">0</span>
                <span id="p2-name" class="font-bold text-sm text-red-400">الحارس الآلي</span>
                <span class="text-xs bg-red-500 text-black font-bold px-2 py-0.5 rounded">منافس</span>
            </div>
        </div>

        <div class="relative w-full flex justify-center">
            <canvas id="gameCanvas" width="800" height="420" class="w-full h-auto max-h-[60vh] object-contain"></canvas>
            <div id="camera-flash" class="flash-overlay"></div>
        </div>

        <div class="w-full flex justify-between items-center mt-3 px-2 gap-4">
            <div class="grid grid-cols-3 gap-1.5 w-36 h-36">
                <div></div>
                <button class="touch-btn rounded-xl text-xl flex items-center justify-center text-white" ontouchstart="setKey('ArrowUp', true)" ontouchend="setKey('ArrowUp', false)" onmousedown="setKey('ArrowUp', true)" onmouseup="setKey('ArrowUp', false)">⬆️</button>
                <div></div>
                <button class="touch-btn rounded-xl text-xl flex items-center justify-center text-white" ontouchstart="setKey('ArrowLeft', true)" ontouchend="setKey('ArrowLeft', false)" onmousedown="setKey('ArrowLeft', true)" onmouseup="setKey('ArrowLeft', false)">⬅️</button>
                <button class="touch-btn rounded-xl text-xl flex items-center justify-center text-white" ontouchstart="setKey('ArrowDown', true)" ontouchend="setKey('ArrowDown', false)" onmousedown="setKey('ArrowDown', true)" onmouseup="setKey('ArrowDown', false)">⬇️</button>
                <button class="touch-btn rounded-xl text-xl flex items-center justify-center text-white" ontouchstart="setKey('ArrowRight', true)" ontouchend="setKey('ArrowRight', false)" onmousedown="setKey('ArrowRight', true)" onmouseup="setKey('ArrowRight', false)">➡️</button>
            </div>

            <div class="flex items-center gap-3">
                <button class="touch-btn w-16 h-16 rounded-full bg-gradient-to-tr from-cyan-500 to-blue-600 border-2 border-cyan-300 text-xs font-black text-black shadow-lg glow-blue flex flex-col items-center justify-center" ontouchstart="triggerBackwardKick()" onmousedown="triggerBackwardKick()">
                    <span>🔄</span>
                    <span>باكورد</span>
                </button>
                <button class="touch-btn w-20 h-20 rounded-full bg-gradient-to-tr from-red-500 to-pink-600 border-2 border-red-300 text-sm font-black text-white shadow-lg flex flex-col items-center justify-center" ontouchstart="setKey('Space', true)" ontouchend="setKey('Space', false)" onmousedown="setKey('Space', true)" onmouseup="setKey('Space', false)">
                    <span>🚀</span>
                    <span>تسديد</span>
                </button>
            </div>
        </div>
    </div>

    <script>
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        
        function playSound(type) {
            if (audioCtx.state === 'suspended') audioCtx.resume();
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.connect(gain);
            gain.connect(audioCtx.destination);

            if (type === 'kick') {
                osc.frequency.setValueAtTime(150, audioCtx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);
                gain.gain.setValueAtTime(1, audioCtx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);
                osc.start(); osc.stop(audioCtx.currentTime + 0.15);
            } else if (type === 'whistle') {
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(2500, audioCtx.currentTime);
                osc.frequency.setValueAtTime(2800, audioCtx.currentTime + 0.1);
                gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.4);
                osc.start(); osc.stop(audioCtx.currentTime + 0.4);
            } else if (type === 'goal') {
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(300, audioCtx.currentTime);
                osc.frequency.linearRampToValueAtTime(600, audioCtx.currentTime + 0.5);
                gain.gain.setValueAtTime(0.5, audioCtx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.8);
                osc.start(); osc.stop(audioCtx.currentTime + 0.8);
            }
        }

        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const flashEl = document.getElementById('camera-flash');

        let isMultiplayer = false;
        let isHost = false;
        let peer = null;
        let conn = null;

        let selectedHero = 'messi';
        let playerName = 'عمر جمال';
        
        let p1Score = 0;
        let p2Score = 0;
        const WINNING_SCORE = 3;

        let isGameOver = false;
        let isSlowMotion = false;
        let isFanInvasion = false;
        let fansList = [];

        const vipImage = new Image();
        vipImage.src = "15401.jpg";

        const keys = {};
        function setKey(code, val) { keys[code] = val; }

        window.addEventListener('keydown', e => keys[e.code] = true);
        window.addEventListener('keyup', e => keys[e.code] = false);

        const player1 = {
            x: 100, y: 180, width: 38, height: 50, speed: 4.5,
            hasBall: true, isBackward: false, backwardAngle: 0,
            type: 'messi', name: 'عمر جمال'
        };

        const player2 = {
            x: 660, y: 180, width: 38, height: 50, speed: 3.5,
            dir: 1, type: 'goalkeeper', name: 'الحارس'
        };

        const ball = {
            x: 135, y: 205, radius: 9, vx: 0, vy: 0, isMoving: false
        };

        const goalRight = { x: 760, y: 110, width: 25, height: 200 };

        function setPlayerType(type) {
            selectedHero = type;
            document.getElementById('btn-messi').style.borderColor = type === 'messi' ? '#facc15' : '#475569';
            document.getElementById('btn-vip').style.borderColor = type === 'vip' ? '#22d3ee' : '#475569';
        }

        function startSinglePlayer() {
            playerName = document.getElementById('player-name-input').value.trim() || 'عمر جمال';
            player1.name = playerName;
            player1.type = selectedHero;
            isMultiplayer = false;
            document.getElementById('p1-name').innerText = playerName;
            document.getElementById('p2-name').innerText = "الحارس الآلي";
            document.getElementById('network-status').innerText = "لعب فردي محلي";
            document.getElementById('start-modal').style.display = 'none';
            playSound('whistle');
            resetPositions();
        }

        function showMultiplayerMenu() {
            document.getElementById('start-modal').style.display = 'none';
            document.getElementById('multiplayer-modal').style.display = 'flex';
        }

        function backToMainMenu() {
            document.getElementById('multiplayer-modal').style.display = 'none';
            document.getElementById('start-modal').style.display = 'flex';
        }

        function initPeer() {
            if (!peer) {
                peer = new Peer();
                peer.on('connection', connection => {
                    conn = connection;
                    setupConnection();
                });
            }
        }

        function createRoom() {
            initPeer();
            isHost = true;
            isMultiplayer = true;
            peer.on('open', id => {
                document.getElementById('room-code-display').innerText = `رمز الغرفة: ${id}`;
                document.getElementById('room-code-display').classList.remove('hidden');
            });
        }

        function joinRoom() {
            const remoteId = document.getElementById('join-room-input').value.trim();
            if (!remoteId) return alert('يرجى كتابة رمز الغرفة صحيحاً!');
            initPeer();
            isHost = false;
            isMultiplayer = true;
            conn = peer.connect(remoteId);
            setupConnection();
        }

        function setupConnection() {
            conn.on('open', () => {
                document.getElementById('multiplayer-modal').style.display = 'none';
                document.getElementById('network-status').innerText = "متصل أونلاين 🟢";
                playerName = document.getElementById('player-name-input').value.trim() || 'عمر جمال';
                conn.send({ type: 'init', name: playerName, hero: selectedHero });
                playSound('whistle');
                resetPositions();
            });

            conn.on('data', data => {
                if (data.type === 'init') {
                    document.getElementById('p2-name').innerText = data.name;
                    player2.name = data.name;
                    player2.type = data.hero;
                } else if (data.type === 'state') {
                    if (!isHost) {
                        player1.x = data.p2.x; player1.y = data.p2.y;
                        player2.x = data.p1.x; player2.y = data.p1.y;
                        ball.x = canvas.width - data.ball.x;
                        ball.y = data.ball.y;
                    } else {
                        player2.x = data.p2.x; player2.y = data.p2.y;
                    }
                }
            });
        }

        function triggerBackwardKick() {
            if (!player1.isBackward) {
                player1.isBackward = true;
                player1.backwardAngle = 0;
                playSound('kick');
            }
        }

        function resetPositions() {
            player1.x = 100; player1.y = 180; player1.hasBall = true;
            player2.x = 660; player2.y = 180;
            ball.isMoving = false; ball.vx = 0; ball.vy = 0;
            ball.x = player1.x + 35; ball.y = player1.y + 25;
            isSlowMotion = false;
        }

        function initFanInvasion() {
            isFanInvasion = true;
            fansList = [];
            const textPoints = [];
            const tempCanvas = document.createElement('canvas');
            tempCanvas.width = 800; tempCanvas.height = 420;
            const tCtx = tempCanvas.getContext('2d');
            
            tCtx.font = "bold 65px Cairo, sans-serif";
            tCtx.fillStyle = "white";
            tCtx.textAlign = "center";
            tCtx.fillText("عمر جمال", 400, 230);

            const imgData = tCtx.getImageData(0, 0, 800, 420);
            for (let y = 0; y < 420; y += 8) {
                for (let x = 0; x < 800; x += 8) {
                    const index = (y * 800 + x) * 4;
                    if (imgData.data[index + 3] > 128) {
                        textPoints.push({ x: x, y: y });
                    }
                }
            }

            for (let i = 0; i < textPoints.length; i++) {
                const target = textPoints[i];
                const startSide = Math.floor(Math.random() * 4);
                let startX, startY;
                if (startSide === 0) { startX = Math.random() * 800; startY = -20; }
                else if (startSide === 1) { startX = 820; startY = Math.random() * 420; }
                else if (startSide === 2) { startX = Math.random() * 800; startY = 440; }
                else { startX = -20; startY = Math.random() * 420; }

                fansList.push({
                    x: startX, y: startY,
                    targetX: target.x, targetY: target.y,
                    speed: 3 + Math.random() * 4,
                    arrived: false,
                    shirtColor: `hsl(${Math.random() * 360}, 80%, 50%)`
                });
            }
        }

        function update() {
            if (isGameOver) {
                if (isFanInvasion) {
                    fansList.forEach(fan => {
                        if (!fan.arrived) {
                            const dx = fan.targetX - fan.x;
                            const dy = fan.targetY - fan.y;
                            const dist = Math.hypot(dx, dy);
                            if (dist < 4) {
                                fan.x = fan.targetX; fan.y = fan.targetY;
                                fan.arrived = true;
                            } else {
                                fan.x += (dx / dist) * fan.speed;
                                fan.y += (dy / dist) * fan.speed;
                            }
                        }
                    });
                }
                return;
            }

            let speedMult = isSlowMotion ? 0.3 : 1;

            if (keys['ArrowUp'] && player1.y > 10) player1.y -= player1.speed * speedMult;
            if (keys['ArrowDown'] && player1.y < canvas.height - player1.height - 10) player1.y += player1.speed * speedMult;
            if (keys['ArrowLeft'] && player1.x > 10) player1.x -= player1.speed * speedMult;
            if (keys['ArrowRight'] && player1.x < canvas.width - 60) player1.x += player1.speed * speedMult;

            if (player1.isBackward) {
                player1.backwardAngle += 0.25;
                if (player1.backwardAngle >= Math.PI * 2) {
                    player1.isBackward = false;
                    player1.backwardAngle = 0;
                    if (player1.hasBall) {
                        player1.hasBall = false;
                        ball.isMoving = true;
                        ball.vx = 15; ball.vy = -1.5;
                        playSound('kick');
                    }
                }
            }

            if (player1.hasBall && !player1.isBackward) {
                ball.x = player1.x + 35;
                ball.y = player1.y + 25;

                if (keys['Space']) {
                    player1.hasBall = false;
                    ball.isMoving = true;
                    ball.vx = 13;
                    ball.vy = (Math.random() - 0.5) * 3;
                    playSound('kick');
                }
            }

            if (!isMultiplayer) {
                player2.y += player2.speed * player2.dir * speedMult;
                if (player2.y <= goalRight.y || player2.y + player2.height >= goalRight.y + goalRight.height) {
                    player2.dir *= -1;
                }
            }

            if (ball.isMoving) {
                ball.x += ball.vx * speedMult;
                ball.y += ball.vy * speedMult;

                if (ball.x + ball.radius >= player2.x && ball.x - ball.radius <= player2.x + player2.width &&
                    ball.y >= player2.y && ball.y <= player2.y + player2.height) {
                    playSound('kick');
                    resetPositions();
                } else if (ball.x + ball.radius >= goalRight.x && ball.y >= goalRight.y && ball.y <= goal.y + goal.height) {
                    triggerGoal(1);
                } else if (ball.x > canvas.width || ball.y < 0 || ball.y > canvas.height) {
                    resetPositions();
                }
            }

            if (isMultiplayer && conn && conn.open) {
                conn.send({
                    type: 'state',
                    p1: { x: player1.x, y: player1.y },
                    p2: { x: player2.x, y: player2.y },
                    ball: { x: ball.x, y: ball.y }
                });
            }
        }

        function triggerGoal(scorer) {
            if (isSlowMotion) return;
            isSlowMotion = true;
            playSound('goal');

            flashEl.style.opacity = '0.8';
            setTimeout(() => flashEl.style.opacity = '0', 150);

            if (scorer === 1) p1Score++; else p2Score++;
            document.getElementById('p1-score').innerText = p1Score;
            document.getElementById('p2-score').innerText = p2Score;

            if (p1Score >= WINNING_SCORE || p2Score >= WINNING_SCORE) {
                setTimeout(() => {
                    isGameOver = true;
                    document.getElementById('match-status').innerText = "🏆 نهاية الماتش - فوز أسطوري!";
                    playSound('whistle');
                    initFanInvasion();
                }, 800);
            } else {
                setTimeout(() => resetPositions(), 1200);
            }
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            ctx.strokeStyle = "rgba(255, 255, 255, 0.4)";
            ctx.lineWidth = 3;
            ctx.strokeRect(10, 10, 780, 400);
            ctx.beginPath();
            ctx.moveTo(400, 10); ctx.lineTo(400, 410);
            ctx.arc(400, 210, 60, 0, Math.PI * 2);
            ctx.stroke();

            ctx.fillStyle = "rgba(255, 255, 255, 0.8)";
            ctx.fillRect(goalRight.x, goalRight.y, goalRight.width, goalRight.height);

            if (player1.isBackward) {
                ctx.save();
                ctx.shadowBlur = 30;
                ctx.shadowColor = "#00d2ff";
                ctx.strokeStyle = "#00d2ff";
                ctx.lineWidth = 4;
                ctx.beginPath();
                ctx.arc(player1.x + 20, player1.y + 25, 40, 0, Math.PI * 2);
                ctx.stroke();
                ctx.restore();
            }

            ctx.save();
            ctx.translate(player1.x + 19, player1.y + 25);
            if (player1.isBackward) ctx.rotate(player1.backwardAngle);

            if (selectedHero === 'vip' && vipImage.complete) {
                ctx.beginPath(); ctx.arc(0, -10, 16, 0, Math.PI * 2); ctx.clip();
                ctx.drawImage(vipImage, -16, -26, 32, 32);
            } else {
                ctx.fillStyle = '#facc15'; ctx.fillRect(-15, -15, 30, 35);
                ctx.fillStyle = '#ffdbac'; ctx.beginPath(); ctx.arc(0, -20, 11, 0, Math.PI * 2); ctx.fill();
                ctx.fillStyle = '#78350f'; ctx.fillRect(-10, -30, 20, 8);
            }
            ctx.restore();

            ctx.fillStyle = '#00d2ff';
            ctx.font = 'bold 11px Cairo';
            ctx.textAlign = 'center';
            ctx.fillText(player1.name, player1.x + 19, player1.y - 10);

            ctx.fillStyle = '#ef4444';
            ctx.fillRect(player2.x, player2.y, player2.width, player2.height);
            ctx.fillStyle = '#ffffff';
            ctx.fillText(player2.name, player2.x + 15, player2.y - 10);

            ctx.fillStyle = '#ffffff';
            ctx.beginPath();
            ctx.arc(ball.x, ball.y, ball.radius, 0, Math.PI * 2);
            ctx.fill();
            ctx.strokeStyle = '#000000'; ctx.lineWidth = 1; ctx.stroke();

            if (isFanInvasion) {
                fansList.forEach(fan => {
                    ctx.fillStyle = fan.shirtColor;
                    if (fan.arrived) {
                        ctx.shadowBlur = 10; ctx.shadowColor = "#facc15";
                        ctx.fillRect(fan.x - 4, fan.y - 2, 8, 4);
                    } else {
                        ctx.beginPath(); ctx.arc(fan.x, fan.y, 4, 0, Math.PI * 2); ctx.fill();
                    }
                });

                ctx.shadowBlur = 0;
                ctx.fillStyle = "rgba(0, 210, 255, 0.9)";
                ctx.font = "bold 24px Cairo";
                ctx.textAlign = "center";
                ctx.fillText("🎉 احتفال نهاية الماتش: عمر جمال 👑", 400, 50);
            }
        }

        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        gameLoop();
    </script>
</body>
</html>
