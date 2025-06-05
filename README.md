<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r165/three.min.js"></script>
<script>
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

    const cursor = document.getElementById('customCursor');
    document.addEventListener('mousemove', (e) => {
        cursor.style.left = e.clientX - 10 + 'px';
        cursor.style.top = e.clientY - 10 + 'px';
        mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
        mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;
        raycaster.setFromCamera(mouse, camera);
        const intersects = raycaster.intersectObjects(intersectObjects);
        cursor.style.borderColor = intersects.length > 0 ? '#ff0000' : '#00ffff';
        cursor.style.boxShadow = intersects.length > 0 
            ? '0 0 30px #ff0000, inset 0 0 15px #ff0000' 
            : '0 0 20px #00ffff, inset 0 0 10px #00ffff';
    });

    const planetData = [/* ... unchanged ... */];
    const challenges = [/* ... unchanged ... */];

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
        const texture = loader.load(`textures/${data.name.toLowerCase()}.jpg`); // Placeholder; replace with actual paths
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
                const clicked = intersects[0].object