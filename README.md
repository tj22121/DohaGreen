# DohaGreen
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>DohaGreen — Qatar Recycling & Waste Drop-Off Locator</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Lucide Icons -->
  <script src="https://unpkg.com/lucide@latest"></script>
  <!-- Google Fonts: Plus Jakarta Sans & Inter -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Plus+Jakarta+Sans:wght@600;700;800&display=swap" rel="stylesheet">

  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
            display: ['Plus Jakarta Sans', 'sans-serif'],
          },
          colors: {
            qycle: {
              50: '#ecfdf5',
              100: '#d1fae5',
              200: '#a7f3d0',
              500: '#10b981',
              600: '#059669',
              700: '#047857',
              800: '#065f46',
              900: '#064e3b',
            },
            maroon: '#8A1538'
          }
        }
      }
    }
  </script>

  <style>
    /* Custom scrollbar hiding for horizontal pill lists */
    .no-scrollbar::-webkit-scrollbar {
      display: none;
    }
    .no-scrollbar {
      -ms-overflow-style: none;
      scrollbar-width: none;
    }

    /* Stylized Doha Map Grid & Water Pattern */
    .doha-map-bg {
      background-color: #eef6f2;
      background-image: 
        radial-gradient(#cbd5e1 1px, transparent 1px),
        linear-gradient(to right, rgba(16, 185, 129, 0.06) 1px, transparent 1px),
        linear-gradient(to bottom, rgba(16, 185, 129, 0.06) 1px, transparent 1px);
      background-size: 24px 24px, 48px 48px, 48px 48px;
    }

    @keyframes pulse-ring {
      0% { transform: scale(0.85); opacity: 0.7; }
      70% { transform: scale(1.45); opacity: 0; }
      100% { transform: scale(1.45); opacity: 0; }
    }
    .pin-pulse::before {
      content: '';
      position: absolute;
      inset: -4px;
      border-radius: 9999px;
      background: currentColor;
      animation: pulse-ring 2s infinite;
      z-index: -1;
    }

    /* Smooth modal transitions */
    .modal-backdrop {
      backdrop-filter: blur(4px);
    }
  </style>
</head>
<body class="bg-slate-100 text-slate-800 antialiased selection:bg-qycle-200 selection:text-qycle-900">

  <!-- App Container: Centered mobile-first shell on desktop, full-bleed on mobile -->
  <div class="min-h-screen max-w-2xl mx-auto bg-slate-50 shadow-2xl border-x border-slate-200/80 flex flex-col relative pb-24">

    <!-- Top Header -->
    <header class="sticky top-0 z-30 bg-white/95 backdrop-blur-md border-b border-slate-200 px-4 py-3">
      <div class="flex items-center justify-between gap-2">
        <!-- Brand Logo -->
        <div class="flex items-center gap-2.5">
          <div class="w-10 h-10 rounded-xl bg-gradient-to-br from-qycle-500 to-qycle-700 flex items-center justify-center text-white shadow-md shadow-qycle-600/20">
            <i data-lucide="recycle" class="w-6 h-6"></i>
          </div>
          <div>
            <div class="flex items-center gap-1.5">
              <h1 class="font-display font-extrabold text-xl tracking-tight text-slate-900 leading-none">DohaGreen</h1>
              <span class="text-[10px] font-bold uppercase tracking-wider px-1.5 py-0.5 rounded bg-qycle-100 text-qycle-800 border border-qycle-200">Doha QA</span>
            </div>
            <p class="text-[11px] text-slate-500 font-medium mt-0.5">5-Min Drop-Offs & Smart Pickup Matcher</p>
          </div>
        </div>

        <!-- Profile Mode Toggle: Household vs Restaurant/Business -->
        <div class="bg-slate-100 p-1 rounded-xl border border-slate-200 flex items-center text-xs font-semibold">
          <button id="mode-household" onclick="setProfileMode('household')" class="px-2.5 py-1.5 rounded-lg transition-all flex items-center gap-1 bg-white text-qycle-700 shadow-sm">
            <i data-lucide="home" class="w-3.5 h-3.5"></i>
            <span>Home</span>
          </button>
          <button id="mode-business" onclick="setProfileMode('business')" class="px-2.5 py-1.5 rounded-lg transition-all flex items-center gap-1 text-slate-600 hover:text-slate-900">
            <i data-lucide="utensils" class="w-3.5 h-3.5"></i>
            <span>Restaurant</span>
          </button>
        </div>
      </div>

      <!-- Restaurant Mode Banner (Shown only in Restaurant/Business mode) -->
      <div id="business-banner" class="hidden mt-2.5 bg-amber-50 border border-amber-200 rounded-xl px-3 py-2 flex items-center justify-between gap-2">
        <div class="flex items-center gap-2 text-xs text-amber-900">
          <i data-lucide="sparkles" class="w-4 h-4 text-amber-600 shrink-0"></i>
          <span><strong>Restaurant & Cafe Mode:</strong> Highlighting surplus food rescue (Wa'hab) & bulk packaging pickups.</span>
        </div>
      </div>
    </header>

    <!-- Main Dynamic Content Area -->
    <main class="flex-1 px-4 pt-4 space-y-5">

      <!-- ========================================================= -->
      <!-- TAB 1: 5-MIN MAP & DROP-OFFS                              -->
      <!-- ========================================================= -->
      <section id="tab-map" class="space-y-4">
        
        <!-- Location & Commute Route Bar -->
        <div class="bg-white rounded-2xl p-3.5 shadow-sm border border-slate-200/80 space-y-3">
          <div class="flex flex-wrap items-center justify-between gap-2">
            <div class="flex items-center gap-2 flex-1 min-w-[180px]">
              <div class="w-8 h-8 rounded-lg bg-qycle-50 text-qycle-600 flex items-center justify-center shrink-0">
                <i data-lucide="map-pin" class="w-4 h-4"></i>
              </div>
              <div class="flex-1">
                <div class="flex items-center justify-between">
                  <label for="area-select" class="block text-[10px] font-bold uppercase tracking-wider text-slate-400">Your Doha Zone</label>
                  <button onclick="detectUserGPSLocation()" class="text-[10px] font-extrabold text-qycle-600 hover:text-qycle-700 flex items-center gap-1 bg-qycle-50 hover:bg-qycle-100 px-2 py-0.5 rounded-md transition">
                    <i data-lucide="crosshair" class="w-3 h-3"></i>
                    <span>Use GPS</span>
                  </button>
                </div>
                <select id="area-select" onchange="handleAreaChange()" class="w-full text-sm font-bold text-slate-800 bg-transparent focus:outline-none cursor-pointer mt-0.5">
                  <option value="Al Waab">Al Waab / Aspire Zone</option>
                  <option value="West Bay">West Bay / Al Dafna</option>
                  <option value="Al Sadd">Al Sadd / Bin Mahmoud</option>
                  <option value="The Pearl">The Pearl-Qatar / Lusail</option>
                  <option value="Al Wakrah">Al Wakrah</option>
                  <option value="Al Rayyan">Al Rayyan / Dahl Al Hamam</option>
                </select>
              </div>
            </div>

            <!-- Add / Report Bin Button -->
            <button onclick="openReportModal()" class="text-xs font-semibold bg-slate-900 hover:bg-slate-800 text-white px-3 py-2 rounded-xl flex items-center gap-1.5 transition shadow-sm">
              <i data-lucide="plus-circle" class="w-3.5 h-3.5 text-qycle-400"></i>
              <span>Add / Report Bin</span>
            </button>
          </div>

          <!-- GPS Status Notice -->
          <div id="gps-status-banner" class="hidden text-[11px] bg-qycle-50 border border-qycle-200 text-qycle-900 px-3 py-2 rounded-xl flex items-center justify-between gap-2">
            <span id="gps-status-text" class="font-medium leading-snug">GPS active: Matched to zone.</span>
            <div class="flex items-center gap-2 shrink-0">
              <button id="gps-demo-btn" onclick="simulateDohaGPS()" class="hidden text-[10px] font-extrabold bg-qycle-600 text-white px-2 py-1 rounded-lg hover:bg-qycle-700 transition">
                Demo GPS
              </button>
              <button onclick="dismissGPSBanner()" class="text-qycle-700 hover:text-qycle-900 font-bold text-sm px-1">&times;</button>
            </div>
          </div>

          <!-- Behavioral Nudge: Route Piggybacking Toggle -->
          <div class="pt-2.5 border-t border-slate-100 flex items-center justify-between gap-2">
            <div class="flex items-center gap-2">
              <span class="inline-flex items-center justify-center w-6 h-6 rounded-full bg-blue-50 text-blue-600">
                <i data-lucide="navigation" class="w-3.5 h-3.5"></i>
              </span>
              <div>
                <p class="text-xs font-semibold text-slate-800">Along My Errand Route</p>
                <p class="text-[11px] text-slate-500">Filter bins inside Al Meera / parks on your daily drive</p>
              </div>
            </div>
            <label class="relative inline-flex items-center cursor-pointer">
              <input type="checkbox" id="route-toggle" onchange="renderMapAndList()" class="sr-only peer">
              <div class="w-10 h-5 bg-slate-200 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-slate-300 after:border after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-qycle-600"></div>
            </label>
          </div>
        </div>

        <!-- Waste Category Filter Chips -->
        <div class="flex items-center gap-2 overflow-x-auto no-scrollbar pb-1" id="map-filter-chips">
          <!-- Populated by JS -->
        </div>

        <!-- Stylized Interactive Doha Radius Map -->
        <div class="relative rounded-2xl overflow-hidden border border-slate-200 shadow-sm doha-map-bg h-64 select-none">
          <!-- Decorative Arabian Gulf Coastline & Doha Roads SVG -->
          <svg class="absolute inset-0 w-full h-full pointer-events-none" viewBox="0 0 600 280" preserveAspectRatio="none">
            <!-- Arabian Gulf Water on East side -->
            <path d="M 470 0 Q 455 70 495 120 T 480 210 T 520 280 L 600 280 L 600 0 Z" fill="#bae6fd" opacity="0.55" />
            <path d="M 470 0 Q 455 70 495 120 T 480 210 T 520 280" fill="none" stroke="#7dd3fc" stroke-width="2" />
            <!-- Major Highways (Salwa Rd, Doha Expressway, Corniche) -->
            <path d="M 0 180 Q 220 165 485 130" fill="none" stroke="#ffffff" stroke-width="7" opacity="0.85" />
            <path d="M 240 0 Q 260 140 230 280" fill="none" stroke="#ffffff" stroke-width="6" opacity="0.85" />
            <path d="M 80 40 Q 290 95 475 75" fill="none" stroke="#ffffff" stroke-width="5" opacity="0.75" />
            <!-- 5-Minute Radius Circle -->
            <circle cx="290" cy="140" r="95" fill="rgba(16, 185, 129, 0.08)" stroke="#10b981" stroke-width="1.5" stroke-dasharray="5,5" />
          </svg>

          <!-- Map Top-Left Overlay Badge -->
          <div class="absolute top-3 left-3 bg-white/95 backdrop-blur px-2.5 py-1.5 rounded-xl shadow-sm border border-slate-200/80 flex items-center gap-1.5">
            <span class="w-2 h-2 rounded-full bg-qycle-500 animate-ping"></span>
            <span class="text-[11px] font-bold text-slate-800" id="map-radius-label">Under 5-Min Radius • Al Waab</span>
          </div>

          <!-- Map Legend Pill -->
          <div class="absolute bottom-2.5 left-3 right-3 flex items-center justify-between pointer-events-none">
            <div class="bg-white/90 backdrop-blur px-2.5 py-1 rounded-lg text-[10px] font-semibold text-slate-600 border border-slate-200/70 shadow-sm">
              Tap any pin for prep checklist & directions
            </div>
            <div class="bg-sky-900/80 text-white px-2 py-0.5 rounded text-[10px] font-semibold">
              Arabian Gulf
            </div>
          </div>

          <!-- Center User Pulse Marker -->
          <div class="absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 z-10 flex flex-col items-center pointer-events-none">
            <div class="w-4 h-4 rounded-full bg-blue-600 border-2 border-white shadow-md relative text-blue-500 pin-pulse"></div>
            <span class="mt-1 px-1.5 py-0.5 rounded bg-slate-900/80 text-white text-[9px] font-bold">You</span>
          </div>

          <!-- Dynamic Interactive Pins Layer -->
          <div id="map-pins-container" class="absolute inset-0 z-20">
            <!-- Pins injected by JS -->
          </div>
        </div>

        <!-- Synchronized Nearby Drop-Off Cards List -->
        <div class="space-y-2.5">
          <div class="flex items-center justify-between">
            <h2 class="font-display font-bold text-base text-slate-900">Nearest Drop-Off Spots (<span id="dropoff-count">0</span>)</h2>
            <span class="text-xs text-slate-500 font-medium">Sorted by travel time</span>
          </div>
          <div id="dropoff-cards-list" class="space-y-3">
            <!-- Cards injected by JS -->
          </div>
        </div>
      </section>


      <!-- ========================================================= -->
      <!-- TAB 2: SMART PICKUP MATCHER & BUILDING TOOLS              -->
      <!-- ========================================================= -->
      <section id="tab-matcher" class="hidden space-y-5">
        
        <!-- Intro Card -->
        <div class="bg-gradient-to-br from-qycle-700 to-qycle-900 rounded-2xl p-4 text-white shadow-md">
          <div class="flex items-start justify-between gap-3">
            <div>
              <span class="inline-block text-[10px] font-bold uppercase tracking-wider bg-white/15 px-2 py-0.5 rounded-md mb-1.5">2-Step Qatar Aggregator</span>
              <h2 class="font-display font-bold text-lg leading-snug">Smart Recycling & Food Pickup Matcher</h2>
              <p class="text-xs text-qycle-100 mt-1 leading-relaxed">
                Don't guess which website covers your Doha zone or whether it's free. Match your batch to the right service in seconds.
              </p>
            </div>
            <div class="w-11 h-11 rounded-2xl bg-white/10 flex items-center justify-center shrink-0">
              <i data-lucide="truck" class="w-6 h-6 text-qycle-200"></i>
            </div>
          </div>
        </div>

        <!-- 2-Step Interactive Filter Box -->
        <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200/80 space-y-4">
          <!-- Step 1 -->
          <div>
            <div class="flex items-center gap-2 mb-2">
              <span class="w-5 h-5 rounded-full bg-qycle-600 text-white text-xs font-bold flex items-center justify-center">1</span>
              <h3 class="text-xs font-bold uppercase tracking-wider text-slate-700">Your Profile & Doha Zone</h3>
            </div>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-2.5">
              <div>
                <label class="block text-[11px] font-medium text-slate-500 mb-1">Property Type</label>
                <select id="matcher-user-type" onchange="renderMatcherResults()" class="w-full rounded-xl border border-slate-200 bg-slate-50 px-3 py-2 text-xs font-semibold text-slate-800 focus:border-qycle-500 focus:outline-none">
                  <option value="household">Household / Compound Villa</option>
                  <option value="apartment">Apartment Tower (Chute Building)</option>
                  <option value="restaurant">Restaurant / Cafe / Hotel</option>
                </select>
              </div>
              <div>
                <label class="block text-[11px] font-medium text-slate-500 mb-1">Zone / Municipality</label>
                <select id="matcher-zone" onchange="renderMatcherResults()" class="w-full rounded-xl border border-slate-200 bg-slate-50 px-3 py-2 text-xs font-semibold text-slate-800 focus:border-qycle-500 focus:outline-none">
                  <option value="All Doha">Doha (West Bay, Al Waab, Al Sadd)</option>
                  <option value="The Pearl">The Pearl-Qatar & Lusail</option>
                  <option value="Al Rayyan">Al Rayyan Municipality</option>
                  <option value="Al Wakrah">Al Wakrah Municipality</option>
                </select>
              </div>
            </div>
          </div>

          <!-- Step 2 -->
          <div class="pt-3 border-t border-slate-100">
            <div class="flex items-center gap-2 mb-2">
              <span class="w-5 h-5 rounded-full bg-qycle-600 text-white text-xs font-bold flex items-center justify-center">2</span>
              <h3 class="text-xs font-bold uppercase tracking-wider text-slate-700">Waste Category & Batch Size</h3>
            </div>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-2.5">
              <div>
                <label class="block text-[11px] font-medium text-slate-500 mb-1">What are you recycling?</label>
                <select id="matcher-waste" onchange="renderMatcherResults()" class="w-full rounded-xl border border-slate-200 bg-slate-50 px-3 py-2 text-xs font-semibold text-slate-800 focus:border-qycle-500 focus:outline-none">
                  <option value="all">All Recyclables (Mixed)</option>
                  <option value="paper">Paper & Cardboard Boxes</option>
                  <option value="plastic">Plastic Bottles & Aluminum Cans</option>
                  <option value="ewaste">E-Waste & Old Batteries</option>
                  <option value="food_surplus">Edible Surplus Food (Unserved)</option>
                  <option value="food_scraps">Kitchen Food Scraps / Organic</option>
                </select>
              </div>
              <div>
                <label class="block text-[11px] font-medium text-slate-500 mb-1">How much do you have?</label>
                <div class="grid grid-cols-2 gap-1.5 bg-slate-100 p-1 rounded-xl border border-slate-200">
                  <button type="button" id="batch-small" onclick="setBatchSize('small')" class="py-1.5 px-2 rounded-lg text-[11px] font-bold bg-white text-qycle-700 shadow-sm transition">
                    Small Bag
                  </button>
                  <button type="button" id="batch-bulk" onclick="setBatchSize('bulk')" class="py-1.5 px-2 rounded-lg text-[11px] font-bold text-slate-600 hover:text-slate-900 transition">
                    Bulk / Commercial
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Matched Qatar Services Output -->
        <div class="space-y-3">
          <div class="flex items-center justify-between">
            <h3 class="font-display font-bold text-base text-slate-900">Best Matches for You (<span id="matcher-count">0</span>)</h3>
            <span class="text-[11px] font-semibold text-qycle-700 bg-qycle-50 px-2 py-0.5 rounded-md border border-qycle-200">Verified Qatar Services</span>
          </div>
          <div id="matcher-results-list" class="space-y-3">
            <!-- Service cards injected by JS -->
          </div>
        </div>

        <!-- Behavioral Design Section: Beat the Trash Chute -->
        <div class="bg-slate-900 text-white rounded-2xl p-4 shadow-md space-y-3">
          <div class="flex items-start gap-3">
            <div class="w-10 h-10 rounded-xl bg-amber-500/20 border border-amber-400/30 text-amber-300 flex items-center justify-center shrink-0">
              <i data-lucide="building-2" class="w-5 h-5"></i>
            </div>
            <div>
              <span class="text-[10px] font-bold uppercase tracking-wider text-amber-300">Behavioral Design Feature</span>
              <h3 class="font-display font-bold text-base">Live in a Trash-Chute Tower?</h3>
              <p class="text-xs text-slate-300 mt-0.5 leading-relaxed">
                Don't walk outside in the heat for a single bag. Team up with neighbors or ask your facility manager to add a lobby recycling bin.
              </p>
            </div>
          </div>

          <div class="grid grid-cols-1 sm:grid-cols-2 gap-2.5 pt-1">
            <button onclick="openPetitionModal()" class="bg-white text-slate-900 hover:bg-slate-100 font-bold text-xs py-2.5 px-3 rounded-xl flex items-center justify-center gap-2 transition">
              <i data-lucide="file-text" class="w-4 h-4 text-qycle-600"></i>
              <span>Request Lobby Bin Template</span>
            </button>
            <button onclick="openPosterModal()" class="bg-slate-800 hover:bg-slate-700 border border-slate-700 text-white font-bold text-xs py-2.5 px-3 rounded-xl flex items-center justify-center gap-2 transition">
              <i data-lucide="qr-code" class="w-4 h-4 text-amber-400"></i>
              <span>Chute-Side QR Poster</span>
            </button>
          </div>
        </div>

      </section>


      <!-- ========================================================= -->
      <!-- TAB 3: "CAN I RECYCLE THIS?" INSTANT SEARCH               -->
      <!-- ========================================================= -->
      <section id="tab-guide" class="hidden space-y-4">
        
        <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200/80 space-y-3">
          <div>
            <h2 class="font-display font-bold text-lg text-slate-900">Can I Recycle This in Qatar?</h2>
            <p class="text-xs text-slate-500 mt-0.5">Prevent bin contamination—check local rules before dropping off.</p>
          </div>

          <!-- Instant Search Input -->
          <div class="relative">
            <i data-lucide="search" class="w-4 h-4 text-slate-400 absolute left-3.5 top-1/2 -translate-y-1/2"></i>
            <input 
              type="text" 
              id="item-search-input" 
              oninput="renderGuideItems()" 
              placeholder="Try 'karak cup', 'pizza box', 'crushed bottle', 'batteries'..." 
              class="w-full pl-10 pr-9 py-2.5 rounded-xl bg-slate-50 border border-slate-200 text-sm text-slate-800 placeholder:text-slate-400 focus:bg-white focus:border-qycle-500 focus:outline-none transition"
            />
            <button id="clear-search-btn" onclick="clearSearch()" class="hidden absolute right-3 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-600">
              <i data-lucide="x-circle" class="w-4 h-4"></i>
            </button>
          </div>

          <!-- Status Filter Chips -->
          <div class="flex items-center gap-1.5 overflow-x-auto no-scrollbar pt-1" id="guide-filter-chips">
            <!-- Injected by JS -->
          </div>
        </div>

        <!-- Guide Results List -->
        <div id="guide-items-list" class="space-y-2.5">
          <!-- Injected by JS -->
        </div>

      </section>


      <!-- ========================================================= -->
      <!-- TAB 4: MY IMPACT & PORTFOLIO CASE STUDY                   -->
      <!-- ========================================================= -->
      <section id="tab-impact" class="hidden space-y-4">
        
        <!-- Personal Impact Hero Card -->
        <div class="bg-gradient-to-br from-slate-900 via-slate-800 to-qycle-950 text-white rounded-2xl p-5 shadow-md space-y-4">
          <div class="flex items-center justify-between">
            <div>
              <span class="text-[10px] font-bold uppercase tracking-wider text-qycle-400">Your Doha Eco-Impact</span>
              <h2 class="font-display font-extrabold text-xl mt-0.5">Keep Qatar Clean & Green</h2>
            </div>
            <div class="px-2.5 py-1 rounded-full bg-amber-500/20 border border-amber-400/30 text-amber-300 text-xs font-bold flex items-center gap-1">
              <i data-lucide="flame" class="w-3.5 h-3.5"></i>
              <span id="stat-streak">4 Day Streak</span>
            </div>
          </div>

          <!-- 3 Key Metrics -->
          <div class="grid grid-cols-3 gap-2.5">
            <div class="bg-white/10 backdrop-blur rounded-xl p-3 border border-white/10">
              <p class="text-[11px] text-slate-300">Diverted</p>
              <p class="font-display font-extrabold text-xl text-qycle-300 mt-0.5"><span id="stat-kg">14.5</span> <span class="text-xs font-semibold">kg</span></p>
            </div>
            <div class="bg-white/10 backdrop-blur rounded-xl p-3 border border-white/10">
              <p class="text-[11px] text-slate-300">Drop-Offs</p>
              <p class="font-display font-extrabold text-xl text-white mt-0.5" id="stat-drops">7</p>
            </div>
            <div class="bg-white/10 backdrop-blur rounded-xl p-3 border border-white/10">
              <p class="text-[11px] text-slate-300">Est. Rewards</p>
              <p class="font-display font-extrabold text-xl text-amber-300 mt-0.5"><span id="stat-qar">18</span> <span class="text-xs font-semibold">QAR</span></p>
            </div>
          </div>

          <!-- Quick "I Recycled Today" Interactive Logger -->
          <div class="bg-white/5 rounded-xl p-3 border border-white/10 space-y-2.5">
            <p class="text-xs font-bold text-slate-200 flex items-center gap-1.5">
              <i data-lucide="plus-square" class="w-4 h-4 text-qycle-400"></i>
              <span>Quick Log: I Recycled Today</span>
            </p>
            <div class="flex flex-wrap sm:flex-nowrap gap-2">
              <select id="log-type-select" class="flex-1 rounded-xl bg-slate-800 border border-slate-700 px-3 py-2 text-xs font-semibold text-white focus:outline-none focus:border-qycle-400">
                <option value="2.0|3">1 Bag Plastic Bottles / Cans (+2 kg / ~3 QAR pts)</option>
                <option value="3.5|4">Flattened Cardboard Stack (+3.5 kg / ~4 QAR)</option>
                <option value="1.5|5">E-Waste / Old Cables (+1.5 kg / ~5 QAR)</option>
                <option value="4.0|0">Surplus Food / Compost Batch (+4 kg diverted)</option>
              </select>
              <button onclick="logQuickRecycle()" class="w-full sm:w-auto bg-qycle-500 hover:bg-qycle-400 text-slate-950 font-extrabold text-xs px-4 py-2 rounded-xl transition flex items-center justify-center gap-1.5 shadow-sm">
                <i data-lucide="check" class="w-4 h-4"></i>
                <span>Log Batch</span>
              </button>
            </div>
          </div>
        </div>

        <!-- Compound & Neighborhood Leaderboard -->
        <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200/80 space-y-3">
          <div class="flex items-center justify-between">
            <div>
              <h3 class="font-display font-bold text-base text-slate-900">Doha Compound & Tower Leaderboard</h3>
              <p class="text-xs text-slate-500">Community diversion rankings this month</p>
            </div>
            <i data-lucide="trophy" class="w-5 h-5 text-amber-500"></i>
          </div>

          <div id="leaderboard-list" class="space-y-2">
            <!-- Populated by JS -->
          </div>
        </div>

        <!-- Student Portfolio Case Study Summary Card -->
        <div class="bg-white rounded-2xl border border-slate-200/80 shadow-sm overflow-hidden">
          <button onclick="togglePortfolioCard()" class="w-full p-4 flex items-center justify-between text-left hover:bg-slate-50 transition">
            <div class="flex items-center gap-2.5">
              <div class="w-8 h-8 rounded-lg bg-maroon/10 text-maroon flex items-center justify-center">
                <i data-lucide="graduation-cap" class="w-4 h-4"></i>
              </div>
              <div>
                <h3 class="font-display font-bold text-sm text-slate-900">About This Student Portfolio Project</h3>
                <p class="text-[11px] text-slate-500">Qatar National Vision 2030 & Field Research Notes</p>
              </div>
            </div>
            <i data-lucide="chevron-down" id="portfolio-chevron" class="w-4 h-4 text-slate-400 transition-transform"></i>
          </button>

          <div id="portfolio-details" class="px-4 pb-4 pt-1 border-t border-slate-100 text-xs text-slate-600 space-y-2.5 leading-relaxed">
            <p>
              <strong>Mission:</strong> Built by a 14-year-old student developer in Doha to bridge the gap between Qatar's expanding recycling infrastructure (<strong>Qatar National Vision 2030</strong> &amp; <strong>TASMU Smart Qatar</strong>) and everyday residents and restaurants.
            </p>
            <p>
              <strong>Key Research Insights Solved:</strong>
            </p>
            <ul class="list-disc pl-4 space-y-1 text-slate-600">
              <li><strong>Visibility &lt; 5 Mins:</strong> Maps public Eco-Domes, Al Meera RVMs, and Baladiya blue bins along daily errand routes.</li>
              <li><strong>Cost Myth Busting:</strong> Highlights that services like Tharwah and Al Meera RVMs actually <em>pay cash or reward points</em> rather than charging fees.</li>
              <li><strong>Restaurant Food Rescue:</strong> Separates edible surplus redistribution (Wa'hab) from organic waste and commercial packaging.</li>
            </ul>
            <div class="p-2.5 rounded-xl bg-slate-50 border border-slate-200/70 text-[11px] text-slate-500">
              <strong>Disclaimer:</strong> Independent student prototype directory. External service names (Tharwah, Elite Paper Recycling, Wa'hab, Al Meera, Baladiya) belong to their respective owners and link to official public portals.
            </div>
          </div>
        </div>

      </section>

    </main>

    <!-- Bottom Navigation Bar (4 Core Tabs) -->
    <nav class="fixed bottom-0 left-1/2 -translate-x-1/2 w-full max-w-2xl bg-white/95 backdrop-blur-md border-t border-slate-200 px-2 py-2 z-40">
      <div class="grid grid-cols-4 gap-1">
        <button onclick="switchTab('map')" id="nav-map" class="nav-btn flex flex-col items-center justify-center py-1.5 px-1 rounded-xl text-qycle-700 bg-qycle-50 font-bold transition">
          <i data-lucide="map" class="w-5 h-5 mb-0.5"></i>
          <span class="text-[11px]">5-Min Map</span>
        </button>
        <button onclick="switchTab('matcher')" id="nav-matcher" class="nav-btn flex flex-col items-center justify-center py-1.5 px-1 rounded-xl text-slate-500 hover:text-slate-900 font-semibold transition">
          <i data-lucide="sparkles" class="w-5 h-5 mb-0.5"></i>
          <span class="text-[11px]">Pickups</span>
        </button>
        <button onclick="switchTab('guide')" id="nav-guide" class="nav-btn flex flex-col items-center justify-center py-1.5 px-1 rounded-xl text-slate-500 hover:text-slate-900 font-semibold transition">
          <i data-lucide="help-circle" class="w-5 h-5 mb-0.5"></i>
          <span class="text-[11px]">Can I Recycle?</span>
        </button>
        <button onclick="switchTab('impact')" id="nav-impact" class="nav-btn flex flex-col items-center justify-center py-1.5 px-1 rounded-xl text-slate-500 hover:text-slate-900 font-semibold transition">
          <i data-lucide="award" class="w-5 h-5 mb-0.5"></i>
          <span class="text-[11px]">My Impact</span>
        </button>
      </div>
    </nav>


    <!-- ========================================================= -->
    <!-- MODAL 1: DROP-OFF LOCATION DETAIL & PREP CHECKLIST        -->
    <!-- ========================================================= -->
    <div id="dropoff-modal" class="hidden fixed inset-0 z-50 bg-slate-900/60 modal-backdrop flex items-end sm:items-center justify-center p-0 sm:p-4">
      <div class="bg-white w-full max-w-lg rounded-t-3xl sm:rounded-3xl p-5 shadow-2xl space-y-4 max-h-[90vh] overflow-y-auto">
        <div class="flex items-start justify-between gap-3">
          <div>
            <div class="flex items-center gap-2 mb-1" id="modal-badges"></div>
            <h3 class="font-display font-extrabold text-lg text-slate-900" id="modal-title">Location Name</h3>
            <p class="text-xs text-slate-500 flex items-center gap-1 mt-0.5" id="modal-address">
              <i data-lucide="map-pin" class="w-3.5 h-3.5 text-qycle-600"></i>
              <span>Area, Doha</span>
            </p>
          </div>
          <button onclick="closeModal('dropoff-modal')" class="p-2 rounded-full bg-slate-100 text-slate-500 hover:text-slate-800">
            <i data-lucide="x" class="w-5 h-5"></i>
          </button>
        </div>

        <!-- Accepted Materials -->
        <div class="bg-slate-50 rounded-2xl p-3 border border-slate-200/80">
          <p class="text-[11px] font-bold uppercase tracking-wider text-slate-500 mb-1.5">Accepted Materials</p>
          <p class="text-xs font-semibold text-slate-800" id="modal-accepted">Paper, Cardboard</p>
        </div>

        <!-- 3-Bullet Before You Drop Off Checklist -->
        <div class="bg-qycle-50/70 rounded-2xl p-3.5 border border-qycle-200 space-y-2">
          <p class="text-xs font-extrabold text-qycle-900 flex items-center gap-1.5">
            <i data-lucide="clipboard-check" class="w-4 h-4 text-qycle-600"></i>
            <span>Before You Drop Off (Prevent Contamination)</span>
          </p>
          <ul id="modal-checklist" class="space-y-1.5 text-xs text-qycle-950">
            <!-- Injected by JS -->
          </ul>
        </div>

        <!-- Action Buttons -->
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-2.5 pt-1">
          <a id="modal-gmaps-link" href="#" target="_blank" rel="noopener noreferrer" class="bg-slate-900 hover:bg-slate-800 text-white font-bold text-xs py-3 px-4 rounded-xl flex items-center justify-center gap-2 transition">
            <i data-lucide="navigation" class="w-4 h-4 text-qycle-400"></i>
            <span>Open in Google Maps</span>
          </a>
          <button id="modal-log-btn" onclick="logFromModal()" class="bg-qycle-600 hover:bg-qycle-500 text-white font-bold text-xs py-3 px-4 rounded-xl flex items-center justify-center gap-2 transition">
            <i data-lucide="check-circle-2" class="w-4 h-4"></i>
            <span>+1 Log Drop-Off Here</span>
          </button>
        </div>
      </div>
    </div>


    <!-- ========================================================= -->
    <!-- MODAL 2: CROWDSOURCED "ADD / REPORT A BIN"                -->
    <!-- ========================================================= -->
    <div id="report-modal" class="hidden fixed inset-0 z-50 bg-slate-900/60 modal-backdrop flex items-end sm:items-center justify-center p-0 sm:p-4">
      <div class="bg-white w-full max-w-lg rounded-t-3xl sm:rounded-3xl p-5 shadow-2xl space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h3 class="font-display font-extrabold text-lg text-slate-900">Add or Report a Doha Bin</h3>
            <p class="text-xs text-slate-500">Help keep the community map accurate across Qatar.</p>
          </div>
          <button onclick="closeModal('report-modal')" class="p-2 rounded-full bg-slate-100 text-slate-500 hover:text-slate-800">
            <i data-lucide="x" class="w-5 h-5"></i>
          </button>
        </div>

        <form onsubmit="submitNewBin(event)" class="space-y-3">
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Spot Name / Landmark</label>
            <input type="text" id="new-bin-name" required placeholder="e.g., Elite Paper Cage near Al Meera Legtaifiya" class="w-full rounded-xl border border-slate-200 px-3 py-2 text-xs font-medium focus:border-qycle-500 focus:outline-none" />
          </div>
          <div class="grid grid-cols-2 gap-2.5">
            <div>
              <label class="block text-xs font-bold text-slate-700 mb-1">Doha Area</label>
              <select id="new-bin-area" class="w-full rounded-xl border border-slate-200 px-3 py-2 text-xs font-medium focus:border-qycle-500 focus:outline-none">
                <option value="Al Waab">Al Waab / Aspire</option>
                <option value="West Bay">West Bay / Dafna</option>
                <option value="Al Sadd">Al Sadd / Bin Mahmoud</option>
                <option value="The Pearl">The Pearl / Lusail</option>
                <option value="Al Wakrah">Al Wakrah</option>
                <option value="Al Rayyan">Al Rayyan</option>
              </select>
            </div>
            <div>
              <label class="block text-xs font-bold text-slate-700 mb-1">Bin / Service Type</label>
              <select id="new-bin-category" class="w-full rounded-xl border border-slate-200 px-3 py-2 text-xs font-medium focus:border-qycle-500 focus:outline-none">
                <option value="paper">Paper & Cardboard Cage</option>
                <option value="plastic">Plastic / Cans RVM</option>
                <option value="municipal">Municipal Blue Bin</option>
                <option value="ewaste">E-Waste / Battery Bin</option>
                <option value="food">Surplus Food Hub</option>
              </select>
            </div>
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Condition / Prep Tip</label>
            <input type="text" id="new-bin-note" placeholder="e.g., Located by North parking entrance, clean & empty" class="w-full rounded-xl border border-slate-200 px-3 py-2 text-xs font-medium focus:border-qycle-500 focus:outline-none" />
          </div>
          <button type="submit" class="w-full bg-qycle-600 hover:bg-qycle-500 text-white font-bold text-xs py-3 rounded-xl transition shadow-sm">
            Pin to Live Qycle Map
          </button>
        </form>
      </div>
    </div>


    <!-- ========================================================= -->
    <!-- MODAL 3: LOBBY RECYCLING BIN PETITION TEMPLATE            -->
    <!-- ========================================================= -->
    <div id="petition-modal" class="hidden fixed inset-0 z-50 bg-slate-900/60 modal-backdrop flex items-end sm:items-center justify-center p-0 sm:p-4">
      <div class="bg-white w-full max-w-lg rounded-t-3xl sm:rounded-3xl p-5 shadow-2xl space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h3 class="font-display font-extrabold text-lg text-slate-900">Request a Lobby Recycling Bin</h3>
            <p class="text-xs text-slate-500">Send this ready-to-use WhatsApp/Email message to your building manager.</p>
          </div>
          <button onclick="closeModal('petition-modal')" class="p-2 rounded-full bg-slate-100 text-slate-500 hover:text-slate-800">
            <i data-lucide="x" class="w-5 h-5"></i>
          </button>
        </div>

        <div class="bg-slate-50 border border-slate-200 rounded-2xl p-3.5 text-xs text-slate-700 leading-relaxed whitespace-pre-line font-medium" id="petition-text">Hello Building Management,

Several residents in our building would love to recycle paper, cardboard, and plastic bottles instead of sending them down the general garbage chute.

Could we please place a dedicated recycling bin in the lobby or basement parking? Organizations like Elite Paper Recycling (4496 5000) and Tharwah provide free collection for buildings in Doha, so there is zero extra cost to management.

Thank you for helping keep our community sustainable!</div>

        <button onclick="copyPetitionText()" class="w-full bg-qycle-600 hover:bg-qycle-500 text-white font-bold text-xs py-3 rounded-xl flex items-center justify-center gap-2 transition">
          <i data-lucide="copy" class="w-4 h-4"></i>
          <span>Copy Message for WhatsApp / Email</span>
        </button>
      </div>
    </div>


    <!-- ========================================================= -->
    <!-- MODAL 4: CHUTE-SIDE QR POSTER PREVIEW                     -->
    <!-- ========================================================= -->
    <div id="poster-modal" class="hidden fixed inset-0 z-50 bg-slate-900/60 modal-backdrop flex items-end sm:items-center justify-center p-0 sm:p-4">
      <div class="bg-white w-full max-w-md rounded-t-3xl sm:rounded-3xl p-5 shadow-2xl space-y-4">
        <div class="flex items-center justify-between">
          <h3 class="font-display font-extrabold text-base text-slate-900">Printable Chute-Room Poster</h3>
          <button onclick="closeModal('poster-modal')" class="p-2 rounded-full bg-slate-100 text-slate-500 hover:text-slate-800">
            <i data-lucide="x" class="w-5 h-5"></i>
          </button>
        </div>

        <!-- Poster Mockup -->
        <div class="border-4 border-qycle-600 rounded-2xl p-5 bg-qycle-50 text-center space-y-3">
          <span class="inline-block px-3 py-1 rounded-full bg-amber-500 text-white text-[11px] font-extrabold uppercase tracking-wider">
            Hold On Before Using the Chute!
          </span>
          <h4 class="font-display font-extrabold text-xl text-slate-900 leading-tight">
            Got Cardboard, Plastic Bottles, or Old Electronics?
          </h4>
          <p class="text-xs text-slate-700 font-medium">
            Don't trash it—earn <strong>Meera Rewards points</strong> or <strong>cash pickup</strong> within 5 minutes of this building!
          </p>
          <div class="w-28 h-28 mx-auto bg-white p-2.5 rounded-2xl border-2 border-slate-900 flex flex-col items-center justify-center shadow-sm">
            <i data-lucide="qr-code" class="w-16 h-16 text-slate-900"></i>
            <span class="text-[9px] font-extrabold uppercase tracking-wider text-qycle-700 mt-1">Scan DohaGreen</span>
          </div>
          <p class="text-[11px] font-bold text-qycle-900">
            DohaGreen • Free Community Recycling Locator
          </p>
        </div>

        <button onclick="showToast('Poster saved! Share with your building facility manager.')" class="w-full bg-slate-900 hover:bg-slate-800 text-white font-bold text-xs py-3 rounded-xl flex items-center justify-center gap-2 transition">
          <i data-lucide="download" class="w-4 h-4 text-qycle-400"></i>
          <span>Save Poster Image (PDF / PNG)</span>
        </button>
      </div>
    </div>

    <!-- ========================================================= -->
    <!-- MODAL 5: FIRST-TIME GPS PERMISSION WELCOME PROMPT         -->
    <!-- ========================================================= -->
    <div id="first-visit-gps-modal" class="hidden fixed inset-0 z-50 bg-slate-900/75 modal-backdrop flex items-center justify-center p-4">
      <div class="bg-white w-full max-w-sm rounded-3xl p-6 shadow-2xl text-center space-y-4 border border-slate-100">
        <div class="w-16 h-16 mx-auto rounded-2xl bg-gradient-to-br from-qycle-500 to-qycle-700 text-white flex items-center justify-center shadow-lg shadow-qycle-600/25">
          <i data-lucide="map-pin" class="w-8 h-8"></i>
        </div>

        <div class="space-y-1.5">
          <span class="inline-block text-[10px] font-extrabold uppercase tracking-wider px-2.5 py-0.5 rounded-full bg-qycle-100 text-qycle-800">
            Welcome to DohaGreen
          </span>
          <h3 class="font-display font-extrabold text-xl text-slate-900 leading-tight">
            Enable GPS Location?
          </h3>
          <p class="text-xs text-slate-600 leading-relaxed">
            Allow <strong>DohaGreen</strong> to access your device's GPS so we can automatically detect your Doha neighborhood and show recycling drop-offs within 5 minutes of you.
          </p>
        </div>

        <div class="bg-slate-50 rounded-xl p-2.5 border border-slate-200/70 text-[11px] text-slate-500 flex items-center justify-center gap-1.5">
          <i data-lucide="shield-check" class="w-4 h-4 text-qycle-600 shrink-0"></i>
          <span>Your location stays private on your device.</span>
        </div>

        <div class="space-y-2 pt-1">
          <button onclick="handleFirstVisitGPSChoice(true)" class="w-full bg-qycle-600 hover:bg-qycle-500 text-white font-bold text-xs py-3.5 px-4 rounded-xl flex items-center justify-center gap-2 transition shadow-md shadow-qycle-600/20">
            <i data-lucide="crosshair" class="w-4 h-4"></i>
            <span>Yes, Enable GPS Location</span>
          </button>
          <button onclick="handleFirstVisitGPSChoice(false)" class="w-full bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-xs py-3 px-4 rounded-xl transition">
            No Thanks, I'll Choose My Area Manually
          </button>
        </div>
      </div>
    </div>

    <!-- Floating Toast Notification -->
    <div id="toast" class="hidden fixed top-20 left-1/2 -translate-x-1/2 z-50 bg-slate-900 text-white px-4 py-2.5 rounded-full shadow-xl text-xs font-bold flex items-center gap-2 border border-slate-700">
      <i data-lucide="check-circle" class="w-4 h-4 text-qycle-400"></i>
      <span id="toast-msg">Action completed!</span>
    </div>

  </div>

  <!-- ========================================================= -->
  <!-- APPLICATION DATA & INTERACTIVE LOGIC                      -->
  <!-- ========================================================= -->
  <script>
    // 1. STATE MANAGEMENT
    let currentTab = 'map';
    let profileMode = 'household'; // 'household' | 'business'
    let selectedMapCategory = 'all';
    let selectedBatchSize = 'small'; // 'small' | 'bulk'
    let selectedGuideFilter = 'all';
    let activeDropoffModalItem = null;
    let userCoords = null; // Stores GPS {lat, lng} if granted

    let userStats = {
      kg: 14.5,
      drops: 7,
      qar: 18,
      streak: 4
    };

    // 2. SAMPLE DOHA DROP-OFF LOCATIONS DATASET WITH LAT/LNG FOR REAL GPS DISTANCE
    const dropoffLocations = [
      {
        id: 1,
        name: "Elite Paper Recycling Eco-Dome",
        area: "Al Rayyan",
        landmark: "Dahl Al Hamam Park (North Gate)",
        category: "paper",
        categoryLabel: "Paper & Cardboard",
        timeLabel: "3 min drive",
        minutes: 3,
        lat: 25.3352, lng: 51.4878,
        alongRoute: true,
        businessFriendly: true,
        badge: "100% FREE",
        badgeColor: "blue",
        x: 36, y: 34,
        accepted: "Cardboard boxes, newspapers, magazines, school notebooks, clean paper cartons.",
        checklist: [
          "Flatten all cardboard boxes so they fit through the Eco-Dome slot.",
          "Keep paper completely dry—wet or oily cardboard cannot be pulped.",
          "Remove plastic bubble wrap or styrofoam inserts before dropping."
        ],
        mapsUrl: "https://www.google.com/maps/search/?api=1&query=Dahl+Al+Hamam+Park+Doha"
      },
      {
        id: 2,
        name: "Al Meera Reverse Vending Machine (RVM)",
        area: "Al Waab",
        landmark: "Al Meera Hypermarket — Al Waab Branch",
        category: "plastic",
        categoryLabel: "Plastic & Cans",
        timeLabel: "2 min drive",
        minutes: 2,
        lat: 25.2635, lng: 51.4648,
        alongRoute: true,
        businessFriendly: false,
        badge: "EARNS POINTS",
        badgeColor: "green",
        x: 32, y: 66,
        accepted: "Empty plastic water/soda bottles (PET) and aluminum beverage cans.",
        checklist: [
          "Do NOT crush bottles or cans—the machine must scan the shape and barcode.",
          "Empty all liquids completely before inserting into the RVM chute.",
          "Enter your Meera Rewards mobile number on screen to collect points."
        ],
        mapsUrl: "https://www.google.com/maps/search/?api=1&query=Al+Meera+Al+Waab+Doha"
      },
      {
        id: 3,
        name: "Baladiya Blue & Grey Sorting Station",
        area: "Al Sadd",
        landmark: "Al Sadd Metro Plaza / Suhaim Bin Hamad St",
        category: "municipal",
        categoryLabel: "Municipal Blue Bins",
        timeLabel: "4 min walk",
        minutes: 4,
        lat: 25.2885, lng: 51.5065,
        alongRoute: true,
        businessFriendly: true,
        badge: "100% FREE",
        badgeColor: "blue",
        x: 52, y: 54,
        accepted: "Blue Bin: Clean plastic, metal tins, glass jars, and paper. Grey Bin: General waste.",
        checklist: [
          "Quickly rinse food residue from yogurt tubs, tins, or glass jars.",
          "Never put leftover shawarma, rice, or wet tissues in the Blue Bin.",
          "Tie bags securely and close the bin lid to protect from wind."
        ],
        mapsUrl: "https://www.google.com/maps/search/?api=1&query=Al+Sadd+Metro+Station+Doha"
      },
      {
        id: 4,
        name: "Al Meera E-Waste & Battery Drop-Off",
        area: "West Bay",
        landmark: "Al Meera — West Bay / Legtaifiya",
        category: "ewaste",
        categoryLabel: "E-Waste & Batteries",
        timeLabel: "4 min drive",
        minutes: 4,
        lat: 25.3284, lng: 51.5282,
        alongRoute: true,
        businessFriendly: true,
        badge: "100% FREE",
        badgeColor: "blue",
        x: 68, y: 30,
        accepted: "AA/AAA batteries, old smartphones, chargers, cables, small routers, and laptops.",
        checklist: [
          "Tape the ends of lithium or 9V batteries to prevent sparks.",
          "Sign out of iCloud/Google and factory-reset old phones before dropping.",
          "Do not drop broken CRT monitors or leaking car batteries here."
        ],
        mapsUrl: "https://www.google.com/maps/search/?api=1&query=Al+Meera+West+Bay+Doha"
      },
      {
        id: 5,
        name: "Wa'hab Surplus Food Coordination Point",
        area: "Al Sadd",
        landmark: "Central Doha Pickup & Drop Hub",
        category: "food",
        categoryLabel: "Surplus Food",
        timeLabel: "5 min drive",
        minutes: 5,
        lat: 25.2795, lng: 51.5112,
        alongRoute: false,
        businessFriendly: true,
        badge: "FREE RESCUE",
        badgeColor: "green",
        x: 47, y: 42,
        accepted: "Safe, untouched, edible surplus meals from restaurants, catering, events & bakeries.",
        checklist: [
          "Must be unserved, hygienically stored food—not plate leftovers.",
          "Pack hot and cold items separately in sealed food-grade trays.",
          "Message Wa'hab on WhatsApp (+974 5052 0220) 2 hours before closing."
        ],
        mapsUrl: "https://www.google.com/maps/search/?api=1&query=Wahab+Qatar+Doha"
      },
      {
        id: 6,
        name: "The Pearl Eco-Cage & RVM Station",
        area: "The Pearl",
        landmark: "Medina Centrale — Basement Parking B1",
        category: "plastic",
        categoryLabel: "Plastic & Cans",
        timeLabel: "3 min walk",
        minutes: 3,
        lat: 25.3672, lng: 51.5489,
        alongRoute: true,
        businessFriendly: true,
        badge: "EARNS POINTS",
        badgeColor: "green",
        x: 75, y: 18,
        accepted: "Plastic bottles, aluminum cans, and flattened delivery cardboard boxes.",
        checklist: [
          "Keep barcodes readable and bottles uncrushed for RVM scanning.",
          "Break down delivery cartons flat before placing in the paper cage.",
          "Keep bottle caps on so small plastics don't get lost during sorting."
        ],
        mapsUrl: "https://www.google.com/maps/search/?api=1&query=Medina+Centrale+The+Pearl+Qatar"
      },
      {
        id: 7,
        name: "Al Wakrah Souq & Park Paper Cage",
        area: "Al Wakrah",
        landmark: "Al Wakrah Public Park Entrance",
        category: "paper",
        categoryLabel: "Paper & Cardboard",
        timeLabel: "3 min drive",
        minutes: 3,
        lat: 25.1743, lng: 51.6078,
        alongRoute: true,
        businessFriendly: true,
        badge: "100% FREE",
        badgeColor: "blue",
        x: 64, y: 82,
        accepted: "Cardboard packaging, flyers, newspapers, and clean office paper.",
        checklist: [
          "Flatten boxes completely to maximize cage space for neighbors.",
          "Ensure no food grease or liquids are soaked into the cardboard.",
          "Available 24/7 with easy curbside pull-up parking."
        ],
        mapsUrl: "https://www.google.com/maps/search/?api=1&query=Al+Wakrah+Public+Park+Qatar"
      }
    ];

    // PICKUP SERVICES DATASET
    const pickupServices = [
      {
        id: 1,
        name: "Tharwah Doorstep Pickup App",
        userTypes: ["household", "apartment", "restaurant"],
        zones: ["All Doha", "The Pearl", "Al Rayyan", "Al Wakrah"],
        wastes: ["all", "paper", "plastic", "ewaste"],
        batches: ["small", "bulk"],
        badge: "EARNS CASH / POINTS",
        secondaryBadge: "Doorstep App",
        badgeStyle: "bg-emerald-100 text-emerald-800 border-emerald-200",
        minRule: "No strict minimums; weighed at your door for cash/rewards.",
        prepNote: "Separate plastics, paper, and e-waste into bags before driver arrives.",
        actionLabel: "Open Tharwah App",
        website: "https://tharwah.qa",
        whatsapp: "https://wa.me/97444000000",
        poolEligible: true
      },
      {
        id: 2,
        name: "Elite Paper Recycling (Bulk Collection)",
        userTypes: ["household", "apartment", "restaurant"],
        zones: ["All Doha", "The Pearl", "Al Rayyan", "Al Wakrah"],
        wastes: ["paper", "all"],
        batches: ["bulk", "small"],
        badge: "100% FREE",
        secondaryBadge: "Paper & Cartons",
        badgeStyle: "bg-blue-100 text-blue-800 border-blue-200",
        minRule: "Free doorstep truck pickup for large stacks of flattened cardboard or office paper.",
        prepNote: "Flatten all boxes completely and keep dry.",
        actionLabel: "Visit EPR Website",
        website: "https://epr.qa",
        whatsapp: "https://wa.me/97444965000",
        poolEligible: true
      },
      {
        id: 3,
        name: "Wa'hab Surplus Food Rescue",
        userTypes: ["restaurant", "household"],
        zones: ["All Doha", "The Pearl", "Al Rayyan"],
        wastes: ["food_surplus", "all"],
        batches: ["small", "bulk"],
        badge: "FREE SURPLUS RESCUE",
        secondaryBadge: "Food Waste Solution",
        badgeStyle: "bg-emerald-100 text-emerald-800 border-emerald-200",
        minRule: "For safe, unserved edible surplus food from hotels, restaurants, and events.",
        prepNote: "Store in hygienic food-grade containers and notify 2 hours prior.",
        actionLabel: "Visit Wa'hab Portal",
        website: "https://wahab.qa",
        whatsapp: "https://wa.me/97450520220",
        poolEligible: false
      },
      {
        id: 4,
        name: "Baladiya Municipal Waste Services",
        userTypes: ["household", "apartment"],
        zones: ["All Doha", "The Pearl", "Al Rayyan", "Al Wakrah"],
        wastes: ["all", "plastic", "paper"],
        batches: ["small", "bulk"],
        badge: "100% FREE",
        secondaryBadge: "Ministry of Municipality",
        badgeStyle: "bg-blue-100 text-blue-800 border-blue-200",
        minRule: "Available across neighborhood streets and blue/grey municipal bins.",
        prepNote: "Sort recyclables into blue bins; general waste into grey bins.",
        actionLabel: "Baladiya Portal",
        website: "https://www.mme.gov.qa",
        whatsapp: "",
        poolEligible: false
      }
    ];

    // CAN I RECYCLE THIS? GUIDE DATASET
    const guideItems = [
      { name: "Karak Paper Cups", status: "no", statusLabel: "No — Grey Bin", where: "Grey General Bin", tip: "Plastic lining inside karak cups prevents normal recycling. Throw in grey bin unless specified otherwise." },
      { name: "Pizza Boxes (Greasy)", status: "no", statusLabel: "No — Grey Bin", where: "Grey General Bin", tip: "Grease and food oil ruin cardboard pulp. Tear off clean top lid for paper bin; put greasy bottom in grey bin." },
      { name: "Plastic Water Bottles (PET)", status: "yes", statusLabel: "Yes — Recyclable", where: "Al Meera RVM or Blue Bins", tip: "Empty liquid, keep caps on, do not crush if using RVM machines." },
      { name: "Aluminum Drink Cans", status: "yes", statusLabel: "Yes — Recyclable", where: "Al Meera RVM or Blue Bins", tip: "Rinse out juice or soda residue and drop into blue bins or RVMs." },
      { name: "AA / AAA Batteries", status: "special", statusLabel: "Special Drop-Off", where: "Al Meera E-Waste Bins", tip: "Never put batteries in general trash due to fire risk. Drop off at designated supermarket e-bins." },
      { name: "Unserved Restaurant Food", status: "food", statusLabel: "Surplus Food Rescue", where: "Wa'hab Pickup", tip: "Edible untouched surplus meals can be rescued by Wa'hab for community distribution." },
      { name: "Cardboard Delivery Boxes", status: "yes", statusLabel: "Yes — Recyclable", where: "Elite Paper Eco-Domes", tip: "Flatten completely to save space in public cages." },
      { name: "Glass Jam / Sauce Jars", status: "yes", statusLabel: "Yes — Recyclable", where: "Municipal Blue Bins", tip: "Rinse clean and remove metal lids." }
    ];

    // LEADERBOARD DATASET
    const leaderboardData = [
      { rank: 1, name: "Al Waab Compound #4", area: "Al Waab", badge: "Compound Villa", kg: 240 },
      { rank: 2, name: "West Bay Tower B", area: "West Bay", badge: "Apartment Tower", kg: 185 },
      { rank: 3, name: "The Pearl Marina Residences", area: "The Pearl", badge: "Apartment Tower", kg: 142 },
      { rank: 4, name: "Al Sadd Villa Group", area: "Al Sadd", badge: "Residential", kg: 98 }
    ];

    // =========================================================
    // NAVIGATION & PROFILE MODE CONTROLS
    // =========================================================
    function switchTab(tabName) {
      currentTab = tabName;
      const tabs = ['map', 'matcher', 'guide', 'impact'];

      tabs.forEach(t => {
        const section = document.getElementById(`tab-${t}`);
        const navBtn = document.getElementById(`nav-${t}`);
        if (section) section.classList.toggle('hidden', t !== tabName);
        if (navBtn) {
          if (t === tabName) {
            navBtn.className = "nav-btn flex flex-col items-center justify-center py-1.5 px-1 rounded-xl text-qycle-700 bg-qycle-50 font-bold transition";
          } else {
            navBtn.className = "nav-btn flex flex-col items-center justify-center py-1.5 px-1 rounded-xl text-slate-500 hover:text-slate-900 font-semibold transition";
          }
        }
      });

      window.scrollTo({ top: 0, behavior: 'smooth' });
      lucide.createIcons();
    }

    function setProfileMode(mode) {
      profileMode = mode;
      const homeBtn = document.getElementById('mode-household');
      const bizBtn = document.getElementById('mode-business');
      const banner = document.getElementById('business-banner');

      if (mode === 'household') {
        homeBtn.className = "px-2.5 py-1.5 rounded-lg transition-all flex items-center gap-1 bg-white text-qycle-700 shadow-sm";
        bizBtn.className = "px-2.5 py-1.5 rounded-lg transition-all flex items-center gap-1 text-slate-600 hover:text-slate-900";
        banner.classList.add('hidden');
        document.getElementById('matcher-user-type').value = 'household';
        setBatchSize('small');
      } else {
        bizBtn.className = "px-2.5 py-1.5 rounded-lg transition-all flex items-center gap-1 bg-white text-amber-700 shadow-sm";
        homeBtn.className = "px-2.5 py-1.5 rounded-lg transition-all flex items-center gap-1 text-slate-600 hover:text-slate-900";
        banner.classList.remove('hidden');
        document.getElementById('matcher-user-type').value = 'restaurant';
        setBatchSize('bulk');
      }

      renderMapAndList();
      renderMatcherResults();
      lucide.createIcons();
    }

    // =========================================================
    // REAL BROWSER GPS INTEGRATION
    // =========================================================
    function applyGPSCoordinates(lat, lng, isSimulated = false) {
      userCoords = { lat, lng };

      // Determine closest Doha zone based on coordinate distance
      const zones = [
        { name: "Al Waab", lat: 25.2635, lng: 51.4648 },
        { name: "West Bay", lat: 25.3284, lng: 51.5282 },
        { name: "Al Sadd", lat: 25.2885, lng: 51.5065 },
        { name: "The Pearl", lat: 25.3672, lng: 51.5489 },
        { name: "Al Wakrah", lat: 25.1743, lng: 51.6078 },
        { name: "Al Rayyan", lat: 25.3352, lng: 51.4878 }
      ];

      let closestZone = "Al Waab";
      let minDist = Infinity;

      zones.forEach(z => {
        const dist = calculateDistanceKm(lat, lng, z.lat, z.lng);
        if (dist < minDist) {
          minDist = dist;
          closestZone = z.name;
        }
      });

      // Set select dropdown to detected zone
      document.getElementById('area-select').value = closestZone;
      document.getElementById('map-radius-label').textContent = `${isSimulated ? 'Demo GPS' : 'GPS Active'} • ${closestZone}`;
      
      // Show GPS banner
      const banner = document.getElementById('gps-status-banner');
      const text = document.getElementById('gps-status-text');
      const demoBtn = document.getElementById('gps-demo-btn');
      demoBtn.classList.add('hidden');
      banner.className = "text-[11px] bg-qycle-50 border border-qycle-200 text-qycle-900 px-3 py-2 rounded-xl flex items-center justify-between gap-2";
      text.textContent = `${isSimulated ? 'Demo GPS' : 'GPS locked'}: Matched to ${closestZone} (${lat.toFixed(4)}, ${lng.toFixed(4)})`;
      banner.classList.remove('hidden');

      renderMapAndList();
      showToast(`Centered on ${closestZone} with live km distances!`);
    }

    function simulateDohaGPS() {
      // Simulates a live user standing near Al Sadd / Bin Mahmoud in central Doha
      applyGPSCoordinates(25.2865, 51.5042, true);
    }

    function detectUserGPSLocation() {
      const banner = document.getElementById('gps-status-banner');
      const text = document.getElementById('gps-status-text');
      const demoBtn = document.getElementById('gps-demo-btn');

      if (!navigator.geolocation) {
        banner.className = "text-[11px] bg-amber-50 border border-amber-200 text-amber-900 px-3 py-2 rounded-xl flex items-center justify-between gap-2";
        text.textContent = "Browser doesn't support GPS. Tap 'Demo GPS' to preview live distance sorting.";
        demoBtn.classList.remove('hidden');
        banner.classList.remove('hidden');
        return;
      }

      showToast("Requesting GPS location...");

      navigator.geolocation.getCurrentPosition(
        (position) => {
          applyGPSCoordinates(position.coords.latitude, position.coords.longitude, false);
        },
        (error) => {
          console.warn("GPS error:", error);
          let reason = "Preview window blocks live GPS hardware.";
          if (error.code === 1) reason = "Location permission blocked by browser or preview iframe.";
          else if (error.code === 2) reason = "GPS signal unavailable on this device.";
          else if (error.code === 3) reason = "GPS request timed out.";

          banner.className = "text-[11px] bg-amber-50 border border-amber-200 text-amber-900 px-3 py-2 rounded-xl flex items-center justify-between gap-2";
          text.textContent = `${reason} Host on HTTPS for real GPS, or tap Demo GPS to test now:`;
          demoBtn.classList.remove('hidden');
          banner.classList.remove('hidden');
          showToast("Preview blocked GPS — tap 'Demo GPS' in the banner to test!");
        },
        { timeout: 6000, enableHighAccuracy: false, maximumAge: 60000 }
      );
    }

    function dismissGPSBanner() {
      document.getElementById('gps-status-banner').classList.add('hidden');
    }

    // Haversine formula to calculate real walking/driving distance from user GPS
    function calculateDistanceKm(lat1, lon1, lat2, lon2) {
      const R = 6371; // Radius of the earth in km
      const dLat = deg2rad(lat2 - lat1);
      const dLon = deg2rad(lon2 - lon1);
      const a = 
        Math.sin(dLat/2) * Math.sin(dLat/2) +
        Math.cos(deg2rad(lat1)) * Math.cos(deg2rad(lat2)) * 
        Math.sin(dLon/2) * Math.sin(dLon/2)
      ; 
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a)); 
      return R * c; // Distance in km
    }

    function deg2rad(deg) {
      return deg * (Math.PI/180);
    }

    // =========================================================
    // TAB 1: MAP & DROP-OFFS RENDERING
    // =========================================================
    const mapCategories = [
      { id: 'all', label: 'All Spots', icon: 'layers' },
      { id: 'paper', label: 'Paper / Boxes', icon: 'file-text' },
      { id: 'plastic', label: 'Plastic / Cans', icon: 'cup-soda' },
      { id: 'municipal', label: 'Blue Bins', icon: 'trash-2' },
      { id: 'ewaste', label: 'E-Waste', icon: 'battery-charging' },
      { id: 'food', label: 'Surplus Food', icon: 'utensils' }
    ];

    function renderMapFilterChips() {
      const container = document.getElementById('map-filter-chips');
      container.innerHTML = mapCategories.map(cat => {
        const active = selectedMapCategory === cat.id;
        return `
          <button onclick="setMapCategory('${cat.id}')" class="shrink-0 px-3 py-1.5 rounded-full text-xs font-bold flex items-center gap-1.5 border transition ${
            active 
              ? 'bg-qycle-600 text-white border-qycle-600 shadow-sm' 
              : 'bg-white text-slate-600 border-slate-200 hover:border-qycle-400'
          }">
            <i data-lucide="${cat.icon}" class="w-3.5 h-3.5"></i>
            <span>${cat.label}</span>
          </button>
        `;
      }).join('');
      lucide.createIcons();
    }

    function setMapCategory(catId) {
      selectedMapCategory = catId;
      renderMapFilterChips();
      renderMapAndList();
    }

    function handleAreaChange() {
      const area = document.getElementById('area-select').value;
      document.getElementById('map-radius-label').textContent = `Under 5-Min Radius • ${area}`;
      renderMapAndList();
    }

    function getPinColorClasses(category) {
      switch(category) {
        case 'paper': return 'bg-emerald-600 text-white border-white';
        case 'plastic': return 'bg-sky-600 text-white border-white';
        case 'municipal': return 'bg-blue-700 text-white border-white';
        case 'ewaste': return 'bg-amber-500 text-slate-950 border-white';
        case 'food': return 'bg-rose-600 text-white border-white';
        default: return 'bg-qycle-600 text-white border-white';
      }
    }

    function renderMapAndList() {
      const selectedArea = document.getElementById('area-select').value;
      const routeOnly = document.getElementById('route-toggle').checked;

      // Filter locations
      let filtered = dropoffLocations.filter(loc => {
        if (selectedMapCategory !== 'all' && loc.category !== selectedMapCategory) return false;
        if (routeOnly && !loc.alongRoute) return false;
        if (profileMode === 'business' && !loc.businessFriendly && selectedMapCategory === 'all') return false;
        return true;
      });

      // Sort by GPS distance if GPS is active, otherwise by selected area
      filtered.sort((a, b) => {
        if (userCoords) {
          const distA = calculateDistanceKm(userCoords.lat, userCoords.lng, a.lat, a.lng);
          const distB = calculateDistanceKm(userCoords.lat, userCoords.lng, b.lat, b.lng);
          return distA - distB;
        } else {
          const aMatch = a.area === selectedArea ? 0 : 1;
          const bMatch = b.area === selectedArea ? 0 : 1;
          if (aMatch !== bMatch) return aMatch - bMatch;
          return a.minutes - b.minutes;
        }
      });

      // Render Map Pins
      const pinsContainer = document.getElementById('map-pins-container');
      pinsContainer.innerHTML = filtered.map(loc => {
        const pinColors = getPinColorClasses(loc.category);
        const isCurrentArea = loc.area === selectedArea;
        return `
          <button 
            onclick="openDropoffModal(${loc.id})"
            style="left: ${loc.x}%; top: ${loc.y}%;"
            class="absolute -translate-x-1/2 -translate-y-1/2 group flex flex-col items-center focus:outline-none z-20 transition-transform hover:scale-110"
            title="${loc.name}"
          >
            <span class="mb-1 px-1.5 py-0.5 rounded-md text-[9px] font-extrabold shadow-sm border ${
              isCurrentArea ? 'bg-slate-900 text-white border-slate-700' : 'bg-white/95 text-slate-800 border-slate-200'
            } whitespace-nowrap">
              ${userCoords ? (calculateDistanceKm(userCoords.lat, userCoords.lng, loc.lat, loc.lng)).toFixed(1) + ' km' : loc.timeLabel}
            </span>
            <div class="w-8 h-8 rounded-full border-2 shadow-md flex items-center justify-center ${pinColors}">
              <i data-lucide="map-pin" class="w-4 h-4"></i>
            </div>
          </button>
        `;
      }).join('');

      // Render Synchronized Cards
      document.getElementById('dropoff-count').textContent = filtered.length;
      const cardsList = document.getElementById('dropoff-cards-list');

      if (filtered.length === 0) {
        cardsList.innerHTML = `
          <div class="bg-white rounded-2xl p-6 text-center border border-slate-200 space-y-2">
            <p class="text-sm font-bold text-slate-800">No bins match that exact filter</p>
            <p class="text-xs text-slate-500">Try turning off "Along My Errand Route" or switch to "All Spots".</p>
          </div>
        `;
        return;
      }

      cardsList.innerHTML = filtered.map(loc => {
        const badgeClass = loc.badgeColor === 'green' 
          ? 'bg-emerald-100 text-emerald-800 border-emerald-200' 
          : 'bg-blue-100 text-blue-800 border-blue-200';

        const liveDistText = userCoords 
          ? (calculateDistanceKm(userCoords.lat, userCoords.lng, loc.lat, loc.lng)).toFixed(1) + ' km away' 
          : loc.timeLabel;

        return `
          <div onclick="openDropoffModal(${loc.id})" class="bg-white hover:border-qycle-400 rounded-2xl p-3.5 shadow-sm border border-slate-200/90 transition cursor-pointer flex items-start justify-between gap-3">
            <div class="space-y-1.5 flex-1">
              <div class="flex flex-wrap items-center gap-1.5">
                <span class="text-[10px] font-extrabold px-2 py-0.5 rounded-md border ${badgeClass}">${loc.badge}</span>
                <span class="text-[10px] font-bold px-2 py-0.5 rounded-md bg-slate-100 text-slate-700">${loc.categoryLabel}</span>
                ${loc.alongRoute ? `<span class="text-[10px] font-semibold text-blue-600 flex items-center gap-0.5"><i data-lucide="navigation" class="w-2.5 h-2.5"></i> On Route</span>` : ''}
              </div>
              <h3 class="font-display font-bold text-sm text-slate-900 leading-snug">${loc.name}</h3>
              <p class="text-xs text-slate-500 flex items-center gap-1">
                <i data-lucide="map-pin" class="w-3 h-3 text-qycle-600 shrink-0"></i>
                <span>${loc.landmark} (${loc.area})</span>
              </p>
            </div>

            <div class="flex flex-col items-end justify-between self-stretch shrink-0">
              <span class="px-2.5 py-1 rounded-xl bg-qycle-50 text-qycle-800 border border-qycle-200 text-xs font-extrabold">
                ${liveDistText}
              </span>
              <span class="text-[11px] font-bold text-qycle-600 flex items-center gap-0.5 mt-2">
                Prep & Map <i data-lucide="chevron-right" class="w-3.5 h-3.5"></i>
              </span>
            </div>
          </div>
        `;
      }).join('');

      lucide.createIcons();
    }

    // Drop-Off Detail Modal
    function openDropoffModal(id) {
      const loc = dropoffLocations.find(item => item.id === id);
      if (!loc) return;
      activeDropoffModalItem = loc;

      const badgeClass = loc.badgeColor === 'green' 
        ? 'bg-emerald-100 text-emerald-800 border-emerald-200' 
        : 'bg-blue-100 text-blue-800 border-blue-200';

      document.getElementById('modal-badges').innerHTML = `
        <span class="text-[10px] font-extrabold px-2 py-0.5 rounded-md border ${badgeClass}">${loc.badge}</span>
        <span class="text-[10px] font-bold px-2 py-0.5 rounded-md bg-qycle-50 text-qycle-800 border border-qycle-200">${loc.timeLabel}</span>
      `;
      document.getElementById('modal-title').textContent = loc.name;
      document.getElementById('modal-address').innerHTML = `<i data-lucide="map-pin" class="w-3.5 h-3.5 text-qycle-600"></i> <span>${loc.landmark} • ${loc.area}</span>`;
      document.getElementById('modal-accepted').textContent = loc.accepted;
      document.getElementById('modal-checklist').innerHTML = loc.checklist.map(rule => `
        <li class="flex items-start gap-2">
          <i data-lucide="check" class="w-3.5 h-3.5 text-qycle-600 shrink-0 mt-0.5"></i>
          <span>${rule}</span>
        </li>
      `).join('');
      document.getElementById('modal-gmaps-link').href = loc.mapsUrl;

      document.getElementById('dropoff-modal').classList.remove('hidden');
      lucide.createIcons();
    }

    function logFromModal() {
      userStats.kg = +(userStats.kg + 2.5).toFixed(1);
      userStats.drops += 1;
      userStats.qar += 3;
      updateStatsUI();
      closeModal('dropoff-modal');
      showToast('Logged +2.5 kg drop-off! Great job keeping Doha green.');
    }

    // Add / Report New Bin Modal
    function openReportModal() {
      document.getElementById('report-modal').classList.remove('hidden');
    }

    function submitNewBin(e) {
      e.preventDefault();
      const name = document.getElementById('new-bin-name').value.trim();
      const area = document.getElementById('new-bin-area').value;
      const category = document.getElementById('new-bin-category').value;
      const note = document.getElementById('new-bin-note').value.trim() || "Community-verified drop-off point.";

      const catLabels = {
        paper: "Paper & Cardboard",
        plastic: "Plastic & Cans",
        municipal: "Municipal Blue Bins",
        ewaste: "E-Waste & Batteries",
        food: "Surplus Food"
      };

      const newPin = {
        id: Date.now(),
        name: name,
        area: area,
        landmark: `Community Pin • ${area}`,
        category: category,
        categoryLabel: catLabels[category],
        timeLabel: "3 min drive",
        minutes: 3,
        alongRoute: true,
        businessFriendly: true,
        badge: "COMMUNITY PIN",
        badgeColor: "blue",
        x: Math.floor(25 + Math.random() * 50),
        y: Math.floor(25 + Math.random() * 50),
        accepted: `${catLabels[category]} — verified by local DohaGreen user.`,
        checklist: [
          note,
          "Keep recyclables clean, empty, and dry.",
          "Report if bin becomes full so operators can empty it."
        ],
        mapsUrl: `https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(name + ' ' + area + ' Doha')}`
      };

      dropoffLocations.unshift(newPin);
      document.getElementById('area-select').value = area;
      handleAreaChange();
      closeModal('report-modal');
      e.target.reset();
      showToast('New Doha drop-off spot added to the live map!');
    }


    // =========================================================
    // TAB 2: SMART PICKUP MATCHER LOGIC
    // =========================================================
    function setBatchSize(size) {
      selectedBatchSize = size;
      const smallBtn = document.getElementById('batch-small');
      const bulkBtn = document.getElementById('batch-bulk');

      if (size === 'small') {
        smallBtn.className = "py-1.5 px-2 rounded-lg text-[11px] font-bold bg-white text-qycle-700 shadow-sm transition";
        bulkBtn.className = "py-1.5 px-2 rounded-lg text-[11px] font-bold text-slate-600 hover:text-slate-900 transition";
      } else {
        bulkBtn.className = "py-1.5 px-2 rounded-lg text-[11px] font-bold bg-white text-qycle-700 shadow-sm transition";
        smallBtn.className = "py-1.5 px-2 rounded-lg text-[11px] font-bold text-slate-600 hover:text-slate-900 transition";
      }
      renderMatcherResults();
    }

    function renderMatcherResults() {
      const userType = document.getElementById('matcher-user-type').value;
      const zone = document.getElementById('matcher-zone').value;
      const waste = document.getElementById('matcher-waste').value;

      const matches = pickupServices.filter(srv => {
        if (!srv.userTypes.includes(userType)) return false;
        if (!srv.zones.includes(zone)) return false;
        if (waste !== 'all' && !srv.wastes.includes(waste)) return false;
        if (!srv.batches.includes(selectedBatchSize)) return false;
        return true;
      });

      document.getElementById('matcher-count').textContent = matches.length;
      const container = document.getElementById('matcher-results-list');

      if (matches.length === 0) {
        container.innerHTML = `
          <div class="bg-white rounded-2xl p-5 text-center border border-slate-200 space-y-2">
            <p class="text-sm font-bold text-slate-800">No direct pickup for that exact combo</p>
            <p class="text-xs text-slate-500">Tip: Try switching Batch Size to "Bulk / Commercial" or check the 5-Min Map tab for free drop-off cages.</p>
          </div>
        `;
        return;
      }

      container.innerHTML = matches.map(srv => `
        <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200/90 space-y-3">
          <div class="flex flex-wrap items-start justify-between gap-2">
            <div>
              <div class="flex flex-wrap items-center gap-1.5 mb-1">
                <span class="text-[10px] font-extrabold px-2 py-0.5 rounded-md border ${srv.badgeStyle}">${srv.badge}</span>
                <span class="text-[10px] font-semibold px-2 py-0.5 rounded-md bg-slate-100 text-slate-600">${srv.secondaryBadge}</span>
              </div>
              <h4 class="font-display font-bold text-base text-slate-900">${srv.name}</h4>
            </div>
          </div>

          <div class="space-y-1.5 text-xs text-slate-600 bg-slate-50 p-3 rounded-xl border border-slate-100">
            <p><strong class="text-slate-800">Minimums & Pricing:</strong> ${srv.minRule}</p>
            <p><strong class="text-slate-800">Prep Rule:</strong> ${srv.prepNote}</p>
          </div>

          <div class="flex flex-wrap items-center gap-2 pt-0.5">
            <a href="${srv.website}" target="_blank" rel="noopener noreferrer" class="flex-1 min-w-[130px] bg-slate-900 hover:bg-slate-800 text-white font-bold text-xs py-2.5 px-3 rounded-xl flex items-center justify-center gap-1.5 transition">
              <i data-lucide="external-link" class="w-3.5 h-3.5 text-qycle-400"></i>
              <span>${srv.actionLabel}</span>
            </a>
            ${srv.whatsapp ? `
              <a href="${srv.whatsapp}" target="_blank" rel="noopener noreferrer" class="bg-emerald-600 hover:bg-emerald-500 text-white font-bold text-xs py-2.5 px-3 rounded-xl flex items-center justify-center gap-1.5 transition">
                <i data-lucide="message-circle" class="w-3.5 h-3.5"></i>
                <span>WhatsApp</span>
              </a>
            ` : ''}
            ${srv.poolEligible && userType === 'apartment' ? `
              <button onclick="showToast('Joined Thursday Tower Pickup Pool! Neighbors notified.')" class="w-full sm:w-auto bg-qycle-50 hover:bg-qycle-100 text-qycle-800 border border-qycle-200 font-bold text-xs py-2.5 px-3 rounded-xl flex items-center justify-center gap-1.5 transition">
                <i data-lucide="users" class="w-3.5 h-3.5"></i>
                <span>Join Building Pool</span>
              </button>
            ` : ''}
          </div>
        </div>
      `).join('');

      lucide.createIcons();
    }

    function openPetitionModal() {
      document.getElementById('petition-modal').classList.remove('hidden');
    }

    function copyPetitionText() {
      const text = document.getElementById('petition-text').textContent;
      navigator.clipboard.writeText(text).then(() => {
        closeModal('petition-modal');
        showToast('Copied template! Paste it into your building WhatsApp group.');
      }).catch(() => {
        closeModal('petition-modal');
        showToast('Template ready to share with management!');
      });
    }

    function openPosterModal() {
      document.getElementById('poster-modal').classList.remove('hidden');
      lucide.createIcons();
    }


    // =========================================================
    // TAB 3: "CAN I RECYCLE THIS?" SEARCH & GUIDE
    // =========================================================
    const guideFilters = [
      { id: 'all', label: 'All (18)' },
      { id: 'yes', label: 'Yes — Recyclable' },
      { id: 'no', label: 'No — Grey Bin' },
      { id: 'special', label: 'Special Drop-Off' },
      { id: 'food', label: 'Food / Compost' }
    ];

    function renderGuideFilterChips() {
      const container = document.getElementById('guide-filter-chips');
      container.innerHTML = guideFilters.map(f => {
        const active = selectedGuideFilter === f.id;
        return `
          <button onclick="setGuideFilter('${f.id}')" class="shrink-0 px-3 py-1.5 rounded-full text-xs font-bold border transition ${
            active 
              ? 'bg-slate-900 text-white border-slate-900' 
              : 'bg-slate-50 text-slate-600 border-slate-200 hover:border-slate-400'
          }">
            ${f.label}
          </button>
        `;
      }).join('');
    }

    function setGuideFilter(id) {
      selectedGuideFilter = id;
      renderGuideFilterChips();
      renderGuideItems();
    }

    function clearSearch() {
      document.getElementById('item-search-input').value = '';
      renderGuideItems();
    }

    function renderGuideItems() {
      const query = document.getElementById('item-search-input').value.toLowerCase().trim();
      const clearBtn = document.getElementById('clear-search-btn');
      clearBtn.classList.toggle('hidden', query.length === 0);

      const filtered = guideItems.filter(item => {
        if (selectedGuideFilter !== 'all' && item.status !== selectedGuideFilter) return false;
        if (query && !item.name.toLowerCase().includes(query) && !item.where.toLowerCase().includes(query) && !item.tip.toLowerCase().includes(query)) {
          return false;
        }
        return true;
      });

      const list = document.getElementById('guide-items-list');
      if (filtered.length === 0) {
        list.innerHTML = `
          <div class="bg-white rounded-2xl p-6 text-center border border-slate-200">
            <p class="text-sm font-bold text-slate-800">No items found for "${query}"</p>
            <p class="text-xs text-slate-500 mt-1">Try searching for "bottles", "boxes", "karak", "batteries", or "food".</p>
          </div>
        `;
        return;
      }

      list.innerHTML = filtered.map(item => {
        let badgeStyle = 'bg-emerald-100 text-emerald-800 border-emerald-200';
        if (item.status === 'no') badgeStyle = 'bg-rose-100 text-rose-800 border-rose-200';
        if (item.status === 'special') badgeStyle = 'bg-amber-100 text-amber-800 border-amber-200';
        if (item.status === 'food') badgeStyle = 'bg-sky-100 text-sky-800 border-sky-200';

        return `
          <div class="bg-white rounded-2xl p-3.5 shadow-sm border border-slate-200/80 space-y-2">
            <div class="flex items-start justify-between gap-2">
              <h3 class="font-display font-bold text-sm text-slate-900">${item.name}</h3>
              <span class="shrink-0 text-[10px] font-extrabold px-2 py-0.5 rounded-md border ${badgeStyle}">${item.statusLabel}</span>
            </div>
            <p class="text-xs font-semibold text-qycle-800 flex items-center gap-1.5">
              <i data-lucide="map-pin" class="w-3.5 h-3.5 text-qycle-600 shrink-0"></i>
              <span>${item.where}</span>
            </p>
            <p class="text-xs text-slate-600 leading-relaxed bg-slate-50 px-3 py-2 rounded-xl border border-slate-100">
              ${item.tip}
            </p>
          </div>
        `;
      }).join('');

      lucide.createIcons();
    }


    // =========================================================
    // TAB 4: IMPACT TRACKER & LEADERBOARD
    // =========================================================
    function updateStatsUI() {
      document.getElementById('stat-kg').textContent = userStats.kg;
      document.getElementById('stat-drops').textContent = userStats.drops;
      document.getElementById('stat-qar').textContent = userStats.qar;
      document.getElementById('stat-streak').textContent = `${userStats.streak} Day Streak`;
    }

    function logQuickRecycle() {
      const val = document.getElementById('log-type-select').value;
      const [kgAdd, qarAdd] = val.split('|').map(Number);

      userStats.kg = +(userStats.kg + kgAdd).toFixed(1);
      userStats.drops += 1;
      userStats.qar += qarAdd;
      userStats.streak += 1;

      // Also boost #1 Al Waab Compound in leaderboard for live feedback
      leaderboardData[0].kg = Math.round(leaderboardData[0].kg + kgAdd);

      updateStatsUI();
      renderLeaderboard();
      showToast(`Logged +${kgAdd} kg! Your Doha impact stats updated.`);
    }

    function renderLeaderboard() {
      const container = document.getElementById('leaderboard-list');
      container.innerHTML = leaderboardData.map(row => {
        const isFirst = row.rank === 1;
        return `
          <div class="flex items-center justify-between p-2.5 rounded-xl border ${
            isFirst ? 'bg-qycle-50/70 border-qycle-200' : 'bg-slate-50 border-slate-100'
          }">
            <div class="flex items-center gap-2.5">
              <span class="w-6 h-6 rounded-full text-xs font-extrabold flex items-center justify-center ${
                isFirst ? 'bg-amber-400 text-slate-950' : 'bg-slate-200 text-slate-700'
              }">${row.rank}</span>
              <div>
                <p class="text-xs font-bold text-slate-900">${row.name}</p>
                <p class="text-[10px] text-slate-500">${row.area} • ${row.badge}</p>
              </div>
            </div>
            <span class="text-xs font-extrabold text-qycle-700">${row.kg} kg</span>
          </div>
        `;
      }).join('');
    }

    function togglePortfolioCard() {
      const details = document.getElementById('portfolio-details');
      const chevron = document.getElementById('portfolio-chevron');
      details.classList.toggle('hidden');
      chevron.classList.toggle('rotate-180');
    }


    // =========================================================
    // UTILITIES: MODALS & TOAST
    // =========================================================
    function closeModal(modalId) {
      document.getElementById(modalId).classList.add('hidden');
    }

    // First-Visit GPS Onboarding Check (Runs only once per device)
    function checkFirstVisitGPSPrompt() {
      try {
        const hasSeenPrompt = localStorage.getItem('dohagreen_gps_prompt_seen');
        if (!hasSeenPrompt) {
          document.getElementById('first-visit-gps-modal').classList.remove('hidden');
          lucide.createIcons();
        }
      } catch (e) {
        // Fallback if localStorage is restricted in strict private mode
        document.getElementById('first-visit-gps-modal').classList.remove('hidden');
        lucide.createIcons();
      }
    }

    function handleFirstVisitGPSChoice(allowGPS) {
      try {
        localStorage.setItem('dohagreen_gps_prompt_seen', 'true');
      } catch (e) {
        console.warn('localStorage unavailable:', e);
      }

      closeModal('first-visit-gps-modal');

      if (allowGPS) {
        detectUserGPSLocation();
      } else {
        showToast('No problem! Select your Doha zone from the dropdown anytime.');
      }
    }

    function showToast(message) {
      const toast = document.getElementById('toast');
      document.getElementById('toast-msg').textContent = message;
      toast.classList.remove('hidden');
      lucide.createIcons();
      clearTimeout(window.toastTimer);
      window.toastTimer = setTimeout(() => {
        toast.classList.add('hidden');
      }, 3200);
    }

    // Initialize App on Load
    window.onload = function() {
      renderMapFilterChips();
      renderMapAndList();
      renderMatcherResults();
      renderGuideFilterChips();
      renderGuideItems();
      updateStatsUI();
      renderLeaderboard();
      lucide.createIcons();
      checkFirstVisitGPSPrompt();
    };
  </script>
</body>
</html>
