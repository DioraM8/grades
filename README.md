<!DOCTYPE html>

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cosmic Patterns: Solar System Explorer</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

```
    body {
        font-family: 'Arial', sans-serif;
        background: radial-gradient(ellipse at center, #0f0f23 0%, #000000 100%);
        overflow: hidden;
        color: white;
    }

    #gameContainer {
        position: relative;
        width: 100vw;
        height: 100vh;
    }

    #canvas {
        position: absolute;
        top: 0;
        left: 0;
        cursor: pointer;
    }

    #ui {
        position: absolute;
        top: 20px;
        left: 20px;
        z-index: 100;
        background: rgba(0, 0, 0, 0.8);
        padding: 20px;
        border-radius: 15px;
        border: 2px solid #00ffff;
        box-shadow: 0 0 30px rgba(0, 255, 255, 0.3);
        backdrop-filter: blur(10px);
        max-width: 300px;
    }

    #challengePanel {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background: linear-gradient(135deg, rgba(25, 25, 112, 0.95), rgba(75, 0, 130, 0.95));
        padding: 30px;
        border-radius: 20px;
        border: 3px solid #ff6b6b;
        box-shadow: 0 0 50px rgba(255, 107, 107, 0.5);
        display: none;
        z-index: 200;
        max-width: 500px;
        text-align: center;
    }

    h1 {
        color: #00ffff;
        margin-bottom: 15px;
        text-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
        font-size: 1.2em;
    }

    h2 {
        color: #ff6b6b;
        margin-bottom: 20px;
        text-shadow: 0 0 10px rgba(255, 107, 107, 0.5);
    }

    .stat {
        margin: 10px 0;
        padding: 8px;
        background: rgba(255, 255, 255, 0.1);
        border-radius: 8px;
        border-left: 4px solid #00ffff;
    }

    .challenge-option {
        display: block;
        width: 100%;
        margin: 10px 0;
        padding: 15px;
        background: linear-gradient(45deg, #4a90e2, #357abd);
        border: none;
        border-radius: 10px;
        color: white;
        font-size: 16px;
        cursor: pointer;
        transition: all 0.3s ease;
        border: 2px solid transparent;
    }

    .challenge-option:hover {
        background: linear-gradient(45deg, #357abd, #4a90e2);
        border-color: #00ffff;
        box-shadow: 0 0 20px rgba(0, 255, 255, 0.4);
        transform: translateY(-2px);
    }

    .challenge-option.correct {
        background: linear-gradient(45deg, #28a745, #20c997);
        border-color: #00ff00;
    }

    .challenge-option.incorrect {
        background: linear-gradient(45deg, #dc3545, #c82333);
        border-color: #ff0000;
    }

    #nextButton {
        margin-top: 20px;
        padding: 12px 25px;
        background: linear-gradient(45deg, #ff6b6b, #ff5252);
        border: none;
        border-radius: 25px;
        color: white;
        font-size: 16px;
        cursor: pointer;
        transition: all 0.3s ease;
    }

    #nextButton:hover {
        background: linear-gradient(45deg, #ff5252, #ff6b6b);
        box-shadow: 0 0 20px rgba(255, 107, 107, 0.5);
        transform: scale(1.05);
    }

    .instructions {
        position: absolute;
        bottom: 20px;
        left: 20px;
        background: rgba(0, 0, 0, 0.8);
        padding: 15px;
        border-radius: 10px;
        border: 2px solid #ffff00;
        box-shadow: 0 0 20px rgba(255, 255, 0, 0.3);
        max-width: 400px;
    }

    .particle {
        position: absolute;
        width: 2px;
        height: 2px;
        background: white;
        border-radius: 50%;
        pointer-events: none;
    }

    @keyframes twinkle {
        0%, 100% { opacity: 0.3; }
        50% { opacity: 1; }
    }

    @keyframes float {
        0%, 100% { transform: translateY(0px); }
        50% { transform: translateY(-10px); }
    }

    .floating {
        animation: float 4s ease-in-out infinite;
    }
</style>
```

</head>
<body>
    <div id="gameContainer">
        <canvas id="canvas"></canvas>

