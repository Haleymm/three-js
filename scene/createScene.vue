<template>
  <div ref="demo1"></div>
</template>

<script>
// 创建场景 demo
import * as THREE from "three";
import dat from "dat.gui";
export default {
  data: () => ({
    controls: {
      scene: null,
      camera: null,
      renderer: null,
      rotationSpeed: 0.02
    }
  }),
  created() {
    this.$nextTick(() => {
      this.init();
    });
  },
  methods: {
    init() {
      let { initMesh, controls } = this;
      const gui = new dat.GUI(); // gui监测器
      gui.add(controls, "rotationSpeed", 0, 0.5);
      initMesh();
    },
    initMesh() {
      // 创建一个场景对象
      this.scene = new THREE.Scene();
      // 设置场景颜色
      this.scene.background = new THREE.Color( 0xcccccc );
      // 创建一个相机对象， (视野角度1，长宽比2，近截面3，远截面4)，1越大，物体越远离屏幕；
      this.camera = new THREE.PerspectiveCamera(
        45,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      ); 
      // 相机位置
      this.camera.position.x = -20;
      this.camera.position.y = 40;
      this.camera.position.z = 30;
      this.camera.lookAt(this.scene.position);

      this.renderer = new THREE.WebGLRenderer({ antialias: true}); // 渲染器
      this.renderer.setSize(window.innerWidth - 600, window.innerHeight - 100);
      this.renderer.shadowMapEnabled = true; // 开启阴影

      let axes = new THREE.AxisHelper(20); // 坐标轴

      let cubeGeometry = new THREE.CubeGeometry(10, 10, 10);
      let cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xff0066 });
      this.cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
      this.cube.position.x = -4;
      this.cube.position.y = 3;
      this.cube.position.z = 0;
      this.cube.castShadow = true;

      let spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(-40, 60, -10);
      spotLight.castShadow = true;

      this.scene.add(axes); // 场景添加坐标轴
      this.scene.add(this.cube);
      this.scene.add(spotLight);

      this.$refs.demo1.append(this.renderer.domElement);
      this.renderScene();
    },
    renderScene() {
      let { controls, cube, scene, camera, renderer } = this;
      cube.rotation.x += controls.rotationSpeed;
      cube.rotation.y += controls.rotationSpeed;
      cube.rotation.z += controls.rotationSpeed;
      requestAnimationFrame(this.renderScene);
      renderer.render(scene, camera);
    }
  }
};
</script>