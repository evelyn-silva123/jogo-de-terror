
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LABIRINTO DA MEMÓRIA - Jogo de Terror</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Courier New', monospace;
            image-rendering: pixelated;
        }
        
        body {
            background: #000;
            color: #8a0303;
            overflow: hidden;
            height: 100vh;
            cursor: default;
        }
        
        #game-container {
            position: relative;
            width: 800px;
            height: 600px;
            margin: 20px auto;
            border: 3px solid #8a0303;
            background: #111;
            overflow: hidden;
        }
        
        #game-canvas {
            width: 100%;
            height: 100%;
        }
        
        .screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: none;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background: #000;
            z-index: 10;
        }
        
        .active {
            display: flex;
        }
        
        h1 {
            font-size: 3rem;
            margin-bottom: 30px;
            text-shadow: 0 0 10px #ff0000;
            letter-spacing: 2px;
            color: #ff4444;
        }
        
        h2 {
            font-size: 2rem;
            margin-bottom: 20px;
            color: #cc3333;
        }
        
        p {
            font-size: 1.1rem;
            margin-bottom: 25px;
            max-width: 500px;
            line-height: 1.5;
            text-align: center;
            color: #aa3333;
        }
        
        .btn {
            background: #2a0000;
            color: #ff6666;
            border: 2px solid #8a0303;
            padding: 12px 30px;
            font-size: 1.1rem;
            cursor: pointer;
            margin: 10px;
            transition: all 0.3s;
            text-transform: uppercase;
            letter-spacing: 1px;
        }
        
        .btn:hover {
            background: #8a0303;
            color: #000;
            box-shadow: 0 0 15px #ff0000;
        }
        
        .ui-panel {
            position: absolute;
            background: rgba(20, 0, 0, 0.8);
            border: 2px solid #8a0303;
            padding: 10px;
            z-index: 5;
        }
        
        #inventory {
            top: 10px;
            left: 10px;
            min-width: 200px;
        }
        
        #sanity {
            top: 10px;
            right: 10px;
            width: 150px;
        }
        
        .inventory-items {
            display: flex;
            gap: 8px;
            margin-top: 8px;
            flex-wrap: wrap;
        }
        
        .item {
            width: 32px;
            height: 32px;
            background: #1a0000;
            border: 1px solid #8a0303;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 16px;
        }
        
        .sanity-bar {
            height: 20px;
            background: #1a0000;
            border: 1px solid #8a0303;
            margin-top: 5px;
            overflow: hidden;
        }
        
        .sanity-fill {
            height: 100%;
            background: #8a0303;
            width: 100%;
            transition: width 0.5s;
        }
        
        .dialog-box {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            width: 90%;
            background: rgba(0, 0, 0, 0.8);
            border: 2px solid #8a0303;
            padding: 15px;
            color: #fff;
            display: none;
            z-index: 20;
        }
        
        .dialog-text {
            margin-bottom: 10px;
            min-height: 40px;
        }
        
        .dialog-choices {
            display: flex;
            gap: 10px;
            justify-content: center;
        }
        
        .dialog-btn {
            background: #2a0000;
            color: #ff6666;
            border: 1px solid #8a0303;
            padding: 8px 15px;
            cursor: pointer;
        }
        
        .petscop-character {
            image-rendering: pixelated;
            position: absolute;
        }
        
        .flash {
            animation: flash 0.5s;
        }
        
        @keyframes flash {
            0% { background-color: #fff; }
            100% { background-color: transparent; }
        }
        
        .glitch {
            animation: glitch 0.3s infinite;
        }
        
        @keyframes glitch {
            0% { transform: translate(0); }
            20% { transform: translate(-2px, 2px); }
            40% { transform: translate(-2px, -2px); }
            60% { transform: translate(2px, 2px); }
            80% { transform: translate(2px, -2px); }
            100% { transform: translate(0); }
        }
    </style>
</head>
<body>
    <div id="game-container">
        <canvas id="game-canvas" width="800" height="600"></canvas>
        
        <!-- Tela de Início -->
        <div id="screen-start" class="screen active">
            <h1>LABIRINTO DA MEMÓRIA</h1>
            <p>Algo foi esquecido aqui... Algo quer ser lembrado...</p>
            <button class="btn" onclick="showScreen('screen-story')">INICIAR</button>
            <button class="btn" onclick="showScreen('screen-instructions')">INSTRUÇÕES</button>
        </div>
        
        <!-- Tela de História -->
        <div id="screen-story" class="screen">
            <h2>A HISTÓRIA</h2>
            <p>Em 1997, um jogo chamado "Labirinto da Memória" foi abandonado. Dizem que o criador desapareceu, deixando para trás entidades presas entre linhas de código.</p>
            <p>Você acorda como "Care", uma figura sem rosto em um mundo que não deveria existir.</p>
            <button class="btn" onclick="startGame()">ACEITAR O DESTINO</button>
        </div>
        
        <!-- Tela de Instruções -->
        <div id="screen-instructions" class="screen">
            <h2>COMO JOGAR</h2>
            <p>• Use WASD ou SETAS para mover Care</p>
            <p>• ESPAÇO para interagir com objetos</p>
            <p>• E para examinar itens do inventário</p>
            <p>• Sua sanidade diminui com eventos perturbadores</p>
            <p>• Encontre os "Pets" para desvendar os segredos</p>
            <button class="btn" onclick="showScreen('screen-start')">VOLTAR</button>
        </div>
        
        <!-- UI do Jogo -->
        <div id="inventory" class="ui-panel">
            <h3>INVENTÁRIO</h3>
            <div class="inventory-items" id="inventory-items"></div>
        </div>
        
        <div id="sanity" class="ui-panel">
            <h3>SANIDADE</h3>
            <div class="sanity-bar">
                <div class="sanity-fill" id="sanity-fill"></div>
            </div>
        </div>
        
        <!-- Caixa de Diálogo -->
        <div id="dialog-box" class="dialog-box">
            <div class="dialog-text" id="dialog-text"></div>
            <div class="dialog-choices" id="dialog-choices"></div>
        </div>
    </div>

    <script>
        // Configuração do jogo
        const config = {
            playerSpeed: 3,
            tileSize: 32,
            canvas: {
                width: 800,
                height: 600
            }
        };

        // Elementos DOM
        const canvas = document.getElementById('game-canvas');
        const ctx = canvas.getContext('2d');
        const sanityFill = document.getElementById('sanity-fill');
        const inventoryItems = document.getElementById('inventory-items');
        const dialogBox = document.getElementById('dialog-box');
        const dialogText = document.getElementById('dialog-text');
        const dialogChoices = document.getElementById('dialog-choices');

        // Estado do jogo
        let gameState = {
            currentScreen: 'screen-start',
            sanity: 100,
            inventory: [],
            currentRoom: 'room1',
            player: {
                x: 100,
                y: 100,
                width: 24,
                height: 32,
                color: '#ff6666'
            },
            npcs: [],
            objects: [],
            keys: {
                w: false, a: false, s: false, d: false,
                arrowup: false, arrowleft: false, arrowdown: false, arrowright: false
            }
        };

        // Personagens no estilo Petscop
        const characters = {
            care: { name: 'Care', color: '#ff6666', dialog: "Onde estou? Por que não consigo me lembrar..." },
            toneth: { name: 'Toneth', color: '#ffaa44', dialog: "Você não deveria estar aqui. Está quebrando as regras." },
            roneth: { name: 'Roneth', color: '#44aaff', dialog: "Cuidado com os cantos escuros. Eles mudam." },
            wavey: { name: 'Wavey', color: '#44ffaa', dialog: "O criador nos abandonou. Agora estamos presos." },
            shadow: { name: 'A Sombra', color: '#330000', dialog: "VOCÊ NÃO DEVERIA TER VOLTADO..." }
        };

        // Mapas e salas
        const rooms = {
            room1: {
                background: '#1a1a2e',
                objects: [
                    { x: 200, y: 150, width: 40, height: 40, type: 'note', color: '#ffff99', text: "Encontre os outros. Eles sabem o caminho." },
                    { x: 500, y: 300, width: 40, height: 40, type: 'key', color: '#ffcc00' },
                    { x: 600, y: 400, width: 60, height: 60, type: 'door', color: '#8b4513', target: 'room2' }
                ],
                npcs: [
                    { x: 300, y: 200, ...characters.toneth }
                ]
            },
            room2: {
                background: '#16213e',
                objects: [
                    { x: 100, y: 100, width: 40, height: 40, type: 'note', color: '#ffff99', text: "A Sombra odeia a luz. Encontre a lanterna." },
                    { x: 400, y: 500, width: 40, height: 40, type: 'lantern', color: '#ffff66' },
                    { x: 700, y: 300, width: 60, height: 60, type: 'door', color: '#8b4513', target: 'room3' }
                ],
                npcs: [
                    { x: 500, y: 200, ...characters.roneth }
                ]
            },
            room3: {
                background: '#0f3460',
                objects: [
                    { x: 150, y: 450, width: 40, height: 40, type: 'note', color: '#ffff99', text: "Ela está sempre observando. Não faça barulho." },
                    { x: 650, y: 150, width: 60, height: 60, type: 'exit', color: '#00ff00' }
                ],
                npcs: [
                    { x: 400, y: 300, ...characters.wavey },
                    { x: 200, y: 100, ...characters.shadow, visible: false }
                ]
            }
        };

        // Inicialização do jogo
        function init() {
            loadRoom(gameState.currentRoom);
            gameLoop();
            setupEventListeners();
        }

        // Carregar sala
        function loadRoom(roomId) {
            gameState.currentRoom = roomId;
            const room = rooms[roomId];
            gameState.objects = [...room.objects];
            gameState.npcs = [...room.npcs];
            
            // Posicionar o jogador na entrada
            gameState.player.x = 50;
            gameState.player.y = config.canvas.height / 2;
        }

        // Loop principal do jogo
        function gameLoop() {
            update();
            render();
            requestAnimationFrame(gameLoop);
        }

        // Atualizar estado do jogo
        function update() {
            if (gameState.currentScreen !== 'game') return;

            // Movimento do jogador
            movePlayer();
            
            // Verificar colisões e interações
            checkInteractions();
            
            // Comportamento dos NPCs
            updateNPCs();
        }

        // Renderizar o jogo
        function render() {
            const room = rooms[gameState.currentRoom];
            
            // Limpar canvas
            ctx.fillStyle = room.background;
            ctx.fillRect(0, 0, config.canvas.width, config.canvas.height);
            
            // Desenhar objetos
            gameState.objects.forEach(obj => {
                ctx.fillStyle = obj.color;
                ctx.fillRect(obj.x, obj.y, obj.width, obj.height);
                
                // Ícones para tipos de objeto
                if (obj.type === 'note') {
                    ctx.fillStyle = '#000';
                    ctx.font = '20px Arial';
                    ctx.fillText('📄', obj.x + 10, obj.y + 25);
                } else if (obj.type === 'key') {
                    ctx.fillStyle = '#000';
                    ctx.font = '20px Arial';
                    ctx.fillText('🔑', obj.x + 10, obj.y + 25);
                } else if (obj.type === 'door') {
                    ctx.fillStyle = '#000';
                    ctx.font = '20px Arial';
                    ctx.fillText('🚪', obj.x + 15, obj.y + 40);
                }
            });
            
            // Desenhar NPCs
            gameState.npcs.forEach(npc => {
                if (npc.visible === false) return;
                
                ctx.fillStyle = npc.color;
                ctx.fillRect(npc.x, npc.y, 32, 32);
                
                // Olhos estilo Petscop
                ctx.fillStyle = '#000';
                ctx.fillRect(npc.x + 8, npc.y + 8, 4, 4);
                ctx.fillRect(npc.x + 20, npc.y + 8, 4, 4);
            });
            
            // Desenhar jogador (Care)
            ctx.fillStyle = gameState.player.color;
            ctx.fillRect(gameState.player.x, gameState.player.y, gameState.player.width, gameState.player.height);
            
            // Desenhar rosto do Care (estilo Petscop)
            ctx.fillStyle = '#000';
            // Olhos
            ctx.fillRect(gameState.player.x + 6, gameState.player.y + 10, 4, 4);
            ctx.fillRect(gameState.player.x + 14, gameState.player.y + 10, 4, 4);
            // Boca
            ctx.fillRect(gameState.player.x + 8, gameState.player.y + 20, 8, 2);
        }

        // Movimentação do jogador
        function movePlayer() {
            let dx = 0, dy = 0;
            
            if (gameState.keys.w || gameState.keys.arrowup) dy = -config.playerSpeed;
            if (gameState.keys.s || gameState.keys.arrowdown) dy = config.playerSpeed;
            if (gameState.keys.a || gameState.keys.arrowleft) dx = -config.playerSpeed;
            if (gameState.keys.d || gameState.keys.arrowright) dx = config.playerSpeed;
            
            // Nova posição
            const newX = gameState.player.x + dx;
            const newY = gameState.player.y + dy;
            
            // Verificar limites da tela
            if (newX >= 0 && newX <= config.canvas.width - gameState.player.width) {
                gameState.player.x = newX;
            }
            if (newY >= 0 && newY <= config.canvas.height - gameState.player.height) {
                gameState.player.y = newY;
            }
        }

        // Verificar interações
        function checkInteractions() {
            const player = gameState.player;
            
            // Verificar colisão com objetos
            gameState.objects.forEach(obj => {
                if (isColliding(player, obj)) {
                    if (obj.type === 'door' && gameState.inventory.includes('key')) {
                        showDialog("A porta se abre...", [
                            { text: "Entrar", action: () => loadRoom(obj.target) }
                        ]);
                    } else if (obj.type === 'note') {
                        showDialog(obj.text, [
                            { text: "Continuar", action: () => {} }
                        ]);
                    } else if (obj.type === 'key') {
                        addToInventory('key', '🔑');
                        gameState.objects = gameState.objects.filter(o => o !== obj);
                        showDialog("Você encontrou uma chave enferrujada", [
                            { text: "Pegar", action: () => {} }
                        ]);
                    } else if (obj.type === 'lantern') {
                        addToInventory('lantern', '🔦');
                        gameState.objects = gameState.objects.filter(o => o !== obj);
                        showDialog("Uma lanterna! Isso pode ajudar contra a escuridão", [
                            { text: "Pegar", action: () => {} }
                        ]);
                    } else if (obj.type === 'exit' && gameState.inventory.includes('lantern')) {
                        showDialog("Você encontrou a saída! A lanterna afastou as sombras.", [
                            { text: "Escapar", action: () => showScreen('screen-victory') }
                        ]);
                    }
                }
            });
            
            // Verificar colisão com NPCs
            gameState.npcs.forEach(npc => {
                if (npc.visible !== false && isColliding(player, npc)) {
                    showDialog(npc.dialog, [
                        { text: "Continuar", action: () => {} }
                    ]);
                    
                    // Encontro com a Sombra reduz sanidade
                    if (npc.name === 'A Sombra') {
                        loseSanity(30);
                        document.body.classList.add('glitch');
                        setTimeout(() => document.body.classList.remove('glitch'), 1000);
                    }
                }
            });
        }

        // Atualizar NPCs
        function updateNPCs() {
            // A Sombra aparece aleatoriamente quando a sanidade está baixa
            if (gameState.sanity < 50 && gameState.currentRoom === 'room3') {
                const shadow = gameState.npcs.find(npc => npc.name === 'A Sombra');
                if (shadow && Math.random() < 0.02) {
                    shadow.visible = true;
                    shadow.x = Math.random() * (config.canvas.width - 32);
                    shadow.y = Math.random() * (config.canvas.height - 32);
                    
                    setTimeout(() => {
                        shadow.visible = false;
                    }, 3000);
                }
            }
        }

        // Detecção de colisão
        function isColliding(rect1, rect2) {
            return rect1.x < rect2.x + rect2.width &&
                   rect1.x + rect1.width > rect2.x &&
                   rect1.y < rect2.y + rect2.height &&
                   rect1.y + rect1.height > rect2.y;
        }

        // Mostrar diálogo
        function showDialog(text, choices) {
            dialogText.textContent = text;
            dialogChoices.innerHTML = '';
            
            choices.forEach(choice => {
                const button = document.createElement('button');
                button.className = 'dialog-btn';
                button.textContent = choice.text;
                button.onclick = () => {
                    dialogBox.style.display = 'none';
                    choice.action();
                };
                dialogChoices.appendChild(button);
            });
            
            dialogBox.style.display = 'block';
        }

        // Adicionar item ao inventário
        function addToInventory(item, icon) {
            if (!gameState.inventory.includes(item)) {
                gameState.inventory.push(item);
                
                const itemElement = document.createElement('div');
                itemElement.className = 'item';
                itemElement.textContent = icon;
                itemElement.title = item;
                inventoryItems.appendChild(itemElement);
            }
        }

        // Reduzir sanidade
        function loseSanity(amount) {
            gameState.sanity = Math.max(0, gameState.sanity - amount);
            sanityFill.style.width = `${gameState.sanity}%`;
            
            if (gameState.sanity === 0) {
                showScreen('screen-game-over');
            }
            
            // Efeito visual quando a sanidade está baixa
            if (gameState.sanity < 30) {
                document.body.classList.add('flash');
                setTimeout(() => document.body.classList.remove('flash'), 200);
            }
        }

        // Controles
        function setupEventListeners() {
            document.addEventListener('keydown', (e) => {
                const key = e.key.toLowerCase();
                if (key in gameState.keys) {
                    gameState.keys[key] = true;
                }
                
                // Espaço para interagir
                if (key === ' ' && gameState.currentScreen === 'game') {
                    // Interação já é verificada automaticamente
                }
            });
            
            document.addEventListener('keyup', (e) => {
                const key = e.key.toLowerCase();
                if (key in gameState.keys) {
                    gameState.keys[key] = false;
                }
            });
        }

        // Gerenciamento de telas
        function showScreen(screenId) {
            document.querySelectorAll('.screen').forEach(screen => {
                screen.classList.remove('active');
            });
            document.getElementById(screenId).classList.add('active');
            gameState.currentScreen = screenId === 'game' ? 'game' : 'menu';
        }

        function startGame() {
            gameState.sanity = 100;
            gameState.inventory = [];
            inventoryItems.innerHTML = '';
            sanityFill.style.width = '100%';
            loadRoom('room1');
            showScreen('game');
        }

        // Tela de Game Over (adicionar ao HTML se necessário)
        const gameOverScreen = document.createElement('div');
        gameOverScreen.id = 'screen-game-over';
        gameOverScreen.className = 'screen';
        gameOverScreen.innerHTML = `
            <h2>GAME OVER</h2>
            <p>A escuridão consumiu sua mente. Você se tornou mais uma memória perdida.</p>
            <button class="btn" onclick="startGame()">TENTAR NOVAMENTE</button>
        `;
        document.getElementById('game-container').appendChild(gameOverScreen);

        // Tela de Vitória (adicionar ao HTML se necessário)
        const victoryScreen = document.createElement('div');
        victoryScreen.id = 'screen-victory';
        victoryScreen.className = 'screen';
        victoryScreen.innerHTML = `
            <h2>ESCAPE BEM-SUCEDIDO</h2>
            <p>Você escapou do labirinto, mas as memórias permanecerão...</p>
            <p>Por enquanto.</p>
            <button class="btn" onclick="startGame()">JOGAR NOVAMENTE</button>
        `;
        document.getElementById('-game-container').appendChild(victoryScreen);

        // Iniciar o jogo quando a página carregar
        window.addEventListener('load', init);
    </script>
</body>
</html>
