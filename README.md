<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quantum Cosmos: Advanced 3D Solar System Explorer</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&display=swap');

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Orbitron', monospace;
            background: linear-gradient(45deg, #0a0a0a, #1a1a2e, #16213e);
            overflow: hidden;
            color: #ffffff;
            cursor: none;
        }

        #gameContainer {
            position: relative;
            width: 100vw;
            height: 100vh;
        }

        #customCursor {
            position: fixed;
            width: 20px;
            height: 20px;
            border: 2px solid #00ffff;
            border-radius: 50%;
            pointer-events: none;
            z要件: z-index: 1000;
            transition: all 0.1s ease;
            box-shadow: 0 0 20px #00ffff, inset 0 0 10px #00ffff;
        }

        #customCursor::before {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 6px;
            height: 6px;
            background: #00ffff;
            border-radius: 50%;
            box-shadow: 0 0 10px #00ffff;
        }

        #hud {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            pointer-events: none;
            z-index: 100;
        }

        .holographic-panel {
            position: absolute;
            background: linear-gradient(135deg, 
                rgba(0, 255, 255, 0.1) 0%,
                rgba(255, 0, 255, 0.1) 50%,
                rgba(0, 255, 0, 0.1) 100%);
            border: 2px solid rgba(0, 255, 255, 0.6);
            border-radius: 15px;
            padding: 20px;
            backdrop-filter: blur(20px);
            box-shadow: 
                0 0 30px rgba(0, 255, 255, 0.3),
                inset 0 0 20px rgba(255, 255, 255, 0.1);
            pointer-events: auto;
            animation: hologramFlicker 4s infinite;
        }

        @keyframes hologramFlicker {
            0%, 100% { opacity: 0.9; }
            50% { opacity: 1; }
            25%, 75% { opacity: 0.85; }
        }

        #topPanel {
            top: 20px;
            left: 20px;
            width: 320px;
        }

        #rightPanel {
            top: 20px;
            right: 20px;
            width: 280px;
        }

        #bottomPanel {
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            width: 400px;
            text-align: center;
        }

        .metric {
            display: flex;
            justify-content: space-between;
            margin: 10px 0;
            padding: 8px 12px;
            background: rgba(0, 255, 255, 0.1);
            border-left: 4px solid #00ffff;
            border-radius: 8px;
            font-size: 14px;
            text-shadow: 0 0 10px rgba(0, 255, 255, 0.8);
        }

        .metric-value {
            color: #00ff00;
            font-weight: bold;
            text-shadow: 0 0 10px rgba(0, 255, 0, 0.8);
        }

        #challengeModal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            background: rgba(0, 0, 0, 0.9);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            backdrop-filter: blur(10px);
        }

        .modal-content {
            background: linear-gradient(135deg, 
                rgba(25, 25, 112, 0.95) 0%,
                rgba(75, 0, 130, 0.95) 50%,
                rgba(139, 0, 139, 0.95) 100%);
            border: 3px solid #ff6b6b;
            border-radius: 25px;
            padding: 40px;
            max-width: 600px;
            text-align: center;
            box-shadow: 
                0 0 50px rgba(255, 107, 107, 0.5),
                inset 0 0 30px rgba(255, 255, 255, 0.1);
            animation: modalPulse 2s infinite;
        }

        @keyframes modalPulse {
            0%, 100% { box-shadow: 0 0 50px rgba(255, 107, 107, 0.5), inset 0 0 30px rgba(255, 255, 255, 0.1); }
            50% { box-shadow: 0 0 80px rgba(255, 107, 107, 0.8), inset 0 0 30px rgba(255, 255, 255, 0.2); }
        }

        .challenge-title {
            font-size: 28px;
            color: #ff6b6b;
            margin-bottom: 20px;
            text-shadow: 0 0 20px rgba(255, 107, 107, 0.8);
            font-weight: 900;
        }

        .challenge-question {
            font-size: 18px;
            margin-bottom: 30px;
            line-height: 1.6;
            color: #ffffff;
        }

        .challenge-option {
            display: block;
            width: 100%;
            margin: 15px 0;
            padding: 18px;
            background: linear-gradient(45deg, #4a90e2, #357abd);
            border: 2px solid transparent;
            border-radius: 15px;
            color: white;
            font-size: 16px;
            font-family: 'Orbitron', monospace;
            cursor: pointer;
            transition: all 0.4s ease;
            position: relative;
            overflow: hidden;
        }

        .challenge-option::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.3), transparent);
            transition: left 0.6s ease;
        }

        .challenge-option:hover::before {
            left: 100%;
        }

        .challenge-option:hover {
            background: linear-gradient(45deg, #357abd, #4a90e2);
            border-color: #00ffff;
            box-shadow: 0 0 30px rgba(0, 255, 255, 0.6);
            transform: translateY(-3px) scale(1.02);
        }

        .challenge-option.correct {
            background: linear-gradient(45deg, #28a745, #20c997);
            border-color: #00ff00;
            box-shadow: 0 0 40px rgba(0, 255, 0, 0.8);
        }

        .challenge-option.incorrect {
            background: linear-gradient(45deg, #dc3545, #c82333);
            border-color: #ff0000;
            box-shadow: 0 0 40px rgba(255, 0, 0, 0.8);
        }

        #nextButton {
            margin-top: 30px;
            padding: 15px 35px;
            background: linear-gradient(45deg, #ff6b6b, #ff5252);
            border: none;
            border-radius: 30px;
            color: white;
            font-size: 18px;
            font-family: 'Orbitron', monospace;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.4s ease;
            box-shadow: 0 0 20px rgba(255, 107, 107, 0.5);
        }

        #nextButton:hover {
            background: linear-gradient(45deg, #ff5252, #ff6b6b);
            box-shadow: 0 0 40px rgba(255, 107, 107, 0.8);
            transform: scale(1.1);
        }

        .title {
            font-size: 24px;
            font-weight: 900;
            color: #00ffff;
            text-shadow: 0 0 20px rgba(0, 255, 255, 0.8);
            margin-bottom: 20px;
            text-align: center;
        }

        .planet-info {
            position: absolute;
            background: rgba(0, 0, 0, 0.9);
            border: 2px solid #ffff00;
            border-radius: 12px;
            padding: 15px;
            color: #ffffff;
            font-size: 14px;
            max-width: 250px;
            pointer-events: none;
            opacity: 0;
            transition: opacity 0.3s ease;
            z-index: 200;
        }

        .controls-hint {
            position: absolute;
            bottom: 20px;
            right: 20px;
            background: rgba(0, 0, 0, 0.8);
            border: 2px solid #ffff00;
            border-radius: 10px;
            padding: 15px;
            font-size: 12px;
            color: #ffff00;
            text-shadow: 0 0 10px rgba(255, 255, 0, 0.8);
        }

        canvas {
            display: block;
            width: 100%;
            height: 100%;
        }

        .loading-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            background: linear-gradient(45deg, #0a0a0a, #1a1a2e);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 2000;
            transition: opacity 1s ease;
        }

        .loading-content {
            text-align: center;
            color: #00ffff;
        }

        .loading-spinner {
            width: 80px;
            height: 80px;
            border: 4px solid rgba(0, 255, 255, 0.3);
            border-top: 4px solid #00ffff;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin: 0 auto 20px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        @media (max-width: 768px) {
            #topPanel, #rightPanel {
                width: 200px;
                font-size: 12px;
                padding: 10px;
            }
            #bottomPanel {
                width: 300px;
                font-size: 12px;
            }
            .metric {
                font-size: 12px;
            }
            .challenge-title {
                font-size: 24px;
            }
            .challenge-question {
                font-size: 16px;
            }
            .challenge-option {
                font-size: 14px;
                padding: 12px;
            }
        }
    </style>
</head>
<body>
    <div id="customCursor"></div>
    <div class="loading-screen" id="loadingScreen">
        <div class="loading-content">
            <div class="loading-spinner"></div>
            <h2>INITIALIZING QUANTUM COSMOS</h2>
            <p>Loading advanced 3D solar system...</p>
        </div>
    </div>
    <div id="gameContainer">
        <canvas id="canvas"></canvas>
        <div id="hud">
            <div class="holographic-panel" id="topPanel" role="region" aria-label="Game Status">
                <div class="title">🌌 QUANTUM COSMOS</div>
                <div class="metric">
                    <span>COSMIC SCORE</span>
                    <span class="metric-value" id="score">0</span>
                </div>
                <div class="metric">
                    <span>DISCOVERIES</span>
                    <span class="metric-value" id="discoveries">0/8</span>
                </div>
                <div class="metric">
                    <span>QUANTUM LEVEL</span>
                    <span class="metric-value" id="level">INITIATE</span>
                </div>
                <div class="metric">
                    <span>DIMENSION</span>
                    <span class="metric-value" id="dimension">PRIME</span>
                </div>
            </div>
            <div class="holographic-panel" id="rightPanel" role="region" aria-label="System Status">
                <div class="title">SYSTEM STATUS</div>
                <div class="metric">
                    <span>GRAVITY FIELD</span>
                    <span class="metric-value">STABLE</span>
                </div>
                <div class="metric">
                    <span>WARP DRIVE</span>
                    <span class="metric-value">ONLINE</span>
                </div>
                <div class="metric">
                    <span>SENSORS</span>
                    <span class="metric-value">ACTIVE</span>
                </div>
                <div class="metric">
                    <span>PATTERN AI</span>
                    <span class="metric-value">LEARNING</span>
                </div>
            </div>
            <div class="holographic-panel" id="bottomPanel" role="region" aria-label="Controls">
                <div style="color: #00ffff; font-size: 16px; margin-bottom: 10px;">
                    🚀 NEURAL INTERFACE CONTROLS
                </div>
                <div style="font-size: 14px; color: #ffff00;">
                    MOUSE/TOUCH: Navigate 3D Space • TAP/CLICK: Investigate Celestial Bodies • SCROLL: Zoom Through Dimensions
                </div>
            </div>
        </div>
        <div class="controls-hint" role="region" aria-label="Advanced Controls">
            <strong>ADVANCED CONTROLS</strong><br>
            Hold SHIFT: Hyperspeed<br>
            Hold CTRL: Quantum View<br>
            SPACE: Pause Time<br>
            R: Reset Perspective
        </div>
        <div id="challengeModal" role="dialog" aria-labelledby="challengeTitle" aria-modal="true">
            <div class="modal-content">
                <div class="challenge-title" id="challengeTitle">QUANTUM CHALLENGE</div>
                <div class="challenge-question" id="challengeQuestion"></div>
                <div id="challengeOptions"></div>
                <button id="nextButton" style="display: none;" aria-label="Continue Exploration">CONTINUE EXPLORATION</button>
            </div>
        </div>
        <div class="planet-info" id="planetInfo" role="region" aria-label="Planet Information">
            <div id="planetName"></div>
            <div id="planetFacts"></div>
            <div id="planetPattern"></div>
        </div>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r165/three.min.js"></script>
    <script>
        // Advanced Three.js Solar System with Quantum Effects
        let scene, camera, renderer;
        let solarSystem = {};
        let mouse = { x: 0, y: 0 };
        let raycaster, intersectObjects = [];
        let animationId;
        let currentChallenge = 0;
        let eventTimer = 0;

        let gameState = {
            score: 0,
            discoveries: 0,
            level: 'INITIATE',
            dimension: 'PRIME',
            discoveredPlanets: new Set(),
            isHyperspeed: false,
            isQuantumView: false,
            isPaused: false
        };

        // Custom cursor
        const cursor = document.getElementById('customCursor');
        document.addEventListener('mousemove', (e) => {
            cursor.style.left = e.clientX - 10 + 'px';
            cursor.style.top = e.clientY - 10 + 'px';
            mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;
            if (raycaster && camera) {
                raycaster.setFromCamera(mouse, camera);
                const intersects = raycaster.intersectObjects(intersectObjects);
                cursor.style.borderColor = intersects.length > 0 ? '#ff0000' : '#00ffff';
                cursor.style.boxShadow = intersects.length > 0 
                    ? '0 0 30px #ff0000, inset 0 0 15px #ff0000' 
                    : '0 0 20px #00ffff, inset 0 0 10px #00ffff';
            }
        });

        // Planetary data with enhanced properties
        const planetData = [
            {
                name: 'Mercury', distance: 15, size: 0.8, color: 0x8c7853,
                speed: 0.04, moons: 0, rings: false,
                facts: "Closest to Sun • 800°F day, -300°F night • No atmosphere",
                pattern: "Extreme tidal forces from Sun prevent moon formation",
                glowColor: 0xffaa00
            },
            {
                name: 'Venus', distance: 22, size: 1.2, color: 0xffc649,
                speed: 0.03, moons: 0, rings: false,
                facts: "Hottest planet • 900°F surface • Backwards rotation",
                pattern: "Runaway greenhouse effect from thick CO₂ atmosphere",
                glowColor: 0xffff00
            },
            {
                name: 'Earth', distance: 30, size: 1.3, color: 0x4a90e2,
                speed: 0.02, moons: 1, rings: false,
                facts: "Goldilocks zone • Liquid water • Magnetic field protection",
                pattern: "Perfect distance + large moon = stable climate",
                glowColor: 0x00ff00
            },
            {
                name: 'Mars', distance: 40, size: 1.0, color: 0xcd5c5c,
                speed: 0.015, moons: 2, rings: false,
                facts: "Red from iron oxide • Polar ice caps • Ancient riverbeds",
                pattern: "Lost atmosphere due to weak magnetic field",
                glowColor: 0xff0000
            },
            {
                name: 'Jupiter', distance: 65, size: 4.0, color: 0xd2691e,
                speed: 0.008, moons: 79, rings: true,
                facts: "Gas giant • Great Red Spot • Asteroid protector",
                pattern: "Massive gravity = cosmic vacuum cleaner",
                glowColor: 0xff8800
            },
            {
                name: 'Saturn', distance: 85, size: 3.5, color: 0xfab1a0,
                speed: 0.006, moons: 83, rings: true,
                facts: "Spectacular rings • Less dense than water • Hexagonal poles",
                pattern: "Ring system from shattered moons and asteroids",
                glowColor: 0xffaa88
            },
            {
                name: 'Uranus', distance: 105, size: 2.5, color: 0x74b9ff,
                speed: 0.004, moons: 27, rings: true,
                facts: "Tilted 98° • Methane atmosphere • Faint rings",
                pattern: "Massive collision knocked it sideways",
                glowColor: 0x00aaff
            },
            {
                name: 'Neptune', distance: 125, size: 2.3, color: 0x0984e3,
                speed: 0.003, moons: 14, rings: false,
                facts: "Fastest winds 1200mph • Deep blue methane • Discovered by math",
                pattern: "Found by gravitational perturbations of Uranus",
                glowColor: 0x0066ff
            }
        ];

        // Enhanced challenges
        const challenges = [
            {
                title: "GRAVITATIONAL DYNAMICS",
                question: "Why does Jupiter have 79 moons while Mercury has none?",
                options: [
                    "Jupiter is older than Mercury",
                    "Jupiter's massive gravity captures objects; Mercury can't compete with Sun's pull",
                    "Jupiter is further from the Sun",
                    "Jupiter is made of gas instead of rock"
                ],
                correct: 1,
                explanation: "Jupiter's enormous mass creates a gravitational sphere that captures objects. Mercury can't compete with the Sun's overwhelming gravitational dominance."
            },
            {
                title: "PLANETARY FORMATION PATTERNS",
                question: "What fundamental pattern explains inner vs outer planet differences?",
                options: [
                    "Inner planets formed first, outer planets formed later",
                    "Solar wind sorted materials: heavy elements inward, light gases outward",
                    "Inner planets are hotter, outer planets are colder",
                    "Asteroid belt separated the two groups"
                ],
                correct: 1,
                explanation: "Solar wind blew light elements outward while heavy elements condensed closer in, creating rocky inner planets and gas giant outer planets."
            },
            {
                title: "COSMIC PROTECTION SYSTEM",
                question: "How does Jupiter act as Earth's 'cosmic bodyguard'?",
                options: [
                    "Jupiter's magnetic field deflects solar radiation",
                    "Jupiter's gravity captures/deflects asteroids that could hit Earth",
                    "Jupiter blocks comets with its ring system",
                    "Jupiter creates a gravitational shield around Earth"
                ],
                correct: 1,
                explanation: "Jupiter's massive gravity acts like a cosmic vacuum cleaner, capturing or deflecting dangerous objects. Without it, Earth would face 1000x more impacts."
            },
            {
                title: "RING SYSTEM MECHANICS",
                question: "Saturn's rings are most likely formed from:",
                options: [
                    "Cosmic dust collected from space",
                    "Moons torn apart by tidal forces + captured asteroid debris",
                    "Frozen gases from Saturn's atmosphere",
                    "Light particles from the Sun"
                ],
                correct: 1,
                explanation: "Saturn's rings are ice and rock fragments from moons destroyed by tidal forces, plus captured debris. They're constantly renewed and destroyed."
            }
        ];

        function initThreeJS() {
            scene = new THREE.Scene();
            scene.fog = new THREE.Fog(0x000000, 200, 1000);
            
            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 2000);
            camera.position.set(0, 50, 150);
            
            renderer = new THREE.WebGLRenderer({ 
                canvas: document.getElementById('canvas'),
                antialias: true,
                alpha: true
            });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.shadowMap.enabled = true;
            renderer.shadowMap.type = THREE.PCFSoftShadowMap;
            
            raycaster = new THREE.Raycaster();
            
            const ambientLight = new THREE.AmbientLight(0x404040, 0.3);
            scene.add(ambientLight);
            
            const sunLight = new THREE.DirectionalLight(0xffffff, 1.5);
            sunLight.position.set(50, 50, 50);
            sunLight.castShadow = true;
            sunLight.shadow.mapSize.width = 1024;
            sunLight.shadow.mapSize.height = 1024;
            sunLight.shadow.camera.near = 0.5;
            sunLight.shadow.camera.far = 500;
            scene.add(sunLight);
            
            const listener = new THREE.AudioListener();
            camera.add(listener);
            const ambientSound = new THREE.Audio(listener);
            const audioLoader = new THREE.AudioLoader();
            audioLoader.load('sounds/ambient-space.mp3', (buffer) => {
                ambientSound.setBuffer(buffer);
                ambientSound.setLoop(true);
                ambientSound.setVolume(0.3);
                ambientSound.play();
            });
            const clickSound = new THREE.Audio(listener);
            audioLoader.load('sounds/click.wav', (buffer) => {
                clickSound.setBuffer(buffer);
                clickSound.setVolume(0.5);
            });
            solarSystem.clickSound = clickSound;

            createStarfield();
            createSolarSystem();
            setupEventListeners();
            animate();
        }

        function createStarfield() {
            const starGeometry = new THREE.BufferGeometry();
            const starVertices = [];
            const starColors = [];
            for (let i = 0; i < 5000; i++) {
                const x = (Math.random() - 0.5) * 2000;
                const y = (Math.random() - 0.5) * 2000;
                const z = (Math.random() - 0.5) * 2000;
                starVertices.push(x, y, z);
                const color = new THREE.Color();
                color.setHSL(Math.random() * 0.3 + 0.5, 0.5, Math.random() * 0.5 + 0.5);
                starColors.push(color.r, color.g, color.b);
            }
            starGeometry.setAttribute('position', new THREE.Float32BufferAttribute(starVertices, 3));
            starGeometry.setAttribute('color', new THREE.Float32BufferAttribute(starColors, 3));
            const starMaterial = new THREE.PointsMaterial({
                size: 2,
                vertexColors: true,
                transparent: true,
                opacity: 0.8
            });
            const stars = new THREE.Points(starGeometry, starMaterial);
            scene.add(stars);
            createNebula();
        }

        function createNebula() {
            const nebulaGeometry = new THREE.SphereGeometry(1000, 64, 64);
            const nebulaMaterial = new THREE.ShaderMaterial({
                uniforms: {
                    time: { value: 0 },
                    color1: { value: new THREE.Color(0x1e1e3e) },
                    color2: { value: new THREE.Color(0x4a90e2) }
                },
                vertexShader: `
                    varying vec2 vUv;
                    void main() {
                        vUv = uv;
                        gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
                    }
                `,
                fragmentShader: `
                    uniform float time;
                    uniform vec3 color1;
                    uniform vec3 color2;
                    varying vec2 vUv;
                    void main() {
                        float t = sin(time * 0.1) * 0.5 + 0.5;
                        vec3 color = mix(color1, color2, vUv.y + t);
                        gl_FragColor = vec4(color, 0.8);
                    }
                `,
                side: THREE.BackSide,
                transparent: true
            });
            const nebula = new THREE.Mesh(nebulaGeometry, nebulaMaterial);
            scene.add(nebula);
            solarSystem.nebula = nebula;
        }

        function createSolarSystem() {
            const sunGeometry = new THREE.SphereGeometry(8, 64, 64);
            const sunMaterial = new THREE.ShaderMaterial({
                uniforms: {
                    time: { value: 0 },
                    glowColor: { value: new THREE.Color(0xffff00) }
                },
                vertexShader: `
                    varying vec2 vUv;
                    void main() {
                        vUv = uv;
                        gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
                    }
                `,
                fragmentShader: `
                    uniform float time;
                    uniform vec3 glowColor;
                    varying vec2 vUv;
                    void main() {
                        float intensity = 0.5 + 0.5 * sin(time * 2.0);
                        float dist = distance(vUv, vec2(0.5, 0.5));
                        float glow = 1.0 - smoothstep(0.3, 0.5, dist);
                        gl_FragColor = vec4(glowColor * intensity * glow, 1.0);
                    }
                `,
                transparent: true
            });
            const sun = new THREE.Mesh(sunGeometry, sunMaterial);
            scene.add(sun);
            solarSystem.sun = sun;

            const sunGlowGeometry = new THREE.SphereGeometry(12, 32, 32);
            const sunGlowMaterial = new THREE.MeshBasicMaterial({
                color: 0xffaa00,
                transparent: true,
                opacity: 0.3
            });
            const sunGlow = new THREE.Mesh(sunGlowGeometry, sunGlowMaterial);
            scene.add(sunGlow);

            solarSystem.planets = [];
            planetData.forEach((data, index) => {
                const planet = createPlanet(data, index);
                solarSystem.planets.push(planet);
            });

            createComet();
        }

        function createPlanet(data, index) {
            const planetGroup = new THREE.Group();
            const loader = new THREE.TextureLoader();
            const texture = loader.load(`textures/${data.name.toLowerCase()}.jpg`); // Replace with actual texture paths
            const geometry = new THREE.SphereGeometry(data.size, 64, 64);
            const material = new THREE.MeshStandardMaterial({
                map: texture,
                roughness: 0.7,
                metalness: 0.2
            });
            const planet = new THREE.Mesh(geometry, material);
            planet.castShadow = true;
            planet.receiveShadow = true;
            planet.userData = { planetData: data, index: index };

            const glowGeometry = new THREE.SphereGeometry(data.size * 1.5, 32, 32);
            const glowMaterial = new THREE.MeshBasicMaterial({
                color: data.glowColor,
                transparent: true,
                opacity: 0.2
            });
            const glow = new THREE.Mesh(glowGeometry, glowMaterial);
            planetGroup.add(planet);
            planetGroup.add(glow);

            const orbitGeometry = new THREE.RingGeometry(data.distance - 0.5, data.distance + 0.5, 64);
            const orbitMaterial = new THREE.MeshBasicMaterial({
                color: 0x444444,
                transparent: true,
                opacity: 0.1,
                side: THREE.DoubleSide
            });
            const orbit = new THREE.Mesh(orbitGeometry, orbitMaterial);
            orbit.rotation.x = -Math.PI / 2;
            scene.add(orbit);

            if (data.rings) {
                const ringGeometry = new THREE.RingGeometry(data.size * 1.2, data.size * 2, 32);
                const ringMaterial = new THREE.MeshBasicMaterial({
                    color: 0xaaaaaa,
                    transparent: true,
                    opacity: 0.6,
                    side: THREE.DoubleSide
                });
                const rings = new THREE.Mesh(ringGeometry, ringMaterial);
                rings.rotation.x = -Math.PI / 2;
                planetGroup.add(rings);
            }

            planetGroup.position.x = data.distance;
            planetGroup.userData = { 
                distance: data.distance, 
                speed: data.speed, 
                angle: Math.random() * Math.PI * 2,
                planetData: data,
                index: index
            };
            scene.add(planetGroup);
            intersectObjects.push(planet);
            return planetGroup;
        }

        function createComet() {
            const cometGeometry = new THREE.BufferGeometry();
            const positions = [];
            const colors = [];
            for (let i = 0; i < 1000; i++) {
                const angle = Math.random() * Math.PI * 2;
                const radius = Math.random() * 50 + 100;
                positions.push(Math.cos(angle) * radius, (Math.random() - 0.5) * 10, Math.sin(angle) * radius);
                const color = new THREE.Color(0x00ffff);
                colors.push(color.r, color.g, color.b);
            }
            cometGeometry.setAttribute('position', new THREE.Float32BufferAttribute(positions, 3));
            cometGeometry.setAttribute('color', new THREE.Float32BufferAttribute(colors, 3));
            const cometMaterial = new THREE.PointsMaterial({
                size: 0.5,
                vertexColors: true,
                transparent: true,
                opacity: 0.7
            });
            const comet = new THREE.Points(cometGeometry, cometMaterial);
            comet.userData = { angle: 0, speed: 0.002, distance: 150 };
            scene.add(comet);
            solarSystem.comet = comet;
        }

        function setupEventListeners() {
            let mouseDown = false;
            let mouseX = 0, mouseY = 0;
            let touchStartX = 0, touchStartY = 0;

            document.addEventListener('mousedown', (e) => {
                mouseDown = true;
                mouseX = e.clientX;
                mouseY = e.clientY;
            });

            document.addEventListener('mouseup', () => {
                mouseDown = false;
            });

            document.addEventListener('mousemove', (e) => {
                cursor.style.left = e.clientX - 10 + 'px';
                cursor.style.top = e.clientY - 10 + 'px';
                mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
                mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;
                if (raycaster && camera) {
                    raycaster.setFromCamera(mouse, camera);
                    const intersects = raycaster.intersectObjects(intersectObjects);
                    cursor.style.borderColor = intersects.length > 0 ? '#ff0000' : '#00ffff';
                    cursor.style.boxShadow = intersects.length > 0 
                        ? '0 0 30px #ff0000, inset 0 0 15px #ff0000' 
                        : '0 0 20px #00ffff, inset 0 0 10px #00ffff';
                }
                if (mouseDown) {
                    const deltaX = e.clientX - mouseX;
                    const deltaY = e.clientY - mouseY;
                    camera.position.x = camera.position.x * Math.cos(deltaX * 0.01) + camera.position.z * Math.sin(deltaX * 0.01);
                    camera.position.z = camera.position.z * Math.cos(deltaX * 0.01) - camera.position.x * Math.sin(deltaX * 0.01);
                    camera.position.y += deltaY * 0.1;
                    camera.lookAt(0, 0, 0);
                    mouseX = e.clientX;
                    mouseY = e.clientY;
                }
            });

            document.addEventListener('touchstart', (e) => {
                touchStartX = e.touches[0].clientX;
                touchStartY = e.touches[0].clientY;
            });

            document.addEventListener('touchmove', (e) => {
                const deltaX = e.touches[0].clientX - touchStartX;
                const deltaY = e.touches[0].clientY - touchStartY;
                camera.position.x = camera.position.x * Math.cos(deltaX * 0.01) + camera.position.z * Math.sin(deltaX * 0.01);
                camera.position.z = camera.position.z * Math.cos(deltaX * 0.01) - camera.position.x * Math.sin(deltaX * 0.01);
                camera.position.y += deltaY * 0.1;
                camera.lookAt(0, 0, 0);
                touchStartX = e.touches[0].clientX;
                touchStartY = e.touches[0].clientY;
            });

            document.addEventListener('touchend', (e) => {
                const touch = e.changedTouches[0];
                mouse.x = (touch.clientX / window.innerWidth) * 2 - 1;
                mouse.y = -(touch.clientY / window.innerHeight) * 2 + 1;
                raycaster.setFromCamera(mouse, camera);
                const intersects = raycaster.intersectObjects(intersectObjects);
                if (intersects.length > 0) {
                    const clicked = intersects[0].object;
                    if (clicked.userData.isMeteor) {
                        gameState.score += 200;
                        updateHUD();
                        scene.remove(clicked);
                        intersectObjects = intersectObjects.filter(obj => obj !== clicked);
                    } else if (clicked.userData.planetData) {
                        investigatePlanet(clicked.userData.planetData, clicked.userData.index);
                    }
                }
            });

            document.addEventListener('click', (e) => {
                raycaster.setFromCamera(mouse, camera);
                const intersects = raycaster.intersectObjects(intersectObjects);
                if (intersects.length > 0) {
                    const clicked = intersects[0].object;
                    if (clicked.userData.isMeteor) {
                        gameState.score += 200;
                        updateHUD();
                        scene.remove(clicked);
                        intersectObjects = intersectObjects.filter(obj => obj !== clicked);
                    } else if (clicked.userData.planetData) {
                        investigatePlanet(clicked.userData.planetData, clicked.userData.index);
                    }
                }
            });

            document.addEventListener('wheel', (e) => {
                if (e.deltaY > 0) {
                    camera.position.multiplyScalar(1.1);
                } else {
                    camera.position.multiplyScalar(0.9);
                }
                camera.lookAt(0, 0, 0);
            });

            document.addEventListener('keydown', (e) => {
                switch (e.code) {
                    case 'Space':
                        e.preventDefault();
                        gameState.isPaused = !gameState.isPaused;
                        updateHUD();
                        break;
                    case 'KeyR':
                        resetCamera();
                        break;
                    case 'ShiftLeft':
                    case 'ShiftRight':
                        gameState.isHyperspeed = true;
                        updateHUD();
                        break;
                    case 'ControlLeft':
                    case 'ControlRight':
                        gameState.isQuantumView = true;
                        toggleQuantumView(true);
                        break;
                }
            });

            document.addEventListener('keyup', (e) => {
                switch (e.code) {
                    case 'ShiftLeft':
                    case 'ShiftRight':
                        gameState.isHyperspeed = false;
                        updateHUD();
                        break;
                    case 'ControlLeft':
                    case 'ControlRight':
                        gameState.isQuantumView = false;
                        toggleQuantumView(false);
                        break;
                }
            });

            window.addEventListener('resize', () => {
                camera.aspect = window.innerWidth / window.innerHeight;
                camera.updateProjectionMatrix();
                renderer.setSize(window.innerWidth, window.innerHeight);
            });

            window.addEventListener('unload', cleanup);
        }

        function cleanup() {
            cancelAnimationFrame(animationId);
            renderer.dispose();
            scene = null;
            camera = null;
            renderer = null;
        }

        function resetCamera() {
            camera.position.set(0, 50, 150);
            camera.lookAt(0, 0, 0);
        }

        function toggleQuantumView(active) {
            solarSystem.planets.forEach(planetGroup => {
                const planet = planetGroup.children[0];
                if (active) {
                    planet.material.wireframe = true;
                    planet.material.opacity = 0.5;
                } else {
                    planet.material.wireframe = false;
                    planet.material.opacity = 1;
                }
            });
        }

        function investigatePlanet(planetData, index) {
            if (!gameState.discoveredPlanets.has(planetData.name)) {
                solarSystem.clickSound.play();
                gameState.discoveredPlanets.add(planetData.name);
                gameState.score += 100;
                gameState.discoveries = gameState.discoveredPlanets.size;
                updateLevel();
                updateHUD();
                showPlanetInfo(planetData, index);
                triggerChallenge();
            } else {
                showPlanetInfo(planetData, index);
            }
        }

        function updateLevel() {
            const discoveries = gameState.discoveries;
            if (discoveries >= 8) {
                gameState.level = 'COSMIC OVERLORD';
                gameState.dimension = 'QUANTUM';
            } else if (discoveries >= 6) {
                gameState.level = 'GALACTIC COMMANDER';
                gameState.dimension = 'HYPERSPACE';
            } else if (discoveries >= 4) {
                gameState.level = 'STAR CAPTAIN';
                gameState.dimension = 'INTERSTELLAR';
            } else if (discoveries >= 2) {
                gameState.level = 'PLANETARY EXPLORER';
                gameState.dimension = 'SOLAR';
            }
        }

        function updateHUD() {
            document.getElementById('score').textContent = gameState.score;
            document.getElementById('discoveries').textContent = `${gameState.discoveries}/8`;
            document.getElementById('level').textContent = gameState.level;
            document.getElementById('dimension').textContent = gameState.dimension;
            document.querySelector('#rightPanel .metric:nth-child(2) .metric-value').textContent = 
                gameState.isHyperspeed ? 'ENGAGED' : 'ONLINE';
            document.querySelector('#rightPanel .metric:nth-child(3) .metric-value').textContent = 
                gameState.isQuantumView ? 'QUANTUM MODE' : 'ACTIVE';
            document.querySelector('#rightPanel .metric:nth-child(4) .metric-value').textContent = 
                gameState.isPaused ? 'PAUSED' : 'LEARNING';
        }

        function showPlanetInfo(planetData, index) {
            const planetInfo = document.getElementById('planetInfo');
            const planetName = document.getElementById('planetName');
            const planetFacts = document.getElementById('planetFacts');
            const planetPattern = document.getElementById('planetPattern');
            
            planetName.textContent = planetData.name.toUpperCase();
            planetFacts.textContent = planetData.facts;
            planetPattern.textContent = `Pattern: ${planetData.pattern}`;
            
            const planetPos = solarSystem.planets[index].position.clone();
            const vector = planetPos.project(camera);
            const x = Math.min(Math.max((vector.x * 0.5 + 0.5) * window.innerWidth + 20, 20), window.innerWidth - 270);
            const y = Math.min(Math.max((-vector.y * 0.5 + 0.5) * window.innerHeight - 20, 20), window.innerHeight - 100);
            
            planetInfo.style.left = `${x}px`;
            planetInfo.style.top = `${y}px`;
            planetInfo.style.opacity = '1';
            
            setTimeout(() => {
                planetInfo.style.opacity = '0';
            }, 5000);
        }

        function triggerChallenge() {
            if (currentChallenge < challenges.length && gameState.discoveries % 2 === 0) {
                gameState.isPaused = true;
                updateHUD();
                showChallenge(currentChallenge);
            }
        }

        function showChallenge(index) {
            const challengeModal = document.getElementById('challengeModal');
            const challengeTitle = document.getElementById('challengeTitle');
            const challengeQuestion = document.getElementById('challengeQuestion');
            const challengeOptions = document.getElementById('challengeOptions');
            const nextButton = document.getElementById('nextButton');
            
            challengeTitle.textContent = challenges[index].title;
            challengeQuestion.textContent = challenges[index].question;
            challengeOptions.innerHTML = '';
            
            challenges[index].options.forEach((option, i) => {
                const button = document.createElement('button');
                button.className = 'challenge-option';
                button.textContent = option;
                button.tabIndex = 0;
                button.setAttribute('aria-label', `Option ${i + 1}: ${option}`);
                button.addEventListener('click', () => handleChallengeAnswer(i, index));
                button.addEventListener('keydown', (e) => {
                    if (e.code === 'Enter' || e.code === 'Space') {
                        handleChallengeAnswer(i, index);
                    }
                });
                challengeOptions.appendChild(button);
            });
            challengeOptions.querySelector('.challenge-option').focus();
            
            challengeModal.style.display = 'flex';
            nextButton.style.display = 'none';
        }

        function handleChallengeAnswer(selected, challengeIndex) {
            const options = document.querySelectorAll('.challenge-option');
            const nextButton = document.getElementById('nextButton');
            
            options.forEach((option, i) => {
                option.disabled = true;
                if (i === challenges[challengeIndex].correct) {
                    option.classList.add('correct');
                } else if (i === selected) {
                    option.classList.add('incorrect');
                }
            });
            
            if (selected === challenges[challengeIndex].correct) {
                gameState.score += 500;
                updateHUD();
            }
            
            const explanation = document.createElement('div');
            explanation.style.color = '#ffffff';
            explanation.style.marginTop = '20px';
            explanation.textContent = challenges[challengeIndex].explanation;
            document.getElementById('challengeOptions').appendChild(explanation);
            
            nextButton.style.display = 'block';
            nextButton.focus();
        }

        function triggerRandomEvent() {
            const events = [
                {
                    message: "Meteor Shower Detected! Click meteors for bonus points!",
                    action: () => {
                        const meteorGeometry = new THREE.SphereGeometry(0.5, 16, 16);
                        const meteorMaterial = new THREE.MeshBasicMaterial({ color: 0xff4444 });
                        const meteor = new THREE.Mesh(meteorGeometry, meteorMaterial);
                        meteor.position.set((Math.random() - 0.5) * 100, 0, (Math.random() - 0.5) * 100);
                        meteor.userData = { isMeteor: true };
                        scene.add(meteor);
                        intersectObjects.push(meteor);
                        setTimeout(() => {
                            scene.remove(meteor);
                            intersectObjects = intersectObjects.filter(obj => obj !== meteor);
                        }, 5000);
                    }
                }
            ];
            const event = events[Math.floor(Math.random() * events.length)];
            event.action();
            showNotification(event.message);
        }

        function showNotification(message) {
            const notification = document.createElement('div');
            notification.style.position = 'absolute';
            notification.style.top = '50%';
            notification.style.left = '50%';
            notification.style.transform = 'translate(-50%, -50%)';
            notification.style.background = 'rgba(0, 0, 0, 0.8)';
            notification.style.padding = '20px';
            notification.style.border = '2px solid #ff4444';
            notification.style.borderRadius = '10px';
            notification.style.color = '#ffffff';
            notification.style.fontSize = '18px';
            notification.style.zIndex = '2000';
            notification.textContent = message;
            document.body.appendChild(notification);
            setTimeout(() => document.body.removeChild(notification), 3000);
        }

        function animate() {
            animationId = requestAnimationFrame(animate);
            if (!gameState.isPaused) {
                solarSystem.planets.forEach(planetGroup => {
                    const speed = gameState.isHyperspeed ? planetGroup.userData.speed * 5 : planetGroup.userData.speed;
                    planetGroup.userData.angle += speed;
                    planetGroup.position.x = Math.cos(planetGroup.userData.angle) * planetGroup.userData.distance;
                    planetGroup.position.z = Math.sin(planetGroup.userData.angle) * planetGroup.userData.distance;
                    planetGroup.children[0].rotation.y += 0.01;
                });
                if (solarSystem.comet) {
                    solarSystem.comet.userData.angle += solarSystem.comet.userData.speed;
                    solarSystem.comet.position.x = Math.cos(solarSystem.comet.userData.angle) * solarSystem.comet.userData.distance;
                    solarSystem.comet.position.z = Math.sin(solarSystem.comet.userData.angle) * solarSystem.comet.userData.distance;
                }
                if (solarSystem.sun) {
                    solarSystem.sun.material.uniforms.time.value += 0.01;
                }
                if (solarSystem.nebula) {
                    solarSystem.nebula.material.uniforms.time.value += 0.01;
                }
                eventTimer += 0.01;
                if (eventTimer > 30 && Math.random() < 0.01) {
                    triggerRandomEvent();
                    eventTimer = 0;
                }
            }
            renderer.render(scene, camera);
        }

        function startGame() {
            const loadingScreen = document.getElementById('loadingScreen');
            setTimeout(() => {
                loadingScreen.style.opacity = '0';
                setTimeout(() => {
                    loadingScreen.style.display = 'none';
                }, 1000);
                initThreeJS();
            }, 2000);
        }

        startGame();
    </script>
</body>
</html>