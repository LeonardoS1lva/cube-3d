<!-- Nesse arquivo contém os arqiovos necessários para construir o desenho 3D de um cubo de seis lados -->
<template>
  <div ref="sceneContainer" style="width: 100%; height: 100%"></div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

export default {
  name: "Model",
  mounted() {
    this.initThreeJS();
  },
  methods: {
    initThreeJS() {
      // Configurações básicas
      const scene = new THREE.Scene(); // Cria uma cena
      const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      ); // Cria uma câmera
      const renderer = new THREE.WebGLRenderer(); // Cria um renderizador
      const container = this.$refs.sceneContainer;
      renderer.setSize(container.clientWidth, container.clientHeight); // Define o tamanho do renderizador
      renderer.setClearColor("#f0f0f0"); // Define a cor de fundo

      this.$refs.sceneContainer.appendChild(renderer.domElement); // Adiciona o renderizador ao container

      // Criar um cubo
      const geometry = new THREE.BoxGeometry(); // Cria uma geometria
      const material = new THREE.MeshBasicMaterial({
        color: "#00ff00",
      }); // Cria um material
      const cube = new THREE.Mesh(geometry, material); // Cria um cubo
      scene.add(cube); // Adiciona o cubo à cena

      // Criar a borda do cubo
      const edgesGeometry = new THREE.EdgesGeometry(geometry); // Cria uma geometria de borda
      const edgesMaterial = new THREE.LineBasicMaterial({ color: "#000000" }); // Cria um material de linha
      const edges = new THREE.LineSegments(edgesGeometry, edgesMaterial); // Cria as bordas
      scene.add(edges); // Adiciona as bordas à cena

      camera.position.z = 5; // Define a posição da câmera

      // Configurar controles de órbita
      const controls = new OrbitControls(camera, renderer.domElement);
      controls.enableDamping = true; // Habilitar amortecimento (inércia)
      controls.dampingFactor = 0.25; // Fator de amortecimento

      // Função de animação
      const animate = function () {
        requestAnimationFrame(animate); // Cria um loop de animação

        cube.rotation.x += 0.01; // Rotaciona o cubo no eixo x
        cube.rotation.y += 0.01; // Rotaciona o cubo no eixo y
        edges.rotation.x = cube.rotation.x; // Mantém as bordas sincronizadas com o cubo
        edges.rotation.y = cube.rotation.y; // Mantém as bordas sincronizadas com o cubo

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
