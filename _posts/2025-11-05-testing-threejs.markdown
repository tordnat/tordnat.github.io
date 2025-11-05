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
Key changes to isolate from CSS interference:

CSS reset block: Added explicit styles to override any global CSS that might affect the canvas
isolation: isolate: Creates a new stacking context to prevent blend mode inheritance
color-scheme: only light: Forces light color scheme for the canvas to prevent dark mode inversions
Important flags: Used !important to ensure our styles override theme styles
Container clearing: Explicitly clear the container before adding the canvas
Explicit color objects: Using new THREE.Color() to ensure proper color creation

This should prevent any CSS from the theme interfering with the Three.js rendering!RetryLMOk, now only the shadow is white on the groundDiagnosed inverted shadow rendering issue and plotted fixes.The user is saying that the shadow on the ground is appearing white instead of dark. This is a classic issue - the shadow is likely being inverted or the plane material isn't properly configured to show shadows. This could be because:

The plane material color might be too light/white
The shadow might be inverted due to some CSS filter
The renderer's shadow settings might need adjustment

Let me fix the shadow rendering specifically.Ah, that's a classic shadow rendering issue! The white shadow means the shadow is being inverted. Let's fix the shadow rendering specifically:
markdown---
layout: post
title: Testing Three js on GH Jekyll
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
  
  // Configure shadows properly
  renderer.shadowMap.enabled = true;
  renderer.shadowMap.type = THREE.PCFSoftShadowMap;
  // Ensure shadows render as dark (not inverted)
  renderer.toneMappingExposure = 1;
  renderer.toneMapping = THREE.NoToneMapping;
  
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
  
  // Add lights - adjusted for better shadow contrast
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.3); // Reduced ambient
  scene.add(ambientLight);
  
  const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
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
  // Ensure shadow darkness
  directionalLight.shadow.intensity = 1; 
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
  
  // Add a ground plane - using MeshLambertMaterial for better shadow reception
  const planeGeometry = new THREE.PlaneGeometry(10, 10);
  const planeMaterial = new THREE.MeshLambertMaterial({ 
    color: new THREE.Color(0x808080), // Mid-gray color
    side: THREE.FrontSide
  });
  const plane = new THREE.Mesh(planeGeometry, planeMaterial);
  plane.rotation.x = -Math.PI / 2;
  plane.position.y = 0;
  plane.receiveShadow = true;
  scene.add(plane);
  
  // Add grid helper (slightly above ground to avoid z-fighting)
  const gridHelper = new THREE.GridHelper(10, 10, 0x444444, 0x333333);
  gridHelper.position.y = 0.01;
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