[banner_3d_header.html](https://github.com/user-attachments/files/28678621/banner_3d_header.html)

<canvas id="c1" style="width:100%;height:220px;display:block;border-radius:10px"></canvas>
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
(function(){
const canvas = document.getElementById('c1');
const W = canvas.offsetWidth || 680, H = 220;
canvas.width = W * devicePixelRatio; canvas.height = H * devicePixelRatio;
canvas.style.height = H + 'px';

const renderer = new THREE.WebGLRenderer({canvas, antialias:true, alpha:false});
renderer.setPixelRatio(devicePixelRatio);
renderer.setSize(W, H);
renderer.setClearColor(0x04020e);

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(60, W/H, 0.1, 100);
camera.position.set(0, 0, 5);

const ptLight1 = new THREE.PointLight(0x8b5cf6, 3, 12);
ptLight1.position.set(-3, 2, 3);
scene.add(ptLight1);
const ptLight2 = new THREE.PointLight(0x14b8a6, 2, 10);
ptLight2.position.set(4, -1, 2);
scene.add(ptLight2);
const ptLight3 = new THREE.PointLight(0xf59e0b, 1.5, 8);
ptLight3.position.set(0, 3, 1);
scene.add(ptLight3);
scene.add(new THREE.AmbientLight(0x0a0520, 1));

const sphereGeo = new THREE.SphereGeometry(0.08, 8, 8);
const nodes = [];
const nodePositions = [];
for(let i=0; i<40; i++){
  const x = (Math.random()-0.5)*10;
  const y = (Math.random()-0.5)*5;
  const z = (Math.random()-0.5)*4 - 1;
  nodePositions.push(new THREE.Vector3(x, y, z));
  const mat = new THREE.MeshStandardMaterial({
    color: [0x8b5cf6, 0x14b8a6, 0xf59e0b][Math.floor(Math.random()*3)],
    metalness: 0.3, roughness: 0.4,
    emissive: [0x4c1d95, 0x0f6e56, 0x854f0b][Math.floor(Math.random()*3)],
    emissiveIntensity: 0.5
  });
  const mesh = new THREE.Mesh(sphereGeo, mat);
  mesh.position.set(x, y, z);
  scene.add(mesh);
  nodes.push(mesh);
}

const lineMat = new THREE.LineBasicMaterial({color:0x8b5cf6, transparent:true, opacity:0.15});
for(let i=0; i<nodePositions.length; i++){
  for(let j=i+1; j<nodePositions.length; j++){
    if(nodePositions[i].distanceTo(nodePositions[j]) < 3.5 && Math.random() > 0.65){
      const geo = new THREE.BufferGeometry().setFromPoints([nodePositions[i], nodePositions[j]]);
      scene.add(new THREE.Line(geo, lineMat));
    }
  }
}

const ringGeo = new THREE.TorusGeometry(1.8, 0.008, 8, 80);
const ringMat = new THREE.MeshStandardMaterial({color:0x8b5cf6, emissive:0x4c1d95, emissiveIntensity:0.8, metalness:0.9, roughness:0.1});
const ring1 = new THREE.Mesh(ringGeo, ringMat);
ring1.rotation.x = Math.PI * 0.3;
ring1.position.set(2.5, 0, -1);
scene.add(ring1);

const ring2Mat = new THREE.MeshStandardMaterial({color:0x14b8a6, emissive:0x0f6e56, emissiveIntensity:0.7, metalness:0.9, roughness:0.1});
const ring2 = new THREE.Mesh(new THREE.TorusGeometry(1.2, 0.006, 8, 60), ring2Mat);
ring2.rotation.x = Math.PI * 0.5;
ring2.rotation.z = Math.PI * 0.2;
ring2.position.set(2.5, 0, -1);
scene.add(ring2);

const coreMat = new THREE.MeshStandardMaterial({color:0x6d28d9, emissive:0x4c1d95, emissiveIntensity:1, metalness:0.5, roughness:0.2});
const core = new THREE.Mesh(new THREE.SphereGeometry(0.25, 24, 24), coreMat);
core.position.set(2.5, 0, -1);
scene.add(core);

const dotGeo = new THREE.BufferGeometry();
const positions = new Float32Array(500*3);
for(let i=0;i<500;i++){
  positions[i*3]   = (Math.random()-0.5)*14;
  positions[i*3+1] = (Math.random()-0.5)*7;
  positions[i*3+2] = (Math.random()-0.5)*6 - 3;
}
dotGeo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
const dotMat = new THREE.PointsMaterial({color:0xc4b5fd, size:0.015, transparent:true, opacity:0.6});
scene.add(new THREE.Points(dotGeo, dotMat));

let t = 0;
function animate(){
  requestAnimationFrame(animate);
  t += 0.008;
  ring1.rotation.y = t * 0.6;
  ring2.rotation.y = -t * 0.4;
  ring2.rotation.z = t * 0.3;
  core.rotation.y = t;
  ptLight1.position.x = Math.sin(t*0.5)*4;
  ptLight1.position.y = Math.cos(t*0.3)*2;
  ptLight2.position.x = Math.cos(t*0.4)*4;
  ptLight2.position.y = Math.sin(t*0.6)*2;
  nodes.forEach((n,i)=>{
    n.position.y += Math.sin(t + i*0.3)*0.001;
    n.position.x += Math.cos(t*0.5 + i*0.2)*0.0005;
  });
  renderer.render(scene, camera);
}
animate();

const overlay = document.createElementNS('http://www.w3.org/2000/svg','svg');
overlay.setAttribute('viewBox','0 0 680 220');
overlay.setAttribute('width','100%');
overlay.setAttribute('height','220');
overlay.style.cssText='position:absolute;top:0;left:0;pointer-events:none';
canvas.parentElement.style.position='relative';
overlay.innerHTML=`
<defs>
  <style>@import url('https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@1&family=DM+Mono:wght@400;500&display=swap');</style>
</defs>
<rect x="0" y="0" width="5" height="220" fill="#8b5cf6" opacity="0.8"/>
<text font-family="'DM Serif Display',Georgia,serif" font-style="italic" font-size="46" fill="#f5f3ff" x="30" y="125" opacity="0.95">Sarina Kasaiyan</text>
<text font-family="'DM Mono',monospace" font-size="11" fill="#a78bfa" letter-spacing="5" x="32" y="152">NEURO-AI  ·  BCI  ·  DEEP LEARNING</text>
<line x1="32" y1="164" x2="280" y2="164" stroke="#8b5cf6" stroke-width="0.8" opacity="0.5"/>
<text font-family="'DM Mono',monospace" font-size="10" fill="#5eead4" letter-spacing="3" x="32" y="182">M.Sc. AI &amp; Robotics  ·  Aspiring Ph.D.</text>
`;
canvas.parentElement.appendChild(overlay);
})();
</script>
