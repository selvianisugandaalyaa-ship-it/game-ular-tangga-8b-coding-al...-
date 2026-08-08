# game-ular-tangga-8b-coding-al..<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ular Tangga 8b Interaktif</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Canvas Confetti for Victory -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;600;700&family=Outfit:wght@400;600;800&display=swap" rel="stylesheet">

    <style>
        body {
            font-family: 'Outfit', sans-serif;
            background: linear-gradient(135deg, #2e0828 0%, #4c113e 50%, #7c1a5b 100%);
            min-height: 100vh;
            color: #fce7f3;
        }

        .font-heading {
            font-family: 'Fredoka', cursive;
        }

        .board-container {
            position: relative;
            width: 100%;
            max-width: 650px;
            aspect-ratio: 1 / 1;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.6), 0 0 35px rgba(236, 72, 153, 0.4);
            border-radius: 1.5rem;
            overflow: hidden;
            background: #3b0e31;
        }

        .board-grid {
            display: grid;
            grid-template-columns: repeat(10, 1fr);
            grid-template-rows: repeat(10, 1fr);
            width: 100%;
            height: 100%;
        }

        .tile {
            position: relative;
            display: flex;
            align-items: flex-start;
            justify-content: flex-start;
            padding: 4px;
            font-weight: 700;
            font-size: clamp(0.6rem, 1.8vw, 0.95rem);
            user-select: none;
            transition: all 0.2s ease;
        }

        .tile-even {
            background-color: #fdf2f8;
            color: #831843;
        }

        .tile-odd {
            background-color: #fce7f3;
            color: #9d174d;
        }

        .tile-special-even {
            background-color: #fbcfe8;
            color: #701a75;
        }

        .tile-special-odd {
            background-color: #f472b6;
            color: #4c0519;
        }

        .tile-start {
            background-color: #f9a8d4 !important;
            color: #831843 !important;
        }

        .tile-finish {
            background: linear-gradient(135deg, #f472b6 0%, #db2777 100%) !important;
            color: #ffffff !important;
        }

        /* Power-Up Special Tiles */
        .tile-boost {
            background-color: #fbcfe8 !important;
            border: 1.5px solid #f472b6;
        }

        .tile-freeze {
            background-color: #e0f2fe !important;
            color: #0369a1 !important;
        }

        .tile-mystery {
            background-color: #f3e8ff !important;
            color: #6b21a8 !important;
        }

        /* Overlay Canvas / SVG layer for snakes and ladders */
        .overlay-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 10;
        }

        .pawn-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 20;
        }

        /* Dynamic Pawns */
        .pawn {
            position: absolute;
            width: clamp(20px, 4.2vw, 38px);
            height: clamp(20px, 4.2vw, 38px);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: clamp(10px, 2.2vw, 20px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.4), inset 0 2px 4px rgba(255,255,255,0.4);
            transition: all 0.35s cubic-bezier(0.34, 1.56, 0.64, 1);
            transform: translate(-50%, -50%);
            border: 2px solid #ffffff;
        }

        .pawn-pulse {
            animation: pulse-ring 1.5s infinite;
        }

        @keyframes pulse-ring {
            0% { box-shadow: 0 0 0 0 rgba(236, 72, 153, 0.7); }
            70% { box-shadow: 0 0 0 12px rgba(236, 72, 153, 0); }
            100% { box-shadow: 0 0 0 0 rgba(236, 72, 153, 0); }
        }

        /* 3D Dice Simulation CSS */
        .dice-container {
            perspective: 600px;
            width: 70px;
            height: 70px;
        }

        .dice {
            width: 70px;
            height: 70px;
            position: relative;
            transform-style: preserve-3d;
            transition: transform 1s cubic-bezier(0.2, 0.8, 0.3, 1);
        }

        .dice-face {
            position: absolute;
            width: 70px;
            height: 70px;
            background: linear-gradient(145deg, #ffffff, #fce7f3);
            border: 2px solid #fbcfe8;
            border-radius: 12px;
            display: grid;
            grid-template: repeat(3, 1fr) / repeat(3, 1fr);
            padding: 8px;
            box-shadow: inset 0 0 10px rgba(219, 39, 119, 0.15), 0 5px 15px rgba(0,0,0,0.3);
        }

        .dot {
            display: block;
            width: 12px;
            height: 12px;
            background-color: #831843;
            border-radius: 50%;
            margin: auto;
            box-shadow: inset 0 1px 2px rgba(0,0,0,0.4);
        }

        .dot-red {
            background-color: #ec4899;
        }

        /* Dice Face Rotations */
        .face-1 { transform: rotateY(0deg) translateZ(35px); }
        .face-2 { transform: rotateY(90deg) translateZ(35px); }
        .face-3 { transform: rotateY(180deg) translateZ(35px); }
        .face-4 { transform: rotateY(-90deg) translateZ(35px); }
        .face-5 { transform: rotateX(90deg) translateZ(35px); }
        .face-6 { transform: rotateX(-90deg) translateZ(35px); }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(46, 8, 40, 0.6);
            border-radius: 8px;
        }
        ::-webkit-scrollbar-thumb {
            background: #9d174d;
            border-radius: 8px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #be185d;
        }

        /* Glassmorphic UI Panels */
        .glass-panel {
            background: rgba(65, 12, 54, 0.65);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(244, 114, 182, 0.25);
        }

        /* Snake Path Glow */
        .snake-path {
            stroke-dasharray: 8, 4;
            animation: snake-dash 20s linear infinite;
        }

        @keyframes snake-dash {
            to { stroke-dashoffset: -100; }
        }
    </style>
