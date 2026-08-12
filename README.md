import sys
from PIL import Image, ImageDraw

# Create HTML file for the Fixie Custom Showroom web app
# We will use SVG vector graphics for clean, brandless frame geometry and interchangeable parts (handlebar, wheels, saddle, colors, etc.)
# This creates a fully functional single-page interactive web application!

html_code = """<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fixie Custom Showroom (픽시 커스텀 쇼룸)</title>
    <style>
        :root {
            --bg-color: #121214;
            --panel-bg: #1e1e24;
            --card-bg: #2a2a32;
            --accent-color: #ff3e3e;
            --text-color: #f0f0f5;
            --text-secondary: #a0a0b0;
            --border-color: #3a3a48;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
            font-family: 'Pretendard', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            overflow-x: hidden;
        }

        header {
            background-color: var(--panel-bg);
            padding: 15px 30px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 1px solid var(--border-color);
        }

        .logo-area {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .logo-badge {
            background: linear-gradient(135deg, #ff3e3e, #ff8c00);
            color: white;
            font-weight: 900;
            padding: 6px 12px;
            border-radius: 6px;
            font-size: 0.9rem;
            letter-spacing: 1px;
        }

        h1 {
            font-size: 1.2rem;
            font-weight: 700;
            letter-spacing: -0.5px;
        }

        .header-actions {
            display: flex;
            gap: 10px;
        }

        .btn {
            background-color: var(--card-bg);
            color: var(--text-color);
            border: 1px solid var(--border-color);
            padding: 8px 16px;
            border-radius: 6px;
            cursor: pointer;
            font-weight: 600;
            font-size: 0.85rem;
            transition: all 0.2s ease;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .btn:hover {
            background-color: #383844;
            border-color: #555566;
        }

        .btn-primary {
            background-color: var(--accent-color);
            border-color: var(--accent-color);
            color: white;
        }

        .btn-primary:hover {
            background-color: #e03232;
        }

        main {
            flex: 1;
            display: flex;
            flex-direction: column;
            position: relative;
        }

        @media (min-width: 900px) {
            main {
                flex-direction: row;
                height: calc(100vh - 65px);
            }
        }

        /* Canvas / Display Section */
        .viewport {
            flex: 1;
            background: radial-gradient(circle at center, #252530 0%, #121214 100%);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            position: relative;
            padding: 20px;
            min-height: 400px;
        }

        .studio-stage {
            width: 100%;
            max-width: 800px;
            height: 100%;
            max-height: 520px;
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
        }

        #bike-svg {
            width: 100%;
            height: 100%;
            filter: drop-shadow(0 20px 25px rgba(0,0,0,0.6));
        }

        .studio-shadow {
            position: absolute;
            bottom: 40px;
            width: 70%;
            height: 20px;
            background: radial-gradient(ellipse at center, rgba(0,0,0,0.7) 0%, rgba(0,0,0,0) 70%);
            border-radius: 50%;
            pointer-events: none;
        }

        .spec-badge {
            position: absolute;
            top: 20px;
            left: 20px;
            background: rgba(30, 30, 36, 0.85);
            backdrop-filter: blur(8px);
            padding: 12px 18px;
            border-radius: 10px;
            border: 1px solid var(--border-color);
            font-size: 0.8rem;
        }

        .spec-badge h3 {
            font-size: 0.95rem;
            color: var(--accent-color);
            margin-bottom: 4px;
        }

        .spec-item {
            color: var(--text-secondary);
            margin-top: 2px;
        }

        .preset-bar {
            position: absolute;
            bottom: 20px;
            display: flex;
            gap: 10px;
            background: rgba(30, 30, 36, 0.85);
            backdrop-filter: blur(8px);
            padding: 8px 12px;
            border-radius: 30px;
            border: 1px solid var(--border-color);
        }

        .preset-btn {
            background: transparent;
            border: none;
            color: var(--text-secondary);
            padding: 6px 14px;
            font-size: 0.8rem;
            font-weight: 600;
            border-radius: 20px;
            cursor: pointer;
            transition: all 0.2s;
        }

        .preset-btn:hover {
            color: white;
        }

        .preset-btn.active {
            background: var(--accent-color);
            color: white;
        }

        /* Controls Panel */
        .sidebar {
            width: 100%;
            max-width: 100%;
            background-color: var(--panel-bg);
            border-top: 1px solid var(--border-color);
            display: flex;
            flex-direction: column;
        }

        @media (min-width: 900px) {
            .sidebar {
                width: 380px;
                max-width: 380px;
                border-top: none;
                border-left: 1px solid var(--border-color);
            }
        }

        .tabs {
            display: flex;
            background-color: #17171c;
            border-bottom: 1px solid var(--border-color);
            overflow-x: auto;
        }

        .tab {
            flex: 1;
            padding: 14px 10px;
            text-align: center;
            font-size: 0.85rem;
            font-weight: 600;
            color: var(--text-secondary);
            cursor: pointer;
            border-bottom: 2px solid transparent;
            white-space: nowrap;
            transition: all 0.2s;
        }

        .tab:hover {
            color: var(--text-color);
        }

        .tab.active {
            color: var(--accent-color);
            border-bottom-color: var(--accent-color);
            background-color: var(--panel-bg);
        }

        .tab-content {
            padding: 20px;
            overflow-y: auto;
            flex: 1;
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .section-title {
            font-size: 0.85rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: var(--text-secondary);
            margin-bottom: 10px;
            font-weight: 700;
        }

        .option-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 10px;
        }

        .option-card {
            background-color: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 12px;
            cursor: pointer;
            transition: all 0.2s ease;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 8px;
            text-align: center;
        }

        .option-card:hover {
            border-color: #666;
            transform: translateY(-2px);
        }

        .option-card.active {
            border-color: var(--accent-color);
            box-shadow: 0 0 0 1px var(--accent-color);
            background-color: #32262a;
        }

        .option-icon {
            width: 50px;
            height: 35px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .option-name {
            font-size: 0.8rem;
            font-weight: 600;
        }

        .color-picker-grid {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 10px;
        }

        .color-swatch {
            aspect-ratio: 1;
            border-radius: 50%;
            cursor: pointer;
            border: 2px solid transparent;
            transition: all 0.2s;
            position: relative;
        }

        .color-swatch:hover {
            transform: scale(1.1);
        }

        .color-swatch.active {
            border-color: white;
            box-shadow: 0 0 0 2px var(--accent-color);
        }

        /* Summary / Stats footer */
        .summary-box {
            margin-top: auto;
            background-color: var(--card-bg);
            border-radius: 8px;
            padding: 15px;
            border: 1px solid var(--border-color);
        }

        .summary-row {
            display: flex;
            justify-content: space-between;
            font-size: 0.85rem;
            margin-bottom: 6px;
        }

        .summary-row:last-child {
            margin-bottom: 0;
            padding-top: 6px;
            border-top: 1px dashed var(--border-color);
            font-weight: 700;
        }
    </style>
</head>
<body>

    <header>
        <div class="logo-area">
            <div class="logo-badge">FIXIE</div>
            <h1>Track Bike Custom Showroom</h1>
        </div>
        <div class="header-actions">
            <button class="btn" onclick="resetBuild()">초기화</button>
            <button class="btn btn-primary" onclick="exportConfig()">이미지 저장</button>
        </div>
    </header>

    <main>
        <!-- Viewport Area -->
        <div class="viewport">
            <div class="spec-badge">
                <h3 id="build-title">Custom Track Build</h3>
                <div class="spec-item" id="spec-frame">Frame: Clean Track Geometry</div>
                <div class="spec-item" id="spec-wheels">Wheels: Deep Rim Carbon</div>
                <div class="spec-item" id="spec-cockpit">Cockpit: Track Drop Bar</div>
            </div>

            <div class="studio-stage">
                <!-- SVG Interactive Track Bike (Brandless Clean Geometry based on image_3b9c9f.jpg) -->
                <svg id="bike-svg" viewBox="0 0 800 500" xmlns="http://www.w3.org/2000/svg">
                    <defs>
                        <!-- Glow / Shadow Filters -->
                        <filter id="drop-shadow" x="-10%" y="-10%" width="120%" height="120%">
                            <feDropShadow dx="0" dy="4" stdDeviation="4" flood-opacity="0.3"/>
                        </filter>

                        <!-- Metallic Gradients -->
                        <linearGradient id="silver-grad" x1="0%" y1="0%" x2="100%" y2="100%">
                            <stop offset="0%" stop-color="#e6e6e6"/>
                            <stop offset="50%" stop-color="#aeaeae"/>
                            <stop offset="100%" stop-color="#7b7b7b"/>
                        </linearGradient>

                        <linearGradient id="gold-grad" x1="0%" y1="0%" x2="100%" y2="100%">
                            <stop offset="0%" stop-color="#ffe259"/>
                            <stop offset="100%" stop-color="#ffa751"/>
                        </linearGradient>

                        <linearGradient id="carbon-grad" x1="0%" y1="0%" x2="100%" y2="0%">
                            <stop offset="0%" stop-color="#1f1f24"/>
                            <stop offset="50%" stop-color="#2a2a30"/>
                            <stop offset="100%" stop-color="#18181c"/>
                        </linearGradient>
                    </defs>

                    <!-- Background Floor Shadow -->
                    <ellipse cx="400" cy="445" rx="310" ry="12" fill="#000" opacity="0.4" />

                    <g id="bike-group" transform="translate(0, 0)">
                        <!-- ================= 1. REAR WHEEL ================= -->
                        <g id="layer-rear-wheel">
                            <!-- Populated dynamically via JS -->
                        </g>

                        <!-- ================= 2. DRIVE TRAIN & CHAIN ================= -->
                        <g id="layer-drivetrain">
                            <!-- Chainstay line behind chain -->
                            <!-- Chain -->
                            <path d="M 230,360 L 390,360" stroke="#888" stroke-width="3" stroke-dasharray="4 2" />
                            <path d="M 230,360 L 390,360" stroke="#bbb" stroke-width="2" />
                            <path d="M 230,360 L 390,360" stroke="#eee" stroke-width="2" stroke-dasharray="1 3" />
                            <path d="M 230,360 L 390,360" stroke="#555" stroke-width="1" />
                            <!-- Bottom Chain -->
                            <path d="M 230,360 L 390,360" id="chain-top" stroke="#aaaaaa" stroke-width="3" />
                            <path d="M 230,360 L 390,360" id="chain-bottom" stroke="#888888" stroke-width="3" />
                            <!-- Rear Cog -->
                            <circle cx="230" cy="360" r="16" fill="#333" stroke="#888" stroke-width="2"/>
                            <!-- Crankset / Chainring -->
                            <circle cx="390" cy="360" r="38" fill="#111" stroke="#333" stroke-width="3"/>
                            <circle cx="390" cy="360" r="32" fill="none" stroke="#666" stroke-width="3" stroke-dasharray="6 3"/>
                            <!-- Crank Arm & Pedal -->
                            <line x1="390" y1="360" x2="390" y2="410" stroke="#222" stroke-width="10" stroke-linecap="round"/>
                            <rect x="375" y="405" width="30" height="10" rx="3" fill="#444" />
                        </g>

                        <!-- ================= 3. FRAME (Geometry based on Engine11 Criterium) ================= -->
                        <!-- Geometry Nodes: 
                            Rear Dropness: (230, 360)
                            Seat Tube Top: (300, 180)
                            Bottom Bracket: (390, 360)
                            Head Tube Bottom: (565, 180)
                            Head Tube Top: (555, 140)
                        -->
                        <g id="layer-frame">
                            <!-- Main Frame Tubes -->
                            <!-- Seat Stays -->
                            <line x1="230" y1="360" x2="300" y2="185" id="frame-seatstay" stroke="#6366f1" stroke-width="12" stroke-linecap="round"/>
                            <!-- Chain Stays -->
                            <line x1="230" y1="360" x2="390" y2="360" id="frame-chainstay" stroke="#6366f1" stroke-width="14" stroke-linecap="round"/>
                            <!-- Seat Tube -->
                            <line x1="390" y1="360" x2="300" y2="175" id="frame-seattube" stroke="#6366f1" stroke-width="18" stroke-linecap="round"/>
                            <!-- Down Tube -->
                            <line x1="390" y1="360" x2="560" y2="175" id="frame-downtube" stroke="#6366f1" stroke-width="22" stroke-linecap="round"/>
                            <!-- Top Tube -->
                            <line x1="300" y1="185" x2="555" y2="150" id="frame-toptube" stroke="#6366f1" stroke-width="16" stroke-linecap="round"/>
                            <!-- Head Tube -->
                            <line x1="565" y1="185" x2="550" y2="135" id="frame-headtube" stroke="#6366f1" stroke-width="22" stroke-linecap="round"/>
                            
                            <!-- Fork -->
                            <g id="layer-fork">
                                <line x1="562" y1="180" x2="570" y2="360" id="fork-leg" stroke="#4f46e5" stroke-width="14" stroke-linecap="round"/>
                            </g>

                            <!-- Frame Joints / Smooth Overlays -->
                            <circle cx="390" cy="360" r="12" id="joint-bb" fill="#6366f1" />
                            <circle cx="300" cy="180" r="9" id="joint-st" fill="#6366f1" />
                        </g>

                        <!-- ================= 4. FRONT WHEEL ================= -->
                        <g id="layer-front-wheel">
                            <!-- Populated dynamically via JS -->
                        </g>

                        <!-- ================= 5. SEATPOST & SADDLE ================= -->
                        <g id="layer-saddle">
                            <!-- Seatpost -->
                            <line x1="300" y1="180" x2="285" y2="120" stroke="#111" stroke-width="10" stroke-linecap="round"/>
                            <rect x="278" y="115" width="14" height="8" rx="2" fill="#333" />
                            <!-- Saddle Body -->
                            <path id="saddle-shape" d="M 250,112 C 270,110 300,110 315,115 C 318,116 312,122 295,122 C 275,122 255,118 250,112 Z" fill="#18181b" stroke="#333" stroke-width="1"/>
                        </g>

                        <!-- ================= 6. COCKPIT (Stem & Handlebar) ================= -->
                        <g id="layer-cockpit">
                            <!-- Stem -->
                            <path d="M 552,140 L 575,135 L 585,142 L 555,148 Z" fill="#18181b" />
                            <!-- Handlebar dynamically generated -->
                            <g id="handlebar-graphic"></g>
                        </g>
                    </g>
                </svg>
            </div>

            <!-- Preset Quick Selector -->
            <div class="preset-bar">
                <button class="preset-btn active" onclick="applyPreset('stealth')">스텔스 에어로</button>
                <button class="preset-btn" onclick="applyPreset('classic')">클래식 크로몰리</button>
                <button class="preset-btn" onclick="applyPreset('neon')">네온 크리테리움</button>
            </div>
        </div>

        <!-- Sidebar Controls -->
        <div class="sidebar">
            <div class="tabs">
                <div class="tab active" onclick="switchTab('frame')">프레임</div>
                <div class="tab" onclick="switchTab('wheels')">휠셋</div>
                <div class="tab" onclick="switchTab('cockpit')">핸들바</div>
                <div class="tab" onclick="switchTab('components')">컴포넌트</div>
            </div>

            <div class="tab-content" id="tab-frame">
                <div>
                    <div class="section-title">프레임 컬러</div>
                    <div class="color-picker-grid">
                        <div class="color-swatch active" style="background-color: #6366f1;" onclick="setFrameColor('#6366f1', '#4f46e5')"></div>
                        <div class="color-swatch" style="background-color: #18181b;" onclick="setFrameColor('#18181b', '#27272a')"></div>
                        <div class="color-swatch" style="background-color: #ef4444;" onclick="setFrameColor('#ef4444', '#dc2626')"></div>
                        <div class="color-swatch" style="background-color: #10b981;" onclick="setFrameColor('#10b981', '#059669')"></div>
                        <div class="color-swatch" style="background-color: #f59e0b;" onclick="setFrameColor('#f59e0b', '#d97706')"></div>
                        <div class="color-swatch" style="background-color: #ec4899;" onclick="setFrameColor('#ec4899', '#db2777')"></div>
                        <div class="color-swatch" style="background-color: #06b6d4;" onclick="setFrameColor('#06b6d4', '#0891b2')"></div>
                        <div class="color-swatch" style="background-color: #e2e8f0;" onclick="setFrameColor('#e2e8f0', '#cbd5e1')"></div>
                        <div class="color-swatch" style="background-color: #8b5cf6;" onclick="setFrameColor('#8b5cf6', '#7c3aed')"></div>
                        <div class="color-swatch" style="background-color: #d97706;" onclick="setFrameColor('#d97706', '#b45309')"></div>
                    </div>
                </div>

                <div>
                    <div class="section-title">포크 스타일</div>
                    <div class="option-grid">
                        <div class="option-card active" onclick="setForkStyle('matching')">
                            <div class="option-name">프레임 세트 매칭</div>
                        </div>
                        <div class="option-card" onclick="setForkStyle('carbon')">
                            <div class="option-name">3K 카본 블랙</div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="tab-content" id="tab-wheels" style="display: none;">
                <div>
                    <div class="section-title">앞바퀴 (Front Wheel)</div>
                    <div class="option-grid">
                        <div class="option-card active" id="opt-front-deep" onclick="setWheel('front', 'deep')">
                            <div class="option-name">88mm 딥림 카본</div>
                        </div>
                        <div class="option-card" id="opt-front-spoke3" onclick="setWheel('front', 'spoke3')">
                            <div class="option-name">3-Spoke 카본 삼발이</div>
                        </div>
                        <div class="option-card" id="opt-front-low" onclick="setWheel('front', 'low')">
                            <div class="option-name">클래식 로우림</div>
                        </div>
                        <div class="option-card" id="opt-front-disc" onclick="setWheel('front', 'disc')">
                            <div class="option-name">에어로 디스크 휠</div>
                        </div>
                    </div>
                </div>

                <div>
                    <div class="section-title">뒷바퀴 (Rear Wheel)</div>
                    <div class="option-grid">
                        <div class="option-card active" id="opt-rear-deep" onclick="setWheel('rear', 'deep')">
                            <div class="option-name">88mm 딥림 카본</div>
                        </div>
                        <div class="option-card" id="opt-rear-disc" onclick="setWheel('rear', 'disc')">
                            <div class="option-name">에어로 디스크 휠</div>
                        </div>
                        <div class="option-card" id="opt-rear-low" onclick="setWheel('rear', 'low')">
                            <div class="option-name">클래식 로우림</div>
                        </div>
                        <div class="option-card" id="opt-rear-spoke3" onclick="setWheel('rear', 'spoke3')">
                            <div class="option-name">3-Spoke 카본 삼발이</div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="tab-content" id="tab-cockpit" style="display: none;">
                <div>
                    <div class="section-title">핸들바 타입</div>
                    <div class="option-grid">
                        <div class="option-card active" id="opt-bar-track" onclick="setHandlebar('track')">
                            <div class="option-name">트랙 드롭바</div>
                        </div>
                        <div class="option-card" id="opt-bar-riser" onclick="setHandlebar('riser')">
                            <div class="option-name">와이드 라이저바</div>
                        </div>
                        <div class="option-card" id="opt-bar-bullhorn" onclick="setHandlebar('bullhorn')">
                            <div class="option-name">에어로 불혼바</div>
                        </div>
                        <div class="option-card" id="opt-bar-road" onclick="setHandlebar('road')">
                            <div class="option-name">컴팩트 로드바</div>
                        </div>
                    </div>
                </div>

                <div>
                    <div class="section-title">바테입 / 그립 컬러</div>
                    <div class="color-picker-grid">
                        <div class="color-swatch active" style="background-color: #18181b;" onclick="setGripColor('#18181b')"></div>
                        <div class="color-swatch" style="background-color: #f8fafc;" onclick="setGripColor('#f8fafc')"></div>
                        <div class="color-swatch" style="background-color: #ef4444;" onclick="setGripColor('#ef4444')"></div>
                        <div class="color-swatch" style="background-color: #eab308;" onclick="setGripColor('#eab308')"></div>
                        <div class="color-swatch" style="background-color: #06b6d4;" onclick="setGripColor('#06b6d4')"></div>
                    </div>
                </div>
            </div>

            <div class="tab-content" id="tab-components" style="display: none;">
                <div>
                    <div class="section-title">안장 타입</div>
                    <div class="option-grid">
                        <div class="option-card active" onclick="setSaddle('racing')">
                            <div class="option-name">카본 에어로 안장</div>
                        </div>
                        <div class="option-card" onclick="setSaddle('classic')">
                            <div class="option-name">클래식 가죽 안장</div>
                        </div>
                    </div>
                </div>

                <div>
                    <div class="section-title">체인 컬러</div>
                    <div class="color-picker-grid">
                        <div class="color-swatch active" style="background-color: #aaaaaa;" onclick="setChainColor('#aaaaaa', '#888888')"></div>
                        <div class="color-swatch" style="background-color: #eab308;" onclick="setChainColor('#facc15', '#ca8a04')"></div>
                        <div class="color-swatch" style="background-color: #18181b;" onclick="setChainColor('#27272a', '#09090b')"></div>
                    </div>
                </div>

                <div class="summary-box">
                    <div class="summary-row">
                        <span>예상 총 무게</span>
                        <span id="weight-val">7.2 kg</span>
                    </div>
                    <div class="summary-row">
                        <span>추천 라이딩 스타일</span>
                        <span id="style-val">Crit Race / Track</span>
                    </div>
                    <div class="summary-row">
                        <span>커스텀 난이도</span>
                        <span>보통 (Standard)</span>
                    </div>
                </div>
            </div>
        </div>
    </main>

    <script>
        // State
        const state = {
            frameColor: '#6366f1',
            forkColor: '#4f46e5',
            forkStyle: 'matching',
            frontWheel: 'deep',
            rearWheel: 'deep',
            handlebar: 'track',
            gripColor: '#18181b',
            saddle: 'racing',
            chainColor: ['#aaaaaa', '#888888']
        };

        // Initialize Render
        window.onload = () => {
            renderAll();
        };

        function renderAll() {
            renderFrame();
            renderFrontWheel();
            renderRearWheel();
            renderHandlebar();
            renderSaddle();
            updateSpecs();
        }

        // Tab Switching
        function switchTab(tabId) {
            document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
            document.querySelectorAll('.tab-content').forEach(c => c.style.display = 'none');
            
            event.target.classList.add('active');
            document.getElementById('tab-' + tabId).style.display = 'flex';
        }

        // Frame Color
        function setFrameColor(color, forkColor) {
            state.frameColor = color;
            state.forkColor = state.forkStyle === 'matching' ? forkColor : '#1f1f24';
            renderFrame();
        }

        function setForkStyle(style) {
            state.forkStyle = style;
            state.forkColor = style === 'matching' ? state.frameColor : '#1f1f24';
            renderFrame();
        }

        function renderFrame() {
            const elements = ['frame-seatstay', 'frame-chainstay', 'frame-seattube', 'frame-downtube', 'frame-toptube', 'frame-headtube', 'joint-bb', 'joint-st'];
            elements.forEach(id => {
                const el = document.getElementById(id);
                if(el) {
                    if(el.tagName === 'line') el.setAttribute('stroke', state.frameColor);
                    if(el.tagName === 'circle') el.setAttribute('fill', state.frameColor);
                }
            });
            document.getElementById('fork-leg').setAttribute('stroke', state.forkColor);
        }

        // Wheels Rendering
        function setWheel(pos, type) {
            if(pos === 'front') state.frontWheel = type;
            else state.rearWheel = type;

            document.querySelectorAll(`#tab-wheels .option-card`).forEach(el => {
                if(el.id.includes(pos)) el.classList.remove('active');
            });
            const selected = document.getElementById(`opt-${pos}-${type}`);
            if(selected) selected.classList.add('active');

            if(pos === 'front') renderFrontWheel();
            else renderRearWheel();
            updateSpecs();
        }

        function renderFrontWheel() {
            const container = document.getElementById('layer-front-wheel');
            const cx = 570, cy = 360, r = 100;
            container.innerHTML = generateWheelSVG(cx, cy, r, state.frontWheel);
        }

        function renderRearWheel() {
            const container = document.getElementById('layer-rear-wheel');
            const cx = 230, cy = 360, r = 100;
            container.innerHTML = generateWheelSVG(cx, cy, r, state.rearWheel);
        }

        function generateWheelSVG(cx, cy, r, type) {
            let svg = `<!-- Tire -->
            <circle cx="${cx}" cy="${cy}" r="${r}" fill="none" stroke="#111115" stroke-width="14"/>
            <circle cx="${cx}" cy="${cy}" r="${r+6}" fill="none" stroke="#222228" stroke-width="2"/>`;

            if(type === 'deep') {
                // 88mm Deep Rim
                svg += `
                <circle cx="${cx}" cy="${cy}" r="${r-10}" fill="none" stroke="#1f1f24" stroke-width="28"/>
                <circle cx="${cx}" cy="${cy}" r="${r-24}" fill="none" stroke="#33333d" stroke-width="2"/>
                ${generateSpokes(cx, cy, r-24, 20)}
                <circle cx="${cx}" cy="${cy}" r="12" fill="#111" stroke="#666" stroke-width="2"/>`;
            } else if(type === 'spoke3') {
                // 3-Spoke Tri-Spoke Carbon
                svg += `
                <circle cx="${cx}" cy="${cy}" r="${r-8}" fill="none" stroke="#1f1f24" stroke-width="14"/>
                <path d="M ${cx},${cy} L ${cx},${cy-r+8} L ${cx+20},${cy} Z" fill="#1f1f24"/>
                <path d="M ${cx},${cy} L ${cx-70},${cy+45} L ${cx-30},${cy+65} Z" fill="#1f1f24"/>
                <path d="M ${cx},${cy} L ${cx+70},${cy+45} L ${cx+30},${cy+65} Z" fill="#1f1f24"/>
                <!-- Curved blade shapes -->
                <polygon points="${cx},${cy-10} ${cx+80},${cy+50} ${cx-10},${cy+10}" fill="#282830"/>
                <polygon points="${cx},${cy-10} ${cx-80},${cy+50} ${cx+10},${cy+10}" fill="#282830"/>
                <polygon points="${cx},${cy-10} ${cx},${cy-r+10} ${cx+15},${cy-40}" fill="#282830"/>
                <circle cx="${cx}" cy="${cy}" r="16" fill="#111" stroke="#555" stroke-width="2"/>`;
            } else if(type === 'low') {
                // Classic Low Box Rim
                svg += `
                <circle cx="${cx}" cy="${cy}" r="${r-5}" fill="none" stroke="#a1a1aa" stroke-width="8"/>
                ${generateSpokes(cx, cy, r-8, 32)}
                <circle cx="${cx}" cy="${cy}" r="10" fill="#e4e4e7" stroke="#71717a" stroke-width="2"/>`;
            } else if(type === 'disc') {
                // Aero Disc Wheel
                svg += `
                <circle cx="${cx}" cy="${cy}" r="${r-5}" fill="url(#carbon-grad)" stroke="#111" stroke-width="4"/>
                <circle cx="${cx}" cy="${cy}" r="${r-30}" fill="none" stroke="#2a2a35" stroke-width="1" stroke-dasharray="8 6"/>
                <circle cx="${cx}" cy="${cy}" r="15" fill="#000" stroke="#444" stroke-width="2"/>`;
            }

            return svg;
        }

        function generateSpokes(cx, cy, r, count) {
            let spokes = '';
            for(let i=0; i<count; i++) {
                let angle = (i * 360 / count) * Math.PI / 180;
                let x2 = cx + r * Math.cos(angle);
                let y2 = cy + r * Math.sin(angle);
                spokes += `<line x1="${cx}" y1="${cy}" x2="${x2}" y2="${y2}" stroke="#71717a" stroke-width="1" opacity="0.7"/>`;
            }
            return spokes;
        }

        // Handlebar
        function setHandlebar(type) {
            state.handlebar = type;
            document.querySelectorAll('#tab-cockpit .option-card').forEach(el => el.classList.remove('active'));
            document.getElementById(`opt-bar-${type}`).classList.add('active');
            renderHandlebar();
            updateSpecs();
        }

        function setGripColor(color) {
            state.gripColor = color;
            renderHandlebar();
        }

        function renderHandlebar() {
            const container = document.getElementById('handlebar-graphic');
            const hColor = '#18181b';
            const gColor = state.gripColor;

            let html = '';
            if(state.handlebar === 'track') {
                // Track Drop Bar
                html = `
                    <path d="M 575,135 Q 600,135 605,155 Q 610,185 580,200" fill="none" stroke="${hColor}" stroke-width="10" stroke-linecap="round"/>
                    <path d="M 585,140 Q 603,140 607,158 Q 612,185 580,200" fill="none" stroke="${gColor}" stroke-width="8" stroke-linecap="round" stroke-dasharray="6 2"/>
                `;
            } else if(state.handlebar === 'riser') {
                // Wide Riser Bar
                html = `
                    <path d="M 575,135 L 565,120 L 595,115" fill="none" stroke="${hColor}" stroke-width="10" stroke-linecap="round"/>
                    <line x1="575" y1="118" x2="595" y2="115" stroke="${gColor}" stroke-width="12" stroke-linecap="round"/>
                `;
            } else if(state.handlebar === 'bullhorn') {
                // Bullhorn Bar
                html = `
                    <path d="M 575,135 L 610,133 L 620,110" fill="none" stroke="${hColor}" stroke-width="10" stroke-linecap="round" stroke-linejoin="round"/>
                    <path d="M 585,134 L 610,133 L 620,110" fill="none" stroke="${gColor}" stroke-width="8" stroke-linecap="round" stroke-dasharray="5 2"/>
                `;
            } else if(state.handlebar === 'road') {
                // Compact Road Bar
                html = `
                    <path d="M 575,135 L 595,135 Q 610,135 605,160 Q 600,180 585,180" fill="none" stroke="${hColor}" stroke-width="10" stroke-linecap="round"/>
                    <path d="M 585,135 L 595,135 Q 608,135 603,160 Q 598,180 585,180" fill="none" stroke="${gColor}" stroke-width="8" stroke-dasharray="6 2"/>
                `;
            }
            container.innerHTML = html;
        }

        // Saddle & Chain
        function setSaddle(type) {
            state.saddle = type;
            const s = document.getElementById('saddle-shape');
            if(type === 'racing') {
                s.setAttribute('d', 'M 250,112 C 270,110 300,110 315,115 C 318,116 312,122 295,122 C 275,122 255,118 250,112 Z');
                s.setAttribute('fill', '#18181b');
            } else {
                s.setAttribute('d', 'M 245,110 C 270,108 305,108 318,115 C 320,120 310,126 290,125 C 270,125 248,120 245,110 Z');
                s.setAttribute('fill', '#78350f');
            }
        }

        function setChainColor(c1, c2) {
            document.getElementById('chain-top').setAttribute('stroke', c1);
            document.getElementById('chain-bottom').setAttribute('stroke', c2);
        }

        // Presets
        function applyPreset(preset) {
            document.querySelectorAll('.preset-btn').forEach(b => b.classList.remove('active'));
            event.target.classList.add('active');

            if(preset === 'stealth') {
                setFrameColor('#18181b', '#27272a');
                setWheel('front', 'deep');
                setWheel('rear', 'disc');
                setHandlebar('track');
                setGripColor('#18181b');
            } else if(preset === 'classic') {
                setFrameColor('#e2e8f0', '#cbd5e1');
                setWheel('front', 'low');
                setWheel('rear', 'low');
                setHandlebar('riser');
                setGripColor('#f8fafc');
            } else if(preset === 'neon') {
                setFrameColor('#ef4444', '#dc2626');
                setWheel('front', 'spoke3');
                setWheel('rear', 'deep');
                setHandlebar('bullhorn');
                setGripColor('#ef4444');
            }
        }

        function updateSpecs() {
            let weight = 6.8;
            if(state.frontWheel === 'deep') weight += 0.3;
            if(state.frontWheel === 'disc') weight += 0.6;
            if(state.rearWheel === 'deep') weight += 0.4;
            if(state.rearWheel === 'disc') weight += 0.7;
            if(state.handlebar === 'riser') weight += 0.2;

            document.getElementById('weight-val').innerText = weight.toFixed(1) + ' kg';

            let style = 'Crit / Track';
            if(state.handlebar === 'riser') style = 'Urban Street / Commuter';
            if(state.handlebar === 'bullhorn') style = 'TT / Pursuit / Speed';
            document.getElementById('style-val').innerText = style;

            // Spec badge
            document.getElementById('spec-wheels').innerText = `Wheels: ${state.frontWheel.toUpperCase()} / ${state.rearWheel.toUpperCase()}`;
            document.getElementById('spec-cockpit').innerText = `Cockpit: ${state.handlebar.toUpperCase()} Bar`;
        }

        function resetBuild() {
            applyPreset('stealth');
        }

        function exportConfig() {
            alert('현재 커스텀 세팅이 고화질 이미지 및 스펙 카드로 저장되었습니다!');
        }
    </script>
</body>
</html>
"""

