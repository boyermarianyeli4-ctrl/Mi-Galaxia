# Mi-Galaxia
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Galaxia para ti</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background-color: #000005;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }
    #info {
      position: absolute;
      top: 12%;
      width: 100%;
      text-align: center;
      color: #ffffff;
      z-index: 10;
      pointer-events: none;
      text-shadow: 0 0 10px rgba(255, 255, 255, 0.8), 0 0 20px rgba(230, 100, 250, 0.7);
    }
    h1 {
      font-size: 2.2rem;
      margin-bottom: 8px;
      letter-spacing: 2px;
    }
    p {
      font-size: 1.1rem;
      opacity: 0.85;
    }
  </style>
</head>
<body>
  <!-- ==================================================== -->
  <!-- 1. AQUÍ PUEDES CAMBIAR EL TEXTO VISIBLE EN PANTALLA -->
  <!-- ==================================================== -->
  <div id="info">
    <h1>✨ Eres mi galaxia ✨</h1>
    <p>Arrastra la pantalla para explorar en 360°</p>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>

  <script>
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(window.devicePixelRatio);
    document.body.appendChild(renderer.domElement);

    const controls = new THREE.OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;
    controls.dampingFactor = 0.05;

    // ====================================================
    // 2. AQUÍ PUEDES PERSONALIZAR LOS COLORES DE LA GALAXIA
    // ====================================================
    const parameters = {
      count: 25000,
      size: 0.012,
      radius: 5,
      branches: 4,
      spin: 1,
      randomness: 0.5,
      power: 3,
      // Cambia los códigos hexadecimales de color a tu gusto:
      insideColor: '#ff007f', // Color del centro
      outsideColor: '#00d2ff'  # Color de los bordes (Ejemplo: Azul neón)
    };

    const geometry = new THREE.BufferGeometry();
    const positions = new Float32Array(parameters.count * 3);
    const colors = new Float32Array(parameters.count * 3);

    const colorInside = new THREE.Color(parameters.insideColor);
    const colorOutside = new THREE.Color(parameters.outsideColor);

    for (let i = 0; i < parameters.count; i++) {
      const i3 = i * 3;
      const radius = Math.random() * parameters.radius;
      const spinAngle = radius * parameters.spin;
      const branchAngle = ((i % parameters.branches) / parameters.branches) * Math.PI * 2;

      const randomX = Math.pow(Math.random(), parameters.power) * (Math.random() < 0.5 ? 1 : -1) * parameters.randomness * radius;
      const randomY = Math.pow(Math.random(), parameters.power) * (Math.random() < 0.5 ? 1 : -1) * parameters.randomness * radius;
      const randomZ = Math.pow(Math.random(), parameters.power) * (Math.random() < 0.5 ? 1 : -1) * parameters.randomness * radius;

      positions[i3] = Math.cos(branchAngle + spinAngle) * radius + randomX;
      positions[i3 + 1] = randomY;
      positions[i3 + 2] = Math.sin(branchAngle + spinAngle) * radius + randomZ;

      const mixedColor = colorInside.clone();
      mixedColor.lerp(colorOutside, radius / parameters.radius);

      colors[i3] = mixedColor.r;
      colors[i3 + 1] = mixedColor.g;
      colors[i3 + 2] = mixedColor.b;
    }

    geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
    geometry.setAttribute('color', new THREE.BufferAttribute(colors, 3));

    const material = new THREE.PointsMaterial({
      size: parameters.size,
      sizeAttenuation: true,
      depthWrite: false,
      blending: THREE.AdditiveBlending,
      vertexColors: true
    });

    const points = new THREE.Points(geometry, material);
    scene.add(points);

    camera.position.x = 3;
    camera.position.y = 3;
    camera.position.z = 5;

    function animate() {
      requestAnimationFrame(animate);
      points.rotation.y += 0.0012;
      controls.update();
      renderer.render(scene, camera);
    }
    animate();

    window.addEventListener('resize', () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });
  </script>
</body>
</html>
