<!DOCTYPE html>
<html lang="fa">
<head>
    <meta charset="UTF-8">
    <style>
        body { margin: 0; overflow: hidden; }
    </style>
</head>
<body>
    <script type="importmap">
        {
            "imports": {
                "three": "https://unpkg.com/three@0.128.0/build/three.module.js"
            }
        }
    </script>
    <script type="module">
        import * as THREE from 'three';

        const scene = new THREE.Scene();
        scene.background = new THREE.Color(0x1a0a1a);
        scene.fog = new THREE.FogExp2(0x1a0a1a, 0.006);

        const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.set(0, 1, 9);
        camera.lookAt(0, 0, 0);

        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // Floating books (cuboids with "pages")
        const books = [];
        const bookColors = [0xaa44ff, 0x44ccaa, 0xff8866, 0x66aaff];
        const bookPos = [
            [-2.2, 0.5, -1], [0, 0.8, -0.5], [2, 0.4, -1.2],
            [-1.5, -0.8, 0.5], [1.3, -0.6, 0.8], [0, -1, 0.2]
        ];
        bookPos.forEach((pos, idx) => {
            const boxGeo = new THREE.BoxGeometry(1.2, 0.3, 0.8);
            const boxMat = new THREE.MeshStandardMaterial({ color: bookColors[idx % bookColors.length], roughness: 0.4, metalness: 0.2 });
            const book = new THREE.Mesh(boxGeo, boxMat);
            book.position.set(pos[0], pos[1], pos[2]);
            scene.add(book);
            // add "pages" edge
            const edgeGeo = new THREE.BoxGeometry(1.22, 0.32, 0.05);
            const edgeMat = new THREE.MeshStandardMaterial({ color: 0xeedd99 });
            const pages = new THREE.Mesh(edgeGeo, edgeMat);
            pages.position.set(pos[0], pos[1], pos[2] + 0.4);
            scene.add(pages);
            books.push({ book, pages, offset: idx });
        });

        // floating citation particles (small glowing dots)
        const citationCount = 400;
        const partGeo = new THREE.BufferGeometry();
        const partPos = new Float32Array(citationCount * 3);
        for (let i = 0; i < citationCount; i++) {
            partPos[i*3] = (Math.random() - 0.5) * 12;
            partPos[i*3+1] = (Math.random() - 0.5) * 7;
            partPos[i*3+2] = (Math.random() - 0.5) * 10 - 3;
        }
        partGeo.setAttribute('position', new THREE.BufferAttribute(partPos, 3));
        const partMat = new THREE.PointsMaterial({ color: 0xffaa88, size: 0.05 });
        const particles = new THREE.Points(partGeo, partMat);
        scene.add(particles);

        // rotating ring with bibtex symbols
        const ringGeo = new THREE.TorusGeometry(2.8, 0.05, 64, 200);
        const ringMat = new THREE.MeshStandardMaterial({ color: 0xcc66ff });
        const ring = new THREE.Mesh(ringGeo, ringMat);
        ring.rotation.x = Math.PI / 2;
        ring.rotation.z = Math.PI / 4;
        scene.add(ring);

        const ambient = new THREE.AmbientLight(0x331133);
        scene.add(ambient);
        const light1 = new THREE.PointLight(0xaa66ff, 0.8);
        light1.position.set(2, 3, 4);
        scene.add(light1);
        const light2 = new THREE.PointLight(0xffaa66, 0.6);
        light2.position.set(-2, 1, 5);
        scene.add(light2);

        let time = 0;
        function animate() {
            requestAnimationFrame(animate);
            time += 0.01;
            books.forEach((item, i) => {
                item.book.rotation.y = Math.sin(time + i) * 0.5;
                item.pages.rotation.y = Math.sin(time + i) * 0.5;
                item.book.position.y += Math.sin(time * 1.5 + i) * 0.002;
                item.pages.position.y = item.book.position.y;
            });
            ring.rotation.z = time * 0.2;
            ring.rotation.x = Math.PI / 2 + Math.sin(time * 0.3) * 0.1;
            particles.rotation.y = time * 0.03;
            camera.position.z = 9 + Math.sin(time * 0.2) * 0.2;
            camera.lookAt(0, 0, 0);
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
