<template>
  <div ref="demo1"></div>
</template>

<script>
//圆弧线、直线
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
      this.renderer.shadowMap.enabled = true; // 开启阴影

      let axes = new THREE.AxesHelper(20); // 坐标轴

      //圆弧线ArcCurve
      {
        //声明一个几何体对象Geometry
        let geometry = new THREE.Geometry();
        //声明一个圆弧对象,（x,y,r,start rad,end rad）
        let arc = new THREE.ArcCurve(0, 0, 10, 0, 2 * Math.PI);
        //getPoints是基类Curve的方法，返回一个vector2对象作为元素组成的数组
        let points = arc.getPoints(50); //分段数50，返回圆弧的51个顶点
        // setFromPoints方法从points中提取数据改变几何体的顶点属性vertices
        geometry.setFromPoints(points);

        //材质对象
        let material = new THREE.LineBasicMaterial({
          color: 0xff0066
        });
        //线条模型对象
        this.cube = new THREE.Line(geometry, material);
      }

      // 三维直线LineCurve3，二维直线LineCurve
      // 绘制直线也可以直接使用geometry.vertices.push(p1,p2),在创建模型对象时，会自动连接两点，构成直线
      var cube1;
      {
        // 创建一个几何体对象
        let geometry = new THREE.Geometry();
        // 添加两个点
        let p1 = new THREE.Vector3(10, 0, 0);
        let p2 = new THREE.Vector3(0, 10, 0);
        // 三维直线LineCurve3
        let LineCurve = new THREE.LineCurve3(p1, p2);
        // 获取直线的10个点
        let pointArr = LineCurve.getPoints(10);
        // 将点数据给到几何体
        geometry.setFromPoints(pointArr);

        // 材质
        let material = new THREE.LineBasicMaterial({
          color: 0xffff00
        });
        //创建模型对象
        cube1 = new THREE.Line(geometry, material);
      }

      let spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(-40, 60, -10);
      spotLight.castShadow = true;

      this.scene.add(axes); // 场景添加坐标轴
      this.scene.add(this.cube);
      this.scene.add(cube1);
      this.scene.add(spotLight);

      this.$refs.demo1.append(this.renderer.domElement);
      this.renderer.render(this.scene, this.camera);
      //   鼠标操作三维场景旋转缩放
      // {
      //   let controls = new THREE.OrbitControls(camera, renderer.domElement); //创建控件对象
      //   controls.addEventListener("change", this.renderer); //监听鼠标、键盘事件
      // }
    }
  }
};
</script>