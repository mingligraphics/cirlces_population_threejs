<script>
  import { onMount } from 'svelte';
  import * as THREE from 'three';
  import { forceSimulation, forceManyBody, forceCenter, forceCollide, forceX, forceY } from 'd3-force';
  import Steps from "/components/Steps.svelte";

  let container;
  let container2;
  let scene, scene2, camera, camera2, renderer, renderer2;
  let dots = [];
  let dots2 = [];
  const numNodes = 1786;
  const numNodes2 = 1585;
  let radius;
  let highlightDots = false;
  let highlightDots2Active = false;
  let animatePosition = false;
  let duplicateCircle = false;

  const nodes = Array.from({ length: numNodes }, () => ({
    x: (Math.random() - 0.5) * 2 * radius,
    y: (Math.random() - 0.5) * 2 * radius,
  }));

  const nodes2 = Array.from({ length: numNodes2 }, () => ({
    x: (Math.random() - 0.5) * 2 * radius,
    y: (Math.random() - 0.5) * 2 * radius,
  }));

  const simulation = forceSimulation(nodes)
    .force("charge", forceManyBody().strength(-2))
    .force("center", forceCenter(0, 0))
    .force("x", forceX(0).strength(0.1))
    .force("y", forceY(0).strength(0.1))
    .force("collide", forceCollide(4));

  let simulation2 = forceSimulation(nodes2)
    .force("charge", forceManyBody().strength(-2))
    .force("center", forceCenter(0, 0))
    .force("x", forceX(0).strength(0.1))
    .force("y", forceY(0).strength(0.1))
    .force("collide", forceCollide(4));

  onMount(() => {
    scene = new THREE.Scene();
    scene.background = new THREE.Color(0xffffff);

    camera = new THREE.PerspectiveCamera(75, 1, 0.1, 1000);
    camera.position.z = 300;

    renderer = new THREE.WebGLRenderer();
    resizeCanvas(renderer, container);
    window.addEventListener('resize', () => resizeCanvas(renderer, container));
    container.appendChild(renderer.domElement);

    const geometry = new THREE.SphereGeometry(2, 32, 32);
    const material = new THREE.MeshBasicMaterial({ color: '#D991B2' });

    for (let i = 0; i < numNodes; i++) {
      const dot = new THREE.Mesh(geometry, material.clone());
      scene.add(dot);
      dots.push(dot);
    }

    simulation.on('tick', () => {
      nodes.forEach((node, i) => {
        dots[i].position.set(node.x, node.y, 0);
      });
      highlightClosestDots(dots, nodes);
    });

    animate();
  });

  function highlightClosestDots(dotsArray, nodesArray) {
    const highlightIndices = highlightDots ? findClosestDots(nodesArray, 5) : [];
    dotsArray.forEach((dot, index) => {
      if (highlightIndices.includes(index)) {
        dot.material.color.set('#64BC52');
        dot.scale.set(2, 2, 2);
      } else {
        dot.material.color.set('#F8AA9A');
        dot.scale.set(1, 1, 1);
      }
    });
  }

  function highlightClosestDots2(dotsArray, nodesArray) {
    const highlightIndices = highlightDots2Active ? findClosestDots(nodesArray, 19) : [];
    dotsArray.forEach((dot, index) => {
      if (highlightIndices.includes(index)) {
        dot.material.color.set('#64BC52');
        dot.scale.set(2, 2, 2);
      } else {
        dot.material.color.set('#CED3DD');
        dot.scale.set(1, 1, 1);
      }
    });
  }

  function findClosestDots(nodes, count) {
    const distances = nodes.map((node, index) => ({
      index,
      distance: Math.sqrt(node.x * node.x + node.y * node.y)
    }));
    distances.sort((a, b) => a.distance - b.distance);
    return distances.slice(0, count).map(d => d.index);
  }

  function resizeCanvas(renderer, container) {
    const width = Math.min(window.innerWidth, 550);
    const height = Math.min(window.innerHeight, 550);
    renderer.setSize(width, height);
    radius = width / 2;
    camera.aspect = width / height;
    camera.updateProjectionMatrix();
  }

  function initSecondCircle() {
    // Dispose of the previous WebGL renderer for the second circle if it exists
    if (renderer2) {
      renderer2.dispose();
      scene2 = null;
      camera2 = null;
      dots2 = [];
    }

    scene2 = new THREE.Scene();
    scene2.background = new THREE.Color(0xffffff);

    camera2 = new THREE.PerspectiveCamera(75, 1, 0.1, 1000);
    camera2.position.z = 300;

    renderer2 = new THREE.WebGLRenderer();
    resizeCanvas(renderer2, container2);
    window.addEventListener('resize', () => resizeCanvas(renderer2, container2));
    container2.innerHTML = ''; // Clear any previous renderer element
    container2.appendChild(renderer2.domElement);

    const geometry = new THREE.SphereGeometry(2, 32, 32);
    const material = new THREE.MeshBasicMaterial({ color: '#C7E3BB' });

    for (let i = 0; i < numNodes2; i++) {
      const dot2 = new THREE.Mesh(geometry, material.clone());
      scene2.add(dot2);
      dots2.push(dot2);
    }

    // Reset the second simulation to ensure it doesn't retain old state
    simulation2 = forceSimulation(nodes2)
      .force("charge", forceManyBody().strength(-2))
      .force("center", forceCenter(0, 0))
      .force("x", forceX(0).strength(0.1))
      .force("y", forceY(0).strength(0.1))
      .force("collide", forceCollide(4))
      .on('tick', () => {
        nodes2.forEach((node, i) => {
          dots2[i].position.set(node.x, node.y, 0);
        });
        highlightClosestDots2(dots2, nodes2);
      });
  }

  function animate() {
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
    if (duplicateCircle && renderer2 && scene2 && camera2) {
      renderer2.render(scene2, camera2);
    }
  }

  let secondCircleInitialized = false;

  $: if (animatePosition && !secondCircleInitialized) {
    setTimeout(() => {
      duplicateCircle = true;
      initSecondCircle();
      secondCircleInitialized = true;
    }, 500);
  }

  let currentStep;

  $: {
    if (currentStep === 0) {
      highlightDots = false;
      highlightDots2Active = false;
      animatePosition = false;
      duplicateCircle = false;
      secondCircleInitialized = false;

      // Reset all dots
      highlightClosestDots(dots, nodes);
      highlightClosestDots2(dots2, nodes2);
    } else if (currentStep === 1) {
      highlightDots = true;
      highlightDots2Active = false;
      animatePosition = false;
      duplicateCircle = false;
      secondCircleInitialized = false;

      // Highlight only in the first circle
      highlightClosestDots(dots, nodes);
    } else if (currentStep === 2) {
      highlightDots = true;
      highlightDots2Active = false;
      animatePosition = true;
      duplicateCircle = true;

      highlightClosestDots(dots, nodes);
      highlightClosestDots2(dots2, nodes2);

      if (renderer2 && scene2 && camera2) {
        requestAnimationFrame(() => renderer2.render(scene2, camera2));
      }
    } else if (currentStep === 3) {
      highlightDots = true;
      highlightDots2Active = true;
      animatePosition = true;
      duplicateCircle = true;

      highlightClosestDots(dots, nodes);
      highlightClosestDots2(dots2, nodes2);

      if (renderer2 && scene2 && camera2) {
        requestAnimationFrame(() => renderer2.render(scene2, camera2));
      }
    }
  }
</script>

<main>
  <section>
    <div class="sticky">
      <div class="parent-container">
        <div bind:this={container} class="responsive-container" class:animate={animatePosition}></div>
        {#if duplicateCircle}
          <div bind:this={container2} class="responsive-container right"></div>
        {/if}
      </div>
    </div>
    <Steps bind:currentStep />
  </section>
</main>

<style>
  .responsive-container {
    width: 550px;
    height: 550px;
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 0 10px;
    transition: transform 0.5s ease;
    overflow: hidden;
  }

  .animate {
    transform: translateX(-5%);
  }

  .parent-container {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    max-width: 1200px;
    margin: auto;
  }

  .sticky {
    position: sticky;
    z-index: 1;
    height: 90vh;
    top: 5vh;
    margin-bottom: 1rem;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  section {
    position: relative;
  }

  main {
    max-width: 1200px;
    margin: 0 auto;
  }
</style>
