<template>
  <div ref="demo1"></div>
</template>

<script>
// 点
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

      let axes = new THREE.AxesHelper(20); // 坐标轴

      // points,任意位置的点
      let cubeGeometry = new THREE.Geometry(); //声明一个几何体对象Geometry
      let p1 = new THREE.Vector3(5, 0, 0); //顶点1坐标
      let p2 = new THREE.Vector3(0, 2, 0); //顶点2坐标
      let p3 = new THREE.Vector3(0, 0, 10); //顶点3坐标
      cubeGeometry.vertices.push(p1, p2, p3);
      // Color对象表示顶点颜色数据
      var color1 = new THREE.Color(0x0000ff); //顶点1颜色——蓝色
      var color2 = new THREE.Color(0x00ff00); //顶点2颜色——红色
      var color3 = new THREE.Color(0xff0000); //顶点3颜色——蓝色
      //顶点颜色数据添加到geometry对象
      cubeGeometry.colors.push(color1, color2, color3);
      // 点材质只有PointsMaterial
      let cubeMaterial = new THREE.PointsMaterial({
        color: 0x0000ff, //颜色
        size: 3, //点渲染尺寸
        vertexColors: THREE.VertexColors, //以顶点颜色为准
      });

      this.cube = new THREE.Points(cubeGeometry, cubeMaterial);

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