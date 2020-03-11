<template>
  <div ref="demo1"></div>
</template>

<script>
//拉伸扫描成型ExtrudeGeometry
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

      var mesh;
      {
        //   拉伸，是特殊的滑动，滑动路线为Z轴
        let shape = new THREE.Shape();
        shape.moveTo(0, 0); //起点
        shape.lineTo(0, 50); //第2点
        shape.lineTo(50, 50); //第3点
        shape.lineTo(50, 0); //第4点
        shape.lineTo(0, 0); //第5点

        var geometry = new THREE.ExtrudeGeometry( //拉伸造型
          shape, //二维轮廓
          //拉伸参数
          {
            amount: 120, //拉伸长度
            bevelEnabled: false //无倒角
          }
        );
        // 几何体拉伸的本质效果就是空间分布顶点数据的产生
        let material = new THREE.MeshPhongMaterial({
          color: 0x00ffff
          //   size: 5.0 //点对象像素尺寸
        }); //材质对象
        // mesh = new THREE.Mesh(geometry, material); //模型对象
      }

      {
        //   滑动，需要定义滑动的窗和路线，路线可以是曲线
        let shape = new THREE.Shape();
        // 四条直线绘制一个矩形轮廓
        shape.moveTo(0, 0); //起点
        shape.lineTo(0, 10); //第2点
        shape.lineTo(10, 10); //第3点
        shape.lineTo(10, 0); //第4点
        shape.lineTo(0, 0); //第5点
        //创建轮廓的滑动轨迹(3D样条曲线)
        let curve = new THREE.SplineCurve3([
          new THREE.Vector3(0, 100, 0),
          new THREE.Vector3(-60, 30, 30),
          new THREE.Vector3(50, 0, 0),
          new THREE.Vector3(10, -50, 30)
        ]);
        let geometry = new THREE.ExtrudeGeometry( //拉伸造型
          shape, //二维轮廓
          //拉伸参数
          {
            bevelEnabled: false, //无倒角
            extrudePath: curve, //选择扫描轨迹
            steps: 50 //扫描方向细分数
          }
        );
        let material = new THREE.MeshPhongMaterial({
          color: 0x00ffff
          //   size: 5.0 //点对象像素尺寸
        }); //材质对象
        material.wireframe = true;
        mesh = new THREE.Mesh(geometry, material); //模型对象
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