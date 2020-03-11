<template>
  <div ref="demo1"></div>
</template>

<script>
//样条曲线、贝塞尔曲线
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
      this.camera.position.x = 200;
      this.camera.position.y = 400;
      this.camera.position.z = 300;
      this.camera.lookAt(this.scene.position);

      this.renderer = new THREE.WebGLRenderer({ antialias: true }); // 渲染器
      this.renderer.setSize(window.innerWidth - 600, window.innerHeight - 100);
      this.renderer.shadowMap.enabled = true; // 开启阴影

      let axes = new THREE.AxesHelper(200); // 坐标轴

      //三维样条曲线 CatmullRomCurve3
      var line;
      {
        let geometry = new THREE.Geometry(); //声明一个几何体对象Geometry
        // 三维样条曲线  Catmull-Rom算法,输入5个顶点可以计算得到光滑的曲线
        let curve = new THREE.CatmullRomCurve3([
          new THREE.Vector3(-50, 20, 90),
          new THREE.Vector3(-10, 40, 40),
          new THREE.Vector3(0, 0, 0),
          new THREE.Vector3(60, -60, 0),
          new THREE.Vector3(70, 0, 80)
        ]);
        let points = curve.getPoints(100); //分段数100，返回101个顶点
        // setFromPoints方法从points中提取数据改变几何体的顶点属性vertices
        geometry.setFromPoints(points);
        //材质
        let material = new THREE.LineBasicMaterial({
          color: 0x0000ff
        });
        //创建模型对象
        line = new THREE.Line(geometry, material);
      }

      //二次贝塞尔曲线：输入3个点，其中2个顶点，一个是控制点，控制点不在曲线上
      //三次贝塞尔曲线：输入4个点，其中3个顶点……
      var line1;
      {
        let geometry = new THREE.Geometry(); //声明一个几何体对象Geometry

        let p1 = new THREE.Vector3(-80, 0, 0);
        let p2 = new THREE.Vector3(20, 100, 0); //控制点
        let p3 = new THREE.Vector3(80, 0, 0);
        let curve = new THREE.QuadraticBezierCurve3(p1, p2, p3);
        // new THREE.CubicBezierCurve3(p1, p2, p3, p4);三次曲线
        
        let points = curve.getPoints(100); //分段数100，返回101个顶点
        geometry.setFromPoints(points);
        //材质
        let material = new THREE.LineBasicMaterial({
          color: 0x00ffff
        });
        //创建模型对象
        line1 = new THREE.Line(geometry, material);
      }

      let spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(-40, 60, -10);
      spotLight.castShadow = true;

      this.scene.add(axes); // 场景添加坐标轴

      this.scene.add(line);
      this.scene.add(line1);

      this.scene.add(spotLight);

      this.$refs.demo1.append(this.renderer.domElement);
      this.renderer.render(this.scene, this.camera);
    }
  }
};
</script>