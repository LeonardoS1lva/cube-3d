<template>
  <div ref="sceneContainer" style="width: 100%; height: 100%"></div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"; // Importa os controles de órbita
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader"; // Importa o carregador de modelos GLTF
import { EXRLoader } from "three/examples/jsm/loaders/EXRLoader.js"; // Importa o carregador de texturas EXR
import treePath from "../assets/models/beech_tree.glb"; // Importa o caminho do modelo 3D
import studioEXRPath from "../assets/textures/photo_studio_01_2k.exr"; // Importa o caminho da textura HDR

export default {
  name: "ModelExample",
  mounted() {
    this.initThreeJS();
  },
  methods: {
    initThreeJS() {
      // Configurações básicas
      const scene = new THREE.Scene(); // Cria uma cena
      scene.background = new THREE.Color(0xf0f0f0); // Define a cor de fundo
      const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      ); // Cria uma câmera
      const renderer = new THREE.WebGLRenderer(); // Cria um renderizador
      const container = this.$refs.sceneContainer;
      renderer.setSize(container.clientWidth, container.clientHeight); // Define o tamanho do renderizador
      renderer.setPixelRatio(window.devicePixelRatio); // Define a proporção de pixels
      // renderer.setClearColor("#f0f0f0"); // Define a cor de fundo
      renderer.outputEncoding = THREE.sRGBEncoding; // Define o espaço de cores
      renderer.toneMapping = THREE.ACESFilmicToneMapping; // Define o mapeamento de tons

      this.$refs.sceneContainer.appendChild(renderer.domElement); // Adiciona o renderizador ao container

      renderer.shadowMap.enabled = true; // Habilita o mapeamento de sombras
      renderer.shadowMap.type = THREE.PCFSoftShadowMap; // Define o tipo de mapeamento de sombras
      renderer.autoClear = false; // Desabilita a limpeza automática do buffer

      // camera.position.z = 5; // Define a posição da câmera

      // Configurar controles de órbita
      const controls = new OrbitControls(camera, renderer.domElement);
      controls.enableDamping = true; // Habilitar amortecimento (inércia)
      controls.dampingFactor = 0.25; // Fator de amortecimento
      controls.minDistance = 1; // Distância mínima
      controls.maxDistance = 10; // Distância máxima

      // console.log(scene);
      // console.log(controls);

      // Adicionar luz ambiente
      // const ambientLight = new THREE.AmbientLight(0x404040);
      // scene.add(ambientLight);

      // Adicionar luz direcional
      const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5);
      directionalLight.position.set(0, 1, 0);
      directionalLight.castShadow = true; // Habilita a projeção de sombras
      directionalLight.shadow.bias = -0.001; // Bias de sombras
      directionalLight.shadow.mapSize.width = 1024; // Largura do mapa de sombras
      directionalLight.shadow.mapSize.height = 1024; // Altura do mapa de sombras
      scene.add(directionalLight);

      // Carregar o modelo 3D
      const loader = new GLTFLoader();
      loader.load(
        treePath,
        (glb) => {
          scene.add(glb.scene);

          // Ajustar a posição do modelo no centro da cena
          var box = new THREE.Box3().setFromObject(glb.scene); // Obter o tamanho do modelo
          var center = box.getCenter(new THREE.Vector3()); // Obter o centro do modelo
          glb.scene.position.sub(center); // Reposiciona o modelo

          // Ajustar a posição da câmera
          camera.position.z = box.max.z * 2;

          // Adicionar sombras
          glb.scene.traverse((child) => {
            if (child.isMesh) {
              child.castShadow = true;
              child.receiveShadow = true;
            }
          });

          const geometry = new THREE.PlaneGeometry(100, 100);
          const material = new THREE.ShadowMaterial({ opacity: 0.3 });
          const plane = new THREE.Mesh(geometry, material);
          plane.rotation.x = -Math.PI / 2;
          plane.position.y = -box.max.y / 2;
          plane.receiveShadow = true;
          scene.add(plane);

        },
        undefined,
        (error) => {
          console.error("Erro ao carregar o modelo GLTF:", error);
        }
      );

      // Carregar textura EXR
      new EXRLoader().load(studioEXRPath, (texture) => {
        texture.mapping = THREE.EquirectangularReflectionMapping; // Mapeamento da textura~
        // scene.background = texture; // Define a textura como fundo da cena
        scene.environment = texture; // Define a textura como ambiente da cena
      });

      // Função de animação
      const animate = function () {
        requestAnimationFrame(animate); // Cria um loop de animação

        controls.update(); // Atualiza os controles de órbita

        renderer.render(scene, camera); // Renderiza a cena
      };

      // Função para ajustar o tamanho do renderizador ao redimensionar a janela
      window.addEventListener("resize", () => {
        const width = container.clientWidth;
        const height = container.clientHeight;
        renderer.setSize(width, height);
        camera.aspect = width / height; // Atualiza o aspecto da câmera
        camera.updateProjectionMatrix(); // Atualiza a matriz de projeção
      });

      animate(); // Chama a função de animação
    },
  },
};
</script>
