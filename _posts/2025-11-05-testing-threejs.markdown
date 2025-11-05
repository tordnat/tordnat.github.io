---
layout: post
title: Testing Three js on Jekyll
date: 2025-11-05
---

This is just a simple test to see whether it's possible to embed a Three js animation into this static website without breaking the site. 

<h1>Here's a spinning cube</h1>
<div id="three-container" style="width: 100%; max-width: 600px; height: 400px; margin: 20px 0; border: 1px solid var(--text-color, #555);"></div>
<script type="module">
  import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.153.0/+esm';
  import { OrbitControls } from 'https://cdn.jsdelivr.net/npm/three@0.153.0/examples/jsm/controls/OrbitControls.js/+esm';
  
  // Basic Three.js scene setup
  const container = document.getElementById('three-container');
  const scene = new THREE.Scene();
  
  // Set background color to match theme
  const isDarkMode = window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches;
  scene.background = new THREE.Color(isDarkMode ? 0x1a1a1a : 0xf0f0f0);
  
  const camera = new THREE.PerspectiveCamera(75, container.clientWidth / container.clientHeight, 0.1, 1000);
  camera.position.set(3, 3, 3);
  
  const renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(container.clientWidth, container.clientHeight);
  renderer.setPixelRatio(window.devicePixelRatio);
  // Enable shadows
  renderer.shadowMap.enabled = true;
  renderer.shadowMap.type = THREE.PCFSoftShadowMap; // Softer shadows
  container.appendChild(renderer.domElement);
  
  // Add OrbitControls for smooth interaction
  const controls = new OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true;
  controls.dampingFactor = 0.05;
  controls.enableZoom = true;
  controls.enablePan = true;
  controls.enableRotate = true;
  controls.autoRotate = true;
  controls.autoRotateSpeed = 2.0;
  
  // Add lights for better visualization
  const ambientLight = new THREE.AmbientLight(0x404040, 0.6);
  scene.add(ambientLight);
  
  const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
  directionalLight.position.set(5, 5, 5);
  directionalLight.castShadow = true;
  // Shadow camera settings
  directionalLight.shadow.camera.near = 0.1;
  directionalLight.shadow.camera.far = 20;
  directionalLight.shadow.camera.left = -5;
  directionalLight.shadow.camera.right = 5;
  directionalLight.shadow.camera.top = 5;
  directionalLight.shadow.camera.bottom = -5;
  // Higher resolution shadows
  directionalLight.shadow.mapSize.width = 2048;
  directionalLight.shadow.mapSize.height = 2048;
  scene.add(directionalLight);
  
  // Add a cube with better material
  const geometry = new THREE.BoxGeometry();
  const material = new THREE.MeshStandardMaterial({ 
    color: 0x0077ff,
    metalness: 0.3,
    roughness: 0.4,
  });
  const cube = new THREE.Mesh(geometry, material);
  cube.castShadow = true; // Enable shadow casting
  cube.receiveShadow = true; // Enable shadow receiving
  cube.position.y = 0.5; // Lift cube slightly
  scene.add(cube);
  
  // Add a ground plane to receive shadows
  const planeGeometry = new THREE.PlaneGeometry(10, 10);
  const planeMaterial = new THREE.MeshStandardMaterial({ 
    color: isDarkMode ? 0x2a2a2a : 0xcccccc,
    roughness: 0.8,
    metalness: 0.2
  });
  const plane = new THREE.Mesh(planeGeometry, planeMaterial);
  plane.rotation.x = -Math.PI / 2;
  plane.position.y = -0.5;
  plane.receiveShadow = true; // Important: plane receives shadows
  scene.add(plane);
  
  // Add axes helper for orientation
  const axesHelper = new THREE.AxesHelper(1.5);
  scene.add(axesHelper);
  
  // Animation loop
  function animate() {
    requestAnimationFrame(animate);
    controls.update(); // Required for damping to work
    renderer.render(scene, camera);
  }
  animate();
  
  // Handle window resize
  window.addEventListener('resize', () => {
    camera.aspect = container.clientWidth / container.clientHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(container.clientWidth, container.clientHeight);
  });
  
  // Handle theme changes (for auto appearance mode)
  window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (e) => {
    scene.background = new THREE.Color(e.matches ? 0x1a1a1a : 0xf0f0f0);
    plane.material.color.set(e.matches ? 0x2a2a2a : 0xcccccc);
  });
</script>