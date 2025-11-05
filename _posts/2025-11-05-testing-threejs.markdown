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
  scene.background = new THREE.Color(isDarkMode ? 0x1a1a1a : 0xffffff);
  
  const camera = new THREE.PerspectiveCamera(75, container.clientWidth / container.clientHeight, 0.1, 1000);
  camera.position.set(3, 3, 3);
  
  const renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(container.clientWidth, container.clientHeight);
  renderer.setPixelRatio(window.devicePixelRatio);
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
  const ambientLight = new THREE.AmbientLight(0x404040, 0.5);
  scene.add(ambientLight);
  
  const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
  directionalLight.position.set(5, 5, 5);
  directionalLight.castShadow = true;
  scene.add(directionalLight);
  
  // Add a cube with better material to show lighting
  const geometry = new THREE.BoxGeometry();
  const material = new THREE.MeshPhongMaterial({ 
    color: 0x0077ff,
    shininess: 100,
    specular: 0x222222
  });
  const cube = new THREE.Mesh(geometry, material);
  scene.add(cube);
  
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
    scene.background = new THREE.Color(e.matches ? 0x1a1a1a : 0xffffff);
  });
</script>