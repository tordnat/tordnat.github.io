---
layout: post
title: Testing Three js on Jekyll
date: 2025-11-05
---

This is just a simple test to see whether it's possible to embed a Three js animation into this static website without breaking the site. 
<h1>Here's a spinning cube</h1>
<div id="three-container" style="width: 100%; max-width: 600px; height: 400px; margin: 20px 0; border: 1px solid #555; background: #000; position: relative; isolation: isolate;">
  <!-- Canvas will be inserted here -->
</div>
<style>
  /* Reset any global styles that might affect the canvas */
  #three-container canvas {
    display: block !important;
    width: 100% !important;
    height: 100% !important;
    filter: none !important;
    transform: none !important;
    mix-blend-mode: normal !important;
    opacity: 1 !important;
    image-rendering: auto !important;
    color-scheme: only light !important;
  }
  
  /* Ensure the container doesn't inherit problematic styles */
  #three-container {
    filter: none !important;
    transform-style: flat !important;
    mix-blend-mode: normal !important;
  }
</style>
<script type="module">
  import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.153.0/+esm';
  import { OrbitControls } from 'https://cdn.jsdelivr.net/npm/three@0.153.0/examples/jsm/controls/OrbitControls.js/+esm';
  
  // Basic Three.js scene setup
  const container = document.getElementById('three-container');
  
  // Clear container to ensure it's empty
  container.innerHTML = '';
  
  const scene = new THREE.Scene();
  scene.background = new THREE.Color(0x1a1a1a);
  
  const camera = new THREE.PerspectiveCamera(75, container.clientWidth / container.clientHeight, 0.1, 1000);
  camera.position.set(4, 4, 4);
  
  const renderer = new THREE.WebGLRenderer({ 
    antialias: true,
    alpha: false,
    powerPreference: "high-performance"
  });
  renderer.setSize(container.clientWidth, container.clientHeight);
  renderer.setPixelRatio(window.devicePixelRatio);
  renderer.shadowMap.enabled = true;
  renderer.shadowMap.type = THREE.PCFSoftShadowMap;
  
  // Append canvas to container
  container.appendChild(renderer.domElement);
  
  // Add OrbitControls
  const controls = new OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true;
  controls.dampingFactor = 0.05;
  controls.enableZoom = true;
  controls.enablePan = true;
  controls.enableRotate = true;
  controls.autoRotate = true;
  controls.autoRotateSpeed = 2.0;
  
  // Add lights
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
  scene.add(ambientLight);
  
  const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
  directionalLight.position.set(5, 8, 5);
  directionalLight.castShadow = true;
  directionalLight.shadow.camera.near = 0.1;
  directionalLight.shadow.camera.far = 20;
  directionalLight.shadow.camera.left = -5;
  directionalLight.shadow.camera.right = 5;
  directionalLight.shadow.camera.top = 5;
  directionalLight.shadow.camera.bottom = -5;
  directionalLight.shadow.mapSize.width = 2048;
  directionalLight.shadow.mapSize.height = 2048;
  scene.add(directionalLight);
  
  // Add a cube with solid color
  const cubeGeometry = new THREE.BoxGeometry(1.5, 1.5, 1.5);
  const cubeMaterial = new THREE.MeshStandardMaterial({ 
    color: new THREE.Color(0x0077ff),
    metalness: 0.2,
    roughness: 0.5
  });
  const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
  cube.castShadow = true;
  cube.receiveShadow = true;
  cube.position.y = 1;
  scene.add(cube);
  
  // Add a ground plane
  const planeGeometry = new THREE.PlaneGeometry(10, 10);
  const planeMaterial = new THREE.MeshStandardMaterial({ 
    color: new THREE.Color(0x333333),
    roughness: 0.9,
    metalness: 0.1
  });
  const plane = new THREE.Mesh(planeGeometry, planeMaterial);
  plane.rotation.x = -Math.PI / 2;
  plane.receiveShadow = true;
  scene.add(plane);
  
  // Add grid helper
  const gridHelper = new THREE.GridHelper(10, 10, 0x444444, 0x222222);
  scene.add(gridHelper);
  
  // Animation loop
  function animate() {
    requestAnimationFrame(animate);
    controls.update();
    renderer.render(scene, camera);
  }
  animate();
  
  // Handle window resize
  window.addEventListener('resize', () => {
    const width = container.clientWidth;
    const height = container.clientHeight;
    camera.aspect = width / height;
    camera.updateProjectionMatrix();
    renderer.setSize(width, height);
  });
</script>