<template>
  <div ref="demo1"></div>
</template>

<script>
// 线
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
      //   const gui = new dat.GUI(); // gui监测器
      //   gui.add(controls, "rotationSpeed", 0, 0.5);
      initMesh();
    },
    initMesh() {
      // 创建一个场景对象
      this.scene = new THREE.Scene();
      // 设置场景颜色
      this.scene.background = new THREE.Color(0xcccccc);

      // 创建一个相机对象， (视野角度1，长宽比2，近截面3，远截面4)，1越大，物体越远离屏幕；
      this.camera = new THREE.PerspectiveCamera(
        45,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      // 相机位置
      this.camera.position.x = 20;
      this.camera.position.y = 30;
      this.camera.position.z = 50;
      this.camera.lookAt(this.scene.position);

      this.renderer = new THREE.WebGLRenderer({ antialias: true }); // 渲染器
      this.renderer.setSize(window.innerWidth - 100, window.innerHeight - 100);
      this.renderer.shadowMap.enabled = true; // 开启阴影

      let axes = new THREE.AxesHelper(200); // 坐标轴

      let cubeGeometry = new THREE.BoxGeometry(10, 10, 10); //创建一个立方体几何对象Geometry
      // 线条渲染模式LineDashedMaterial、LineBasicMaterial
      let cubeMaterial = new THREE.LineDashedMaterial({
        color: 0xff0000, //线条颜色
        linewidth: 1,
        scale: 0.1,
        dashSize: 0.6,
        gapSize:0.2
      }); //材质对象

      // line、lineLoop、lineSegments、
      this.cube = new THREE.Line(cubeGeometry, cubeMaterial);

      this.cube.position.x = 0;
      this.cube.position.y = 2;
      this.cube.position.z = 0;
      this.cube.castShadow = true;

      let spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(-40, 60, -10);
      spotLight.castShadow = true;

      this.scene.add(axes); // 场景添加坐标轴
      this.scene.add(this.cube);
      this.scene.add(spotLight);

      this.$refs.demo1.append(this.renderer.domElement);
      this.renderer.render(this.scene, this.camera);
    }
  }
};
</script>