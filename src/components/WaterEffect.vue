// WaterEffect.vue
<script setup lang="ts">
import { onMounted, onBeforeUnmount, ref } from "vue";
import * as THREE from "three";

const props = defineProps<{
  imageUrl: string;
}>();

const containerRef = ref<HTMLDivElement>();

const vertexShader = `
  varying vec2 vUv;
  
  void main() {
    vUv = uv;
    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
  }
`;

const fragmentShader = `
  uniform sampler2D uTexture;
  uniform vec2 uMouse;
  uniform float uMouseEffect;
  varying vec2 vUv;

  void main() {
    vec2 uv = vUv;
    
    // Calculate distance from current pixel to mouse position
    float distanceFromMouse = length(uv - uMouse);
    
    // Create ripple only when mouse moves (controlled by uMouseEffect)
    float ripple = sin(distanceFromMouse * 40.0) * 0.2 * uMouseEffect;
     ripple *= smoothstep(0.16, 0.0, distanceFromMouse); // Tighter fade out effect
    
    // Apply distortion only around mouse position
    vec2 distortedUV = uv + vec2(ripple);
    
    // Sample texture with distorted coordinates
    vec4 texture = texture2D(uTexture, distortedUV);
    
    gl_FragColor = texture;
  }
`;

let scene: THREE.Scene;
let camera: THREE.OrthographicCamera;
let renderer: THREE.WebGLRenderer;
let geometry: THREE.PlaneGeometry;
let material: THREE.ShaderMaterial;
let mesh: THREE.Mesh;
let mousePosition = new THREE.Vector2(0.5, 0.5);
let mouseEffect = 0;
let lastMouseX = 0;
let lastMouseY = 0;
let animationFrameId: number;

const init = async () => {
  if (!containerRef.value) return;

  // Setup scene
  scene = new THREE.Scene();

  // Setup camera
  const aspect = window.innerWidth / window.innerHeight;
  camera = new THREE.OrthographicCamera(-aspect, aspect, 1, -1, 0.1, 100);
  camera.position.z = 1;

  // Setup renderer
  renderer = new THREE.WebGLRenderer({ alpha: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  containerRef.value.appendChild(renderer.domElement);

  // Load and setup texture
  const textureLoader = new THREE.TextureLoader();
  const texture = await textureLoader.loadAsync(props.imageUrl);
  texture.minFilter = THREE.LinearFilter;
  texture.magFilter = THREE.LinearFilter;

  // Calculate texture aspect ratio and coverage
  const imageAspect = texture.image.width / texture.image.height;
  const screenAspect = window.innerWidth / window.innerHeight;

  // Adjust UV scale to maintain aspect ratio and cover
  let scaleX = 1;
  let scaleY = 1;

  if (screenAspect > imageAspect) {
    scaleY = imageAspect / screenAspect;
  } else {
    scaleX = screenAspect / imageAspect;
  }

  // Create material with custom shaders
  material = new THREE.ShaderMaterial({
    uniforms: {
      uTexture: { value: texture },
      uMouse: { value: mousePosition },
      uMouseEffect: { value: mouseEffect },
    },
    vertexShader,
    fragmentShader,
  });

  // Create mesh with adjusted geometry for cover effect
  geometry = new THREE.PlaneGeometry(2 * aspect, 2);
  const uvs = geometry.attributes.uv;
  for (let i = 0; i < uvs.count; i++) {
    const u = uvs.getX(i);
    const v = uvs.getY(i);
    uvs.setXY(
      i,
      (u - 0.5) * scaleX + 0.5, // Center horizontally
      (v - 0.0) * scaleY + 0.0 // Align to bottom
    );
  }

  mesh = new THREE.Mesh(geometry, material);
  scene.add(mesh);

  // Start animation
  animate();
};

const animate = () => {
  if (!material) return;

  // Decay mouse effect over time
  mouseEffect *= 0.95;
  material.uniforms.uMouseEffect.value = mouseEffect;

  renderer.render(scene, camera);
  animationFrameId = requestAnimationFrame(animate);
};

const handleMouseMove = (event: MouseEvent) => {
  if (!containerRef.value) return;

  const rect = containerRef.value.getBoundingClientRect();
  mousePosition.x = (event.clientX - rect.left) / rect.width;
  mousePosition.y = 1 - (event.clientY - rect.top) / rect.height;

  // Calculate mouse movement speed for effect intensity
  const speed = Math.sqrt(
    Math.pow(event.clientX - lastMouseX, 2) +
      Math.pow(event.clientY - lastMouseY, 2)
  );

  mouseEffect = Math.min(speed * 0.01, 1);

  lastMouseX = event.clientX;
  lastMouseY = event.clientY;

  if (material) {
    material.uniforms.uMouse.value = mousePosition;
  }
};

const handleResize = () => {
  if (!containerRef.value || !camera || !renderer) return;

  const aspect = window.innerWidth / window.innerHeight;

  // Update camera
  camera.left = -aspect;
  camera.right = aspect;
  camera.updateProjectionMatrix();

  // Update mesh geometry
  if (geometry) {
    geometry.dispose();
    geometry = new THREE.PlaneGeometry(2 * aspect, 2);
    mesh.geometry = geometry;
  }

  // Update renderer
  renderer.setSize(window.innerWidth, window.innerHeight);
};

// Lifecycle hooks
onMounted(() => {
  init();
  window.addEventListener("mousemove", handleMouseMove);
  window.addEventListener("resize", handleResize);
});

onBeforeUnmount(() => {
  window.removeEventListener("mousemove", handleMouseMove);
  window.removeEventListener("resize", handleResize);

  // Cancel animation frame
  if (animationFrameId) {
    cancelAnimationFrame(animationFrameId);
  }

  // Cleanup resources
  geometry?.dispose();
  material?.dispose();
  renderer?.dispose();
});
</script>

<template>
  <div class="background-container">
    <img :src="imageUrl" class="background-image" />
    <div ref="containerRef" class="water-effect"></div>
  </div>
</template>

<style scoped>
.background-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
  overflow: hidden;
}

.background-image {
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: bottom center;
}

.water-effect {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}
</style>
