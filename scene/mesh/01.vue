<template>
  <div ref="demo1"></div>
</template>

<script>
//网格模型 MESH
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
      this.camera.position.y = 40;
      this.camera.position.z = 30;
      this.camera.lookAt(this.scene.position);

      this.renderer = new THREE.WebGLRenderer({ antialias: true }); // 渲染器
      this.renderer.setSize(window.innerWidth - 600, window.innerHeight - 100);
      this.renderer.shadowMapEnabled = true; // 开启阴影

      let axes = new THREE.AxesHelper(20); // 坐标轴

      // 网格模型-几何体
      //   几何体类型-长方体
      let cubeGeometry = new THREE.BoxGeometry(10, 10, 10);

      // 网格模型-材质
      let cubeMaterial = new THREE.MeshLambertMaterial({
        color: 0xff0066
        // 是否只显示几何体的线条
        // wireframe: true
      });
      //网格模型对象Mesh
      this.cube = new THREE.Mesh(cubeGeometry, cubeMaterial);

      //缩放2-模型
      this.cube.scale.set(1, 1.2, 1);
      this.cube.scale.x = 0.5;
      // 坐标轴平移
      this.cube.translateX(10);
      this.cube.translateZ(-5);
      // 自定义平移方向
      let axis = new THREE.Vector3(1, 1, 0);
      axis.normalize(); //向量归一化
      this.cube.translateOnAxis(axis, 20);

      // 设置y坐标
      //   this.cube.position.x = 0;
      //   this.cube.position.y = 3;
      //   this.cube.position.z = 0;
      //   this.cube.castShadow = true;

      //   绕坐标轴旋转45°
      this.cube.rotateX(Math.PI / 4);
      // 绕自定义(0,1,0)向量旋转π/8
      let degv = new THREE.Vector3(0, 1, 0);
      degv.normalize();
      this.cube.rotateOnAxis(axis, Math.PI / 8);

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