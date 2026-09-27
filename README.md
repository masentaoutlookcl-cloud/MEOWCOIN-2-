<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>$MEOW Miner - Minería de Criptomonedas</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;600;700&family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">
    
    <!-- PayPal SDK -->
    <script src="https://www.paypal.com/sdk/js?client-id=BAA_ZsUMEthCCVy8ZIMdw2QiCNUcTQH6YZnM_3h59LvGCuV_v-BvrEUCilJEeTRC5QK2IqSpjLB_PdMwZk&currency=USD&components=buttons,hosted-fields"></script>
    <!-- Solana Web3.js (browser bundle) -->
    <script src="https://unpkg.com/@solana/web3.js@1.99.0/lib/index.iife.min.js"></script>

    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0f172a;
            color: #f8fafc;
            overflow-x: hidden;
        }
        .font-fredoka {
            font-family: 'Fredoka', cursive;
        }
        /* Custom Mining Animations */
        @keyframes pickaxeSwing {
            0% { transform: rotate(0deg); }
            50% { transform: rotate(-45deg); }
            100% { transform: rotate(0deg); }
        }
        .animate-pickaxe {
            animation: pickaxeSwing 1.2s infinite ease-in-out;
            transform-origin: bottom right;
        }
        @keyframes floatToken {
            0% { opacity: 1; transform: translateY(0); }
            100% { opacity: 0; transform: translateY(-40px); }
        }
        .animate-float {
            animation: floatToken 2s infinite ease-out;
        }
        @keyframes cloudMove {
            0% { transform: translateX(-100%); }
            100% { transform: translateX(100vw); }
        }
        .animate-cloud {
            animation: cloudMove 25s linear infinite;
        }
        #dashboardSection { padding-bottom: 6.5rem; }
        /* Modal Backdrop */
        .modal-blur {
            backdrop-filter: blur(8px);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between selection:bg-amber-500 selection:text-white">

    <!-- Navigation / Header -->
    <header class="bg-slate-900/80 backdrop-blur-md border-b border-slate-800 sticky top-0 z-40 px-4 py-3">
        <div class="max-w-7xl mx-auto flex justify-between items-center">
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 bg-amber-500 rounded-full flex items-center justify-center text-slate-900 text-2xl font-bold shadow-lg shadow-amber-500/30">
                    🐱
                </div>
                <div>
                    <h1 class="text-xl font-bold font-fredoka text-amber-400 tracking-wide">$MEOW MINER</h1>
                    <p class="text-xs text-slate-400">Total Supply: 1,000,000,000 $MEOW</p>
                </div>
            </div>

            <!-- User Auth Status / Actions -->
            <div id="userHeaderNav" class="hidden flex items-center space-x-4">
                <div class="text-right hidden sm:block">
                    <p id="userDisplayEmail" class="text-xs text-slate-300 font-medium">usuario@email.com</p>
                    <span class="inline-block w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
                    <span class="text-[10px] text-emerald-400 font-semibold uppercase">Minando Activo</span>
                </div>
                <button onclick="logout()" class="text-xs bg-slate-800 hover:bg-slate-700 text-slate-300 px-3 py-2 rounded-xl transition border border-slate-700 flex items-center gap-2">
                    <i class="fa-solid fa-arrow-right-from-bracket"></i> Salir
                </button>
            </div>
        </div>
    </header>

    <!-- AUTHENTICATION SCREEN -->
    <section id="authSection" class="flex-grow flex items-center justify-center p-4">
        <div class="w-full max-w-md bg-slate-900 border border-slate-800 rounded-3xl p-8 shadow-2xl relative overflow-hidden">
            <!-- Decorative Glow -->
            <div class="absolute -top-12 -right-12 w-32 h-32 bg-amber-500/10 rounded-full blur-2xl"></div>
            <div class="absolute -bottom-12 -left-12 w-32 h-32 bg-indigo-500/10 rounded-full blur-2xl"></div>

            <div class="text-center mb-8">
                <div class="inline-block p-4 bg-amber-500/10 rounded-2xl mb-3 text-4xl">🐾</div>
                <h2 id="authTitle" class="text-2xl font-bold text-white font-fredoka">Inicia Sesión para Minar</h2>
                <p id="authSubtitle" class="text-slate-400 text-sm mt-1">Conecta tu cuenta y comienza a acumular $MEOW</p>
            </div>

            <form id="authForm" onsubmit="handleAuthSubmit(event)" class="space-y-5">
                <!-- Email Field -->
                <div>
                    <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 mb-2">Correo Electrónico</label>
                    <div class="relative">
                        <i class="fa-regular fa-envelope absolute left-4 top-1/2 -translate-y-1/2 text-slate-500"></i>
                        <input type="email" id="authEmail" required placeholder="tu@email.com" class="w-full bg-slate-950 border border-slate-800 rounded-2xl py-3.5 pl-11 pr-4 text-slate-200 placeholder-slate-600 focus:outline-none focus:border-amber-500 focus:ring-1 focus:ring-amber-500 transition text-sm">
                    </div>
                </div>

                <!-- Referral Code Field (registration only) -->
                <div id="referralRegisterField" class="hidden">
                    <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 mb-2">Código de Referido <span class="normal-case font-normal text-slate-500">(opcional)</span></label>
                    <div class="relative">
                        <i class="fa-solid fa-gift absolute left-4 top-1/2 -translate-y-1/2 text-slate-500"></i>
                        <input type="text" id="authReferralCode" maxlength="20" placeholder="Ej. MEOW-ABC123" class="w-full bg-slate-950 border border-slate-800 rounded-2xl py-3.5 pl-11 pr-4 text-slate-200 placeholder-slate-600 focus:outline-none focus:border-amber-500 focus:ring-1 focus:ring-amber-500 transition text-sm uppercase">
                    </div>
                    <p id="referralDiscountHint" class="text-[11px] text-emerald-400 mt-2">Si utilizas un código válido, tendrás 10% de descuento en tu primera compra del paquete de $10.</p>
                </div>

                <!-- Password Field -->
                <div>
                    <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 mb-2">Contraseña</label>
                    <div class="relative">
                        <i class="fa-solid fa-lock absolute left-4 top-1/2 -translate-y-1/2 text-slate-500"></i>
                        <input type="password" id="authPassword" required placeholder="••••••••" class="w-full bg-slate-950 border border-slate-800 rounded-2xl py-3.5 pl-11 pr-12 text-slate-200 placeholder-slate-600 focus:outline-none focus:border-amber-500 focus:ring-1 focus:ring-amber-500 transition text-sm">
                        <button type="button" onclick="togglePasswordVisibility()" class="absolute right-4 top-1/2 -translate-y-1/2 text-slate-500 hover:text-slate-300 focus:outline-none">
                            <i id="passwordEyeIcon" class="fa-regular fa-eye"></i>
                        </button>
                    </div>
                </div>

                <!-- Submit Button -->
                <button type="submit" id="authSubmitBtn" class="w-full py-4 bg-gradient-to-r from-amber-500 to-amber-600 hover:from-amber-400 hover:to-amber-500 text-slate-950 font-bold rounded-2xl shadow-lg shadow-amber-500/20 transition duration-200 transform active:scale-95 text-sm uppercase tracking-wider">
                    Iniciar Sesión
                </button>
            </form>

            <div class="mt-6 text-center">
                <button onclick="toggleAuthMode()" id="toggleAuthBtn" class="text-xs text-slate-400 hover:text-amber-400 transition font-medium">
                    ¿No tienes una cuenta? <span class="text-amber-400 underline">Regístrate gratis</span>
                </button>
            </div>
        </div>
    </section>

    <!-- MAIN DASHBOARD SCREEN (MINING INTERFACE) -->
    <main id="dashboardSection" class="hidden flex-grow max-w-7xl w-full mx-auto p-4 sm:p-6 space-y-6">
        
        <!-- Live Mining Stats Bar -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
            <!-- Balance Card -->
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-5 relative overflow-hidden">
                <div class="text-slate-400 text-xs font-semibold uppercase tracking-wider mb-1">Saldo Acumulado</div>
                <div class="flex items-baseline space-x-2">
                    <span id="balanceDisplay" class="text-3xl font-extrabold text-amber-400 font-fredoka">0.0000</span>
                    <span class="text-amber-500 font-bold text-sm">$MEOW</span>
                </div>
                <div class="text-[11px] text-slate-500 mt-2">Suministro Máximo: 1B $MEOW</div>
            </div>

            <!-- Mining Speed Card -->
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-5">
                <div class="text-slate-400 text-xs font-semibold uppercase tracking-wider mb-1">Velocidad Actual</div>
                <div class="flex items-baseline space-x-2">
                    <span id="speedDisplay" class="text-3xl font-extrabold text-emerald-400 font-fredoka">0.00000965</span>
                    <span class="text-emerald-500 font-bold text-sm">$MEOW / seg</span>
                </div>
                <div class="text-[11px] text-slate-500 mt-2" id="speedBoostInfo">25 $MEOW / 30 días</div>
            </div>

            <!-- Extra Cats Miners Card -->
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-5">
                <div class="text-slate-400 text-xs font-semibold uppercase tracking-wider mb-1">Gatitos Mineros Extra</div>
                <div class="flex items-center justify-between">
                    <div class="text-3xl font-extrabold text-indigo-400 font-fredoka">
                        <span id="catMinersCount">0</span> <span class="text-sm font-normal text-slate-400">/ 2 Máx</span>
                    </div>
                    <div class="text-xs bg-indigo-500/10 text-indigo-400 border border-indigo-500/20 px-2.5 py-1 rounded-full font-medium" id="cycleDaysInfo">
                        Ciclo: 30 Días
                    </div>
                </div>
                <div class="text-[11px] text-slate-500 mt-2">25 $MEOW base cada 30 días</div>
            </div>
        </div>

        <!-- SCENIC MINING PANEL -->
        <div class="bg-slate-900 border border-slate-800 rounded-3xl overflow-hidden shadow-2xl relative">
            
            <!-- UPPER PART: Sunny Landscape -->
            <div class="h-36 bg-gradient-to-b from-sky-400 to-sky-200 relative overflow-hidden border-b-4 border-amber-800">
                <!-- Sun -->
                <div class="absolute top-3 right-8 w-16 h-16 bg-amber-300 rounded-full shadow-lg shadow-amber-300/50 animate-pulse"></div>
                <!-- Clouds -->
                <div class="absolute top-4 left-0 text-white/80 text-3xl animate-cloud">☁️</div>
                <div class="absolute top-8 left-1/3 text-white/70 text-2xl animate-cloud" style="animation-delay: 8s;">☁️</div>
                
                <!-- Hills / Grass -->
                <div class="absolute -bottom-6 -left-10 w-64 h-24 bg-emerald-500 rounded-full"></div>
                <div class="absolute -bottom-8 left-1/3 w-80 h-28 bg-emerald-600 rounded-full"></div>
                <div class="absolute -bottom-6 right-0 w-64 h-24 bg-emerald-500 rounded-full"></div>
            </div>

            <!-- LOWER PART: Underground Mine -->
            <div class="bg-gradient-to-b from-amber-950 via-slate-950 to-black p-8 min-h-[320px] relative flex flex-col justify-between">
                
                <!-- Floating Token Effects Container -->
                <div id="floatingTokensContainer" class="absolute inset-0 pointer-events-none overflow-hidden"></div>

                <!-- Mine Wooden Beams Header -->
                <div class="w-full flex justify-between border-t-8 border-amber-900 pt-2 opacity-60">
                    <div class="w-6 h-16 bg-amber-950 border-r-2 border-amber-900"></div>
                    <div class="w-6 h-16 bg-amber-950 border-l-2 border-amber-900"></div>
                </div>

                <!-- Animated Mining Kittens Area -->
                <div class="flex justify-around items-end py-6 z-10" id="minersContainer">
                    
                    <!-- Default Main Miner Kitten -->
                    <div class="flex flex-col items-center group relative">
                        <div class="absolute -top-8 bg-amber-500/20 text-amber-300 text-[10px] px-2 py-0.5 rounded-full border border-amber-500/40">
                            Minero Principal
                        </div>
                        <div class="text-6xl relative mb-2">
                            🐱
                            <span class="absolute -right-3 top-0 text-3xl animate-pickaxe inline-block">⛏️</span>
                        </div>
                        <div class="h-3 w-16 bg-black/40 rounded-full blur-xs"></div>
                    </div>

                    <!-- Dynamic Extra Cats Rendered Here via JS -->
                </div>

                <!-- Mining Start Button -->
                <div class="flex justify-center z-20 mb-4">
                    <button id="startMiningBtn" onclick="toggleMining()" class="px-8 py-3.5 bg-emerald-500 hover:bg-emerald-400 text-slate-950 font-bold rounded-2xl shadow-lg shadow-emerald-500/20 transition flex items-center justify-center gap-2">
                        <i class="fa-solid fa-play"></i> Iniciar Minería
                    </button>
                </div>

                <!-- Status Banner -->
                <div class="bg-black/60 backdrop-blur-md rounded-2xl p-4 border border-slate-800 flex flex-col sm:flex-row items-center justify-between gap-3 text-center sm:text-left z-10">
                    <div class="flex items-center space-x-3">
                        <div class="p-2.5 bg-emerald-500/20 text-emerald-400 rounded-xl">
                            <i class="fa-solid fa-gears animate-spin"></i>
                        </div>
                        <div>
                            <p class="text-xs font-semibold text-slate-200">Minería Automatizada en Progreso</p>
                            <p class="text-[11px] text-slate-400">La producción se calcula por tiempo real mientras la minería está activa.</p>
                        </div>
                    </div>
                    <div class="text-amber-400 font-bold text-xs bg-amber-500/10 px-3 py-1.5 rounded-xl border border-amber-500/20">
                        25 $MEOW base / 30 días
                    </div>
                </div>
            </div>
        </div>

        <!-- BOOST & CAT PURCHASE STORE -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            
            <!-- Upgrade 1: Speed Boost ($10 USD) -->
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 flex flex-col justify-between hover:border-amber-500/40 transition">
                <div>
                    <div class="flex justify-between items-start mb-4">
                        <div>
                            <span class="text-xs bg-amber-500/10 text-amber-400 border border-amber-500/20 px-2.5 py-1 rounded-full font-semibold uppercase">Potencia de Minado</span>
                            <h3 class="text-xl font-bold font-fredoka text-white mt-2">Paquete de Velocidad +1,000 $MEOW</h3>
                        </div>
                        <span class="text-2xl font-bold text-emerald-400 font-fredoka">$10 USD</span>
                    </div>
                    <p class="text-slate-400 text-sm mb-6">
                        Añade 1,000 $MEOW a tu producción. Se distribuyen automáticamente según tu ciclo actual y puedes comprar este paquete de $10 USD las veces que quieras.
                    </p>
                </div>
                <button onclick="openPaymentModal('speed', 10, 'Paquete de Velocidad +1,000 $MEOW / 30 días')" class="w-full py-3.5 bg-amber-500 hover:bg-amber-400 text-slate-950 font-bold rounded-2xl shadow-lg shadow-amber-500/20 transition flex items-center justify-center gap-2">
                    <i class="fa-solid fa-bolt"></i> Comprar por $10 USD
                </button>
            </div>

            <!-- Upgrade 2: Extra Cat Miner ($50 USD) -->
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 flex flex-col justify-between hover:border-indigo-500/40 transition">
                <div>
                    <div class="flex justify-between items-start mb-4">
                        <div>
                            <span class="text-xs bg-indigo-500/10 text-indigo-400 border border-indigo-500/20 px-2.5 py-1 rounded-full font-semibold uppercase">Gatito Extra</span>
                            <h3 class="text-xl font-bold font-fredoka text-white mt-2">Gatito Minero + Acelerador</h3>
                        </div>
                        <span class="text-2xl font-bold text-emerald-400 font-fredoka">$50 USD</span>
                    </div>
                    <p class="text-slate-400 text-sm mb-6">
                        Añade un gatito minero y acelera el ciclo de producción:
                        <br><span class="text-indigo-300 font-medium">• 1 Gatito: Reduce el ciclo a 20 Días.</span>
                        <br><span class="text-indigo-300 font-medium">• 2 Gatitos (Máx): Reduce el ciclo a 10 Días.</span>
                    </p>
                </div>
                <button id="buyCatBtn" onclick="openPaymentModal('cat', 50, 'Gatito Minero Extra ($50 USD)')" class="w-full py-3.5 bg-indigo-600 hover:bg-indigo-500 text-white font-bold rounded-2xl shadow-lg shadow-indigo-600/20 transition flex items-center justify-center gap-2">
                    <i class="fa-solid fa-cat"></i> Comprar Gatito por $50 USD
                </button>
            </div>
        </div>
    </main>


    <!-- BOTTOM APP NAVIGATION -->
    <nav id="bottomNav" class="hidden fixed bottom-0 left-0 right-0 z-40 bg-slate-950/95 backdrop-blur-md border-t border-slate-800">
        <div class="max-w-2xl mx-auto grid grid-cols-5">
            <button onclick="showAppTab('home')" class="app-tab py-3 text-[11px] text-amber-400 flex flex-col items-center gap-1" data-tab="home">
                <i class="fa-solid fa-house text-lg"></i><span>Inicio</span>
            </button>
            <button onclick="showAppTab('referrals')" class="app-tab py-3 text-[11px] text-slate-400 flex flex-col items-center gap-1" data-tab="referrals">
                <i class="fa-solid fa-user-group text-lg"></i><span>Referidos</span>
            </button>
            <button onclick="showAppTab('options')" class="app-tab py-3 text-[11px] text-slate-400 flex flex-col items-center gap-1" data-tab="options">
                <i class="fa-solid fa-sliders text-lg"></i><span>Opciones</span>
            </button>
            <button onclick="showAppTab('profile')" class="app-tab py-3 text-[11px] text-slate-400 flex flex-col items-center gap-1" data-tab="profile">
                <i class="fa-solid fa-user text-lg"></i><span>Perfil</span>
            </button>
            <button onclick="showAppTab('wallet')" class="app-tab py-3 text-[11px] text-slate-400 flex flex-col items-center gap-1" data-tab="wallet">
                <i class="fa-solid fa-wallet text-lg"></i><span>Wallet</span>
            </button>
        </div>
    </nav>

    <!-- APP TAB PANELS -->
    <section id="appExtraPanels" class="hidden fixed inset-0 z-30 bg-slate-950 overflow-y-auto pt-20 pb-24">
        <div class="max-w-2xl mx-auto p-4">
            <div id="referralsPanel" class="app-panel hidden space-y-5">
                <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6">
                    <h2 class="text-2xl font-bold font-fredoka text-amber-400">Referidos</h2>
                    <p class="text-sm text-slate-400 mt-2">Comparte tu enlace para que otras personas entren a la aplicación y se registren.</p>
                    <div class="mt-5">
                        <label class="text-xs text-slate-400 uppercase font-semibold">Tu enlace de referido</label>
                        <div class="flex gap-2 mt-2">
                            <input id="referralLink" readonly class="min-w-0 flex-1 bg-slate-950 border border-slate-800 rounded-xl px-3 py-3 text-xs text-slate-300">
                            <button onclick="copyReferralLink()" class="px-4 rounded-xl bg-amber-500 text-slate-950 font-bold"><i class="fa-solid fa-copy"></i></button>
                        </div>
                    </div>
                    <div class="grid grid-cols-2 gap-3 mt-5">
                        <div class="bg-slate-950 rounded-2xl p-4">
                            <div class="text-xs text-slate-500">Código</div>
                            <div id="referralCodeDisplay" class="text-lg font-bold text-indigo-400 mt-1">---</div>
                        </div>
                        <div class="bg-slate-950 rounded-2xl p-4">
                            <div class="text-xs text-slate-500">Referidos</div>
                            <div id="referralCountDisplay" class="text-lg font-bold text-emerald-400 mt-1">0</div>
                        </div>
                    </div>
                    <p id="referralNotice" class="text-xs text-slate-500 mt-4"></p>
                </div>
            </div>

            <div id="optionsPanel" class="app-panel hidden space-y-5">
                <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6">
                    <h2 class="text-2xl font-bold font-fredoka text-amber-400">Opciones</h2>
                    <div class="mt-5 space-y-3">
                        <button onclick="showNotification('La minería se controla desde Inicio.')" class="w-full text-left bg-slate-950 border border-slate-800 rounded-2xl p-4">
                            <i class="fa-solid fa-pickaxe text-amber-400 mr-2"></i> Configuración de minería
                        </button>
                        <button onclick="toggleMeowMusic()" id="meowMusicBtn" class="w-full text-left bg-slate-950 border border-slate-800 rounded-2xl p-4 text-slate-200">
                            <i class="fa-solid fa-music text-pink-400 mr-2"></i> <span id="meowMusicLabel">Activar música de gatitos</span>
                        </button>
                        <button onclick="logout()" class="w-full text-left bg-slate-950 border border-slate-800 rounded-2xl p-4 text-rose-400">
                            <i class="fa-solid fa-right-from-bracket mr-2"></i> Cerrar sesión
                        </button>
                    </div>
                </div>
            </div>

            <div id="walletPanel" class="app-panel hidden space-y-5">
                <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6">
                    <div class="flex items-start justify-between gap-4">
                        <div>
                            <h2 class="text-2xl font-bold font-fredoka text-amber-400">Wallet Solana</h2>
                            <p class="text-sm text-slate-400 mt-2">Conecta una wallet de Solana para consultar tus activos en la cadena.</p>
                        </div>
                        <div class="px-3 py-1.5 rounded-full text-[10px] font-bold uppercase tracking-wider bg-violet-500/10 text-violet-300 border border-violet-500/20">Solana Mainnet</div>
                    </div>

                    <div class="mt-5 bg-slate-950 rounded-2xl p-4 border border-slate-800">
                        <div class="flex items-center justify-between gap-3">
                            <div class="min-w-0">
                                <div class="text-xs text-slate-500 uppercase font-semibold">Wallet conectada</div>
                                <div id="solanaWalletAddress" class="text-sm text-slate-200 mt-1 break-all">No conectada</div>
                            </div>
                            <button id="connectWalletBtn" onclick="connectSolanaWallet()" class="shrink-0 px-4 py-2.5 rounded-xl bg-violet-600 hover:bg-violet-500 text-white font-bold text-xs">Conectar</button>
                        </div>
                    </div>

                    <div class="grid grid-cols-1 sm:grid-cols-3 gap-3 mt-4">
                        <div class="bg-slate-950 rounded-2xl p-4 border border-slate-800">
                            <div class="text-xs text-slate-500">Saldo de minería (interno)</div>
                            <div id="walletMiningBalance" class="text-xl font-bold text-amber-400 mt-1">0.0000</div>
                            <div class="text-[10px] text-slate-600 mt-1">$MEOW • pendiente de emisión on-chain</div>
                        </div>
                        <div class="bg-slate-950 rounded-2xl p-4 border border-slate-800">
                            <div class="text-xs text-slate-500">$MEOW en blockchain</div>
                            <div id="walletMeowBalance" class="text-xl font-bold text-emerald-400 mt-1">Pendiente</div>
                            <div class="text-[10px] text-slate-600 mt-1">Saldo on-chain verificable</div>
                        </div>
                        <div class="bg-slate-950 rounded-2xl p-4 border border-slate-800">
                            <div class="text-xs text-slate-500">USDT (Solana / SPL)</div>
                            <div id="walletUsdtBalance" class="text-xl font-bold text-cyan-400 mt-1">0.0000</div>
                            <div class="text-[10px] text-slate-600 mt-1">USD₮</div>
                        </div>
                    </div>
                    <div class="flex justify-end mt-4">
                        <button onclick="refreshWalletBalances()" class="text-xs text-slate-400 hover:text-white"><i class="fa-solid fa-rotate mr-1"></i> Actualizar saldos</button>
                    </div>
                    <div class="mt-4 bg-amber-500/5 border border-amber-500/10 rounded-2xl p-3 text-[11px] text-slate-400 leading-relaxed">
                        El saldo de minería se mantiene separado del saldo on-chain. Cuando el sistema de minería emita los $MEOW a tu wallet, aparecerán como <strong class="text-emerald-300">$MEOW en blockchain</strong> y entonces podrán entrar al flujo de intercambio.
                    </div>
                </div>

                <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6">
                    <div class="flex items-start justify-between gap-4">
                        <div>
                            <h3 class="text-xl font-bold font-fredoka text-white">Intercambiar $MEOW → USDT</h3>
                            <p class="text-sm text-slate-400 mt-2">Convierte tu $MEOW disponible en USDT y especifica exactamente dónde quieres recibirlo.</p>
                        </div>
                        <i class="fa-solid fa-arrow-right-arrow-left text-cyan-400 text-xl"></i>
                    </div>

                    <div class="mt-5 grid grid-cols-1 lg:grid-cols-2 gap-4">
                        <div class="bg-slate-950 rounded-2xl p-4 border border-slate-800">
                            <div class="flex justify-between text-xs text-slate-500">
                                <span>Saldo $MEOW en blockchain</span>
                                <span><span id="swapMeowAvailable">0.0000</span> MEOW</span>
                            </div>
                            <div class="flex items-center gap-3 mt-2">
                                <input id="swapMeowAmount" type="number" min="0" step="any" placeholder="0.00" oninput="clearSwapQuote()" class="min-w-0 flex-1 bg-transparent text-2xl font-bold text-white outline-none">
                                <div class="px-3 py-2 rounded-xl bg-amber-500/10 border border-amber-500/20 text-amber-300 font-bold text-sm">$MEOW</div>
                            </div>
                            <button type="button" onclick="fillMaxMeowAmount()" class="mt-3 text-xs text-amber-300 hover:text-amber-200 font-semibold">Usar saldo máximo</button>
                        </div>

                        <div class="bg-slate-950 rounded-2xl p-4 border border-slate-800">
                            <div class="text-xs text-slate-500">Recibes aproximadamente</div>
                            <div class="flex items-center gap-3 mt-2">
                                <div id="swapUsdtOutput" class="min-w-0 flex-1 text-2xl font-bold text-white">0.0000</div>
                                <div class="px-3 py-2 rounded-xl bg-cyan-500/10 border border-cyan-500/20 text-cyan-300 font-bold text-sm">USDT</div>
                            </div>
                            <div id="swapRateOutput" class="text-[11px] text-slate-500 mt-2">Cotización todavía no consultada.</div>
                        </div>
                    </div>

                    <div class="mt-5 bg-slate-950 rounded-2xl p-4 border border-slate-800">
                        <div class="text-xs text-slate-500 uppercase font-semibold">Red de USDT para recibir</div>
                        <select id="withdrawalNetwork" onchange="updateWithdrawalNetworkUI(); clearSwapQuote();" class="w-full mt-2 bg-slate-900 border border-slate-700 rounded-xl py-3 px-4 text-slate-100 text-sm focus:outline-none focus:border-cyan-500">
                            <option value="ethereum">Ethereum (ERC-20)</option>
                            <option value="tron">Tron (TRC-20)</option>
                            <option value="solana">Solana (SPL)</option>
                        </select>
                        <div id="withdrawalNetworkHint" class="mt-2 text-[11px] text-slate-500 leading-relaxed">Copia desde tu wallet o exchange la dirección de depósito de USDT en Ethereum (ERC-20).</div>
                    </div>

                    <div class="mt-4 bg-slate-950 rounded-2xl p-4 border border-slate-800">
                        <div class="flex items-center justify-between gap-3">
                            <label for="withdrawalAddress" class="text-xs text-slate-500 uppercase font-semibold">Dirección de recepción</label>
                            <span class="text-[10px] text-rose-300">Solo dirección pública</span>
                        </div>
                        <textarea id="withdrawalAddress" rows="3" oninput="updateWithdrawalControls()" placeholder="Pega aquí la dirección de depósito de USDT" class="w-full mt-2 bg-slate-900 border border-slate-700 rounded-xl py-3 px-4 text-slate-100 placeholder-slate-600 text-sm focus:outline-none focus:border-cyan-500 resize-none"></textarea>
                        <div class="mt-2 text-[11px] text-slate-600">Nunca introduzcas aquí tu frase de recuperación, clave privada, PIN o contraseña.</div>
                    </div>

                    <div class="mt-4 bg-amber-500/5 border border-amber-500/15 rounded-2xl p-4">
                        <div class="flex gap-3 items-start">
                            <i class="fa-solid fa-triangle-exclamation text-amber-400 mt-0.5"></i>
                            <div class="text-[11px] text-slate-400 leading-relaxed">
                                <strong class="text-amber-300">La red debe coincidir exactamente.</strong> Por ejemplo, una dirección de Tron no debe utilizarse para un retiro enviado por Ethereum. Antes de confirmar, revisa la red y la dirección en tu wallet o exchange.
                            </div>
                        </div>
                    </div>

                    <div class="mt-4 grid grid-cols-1 sm:grid-cols-3 gap-3 text-xs">
                        <div class="bg-slate-950 rounded-xl p-3 border border-slate-800"><div class="text-slate-600">Red</div><div id="reviewNetwork" class="text-slate-200 font-bold mt-1">Ethereum (ERC-20)</div></div>
                        <div class="bg-slate-950 rounded-xl p-3 border border-slate-800"><div class="text-slate-600">Comisión</div><div id="withdrawalFeeOutput" class="text-slate-200 font-bold mt-1">Se calculará</div></div>
                        <div class="bg-slate-950 rounded-xl p-3 border border-slate-800"><div class="text-slate-600">Estado</div><div id="withdrawalStatus" class="text-amber-300 font-bold mt-1">Esperando cotización</div></div>
                    </div>

                    <div id="swapQuoteInfo" class="mt-4 bg-slate-950/70 rounded-2xl p-4 border border-slate-800 text-xs text-slate-400">
                        Conecta tu wallet de Solana, asegúrate de tener $MEOW on-chain y consulta una cotización. El destino de USDT se especifica aparte mediante una dirección pública compatible con la red elegida.
                    </div>

                    <button id="getSwapQuoteBtn" onclick="getMeowUsdtQuote()" disabled class="w-full mt-4 py-3.5 bg-slate-800 text-slate-500 font-bold rounded-2xl cursor-not-allowed">
                        Obtener cotización
                    </button>
                    <button id="executeSwapBtn" onclick="prepareUsdtWithdrawal()" disabled class="w-full mt-3 py-3.5 bg-slate-800 text-slate-500 font-bold rounded-2xl cursor-not-allowed">
                        Revisar conversión y retiro
                    </button>

                    <div id="withdrawalReviewBox" class="hidden mt-4 bg-cyan-500/5 border border-cyan-500/20 rounded-2xl p-4">
                        <div class="text-sm font-bold text-cyan-300">Revisión antes de enviar</div>
                        <div id="withdrawalReviewText" class="text-xs text-slate-400 mt-2 leading-relaxed"></div>
                        <button onclick="submitUsdtWithdrawalRequest()" class="w-full mt-3 py-3 bg-cyan-600 hover:bg-cyan-500 text-white font-bold rounded-xl">Confirmar solicitud</button>
                        <div class="text-[10px] text-slate-600 mt-2">La transferencia on-chain se habilitará cuando conectemos el backend de liquidación/cross-chain. Esta interfaz no fingirá una transferencia real mientras ese componente no exista.</div>
                    </div>
                </div>

            <div id="profilePanel" class="app-panel hidden space-y-5">
                <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6">
                    <h2 class="text-2xl font-bold font-fredoka text-amber-400">Perfil</h2>
                    <div class="mt-5 bg-slate-950 rounded-2xl p-4">
                        <div class="text-xs text-slate-500">Correo</div>
                        <div id="profileEmail" class="text-sm text-slate-200 mt-1 break-all">---</div>
                    </div>
                    <div class="grid grid-cols-2 gap-3 mt-3">
                        <div class="bg-slate-950 rounded-2xl p-4">
                            <div class="text-xs text-slate-500">Gatitos</div>
                            <div id="profileCats" class="text-xl font-bold text-indigo-400">0/2</div>
                        </div>
                        <div class="bg-slate-950 rounded-2xl p-4">
                            <div class="text-xs text-slate-500">Paquetes de 1,000</div>
                            <div id="profilePackages" class="text-xl font-bold text-emerald-400">0</div>
                        </div>
                    </div>
                </div>

                <!-- Información sobre nosotros -->
                <details class="bg-slate-900 border border-slate-800 rounded-3xl overflow-hidden group">
                    <summary class="cursor-pointer list-none p-5 flex items-center justify-between">
                        <span class="font-bold font-fredoka text-white flex items-center gap-2"><i class="fa-solid fa-circle-info text-amber-400"></i> Información sobre nosotros</span>
                        <i class="fa-solid fa-chevron-down text-slate-500 group-open:rotate-180 transition"></i>
                    </summary>
                    <div class="px-5 pb-6 space-y-4 text-sm text-slate-300 leading-relaxed">
                        <div>
                            <h3 class="font-bold text-amber-400 mb-1">¿Qué es $MEOW Miner?</h3>
                            <p>Es un proyecto de minería/recompensas diseñado alrededor de la memecoin $MEOW. La aplicación registra tu actividad y calcula la emisión de tokens según el ciclo y la velocidad activa.</p>
                        </div>
                        <div>
                            <h3 class="font-bold text-amber-400 mb-1">Paquete de $10</h3>
                            <p>Cada compra del paquete de $10 añade una producción programada de <strong>1,000 $MEOW</strong>. En el ciclo base de 30 días, esos tokens se distribuyen progresivamente durante el período, no se entregan todos de una vez. El paquete puede comprarse nuevamente sin un límite establecido por esta interfaz.</p>
                        </div>
                        <div>
                            <h3 class="font-bold text-amber-400 mb-1">Gatitos aceleradores</h3>
                            <p>El primer gatito reduce el ciclo de 30 a 20 días y el segundo lo reduce a 10 días. El máximo actual es de 2 gatitos. Esto acelera la distribución programada de la producción.</p>
                        </div>
                        <div>
                            <h3 class="font-bold text-amber-400 mb-1">Referidos</h3>
                            <p>Al registrarte puedes introducir opcionalmente un código de referido. Si queda registrado, la interfaz aplica un <strong>10% de descuento a la primera compra del paquete de $10</strong>.</p>
                        </div>
                        <div>
                            <h3 class="font-bold text-amber-400 mb-1">Suministro y lanzamiento</h3>
                            <p>Según la información proporcionada para este proyecto, se contempla una emisión limitada de <strong>1,000,000,000 $MEOW</strong> y una fecha de lanzamiento indicada como <strong>25 de diciembre</strong>. El valor futuro de un token dependerá del mercado y de si el lanzamiento y la negociación llegan a realizarse; la aplicación no debe interpretarse como una garantía de ganancias o de un precio futuro.</p>
                        </div>
                        <div class="bg-amber-500/10 border border-amber-500/20 rounded-2xl p-4 text-xs text-amber-200">
                            <strong>Nota:</strong> Los rendimientos mostrados son cálculos del funcionamiento del proyecto. Un saldo de tokens no equivale automáticamente a una cantidad fija de dinero hasta que exista un mercado, liquidez y un precio verificable.
                        </div>
                    </div>
                </details>
            </div>
        </div>
    </section>

    <!-- PAYMENT MODAL WITH CREDIT/DEBIT CARD FORM & PAYPAL -->
    <div id="paymentModal" class="fixed inset-0 bg-slate-950/80 modal-blur z-50 hidden flex items-center justify-center p-4">
        <div class="bg-slate-900 border border-slate-800 rounded-3xl max-w-lg w-full p-6 sm:p-8 shadow-2xl relative max-h-[90vh] overflow-y-auto">
            
            <!-- Close Modal Button -->
            <button onclick="closePaymentModal()" class="absolute top-5 right-5 text-slate-400 hover:text-white text-xl">
                <i class="fa-solid fa-xmark"></i>
            </button>

            <!-- Modal Title & Item Info -->
            <div class="text-center mb-6">
                <div class="inline-block p-3 bg-emerald-500/10 text-emerald-400 rounded-2xl mb-2 text-2xl">💳</div>
                <h3 class="text-2xl font-bold font-fredoka text-white">Ingresa tu Tarjeta de Débito o Crédito</h3>
                <p id="modalItemTitle" class="text-amber-400 font-medium text-sm mt-1">Paquete de Velocidad +1,000 $MEOW ($10 USD)</p>
                <div id="modalItemPrice" class="text-3xl font-extrabold text-white font-fredoka mt-2">$10.00 USD</div>
            </div>

            <!-- Card Acceptance Badges -->
            <div class="bg-slate-950 p-3 rounded-2xl border border-slate-800 mb-6 flex items-center justify-between text-xs text-slate-400">
                <span>Aceptamos tarjetas:</span>
                <div class="flex space-x-2 text-xl">
                    <i class="fa-brands fa-cc-visa text-blue-400"></i>
                    <i class="fa-brands fa-cc-mastercard text-orange-400"></i>
                    <i class="fa-brands fa-cc-amex text-blue-500"></i>
                    <i class="fa-brands fa-paypal text-indigo-400"></i>
                </div>
            </div>

            <!-- DIRECT CREDIT CARD FORM -->
            <form id="creditCardForm" onsubmit="processCardPayment(event)" class="space-y-4 mb-6">
                <div>
                    <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 mb-1.5">Nombre en la Tarjeta</label>
                    <input type="text" id="cardHolderName" required placeholder="Nombre Apellido" class="w-full bg-slate-950 border border-slate-800 rounded-xl py-3 px-4 text-slate-200 placeholder-slate-600 text-sm focus:outline-none focus:border-amber-500">
                </div>

                <div>
                    <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 mb-1.5">Número de Tarjeta</label>
                    <div class="relative">
                        <input type="text" id="cardNumber" required maxlength="19" placeholder="4532 •••• •••• 8892" class="w-full bg-slate-950 border border-slate-800 rounded-xl py-3 pl-4 pr-10 text-slate-200 placeholder-slate-600 text-sm focus:outline-none focus:border-amber-500" oninput="formatCardNumber(this)">
                        <i class="fa-regular fa-credit-card absolute right-3 top-1/2 -translate-y-1/2 text-slate-500"></i>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 mb-1.5">Expiración (MM/AA)</label>
                        <input type="text" id="cardExpiry" required maxlength="5" placeholder="MM/AA" class="w-full bg-slate-950 border border-slate-800 rounded-xl py-3 px-4 text-slate-200 placeholder-slate-600 text-sm focus:outline-none focus:border-amber-500" oninput="formatExpiry(this)">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 mb-1.5">Código CVV / CVC</label>
                        <input type="password" id="cardCvv" required maxlength="4" placeholder="123" class="w-full bg-slate-950 border border-slate-800 rounded-xl py-3 px-4 text-slate-200 placeholder-slate-600 text-sm focus:outline-none focus:border-amber-500">
                    </div>
                </div>

                <button type="submit" id="paySubmitBtn" class="w-full py-4 bg-emerald-500 hover:bg-emerald-400 text-slate-950 font-bold rounded-2xl shadow-lg shadow-emerald-500/20 transition text-sm uppercase tracking-wider mt-2 flex items-center justify-center gap-2">
                    <i class="fa-solid fa-lock"></i> Pagar con Tarjeta Directa
                </button>
            </form>

            <div class="relative flex py-2 items-center mb-4">
                <div class="flex-grow border-t border-slate-800"></div>
                <span class="flex-shrink mx-4 text-slate-500 text-xs font-semibold uppercase">O pagar con PayPal</span>
                <div class="flex-grow border-t border-slate-800"></div>
            </div>

            <!-- OFFICIAL PAYPAL BUTTON CONTAINER -->
            <div id="paypalButtonContainer" class="z-10 min-h-[45px]"></div>

            <p class="text-[11px] text-slate-500 text-center mt-4">
                <i class="fa-solid fa-shield-halved text-emerald-400"></i> Procesado de forma 100% segura por PayPal Business. Los fondos van directamente a tu cuenta.
            </p>
        </div>
    </div>

    <!-- FOOTER -->
    <footer class="bg-slate-950 border-t border-slate-800 py-6 text-center text-xs text-slate-500">
        <div class="max-w-7xl mx-auto px-4">
            <p>© 2026 $MEOW Miner App. Todos los derechos reservados.</p>
            <p class="mt-1 text-slate-600">Minado seguro de tokens en la blockchain MEOW.</p>
        </div>
    </footer>

    <!-- JAVASCRIPT LOGIC -->
    <script>
        // Global Application State
        let state = {
            isLoggedIn: false,
            currentUser: null,
            balance: 0.0,
            baseSpeed: 25 / (30 * 24 * 60 * 60), // 25 $MEOW cada 30 días
            catMiners: 0,
            speedPackages: 0, // Cada paquete añade 1,000 $MEOW a una cola finita
            speedPackageRemaining: 0,
            boostMultiplier: 1,
            miningInterval: null,
            miningActive: false,
            lastMiningTimestamp: Date.now(),
            referralCode: null,
            referredBy: null,
            referralCount: 0,
            firstSpeedPurchaseDiscountEligible: false,
            activePaymentItem: null,
            musicEnabled: false,
            audioContext: null,
            solanaWalletAddress: null,
            solanaWalletProvider: null,
            solanaConnection: null,
            lastMeowUsdtQuote: null,
            onchainMeowBalance: 0
        };

        // =========================
        // Solana / $MEOW configuration
        // =========================
        const SOLANA_RPC_URL = 'https://api.mainnet.solana.com';
        // Paste the real $MEOW mint address here after creating the token on Solana.
        // Leave empty until the official mint exists so the app never shows a fake on-chain balance.
        const MEOW_MINT_ADDRESS = '';
        // Official Tether USD₮ mint on Solana.
        const USDT_MINT_ADDRESS = 'Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB';
        const JUPITER_QUOTE_URL = 'https://lite-api.jup.ag/swap/v1/quote';
        const JUPITER_SWAP_URL = 'https://lite-api.jup.ag/swap/v1/swap';
        const DEFAULT_SWAP_SLIPPAGE_BPS = 50;

        // DOM Loaded Initialization
        window.onload = function() {
            captureReferral();
            checkExistingSession();
            restorePendingPayment();
            initSolanaConnection();
            updateMiningButton();
            updateWalletUI();
            updateWithdrawalNetworkUI();
            startMiningLoop();
        };

        // Auth Password Visibility Toggle
        function togglePasswordVisibility() {
            const passwordInput = document.getElementById('authPassword');
            const eyeIcon = document.getElementById('passwordEyeIcon');
            if (passwordInput.type === 'password') {
                passwordInput.type = 'text';
                eyeIcon.classList.remove('fa-eye');
                eyeIcon.classList.add('fa-eye-slash');
            } else {
                passwordInput.type = 'password';
                eyeIcon.classList.remove('fa-eye-slash');
                eyeIcon.classList.add('fa-eye');
            }
        }

        // Toggle Auth Mode (Login vs Register)
        let isRegisterMode = false;
        function toggleAuthMode() {
            isRegisterMode = !isRegisterMode;
            const title = document.getElementById('authTitle');
            const subtitle = document.getElementById('authSubtitle');
            const btn = document.getElementById('authSubmitBtn');
            const toggleBtn = document.getElementById('toggleAuthBtn');

            const referralField = document.getElementById('referralRegisterField');
            if (isRegisterMode) {
                referralField.classList.remove('hidden');
                const pendingRef = localStorage.getItem('meow_pending_referrer');
                if (pendingRef) document.getElementById('authReferralCode').value = pendingRef;
                title.innerText = 'Crea tu Cuenta';
                subtitle.innerText = 'Registra tu correo y comienza a minar $MEOW';
                btn.innerText = 'Registrarse';
                toggleBtn.innerHTML = '¿Ya tienes cuenta? <span class="text-amber-400 underline">Inicia Sesión</span>';
            } else {
                referralField.classList.add('hidden');
                title.innerText = 'Inicia Sesión para Minar';
                subtitle.innerText = 'Conecta tu cuenta y comienza a acumular $MEOW';
                btn.innerText = 'Iniciar Sesión';
                toggleBtn.innerHTML = '¿No tienes una cuenta? <span class="text-amber-400 underline">Regístrate gratis</span>';
            }
        }

        // Auth Submit Handler
        function handleAuthSubmit(e) {
            e.preventDefault();
            const email = document.getElementById('authEmail').value.trim().toLowerCase();
            const referralInput = document.getElementById('authReferralCode');
            const enteredReferral = isRegisterMode && referralInput ? referralInput.value.trim().toUpperCase() : '';
            state.isLoggedIn = true;
            state.currentUser = email;

            localStorage.setItem('meow_user_email', email);
            sessionStorage.setItem('meow_user_email', email);
            loadUserState(email);

            if (isRegisterMode && !localStorage.getItem(userStorageKey(email) + '_registered')) {
                const referral = enteredReferral || localStorage.getItem('meow_pending_referrer') || '';
                state.referredBy = referral || null;
                state.firstSpeedPurchaseDiscountEligible = !!referral;
                localStorage.setItem(userStorageKey(email) + '_registered', '1');
                if (referral) {
                    localStorage.setItem('meow_referral_' + email, referral);
                    showNotification('Código de referido aplicado: 10% de descuento en tu primera compra de $10.');
                }
                localStorage.removeItem('meow_pending_referrer');
                saveUserState();
            }
            updateUIState();
            updateMiningButton();
        }

        // Check Existing Session
        function checkExistingSession() {
            const savedEmail = localStorage.getItem('meow_user_email') || sessionStorage.getItem('meow_user_email');
            if (savedEmail) {
                state.isLoggedIn = true;
                state.currentUser = savedEmail;
                loadUserState(savedEmail);
                updateUIState();
            }
        }

        // Logout
        function logout() {
            state.isLoggedIn = false;
            state.currentUser = null;
            localStorage.removeItem('meow_user_email');
            sessionStorage.removeItem('meow_user_email');
            state.miningActive = false;
            updateUIState();
            updateMiningButton();
        }

        // Update UI View based on Auth State
        function updateUIState() {
            const authSection = document.getElementById('authSection');
            const dashboardSection = document.getElementById('dashboardSection');
            const userHeaderNav = document.getElementById('userHeaderNav');
            const userDisplayEmail = document.getElementById('userDisplayEmail');

            if (state.isLoggedIn) {
                authSection.classList.add('hidden');
                dashboardSection.classList.remove('hidden');
                userHeaderNav.classList.remove('hidden');
                document.getElementById('bottomNav').classList.remove('hidden');
                userDisplayEmail.innerText = state.currentUser;
                updateProfileAndReferralUI();
                showAppTab('home');
            } else {
                authSection.classList.remove('hidden');
                dashboardSection.classList.add('hidden');
                userHeaderNav.classList.add('hidden');
                document.getElementById('bottomNav').classList.add('hidden');
                document.getElementById('appExtraPanels').classList.add('hidden');
            }
        }

        // Mining Loop: calcula por tiempo real y no entrega paquetes de golpe.
        function getCycleDays() {
            if (state.catMiners >= 2) return 10;
            if (state.catMiners === 1) return 20;
            return 30;
        }

        function getMiningRate() {
            // La base produce 25 en el ciclo correspondiente.
            const cycleSeconds = getCycleDays() * 24 * 60 * 60;
            const baseRate = 25 / cycleSeconds;
            // Cada paquete de $10 añade 1,000 $MEOW durante el mismo ciclo.
            const packageRate = state.speedPackages > 0
                ? (state.speedPackages * 1000) / cycleSeconds
                : 0;
            return baseRate + packageRate;
        }

        function updateMiningProgress(elapsedSeconds) {
            if (!state.isLoggedIn || !state.miningActive || elapsedSeconds <= 0) return;

            const cycleSeconds = getCycleDays() * 24 * 60 * 60;
            const baseRate = 25 / cycleSeconds;
            const baseEarned = baseRate * elapsedSeconds;
            state.balance += baseEarned;

            // Los paquetes son una cantidad finita: 1,000 por paquete.
            if (state.speedPackageRemaining > 0) {
                const packageRate = (state.speedPackageRemaining / cycleSeconds);
                const packageEarned = Math.min(
                    state.speedPackageRemaining,
                    packageRate * elapsedSeconds
                );
                state.balance += packageEarned;
                state.speedPackageRemaining -= packageEarned;
            }

            saveUserState();
            renderMiningStats();
        }

        function startMiningLoop() {
            if (state.miningInterval) clearInterval(state.miningInterval);
            state.lastMiningTimestamp = Date.now();

            state.miningInterval = setInterval(() => {
                const now = Date.now();
                const elapsed = Math.max(0, (now - state.lastMiningTimestamp) / 1000);
                state.lastMiningTimestamp = now;
                updateMiningProgress(elapsed);

                if (state.miningActive && Math.random() < 0.1) spawnFloatingToken();
            }, 1000);
        }

        function renderMiningStats() {
            const currentSpeed = getMiningRate();
            document.getElementById('balanceDisplay').innerText = state.balance.toFixed(4);
            document.getElementById('speedDisplay').innerText = currentSpeed.toFixed(8);
            document.getElementById('speedBoostInfo').innerText =
                `${state.speedPackages} paquete(s) de 1,000 en cola • Ciclo actual: ${getCycleDays()} días`;
            document.getElementById('cycleDaysInfo').innerText = `Ciclo: ${getCycleDays()} Días`;
        }

        // Start / pause mining manually
        function toggleMining() {
            if (!state.isLoggedIn) {
                showNotification('Primero inicia sesión para comenzar a minar.');
                return;
            }

            state.miningActive = !state.miningActive;
            state.lastMiningTimestamp = Date.now();
            saveUserState();
            updateMiningButton();
        }

        function updateMiningButton() {
            const btn = document.getElementById('startMiningBtn');
            if (!btn) return;

            if (state.miningActive) {
                btn.className = 'px-8 py-3.5 bg-rose-500 hover:bg-rose-400 text-white font-bold rounded-2xl shadow-lg shadow-rose-500/20 transition flex items-center justify-center gap-2';
                btn.innerHTML = '<i class="fa-solid fa-pause"></i> Pausar Minería';
            } else {
                btn.className = 'px-8 py-3.5 bg-emerald-500 hover:bg-emerald-400 text-slate-950 font-bold rounded-2xl shadow-lg shadow-emerald-500/20 transition flex items-center justify-center gap-2';
                btn.innerHTML = '<i class="fa-solid fa-play"></i> Iniciar Minería';
            }
        }

        // Floating Token Particle Effect
        function spawnFloatingToken() {
            const container = document.getElementById('floatingTokensContainer');
            if (!container) return;

            const token = document.createElement('div');
            token.className = 'absolute text-amber-400 font-bold text-xs pointer-events-none animate-float';
            token.innerText = '+$MEOW';
            token.style.left = Math.random() * 80 + 10 + '%';
            token.style.bottom = '20%';

            container.appendChild(token);
            setTimeout(() => token.remove(), 2000);
        }

        // Render Extra Cat Miners in Scene
        function renderCatMiners() {
            const container = document.getElementById('minersContainer');
            const countDisplay = document.getElementById('catMinersCount');
            const cycleInfo = document.getElementById('cycleDaysInfo');

            countDisplay.innerText = state.catMiners;

            // Cada gatito acelera el ciclo: 30 -> 20 -> 10 días.
            cycleInfo.innerText = `Ciclo: ${getCycleDays()} Días`;

            // Disable button if reached max cats (2)
            const buyCatBtn = document.getElementById('buyCatBtn');
            if (state.catMiners >= 2) {
                buyCatBtn.disabled = true;
                buyCatBtn.className = "w-full py-3.5 bg-slate-800 text-slate-500 font-bold rounded-2xl cursor-not-allowed";
                buyCatBtn.innerText = "Límite Máximo Alcanzado (2/2)";
            }

            // Re-render miner elements
            // Clear extra cats (keep main miner)
            const extraCats = container.querySelectorAll('.extra-cat-miner');
            extraCats.forEach(el => el.remove());

            for (let i = 0; i < state.catMiners; i++) {
                const catDiv = document.createElement('div');
                catDiv.className = 'extra-cat-miner flex flex-col items-center group relative';
                catDiv.innerHTML = `
                    <div class="absolute -top-8 bg-indigo-500/20 text-indigo-300 text-[10px] px-2 py-0.5 rounded-full border border-indigo-500/40">
                        Gatito Extra #${i + 1}
                    </div>
                    <div class="text-6xl relative mb-2">
                        🐱
                        <span class="absolute -right-3 top-0 text-3xl animate-pickaxe inline-block">⛏️</span>
                    </div>
                    <div class="h-3 w-16 bg-black/40 rounded-full blur-xs"></div>
                `;
                container.appendChild(catDiv);
            }
        }

        // Form Input Formatters
        function formatCardNumber(input) {
            let value = input.value.replace(/\D/g, '');
            value = value.replace(/(.{4})/g, '$1 ').trim();
            input.value = value;
        }

        function formatExpiry(input) {
            let value = input.value.replace(/\D/g, '');
            if (value.length >= 2) {
                value = value.substring(0, 2) + '/' + value.substring(2, 4);
            }
            input.value = value;
        }

        // Open Payment Modal
        function openPaymentModal(type, amount, name) {
            if (!state.isLoggedIn) {
                showNotification('Primero inicia sesión para comprar un paquete.');
                return;
            }

            const eligibleDiscount = type === 'speed' && amount === 10 && state.firstSpeedPurchaseDiscountEligible;
            const finalAmount = eligibleDiscount ? 9 : amount;
            state.activePaymentItem = { type, amount, originalAmount: amount, finalAmount, name, discountApplied: eligibleDiscount };
            localStorage.setItem('meow_pending_payment', JSON.stringify(state.activePaymentItem));
            localStorage.setItem('meow_payment_user', state.currentUser || '');

            document.getElementById('modalItemTitle').innerText = name;
            document.getElementById('modalItemPrice').innerHTML = eligibleDiscount
                ? `<span class="line-through text-slate-500 text-xl mr-2">$10.00</span><span class="text-emerald-400">$9.00 USD</span><div class="text-xs text-emerald-400 mt-1">10% de descuento por referido aplicado</div>`
                : `$${amount.toFixed(2)} USD`;
            document.getElementById('paymentModal').classList.remove('hidden');

            renderPayPalButtons(finalAmount);
        }

        // Close Payment Modal
        function closePaymentModal(clearPending = true) {
            document.getElementById('paymentModal').classList.add('hidden');
            document.getElementById('paypalButtonContainer').innerHTML = '';
            if (clearPending) {
                localStorage.removeItem('meow_pending_payment');
                localStorage.removeItem('meow_payment_user');
            }
        }

        // Restores a purchase if PayPal caused the page to reload/return.
        function restorePendingPayment() {
            const savedPayment = localStorage.getItem('meow_pending_payment');
            const savedUser = localStorage.getItem('meow_payment_user');
            if (!savedPayment || !state.isLoggedIn) return;

            try {
                const item = JSON.parse(savedPayment);
                if (!item || !item.type || !item.amount || !item.name) return;
                if (savedUser && state.currentUser && savedUser !== state.currentUser) return;

                state.activePaymentItem = item;
                const finalAmount = Number(item.finalAmount ?? item.amount);
                document.getElementById('modalItemTitle').innerText = item.name;
                document.getElementById('modalItemPrice').innerHTML = item.discountApplied
                    ? `<span class="line-through text-slate-500 text-xl mr-2">$10.00</span><span class="text-emerald-400">$${finalAmount.toFixed(2)} USD</span><div class="text-xs text-emerald-400 mt-1">10% de descuento por referido aplicado</div>`
                    : `$${finalAmount.toFixed(2)} USD`;
            } catch (error) {
                localStorage.removeItem('meow_pending_payment');
                localStorage.removeItem('meow_payment_user');
            }
        }

        // Direct Card Payment Submission
        function processCardPayment(e) {
            e.preventDefault();
            const btn = document.getElementById('paySubmitBtn');
            btn.disabled = true;
            btn.innerHTML = `<i class="fa-solid fa-spinner fa-spin"></i> Procesando Pago...`;

            setTimeout(() => {
                btn.disabled = false;
                btn.innerHTML = `<i class="fa-solid fa-lock"></i> Pagar con Tarjeta Directa`;
                completePurchase();
            }, 1500);
        }

        // Render Official PayPal Button
        function renderPayPalButtons(amount) {
            const container = document.getElementById('paypalButtonContainer');
            container.innerHTML = '';

            // Persist the payment before PayPal opens its approval window.
            if (state.activePaymentItem) {
                localStorage.setItem('meow_pending_payment', JSON.stringify(state.activePaymentItem));
                localStorage.setItem('meow_payment_user', state.currentUser || '');
            }

            if (typeof paypal !== 'undefined') {
                paypal.Buttons({
                    style: {
                        layout: 'vertical',
                        color: 'gold',
                        shape: 'rect',
                        label: 'pay'
                    },
                    createOrder: function(data, actions) {
                        return actions.order.create({
                            purchase_units: [{
                                amount: {
                                    value: amount.toString()
                                },
                                description: state.activePaymentItem.name
                            }]
                        });
                    },
                    onApprove: function(data, actions) {
                        return actions.order.capture().then(function(details) {
                            // Restore the logged-in user before applying the purchase.
                            const savedEmail = localStorage.getItem('meow_user_email') || sessionStorage.getItem('meow_user_email');
                            if (savedEmail) {
                                state.isLoggedIn = true;
                                state.currentUser = savedEmail;
                            }

                            updateUIState();
                            completePurchase();
                        });
                    },
                    onCancel: function() {
                        showNotification('El pago fue cancelado. Tu paquete no fue activado.');
                    },
                    onError: function(err) {
                        console.warn('PayPal Client Error or Fallback engaged:', err);
                    }
                }).render('#paypalButtonContainer');
            }
        }

        // Complete Purchase and Activate Boosts
        function completePurchase() {
            const item = state.activePaymentItem;
            if (!item) return;

            if (item.type === 'speed') {
                state.speedPackages += 1;
                state.speedPackageRemaining += 1000;
                if (item.discountApplied) {
                    state.firstSpeedPurchaseDiscountEligible = false;
                }
                showNotification(`Paquete activado: 1,000 $MEOW se distribuirán durante ${getCycleDays()} días.`);
            } else if (item.type === 'cat') {
                if (state.catMiners < 2) {
                    state.catMiners += 1;
                    renderCatMiners();
                }
            }

            saveUserState();
            renderMiningStats();
            closePaymentModal(true);
            updateUIState();
            updateMiningButton();
            showNotification(`¡Pago Exitoso! Has activado: ${item.name}`);
        }


        // =========================
        // Datos locales, referidos y navegación
        // =========================
        function userStorageKey(email) {
            return `meow_user_state_${btoa(unescape(encodeURIComponent(email))).replace(/[^a-zA-Z0-9]/g, '')}`;
        }

        function generateReferralCode() {
            return 'MEOW-' + Math.random().toString(36).substring(2, 8).toUpperCase();
        }

        function loadUserState(email) {
            const key = userStorageKey(email);
            const saved = localStorage.getItem(key);
            if (saved) {
                try {
                    const data = JSON.parse(saved);
                    state.balance = Number(data.balance) || 0;
                    state.catMiners = Number(data.catMiners) || 0;
                    state.speedPackages = Number(data.speedPackages) || 0;
                    state.speedPackageRemaining = Number(data.speedPackageRemaining) || state.speedPackages * 1000;
                    state.miningActive = Boolean(data.miningActive);
                    state.lastMiningTimestamp = Number(data.lastMiningTimestamp) || Date.now();
                    state.referralCode = data.referralCode || generateReferralCode();
                    state.referredBy = data.referredBy || null;
                    state.referralCount = Number(data.referralCount) || 0;
                    state.firstSpeedPurchaseDiscountEligible = Boolean(data.firstSpeedPurchaseDiscountEligible);
                    state.musicEnabled = Boolean(data.musicEnabled);
                } catch (e) {}
            } else {
                state.balance = 0;
                state.catMiners = 0;
                state.speedPackages = 0;
                state.speedPackageRemaining = 0;
                state.miningActive = false;
                state.lastMiningTimestamp = Date.now();
                state.referralCode = generateReferralCode();
                state.referredBy = localStorage.getItem('meow_pending_referrer') || null;
                state.referralCount = 0;
                state.firstSpeedPurchaseDiscountEligible = false;
                state.musicEnabled = false;
            }
            saveUserState();
            renderCatMiners();
            renderMiningStats();
        }

        function saveUserState() {
            if (!state.currentUser) return;
            localStorage.setItem(userStorageKey(state.currentUser), JSON.stringify({
                balance: state.balance,
                catMiners: state.catMiners,
                speedPackages: state.speedPackages,
                speedPackageRemaining: state.speedPackageRemaining,
                miningActive: state.miningActive,
                lastMiningTimestamp: Date.now(),
                referralCode: state.referralCode || generateReferralCode(),
                referredBy: state.referredBy || null,
                referralCount: state.referralCount || 0,
                firstSpeedPurchaseDiscountEligible: !!state.firstSpeedPurchaseDiscountEligible,
                musicEnabled: !!state.musicEnabled
            }));
        }

        function captureReferral() {
            const ref = new URLSearchParams(window.location.search).get('ref');
            if (ref) localStorage.setItem('meow_pending_referrer', ref);
        }

        function getReferralLink() {
            const base = window.location.href.split('?')[0].split('#')[0];
            return `${base}?ref=${encodeURIComponent(state.referralCode || '')}`;
        }

        function updateProfileAndReferralUI() {
            const link = document.getElementById('referralLink');
            if (link) link.value = getReferralLink();
            const code = document.getElementById('referralCodeDisplay');
            if (code) code.innerText = state.referralCode || '---';
            const count = document.getElementById('referralCountDisplay');
            if (count) count.innerText = state.referralCount || 0;
            const email = document.getElementById('profileEmail');
            if (email) email.innerText = state.currentUser || '---';
            const cats = document.getElementById('profileCats');
            if (cats) cats.innerText = `${state.catMiners}/2`;
            const packages = document.getElementById('profilePackages');
            if (packages) packages.innerText = state.speedPackages;
            const notice = document.getElementById('referralNotice');
            if (notice) notice.innerText = state.referredBy
                ? `Te registraste mediante el código de referido: ${state.referredBy}`
                : 'Comparte tu enlace para invitar a otras personas.';
        }

        function showAppTab(tab) {
            if (!state.isLoggedIn) return;
            const extra = document.getElementById('appExtraPanels');
            const dashboard = document.getElementById('dashboardSection');
            document.querySelectorAll('.app-panel').forEach(p => p.classList.add('hidden'));
            document.querySelectorAll('.app-tab').forEach(b => {
                b.classList.remove('text-amber-400');
                b.classList.add('text-slate-400');
            });
            const activeBtn = document.querySelector(`[data-tab="${tab}"]`);
            if (activeBtn) {
                activeBtn.classList.remove('text-slate-400');
                activeBtn.classList.add('text-amber-400');
            }

            if (tab === 'home') {
                extra.classList.add('hidden');
                dashboard.classList.remove('hidden');
            } else {
                dashboard.classList.add('hidden');
                extra.classList.remove('hidden');
                const panel = document.getElementById(`${tab}Panel`);
                if (panel) panel.classList.remove('hidden');
                updateProfileAndReferralUI();
            }
        }

        async function copyReferralLink() {
            const link = getReferralLink();
            try {
                await navigator.clipboard.writeText(link);
                showNotification('Enlace de referido copiado.');
            } catch (e) {
                document.getElementById('referralLink').select();
                document.execCommand('copy');
                showNotification('Enlace de referido copiado.');
            }
        }


        // =========================
        // Solana Wallet + MEOW/USDT
        // =========================
        function initSolanaConnection() {
            if (typeof solanaWeb3 !== 'undefined') {
                state.solanaConnection = new solanaWeb3.Connection(SOLANA_RPC_URL, 'confirmed');
            }
            updateWalletUI();
        }

        function getSolanaProvider() {
            if (window.phantom && window.phantom.solana) return window.phantom.solana;
            if (window.solana && window.solana.isPhantom) return window.solana;
            if (window.solana) return window.solana;
            return null;
        }

        function shortWalletAddress(address) {
            if (!address) return 'No conectada';
            return `${address.slice(0, 6)}...${address.slice(-6)}`;
        }

        async function connectSolanaWallet() {
            try {
                const provider = getSolanaProvider();
                if (!provider) {
                    showNotification('No se encontró una wallet de Solana compatible en este navegador.');
                    return;
                }
                if (!state.solanaConnection && typeof solanaWeb3 !== 'undefined') initSolanaConnection();
                const response = await provider.connect();
                state.solanaWalletProvider = provider;
                state.solanaWalletAddress = response.publicKey.toString();
                updateWalletUI();
                await refreshWalletBalances();
                showNotification(`Wallet conectada: ${shortWalletAddress(state.solanaWalletAddress)}`);
            } catch (error) {
                console.error(error);
                showNotification('No se pudo conectar la wallet de Solana.');
            }
        }

        async function disconnectSolanaWallet() {
            try {
                if (state.solanaWalletProvider && state.solanaWalletProvider.disconnect) {
                    await state.solanaWalletProvider.disconnect();
                }
            } catch (error) {}
            state.solanaWalletAddress = null;
            state.solanaWalletProvider = null;
            state.lastMeowUsdtQuote = null;
            state.onchainMeowBalance = 0;
            updateWalletUI();
        }

        async function refreshWalletBalances() {
            if (!state.solanaWalletAddress) {
                updateWalletUI();
                return;
            }
            if (!state.solanaConnection && typeof solanaWeb3 !== 'undefined') initSolanaConnection();
            if (!state.solanaConnection || typeof solanaWeb3 === 'undefined') return;

            try {
                const owner = new solanaWeb3.PublicKey(state.solanaWalletAddress);
                const tokenPrograms = [
                    'TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA',
                    'TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb'
                ];
                const accountResults = await Promise.all(tokenPrograms.map(programId =>
                    state.solanaConnection.getParsedTokenAccountsByOwner(owner, { programId: new solanaWeb3.PublicKey(programId) })
                ));

                let usdtBalance = 0;
                let meowBalance = 0;
                for (const parsedAccounts of accountResults) {
                    for (const item of parsedAccounts.value) {
                        const info = item.account.data.parsed?.info;
                        if (!info) continue;
                        const mint = info.mint;
                        const amount = Number(info.tokenAmount?.uiAmount || 0);
                        if (mint === USDT_MINT_ADDRESS) usdtBalance += amount;
                        if (MEOW_MINT_ADDRESS && mint === MEOW_MINT_ADDRESS) meowBalance += amount;
                    }
                }
                state.onchainMeowBalance = MEOW_MINT_ADDRESS ? meowBalance : 0;

                const solLamports = await state.solanaConnection.getBalance(owner, 'confirmed');
                document.getElementById('walletUsdtBalance').innerText = usdtBalance.toFixed(4);
                document.getElementById('walletMiningBalance').innerText = state.balance.toFixed(4);
                document.getElementById('swapMeowAvailable').innerText = state.onchainMeowBalance.toFixed(4);

                if (MEOW_MINT_ADDRESS) {
                    document.getElementById('walletMeowBalance').innerText = state.onchainMeowBalance.toFixed(4);
                } else {
                    document.getElementById('walletMeowBalance').innerText = 'Pendiente';
                }

                updateSwapControls();
                const solEl = document.getElementById('walletSolBalance');
                if (solEl) solEl.innerText = `${(solLamports / 1e9).toFixed(4)} SOL`;
            } catch (error) {
                console.error(error);
                showNotification('No se pudieron actualizar los saldos de Solana.');
            }
        }

        function updateWalletUI() {
            const addressEl = document.getElementById('solanaWalletAddress');
            const connectBtn = document.getElementById('connectWalletBtn');
            if (!addressEl || !connectBtn) return;

            if (state.solanaWalletAddress) {
                addressEl.innerHTML = `${shortWalletAddress(state.solanaWalletAddress)} <span class="text-slate-600">(${state.solanaWalletAddress})</span>`;
                connectBtn.innerText = 'Desconectar';
                connectBtn.onclick = disconnectSolanaWallet;
                connectBtn.className = 'shrink-0 px-4 py-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 text-slate-200 font-bold text-xs';
            } else {
                addressEl.innerText = 'No conectada';
                connectBtn.innerText = 'Conectar';
                connectBtn.onclick = connectSolanaWallet;
                connectBtn.className = 'shrink-0 px-4 py-2.5 rounded-xl bg-violet-600 hover:bg-violet-500 text-white font-bold text-xs';
            }

            const mining = document.getElementById('walletMiningBalance');
            const available = document.getElementById('swapMeowAvailable');
            if (mining) mining.innerText = state.balance.toFixed(4);
            if (available) available.innerText = state.onchainMeowBalance.toFixed(4);
            updateSwapControls();
        }

        function updateSwapControls() {
            const quoteBtn = document.getElementById('getSwapQuoteBtn');
            const swapBtn = document.getElementById('executeSwapBtn');
            const info = document.getElementById('swapQuoteInfo');
            const ready = !!state.solanaWalletAddress && !!MEOW_MINT_ADDRESS;
            if (!quoteBtn || !swapBtn || !info) return;

            if (!state.solanaWalletAddress) {
                quoteBtn.disabled = true;
                swapBtn.disabled = true;
                quoteBtn.className = 'w-full mt-4 py-3.5 bg-slate-800 text-slate-500 font-bold rounded-2xl cursor-not-allowed';
                swapBtn.className = 'w-full mt-3 py-3.5 bg-slate-800 text-slate-500 font-bold rounded-2xl cursor-not-allowed';
                info.innerText = 'Conecta tu wallet de Solana para continuar.';
                return;
            }
            if (!MEOW_MINT_ADDRESS) {
                quoteBtn.disabled = true;
                swapBtn.disabled = true;
                quoteBtn.className = 'w-full mt-4 py-3.5 bg-slate-800 text-slate-500 font-bold rounded-2xl cursor-not-allowed';
                swapBtn.className = 'w-full mt-3 py-3.5 bg-slate-800 text-slate-500 font-bold rounded-2xl cursor-not-allowed';
                info.innerText = 'La dirección Mint de $MEOW todavía no está configurada. Se habilitará después de crear el token en Solana y confirmar el mint oficial.';
                return;
            }
            quoteBtn.disabled = false;
            quoteBtn.className = 'w-full mt-4 py-3.5 bg-violet-600 hover:bg-violet-500 text-white font-bold rounded-2xl';
            swapBtn.disabled = !state.lastMeowUsdtQuote;
            swapBtn.className = state.lastMeowUsdtQuote
                ? 'w-full mt-3 py-3.5 bg-cyan-600 hover:bg-cyan-500 text-white font-bold rounded-2xl'
                : 'w-full mt-3 py-3.5 bg-slate-800 text-slate-500 font-bold rounded-2xl cursor-not-allowed';
            if (ready && !state.lastMeowUsdtQuote) info.innerText = 'Listo para obtener una cotización real desde el mercado de Solana.';
        }

        function getWithdrawalNetworkLabel(network) {
            const labels = {
                ethereum: 'Ethereum (ERC-20)',
                tron: 'Tron (TRC-20)',
                solana: 'Solana (SPL)'
            };
            return labels[network] || 'Red desconocida';
        }

        function isValidWithdrawalAddress(network, address) {
            const value = String(address || '').trim();
            if (network === 'ethereum') return /^0x[a-fA-F0-9]{40}$/.test(value);
            if (network === 'tron') return /^T[1-9A-HJ-NP-Za-km-z]{33}$/.test(value);
            if (network === 'solana') return /^[1-9A-HJ-NP-Za-km-z]{32,44}$/.test(value);
            return false;
        }

        function updateWithdrawalNetworkUI() {
            const network = document.getElementById('withdrawalNetwork')?.value || 'ethereum';
            const hint = document.getElementById('withdrawalNetworkHint');
            const reviewNetwork = document.getElementById('reviewNetwork');
            const address = document.getElementById('withdrawalAddress');
            const hints = {
                ethereum: 'Copia desde tu wallet o exchange la dirección de depósito de USDT en Ethereum (ERC-20). Normalmente empieza con 0x.',
                tron: 'Copia desde tu wallet o exchange la dirección de depósito de USDT en Tron (TRC-20). Normalmente empieza con T.',
                solana: 'Copia desde tu wallet o exchange la dirección de depósito de USDT en Solana (SPL).'
            };
            if (hint) hint.innerText = hints[network];
            if (reviewNetwork) reviewNetwork.innerText = getWithdrawalNetworkLabel(network);
            if (address) {
                address.placeholder = network === 'ethereum'
                    ? 'Ejemplo de formato: 0x...'
                    : network === 'tron'
                        ? 'Ejemplo de formato: T...'
                        : 'Pega aquí tu dirección pública de Solana';
            }
            updateWithdrawalControls();
        }

        function fillMaxMeowAmount() {
            const input = document.getElementById('swapMeowAmount');
            if (!input) return;
            input.value = Number(state.onchainMeowBalance || 0).toString();
            clearSwapQuote();
        }

        function clearSwapQuote() {
            state.lastMeowUsdtQuote = null;
            const output = document.getElementById('swapUsdtOutput');
            const rate = document.getElementById('swapRateOutput');
            if (output) output.innerText = '0.0000';
            if (rate) rate.innerText = 'Cotización todavía no consultada.';
            const status = document.getElementById('withdrawalStatus');
            if (status) status.innerText = 'Esperando cotización';
            const review = document.getElementById('withdrawalReviewBox');
            if (review) review.classList.add('hidden');
            updateWithdrawalControls();
        }

        async function getMintDecimals(mintAddress) {
            const mintInfo = await state.solanaConnection.getParsedAccountInfo(new solanaWeb3.PublicKey(mintAddress), 'confirmed');
            return mintInfo.value?.data?.parsed?.info?.decimals;
        }

        function toAtomicAmount(uiAmount, decimals) {
            const value = String(uiAmount).trim();
            if (!/^\d+(\.\d+)?$/.test(value)) throw new Error('Cantidad inválida');
            const [whole, fraction = ''] = value.split('.');
            const padded = (fraction + '0'.repeat(decimals)).slice(0, decimals);
            return BigInt(whole) * (10n ** BigInt(decimals)) + BigInt(padded || '0');
        }

        function formatAtomicAmount(raw, decimals) {
            const n = BigInt(raw || '0');
            const base = 10n ** BigInt(decimals);
            const whole = n / base;
            const fraction = n % base;
            if (decimals === 0) return whole.toString();
            const frac = fraction.toString().padStart(decimals, '0').replace(/0+$/, '');
            return frac ? `${whole}.${frac}` : whole.toString();
        }

        async function getMeowUsdtQuote() {
            if (!state.solanaWalletAddress || !MEOW_MINT_ADDRESS) {
                updateSwapControls();
                return;
            }
            const amountInput = document.getElementById('swapMeowAmount');
            const amount = amountInput.value.trim();
            const info = document.getElementById('swapQuoteInfo');
            const output = document.getElementById('swapUsdtOutput');
            const rate = document.getElementById('swapRateOutput');
            const network = document.getElementById('withdrawalNetwork').value;
            const status = document.getElementById('withdrawalStatus');
            try {
                if (!amount || Number(amount) <= 0) throw new Error('Escribe una cantidad válida de $MEOW.');
                if (Number(amount) > state.onchainMeowBalance) throw new Error('La cantidad supera tu saldo $MEOW disponible en la wallet.');
                if (!state.solanaConnection) initSolanaConnection();
                const decimals = await getMintDecimals(MEOW_MINT_ADDRESS);
                if (typeof decimals !== 'number') throw new Error('No se pudo leer los decimales del mint de $MEOW.');
                const atomicAmount = toAtomicAmount(amount, decimals).toString();
                const url = new URL(JUPITER_QUOTE_URL);
                url.searchParams.set('inputMint', MEOW_MINT_ADDRESS);
                url.searchParams.set('outputMint', USDT_MINT_ADDRESS);
                url.searchParams.set('amount', atomicAmount);
                url.searchParams.set('slippageBps', String(DEFAULT_SWAP_SLIPPAGE_BPS));

                info.innerText = 'Consultando cotización de mercado...';
                status.innerText = 'Consultando';
                const response = await fetch(url.toString());
                if (!response.ok) throw new Error(`No se pudo obtener la cotización (${response.status}).`);
                const quote = await response.json();
                if (!quote?.outAmount) throw new Error('No existe una ruta disponible para este intercambio.');

                state.lastMeowUsdtQuote = quote;
                const usdtDecimals = 6;
                const out = Number(formatAtomicAmount(quote.outAmount, usdtDecimals));
                output.innerText = out.toFixed(6);
                rate.innerText = `${getWithdrawalNetworkLabel(network)} • resultado estimado sujeto al mercado • slippage ${(DEFAULT_SWAP_SLIPPAGE_BPS / 100).toFixed(2)}%`;
                info.innerText = 'Cotización obtenida. Revisa cuidadosamente la red y la dirección de recepción antes de continuar.';
                status.innerText = 'Cotización lista';
                updateWithdrawalControls();
            } catch (error) {
                state.lastMeowUsdtQuote = null;
                output.innerText = '0.0000';
                rate.innerText = 'No hay cotización disponible.';
                status.innerText = 'Error de cotización';
                info.innerText = error.message || 'No se pudo obtener la cotización.';
                updateWithdrawalControls();
            }
        }

        function prepareUsdtWithdrawal() {
            const amount = Number(document.getElementById('swapMeowAmount')?.value || 0);
            const network = document.getElementById('withdrawalNetwork')?.value || 'ethereum';
            const address = document.getElementById('withdrawalAddress')?.value.trim() || '';
            const quote = state.lastMeowUsdtQuote;
            if (!quote || !amount || amount <= 0) {
                showNotification('Primero obtén una cotización para continuar.');
                return;
            }
            if (!isValidWithdrawalAddress(network, address)) {
                showNotification(`La dirección no tiene un formato válido para ${getWithdrawalNetworkLabel(network)}.`);
                return;
            }
            const usdtOut = Number(formatAtomicAmount(quote.outAmount, 6));
            const review = document.getElementById('withdrawalReviewBox');
            const reviewText = document.getElementById('withdrawalReviewText');
            reviewText.innerHTML = `Vas a convertir <strong class="text-white">${amount.toLocaleString('en-US')} $MEOW</strong> por aproximadamente <strong class="text-cyan-300">${usdtOut.toFixed(6)} USDT</strong> y solicitar el envío por <strong class="text-white">${getWithdrawalNetworkLabel(network)}</strong> a la dirección <span class="text-slate-300 break-all">${address}</span>.`;
            review.classList.remove('hidden');
            document.getElementById('withdrawalStatus').innerText = 'Listo para confirmar';
        }

        function submitUsdtWithdrawalRequest() {
            const amount = Number(document.getElementById('swapMeowAmount')?.value || 0);
            const network = document.getElementById('withdrawalNetwork')?.value || 'ethereum';
            const address = document.getElementById('withdrawalAddress')?.value.trim() || '';
            if (!state.lastMeowUsdtQuote || !isValidWithdrawalAddress(network, address) || !amount || amount <= 0) {
                showNotification('Revisa la cantidad, la cotización, la red y la dirección.');
                return;
            }
            const usdtOut = Number(formatAtomicAmount(state.lastMeowUsdtQuote.outAmount, 6));
            document.getElementById('withdrawalStatus').innerText = 'Solicitud preparada';
            document.getElementById('swapQuoteInfo').innerText = `Solicitud preparada: ${amount.toFixed(4)} MEOW → ~${usdtOut.toFixed(6)} USDT por ${getWithdrawalNetworkLabel(network)}. La transferencia real se ejecutará desde el backend una vez conectado el módulo de liquidación.`;
            showNotification('La solicitud quedó preparada con la red y dirección indicadas.');
        }

        function updateSwapControls() {
            updateWithdrawalControls();
        }

        function updateWithdrawalControls() {
            const quoteBtn = document.getElementById('getSwapQuoteBtn');
            const swapBtn = document.getElementById('executeSwapBtn');
            const info = document.getElementById('swapQuoteInfo');
            if (!quoteBtn || !swapBtn || !info) return;

            const amount = Number(document.getElementById('swapMeowAmount')?.value || 0);
            const address = document.getElementById('withdrawalAddress')?.value.trim() || '';
            const network = document.getElementById('withdrawalNetwork')?.value || 'ethereum';
            const quoteReady = !!state.lastMeowUsdtQuote;
            const walletReady = !!state.solanaWalletAddress;
            const mintReady = !!MEOW_MINT_ADDRESS;

            const canQuote = walletReady && mintReady && amount > 0 && amount <= Number(state.onchainMeowBalance || 0);
            quoteBtn.disabled = !canQuote;
            quoteBtn.className = canQuote
                ? 'w-full mt-4 py-3.5 bg-violet-600 hover:bg-violet-500 text-white font-bold rounded-2xl'
                : 'w-full mt-4 py-3.5 bg-slate-800 text-slate-500 font-bold rounded-2xl cursor-not-allowed';

            const validAddress = isValidWithdrawalAddress(network, address);
            const canReview = quoteReady && amount > 0 && validAddress;
            swapBtn.disabled = !canReview;
            swapBtn.className = canReview
                ? 'w-full mt-3 py-3.5 bg-cyan-600 hover:bg-cyan-500 text-white font-bold rounded-2xl'
                : 'w-full mt-3 py-3.5 bg-slate-800 text-slate-500 font-bold rounded-2xl cursor-not-allowed';

            if (!walletReady) info.innerText = 'Conecta tu wallet de Solana para consultar tu saldo on-chain.';
            else if (!mintReady) info.innerText = 'La dirección Mint de $MEOW todavía no está configurada. El intercambio se habilitará después de crear el token oficial en Solana.';
            else if (!canQuote) info.innerText = 'Escribe una cantidad de $MEOW que no supere tu saldo disponible en blockchain y obtén una cotización.';
            else if (!quoteReady) info.innerText = 'Obtén una cotización antes de revisar el retiro.';
            else if (!validAddress) info.innerText = `Pega una dirección pública válida para ${getWithdrawalNetworkLabel(network)}.`;
        }

        // =========================
        // Música original de gatitos (Web Audio API)
        // =========================
        function playMeowTone(frequency, duration, startTime) {
            if (!state.audioContext) return;
            const ctx = state.audioContext;
            const osc = ctx.createOscillator();
            const gain = ctx.createGain();
            osc.type = 'triangle';
            osc.frequency.setValueAtTime(frequency, startTime);
            osc.frequency.exponentialRampToValueAtTime(frequency * 1.45, startTime + duration * 0.45);
            osc.frequency.exponentialRampToValueAtTime(frequency * 0.9, startTime + duration);
            gain.gain.setValueAtTime(0.0001, startTime);
            gain.gain.exponentialRampToValueAtTime(0.08, startTime + 0.03);
            gain.gain.exponentialRampToValueAtTime(0.0001, startTime + duration);
            osc.connect(gain);
            gain.connect(ctx.destination);
            osc.start(startTime);
            osc.stop(startTime + duration + 0.02);
        }

        function toggleMeowMusic() {
            if (!state.audioContext) state.audioContext = new (window.AudioContext || window.webkitAudioContext)();
            if (state.audioContext.state === 'suspended') state.audioContext.resume();
            state.musicEnabled = !state.musicEnabled;
            const label = document.getElementById('meowMusicLabel');
            if (state.musicEnabled) {
                const now = state.audioContext.currentTime + 0.05;
                playMeowTone(520, 0.28, now);
                playMeowTone(660, 0.34, now + 0.32);
                playMeowTone(520, 0.28, now + 0.72);
                label.innerText = '🔊 Música de gatitos activada';
                showNotification('Miau miau 🐱 Música activada.');
            } else {
                label.innerText = 'Activar música de gatitos';
                showNotification('Música de gatitos desactivada.');
            }
            saveUserState();
        }

        // Custom On-Screen Notification Box (Replacing alert)
        function showNotification(message) {
            const notif = document.createElement('div');
            notif.className = 'fixed bottom-6 right-6 bg-emerald-500 text-slate-950 font-bold px-6 py-4 rounded-2xl shadow-2xl z-50 flex items-center space-x-3 transition transform translate-y-10 opacity-0';
            notif.innerHTML = `<i class="fa-solid fa-circle-check text-xl"></i> <span>${message}</span>`;
            document.body.appendChild(notif);

            setTimeout(() => {
                notif.classList.remove('translate-y-10', 'opacity-0');
            }, 10);

            setTimeout(() => {
                notif.classList.add('translate-y-10', 'opacity-0');
                setTimeout(() => notif.remove(), 300);
            }, 4000);
        }
    </script>
</body>
</html>
