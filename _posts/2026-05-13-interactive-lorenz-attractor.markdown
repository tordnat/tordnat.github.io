---
layout: post
title: Interactive Lorenz Attractor
date: 2026-05-13
---

I made this animation for the course Modelling and Simulation TTK4130 at NTNU. 

<div id="lorenz-container" style="width: 100%; height: 60vh; min-height: 400px; border: 1px solid #555; position: relative; cursor: crosshair;">
  <div id="lorenz-hint" style="position: absolute; bottom: 8px; left: 0; right: 0; text-align: center; color: rgba(255,255,255,0.45); font-size: 12px; pointer-events: none; font-family: sans-serif; user-select: none;">
    Drag to orbit &nbsp;&middot;&nbsp; Scroll to zoom &nbsp;&middot;&nbsp; Click to spawn a trajectory &mdash; 10 remaining
  </div>
</div>

<script type="importmap">
  {
    "imports": {
      "three": "https://cdn.jsdelivr.net/npm/three@0.150.1/build/three.module.js"
    }
  }
</script>

<script type="module">
    import * as THREE from 'three';
    import { OrbitControls } from 'https://cdn.jsdelivr.net/npm/three@0.150.1/examples/jsm/controls/OrbitControls.js';

    const container = document.getElementById('lorenz-container');
    const hint = document.getElementById('lorenz-hint');

    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x1e1e1e);

    const camera = new THREE.PerspectiveCamera(75, container.clientWidth / container.clientHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(container.clientWidth, container.clientHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    container.appendChild(renderer.domElement);

    const controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    controls.dampingFactor = 0.05;
    controls.enableZoom = true;
    controls.zoomSpeed = 0.6;
    controls.enablePan = false;
    controls.enableRotate = true;
    controls.autoRotate = true;
    controls.autoRotateSpeed = 0.4;

    function onResize() {
        const w = container.clientWidth;
        const h = container.clientHeight;
        camera.aspect = w / h;
        camera.updateProjectionMatrix();
        renderer.setSize(w, h);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    }
    window.addEventListener('resize', onResize, false);
    if (window.ResizeObserver) {
        new ResizeObserver(entries => {
            for (const entry of entries) {
                if (entry.target === container) onResize();
            }
        }).observe(container);
    }

    scene.add(new THREE.AmbientLight(0x404040, 0.6));
    const dirLight = new THREE.DirectionalLight(0xffffff, 0.8);
    dirLight.position.set(10, 10, 10);
    scene.add(dirLight);

    const crossingPoint = new THREE.Vector3(0, 0, 1.4);

    // Initial camera position — same view as the old manual orbit start
    camera.position.set(5, 1.2, 1.4);
    controls.target.copy(crossingPoint);
    controls.update();

    // Lorenz parameters
    const sigma = 10, rho = 28, beta = 8 / 3, dt = 0.01;

    function generateLorenzTrajectory(x0, y0, z0, steps) {
        const pts = [];
        let x = x0, y = y0, z = z0;
        for (let i = 0; i < steps; i++) {
            const dx = sigma * (y - x);
            const dy = x * (rho - z) - y;
            const dz = x * y - beta * z;
            x += dx * dt;
            y += dy * dt;
            z += dz * dt;
            pts.push(new THREE.Vector3(x * 0.1, y * 0.1, z * 0.1));
        }
        return pts;
    }

    // First color is yellow (initial), rest for click-spawned trajectories
    const COLORS = [
        0xffd700, // yellow  — initial
        0x00e5ff, // cyan
        0xff6600, // orange
        0x00ff88, // green
        0xff0066, // pink
        0xaa44ff, // purple
        0xff3322, // red
        0x44aaff, // sky blue
        0xffffff, // white
        0xaaff00, // lime
        0xff88cc, // light pink
    ];

    const MAX_CLICK_TRAJECTORIES = 10;
    const ANIMATION_SPEED = 3;

    function createTrajectory(x0, y0, z0, color, steps = 100000) {
        const lorenzPoints = generateLorenzTrajectory(x0, y0, z0, steps);
        const geometry = new THREE.BufferGeometry();
        const line = new THREE.Line(
            geometry,
            new THREE.LineBasicMaterial({ color, transparent: true, opacity: 0.8 })
        );
        scene.add(line);

        const sphere = new THREE.Mesh(
            new THREE.SphereGeometry(0.05),
            new THREE.MeshPhongMaterial({
                color,
                emissive: new THREE.Color(color).multiplyScalar(0.25),
                shininess: 100,
            })
        );
        scene.add(sphere);

        return { lorenzPoints, geometry, line, sphere, currentFrame: 0, animatedPoints: [] };
    }

    function disposeTrajectory(traj) {
        scene.remove(traj.line);
        scene.remove(traj.sphere);
        traj.geometry.dispose();
        traj.line.material.dispose();
        traj.sphere.geometry.dispose();
        traj.sphere.material.dispose();
    }

    function stepTrajectory(traj) {
        for (let i = 0; i < ANIMATION_SPEED && traj.currentFrame < traj.lorenzPoints.length; i++) {
            traj.animatedPoints.push(traj.lorenzPoints[traj.currentFrame++]);
        }
        if (traj.animatedPoints.length > 0) {
            traj.geometry.setFromPoints(traj.animatedPoints);
            traj.sphere.position.copy(traj.animatedPoints[traj.animatedPoints.length - 1]);
        }
        if (traj.currentFrame >= traj.lorenzPoints.length) {
            traj.currentFrame = 0;
            traj.animatedPoints = [];
        }
    }

    // Initial yellow trajectory — always present
    const initialTrajectory = createTrajectory(1, 1, 1, COLORS[0]);

    // Click-spawned trajectories
    const clickTrajectories = [];
    let totalClicks = 0;

    function updateHint() {
        const remaining = MAX_CLICK_TRAJECTORIES - clickTrajectories.length;
        const spawnText = remaining > 0
            ? `Click to spawn a trajectory — ${remaining} remaining`
            : 'Click to replace the oldest trajectory';
        hint.innerHTML = `Drag to orbit &nbsp;&middot;&nbsp; Scroll to zoom &nbsp;&middot;&nbsp; ${spawnText}`;
    }

    // Distinguish click from drag using pointer events
    let pointerDownPos = null;

    renderer.domElement.addEventListener('pointerdown', (e) => {
        pointerDownPos = { x: e.clientX, y: e.clientY };
    });

    renderer.domElement.addEventListener('pointerup', (e) => {
        if (!pointerDownPos) return;
        const dx = e.clientX - pointerDownPos.x;
        const dy = e.clientY - pointerDownPos.y;
        pointerDownPos = null;
        if (Math.sqrt(dx * dx + dy * dy) > 5) return; // drag — ignore

        // Remove oldest click trajectory if at cap
        if (clickTrajectories.length >= MAX_CLICK_TRAJECTORIES) {
            disposeTrajectory(clickTrajectories.shift());
        }

        // Map 2-D click to 3-D world position on a plane facing the camera
        const rect = renderer.domElement.getBoundingClientRect();
        const nx = ((e.clientX - rect.left) / rect.width) * 2 - 1;
        const ny = -((e.clientY - rect.top) / rect.height) * 2 + 1;

        const raycaster = new THREE.Raycaster();
        raycaster.setFromCamera(new THREE.Vector2(nx, ny), camera);

        const planeNormal = new THREE.Vector3();
        camera.getWorldDirection(planeNormal);
        const plane = new THREE.Plane(planeNormal, -planeNormal.dot(crossingPoint));
        const worldPos = new THREE.Vector3();
        if (!raycaster.ray.intersectPlane(plane, worldPos)) return;

        // Convert Three.js scale back to Lorenz coordinates (scale factor 0.1)
        const colorIndex = (totalClicks % (COLORS.length - 1)) + 1;
        totalClicks++;

        // Use fewer steps for click trajectories so computation stays fast
        const traj = createTrajectory(worldPos.x * 10, worldPos.y * 10, worldPos.z * 10, COLORS[colorIndex], 50000);
        clickTrajectories.push(traj);
        updateHint();
    });

    function animate() {
        requestAnimationFrame(animate);

        controls.update();

        stepTrajectory(initialTrajectory);
        for (const traj of clickTrajectories) stepTrajectory(traj);

        renderer.render(scene, camera);
    }

    animate();
</script>