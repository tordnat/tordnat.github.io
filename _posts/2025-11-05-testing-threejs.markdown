---
layout: post
title: Testing Three js with Jekyll
date: 2025-11-05
---

This is just a simple test to see whether it's possible to embed a Three js animation into this static website without breaking the site. 

<h1>Here's a spinning cube</h1>
<div id="three-container" style="width: 100%; max-width: 600px; height: 400px; margin: 20px 0; border: 1px solid var(--text-color, #000);"></div>
<script src="https://cdn.jsdelivr.net/npm/three@0.153.0/build/three.min.js"></script>
<script>
  // Wait for DOM to be fully loaded
  document.addEventListener('DOMContentLoaded', function() {
    // Basic Three.js scene setup
    const container = document.getElementById('three-container');
    const scene = new THREE.Scene();
    
    // Set background color to match theme
    const isDarkMode = window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches;
    scene.background = new THREE.Color(isDarkMode ? 0x0a0a0a : 0xffffff);
    
    const camera = new THREE.PerspectiveCamera(75, container.clientWidth / container.clientHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(container.clientWidth, container.clientHeight);
    container.appendChild(renderer.domElement);
    
    // Add a spinning cube
    const geometry = new THREE.BoxGeometry();
    const material = new THREE.MeshBasicMaterial({ color: 0x0077ff });
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);
    camera.position.z = 5;
    
    // Animation loop
    function animate() {
      requestAnimationFrame(animate);
      cube.rotation.x += 0.01;
      cube.rotation.y += 0.01;
      renderer.render(scene, camera);
    }
    animate();
    
    // Handle window resize
    window.addEventListener('resize', function() {
      camera.aspect = container.clientWidth / container.clientHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(container.clientWidth, container.clientHeight);
    });
    
    // Handle theme changes (for auto appearance mode)
    window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (e) => {
      scene.background = new THREE.Color(e.matches ? 0x0a0a0a : 0xffffff);
    });
  });
</script>