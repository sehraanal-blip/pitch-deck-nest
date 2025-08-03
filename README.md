<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NEST Ventures: Interactive Pitch Deck</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&family=Clash+Display:wght@600&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg-color: #0D0D12;
            --primary-accent: #3A7DFF;
            --secondary-accent: #00C2FF;
            --text-primary: #EAEBF0;
            --text-secondary: #A0A3BD;
            --border-color: rgba(255, 255, 255, 0.1);
        }

        body {
            font-family: 'Inter', sans-serif;
            background-color: var(--bg-color);
            color: var(--text-primary);
            overflow: hidden;
            position: relative;
        }
        
        #particle-canvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -2;
        }

        #background-gradient {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: radial-gradient(ellipse 80% 80% at 50% -20%, rgba(58, 125, 255, 0.1), rgba(255,255,255,0));
            z-index: -1;
            transition: transform 1s ease-in-out;
        }

        .slide {
            width: 100vw;
            height: 100vh;
            position: absolute;
            top: 0;
            left: 0;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 2rem 1.5rem; /* Base padding for mobile */
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.8s cubic-bezier(0.4, 0, 0.2, 1), transform 0.8s cubic-bezier(0.4, 0, 0.2, 1);
            transform: translateY(30px);
            overflow-y: auto; /* Allow scrolling on small screens if content overflows */
        }
        
        @media (min-width: 768px) {
            .slide {
                padding: 2rem 4rem; /* Larger padding for tablets and desktops */
                overflow-y: hidden;
            }
        }
        
        .slide.active {
            opacity: 1;
            visibility: visible;
            transform: translateY(0);
        }
        
        .slide-content {
            max-width: 1200px;
            width: 100%;
        }

        h1, h2, h3 {
            font-family: 'Clash Display', sans-serif;
            letter-spacing: -0.02em;
        }

        .gradient-text {
            background: linear-gradient(90deg, var(--primary-accent), var(--secondary-accent));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .nav-controls {
            position: fixed;
            bottom: 1.5rem;
            right: 1.5rem;
            display: flex;
            gap: 0.75rem;
            z-index: 100;
        }

        .nav-button {
            background-color: rgba(255, 255, 255, 0.05);
            border: 1px solid var(--border-color);
            color: var(--text-secondary);
            width: 2.5rem;
            height: 2.5rem;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            transition: all 0.3s;
            backdrop-filter: blur(10px);
        }
        
        @media (min-width: 768px) {
            .nav-button {
                width: 3rem;
                height: 3rem;
            }
        }

        .nav-button:hover {
            background-color: rgba(58, 125, 255, 0.2);
            border-color: var(--primary-accent);
            color: var(--text-primary);
        }
        
        .slide-counter {
            position: fixed;
            bottom: 2rem;
            left: 1.5rem;
            font-size: 0.875rem;
            color: var(--text-secondary);
            z-index: 100;
            font-variant-numeric: tabular-nums;
        }
        
        @media (min-width: 768px) {
            .slide-counter {
                bottom: 2.5rem;
                left: 2rem;
                font-size: 1rem;
            }
        }

        .card {
            background: linear-gradient(145deg, rgba(255, 255, 255, 0.05), rgba(255, 255, 255, 0.02));
            border: 1px solid var(--border-color);
            border-radius: 1.5rem;
            padding: 1rem; /* Base padding for mobile */
            transition: all 0.3s ease;
            backdrop-filter: blur(5px);
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }
        
        @media (min-width: 768px) {
            .card {
                padding: 2rem; /* Larger padding for tablets and desktops */
            }
        }

        .card:hover {
            transform: translateY(-5px);
            border-color: rgba(58, 125, 255, 0.5);
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
        }

        .flywheel { position: relative; width: 90vw; height: 90vw; max-width: 550px; max-height: 550px; display: flex; justify-content: center; align-items: center; margin-top: 2rem; }
        .flywheel-path { position: absolute; width: 100%; height: 100%; border-radius: 50%; border: 2px dashed rgba(58, 125, 255, 0.2); animation: rotate 60s linear infinite; }
        @keyframes rotate { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
        .flywheel-center { width: 35%; height: 35%; background: radial-gradient(circle, rgba(58, 125, 255, 0.15) 0%, rgba(58, 125, 255, 0) 70%); border-radius: 50%; display: flex; justify-content: center; align-items: center; text-align: center; font-size: clamp(1rem, 4vw, 1.5rem); color: var(--primary-accent); }
        .flywheel-item { position: absolute; width: 30%; max-width: 160px; text-align: center; padding: 0.75rem; border-radius: 1rem; background: rgba(13, 13, 18, 0.8); backdrop-filter: blur(5px); transition: transform 0.3s ease; animation: pulse 4s ease-in-out infinite; }
        .flywheel-item:hover { transform: scale(1.05) !important; animation-play-state: paused; }
        @keyframes pulse { 0%, 100% { box-shadow: 0 0 0 0 rgba(58, 125, 255, 0); } 50% { box-shadow: 0 0 15px 3px rgba(58, 125, 255, 0.3); } }

        /* SVG Logo Animation */
        #animated-logo path { stroke-dasharray: 1000; stroke-dashoffset: 1000; animation: draw 2.5s ease-in-out forwards; }
        #animated-logo.active path:nth-child(1) { animation-delay: 0s; }
        #animated-logo.active path:nth-child(2) { animation-delay: 0.5s; }
        #animated-logo.active path:nth-child(3) { animation-delay: 1s; }
        @keyframes draw { to { stroke-dashoffset: 0; } }
        
    </style>
</head>
<body>
    <canvas id="particle-canvas"></canvas>
    <div id="background-gradient"></div>

    <!-- Slide 1: Title -->
    <section id="slide-1" class="slide active">
        <div class="slide-content text-center">
            <svg id="animated-logo" class="mx-auto mb-6 h-16 w-16 md:h-20 md:w-20 text-blue-500 active" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M12 2L2 7L12 12L22 7L12 2Z" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
                <path d="M2 17L12 22L22 17" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
                <path d="M2 12L12 17L22 12" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
            <h1 class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl font-semibold tracking-tighter mb-4">NEST Ventures</h1>
            <p class="text-lg md:text-2xl text-slate-300 max-w-3xl mx-auto">We don't just fund startups. We build market-defining companies from the ground up.</p>
        </div>
    </section>

    <!-- Slide 2: The Problem -->
    <section id="slide-2" class="slide">
        <div class="slide-content">
            <h2 class="text-4xl sm:text-5xl md:text-6xl font-semibold text-center mb-8 md:mb-16">The <span class="gradient-text">Execution Gap</span> is the Silent Killer</h2>
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6 md:gap-8">
                <div class="card">
                    <svg class="w-10 h-10 md:w-12 md:h-12 mb-4 text-blue-400" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 12h.01"/><path d="M12 6.5a5.5 5.5 0 1 1-5.5 5.5"/><path d="M12 6.5a5.5 5.5 0 0 0 0-11 5.5 5.5 0 0 0 0 11Z"/><path d="M14 12a2 2 0 1 1-2-2"/><path d="M14 12a2 2 0 0 0 0-4 2 2 0 0 0 0 4Z"/><path d="M12 14a2 2 0 1 0 2 2"/><path d="M12 14a2 2 0 0 0 0 4 2 2 0 0 0 0-4Z"/><path d="M10 12a2 2 0 1 1-2-2"/><path d="M10 12a2 2 0 0 0 0-4 2 2 0 0 0 0 4Z"/></svg>
                    <h3 class="text-lg md:text-xl font-semibold mb-2 text-blue-400">Founder Overload</h3>
                    <p class="text-sm md:text-base text-slate-400">Founders are stretched thin, juggling strategy, operations, and fundraising.</p>
                </div>
                <div class="card">
                    <svg class="w-10 h-10 md:w-12 md:h-12 mb-4 text-blue-400" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="12" y1="2" x2="12" y2="22"/><line x1="17" y1="5" x2="7" y2="5"/><line x1="17" y1="12" x2="7" y2="12"/><line x1="17" y1="19" x2="7" y2="19"/></svg>
                    <h3 class="text-lg md:text-xl font-semibold mb-2 text-blue-400">Capital Misalignment</h3>
                    <p class="text-sm md:text-base text-slate-400">Traditional VC models often force premature scaling over foundational strength.</p>
                </div>
                <div class="card">
                    <svg class="w-10 h-10 md:w-12 md:h-12 mb-4 text-blue-400" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M18 10h-1.26A8 8 0 1 0 9 20h9a5 5 0 0 0 0-10Z"/><path d="m13 11-4-4 6-6"/></svg>
                    <h3 class="text-lg md:text-xl font-semibold mb-2 text-blue-400">Fragmented Support</h3>
                    <p class="text-sm md:text-base text-slate-400">Advisory is passive, lacking deeply embedded, operational expertise.</p>
                </div>
                <div class="card">
                    <svg class="w-10 h-10 md:w-12 md:h-12 mb-4 text-blue-400" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 22c5.523 0 10-4.477 10-10S17.523 2 12 2 2 6.477 2 12s4.477 10 10 10Z"/><path d="m9 12 2 2 4-4"/></svg>
                    <h3 class="text-lg md:text-xl font-semibold mb-2 text-blue-400">Operational Debt</h3>
                    <p class="text-sm md:text-base text-slate-400">Weak systems create a fragile foundation that crumbles under scale.</p>
                </div>
            </div>
        </div>
    </section>

    <!-- Slide 3: Our Solution -->
    <section id="slide-3" class="slide">
        <div class="slide-content text-center">
            <h2 class="text-4xl sm:text-5xl md:text-6xl font-semibold mb-4">We are <span class="gradient-text">Venture Builders</span>, Not Venture Capitalists.</h2>
            <p class="text-base md:text-xl text-slate-300 max-w-4xl mx-auto mb-8 md:mb-16">NEST is an operational partner that provides capital, embedded talent, and a shared services platform to transform promising ideas into resilient, scalable businesses.</p>
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6 md:gap-8">
                <div class="text-center p-2 md:p-4">
                    <svg class="w-12 h-12 md:w-16 md:h-16 mx-auto mb-4 text-cyan-400" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M22 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/></svg>
                    <h3 class="text-xl md:text-2xl font-semibold mb-2">Embedded Operators</h3>
                    <p class="text-sm md:text-base text-slate-400">Our teams for GTM, product, and finance integrate directly into your startup.</p>
                </div>
                <div class="text-center p-2 md:p-4">
                    <svg class="w-12 h-12 md:w-16 md:h-16 mx-auto mb-4 text-cyan-400" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="2" y="7" width="20" height="14" rx="2" ry="2"/><path d="M16 21V5a2 2 0 0 0-2-2h-4a2 2 0 0 0-2 2v16"/></svg>
                    <h3 class="text-xl md:text-2xl font-semibold mb-2">Shared Infrastructure</h3>
                    <p class="text-sm md:text-base text-slate-400">Leverage our pre-built legal, tech, and administrative frameworks from day one.</p>
                </div>
                <div class="text-center p-2 md:p-4">
                    <svg class="w-12 h-12 md:w-16 md:h-16 mx-auto mb-4 text-cyan-400" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 2v20M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"/></svg>
                    <h3 class="text-xl md:text-2xl font-semibold mb-2">Patient Capital</h3>
                    <p class="text-sm md:text-base text-slate-400">We invest with a long-term view, focused on building sustainable value.</p>
                </div>
                <div class="text-center p-2 md:p-4">
                    <svg class="w-12 h-12 md:w-16 md:h-16 mx-auto mb-4 text-cyan-400" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.72"/><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.72-1.72"/></svg>
                    <h3 class="text-xl md:text-2xl font-semibold mb-2">Ecosystem Network</h3>
                    <p class="text-sm md:text-base text-slate-400">Gain immediate access to our network of clients, partners, and talent.</p>
                </div>
            </div>
        </div>
    </section>

    <!-- Slide 4: The Flywheel -->
    <section id="slide-4" class="slide">
        <div class="slide-content text-center">
            <h2 class="text-4xl sm:text-5xl md:text-6xl font-semibold mb-4">The NEST <span class="gradient-text">Growth Engine</span></h2>
            <p class="text-base md:text-xl text-slate-300 max-w-4xl mx-auto mb-8">Our flywheel model creates compounding value across our entire portfolio.</p>
            <div class="flywheel mx-auto">
                <div class="flywheel-path"></div>
                <div class="flywheel-center">NEST<br>Flywheel</div>
                <div class="flywheel-item" style="animation-delay: 0s;">
                    <h3 class="font-bold text-sm md:text-lg mb-1">1. Ideate & Build</h3>
                    <p class="text-xs md:text-sm text-slate-400">Co-create ventures or acquire promising teams.</p>
                </div>
                <div class="flywheel-item" style="animation-delay: -0.8s;">
                    <h3 class="font-bold text-sm md:text-lg mb-1">2. Integrate & Refine</h3>
                    <p class="text-xs md:text-sm text-slate-400">Embed operators and deploy shared services.</p>
                </div>
                <div class="flywheel-item" style="animation-delay: -1.6s;">
                    <h3 class="font-bold text-sm md:text-lg mb-1">3. Scale & Accelerate</h3>
                    <p class="text-xs md:text-sm text-slate-400">Drive GTM strategy and secure follow-on capital.</p>
                </div>
                <div class="flywheel-item" style="animation-delay: -2.4s;">
                    <h3 class="font-bold text-sm md:text-lg mb-1">4. Cross-Pollinate</h3>
                    <p class="text-xs md:text-sm text-slate-400">Create synergies and share learnings across portfolio.</p>
                </div>
                <div class="flywheel-item" style="animation-delay: -3.2s;">
                    <h3 class="font-bold text-sm md:text-lg mb-1">5. Engineer Exits</h3>
                    <p class="text-xs md:text-sm text-slate-400">Drive value back into the flywheel via strategic exits.</p>
                </div>
            </div>
        </div>
    </section>

    <!-- Slide 5: Portfolio -->
    <section id="slide-5" class="slide">
        <div class="slide-content">
            <h2 class="text-4xl sm:text-5xl md:text-6xl font-semibold text-center mb-8 md:mb-12">Our Inaugural <span class="gradient-text">Portfolio</span></h2>
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6 md:gap-8">
                <div class="card">
                    <h3 class="text-xl md:text-2xl font-semibold mb-2">DigiClinic</h3>
                    <p class="text-blue-400 mb-4">AI Diagnostics</p>
                    <p class="text-sm md:text-base text-slate-300 mb-4">AI-powered diagnostics for breast USG & pathology.</p>
                    <p class="font-semibold">Valuation: <span class="text-white">₹36 Cr</span></p>
                </div>
                <div class="card">
                    <h3 class="text-xl md:text-2xl font-semibold mb-2">MotionX</h3>
                    <p class="text-blue-400 mb-4">AI Content Studio</p>
                    <p class="text-sm md:text-base text-slate-300 mb-4">Generative AI video platform for enterprise marketing.</p>
                    <p class="font-semibold">FY25 Revenue Proj: <span class="text-white">₹4 Cr</span></p>
                </div>
                <div class="card">
                    <h3 class="text-xl md:text-2xl font-semibold mb-2">Clubin</h3>
                    <p class="text-blue-400 mb-4">AI x Nightlife</p>
                    <p class="text-sm md:text-base text-slate-300 mb-4">Youth social platform with behavioral analytics & POS SaaS.</p>
                    <p class="font-semibold">Valuation: <span class="text-white">₹8.5 Cr</span></p>
                </div>
                <div class="card">
                    <h3 class="text-xl md:text-2xl font-semibold mb-2">Lashme</h3>
                    <p class="text-blue-400 mb-4">HealthTech IoT</p>
                    <p class="text-sm md:text-base text-slate-300 mb-4">IoT solution for tracking medical compliance and sterilization.</p>
                    <p class="font-semibold">FY25 Revenue Proj: <span class="text-white">₹4 Cr</span></p>
                </div>
            </div>
        </div>
    </section>

    <!-- Slide 6: Portfolio Traction -->
    <section id="slide-6" class="slide">
        <div class="slide-content">
            <h2 class="text-4xl md:text-5xl lg:text-6xl font-semibold text-center mb-8">Portfolio <span class="gradient-text">Traction</span></h2>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 sm:gap-6 lg:gap-8 items-center max-w-4xl mx-auto">
                <div class="card p-3 sm:p-4">
                    <h3 class="text-base sm:text-lg font-semibold text-center mb-2">DigiClinic: Growth Factor</h3>
                    <canvas id="digiClinicChart"></canvas>
                </div>
                <div class="card p-3 sm:p-4">
                    <h3 class="text-base sm:text-lg font-semibold text-center mb-2">MotionX: Revenue (₹ Cr)</h3>
                    <canvas id="motionXChart"></canvas>
                </div>
            </div>
        </div>
    </section>

    <!-- Slide 7: NEST Ventures Traction -->
    <section id="slide-7" class="slide">
        <div class="slide-content">
            <h2 class="text-4xl md:text-5xl lg:text-6xl font-semibold text-center mb-8">NEST Ventures <span class="gradient-text">Traction</span></h2>
            <div class="card p-4 md:p-8 max-w-4xl mx-auto">
                <h3 class="text-xl md:text-2xl font-semibold text-center mb-4">Projected Revenue Growth (in ₹ Crore)</h3>
                <canvas id="nestRevenueChart"></canvas>
            </div>
        </div>
    </section>

    <!-- Slide 8: The Ask -->
    <section id="slide-8" class="slide">
        <div class="slide-content">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-8 md:gap-16 items-center">
                <div class="text-center md:text-left">
                    <h2 class="text-xl md:text-2xl text-slate-400 mb-2">The Opportunity</h2>
                    <p class="text-5xl sm:text-6xl md:text-7xl lg:text-8xl font-semibold text-white mb-8">₹5 Crore Seed</p>
                    <p class="text-base md:text-xl text-slate-300">To activate our next cohort of startups, scale our operational team, and expand our shared services platform.</p>
                </div>
                <div class="card p-4 md:p-8">
                    <h3 class="text-xl md:text-2xl font-semibold text-center mb-4">Use of Funds</h3>
                    <canvas id="fundsChart"></canvas>
                </div>
            </div>
        </div>
    </section>

    <!-- Slide 9: Team -->
    <section id="slide-9" class="slide">
        <div class="slide-content">
            <h2 class="text-4xl sm:text-5xl md:text-6xl font-semibold text-center mb-8 md:mb-12">The <span class="gradient-text">Architects</span></h2>
            <div class="grid grid-cols-3 sm:grid-cols-5 gap-4 md:gap-6 text-center">
                <div class="p-2 md:p-4 transition-transform duration-300 hover:scale-105"><img src="https://placehold.co/128x128/0D0D12/EAEBF0?text=S" class="w-20 h-20 md:w-24 md:h-24 rounded-full mx-auto mb-4 border-2 border-slate-700 transition-all duration-300 hover:border-blue-500 hover:shadow-lg hover:shadow-blue-500/20" alt="Sehran"><h3 class="font-semibold text-sm sm:text-base md:text-lg">Sehran</h3><p class="text-xs md:text-sm text-slate-400">Founder & CEO</p></div>
                <div class="p-2 md:p-4 transition-transform duration-300 hover:scale-105"><img src="https://placehold.co/128x128/0D0D12/EAEBF0?text=MK" class="w-20 h-20 md:w-24 md:h-24 rounded-full mx-auto mb-4 border-2 border-slate-700 transition-all duration-300 hover:border-blue-500 hover:shadow-lg hover:shadow-blue-500/20" alt="Mehboob Khan"><h3 class="font-semibold text-sm sm:text-base md:text-lg">Mehboob Khan</h3><p class="text-xs md:text-sm text-slate-400">Co-Founder</p></div>
                <div class="p-2 md:p-4 transition-transform duration-300 hover:scale-105"><img src="https://placehold.co/128x128/0D0D12/EAEBF0?text=SC" class="w-20 h-20 md:w-24 md:h-24 rounded-full mx-auto mb-4 border-2 border-slate-700 transition-all duration-300 hover:border-blue-500 hover:shadow-lg hover:shadow-blue-500/20" alt="Shreyasri C."><h3 class="font-semibold text-sm sm:text-base md:text-lg">Shreyasri C.</h3><p class="text-xs md:text-sm text-slate-400">COO</p></div>
                <div class="p-2 md:p-4 transition-transform duration-300 hover:scale-105"><img src="https://placehold.co/128x128/0D0D12/EAEBF0?text=ZM" class="w-20 h-20 md:w-24 md:h-24 rounded-full mx-auto mb-4 border-2 border-slate-700 transition-all duration-300 hover:border-blue-500 hover:shadow-lg hover:shadow-blue-500/20" alt="M. Zubair Momin"><h3 class="font-semibold text-sm sm:text-base md:text-lg">M. Zubair Momin</h3><p class="text-xs md:text-sm text-slate-400">CIO</p></div>
                <div class="p-2 md:p-4 transition-transform duration-300 hover:scale-105"><img src="https://placehold.co/128x128/0D0D12/EAEBF0?text=SA" class="w-20 h-20 md:w-24 md:h-24 rounded-full mx-auto mb-4 border-2 border-slate-700 transition-all duration-300 hover:border-blue-500 hover:shadow-lg hover:shadow-blue-500/20" alt="Sakshi Agarwal"><h3 class="font-semibold text-sm sm:text-base md:text-lg">Sakshi Agarwal</h3><p class="text-xs md:text-sm text-slate-400">CFO</p></div>
            </div>
        </div>
    </section>

    <!-- Slide 10: Contact -->
    <section id="slide-10" class="slide">
        <div class="slide-content text-center">
            <h2 class="text-5xl md:text-7xl font-semibold mb-8">Let's Build <span class="gradient-text">Together.</span></h2>
            <p class="text-lg md:text-xl text-slate-300">Sehran, Founder & CEO</p>
            <p class="text-lg md:text-xl text-slate-400 mt-2 mb-12 md:mb-16">[email protected]</p>
        </div>
    </section>

    <!-- Navigation -->
    <div class="nav-controls">
        <button id="prev-btn" class="nav-button"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m15 18-6-6 6-6"/></svg></button>
        <button id="next-btn" class="nav-button"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m9 18 6-6-6-6"/></svg></button>
    </div>
    <div id="slide-counter" class="slide-counter"></div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            try {
                // --- Particle Animation ---
                const particleCanvas = document.getElementById('particle-canvas');
                const pctx = particleCanvas.getContext('2d');
                let particlesArray;
                function resizeCanvas() {
                    particleCanvas.width = window.innerWidth;
                    particleCanvas.height = window.innerHeight;
                }
                class Particle {
                    constructor(x, y, dX, dY, s, c) { this.x = x; this.y = y; this.directionX = dX; this.directionY = dY; this.size = s; this.color = c; }
                    draw() { pctx.beginPath(); pctx.arc(this.x, this.y, this.size, 0, Math.PI * 2, false); pctx.fillStyle = this.color; pctx.fill(); }
                    update() { if (this.x > particleCanvas.width || this.x < 0) this.directionX = -this.directionX; if (this.y > particleCanvas.height || this.y < 0) this.directionY = -this.directionY; this.x += this.directionX; this.y += this.directionY; this.draw(); }
                }
                function initParticles() {
                    particlesArray = [];
                    let num = (particleCanvas.height * particleCanvas.width) / 9000;
                    for (let i = 0; i < num; i++) {
                        let s = (Math.random() * 2) + 0.5, x = Math.random() * ((innerWidth - s * 2) - (s * 2)) + s * 2, y = Math.random() * ((innerHeight - s * 2) - (s * 2)) + s * 2, dX = (Math.random() * 0.2) - 0.1, dY = (Math.random() * 0.2) - 0.1, c = 'rgba(58,125,255,0.3)';
                        particlesArray.push(new Particle(x, y, dX, dY, s, c));
                    }
                }
                function animateParticles() { requestAnimationFrame(animateParticles); pctx.clearRect(0, 0, innerWidth, innerHeight); for (let i = 0; i < particlesArray.length; i++) { particlesArray[i].update(); } }

                // --- Deck Functionality ---
                const slides = document.querySelectorAll('.slide');
                const prevBtn = document.getElementById('prev-btn');
                const nextBtn = document.getElementById('next-btn');
                const slideCounter = document.getElementById('slide-counter');
                const backgroundGradient = document.getElementById('background-gradient');
                const animatedLogo = document.getElementById('animated-logo');
                let currentSlide = 0;
                const totalSlides = slides.length;
                const chartInstances = {};

                // --- Responsive Flywheel ---
                const flywheel = document.querySelector('.flywheel');
                const flywheelItems = document.querySelectorAll('.flywheel-item');
                function positionFlywheelItems() {
                    if (!flywheel) return;
                    const radius = flywheel.offsetWidth / 2.2; // Adjust radius for padding
                    const angleIncrement = 360 / flywheelItems.length;
                    flywheelItems.forEach((item, index) => {
                        const angle = index * angleIncrement - 90; // Start from top
                        const x = radius * Math.cos(angle * Math.PI / 180);
                        const y = radius * Math.sin(angle * Math.PI / 180);
                        item.style.transform = `translate(${x}px, ${y}px) translate(-50%, -50%)`;
                    });
                }

                function showSlide(index) {
                    slides.forEach((slide, i) => slide.classList.toggle('active', i === index));
                    animatedLogo.classList.toggle('active', index === 0);
                    slideCounter.textContent = `${(index + 1).toString().padStart(2, '0')} / ${totalSlides.toString().padStart(2, '0')}`;
                    const x = (index / (totalSlides - 1)) * 100;
                    backgroundGradient.style.transform = `translateX(calc(-50% + ${x}%))`;
                    
                    if (index === 3) { // Flywheel slide
                        requestAnimationFrame(positionFlywheelItems);
                    }

                    // Animate charts on their respective slides
                    const chartUpdaters = chartInstances[index];
                    if (chartUpdaters && Array.isArray(chartUpdaters)) {
                        setTimeout(() => chartUpdaters.forEach(c => c.update()), 400); // Delay for slide transition
                    }
                }
                function nextSlide() { currentSlide = (currentSlide + 1) % totalSlides; showSlide(currentSlide); }
                function prevSlide() { currentSlide = (currentSlide - 1 + totalSlides) % totalSlides; showSlide(currentSlide); }
                nextBtn.addEventListener('click', nextSlide);
                prevBtn.addEventListener('click', prevSlide);
                document.addEventListener('keydown', (e) => { if (e.key === 'ArrowRight' || e.key === ' ' || e.key === 'ArrowDown') { e.preventDefault(); nextSlide(); } else if (e.key === 'ArrowLeft' || e.key === 'ArrowUp') { e.preventDefault(); prevSlide(); } });

                // --- Chart.js Configurations ---
                const chartTextColor = '#A0A3BD', chartGridColor = 'rgba(255, 255, 255, 0.05)';
                Chart.defaults.font.family = "'Inter', sans-serif";
                const createBarGradient = (ctx, area, c1, c2) => { if (!area) return null; const g = ctx.createLinearGradient(0, area.bottom, 0, area.top); g.addColorStop(0, c1); g.addColorStop(1, c2); return g; };
                const animationConfig = { duration: 1200, easing: 'easeInOutQuart' };
                const commonChartOptions = (aspectRatio = 1.5) => ({ 
                    animation: false, 
                    responsive: true, 
                    maintainAspectRatio: true, 
                    aspectRatio: window.innerWidth < 768 ? 1.2 : aspectRatio, // More mobile-friendly aspect ratio
                    plugins: { legend: { display: false } }, 
                    scales: { 
                        y: { beginAtZero: true, ticks: { color: chartTextColor, font: {size: 10} }, grid: { color: chartGridColor } }, 
                        x: { ticks: { color: chartTextColor, font: {size: 10} }, grid: { display: false } } 
                    } 
                });

                chartInstances[5] = []; // For Portfolio Traction slide
                const digiClinicCtx = document.getElementById('digiClinicChart')?.getContext('2d');
                if (digiClinicCtx) { const c = new Chart(digiClinicCtx, { type: 'bar', data: { labels: ['FY24', 'FY25', 'FY26'], datasets: [{ label: 'Growth Factor (x)', data: [1, 10, 150], backgroundColor: (c) => createBarGradient(c.chart.ctx, c.chart.chartArea, 'rgba(58,125,255,0.2)', 'rgba(58,125,255,0.8)'), borderColor: '#3A7DFF', borderWidth: 1, borderRadius: 4 }] }, options: commonChartOptions() }); chartInstances[5].push({ update: () => c.update(animationConfig) }); }
                const motionXCtx = document.getElementById('motionXChart')?.getContext('2d');
                if (motionXCtx) { const c = new Chart(motionXCtx, { type: 'line', data: { labels: ['Q1', 'Q2', 'Q3', 'Q4'], datasets: [{ label: 'Revenue (₹ Cr)', data: [0.5, 1.2, 2.5, 4], fill: true, backgroundColor: (c) => createBarGradient(c.chart.ctx, c.chart.chartArea, 'rgba(0,194,255,0.0)', 'rgba(0,194,255,0.4)'), borderColor: '#00C2FF', tension: 0.4, pointBackgroundColor: '#00C2FF', pointRadius: 4 }] }, options: commonChartOptions() }); chartInstances[5].push({ update: () => c.update(animationConfig) }); }
                
                chartInstances[6] = []; // For NEST Ventures Traction slide
                const nestRevenueCtx = document.getElementById('nestRevenueChart')?.getContext('2d');
                if(nestRevenueCtx) { const c = new Chart(nestRevenueCtx, { type: 'line', data: { labels: ['FY25', 'FY26 Proj.'], datasets: [{ label: 'Revenue (₹ Cr)', data: [2.8, 5.6], fill: true, backgroundColor: (c) => createBarGradient(c.chart.ctx, c.chart.chartArea, 'rgba(58,125,255,0.0)', 'rgba(58,125,255,0.4)'), borderColor: '#3A7DFF', tension: 0.4, pointBackgroundColor: '#3A7DFF', pointRadius: 5 }] }, options: commonChartOptions(2) }); chartInstances[6].push({ update: () => c.update(animationConfig) }); }

                chartInstances[7] = []; // For The Ask slide
                const fundsCtx = document.getElementById('fundsChart')?.getContext('2d');
                if (fundsCtx) { const c = new Chart(fundsCtx, { type: 'doughnut', data: { labels: ['Portfolio Activation', 'Senior Hiring', 'New Startup Seeding', 'Platform & Infra'], datasets: [{ data: [60, 20, 15, 5], backgroundColor: ['#3A7DFF', '#00C2FF', '#2b5cb5', '#0082a8'], borderColor: '#0D0D12', borderWidth: 5, hoverOffset: 10 }] }, options: { animation: false, responsive: true, maintainAspectRatio: true, aspectRatio: window.innerWidth < 768 ? 1 : 1.5, cutout: '70%', plugins: { legend: { position: 'bottom', labels: { color: chartTextColor, padding: 15, font: {size: 10} } } } } }); chartInstances[7].push({ update: () => c.update(animationConfig) }); }

                // --- Initial calls and event listeners ---
                function handleResize() {
                    resizeCanvas();
                    initParticles();
                    positionFlywheelItems();
                    // Redraw active charts to adjust to new aspect ratios
                    const activeChartUpdaters = chartInstances[currentSlide];
                    if (activeChartUpdaters && Array.isArray(activeChartUpdaters)) {
                       activeChartUpdaters.forEach(instance => {
                           const chart = instance.update.toString().includes('c.update') ? Chart.getChart(instance.canvas) : null;
                           if(chart) {
                                chart.options.aspectRatio = window.innerWidth < 768 ? (chart.config.type === 'doughnut' ? 1 : 1.2) : 1.5;
                                chart.resize();
                           }
                       });
                    }
                }

                resizeCanvas();
                initParticles();
                animateParticles();
                positionFlywheelItems();
                showSlide(currentSlide);
                window.addEventListener('resize', handleResize);

            } catch (error) {
                console.error("An error occurred while initializing the presentation:", error);
                document.body.innerHTML = `<div style="color: white; text-align: center; padding: 2rem; height: 100vh; display: flex; flex-direction: column; justify-content: center; align-items: center;"><h1>Something went wrong</h1><p>We're sorry, but the presentation could not be loaded. Please try refreshing the page.</p><p style="color: #555; font-size: 0.8em; margin-top: 1rem;">Error: ${error.message}</p></div>`;
            }
        });
    </script>
</body>
</ht
