
<canvas id="c3" style="width:100%;height:200px;display:block;border-radius:10px"></canvas>
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
(function(){
const canvas=document.getElementById('c3');
const W=canvas.offsetWidth||680,H=200;
canvas.width=W*devicePixelRatio;canvas.height=H*devicePixelRatio;
canvas.style.height=H+'px';
const renderer=new THREE.WebGLRenderer({canvas,antialias:true});
renderer.setPixelRatio(devicePixelRatio);
renderer.setSize(W,H);
renderer.setClearColor(0x020c08);

const scene=new THREE.Scene();
const camera=new THREE.PerspectiveCamera(55,W/H,0.1,100);
camera.position.set(0,0,6);

scene.add(new THREE.AmbientLight(0x0a1a10,2));
const pl1=new THREE.PointLight(0x14b8a6,5,14);
pl1.position.set(-1,2,4);
scene.add(pl1);
const pl2=new THREE.PointLight(0x8b5cf6,3,12);
pl2.position.set(4,1,3);
scene.add(pl2);
const pl3=new THREE.PointLight(0x1d9e75,2,8);
pl3.position.set(0,-2,2);
scene.add(pl3);

const layers=[[-4.5,-2,1.5],[0,-1,0],[4.5,-2,-1.5]];
const layerNodes=[];
layers.forEach((lp,li)=>{
  const cnt=[4,5,4][li];
  const lnodes=[];
  for(let i=0;i<cnt;i++){
    const y=(i-(cnt-1)/2)*1.1;
    const geo=new THREE.SphereGeometry(0.12,16,16);
    const col=[0x14b8a6,0x8b5cf6,0x1d9e75][li];
    const ecol=[0x0f6e56,0x4c1d95,0x0e5c3a][li];
    const mat=new THREE.MeshStandardMaterial({color:col,emissive:ecol,emissiveIntensity:0.8,metalness:0.4,roughness:0.3});
    const mesh=new THREE.Mesh(geo,mat);
    mesh.position.set(lp[0],y,lp[2]);
    scene.add(mesh);
    lnodes.push(mesh);
  }
  layerNodes.push(lnodes);
});

const edgeMat=new THREE.LineBasicMaterial({color:0x14b8a6,transparent:true,opacity:0.12});
for(let l=0;l<layerNodes.length-1;l++){
  layerNodes[l].forEach(a=>{
    layerNodes[l+1].forEach(b=>{
      const geo=new THREE.BufferGeometry().setFromPoints([a.position.clone(),b.position.clone()]);
      scene.add(new THREE.Line(geo,edgeMat));
    });
  });
}

const pulseMat=new THREE.MeshStandardMaterial({color:0xf59e0b,emissive:0x854f0b,emissiveIntensity:1.5,metalness:0.2,roughness:0.1,transparent:true,opacity:0.9});
const pulseGeo=new THREE.SphereGeometry(0.06,12,12);

const particles=[];
for(let i=0;i<6;i++){
  const m=new THREE.Mesh(pulseGeo,pulseMat.clone());
  scene.add(m);
  const li=Math.floor(Math.random()*2);
  const si=Math.floor(Math.random()*layerNodes[li].length);
  const ei=Math.floor(Math.random()*layerNodes[li+1].length);
  particles.push({mesh:m,start:layerNodes[li][si].position.clone(),end:layerNodes[li+1][ei].position.clone(),t:Math.random(),speed:0.004+Math.random()*0.006});
}

const gridGeo=new THREE.PlaneGeometry(20,10,30,15);
scene.add(new THREE.Mesh(gridGeo,new THREE.MeshStandardMaterial({color:0x041a0c,wireframe:true,transparent:true,opacity:0.2,emissive:0x0f6e56,emissiveIntensity:0.3})));
scene.getObjectByProperty('type','Mesh').rotation={x:-0.4,y:0,z:0};

let t=0;
function animate(){
  requestAnimationFrame(animate);
  t+=0.01;
  particles.forEach(p=>{
    p.t+=p.speed;
    if(p.t>1)p.t=0;
    p.mesh.position.lerpVectors(p.start,p.end,p.t);
    p.mesh.material.opacity=Math.sin(p.t*Math.PI)*0.9+0.1;
  });
  layerNodes.forEach((layer,li)=>{
    layer.forEach((n,ni)=>{
      n.position.y+=Math.sin(t+li*1.2+ni*0.7)*0.001;
      n.scale.setScalar(1+Math.sin(t*2+li+ni)*0.05);
    });
  });
  pl1.position.x=Math.sin(t*0.5)*3-1;
  pl2.position.y=Math.cos(t*0.4)*2;
  renderer.render(scene,camera);
}
animate();

const o=document.createElementNS('http://www.w3.org/2000/svg','svg');
o.setAttribute('viewBox','0 0 680 200');
o.setAttribute('width','100%');
o.setAttribute('height','200');
o.style.cssText='position:absolute;top:0;left:0;pointer-events:none';
canvas.parentElement.style.position='relative';
o.innerHTML=`
<rect x="0" y="0" width="5" height="200" fill="#14b8a6" opacity="0.8"/>
<text font-family="monospace" font-size="10" fill="#5eead4" letter-spacing="6" x="20" y="22">SECTION 03</text>
<text font-family="monospace" font-size="20" font-weight="600" fill="#f5f3ff" x="20" y="50">Selected Research Projects</text>
<text font-family="monospace" font-size="10" fill="#9ca3af" x="20" y="68">BCI &#183; Medical Imaging &#183; Autonomous Agents &#183; NMT</text>
<rect x="20" y="85" width="118" height="20" rx="3" fill="#0f4a3a" opacity="0.8"/>
<text font-family="monospace" font-size="9" fill="#5eead4" x="28" y="99">Schizophrenia 97%</text>
<rect x="148" y="85" width="108" height="20" rx="3" fill="#1a0a40" opacity="0.8"/>
<text font-family="monospace" font-size="9" fill="#a78bfa" x="156" y="99">Brain Tumor Seg</text>
<rect x="266" y="85" width="90" height="20" rx="3" fill="#0a2e18" opacity="0.8"/>
<text font-family="monospace" font-size="9" fill="#4ade80" x="274" y="99">RL Agents DQN</text>
<text font-family="monospace" font-size="10" fill="#6b7280" x="20" y="185">PyTorch &#183; TensorFlow &#183; MNE-Python &#183; OpenAI Gym</text>
`;
canvas.parentElement.appendChild(o);
})();
</script>