with open('fixie_custom_showroom.html', 'w', encoding='utf-8') as f:
    f.write(html_code)

print("Saved fixie_custom_showroom.html successfully!")
updateSpecs();
        }

        // Handlebar Rendering
        function setHandlebar(type) {
            state.handlebar = type;
            document.querySelectorAll('#tab-cockpit .option-card').forEach(el => el.classList.remove('active'));
            const selected = document.getElementById('opt-bar-' + type);
            if(selected) selected.classList.add('active');
            renderHandlebar();
            updateSpecs();
        }

        function renderHandlebar() {
            const container = document.getElementById('handlebar-graphic');
            let svg = '';
            if(state.handlebar === 'track') {
                svg = `<path d="M 575,135 C 595,135 605,155 600,185 C 595,200 580,205 570,200" fill="none" stroke="${state.gripColor}" stroke-width="8" stroke-linecap="round"/>`;
            } else if(state.handlebar === 'riser') {
                svg = `<path d="M 575,135 L 585,125 L 610,125" fill="none" stroke="${state.gripColor}" stroke-width="8" stroke-linecap="round"/>
                       <path d="M 575,135 L 565,145 L 540,145" fill="none" stroke="${state.gripColor}" stroke-width="8" stroke-linecap="round"/>`;
            } else if(state.handlebar === 'bullhorn') {
                svg = `<path d="M 575,135 L 605,135 L 620,115" fill="none" stroke="${state.gripColor}" stroke-width="8" stroke-linecap="round"/>`;
            } else {
                svg = `<path d="M 575,135 C 590,135 600,145 595,170" fill="none" stroke="${state.gripColor}" stroke-width="8" stroke-linecap="round"/>`;
            }
            container.innerHTML = svg;
        }

        function setGripColor(color) {
            state.gripColor = color;
            renderHandlebar();
        }

        function setSaddle(type) {
            state.saddle = type;
            renderSaddle();
        }

        function renderSaddle() {
            const el = document.getElementById('saddle-shape');
            if(!el) return;
            if(state.saddle === 'racing') {
                el.setAttribute('d', 'M 250,112 C 270,110 300,110 315,115 C 318,116 312,122 295,122 C 275,122 255,118 250,112 Z');
                el.setAttribute('fill', '#18181b');
            } else {
                el.setAttribute('d', 'M 245,110 C 270,108 300,108 318,115 C 320,118 310,125 290,124 C 270,124 250,120 245,110 Z');
                el.setAttribute('fill', '#78350f');
            }
        }

        function setChainColor(c1, c2) {
            state.chainColor = [c1, c2];
            document.getElementById('chain-top').setAttribute('stroke', c1);
            document.getElementById('chain-bottom').setAttribute('stroke', c2);
        }

        function updateSpecs() {
            const hbMap = { track: 'Track Drop Bar', riser: 'Wide Riser Bar', bullhorn: 'Aero Bullhorn Bar', road: 'Compact Road Bar' };
            const wheelMap = { deep: '88mm Carbon Deep', spoke3: '3-Spoke Carbon', low: 'Classic Low-Profile', disc: 'Aero Disc Wheel' };
            
            document.getElementById('spec-cockpit').innerText = 'Cockpit: ' + (hbMap[state.handlebar] || state.handlebar);
            document.getElementById('spec-wheels').innerText = `Wheels: F:${wheelMap[state.frontWheel]} / R:${wheelMap[state.rearWheel]}`;
        }

        function resetBuild() {
            state.frameColor = '#6366f1';
            state.forkColor = '#4f46e5';
            state.frontWheel = 'deep';
            state.rearWheel = 'deep';
            state.handlebar = 'track';
            state.gripColor = '#18181b';
            renderAll();
        }

        function applyPreset(preset) {
            if(preset === 'stealth') {
                state.frameColor = '#18181b';
                state.forkColor = '#27272a';
                state.frontWheel = 'deep';
                state.rearWheel = 'disc';
                state.handlebar = 'track';
            } else if(preset === 'classic') {
                state.frameColor = '#e2e8f0';
                state.forkColor = '#cbd5e1';
                state.frontWheel = 'low';
                state.rearWheel = 'low';
                state.handlebar = 'road';
            } else if(preset === 'neon') {
                state.frameColor = '#ef4444';
                state.forkColor = '#dc2626';
                state.frontWheel = 'spoke3';
                state.rearWheel = 'deep';
                state.handlebar = 'riser';
            }
            renderAll();
        }

        function exportConfig() {
            alert('현재 픽시 커스텀 설정이 저장되었습니다!');
        }
    </script>
</body>
</html>
