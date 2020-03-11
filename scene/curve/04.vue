<template>
  <div ref="demo1"></div>
</template>

<script>
//TubeGeometry 通过一条曲线生成一个圆管
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
      this.camera.position.z = 30;
      this.camera.lookAt(this.scene.position);

      this.renderer = new THREE.WebGLRenderer({ antialias: true }); // 渲染器
      this.renderer.setSize(window.innerWidth - 600, window.innerHeight - 100);
      this.renderer.shadowMap.enabled = true; // 开启阴影

      let axes = new THREE.AxesHelper(200); // 坐标轴

      var pipe;
      {
        //创建管道成型的路径(3D样条曲线),管道的整体形状是三次样条曲线
        let path = new THREE.CatmullRomCurve3([
          new THREE.Vector3(-10, -50, -50),
          new THREE.Vector3(10, 0, 0),
          new THREE.Vector3(8, 50, 50),
          new THREE.Vector3(-5, 0, 100)
        ]);
        // LineCurve3创建直线段路径，管道是直的
        // let path = new THREE.LineCurve3(new THREE.Vector3(0, 100, 0), new THREE.Vector3(0, 0, 0));

        // path:路径   40：沿着轨迹细分数  2：管道半径   25：管道截面圆细分数
        let geometry = new THREE.TubeGeometry(path, 100, 5, 25);

        //材质对象
        var material = new THREE.LineBasicMaterial({
          color: 0x0000ff
        });
        //线条模型对象
        // pipe = new THREE.Line(geometry, material);
      }

      // 多段路径生成管道
      {
          let path1 = new THREE.CatmullRomCurve3([
          new THREE.Vector3(-10, -50, -50),
          new THREE.Vector3(10, 0, 0),
          new THREE.Vector3(8, 50, 50),
          new THREE.Vector3(-5, 0, 100)
        ]);
         let path2 = new THREE.LineCurve3(new THREE.Vector3(-5, 0, 100), new THREE.Vector3(80, 20, -50));

         let CurvePath = new THREE.CurvePath();// 创建CurvePath对象
         CurvePath.curves.push(path1,path2);
        let geometry = new THREE.TubeGeometry(CurvePath, 250, 5, 25);
        //材质对象
        var material = new THREE.LineBasicMaterial({
          color: 0x0000ff
        });
        //线条模型对象
        pipe = new THREE.Line(geometry, material);

      }
      let spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(-40, 60, -10);
      spotLight.castShadow = true;

      this.scene.add(axes); // 场景添加坐标轴

      this.scene.add(pipe);

      this.scene.add(spotLight);

      this.$refs.demo1.append(this.renderer.domElement);
      this.renderer.render(this.scene, this.camera);
    }
  }
};
</script>