<template>
  <div ref="demo1"></div>
</template>

<script>
//旋转造型LatheGeometry,如球体，常见杯子
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
      this.camera.position.x = 100;
      this.camera.position.y = 100;
      this.camera.position.z = 300;
      this.camera.lookAt(this.scene.position);

      this.renderer = new THREE.WebGLRenderer({ antialias: true }); // 渲染器
      this.renderer.setSize(window.innerWidth - 600, window.innerHeight - 100);
      this.renderer.shadowMap.enabled = true; // 开启阴影

      let axes = new THREE.AxesHelper(200); // 坐标轴

      //创建旋转几何体
      var mesh;
      {
        let points = [
          new THREE.Vector2(50, 60),
          new THREE.Vector2(25, 0),
          new THREE.Vector2(50, -60)
        ];
        // 借助样条插值得到平滑曲面
        let shape = new THREE.Shape(); //创建Shape对象
        shape.splineThru(points); //顶点带入样条插值计算函数
        let splinePoints = shape.getPoints(20); //插值计算细分数20

        let geometry = new THREE.LatheGeometry(splinePoints, 30); //旋转造型
        let material = new THREE.MeshPhongMaterial({
          color: 0x00ffff, //三角面颜色
          side: THREE.DoubleSide //两面可见
        }); //材质对象
        material.wireframe = true; //线条模式渲染(查看细分数)
        mesh = new THREE.Mesh(geometry, material); //旋转网格模型对象
      }

      let spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(-40, 60, -10);
      spotLight.castShadow = true;

      this.scene.add(axes); // 场景添加坐标轴

      this.scene.add(mesh);

      this.scene.add(spotLight);

      this.$refs.demo1.append(this.renderer.domElement);
      this.renderer.render(this.scene, this.camera);
    }
  }
};
</script>