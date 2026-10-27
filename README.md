<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chef Simulator: Restaurant Tycoon</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
            user-select: none;
        }
        .custom-scrollbar::-webkit-scrollbar {
            width: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: rgba(0, 0, 0, 0.05);
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: rgba(0, 0, 0, 0.2);
            border-radius: 9999px;
        }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 min-h-screen flex flex-col justify-between overflow-x-hidden">

    <!-- Header / Navbar Top -->
    <header class="bg-slate-800 border-b border-slate-700 px-4 py-3 sticky top-0 z-30 shadow-md">
        <div class="max-w-7xl mx-auto flex flex-wrap justify-between items-center gap-4">
            <div class="flex items-center space-x-3">
                <div class="bg-amber-500 p-2 rounded-xl text-slate-900 font-extrabold text-xl">
                    <i data-lucide="utensils-crossed" class="w-6 h-6"></i>
                </div>
                <div>
                    <h1 class="font-extrabold text-lg tracking-tight text-white leading-none">Dapur Master Chef</h1>
                    <span id="difficulty-badge" class="text-xs text-amber-400 font-semibold uppercase tracking-wider">Mudah</span>
                </div>
            </div>

            <div class="flex items-center space-x-6 text-sm">
                <div class="flex items-center space-x-2 bg-slate-700/60 px-3 py-1.5 rounded-lg border border-slate-600">
                    <i data-lucide="wallet" class="w-4 h-4 text-emerald-400"></i>
                    <span class="text-slate-400">Kas Hari Ini:</span>
                    <span id="daily-money" class="font-bold text-emerald-400">Rp 0</span>
                </div>
                <div class="flex items-center space-x-2 bg-slate-700/60 px-3 py-1.5 rounded-lg border border-slate-600">
                    <i data-lucide="piggy-bank" class="w-4 h-4 text-amber-400"></i>
                    <span class="text-slate-400">Total Uang:</span>
                    <span id="total-money" class="font-bold text-amber-400">Rp 0</span>
                </div>
                <div class="flex items-center space-x-2 bg-slate-700/60 px-3 py-1.5 rounded-lg border border-slate-600">
                    <i data-lucide="clock" class="w-4 h-4 text-blue-400"></i>
                    <span class="text-slate-400">Jam Operasional:</span>
                    <span id="shift-timer" class="font-bold text-blue-400">03:00</span>
                </div>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="max-w-7xl mx-auto w-full p-4 flex-grow grid grid-cols-1 lg:grid-cols-12 gap-6">

        <!-- Area Pelanggan (Left / Top Panel) -->
        <section class="lg:col-span-5 flex flex-col gap-4">
            <div class="bg-slate-800 border border-slate-700 rounded-2xl p-4 shadow-xl flex-grow flex flex-col">
                <div class="flex justify-between items-center mb-4 pb-2 border-b border-slate-700">
                    <h2 class="font-bold text-lg flex items-center gap-2">
                        <i data-lucide="users" class="w-5 h-5 text-indigo-400"></i>
                        Antrean Pelanggan (<span id="customer-count">0</span>)
                    </h2>
                    <span class="text-xs text-slate-400">Batas Tunggu Sesuai Kesulitan</span>
                </div>

                <!-- Daftar Pelanggan (Scrollable) -->
                <div id="customer-list" class="flex-grow space-y-4 overflow-y-auto max-h-[500px] pr-2 custom-scrollbar">
                    <!-- Dynamic Customers Loaded Here -->
                </div>
            </div>
        </section>

        <!-- Area Dapur & Memasak (Right / Bottom Panel) -->
        <section class="lg:col-span-7 flex flex-col gap-4">
            <div class="bg-slate-800 border border-slate-700 rounded-2xl p-5 shadow-xl">
                <div class="flex justify-between items-center mb-4 pb-2 border-b border-slate-700">
                    <h2 class="font-bold text-lg flex items-center gap-2">
                        <i data-lucide="cooking-pot" class="w-5 h-5 text-amber-400"></i>
                        Meja Memasak & Resep
                    </h2>
                    <button id="clear-assembly" class="text-xs bg-rose-500/20 text-rose-300 hover:bg-rose-500/30 px-3 py-1 rounded-md transition border border-rose-500/30 flex items-center gap-1">
                        <i data-lucide="trash-2" class="w-3.5 h-3.5"></i> Buang Bahan
                    </button>
                </div>

                <!-- Meja Racik / Piring Aktif -->
                <div class="bg-slate-900/80 rounded-xl p-4 border border-slate-700 mb-6">
                    <span class="text-xs text-slate-400 font-semibold uppercase tracking-wider block mb-2">Piring / Wajan Saat Ini:</span>
                    <div id="plate-ingredients" class="flex flex-wrap gap-2 min-h-[48px] items-center bg-slate-950/60 p-3 rounded-lg border border-slate-800">
                        <span class="text-sm text-slate-500 italic">Klik bahan-bahan di bawah untuk mulai meracik...</span>
                    </div>

                    <!-- Tombol Sajikan -->
                    <div class="mt-4 flex justify-end">
                        <button id="serve-btn" class="bg-emerald-600 hover:bg-emerald-500 active:scale-95 text-white font-bold px-6 py-2.5 rounded-xl shadow-lg shadow-emerald-600/30 flex items-center gap-2 transition duration-200">
                            <i data-lucide="send" class="w-5 h-5"></i> Sajikan Pesanan
                        </button>
                    </div>
                </div>

                <!-- Pilihan Bahan Masakan (Grid) -->
                <div>
                    <h3 class="text-sm font-semibold text-slate-300 mb-3 flex items-center gap-2">
                        <i data-lucide="box" class="w-4 h-4 text-amber-400"></i> Rak Bahan Makanan
                    </h3>
                    <div id="ingredient-shelf" class="grid grid-cols-3 sm:grid-cols-4 md:grid-cols-5 gap-2.5">
                        <!-- Dynamic Ingredients Loaded Here -->
                    </div>
                </div>
            </div>

            <!-- Panduan Menu Ringkas -->
            <div class="bg-slate-800/80 border border-slate-700/80 rounded-2xl p-4">
                <details class="group">
                    <summary class="font-semibold text-sm cursor-pointer flex justify-between items-center text-slate-300 group-open:text-amber-400">
                        <span>Buku Resep & Komposisi Menu</span>
                        <i data-lucide="chevron-down" class="w-4 h-4 transition-transform group-open:rotate-180"></i>
                    </summary>
                    <div id="recipe-book" class="mt-3 grid grid-cols-1 sm:grid-cols-2 gap-2 text-xs text-slate-400 pt-2 border-t border-slate-700/50">
                        <!-- Dynamic Recipe Book -->
                    </div>
                </details>
            </div>
        </section>

    </main>

    <!-- Modal Pilihan Level Kesulitan -->
    <div id="modal-difficulty" class="fixed inset-0 bg-slate-950/80 backdrop-blur-md z-50 flex items-center justify-center p-4">
        <div class="bg-slate-800 border border-slate-700 rounded-2xl max-w-xl w-full p-6 shadow-2xl relative">
            <h2 class="text-2xl font-black text-center text-white mb-2 flex items-center justify-center gap-2">
                <i data-lucide="chef-hat" class="w-8 h-8 text-amber-400"></i> Pilih Tingkat Kesulitan
            </h2>
            <p class="text-slate-400 text-sm text-center mb-6">Pilih tantangan memasak sesuai kemampuan memasakmu!</p>

            <div id="difficulty-options" class="grid grid-cols-2 sm:grid-cols-2 gap-3 max-h-[60vh] overflow-y-auto pr-1 custom-scrollbar">
                <!-- Level buttons generated dynamically -->
            </div>
        </div>
    </div>

    <!-- Modal Laporan Gaji (Toko Tutup) -->
    <div id="modal-summary" class="fixed inset-0 bg-slate-950/80 backdrop-blur-md z-50 hidden flex items-center justify-center p-4">
        <div class="bg-slate-800 border border-slate-700 rounded-2xl max-w-md w-full p-6 shadow-2xl text-center">
            <div class="w-16 h-16 bg-amber-500/20 text-amber-400 rounded-full flex items-center justify-center mx-auto mb-4 border border-amber-500/30">
                <i data-lucide="store" class="w-8 h-8"></i>
            </div>
            <h2 class="text-2xl font-black text-white mb-1">Toko Tutup!</h2>
            <p class="text-slate-400 text-xs mb-6">Laporan Pendapatan Hari Ini</p>

            <div class="bg-slate-900/80 rounded-xl p-4 border border-slate-700 text-left space-y-3 mb-6 text-sm">
                <div class="flex justify-between">
                    <span class="text-slate-400">Total Pelanggan Dilayani:</span>
                    <span id="stat-served" class="font-bold text-white">0</span>
                </div>
                <div class="flex justify-between">
                    <span class="text-slate-400">Pelanggan Kabur:</span>
                    <span id="stat-missed" class="font-bold text-rose-400">0</span>
                </div>
                <div class="flex justify-between">
                    <span class="text-slate-400">Pesanan Tepat Waktu (< 2 mnt):</span>
                    <span id="stat-fast" class="font-bold text-emerald-400">0</span>
                </div>
                <div class="flex justify-between">
                    <span class="text-slate-400">Pesanan Lambat (> 2 mnt - Potong 50%):</span>
                    <span id="stat-slow" class="font-bold text-amber-400">0</span>
                </div>
                <hr class="border-slate-700 my-1">
                <div class="flex justify-between text-base font-bold">
                    <span class="text-slate-200">Gaji Bersih Hari Ini:</span>
                    <span id="stat-total-earned" class="text-emerald-400">Rp 0</span>
                </div>
            </div>

            <button id="next-day-btn" class="w-full bg-amber-500 hover:bg-amber-400 text-slate-950 font-extrabold py-3 rounded-xl transition shadow-lg shadow-amber-500/20">
                Lanjut Hari Berikutnya
            </button>
        </div>
    </div>

    <!-- Notification / Toast Container -->
    <div id="toast-container" class="fixed bottom-4 right-4 z-50 flex flex-col gap-2 pointer-events-none"></div>

    <!-- Game Logic -->
    <script>
        // Data Resep & Harga Standard
        const RECIPES = {
            "Burger": { ingredients: ["Roti", "Daging Burger", "Keju", "Selada"], price: 25000, icon: "🍔" },
            "Nasi Goreng": { ingredients: ["Nasi", "Telur", "Bumbu Nasi Goreng", "Kecap"], price: 20000, icon: "🍳" },
            "Seblak": { ingredients: ["Kerupuk", "Cabai", "Bumbu Seblak", "Sosis", "Telur"], price: 18000, icon: "🍲" },
            "Pizza": { ingredients: ["Adonan Pizza", "Saus Tomat", "Keju", "Sosis"], price: 45000, icon: "🍕" },
            "Roti Bakar": { ingredients: ["Roti", "Mentega", "Cokelat"], price: 12000, icon: "🍞" },
            "Mie Kuah": { ingredients: ["Mie", "Air Kuah", "Telur", "Sayuran"], price: 15000, icon: "🍜" },
            "Mie Goreng": { ingredients: ["Mie", "Bumbu Mie", "Kecap", "Telur"], price: 16000, icon: "🍝" },
            "Ayam Goreng": { ingredients: ["Ayam", "Tepung Crisp", "Minyak Goreng"], price: 22000, icon: "🍗" },
            "Ayam Geprek": { ingredients: ["Ayam", "Tepung Crisp", "Cabai", "Bawang"], price: 24000, icon: "🌶️" }
        };

        // Complete list of unique ingredients
        const INGREDIENTS = [
            "Roti", "Daging Burger", "Keju", "Selada",
            "Nasi", "Telur", "Bumbu Nasi Goreng", "Kecap",
            "Kerupuk", "Cabai", "Bumbu Seblak", "Sosis",
            "Adonan Pizza", "Saus Tomat", "Mentega", "Cokelat",
            "Mie", "Air Kuah", "Sayuran", "Bumbu Mie",
            "Ayam", "Tepung Crisp", "Minyak Goreng", "Bawang"
        ];

        // Level Kesulitan Settings
        const DIFFICULTIES = {
            "Mudah": { patience: 90, spawnRate: 7000, maxCustomers: 2, label: "Mudah", color: "bg-emerald-500" },
            "Lumayan": { patience: 75, spawnRate: 6000, maxCustomers: 3, label: "Lumayan", color: "bg-teal-500" },
            "Sulit": { patience: 60, spawnRate: 5000, maxCustomers: 3, label: "Sulit", color: "bg-blue-500" },
            "Humalitied": { patience: 50, spawnRate: 4500, maxCustomers: 4, label: "Humalitied", color: "bg-indigo-500" },
            "Hardscore": { patience: 40, spawnRate: 4000, maxCustomers: 4, label: "Hardscore", color: "bg-purple-500" },
            "Impossible": { patience: 30, spawnRate: 3500, maxCustomers: 4, label: "Impossible", color: "bg-rose-500" },
            "Legendary": { patience: 25, spawnRate: 3000, maxCustomers: 5, label: "Legendary", color: "bg-amber-500" },
            "Epic impossible": { patience: 20, spawnRate: 2500, maxCustomers: 5, label: "Epic Impossible", color: "bg-orange-600" },
            "Master impossible": { patience: 15, spawnRate: 2000, maxCustomers: 5, label: "Master Impossible", color: "bg-red-600" },
            "Gold impossible": { patience: 12, spawnRate: 1500, maxCustomers: 6, label: "Gold Impossible", color: "bg-yellow-400 text-slate-950" }
        };

        // State Game
        let currentDifficulty = "Mudah";
        let totalMoney = 0;
        let dailyMoney = 0;
        let shiftTimeLeft = 180; // 3 menit per shift (detik)
        let shiftInterval = null;
        let customerSpawnInterval = null;
        let activeCustomers = [];
        let currentAssembly = [];
        let customerIdCounter = 1;

        // Statistics Shift
        let statsServed = 0;
        let statsMissed = 0;
        let statsFast = 0;
        let statsSlow = 0;

        // Sound Effects Mocking using Web Audio API
        const playSound = (freq, type = 'sine', duration = 0.1) => {
            try {
                const ctx = new (window.AudioContext || window.webkitAudioContext)();
                const osc = ctx.createOscillator();
                const gain = ctx.createGain();
                osc.type = type;
                osc.frequency.setValueAtTime(freq, ctx.currentTime);
                gain.gain.setValueAtTime(0.1, ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + duration);
                osc.connect(gain);
                gain.connect(ctx.destination);
                osc.start();
                osc.stop(ctx.currentTime + duration);
            } catch(e) {}
        };

        // DOM Elements
        const modalDifficulty = document.getElementById('modal-difficulty');
        const modalSummary = document.getElementById('modal-summary');
        const difficultyOptions = document.getElementById('difficulty-options');
        const difficultyBadge = document.getElementById('difficulty-badge');
        const ingredientShelf = document.getElementById('ingredient-shelf');
        const plateIngredients = document.getElementById('plate-ingredients');
        const customerList = document.getElementById('customer-list');
        const customerCountEl = document.getElementById('customer-count');
        const recipeBook = document.getElementById('recipe-book');
        const dailyMoneyEl = document.getElementById('daily-money');
        const totalMoneyEl = document.getElementById('total-money');
        const shiftTimerEl = document.getElementById('shift-timer');

        // Init Game
        window.onload = () => {
            lucide.createIcons();
            renderDifficultyButtons();
            renderIngredientShelf();
            renderRecipeBook();
            setupEventListeners();
        };

        // Render Tingkat Kesulitan ke Modal
        function renderDifficultyButtons() {
            difficultyOptions.innerHTML = '';
            Object.keys(DIFFICULTIES).forEach(key => {
                const diff = DIFFICULTIES[key];
                const btn = document.createElement('button');
                btn.className = `p-3 rounded-xl border border-slate-700 bg-slate-900/60 hover:bg-slate-700 transition text-left flex flex-col justify-between group`;
                btn.innerHTML = `
                    <div class="flex items-center justify-between w-full mb-1">
                        <span class="font-extrabold text-white group-hover:text-amber-400">${diff.label}</span>
                        <span class="text-[10px] px-2 py-0.5 rounded-full ${diff.color} font-bold text-white">Lvl</span>
                    </div>
                    <span class="text-xs text-slate-400">Patience: ${diff.patience}s | Max: ${diff.maxCustomers} Cust</span>
                `;
                btn.onclick = () => startGame(key);
                difficultyOptions.appendChild(btn);
            });
        }

        // Render Rak Bahan
        function renderIngredientShelf() {
            ingredientShelf.innerHTML = '';
            INGREDIENTS.forEach(ing => {
                const btn = document.createElement('button');
                btn.className = 'bg-slate-700/60 hover:bg-amber-500/20 hover:border-amber-500/50 border border-slate-600 rounded-xl p-2.5 text-center text-xs font-semibold text-slate-200 active:scale-95 transition flex flex-col items-center justify-center gap-1 shadow-sm';
                btn.innerText = ing;
                btn.onclick = () => addIngredientToPlate(ing);
                ingredientShelf.appendChild(btn);
            });
        }

        // Render Buku Resep
        function renderRecipeBook() {
            recipeBook.innerHTML = '';
            Object.keys(RECIPES).forEach(rName => {
                const rec = RECIPES[rName];
                const div = document.createElement('div');
                div.className = 'bg-slate-900/40 p-2 rounded-lg border border-slate-800 flex justify-between items-center';
                div.innerHTML = `
                    <div>
                        <span class="font-bold text-slate-200">${rec.icon} ${rName}</span>
                        <div class="text-[11px] text-slate-400">${rec.ingredients.join(', ')}</div>
                    </div>
                    <span class="text-amber-400 font-bold">Rp ${rec.price.toLocaleString('id-ID')}</span>
                `;
                recipeBook.appendChild(div);
            });
        }

        // Setup Event Listeners
        function setupEventListeners() {
            document.getElementById('clear-assembly').onclick = () => {
                currentAssembly = [];
                updatePlateUI();
                playSound(200, 'sawtooth', 0.1);
            };

            document.getElementById('serve-btn').onclick = serveDish;
            document.getElementById('next-day-btn').onclick = () => {
                modalSummary.classList.add('hidden');
                modalDifficulty.classList.remove('hidden');
            };
        }

        // Mulai Game
        function startGame(difficultyKey) {
            currentDifficulty = difficultyKey;
            difficultyBadge.innerText = DIFFICULTIES[difficultyKey].label;
            modalDifficulty.classList.add('hidden');

            // Reset Shift Variable
            dailyMoney = 0;
            shiftTimeLeft = 180;
            activeCustomers = [];
            currentAssembly = [];
            statsServed = 0;
            statsMissed = 0;
            st
