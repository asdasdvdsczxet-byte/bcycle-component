<!DOCTYPE html>
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

        .preset-btn.active {
            background: var(--accent-color);
            color: white;
        }

        .sidebar {
            width: 100%;
            background-color: var(--panel-bg);
            border-top: 1px solid var(--border-color);
            display: flex;
            flex-direction: column;
        }

        @media (min-width: 900px) {
            .sidebar {
                width: 380px;
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
            text-align: center;
        }

        .option-card.active {
            border-color: var(--accent-color);
            background-color: #32262a;
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
        }

        .color-swatch.active {
            border-color: white;
            box-shadow: 0 0 0 2px var(--accent-color);
        }

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
    </style>
</head>
<body>

    <header>
        <div class="logo-area">
            <div class="logo-badge">FIXIE</div>
            <h1>Engine 11 Sprinter Custom Showroom</h1>
        </div>
        <div class="header-actions">
            <button class="btn" onclick="resetBuild()">초기화</button>
            <button class="btn btn-primary" onclick="exportConfig()">이미지 저장</button>
        </div>
    </header>

    <main>
        <div class="viewport">
            <div class="spec-badge">
                <h3 id="build-title">Engine 11 Sprinter Build</h3>
                <div class="spec-item" id="spec-frame">Frame: Real Photo Graphic</div>
                <div class="spec-item" id="spec-wheels">Wheels: 88mm Carbon Deep</div>
                <div class="spec-item" id="spec-cockpit">Cockpit: Track Drop Bar</div>
         <g id="bike-group">
    <!-- 1. REAR WHEEL -->
    <g id="layer-rear-wheel"></g>

    <!-- 2. DRIVE TRAIN & CHAIN -->
    <g id="layer-drivetrain">
        <path d="M 230,360 L 392,360" stroke="#888" stroke-width="3" stroke-dasharray="4 2" />
        <path d="M 230,360 L 392,360" id="chain-top" stroke="#aaaaaa" stroke-width="3" />
        <path d="M 230,360 L 392,360" id="chain-bottom" stroke="#888888" stroke-width="3" />
        <circle cx="230" cy="360" r="16" fill="#333" stroke="#888" stroke-width="2"/>
        <circle cx="392" cy="360" r="38" fill="#111" stroke="#333" stroke-width="3"/>
        <circle cx="392" cy="360" r="32" fill="none" stroke="#666" stroke-width="3" stroke-dasharray="6 3"/>
        <line x1="392" y1="360" x2="392" y2="400" stroke="#222" stroke-width="10" stroke-linecap="round"/>
        <rect x="377" y="395" width="30" height="10" rx="3" fill="#444" />
    </g>

    <!-- 3. REAL FRAME IMAGE -->
    <g id="layer-frame">
        <image href="image-removebg-preview.png" x="220" y="145" width="350" height="225"/>
    </g>

    <!-- 4. FRONT WHEEL -->
    <g id="layer-front-wheel"></g>

    <!-- 5. SEATPOST & SADDLE -->
    <g id="layer-saddle">
        <line x1="334" y1="210" x2="320" y2="135" stroke="#111" stroke-width="9" stroke-linecap="round"/>
        <rect x="313" y="130" width="14" height="8" rx="2" fill="#333" />
        <path id="saddle-shape" d="M 285,127 C 305,125 335,125 350,130 C 353,131 347,137 330,137 C 310,137 290,133 285,127 Z" fill="#18181b" stroke="#333" stroke-width="1"/>
    </g>

    <!-- 6. COCKPIT (Stem & Handlebar) -->
    <g id="layer-cockpit">
        <path d="M 522,178 L 545,173 L 555,180 L 525,186 Z" fill="#18181b" />
        <g id="handlebar-graphic"></g>
    </g>
</g>
            </div>
        </div>

        <div class="sidebar">
            <div class="tabs">
                <div class="tab active" onclick="switchTab('wheels')">휠셋</div>
                <div class="tab" onclick="switchTab('cockpit')">핸들바</div>
                <div class="tab" onclick="switchTab('components')">컴포넌트</div>
            </div>

            <div class="tab-content" id="tab-wheels">
                <div>
                    <div class="section-title">앞바퀴 (Front Wheel)</div>
                    <div class="option-grid">
                        <div class="option-card active" id="opt-front-deep" onclick="setWheel('front', 'deep')">
                            <div class="option-name">88mm 딥림 카본</div>
                        </div>
                        <div class="option-card" id="opt-front-spoke3" onclick="setWheel('front', 'spoke3')">
                            <div class="option-name">3-Spoke 삼발이</div>
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
                            <div class="option-name">3-Spoke 삼발이</div>
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
                    <div class="section-title">그립 / 바테입 컬러</div>
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
                            <div class="option-name">카본 레이싱 안장</div>
                        </div>
                        <div class="option-card" onclick="setSaddle('classic')">
                            <div class="option-name">클래식 가죽 안장</div>
                        </div>
                    </div>
                </div>

                <div class="summary-box">
                    <div class="summary-row">
                        <span>프레임 모델</span>
                        <span>Engine11 Sprinter</span>
                    </div>
                    <div class="summary-row">
                        <span>추천 라이딩 스타일</span>
                        <span>Track / Crit Race</span>
                    </div>
                </div>
            </div>
        </div>
    </main>

    <script>
        const state = {
            frontWheel: 'deep',
            rearWheel: 'deep',
            handlebar: 'track',
            gripColor: '#18181b',
            saddle: 'racing'
        };

        window.onload = () => {
            renderAll();
        };

        function renderAll() {
            renderFrontWheel();
            renderRearWheel();
            renderHandlebar();
            renderSaddle();
            updateSpecs();
        }

        function switchTab(tabId) {
            document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
            document.querySelectorAll('.tab-content').forEach(c => c.style.display = 'none');
            event.target.classList.add('active');
            document.getElementById('tab-' + tabId).style.display = 'flex';
        }

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
            const cx = 590, cy = 360, r = 100;
            container.innerHTML = generateWheelSVG(cx, cy, r, state.frontWheel);
        }

        function renderRearWheel() {
            const container = document.getElementById('layer-rear-wheel');
            const cx = 230, cy = 360, r = 100;
            container.innerHTML = generateWheelSVG(cx, cy, r, state.rearWheel);
        }

        function generateWheelSVG(cx, cy, r, type) {
            let svg = `<circle cx="${cx}" cy="${cy}" r="${r}" fill="none" stroke="#111115" stroke-width="14"/>
                       <circle cx="${cx}" cy="${cy}" r="${r+6}" fill="none" stroke="#222228" stroke-width="2"/>`;

            if(type === 'deep') {
                svg += `<circle cx="${cx}" cy="${cy}" r="${r-10}" fill="none" stroke="#1f1f24" stroke-width="28"/>
                        <circle cx="${cx}" cy="${cy}" r="${r-24}" fill="none" stroke="#33333d" stroke-width="2"/>
                        ${generateSpokes(cx, cy, r-24, 20)}
                        <circle cx="${cx}" cy="${cy}" r="12" fill="#111" stroke="#666" stroke-width="2"/>`;
            } else if(type === 'spoke3') {
                svg += `<circle cx="${cx}" cy="${cy}" r="${r-8}" fill="none" stroke="#1f1f24" stroke-width="14"/>
                        <path d="M ${cx},${cy} L ${cx},${cy-r+8} L ${cx+20},${cy} Z" fill="#1f1f24"/>
                        <path d="M ${cx},${cy} L ${cx-70},${cy+45} L ${cx-30},${cy+65} Z" fill="#1f1f24"/>
                        <path d="M ${cx},${cy} L ${cx+70},${cy+45} L ${cx+30},${cy+65} Z" fill="#1f1f24"/>
                        <circle cx="${cx}" cy="${cy}" r="14" fill="#111" stroke="#444" stroke-width="2"/>`;
            } else if(type === 'low') {
                svg += `<circle cx="${cx}" cy="${cy}" r="${r-4}" fill="none" stroke="#888888" stroke-width="8"/>
                        ${generateSpokes(cx, cy, r-8, 32)}
                        <circle cx="${cx}" cy="${cy}" r="8" fill="#aaa" stroke="#333" stroke-width="2"/>`;
            } else if(type === 'disc') {
                svg += `<circle cx="${cx}" cy="${cy}" r="${r-2}" fill="#18181c" stroke="#2a2a32" stroke-width="2"/>
                        <circle cx="${cx}" cy="${cy}" r="15" fill="#333" stroke="#666" stroke-width="2"/>`;
            }
            return svg;
        }

        function generateSpokes(cx, cy, r, count) {
            let spokes = '';
            for(let i = 0; i < count; i++) {
                let angle = (i * 360 / count) * Math.PI / 180;
                let x2 = cx + r * Math.cos(angle);
                let y2 = cy + r * Math.sin(angle);
                spokes += `<line x1="${cx}" y1="${cy}" x2="${x2}" y2="${y2}" stroke="#aaaaaa" stroke-width="1" opacity="0.7"/>`;
            }
            return spokes;
        }

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
                svg = `<path d="M 593,153 C 613,153 623,173 618,203 C 613,218 598,223 588,218" fill="none" stroke="${state.gripColor}" stroke-width="8" stroke-linecap="round"/>`;
            } else if(state.handlebar === 'riser') {
                svg = `<path d="M 593,153 L 603,143 L 628,143" fill="none" stroke="${state.gripColor}" stroke-width="8" stroke-linecap="round"/>
                       <path d="M 593,153 L 583,163 L 558,163" fill="none" stroke="${state.gripColor}" stroke-width="8" stroke-linecap="round"/>`;
            } else if(state.handlebar === 'bullhorn') {
                svg = `<path d="M 593,153 L 623,153 L 638,133" fill="none" stroke="${state.gripColor}" stroke-width="8" stroke-linecap="round"/>`;
            } else {
                svg = `<path d="M 593,153 C 608,153 618,163 613,188" fill="none" stroke="${state.gripColor}" stroke-width="8" stroke-linecap="round"/>`;
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
                el.setAttribute('d', 'M 270,112 C 290,110 320,110 335,115 C 338,116 332,122 315,122 C 295,122 275,118 270,112 Z');
                el.setAttribute('fill', '#18181b');
            } else {
                el.setAttribute('d', 'M 265,110 C 290,108 320,108 338,115 C 340,118 330,125 310,124 C 290,124 270,120 265,110 Z');
                el.setAttribute('fill', '#78350f');
            }
        }

        function updateSpecs() {
            const hbMap = { track: 'Track Drop Bar', riser: 'Wide Riser Bar', bullhorn: 'Aero Bullhorn Bar', road: 'Compact Road Bar' };
            const wheelMap = { deep: '88mm Carbon Deep', spoke3: '3-Spoke Carbon', low: 'Classic Low-Profile', disc: 'Aero Disc Wheel' };
            document.getElementById('spec-cockpit').innerText = 'Cockpit: ' + (hbMap[state.handlebar] || state.handlebar);
            document.getElementById('spec-wheels').innerText = `Wheels: F:${wheelMap[state.frontWheel]} / R:${wheelMap[state.rearWheel]}`;
        }

        function resetBuild() {
            state.frontWheel = 'deep';
            state.rearWheel = 'deep';
            state.handlebar = 'track';
            state.gripColor = '#18181b';
            renderAll();
        }

        function applyPreset(preset) {
            if(preset === 'stealth') {
                state.frontWheel = 'deep';
                state.rearWheel = 'deep';
                state.handlebar = 'track';
            } else if(preset === 'classic') {
                state.frontWheel = 'low';
                state.rearWheel = 'low';
                state.handlebar = 'road';
            } else if(preset === 'trispoke') {
                state.frontWheel = 'spoke3';
                state.rearWheel = 'disc';
                state.handlebar = 'bullhorn';
            }
            renderAll();
        }

        function exportConfig() {
            alert('현재 엔진11 커스텀 설정이 저장되었습니다!');
        }
    </script>
</body>
</html>
