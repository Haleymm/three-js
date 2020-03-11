<template>
  <div ref="demo1"></div>
</template>

<script>
//多个线条组合CurvePath：把多个圆弧线、样条曲线、直线等多个曲线合并成一个曲线。
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

      var curve;
      //创建 U 型圆弧
      {
        //声明一个几何体对象Geometry
        let geometry = new THREE.Geometry();
        //创建底部半圆弧
        let R = 80;
        let arc = new THREE.ArcCurve(0, 0, R, 0, Math.PI, true);
        // 绘制左右两条直线,线条长度为100
        let line1 = new THREE.LineCurve(
          new THREE.Vector2(R, 100, 0),
          new THREE.Vector2(R, 0, 0)
        );
        let line2 = new THREE.LineCurve(
          new THREE.Vector2(-R, 0, 0),
          new THREE.Vector2(-R, 100, 0)
        );
        // 创建组合曲线对象CurvePath
        var CurvePath = new THREE.CurvePath();
        // 把多个线条插入到CurvePath中
        CurvePath.curves.push(line1, arc, line2);

        //分段数200，得到201个顶点
        var points = CurvePath.getPoints(200);
        // setFromPoints方法从points中提取数据改变几何体的顶点属性vertices
        geometry.setFromPoints(points);
        //材质对象
        var material = new THREE.LineBasicMaterial({
          color: 0x000000
        });
        //线条模型对象
       curve = new THREE.Line(geometry, material);
      }

      let spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(-40, 60, -10);
      spotLight.castShadow = true;

      this.scene.add(axes); // 场景添加坐标轴

      this.scene.add(curve);

      this.scene.add(spotLight);

      this.$refs.demo1.append(this.renderer.domElement);
      this.renderer.render(this.scene, this.camera);
    }
  }
};
</script>