</head>
<body class="flex flex-col min-h-screen justify-between p-2 sm:p-4 md:p-6">

    <!-- Header Navbar -->
    <header class="max-w-7xl mx-auto w-full flex flex-col sm:flex-row items-center justify-between gap-4 mb-4 glass-panel p-4 rounded-2xl">
        <div class="flex items-center gap-3">
            <div class="bg-gradient-to-tr from-pink-500 to-rose-600 p-3 rounded-2xl shadow-lg text-white">
                <i class="fa-solid fa-staff-snake text-2xl"></i>
            </div>
            <div>
                <h1 class="text-2xl sm:text-3xl font-heading font-bold text-transparent bg-clip-text bg-gradient-to-r from-pink-200 via-pink-400 to-rose-400">
                    Ular Tangga 8b
                </h1>
                <div class="flex items-center gap-2 mt-0.5">
                    <p class="text-xs text-pink-200/80">Game Papan Klasik Seru & Interaktif</p>
                    <span class="text-pink-400/50">•</span>
                    <!-- Lencana Penulis Game -->
                    <button onclick="openAuthorModal()" class="flex items-center gap-1.5 bg-pink-950/60 hover:bg-pink-900/80 border border-pink-700/50 px-2.5 py-0.5 rounded-full text-[11px] font-medium text-pink-200 hover:text-white transition shadow-sm" title="Klik untuk mengubah nama penulis">
                        <i class="fa-solid fa-pen-nib text-pink-400 text-[10px]"></i>
                        <span>Penulis: <strong id="author-display-header" class="text-amber-300 font-bold">Kelas 8B</strong></span>
                    </button>
                </div>
            </div>
        </div>

        <div class="flex items-center gap-2 sm:gap-3 flex-wrap justify-center">
            <button id="btn-sound" onclick="toggleSound()" class="px-3 py-2 rounded-xl bg-pink-950/70 hover:bg-pink-900/80 text-pink-100 border border-pink-800/60 text-sm font-semibold flex items-center gap-2 transition">
                <i id="sound-icon" class="fa-solid fa-volume-high text-pink-400"></i>
                <span id="sound-text" class="hidden sm:inline">Suara: On</span>
            </button>
            <button onclick="openLevelModal()" class="px-3 py-2 rounded-xl bg-pink-950/70 hover:bg-pink-900/80 text-pink-100 border border-pink-800/60 text-sm hover:scale-105 font-bold flex items-center gap-2 transition text-amber-300">
                <i class="fa-solid fa-trophy text-amber-400"></i>
                <span>Level <span id="header-level-badge">1</span>/100</span>
            </button>
            <button onclick="openRulesModal()" class="px-3 py-2 rounded-xl bg-pink-950/70 hover:bg-pink-900/80 text-pink-100 border border-pink-800/60 text-sm font-semibold flex items-center gap-2 transition">
                <i class="fa-solid fa-circle-info text-pink-300"></i>
                <span>Aturan</span>
            </button>
            <button onclick="openSetupModal()" class="px-4 py-2 rounded-xl bg-gradient-to-r from-pink-600 to-rose-600 hover:from-pink-500 hover:to-rose-500 text-white text-sm font-bold shadow-lg shadow-pink-950/50 flex items-center gap-2 transition">
                <i class="fa-solid fa-rotate-right"></i>
                <span>Game Baru</span>
            </button>
        </div>
    </header>

    <!-- Main Content Area -->
    <main class="max-w-7xl mx-auto w-full grid grid-cols-1 lg:grid-cols-12 gap-6 items-start my-auto">
        
        <!-- Left Side: Game Board -->
        <div class="lg:col-span-7 xl:col-span-8 flex flex-col items-center justify-center">
            <div class="board-container" id="board-container">
                <!-- Grid Tiles (100 squares generated via JS) -->
                <div id="board-grid" class="board-grid"></div>

                <!-- SVG Overlay for Snakes and Ladders -->
                <svg id="svg-overlay" class="overlay-layer" viewBox="0 0 1000 1000" preserveAspectRatio="none">
                    <defs>
                        <!-- Ladder Gradient -->
                        <linearGradient id="ladderGrad" x1="0%" y1="0%" x2="100%" y2="100%">
                            <stop offset="0%" stop-color="#f472b6" />
                            <stop offset="100%" stop-color="#db2777" />
                        </linearGradient>
                        <!-- Snake Gradient -->
                        <linearGradient id="snakeGrad" x1="0%" y1="0%" x2="100%" y2="100%">
                            <stop offset="0%" stop-color="#fb7185" />
                            <stop offset="100%" stop-color="#9f1239" />
                        </linearGradient>
                    </defs>
                    <g id="ladders-group"></g>
                    <g id="snakes-group"></g>
                </svg>

                <!-- Pawn Overlay Layer -->
                <div id="pawn-layer" class="pawn-layer"></div>
            </div>
        </div>

        <!-- Right Side: Dashboard & Turn Controls -->
        <div class="lg:col-span-5 xl:col-span-4 flex flex-col gap-4">
            
            <!-- Turn & Roll Card -->
            <div class="glass-panel p-5 rounded-2xl flex flex-col items-center gap-4 text-center relative overflow-hidden">
                <div id="turn-banner" class="w-full py-2 px-4 rounded-xl font-heading text-lg font-bold flex items-center justify-center gap-2 bg-pink-950/80 text-white border border-pink-800/50">
                    <span id="current-player-avatar" class="text-2xl">🦁</span>
                    <span id="current-player-name">Giliran Pemain 1</span>
                </div>

                <!-- 3D Dice Display -->
                <div class="dice-container my-2 flex justify-center items-center">
                    <div id="dice" class="dice">
                        <!-- Face 1 -->
                        <div class="dice-face face-1">
                            <div></div><div></div><div></div>
                            <div></div><div class="dot dot-red"></div><div></div>
                            <div></div><div></div><div></div>
                        </div>
                        <!-- Face 2 -->
                        <div class="dice-face face-2">
                            <div class="dot"></div><div></div><div></div>
                            <div></div><div></div><div></div>
                            <div></div><div></div><div class="dot"></div>
                        </div>
                        <!-- Face 3 -->
                        <div class="dice-face face-3">
                            <div class="dot"></div><div></div><div></div>
                            <div></div><div class="dot"></div><div></div>
                            <div></div><div></div><div class="dot"></div>
                        </div>
                        <!-- Face 4 -->
                        <div class="dice-face face-4">
                            <div class="dot"></div><div></div><div class="dot"></div>
                            <div></div><div></div><div></div>
                            <div class="dot"></div><div></div><div class="dot"></div>
                        </div>
                        <!-- Face 5 -->
                        <div class="dice-face face-5">
                            <div class="dot"></div><div></div><div class="dot"></div>
                            <div></div><div class="dot"></div><div></div>
                            <div class="dot"></div><div></div><div class="dot"></div>
                        </div>
                        <!-- Face 6 -->
                        <div class="dice-face face-6">
                            <div class="dot"></div><div></div><div class="dot"></div>
                            <div class="dot"></div><div></div><div class="dot"></div>
                            <div class="dot"></div><div></div><div class="dot"></div>
                        </div>
                    </div>
                </div>

                <!-- Roll Button -->
                <button id="btn-roll" onclick="handleRollDice()" class="w-full py-4 rounded-xl bg-gradient-to-r from-pink-500 via-rose-500 to-fuchsia-600 hover:from-pink-400 hover:to-fuchsia-500 text-white font-heading font-bold text-xl shadow-lg shadow-pink-950/50 hover:scale-[1.02] active:scale-95 transition flex items-center justify-center gap-3">
                    <i class="fa-solid fa-dice text-2xl animate-bounce"></i>
                    <span>LEMPAR DADU</span>
                </button>

                <p id="dice-result-text" class="text-sm font-semibold text-pink-200/90 h-6">
                    Klik tombol untuk melempar dadu!
                </p>
            </div>

            <!-- Players Status List -->
            <div class="glass-panel p-4 rounded-2xl flex flex-col gap-3">
                <h2 class="text-base font-bold text-pink-200 flex items-center gap-2 border-b border-pink-800/50 pb-2">
                    <i class="fa-solid fa-users text-pink-400"></i>
                    <span>Posisi Pemain</span>
                </h2>
                <div id="players-list" class="flex flex-col gap-2">
                    <!-- Populated via JS -->
                </div>
            </div>

            <!-- Game Activity Log -->
            <div class="glass-panel p-4 rounded-2xl flex flex-col gap-2">
                <h2 class="text-sm font-bold text-pink-200 flex items-center justify-between border-b border-pink-800/50 pb-2">
                    <span class="flex items-center gap-2"><i class="fa-solid fa-list-ul text-pink-400"></i> Catatan Permainan</span>
                    <button onclick="clearLog()" class="text-xs text-pink-300/70 hover:text-pink-100">Bersihkan</button>
                </h2>
                <div id="game-log" class="h-32 overflow-y-auto text-xs flex flex-col gap-1 pr-1 text-pink-100/90 font-mono">
                    <div class="text-pink-300/60 italic">Permainan dimulai. Selamat bermain!</div>
                </div>
            </div>

        </div>
    </main>

    <!-- Footer -->
    <footer class="max-w-7xl mx-auto w-full text-center text-xs text-pink-300/60 py-4 mt-6 flex flex-col items-center gap-1">
        <p>Ular Tangga 8b &copy; 2026 - Dibuat oleh <span id="author-display-footer" class="font-bold text-pink-200">Kelas 8B</span></p>
        <p class="text-[10px] text-pink-400/50">Dibuat dengan HTML5, Tailwind CSS & JavaScript</p>
    </footer>

    <!-- Modal Setup Permainan -->
    <div id="setup-modal" class="fixed inset-0 bg-black/80 backdrop-blur-md z-50 flex items-center justify-center p-4">
        <div class="glass-panel bg-pink-950/90 border-pink-700/60 max-w-md w-full rounded-2xl p-6 shadow-2xl flex flex-col gap-5 max-h-[90vh] overflow-y-auto">
            <div class="text-center">
                <div class="inline-flex p-3 rounded-2xl bg-pink-500/20 text-pink-300 text-3xl mb-2">
                    <i class="fa-solid fa-gamepad"></i>
                </div>
                <h2 class="text-2xl font-heading font-bold text-white">Pengaturan Game Baru</h2>
                <p class="text-xs text-pink-200/70">Pilih mode dan jumlah pemain untuk memulai</p>
            </div>

            <!-- Input Nama Penulis/Pembuat Game -->
            <div class="flex flex-col gap-1.5 bg-pink-900/30 p-3 rounded-xl border border-pink-800/50">
                <label for="input-author-setup" class="text-xs font-bold text-pink-200 uppercase tracking-wider flex items-center justify-between">
                    <span><i class="fa-solid fa-user-pen text-pink-400 mr-1"></i> Nama Penulis / Pembuat</span>
                </label>
                <input type="text" id="input-author-setup" placeholder="Masukkan nama Anda / Kelas..." class="w-full bg-pink-950/80 border border-pink-700/60 rounded-lg px-3 py-1.5 text-xs text-white focus:outline-none focus:border-pink-400">
            </div>

            <!-- Mode Selection -->
            <div class="flex flex-col gap-2">
                <label class="text-xs font-bold text-pink-200 uppercase tracking-wider">Mode Permainan</label>
                <div class="grid grid-cols-3 gap-2">
                    <button type="button" id="mode-campaign" onclick="setGameMode('campaign')" class="py-2.5 px-2 rounded-xl border-2 border-pink-500 bg-pink-500/20 text-pink-200 font-bold text-xs flex flex-col items-center gap-1 transition">
                        <i class="fa-solid fa-trophy text-amber-400 text-base"></i>
                        <span>Level 1-100</span>
                    </button>
                    <button type="button" id="mode-vs-ai" onclick="setGameMode('vs-ai')" class="py-2.5 px-2 rounded-xl border-2 border-pink-900/60 bg-pink-950/60 text-pink-300/70 font-bold text-xs flex flex-col items-center gap-1 transition">
                        <i class="fa-solid fa-robot text-base"></i>
                        <span>VS AI</span>
                    </button>
                    <button type="button" id="mode-multiplayer" onclick="setGameMode('multiplayer')" class="py-2.5 px-2 rounded-xl border-2 border-pink-900/60 bg-pink-950/60 text-pink-300/70 font-bold text-xs flex flex-col items-center gap-1 transition">
                        <i class="fa-solid fa-user-group text-base"></i>
                        <span>Multiplayer</span>
                    </button>
                </div>
            </div>

            <!-- VS AI Options Container -->
            <div id="vs-ai-options-container" class="flex flex-col gap-3 bg-pink-900/30 p-3 rounded-xl border border-pink-800/50 hidden">
                <div class="flex justify-between items-center">
                    <label class="text-xs font-bold text-pink-200 uppercase tracking-wider flex items-center gap-1">
                        <i class="fa-solid fa-robot text-pink-400"></i>
                        <span>Jumlah Musuh AI</span>
                    </label>
                    <span id="ai-count-display" class="text-xs font-bold text-pink-300">1 Bot AI</span>
                </div>
                <div class="grid grid-cols-3 gap-2">
                    <button type="button" id="ai-count-1" onclick="setAiCount(1)" class="py-1.5 rounded-lg border border-pink-500 bg-pink-500/30 text-white font-bold text-xs">1 AI</button>
                    <button type="button" id="ai-count-2" onclick="setAiCount(2)" class="py-1.5 rounded-lg border border-pink-800 bg-pink-950/60 text-pink-300 font-bold text-xs">2 AI</button>
                    <button type="button" id="ai-count-3" onclick="setAiCount(3)" class="py-1.5 rounded-lg border border-pink-800 bg-pink-950/60 text-pink-300 font-bold text-xs">3 AI</button>
                </div>

                <div class="flex flex-col gap-1.5 mt-1">
                    <label class="text-xs font-bold text-pink-200 uppercase tracking-wider flex items-center gap-1">
                        <i class="fa-solid fa-brain text-pink-400"></i>
                        <span>Tingkat Kesulitan AI</span>
                    </label>
                    <select id="ai-difficulty-select" class="w-full bg-pink-950/80 border border-pink-700/60 rounded-lg px-3 py-1.5 text-xs text-white focus:outline-none focus:border-pink-400 font-semibold">
                        <option value="easy">🟢 Santai (AI Lempar Normal)</option>
                        <option value="smart" selected>🟡 Cerdas (AI Responsif & Cepat)</option>
                        <option value="hard">🔴 Master / Hoki (AI Punya Taktik Menghindar Ular! 🎲)</option>
                    </select>
                </div>
            </div>

            <!-- Level Selector Trigger in Setup -->
            <div id="campaign-level-select" class="flex justify-between items-center bg-pink-900/40 p-3 rounded-xl border border-pink-800/60">
                <div>
                    <span class="text-xs text-pink-300 font-bold block">PILIH LEVEL</span>
                    <span id="selected-level-title" class="text-sm font-bold text-amber-300">Level 1 (Pemula)</span>
                </div>
                <button type="button" onclick="openLevelModal()" class="px-3 py-1.5 rounded-lg bg-pink-800 hover:bg-pink-700 text-white text-xs font-bold transition flex items-center gap-1">
                    <i class="fa-solid fa-list-check"></i>
                    <span>Pilih Level</span>
                </button>
            </div>

            <!-- Player Count Selector for Multiplayer -->
            <div id="player-count-container" class="flex flex-col gap-2 hidden">
                <div class="flex justify-between items-center">
                    <label class="text-xs font-bold text-pink-200 uppercase tracking-wider">Jumlah Pemain</label>
                    <span id="player-count-display" class="text-xs font-bold text-pink-300">2 Pemain</span>
                </div>
                <input type="range" id="input-player-count" min="2" max="4" value="2" oninput="updatePlayerCountDisplay(this.value)" class="w-full accent-pink-500 cursor-pointer">
            </div>

            <!-- Player Names Input Container -->
            <div class="flex flex-col gap-2">
                <label class="text-xs font-bold text-pink-200 uppercase tracking-wider">Nama Pemain</label>
                <div id="player-inputs" class="flex flex-col gap-2"></div>
            </div>

            <!-- Start Game Button -->
            <button type="button" onclick="startGameFromSetup()" class="w-full py-3.5 rounded-xl bg-gradient-to-r from-pink-500 via-rose-500 to-fuchsia-600 hover:from-pink-400 hover:to-fuchsia-500 text-white font-heading font-bold text-base shadow-lg shadow-pink-950/50 hover:scale-[1.02] active:scale-95 transition flex items-center justify-center gap-2 mt-2">
                <i class="fa-solid fa-play"></i>
                <span>Mulai Permainan</span>
            </button>
        </div>
    </div>

    <!-- Modal Ubah Nama Pemain (Quick Player Rename Modal) -->
    <div id="rename-player-modal" class="fixed inset-0 bg-black/80 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden">
        <div class="glass-panel bg-pink-950/90 border-pink-700/60 max-w-sm w-full rounded-2xl p-6 shadow-2xl flex flex-col gap-4">
            <div class="flex justify-between items-center border-b border-pink-800/60 pb-3">
                <h2 class="text-lg font-heading font-bold text-pink-200 flex items-center gap-2">
                    <i class="fa-solid fa-user-pen text-pink-400"></i> Ubah Nama Pemain
                </h2>
                <button onclick="closeRenamePlayerModal()" class="text-pink-300/70 hover:text-white text-base">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>
            
            <input type="hidden" id="edit-player-id">

            <div class="flex items-center gap-3 bg-pink-900/40 p-3 rounded-xl border border-pink-800/50">
                <button type="button" onclick="cycleModalPlayerAvatar()" id="modal-player-avatar-btn" class="text-2xl p-2 bg-pink-950/80 hover:bg-pink-900 rounded-xl transition border border-pink-700/50" title="Klik untuk ganti avatar">
                    🦁
                </button>
                <div class="flex flex-col flex-grow">
                    <label class="text-[10px] font-bold text-pink-300/80 uppercase">Nama Pemain Baru</label>
                    <input type="text" id="input-rename-player" placeholder="Masukkan nama..." class="bg-transparent border-b border-pink-500 py-1 text-sm text-white font-bold focus:outline-none focus:border-pink-300">
                </div>
            </div>
            <p class="text-[11px] text-pink-300/70 italic text-center">Tip: Klik ikon emoji di atas untuk mengganti avatar pemain!</p>

            <div class="flex items-center gap-2 pt-2">
                <button onclick="closeRenamePlayerModal()" class="w-1/2 py-2.5 rounded-xl bg-pink-900/60 hover:bg-pink-800 text-pink-200 text-xs font-semibold transition">
                    Batal
                </button>
                <button onclick="savePlayerRename()" class="w-1/2 py-2.5 rounded-xl bg-gradient-to-r from-pink-500 to-rose-600 hover:from-pink-400 hover:to-rose-500 text-white text-xs font-bold transition">
                    Simpan Nama
                </button>
            </div>
        </div>
    </div>

    <!-- Modal Ubah Penulis (Quick Author Modal) -->
    <div id="author-modal" class="fixed inset-0 bg-black/80 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden">
        <div class="glass-panel bg-pink-950/90 border-pink-700/60 max-w-sm w-full rounded-2xl p-6 shadow-2xl flex flex-col gap-4">
            <div class="flex justify-between items-center border-b border-pink-800/60 pb-3">
                <h2 class="text-lg font-heading font-bold text-pink-200 flex items-center gap-2">
                    <i class="fa-solid fa-pen-nib text-pink-400"></i> Ubah Nama Penulis
                </h2>
                <button onclick="closeAuthorModal()" class="text-pink-300/70 hover:text-white text-base">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>
            
            <div class="flex flex-col gap-2">
                <label class="text-xs font-semibold text-pink-200">Nama Penulis / Kreator Game:</label>
                <input type="text" id="input-author-quick" placeholder="Contoh: Budi (Kelas 8B)" class="w-full bg-pink-900/50 border border-pink-700/70 rounded-xl px-3 py-2 text-sm text-white focus:outline-none focus:border-pink-400">
            </div>

            <div class="flex items-center gap-2 pt-2">
                <button onclick="closeAuthorModal()" class="w-1/2 py-2.5 rounded-xl bg-pink-900/60 hover:bg-pink-800 text-pink-200 text-xs font-semibold transition">
                    Batal
                </button>
                <button onclick="saveAuthorNameFromQuickModal()" class="w-1/2 py-2.5 rounded-xl bg-gradient-to-r from-pink-500 to-rose-600 hover:from-pink-400 hover:to-rose-500 text-white text-xs font-bold transition">
                    Simpan
                </button>
            </div>
        </div>
    </div>

    <!-- Modal Aturan Game -->
    <div id="rules-modal" class="fixed inset-0 bg-black/80 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden">
        <div class="glass-panel bg-pink-950/90 border-pink-700/60 max-w-lg w-full rounded-2xl p-6 shadow-2xl flex flex-col gap-4">
            <div class="flex justify-between items-center border-b border-pink-800/60 pb-3">
                <h2 class="text-xl font-heading font-bold text-pink-300 flex items-center gap-2">
                    <i class="fa-solid fa-book-open"></i> Aturan Permainan
                </h2>
                <button onclick="closeRulesModal()" class="text-pink-300/70 hover:text-white text-lg">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>
            <div class="text-sm text-pink-100/90 flex flex-col gap-3 max-h-80 overflow-y-auto pr-2 leading-relaxed">
                <div class="flex items-start gap-3">
                    <span class="bg-pink-500/30 text-pink-200 px-2 py-1 rounded font-bold text-xs">1</span>
                    <p>Semua pemain memulai dari petak **1** dan berlomba mencapai petak **100**.</p>
                </div>
                <div class="flex items-start gap-3">
                    <span class="bg-pink-500/30 text-pink-200 px-2 py-1 rounded font-bold text-xs">2</span>
                    <p>Masing-masing pemain bergantian melempar dadu (angka 1-6).</p>
                </div>
                <div class="flex items-start gap-3">
                    <span class="bg-pink-500/30 text-pink-200 px-2 py-1 rounded font-bold text-xs">3</span>
                    <p><strong class="text-pink-300">TANGGA:</strong> Jika mendarat di bawah tangga, kamu akan memanjat naik ke ujung atas tangga!</p>
                </div>
                <div class="flex items-start gap-3">
                    <span class="bg-pink-500/30 text-pink-200 px-2 py-1 rounded font-bold text-xs">4</span>
                    <p><strong class="text-rose-400">ULAR:</strong> Jika mendarat di kepala ular, kamu akan merosot turun ke ekor ular!</p>
                </div>
                <div class="flex items-start gap-3">
                    <span class="bg-pink-500/30 text-pink-200 px-2 py-1 rounded font-bold text-xs">5</span>
                    <p><strong class="text-pink-200">BONUS 6:</strong> Melempar angka 6 memberikan giliran lemparan ekstra!</p>
                </div>
                <div class="flex items-start gap-3">
                    <span class="bg-pink-500/30 text-pink-200 px-2 py-1 rounded font-bold text-xs">6</span>
                    <p>Pemain harus mendarat **PAS di petak 100**. Jika dadu melebihi petak 100, bidak akan memantul mundur sisa langkahnya.</p>
                </div>
            </div>
            <button onclick="closeRulesModal()" class="w-full py-3 rounded-xl bg-pink-900/80 hover:bg-pink-800 text-white font-bold transition">
                Mengerti & Tutup
            </button>
        </div>
    </div>

    <!-- Modal Pilih Level (1-100) -->
    <div id="level-modal" class="fixed inset-0 bg-black/85 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden">
        <div class="glass-panel bg-pink-950/95 border-pink-700/60 max-w-2xl w-full rounded-2xl p-6 shadow-2xl flex flex-col gap-4 max-h-[90vh]">
            <div class="flex justify-between items-center border-b border-pink-800/60 pb-3">
                <div>
                    <h2 class="text-xl font-heading font-bold text-pink-200 flex items-center gap-2">
                        <i class="fa-solid fa-map-location-dot text-amber-400"></i> Pilih Level (1 - 100)
                    </h2>
                    <p class="text-xs text-pink-300/70">Kalahkan AI untuk membuka level berikutnya!</p>
                </div>
                <button onclick="closeLevelModal()" class="text-pink-300/70 hover:text-white text-lg">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>

            <!-- Level Category Tabs -->
            <div class="flex items-center gap-2 overflow-x-auto pb-1 text-xs">
                <button onclick="renderLevelGrid(1, 25)" class="px-3 py-1.5 rounded-lg bg-pink-600 text-white font-bold whitespace-nowrap level-tab" id="tab-1-25">Level 1 - 25</button>
                <button onclick="renderLevelGrid(26, 50)" class="px-3 py-1.5 rounded-lg bg-pink-900/60 hover:bg-pink-800 text-pink-200 font-bold whitespace-nowrap level-tab" id="tab-26-50">Level 26 - 50</button>
                <button onclick="renderLevelGrid(51, 75)" class="px-3 py-1.5 rounded-lg bg-pink-900/60 hover:bg-pink-800 text-pink-200 font-bold whitespace-nowrap level-tab" id="tab-51-75">Level 51 - 75</button>
                <button onclick="renderLevelGrid(76, 100)" class="px-3 py-1.5 rounded-lg bg-pink-900/60 hover:bg-pink-800 text-pink-200 font-bold whitespace-nowrap level-tab" id="tab-76-100">Level 76 - 100</button>
            </div>

            <!-- Grid Level 1-100 -->
            <div id="level-grid-container" class="grid grid-cols-5 sm:grid-cols-5 md:grid-cols-5 gap-2.5 overflow-y-auto p-1 max-h-[55vh]">
                <!-- Generated via JS -->
            </div>

            <div class="flex justify-between items-center pt-2 border-t border-pink-800/60 text-xs text-pink-200/80">
                <span>⭐ Total Bintang: <strong id="total-stars-count" class="text-amber-300 font-bold">0</strong> / 300</span>
                <button onclick="closeLevelModal()" class="px-4 py-2 rounded-xl bg-pink-900 hover:bg-pink-800 text-white font-bold transition">
                    Tutup
                </button>
            </div>
        </div>
    </div>

    <!-- Modal Kemenangan (Victory) -->
    <div id="victory-modal" class="fixed inset-0 bg-black/85 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden">
        <div class="glass-panel bg-pink-950/95 border-pink-500/60 max-w-md w-full rounded-2xl p-6 shadow-2xl flex flex-col items-center text-center gap-4">
            <div class="text-6xl my-1" id="winner-avatar">🏆</div>
            <h2 class="text-2xl font-heading font-bold text-transparent bg-clip-text bg-gradient-to-r from-pink-200 via-pink-400 to-rose-400" id="winner-title">
                Selamat!
            </h2>
            
            <!-- Stars earned for level campaign -->
            <div id="victory-stars" class="flex items-center justify-center gap-2 text-2xl my-1 hidden">
                <i id="star-1" class="fa-solid fa-star text-amber-400"></i>
                <i id="star-2" class="fa-solid fa-star text-amber-400"></i>
                <i id="star-3" class="fa-solid fa-star text-amber-400"></i>
            </div>

            <p class="text-pink-100 text-sm" id="winner-message">
                Pemain 1 telah mencapai petak 100 dan memenangkan permainan!
            </p>

            <div class="w-full flex flex-col gap-2 mt-2">
                <button id="btn-next-level" onclick="goToNextLevel()" class="w-full py-3.5 rounded-xl bg-gradient-to-r from-pink-500 via-rose-500 to-fuchsia-600 text-white font-heading font-bold text-base hover:scale-105 transition flex items-center justify-center gap-2">
                    <span>Level Selanjutnya</span>
                    <i class="fa-solid fa-arrow-right"></i>
                </button>
                <button onclick="closeVictoryModal(); openSetupModal();" class="w-full py-2.5 rounded-xl bg-pink-900/80 hover:bg-pink-800 text-pink-200 text-xs font-semibold transition">
                    Menu Permainan
                </button>
            </div>
        </div>
    </div>

    <script>
        // Web Audio API Synthesizer (No external sound files required)
        let audioCtx = null;
        let isSoundEnabled = true;

        function initAudio() {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
        }

        function playSound(type) {
            if (!isSoundEnabled) return;
            try {
                initAudio();
                const now = audioCtx.currentTime;

                if (type === 'dice') {
                    for (let i = 0; i < 5; i++) {
                        const osc = audioCtx.createOscillator();
                        const gain = audioCtx.createGain();
                        osc.type = 'sine';
                        osc.frequency.setValueAtTime(150 + Math.random() * 200, now + i * 0.05);
                        gain.gain.setValueAtTime(0.15, now + i * 0.05);
                        gain.gain.exponentialRampToValueAtTime(0.01, now + i * 0.05 + 0.04);
                        osc.connect(gain);
                        gain.connect(audioCtx.destination);
                        osc.start(now + i * 0.05);
                        osc.stop(now + i * 0.05 + 0.04);
                    }
                } else if (type === 'step') {
                    const osc = audioCtx.createOscillator();
                    const gain = audioCtx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(320, now);
                    osc.frequency.exponentialRampToValueAtTime(440, now + 0.08);
                    gain.gain.setValueAtTime(0.2, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.08);
                    osc.connect(gain);
                    gain.connect(audioCtx.destination);
                    osc.start(now);
                    osc.stop(now + 0.08);
                } else if (type === 'ladder') {
                    [300, 400, 500, 650].forEach((freq, idx) => {
                        const osc = audioCtx.createOscillator();
                        const gain = audioCtx.createGain();
                        osc.type = 'sine';
                        osc.frequency.setValueAtTime(freq, now + idx * 0.1);
                        gain.gain.setValueAtTime(0.25, now + idx * 0.1);
                        gain.gain.exponentialRampToValueAtTime(0.01, now + idx * 0.1 + 0.15);
                        osc.connect(gain);
                        gain.connect(audioCtx.destination);
                        osc.start(now + idx * 0.1);
                        osc.stop(now + idx * 0.1 + 0.15);
                    });
                } else if (type === 'snake') {
                    const osc = audioCtx.createOscillator();
                    const gain = audioCtx.createGain();
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(600, now);
                    osc.frequency.exponentialRampToValueAtTime(150, now + 0.4);
                    gain.gain.setValueAtTime(0.2, now);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.4);
                    osc.connect(gain);
                    gain.connect(audioCtx.destination);
                    osc.start(now);
                    osc.stop(now + 0.4);
                } else if (type === 'win') {
                    [523.25, 659.25, 783.99, 1046.50].forEach((freq) => {
                        const osc = audioCtx.createOscillator();
                        const gain = audioCtx.createGain();
                        osc.type = 'triangle';
                        osc.frequency.setValueAtTime(freq, now);
                        gain.gain.setValueAtTime(0.3, now);
                        gain.gain.exponentialRampToValueAtTime(0.001, now + 1.2);
                        osc.connect(gain);
                        gain.connect(audioCtx.destination);
                        osc.start(now);
                        osc.stop(now + 1.2);
                    });
                }
            } catch (e) {
                console.log("Audio not allowed yet:", e);
            }
        }

        function toggleSound() {
            isSoundEnabled = !isSoundEnabled;
            const icon = document.getElementById('sound-icon');
            const text = document.getElementById('sound-text');
            if (icon && text) {
                if (isSoundEnabled) {
                    icon.className = 'fa-solid fa-volume-high text-pink-400';
                    text.innerText = 'Suara: On';
                } else {
                    icon.className = 'fa-solid fa-volume-xmark text-slate-400';
                    text.innerText = 'Suara: Off';
                }
            }
        }

        let LADDERS = { 4: 14, 9: 31, 20: 38, 28: 84, 36: 44, 51: 67, 71: 91, 80: 99 };
        let SNAKES = { 17: 7, 32: 11, 43: 18, 54: 34, 62: 19, 64: 60, 73: 52, 87: 32, 93: 73, 95: 75, 98: 79, 99: 21 };
        let SPECIAL_TILES = {};

        const AVATARS = [
            { icon: '🦁', name: 'Singa', color: '#ec4899' },
            { icon: '🐸', name: 'Katak', color: '#f43f5e' },
            { icon: '🦊', name: 'Rubah', color: '#d946ef' },
            { icon: '🤖', name: 'Robot', color: '#a855f7' },
            { icon: '🦄', name: 'Kuda Uni', color: '#f472b6' },
            { icon: '🚀', name: 'Roket', color: '#e11d48' }
        ];

        let currentLevel = 1;
        let maxUnlockedLevel = parseInt(localStorage.getItem('ut8b_maxLevel') || '1');
        let levelStars = JSON.parse(localStorage.getItem('ut8b_stars') || '{}');
        let authorName = localStorage.getItem('ut8b_author') || 'Kelas 8B';

        let players = [];
        let currentPlayerIndex = 0;
        let isRolling = false;
        let isMoving = false;
        let gameMode = 'campaign';
        let aiCount = 1;
        let aiDifficulty = 'smart';
        let diceRotations = { x: 0, y: 0 };
        let turnCounter = 0;
        let setupPlayerAvatars = [];

        function generateLevelConfig(level) {
            let seed = level * 9973;
            function pseudoRandom() {
                seed = (seed * 9301 + 49297) % 233280;
                return seed / 233280;
            }

            const ladderCount = Math.max(3, 8 - Math.floor(level / 20));
            const snakeCount = Math.min(22, 8 + Math.floor(level / 5));
            
            const ladders = {};
            const snakes = {};
            const specials = {};

            const usedSquares = new Set([1, 100]);

            function getRandomSquare(min, max) {
                let sq;
                let attempts = 0;
                do {
                    sq = Math.floor(min + pseudoRandom() * (max - min + 1));
                    attempts++;
                } while (usedSquares.has(sq) && attempts < 100);
                return sq;
            }

            for (let i = 0; i < ladderCount; i++) {
                const bottom = getRandomSquare(3, 82);
                if (usedSquares.has(bottom)) continue;
                
                const climb = Math.floor(12 + pseudoRandom() * 28);
                const top = Math.min(99, bottom + climb);

                if (!usedSquares.has(top) && top > bottom + 5) {
                    ladders[bottom] = top;
                    usedSquares.add(bottom);
                    usedSquares.add(top);
                }
            }

            for (let i = 0; i < snakeCount; i++) {
                const head = getRandomSquare(18, 98);
                if (usedSquares.has(head)) continue;

                const drop = Math.floor(10 + pseudoRandom() * 35);
                const tail = Math.max(2, head - drop);

                if (!usedSquares.has(tail) && head > tail + 5) {
                    snakes[head] = tail;
                    usedSquares.add(head);
                    usedSquares.add(tail);
                }
            }

            if (level >= 5) {
                const types = ['boost', 'freeze', 'mystery'];
                const specialCount = Math.min(6, Math.floor(level / 10) + 1);
                
                for (let i = 0; i < specialCount; i++) {
                    const sq = getRandomSquare(10, 90);
                    if (!usedSquares.has(sq)) {
                        const type = types[Math.floor(pseudoRandom() * types.length)];
                        specials[sq] = type;
                        usedSquares.add(sq);
                    }
                }
            }

            return { ladders, snakes, specials };
        }

        function loadLevel(level) {
            currentLevel = level;
            const badge = document.getElementById('header-level-badge');
            const title = document.getElementById('selected-level-title');
            if (badge) badge.innerText = level;
            if (title) title.innerText = `Level ${level} ${getLevelDifficultyTitle(level)}`;

            if (gameMode === 'campaign') {
                const config = generateLevelConfig(level);
                LADDERS = config.ladders;
                SNAKES = config.snakes;
                SPECIAL_TILES = config.specials;
            }
        }

        function getLevelDifficultyTitle(lvl) {
            if (lvl <= 15) return '(Pemula)';
            if (lvl <= 40) return '(Menengah)';
            if (lvl <= 70) return '(Tantangan)';
            if (lvl <= 95) return '(Sangat Sulit)';
            return '(Master 👑)';
        }

        function getSquareCoords(square) {
            const index = square - 1;
            const row = Math.floor(index / 10);
            const colRem = index % 10;
            
            let col = (row % 2 === 0) ? colRem : 9 - colRem;
            return { row, col };
        }

        function buildBoard() {
            const gridContainer = document.getElementById('board-grid');
            if (!gridContainer) return;
            gridContainer.innerHTML = '';

            for (let row = 9; row >= 0; row--) {
                for (let col = 0; col < 10; col++) {
                    let squareNum = (row % 2 === 0) ? row * 10 + col + 1 : row * 10 + (9 - col) + 1;

                    const tile = document.createElement('div');
                    
                    let bgClass = (squareNum % 2 === 0) ? 'tile-even' : 'tile-odd';
                    if (LADDERS[squareNum]) bgClass = 'tile-special-even';
                    if (SNAKES[squareNum]) bgClass = 'tile-special-odd';
                    if (SPECIAL_TILES[squareNum] === 'boost') bgClass = 'tile-boost';
                    if (SPECIAL_TILES[squareNum] === 'freeze') bgClass = 'tile-freeze';
                    if (SPECIAL_TILES[squareNum] === 'mystery') bgClass = 'tile-mystery';
                    if (squareNum === 1) bgClass = 'tile-start';
                    if (squareNum === 100) bgClass = 'tile-finish';

                    tile.className = `tile ${bgClass}`;
                    tile.id = `tile-${squareNum}`;

                    let iconHtml = '';
                    if (squareNum === 1) iconHtml = '<span class="text-[9px] font-bold text-pink-700 block">START</span>';
                    else if (squareNum === 100) iconHtml = '<i class="fa-solid fa-trophy text-amber-600 text-xs"></i>';
                    else if (LADDERS[squareNum]) iconHtml = '<i class="fa-solid fa-stairs text-pink-600 text-[10px]"></i>';
                    else if (SNAKES[squareNum]) iconHtml = '<i class="fa-solid fa-worm text-rose-600 text-[10px]"></i>';
                    else if (SPECIAL_TILES[squareNum] === 'boost') iconHtml = '<i class="fa-solid fa-rocket text-pink-600 text-[10px]"></i>';
                    else if (SPECIAL_TILES[squareNum] === 'freeze') iconHtml = '<i class="fa-solid fa-snowflake text-sky-600 text-[10px]"></i>';
                    else if (SPECIAL_TILES[squareNum] === 'mystery') iconHtml = '<i class="fa-solid fa-circle-question text-purple-600 text-[10px]"></i>';

                    tile.innerHTML = `
                        <div class="flex flex-col justify-between h-full w-full">
                            <span class="leading-none opacity-80">${squareNum}</span>
                            <div class="self-end">${iconHtml}</div>
                        </div>
                    `;

                    gridContainer.appendChild(tile);
                }
            }
        }

        function getCenterOfSquare(square) {
            const { row, col } = getSquareCoords(square);
            const x = 50 + col * 100;
            const y = 950 - row * 100;
            return { x, y };
        }

        function drawSnakesAndLadders() {
            const laddersGroup = document.getElementById('ladders-group');
            const snakesGroup = document.getElementById('snakes-group');
            if (!laddersGroup || !snakesGroup) return;
            
            laddersGroup.innerHTML = '';
            snakesGroup.innerHTML = '';

            Object.entries(LADDERS).forEach(([startStr, endNum]) => {
                const startNum = parseInt(startStr);
                const p1 = getCenterOfSquare(startNum);
                const p2 = getCenterOfSquare(endNum);

                const dx = p2.x - p1.x;
                const dy = p2.y - p1.y;
                const len = Math.hypot(dx, dy) || 1;
                const nx = (-dy / len) * 12;
                const ny = (dx / len) * 12;

                const r1x1 = p1.x + nx, r1y1 = p1.y + ny;
                const r1x2 = p2.x + nx, r1y2 = p2.y + ny;
                const r2x1 = p1.x - nx, r2y1 = p1.y - ny;
                const r2x2 = p2.x - nx, r2y2 = p2.y - ny;

                let svgContent = `
                    <line x1="${r1x1}" y1="${r1y1}" x2="${r1x2}" y2="${r1y2}" stroke="url(#ladderGrad)" stroke-width="6" stroke-linecap="round" />
                    <line x1="${r2x1}" y1="${r2y1}" x2="${r2x2}" y2="${r2y2}" stroke="url(#ladderGrad)" stroke-width="6" stroke-linecap="round" />
                `;

                const rungsCount = Math.max(3, Math.floor(len / 45));
                for (let i = 1; i < rungsCount; i++) {
                    const t = i / rungsCount;
                    const rx1 = r1x1 + t * (r1x2 - r1x1);
                    const ry1 = r1y1 + t * (r1y2 - r1y1);
                    const rx2 = r2x1 + t * (r2x2 - r2x1);
                    const ry2 = r2y1 + t * (r2y2 - r2y1);
                    svgContent += `<line x1="${rx1}" y1="${ry1}" x2="${rx2}" y2="${ry2}" stroke="#fef08a" stroke-width="4" stroke-linecap="round" />`;
                }

                laddersGroup.innerHTML += svgContent;
            });

            Object.entries(SNAKES).forEach(([headStr, tailNum]) => {
                const headNum = parseInt(headStr);
                const pHead = getCenterOfSquare(headNum);
                const pTail = getCenterOfSquare(tailNum);

                const midX = (pHead.x + pTail.x) / 2;
                const midY = (pHead.y + pTail.y) / 2;
                const ctrlX = midX + (pHead.y - pTail.y) * 0.35;
                const ctrlY = midY + (pTail.x - pHead.x) * 0.35;

                const pathData = `M ${pHead.x} ${pHead.y} Q ${ctrlX} ${ctrlY} ${pTail.x} ${pTail.y}`;

                let svgContent = `
                    <path d="${pathData}" stroke="url(#snakeGrad)" stroke-width="14" fill="none" stroke-linecap="round" class="snake-path" />
                    <path d="${pathData}" stroke="#fca5a5" stroke-width="4" fill="none" stroke-linecap="round" stroke-dasharray="4,8" />
                    <circle cx="${pHead.x}" cy="${pHead.y}" r="12" fill="#dc2626" />
                    <circle cx="${pHead.x - 3}" cy="${pHead.y - 3}" r="3" fill="#ffffff" />
                    <circle cx="${pHead.x + 3}" cy="${pHead.y - 3}" r="3" fill="#ffffff" />
                    <circle cx="${pTail.x}" cy="${pTail.y}" r="5" fill="#991b1b" />
                `;

                snakesGroup.innerHTML += svgContent;
            });
        }

        function createPawnsUI() {
            const pawnLayer = document.getElementById('pawn-layer');
            if (!pawnLayer) return;
            pawnLayer.innerHTML = '';

            players.forEach((player, idx) => {
                const pawn = document.createElement('div');
                pawn.id = `pawn-${player.id}`;
                pawn.className = `pawn ${idx === currentPlayerIndex ? 'pawn-pulse' : ''}`;
                pawn.style.backgroundColor = player.color;
                pawn.innerHTML = player.avatar;
                pawnLayer.appendChild(pawn);
            });

            updatePawnPositions();
        }

        function updatePawnPositions() {
            const squareMap = {};

            players.forEach(p => {
                if (!squareMap[p.position]) squareMap[p.position] = [];
                squareMap[p.position].push(p);
            });

            Object.entries(squareMap).forEach(([squareStr, playerList]) => {
                const square = parseInt(squareStr);
                const { row, col } = getSquareCoords(square);

                const baseX = (col + 0.5) * 10;
                const baseY = (9 - row + 0.5) * 10;

                const total = playerList.length;
                playerList.forEach((player, index) => {
                    const pawn = document.getElementById(`pawn-${player.id}`);
                    if (!pawn) return;

                    let offsetX = 0;
                    let offsetY = 0;
                    if (total > 1) {
                        const angle = (index / total) * Math.PI * 2;
                        offsetX = Math.cos(angle) * 1.8;
                        offsetY = Math.sin(angle) * 1.8;
                    }

                    pawn.style.left = `${baseX + offsetX}%`;
                    pawn.style.top = `${baseY + offsetY}%`;

                    if (players[currentPlayerIndex] && player.id === players[currentPlayerIndex].id) {
                        pawn.classList.add('pawn-pulse');
                    } else {
                        pawn.classList.remove('pawn-pulse');
                    }
                });
            });
        }

        function setGameMode(mode) {
            gameMode = mode;
            const btnCampaign = document.getElementById('mode-campaign');
            const btnAi = document.getElementById('mode-vs-ai');
            const btnMulti = document.getElementById('mode-multiplayer');
            const countContainer = document.getElementById('player-count-container');
            const aiOptionsContainer = document.getElementById('vs-ai-options-container');
            const campaignLevelSelect = document.getElementById('campaign-level-select');

            [btnCampaign, btnAi, btnMulti].forEach(btn => {
                if (btn) btn.className = 'py-2.5 px-2 rounded-xl border-2 border-pink-900/60 bg-pink-950/60 text-pink-300/70 font-bold text-xs flex flex-col items-center gap-1 transition';
            });

            if (mode === 'campaign') {
                if (btnCampaign) btnCampaign.className = 'py-2.5 px-2 rounded-xl border-2 border-pink-500 bg-pink-500/20 text-pink-200 font-bold text-xs flex flex-col items-center gap-1 transition';
                if (countContainer) countContainer.classList.add('hidden');
                if (aiOptionsContainer) aiOptionsContainer.classList.add('hidden');
                if (campaignLevelSelect) campaignLevelSelect.classList.remove('hidden');
                renderPlayerInputs(2);
            } else if (mode === 'vs-ai') {
                if (btnAi) btnAi.className = 'py-2.5 px-2 rounded-xl border-2 border-pink-500 bg-pink-500/20 text-pink-200 font-bold text-xs flex flex-col items-center gap-1 transition';
                if (countContainer) countContainer.classList.add('hidden');
                if (aiOptionsContainer) aiOptionsContainer.classList.remove('hidden');
                if (campaignLevelSelect) campaignLevelSelect.classList.add('hidden');
                renderPlayerInputs(1 + aiCount);
            } else {
                if (btnMulti) btnMulti.className = 'py-2.5 px-2 rounded-xl border-2 border-pink-500 bg-pink-500/20 text-pink-200 font-bold text-xs flex flex-col items-center gap-1 transition';
                if (countContainer) countContainer.classList.remove('hidden');
                if (aiOptionsContainer) aiOptionsContainer.classList.add('hidden');
                if (campaignLevelSelect) campaignLevelSelect.classList.add('hidden');
                const countInput = document.getElementById('input-player-count');
                const count = countInput ? parseInt(countInput.value) : 2;
                renderPlayerInputs(count);
            }
        }

        function setAiCount(count) {
            aiCount = count;
            const display = document.getElementById('ai-count-display');
            if (display) display.innerText = `${count} Bot AI`;
            
            [1, 2, 3].forEach(n => {
                const btn = document.getElementById(`ai-count-${n}`);
                if (btn) {
                    if (n === count) btn.className = 'py-1.5 rounded-lg border border-pink-500 bg-pink-500/30 text-white font-bold text-xs';
                    else btn.className = 'py-1.5 rounded-lg border border-pink-800 bg-pink-950/60 text-pink-300 font-bold text-xs';
                }
            });

            if (gameMode === 'vs-ai') {
                renderPlayerInputs(1 + aiCount);
            }
        }

        function updatePlayerCountDisplay(val) {
            const display = document.getElementById('player-count-display');
            if (display) display.innerText = `${val} Pemain`;
            renderPlayerInputs(parseInt(val));
        }

        function renderPlayerInputs(count) {
            const container = document.getElementById('player-inputs');
            if (!container) return;
            container.innerHTML = '';
            setupPlayerAvatars = [];

            const aiNames = ['AI Bot Alpha 🤖', 'AI Bot Beta 🤖', 'AI Bot Gamma 🤖'];

            for (let i = 0; i < count; i++) {
                const isAi = (gameMode === 'vs-ai' && i >= 1) || (gameMode === 'campaign' && i === 1);
                const avatarObj = isAi ? AVATARS[(3 + i) % AVATARS.length] : AVATARS[i % AVATARS.length];
                setupPlayerAvatars.push(avatarObj.icon);

                let defaultName = `Pemain ${i + 1}`;
                if (isAi) {
                    defaultName = gameMode === 'campaign' ? 'Komputer (AI)' : aiNames[(i - 1) % 3];
                }

                const div = document.createElement('div');
                div.className = 'flex items-center gap-2 bg-pink-900/30 p-2 rounded-xl border border-pink-800/50';
                div.innerHTML = `
                    <button type="button" onclick="cycleSetupPlayerAvatar(${i})" id="setup-avatar-btn-${i}" class="text-xl p-1 bg-pink-950/60 hover:bg-pink-900 rounded-lg transition" title="Klik untuk ganti avatar">
                        ${setupPlayerAvatars[i]}
                    </button>
                    <div class="flex flex-col w-full">
                        <input type="text" id="player-name-${i}" value="${defaultName}" ${isAi ? 'readonly' : ''} placeholder="Nama Pemain ${i + 1}" class="w-full bg-pink-950/60 border border-pink-700/60 rounded-lg px-3 py-1.5 text-xs text-white focus:outline-none focus:border-pink-400 font-semibold">
                    </div>
                `;
                container.appendChild(div);
            }
        }

        function cycleSetupPlayerAvatar(index) {
            const currentAvatar = setupPlayerAvatars[index];
            const currentIndex = AVATARS.findIndex(a => a.icon === currentAvatar);
            const nextAvatar = AVATARS[(currentIndex + 1) % AVATARS.length];
            setupPlayerAvatars[index] = nextAvatar.icon;
            
            const btn = document.getElementById(`setup-avatar-btn-${index}`);
            if (btn) btn.innerText = nextAvatar.icon;
        }

        function openLevelModal() {
            const modal = document.getElementById('level-modal');
            if (modal) modal.classList.remove('hidden');
            renderLevelGrid(1, 25);
            updateTotalStarsDisplay();
        }

        function closeLevelModal() {
            const modal = document.getElementById('level-modal');
            if (modal) modal.classList.add('hidden');
        }

        function openRulesModal() {
            const modal = document.getElementById('rules-modal');
            if (modal) modal.classList.remove('hidden');
        }

        function closeRulesModal() {
            const modal = document.getElementById('rules-modal');
            if (modal) modal.classList.add('hidden');
        }

        function openSetupModal() {
            const modal = document.getElementById('setup-modal');
            if (modal) modal.classList.remove('hidden');
            const authorInput = document.getElementById('input-author-setup');
            if (authorInput) authorInput.value = authorName;
            setGameMode(gameMode);
        }

        function updateAuthorDisplays() {
            const headerAuthor = document.getElementById('author-display-header');
            const footerAuthor = document.getElementById('author-display-footer');
            if (headerAuthor) headerAuthor.innerText = authorName;
            if (footerAuthor) footerAuthor.innerText = authorName;
            localStorage.setItem('ut8b_author', authorName);
        }

        function openAuthorModal() {
            const input = document.getElementById('input-author-quick');
            if (input) input.value = authorName;
            const modal = document.getElementById('author-modal');
            if (modal) modal.classList.remove('hidden');
        }

        function closeAuthorModal() {
            const modal = document.getElementById('author-modal');
            if (modal) modal.classList.add('hidden');
        }

        function saveAuthorNameFromQuickModal() {
            const input = document.getElementById('input-author-quick');
            if (input && input.value.trim() !== '') {
                authorName = input.value.trim();
                updateAuthorDisplays();
            }
            closeAuthorModal();
        }

        function closeVictoryModal() {
            const modal = document.getElementById('victory-modal');
            if (modal) modal.classList.add('hidden');
        }

        function renderLevelGrid(start, end) {
            const container = document.getElementById('level-grid-container');
            if (!container) return;
            container.innerHTML = '';

            const tabs = ['1-25', '26-50', '51-75', '76-100'];
            tabs.forEach(t => {
                const el = document.getElementById(`tab-${t}`);
                if (el) {
                    if (t === `${start}-${end}`) el.className = 'px-3 py-1.5 rounded-lg bg-pink-600 text-white font-bold whitespace-nowrap level-tab';
                    else el.className = 'px-3 py-1.5 rounded-lg bg-pink-900/60 hover:bg-pink-800 text-pink-200 font-bold whitespace-nowrap level-tab';
                }
            });

            for (let i = start; i <= end; i++) {
                const isUnlocked = i <= maxUnlockedLevel;
                const stars = levelStars[i] || 0;
                const isCurrent = i === currentLevel;

                const card = document.createElement('button');
                card.type = 'button';
                
                let bgStyle = isUnlocked 
                    ? (isCurrent ? 'bg-gradient-to-br from-pink-500 to-rose-600 text-white border-pink-300 ring-2 ring-pink-400' : 'bg-pink-900/60 hover:bg-pink-800 text-pink-100 border-pink-700/60') 
                    : 'bg-pink-950/40 text-pink-400/40 border-pink-950 opacity-60 cursor-not-allowed';

                card.className = `p-2.5 rounded-xl border flex flex-col items-center justify-between gap-1 transition ${bgStyle}`;
                
                let starsHtml = '';
                if (isUnlocked) {
                    starsHtml = '<div class="flex gap-0.5 text-[10px] text-amber-400">';
                    for (let s = 1; s <= 3; s++) {
                        starsHtml += `<i class="${s <= stars ? 'fa-solid' : 'fa-regular'} fa-star"></i>`;
                    }
                    starsHtml += '</div>';
                } else {
                    starsHtml = '<i class="fa-solid fa-lock text-xs text-pink-400/50"></i>';
                }

                card.innerHTML = `
                    <span class="text-xs font-bold">Lvl ${i}</span>
                    ${starsHtml}
                `;

                if (isUnlocked) {
                    card.onclick = () => {
                        loadLevel(i);
                        closeLevelModal();
                        addLog(`Memilih Level ${i}`);
                    };
                }

                container.appendChild(card);
            }
        }

        function updateTotalStarsDisplay() {
            let total = 0;
            Object.values(levelStars).forEach(s => total += s);
            const el = document.getElementById('total-stars-count');
            if (el) el.innerText = total;
        }

        function startGameFromSetup() {
            initAudio();
            
            const setupAuthorInput = document.getElementById('input-author-setup');
            if (setupAuthorInput && setupAuthorInput.value.trim() !== '') {
                authorName = setupAuthorInput.value.trim();
                updateAuthorDisplays();
            }

            players = [];
            turnCounter = 0;

            if (gameMode === 'vs-ai') {
                const diffSelect = document.getElementById('ai-difficulty-select');
                if (diffSelect) aiDifficulty = diffSelect.value;
            }

            let count = 2;
            if (gameMode === 'multiplayer') {
                const countInput = document.getElementById('input-player-count');
                if (countInput) count = parseInt(countInput.value);
            } else if (gameMode === 'vs-ai') {
                count = 1 + aiCount;
            }

            if (gameMode === 'campaign') {
                loadLevel(currentLevel);
            } else {
                LADDERS = { 4: 14, 9: 31, 20: 38, 28: 84, 36: 44, 51: 67, 71: 91, 80: 99 };
                SNAKES = { 17: 7, 32: 11, 43: 18, 54: 34, 62: 19, 64: 60, 73: 52, 87: 32, 93: 73, 95: 75, 98: 79, 99: 21 };
                SPECIAL_TILES = {};
            }

            for (let i = 0; i < count; i++) {
                const isAi = (gameMode === 'vs-ai' && i >= 1) || (gameMode === 'campaign' && i === 1);
                const nameInput = document.getElementById(`player-name-${i}`);
                const aiNames = ['AI Bot Alpha 🤖', 'AI Bot Beta 🤖', 'AI Bot Gamma 🤖'];
                const fallbackName = isAi ? aiNames[(i - 1) % 3] : `Pemain ${i + 1}`;
                const name = nameInput ? nameInput.value.trim() : fallbackName;
                const avatarIcon = setupPlayerAvatars[i] || AVATARS[i % AVATARS.length].icon;
                const avatarObj = AVATARS.find(a => a.icon === avatarIcon) || AVATARS[i % AVATARS.length];

                players.push({
                    id: i + 1,
                    name: name || fallbackName,
                    avatar: avatarIcon,
                    color: avatarObj.color,
                    position: 1,
                    isAi: isAi,
                    frozen: false
                });
            }

            currentPlayerIndex = 0;
            const setupModal = document.getElementById('setup-modal');
            if (setupModal) setupModal.classList.add('hidden');
            
            buildBoard();
            drawSnakesAndLadders();
            createPawnsUI();
            updateTurnDisplay();
            updatePlayersListUI();
            
            addLog(`Permainan dimulai! Mode: ${gameMode === 'campaign' ? `Level ${currentLevel}` : (gameMode === 'vs-ai' ? `Vs ${aiCount} AI (${aiDifficulty.toUpperCase()})` : 'Multiplayer')}`);
            
            // Trigger AI if player 1 is AI (safety check)
            if (players[currentPlayerIndex] && players[currentPlayerIndex].isAi) {
                setTimeout(() => handleRollDice(false), 1000);
            }
        }

        function updateTurnDisplay() {
            if (!players.length) return;
            const player = players[currentPlayerIndex];
            const avatarEl = document.getElementById('current-player-avatar');
            const nameEl = document.getElementById('current-player-name');
            const diceResultText = document.getElementById('dice-result-text');
            const btnRoll = document.getElementById('btn-roll');

            if (avatarEl) avatarEl.innerText = player.avatar;
            if (nameEl) nameEl.innerText = `Giliran ${player.name} ${player.isAi ? '🤖' : ''}`;
            
            if (btnRoll) {
                if (player.isAi) {
                    btnRoll.classList.add('opacity-50', 'cursor-not-allowed');
                    if (diceResultText) diceResultText.innerText = `🤖 ${player.name} sedang berpikir...`;
                } else {
                    btnRoll.classList.remove('opacity-50', 'cursor-not-allowed');
                    if (diceResultText) diceResultText.innerText = `${player.name}, lemparkan dadumu!`;
                }
            }

            updatePawnPositions();
        }

        let editingPlayerAvatarIndex = 0;

        function updatePlayersListUI() {
            const container = document.getElementById('players-list');
            if (!container) return;
            container.innerHTML = '';

            players.forEach((player, idx) => {
                const isCurrent = idx === currentPlayerIndex;
                const card = document.createElement('div');
                card.className = `p-3 rounded-xl flex items-center justify-between transition ${
                    isCurrent 
                        ? 'bg-pink-900/80 border-2 border-pink-400 shadow-md scale-[1.02]' 
                        : 'bg-pink-950/50 border border-pink-800/40 opacity-80'
                }`;

                card.innerHTML = `
                    <div class="flex items-center gap-3">
                        <div class="w-9 h-9 rounded-full flex items-center justify-center text-lg font-bold shadow" style="background-color: ${player.color}">
                            ${player.avatar}
                        </div>
                        <div class="flex flex-col">
                            <span class="text-xs font-bold text-white flex items-center gap-1">
                                ${player.name}
                                ${player.isAi ? '<span class="text-[10px] bg-pink-800 px-1.5 py-0.2 rounded font-mono">AI</span>' : ''}
                                ${player.frozen ? '❄️' : ''}
                            </span>
                            <span class="text-[11px] text-pink-300">Petak ${player.position} / 100</span>
                        </div>
                    </div>
                    <div class="flex items-center gap-2">
                        ${isCurrent ? '<span class="text-[10px] bg-pink-500 text-white font-bold px-2 py-0.5 rounded-full animate-pulse">GILIRAN</span>' : ''}
                        ${!player.isAi ? `<button onclick="openRenamePlayerModal(${player.id})" class="text-pink-300/70 hover:text-white p-1 text-xs" title="Ubah Nama"><i class="fa-solid fa-pen"></i></button>` : ''}
                    </div>
                `;
                container.appendChild(card);
            });
        }

        function openRenamePlayerModal(playerId) {
            const player = players.find(p => p.id === playerId);
            if (!player) return;
            document.getElementById('edit-player-id').value = playerId;
            document.getElementById('input-rename-player').value = player.name;
            
            editingPlayerAvatarIndex = AVATARS.findIndex(a => a.icon === player.avatar);
            if (editingPlayerAvatarIndex === -1) editingPlayerAvatarIndex = 0;
            
            const avatarBtn = document.getElementById('modal-player-avatar-btn');
            if (avatarBtn) avatarBtn.innerText = AVATARS[editingPlayerAvatarIndex].icon;
            
            const modal = document.getElementById('rename-player-modal');
            if (modal) modal.classList.remove('hidden');
        }

        function closeRenamePlayerModal() {
            const modal = document.getElementById('rename-player-modal');
            if (modal) modal.classList.add('hidden');
        }

        function cycleModalPlayerAvatar() {
            editingPlayerAvatarIndex = (editingPlayerAvatarIndex + 1) % AVATARS.length;
            const avatarBtn = document.getElementById('modal-player-avatar-btn');
            if (avatarBtn) avatarBtn.innerText = AVATARS[editingPlayerAvatarIndex].icon;
        }

        async function handleRollDice(isManualClick = true) {
            if (isRolling || isMoving) return;

            const player = players[currentPlayerIndex];
            if (!player) return;

            // Block human manual click during AI turn
            if (isManualClick && player.isAi) {
                addLog(`⏳ Tunggu giliran ${player.name} selesai!`);
                return;
            }

            isRolling = true;
            const btnRoll = document.getElementById('btn-roll');
            if (btnRoll) btnRoll.disabled = true;

            playSound('dice');

            // Determine dice roll value with AI difficulty intelligence
            let diceValue = Math.floor(Math.random() * 6) + 1;
            
            if (player.isAi && aiDifficulty === 'hard') {
                // Master AI logic: Try to avoid snakes if possible
                let targetPos = player.position + diceValue;
                if (SNAKES[targetPos]) {
                    let altValue = Math.floor(Math.random() * 6) + 1;
                    if (!SNAKES[player.position + altValue]) {
                        diceValue = altValue;
                    }
                }
            }

            roll3DDice(diceValue);

            const resultText = document.getElementById('dice-result-text');
            if (resultText) resultText.innerText = `${player.name} sedang melempar...`;

            await new Promise(r => setTimeout(r, 1000));

            isRolling = false;
            if (resultText) resultText.innerText = `${player.name} dapat angka ${diceValue}!`;
            addLog(`${player.name} ${player.isAi ? '🤖' : ''} melempar dadu & dapat angka ${diceValue}.`);

            await movePlayerSteps(player, diceValue);
        }

        async function movePlayerSteps(player, steps) {
            isMoving = true;
            turnCounter++;

            let currentPos = player.position;

            for (let i = 0; i < steps; i++) {
                if (currentPos < 100) {
                    currentPos++;
                } else {
                    currentPos--;
                }
                
                player.position = currentPos;
                updatePawnPositions();
                updatePlayersListUI();
                playSound('step');
                await new Promise(r => setTimeout(r, 260));
            }

            if (LADDERS[currentPos]) {
                const newPos = LADDERS[currentPos];
                addLog(`🪜 Hore! ${player.name} ${player.isAi ? '🤖' : ''} naik TANGGA dari ${currentPos} ke ${newPos}!`);
                playSound('ladder');
                await new Promise(r => setTimeout(r, 380));
                player.position = newPos;
                updatePawnPositions();
                updatePlayersListUI();
            } 
            else if (SNAKES[currentPos]) {
                const newPos = SNAKES[currentPos];
                addLog(`🐍 Waduh! ${player.name} ${player.isAi ? '🤖' : ''} digigit ULAR dari ${currentPos} ke ${newPos}!`);
                playSound('snake');
                await new Promise(r => setTimeout(r, 380));
                player.position = newPos;
                updatePawnPositions();
                updatePlayersListUI();
            }
            else if (SPECIAL_TILES[currentPos]) {
                const type = SPECIAL_TILES[currentPos];
                if (type === 'boost') {
                    addLog(`🚀 ${player.name} dapat BOOST +2 langkah!`);
                    playSound('ladder');
                    await new Promise(r => setTimeout(r, 300));
                    player.position = Math.min(100, player.position + 2);
                    updatePawnPositions();
                } else if (type === 'freeze') {
                    const opponent = players.find(p => p.id !== player.id);
                    if (opponent) {
                        opponent.frozen = true;
                        addLog(`❄️ ${player.name} membekukan ${opponent.name} untuk 1 putaran!`);
                        playSound('snake');
                    }
                } else if (type === 'mystery') {
                    const bonus = Math.random() > 0.5 ? 3 : -2;
                    addLog(`❓ MISTERI! ${player.name} ${bonus > 0 ? 'maju +3' : 'mundur -2'} langkah!`);
                    player.position = Math.max(1, Math.min(100, player.position + bonus));
                    updatePawnPositions();
                }
                updatePlayersListUI();
            }

            if (player.position === 100) {
                triggerVictory(player);
                isMoving = false;
                return;
            }

            let extraTurn = (steps === 6);
            if (extraTurn) {
                addLog(`🎲 ${player.name} dapat giliran ekstra karena dapat angka 6!`);
            } else {
                let nextIdx = (currentPlayerIndex + 1) % players.length;
                if (players[nextIdx].frozen) {
                    addLog(`❄️ ${players[nextIdx].name} terbeku & melewatkan giliran!`);
                    players[nextIdx].frozen = false;
                    nextIdx = (nextIdx + 1) % players.length;
                }
                currentPlayerIndex = nextIdx;
            }

            updateTurnDisplay();
            isMoving = false;
            const btnRoll = document.getElementById('btn-roll');
            if (btnRoll) btnRoll.disabled = false;

            const nextPlayer = players[currentPlayerIndex];
            if (nextPlayer.isAi) {
                const aiDelay = aiDifficulty === 'easy' ? 1200 : (aiDifficulty === 'hard' ? 800 : 1000);
                setTimeout(() => {
                    handleRollDice(false);
                }, aiDelay);
            }
        }

        function savePlayerRename() {
            const hiddenId = document.getElementById('edit-player-id');
            const input = document.getElementById('input-rename-player');
            if (!hiddenId || !input) return;

            const playerId = parseInt(hiddenId.value);
            const newName = input.value.trim();
            const player = players.find(p => p.id === playerId);

            if (player && newName !== '') {
                const oldName = player.name;
                player.name = newName;
                player.avatar = AVATARS[editingPlayerAvatarIndex].icon;
                player.color = AVATARS[editingPlayerAvatarIndex].color;

                const pawn = document.getElementById(`pawn-${player.id}`);
                if (pawn) {
                    pawn.innerHTML = player.avatar;
                    pawn.style.backgroundColor = player.color;
                }

                addLog(`Nama ${oldName} diubah menjadi ${newName}`);
                updatePlayersListUI();
                updateTurnDisplay();
            }

            closeRenamePlayerModal();
        }

        function addLog(message) {
            const logContainer = document.getElementById('game-log');
            if (!logContainer) return;
            const entry = document.createElement('div');
            entry.className = 'py-0.5 border-b border-pink-900/30';
            entry.innerText = `> ${message}`;
            logContainer.appendChild(entry);
            logContainer.scrollTop = logContainer.scrollHeight;
        }

        function clearLog() {
            const logContainer = document.getElementById('game-log');
            if (logContainer) logContainer.innerHTML = '<div class="text-pink-300/60 italic">Catatan dibersihkan.</div>';
        }

        function roll3DDice(value) {
            const dice = document.getElementById('dice');
            if (!dice) return;
            
            const rotations = {
                1: { x: 0, y: 0 },
                2: { x: 0, y: -90 },
                3: { x: 0, y: 180 },
                4: { x: 0, y: 90 },
                5: { x: -90, y: 0 },
                6: { x: 90, y: 0 }
            };

            const target = rotations[value] || { x: 0, y: 0 };
            
            diceRotations.x += 720 + target.x;
            diceRotations.y += 720 + target.y;

            dice.style.transform = `rotateX(${diceRotations.x}deg) rotateY(${diceRotations.y}deg)`;
        }

        function triggerVictory(winner) {
            playSound('win');
            
            if (typeof confetti === 'function') {
                confetti({ particleCount: 120, spread: 80, origin: { y: 0.6 } });
            }

            const avatar = document.getElementById('winner-avatar');
            const title = document.getElementById('winner-title');
            const msg = document.getElementById('winner-message');
            if (avatar) avatar.innerText = winner.avatar;
            if (title) title.innerText = `${winner.name} Menang! 🎉`;
            if (msg) msg.innerText = `${winner.name} telah berhasil mencapai petak 100!`;

            const victoryStars = document.getElementById('victory-stars');
            const btnNextLevel = document.getElementById('btn-next-level');

            if (gameMode === 'campaign' && !winner.isAi) {
                let starsEarned = 1;
                if (turnCounter <= 16) starsEarned = 3;
                else if (turnCounter <= 26) starsEarned = 2;

                levelStars[currentLevel] = Math.max(levelStars[currentLevel] || 0, starsEarned);
                
                if (currentLevel === maxUnlockedLevel && maxUnlockedLevel < 100) {
                    maxUnlockedLevel++;
                }

                localStorage.setItem('ut8b_maxLevel', maxUnlockedLevel);
                localStorage.setItem('ut8b_stars', JSON.stringify(levelStars));

                if (victoryStars) {
                    victoryStars.classList.remove('hidden');
                    for (let s = 1; s <= 3; s++) {
                        const starEl = document.getElementById(`star-${s}`);
                        if (starEl) {
                            if (s <= starsEarned) starEl.className = 'fa-solid fa-star text-amber-400';
                            else starEl.className = 'fa-regular fa-star text-pink-400/40';
                        }
                    }
                }

                if (btnNextLevel) {
                    if (currentLevel < 100) {
                        btnNextLevel.classList.remove('hidden');
                    } else {
                        btnNextLevel.classList.add('hidden');
                    }
                }
            } else {
                if (victoryStars) victoryStars.classList.add('hidden');
                if (btnNextLevel) btnNextLevel.classList.add('hidden');
            }

            const vicModal = document.getElementById('victory-modal');
            if (vicModal) vicModal.classList.remove('hidden');
            addLog(`🏆 ${winner.name} memenangkan permainan!`);
        }

        function goToNextLevel() {
            if (currentLevel < 100) {
                currentLevel++;
                closeVictoryModal();
                startGameFromSetup();
            }
        }

        window.onload = function() {
            updateAuthorDisplays();
            renderPlayerInputs(2);
            startGameFromSetup();
        };

        window.onresize = function() {
            drawSnakesAndLadders();
            updatePawnPositions();
        };
    </script>
</body>
</html>-