```
    <div id="ui" class="floating">
        <h1>🌌 Cosmic Patterns Explorer</h1>
        <div class="stat">Score: <span id="score">0</span></div>
        <div class="stat">Discoveries: <span id="discoveries">0</span>/8</div>
        <div class="stat">Pattern Level: <span id="level">Beginner</span></div>
        <div style="margin-top: 15px; font-size: 14px; color: #ffff00;">
            Click planets to discover patterns and unlock challenges!
        </div>
    </div>

    <div id="challengePanel">
        <h2 id="challengeTitle">Pattern Challenge</h2>
        <p id="challengeQuestion"></p>
        <div id="challengeOptions"></div>
        <button id="nextButton" style="display: none;">Continue Exploring</button>
    </div>

    <div class="instructions">
        <strong>🚀 Mission:</strong> Explore the solar system to uncover cosmic patterns! Each planet holds secrets about gravity, formation, and planetary relationships. Click to investigate!
    </div>
</div>

<script>
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    const ui = document.getElementById('ui');
    const challengePanel = document.getElementById('challengePanel');

    let gameState = {
        score: 0,
        discoveries: 0,
        level: 'Beginner',
        currentChallenge: null,
        discoveredPlanets: new Set(),
        stars: []
    };

    // Resize canvas
    function resizeCanvas() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
    }
    resizeCanvas();
    window.addEventListener('resize', resizeCanvas);

    // Create starfield
    function createStars() {
        gameState.stars = [];
        for (let i = 0; i < 200; i++) {
            gameState.stars.push({
                x: Math.random() * canvas.width,
                y: Math.random() * canvas.height,
                size: Math.random() * 2 + 0.5,
                twinkle: Math.random() * Math.PI * 2,
                speed: Math.random() * 0.02 + 0.01
            });
        }
    }

    // Solar system data with educational content
    const solarSystem = {
        sun: { x: 0, y: 0, radius: 30, color: '#ffff00', name: 'Sun' },
        planets: [
            {
                name: 'Mercury', distance: 80, radius: 4, color: '#8c7853',
                angle: 0, speed: 0.02, moons: 0,
                facts: "Closest to Sun, extreme temperatures, no atmosphere",
                pattern: "Small, rocky, no moons due to Sun's gravity"
            },
            {
                name: 'Venus', distance: 120, radius: 6, color: '#ffc649',
                angle: Math.PI/4, speed: 0.015, moons: 0,
                facts: "Hottest planet, thick atmosphere, backwards rotation",
                pattern: "Dense atmosphere traps heat - greenhouse effect"
            },
            {
                name: 'Earth', distance: 160, radius: 7, color: '#4a90e2',
                angle: Math.PI/2, speed: 0.01, moons: 1,
                facts: "Perfect distance from Sun, liquid water, one large moon",
                pattern: "Goldilocks zone - just right for life"
            },
            {
                name: 'Mars', distance: 200, radius: 5, color: '#cd5c5c',
                angle: Math.PI, speed: 0.008, moons: 2,
                facts: "Red from iron oxide, polar ice caps, two small moons",
                pattern: "Thin atmosphere, evidence of ancient water"
            },
            {
                name: 'Jupiter', distance: 300, radius: 20, color: '#d2691e',
                angle: Math.PI*1.5, speed: 0.004, moons: 79,
                facts: "Gas giant, Great Red Spot, asteroid protector",
                pattern: "Massive gravity captures many moons and deflects asteroids"
            },
            {
                name: 'Saturn', distance: 380, radius: 18, color: '#fab1a0',
                angle: 0, speed: 0.003, moons: 83,
                facts: "Beautiful rings, less dense than water, many moons",
                pattern: "Ring system from broken moons and asteroids"
            },
            {
                name: 'Uranus', distance: 450, radius: 12, color: '#74b9ff',
                angle: Math.PI/3, speed: 0.002, moons: 27,
                facts: "Tilted on side, methane atmosphere, faint rings",
                pattern: "Unusual tilt suggests massive ancient collision"
            },
            {
                name: 'Neptune', distance: 520, radius: 11, color: '#0984e3',
                angle: Math.PI*2/3, speed: 0.0015, moons: 14,
                facts: "Windiest planet, deep blue color, distant and cold",
                pattern: "Discovered by mathematical prediction, not observation"
            }
        ]
    };

    // Challenge questions that promote critical thinking
    const challenges = [
        {
            title: "Gravity's Influence",
            question: "Why does Jupiter have 79 moons while Mercury has none?",
            options: [
                "Jupiter is older than Mercury",
                "Jupiter's massive gravity can capture and hold many objects",
                "Jupiter is further from the Sun",
                "Jupiter is made of gas"
            ],
            correct: 1,
            explanation: "Jupiter's enormous mass creates strong gravitational pull that can capture passing objects. Mercury, being small and close to the Sun, can't compete with the Sun's gravity."
        },
        {
            title: "Pattern Recognition",
            question: "What pattern do you notice about inner vs outer planets?",
            options: [
                "Inner planets are larger and have more moons",
                "Inner planets are small, rocky, with few moons; outer planets are large, gaseous, with many moons",
                "All planets are the same size",
                "Outer planets are hotter"
            ],
            correct: 1,
            explanation: "The solar system formed with rocky materials closer to the Sun and gas/ice further out, creating two distinct planetary types."
        },
        {
            title: "Protective Patterns",
            question: "How does Jupiter protect Earth from asteroids?",
            options: [
                "Jupiter blocks asteroids with its rings",
                "Jupiter's gravity deflects or captures asteroids before they reach inner planets",
                "Jupiter creates a magnetic field barrier",
                "Jupiter doesn't protect Earth"
            ],
            correct: 1,
            explanation: "Jupiter acts like a cosmic vacuum cleaner, using its massive gravity to capture or deflect asteroids that might otherwise hit Earth."
        },
        {
            title: "Formation Connections",
            question: "Why are Saturn's rings likely formed from?",
            options: [
                "Dust from space",
                "Broken apart moons and captured asteroids",
                "Gas from Saturn's atmosphere",
                "Light from the Sun"
            ],
            correct: 1,
            explanation: "Saturn's rings are made of ice and rock fragments, likely from moons that got too close and were torn apart by tidal forces."
        },
        {
            title: "Comparative Analysis",
            question: "What makes Earth special in the 'Goldilocks Zone'?",
            options: [
                "It's the biggest planet",
                "It's the right distance from the Sun for liquid water",
                "It has the most moons",
                "It spins the fastest"
            ],
            correct: 1,
            explanation: "Earth sits in the habitable zone where temperatures allow liquid water - not too hot like Venus, not too cold like Mars."
        },
        {
            title: "Scale and Perspective",
            question: "If Earth were a marble, approximately how big would Jupiter be?",
            options: [
                "Another marble",
                "A ping pong ball",
                "A basketball",
                "A softball"
            ],
            correct: 2,
            explanation: "Jupiter is about 11 times wider than Earth. This huge size difference affects everything from gravity to moon systems."
        },
        {
            title: "System Interactions",
            question: "How did Earth likely get its Moon?",
            options: [
                "It was captured from space",
                "A Mars-sized object collided with early Earth",
                "It formed at the same time as Earth",
                "It split off from Earth"
            ],
            correct: 1,
            explanation: "The Giant Impact Hypothesis suggests a Mars-sized object hit Earth, and the debris formed our unusually large Moon."
        },
        {
            title: "Interconnected Systems",
            question: "How do we discover planets around other stars?",
            options: [
                "We see them with telescopes",
                "We detect tiny brightness changes when they pass in front of their star",
                "We hear radio signals from them",
                "We can't discover them"
            ],
            correct: 1,
            explanation: "Most exoplanets are found using the transit method - measuring tiny dips in starlight when planets pass between us and their star."
        }
    ];

    function updateUI() {
        document.getElementById('score').textContent = gameState.score;
        document.getElementById('discoveries').textContent = gameState.discoveries;
        
        const levels = ['Beginner', 'Explorer', 'Astronomer', 'Cosmic Sage'];
        const levelIndex = Math.min(Math.floor(gameState.discoveries / 2), levels.length - 1);
        gameState.level = levels[levelIndex];
        document.getElementById('level').textContent = gameState.level;
    }

    function drawStars() {
        gameState.stars.forEach(star => {
            star.twinkle += star.speed;
            const brightness = (Math.sin(star.twinkle) + 1) / 2;
            
            ctx.globalAlpha = brightness * 0.8 + 0.2;
            ctx.fillStyle = 'white';
            ctx.beginPath();
            ctx.arc(star.x, star.y, star.size, 0, Math.PI * 2);
            ctx.fill();
        });
        ctx.globalAlpha = 1;
    }

    function drawSolarSystem() {
        const centerX = canvas.width / 2;
        const centerY = canvas.height / 2;

        // Draw orbits
        ctx.strokeStyle = 'rgba(255, 255, 255, 0.1)';
        ctx.lineWidth = 1;
        solarSystem.planets.forEach(planet => {
            ctx.beginPath();
            ctx.arc(centerX, centerY, planet.distance, 0, Math.PI * 2);
            ctx.stroke();
        });

        // Draw Sun with glow effect
        const gradient = ctx.createRadialGradient(centerX, centerY, 0, centerX, centerY, solarSystem.sun.radius * 2);
        gradient.addColorStop(0, '#ffff00');
        gradient.addColorStop(0.5, '#ffaa00');
        gradient.addColorStop(1, 'rgba(255, 170, 0, 0)');
        
        ctx.fillStyle = gradient;
        ctx.beginPath();
        ctx.arc(centerX, centerY, solarSystem.sun.radius * 2, 0, Math.PI * 2);
        ctx.fill();
        
        ctx.fillStyle = solarSystem.sun.color;
        ctx.beginPath();
        ctx.arc(centerX, centerY, solarSystem.sun.radius, 0, Math.PI * 2);
        ctx.fill();

        // Draw planets
        solarSystem.planets.forEach((planet, index) => {
            planet.angle += planet.speed;
            
            const x = centerX + Math.cos(planet.angle) * planet.distance;
            const y = centerY + Math.sin(planet.angle) * planet.distance;
            
            // Planet glow for discovered planets
            if (gameState.discoveredPlanets.has(index)) {
                const glowGradient = ctx.createRadialGradient(x, y, 0, x, y, planet.radius * 3);
                glowGradient.addColorStop(0, planet.color);
                glowGradient.addColorStop(1, 'rgba(0, 255, 255, 0)');
                
                ctx.fillStyle = glowGradient;
                ctx.beginPath();
                ctx.arc(x, y, planet.radius * 3, 0, Math.PI * 2);
                ctx.fill();
            }
            
            // Planet
            ctx.fillStyle = planet.color;
            ctx.beginPath();
            ctx.arc(x, y, planet.radius, 0, Math.PI * 2);
            ctx.fill();
            
            // Planet name for discovered planets
            if (gameState.discoveredPlanets.has(index)) {
                ctx.fillStyle = 'white';
                ctx.font = '12px Arial';
                ctx.textAlign = 'center';
                ctx.fillText(planet.name, x, y - planet.radius - 15);
            }
            
            planet.screenX = x;
            planet.screenY = y;
        });
    }

    function handleClick(event) {
        const rect = canvas.getBoundingClientRect();
        const clickX = event.clientX - rect.left;
        const clickY = event.clientY - rect.top;
        
        solarSystem.planets.forEach((planet, index) => {
            const distance = Math.sqrt(
                Math.pow(clickX - planet.screenX, 2) + 
                Math.pow(clickY - planet.screenY, 2)
            );
            
            if (distance <= planet.radius + 10) {
                if (!gameState.discoveredPlanets.has(index)) {
                    gameState.discoveredPlanets.add(index);
                    gameState.discoveries++;
                    gameState.score += 100;
                    
                    // Show challenge
                    if (challenges[index]) {
                        showChallenge(challenges[index]);
                    }
                    
                    updateUI();
                }
            }
        });
    }

    function showChallenge(challenge) {
        gameState.currentChallenge = challenge;
        
        document.getElementById('challengeTitle').textContent = challenge.title;
        document.getElementById('challengeQuestion').textContent = challenge.question;
        
        const optionsContainer = document.getElementById('challengeOptions');
        optionsContainer.innerHTML = '';
        
        challenge.options.forEach((option, index) => {
            const button = document.createElement('button');
            button.className = 'challenge-option';
            button.textContent = option;
            button.onclick = () => handleChallengeAnswer(index);
            optionsContainer.appendChild(button);
        });
        
        document.getElementById('nextButton').style.display = 'none';
        challengePanel.style.display = 'block';
    }

    function handleChallengeAnswer(selectedIndex) {
        const challenge = gameState.currentChallenge;
        const buttons = document.querySelectorAll('.challenge-option');
        
        buttons.forEach((button, index) => {
            button.disabled = true;
            if (index === challenge.correct) {
                button.classList.add('correct');
            } else if (index === selectedIndex && index !== challenge.correct) {
                button.classList.add('incorrect');
            }
        });
        
        if (selectedIndex === challenge.correct) {
            gameState.score += 200;
            updateUI();
        }
        
        // Show explanation
        setTimeout(() => {
            document.getElementById('challengeQuestion').textContent = challenge.explanation;
            document.getElementById('nextButton').style.display = 'block';
        }, 1500);
    }

    function closeChallenge() {
        challengePanel.style.display = 'none';
        gameState.currentChallenge = null;
    }

    function gameLoop() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        
        drawStars();
        drawSolarSystem();
        
        requestAnimationFrame(gameLoop);
    }

    // Event listeners
    canvas.addEventListener('click', handleClick);
    document.getElementById('nextButton').addEventListener('click', closeChallenge);

    // Initialize game
    createStars();
    updateUI();
    gameLoop();

    // Add some ambient particles
    function createParticle() {
        const particle = document.createElement('div');
        particle.className = 'particle';
        particle.style.left = Math.random() * window.innerWidth + 'px';
        particle.style.top = Math.random() * window.innerHeight + 'px';
        particle.style.animationDelay = Math.random() * 3 + 's';
        particle.style.animation = 'twinkle 3s infinite';
        document.body.appendChild(particle);
        
        setTimeout(() => {
            particle.remove();
        }, 6000);
    }

    setInterval(createParticle, 500);
</script>
```

</body>
</html>