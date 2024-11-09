
# Step-by-Step Implementation Guide

### 1. **Set Up Dependencies**

To work with `Three.js`, `GLTFLoader`, and `gsap`, we’ll use external URLs for importing modules.

- Include the following libraries by linking them directly in your code:
  ```javascript
  import * as THREE from 'https://cdn.skypack.dev/three@0.129.0/build/three.module.js';
  import { GLTFLoader } from 'https://cdn.skypack.dev/three@0.129.0/examples/jsm/loaders/GLTFLoader.js';
  import { gsap } from 'https://cdn.skypack.dev/gsap';
  ```

### 2. **Initialize the Camera**

Define a camera with a perspective view to provide depth and positioning.

- Set up the camera with these parameters:
  ```javascript
  const camera = new THREE.PerspectiveCamera(
      10,                             // Field of view
      window.innerWidth / window.innerHeight, // Aspect ratio
      0.1,                            // Near clipping plane
      1000                            // Far clipping plane
  );
  camera.position.z = 13; // Set camera position
  ```

### 3. **Create the Scene**

The `scene` will contain our 3D objects, lights, and the camera.

- Initialize the scene:
  ```javascript
  const scene = new THREE.Scene();
  ```

### 4. **Load the 3D Model**

Use `GLTFLoader` to load the 3D model (`demon_bee_full_texture.glb`). Configure the model to animate and move based on scroll events.

- Load the model and set up animations:
  ```javascript
  let bee; // Holds the 3D model
  let mixer; // Controls animations

  const loader = new GLTFLoader();
  loader.load('/demon_bee_full_texture.glb', (gltf) => {
      bee = gltf.scene;
      scene.add(bee);

      mixer = new THREE.AnimationMixer(bee);
      mixer.clipAction(gltf.animations[0]).play();

      modelMove(); // Initialize positioning based on sections
  });
  ```

### 5. **Set Up the Renderer**

Create the WebGL renderer and configure it for transparency.

- Add the renderer to your HTML element:
  ```javascript
  const renderer = new THREE.WebGLRenderer({ alpha: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.getElementById('container3D').appendChild(renderer.domElement);
  ```

### 6. **Add Lighting**

Set up an `AmbientLight` and a `DirectionalLight` for better visual effects on the model.

- Add lights to the scene:
  ```javascript
  const ambientLight = new THREE.AmbientLight(0xffffff, 1.3); // Soft light
  scene.add(ambientLight);

  const topLight = new THREE.DirectionalLight(0xffffff, 1); // Directional light
  topLight.position.set(500, 500, 500);
  scene.add(topLight);
  ```

### 7. **Render the Scene**

Define a recursive function, `reRender3D`, to animate and render the scene continuously.

- Implement rendering:
  ```javascript
  const reRender3D = () => {
      requestAnimationFrame(reRender3D);
      renderer.render(scene, camera);

      if (mixer) mixer.update(0.02); // Update animations
  };
  reRender3D(); // Start rendering
  ```

### 8. **Define Model Positions**

Set positions and rotations for the model in relation to specific sections.

- Configure an array of positions:
  ```javascript
  let arrPositionModel = [
      { id: 'banner', position: { x: 0, y: -1, z: 0 }, rotation: { x: 0, y: 1.5, z: 0 } },
      { id: "intro", position: { x: 1, y: -1, z: -5 }, rotation: { x: 0.5, y: -0.5, z: 0 } },
      { id: "description", position: { x: -1, y: -1, z: -5 }, rotation: { x: 0, y: 0.5, z: 0 } },
      { id: "contact", position: { x: 0.8, y: -1, z: 0 }, rotation: { x: 0.3, y: -0.5, z: 0 } },
  ];
  ```

### 9. **Animate Model Based on Scroll**

The `modelMove` function will detect which section is in view and animate the model’s position accordingly.

- Implement section-based model movement:
  ```javascript
  const modelMove = () => {
      const sections = document.querySelectorAll('.section');
      let currentSection;

      sections.forEach((section) => {
          const rect = section.getBoundingClientRect();
          if (rect.top <= window.innerHeight / 3) {
              currentSection = section.id;
          }
      });

      let position_active = arrPositionModel.findIndex((val) => val.id == currentSection);
      if (position_active >= 0) {
          let new_coordinates = arrPositionModel[position_active];
          gsap.to(bee.position, {
              x: new_coordinates.position.x,
              y: new_coordinates.position.y,
              z: new_coordinates.position.z,
              duration: 3,
              ease: "power1.out"
          });
          gsap.to(bee.rotation, {
              x: new_coordinates.rotation.x,
              y: new_coordinates.rotation.y,
              z: new_coordinates.rotation.z,
              duration: 3,
              ease: "power1.out"
          });
      }
  };
  ```

### 10. **Set Up Event Listeners**

Add event listeners to update the model based on scroll and window resize events.

- Scroll and resize event listeners:
  ```javascript
  window.addEventListener('scroll', () => {
      if (bee) {
          modelMove();
      }
  });

  window.addEventListener('resize', () => {
      renderer.setSize(window.innerWidth, window.innerHeight);
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
  });
  ```

---

This setup will load and animate a 3D model in response to user interactions with the scroll and window resize events, providing an interactive and visually engaging experience.
