<template>
  <div ref="demo1"></div>
</template>

<script>
//Shape对象和轮廓填充ShapeGeometry
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

      //自定义几何体
      var mesh;
      {
        // 定义6个点
        let points = [
          new THREE.Vector2(-50, -50),
          new THREE.Vector2(-60, 0),
          new THREE.Vector2(0, 50),
          new THREE.Vector2(60, 0),
          new THREE.Vector2(50, -50),
          new THREE.Vector2(-50, -50)
        ];

        // 通过顶点定义轮廓
        let shape = new THREE.Shape(points);

        // shape可以理解为一个需要填充的轮廓
        // 所谓填充：ShapeGeometry算法利用顶点计算出三角面face3数据填充轮廓
        let geometry = new THREE.ShapeGeometry(shape, 25);

        let material = new THREE.MeshPhongMaterial({
          color: 0x00ffff, //三角面颜色
          side: THREE.DoubleSide //两面可见
        }); //材质对象
        // material.wireframe = true; //线条模式渲染(查看细分数)
        mesh = new THREE.Mesh(geometry, material); 
      }

      {
        // 定义圆形平面,通过ShapeGeometry生成几何体
        // 通过shpae基类path的方法绘制轮廓（本质也是生成顶点）
        let shape = new THREE.Shape();
        shape.absarc(0, 0, 100, 0, 2 * Math.PI); //圆弧轮廓
        console.log(shape.getPoints(15)); //查看shape顶点数据

        let geometry = new THREE.ShapeGeometry(shape, 25);

        let material = new THREE.MeshPhongMaterial({
          color: 0x00ffff, //三角面颜色
          side: THREE.DoubleSide //两面可见
        }); //材质对象
        // mesh = new THREE.Mesh(geometry, material); 
      }

      {
        // 多个shape同时填充
        // 轮廓对象1
        let shape = new THREE.Shape();
        shape.arc(-50, 0, 30, 0, 2 * Math.PI);
        // 轮廓对象2
        let shape2 = new THREE.Shape();
        shape2.arc(50, 0, 30, 0, 2 * Math.PI);
        // 轮廓对象3
        let shape3 = new THREE.Shape();
        shape3.arc(0, 50, 30, 0, 2 * Math.PI);
        // 多个shape作为元素组成数组,每一个shpae可以理解为一个要填充的轮廓
        let geometry = new THREE.ShapeGeometry([shape, shape2, shape3], 30);
        let material = new THREE.MeshPhongMaterial({
          color: 0x00ffff, //三角面颜色
          side: THREE.DoubleSide //两面可见
        }); //材质对象
        // mesh = new THREE.Mesh(geometry, material); 
      }


      // 嵌套
      {
        // 一个外轮廓圆弧嵌套三个内圆弧轮廓
        let shape = new THREE.Shape(); //Shape对象
        //外轮廓
        shape.arc(0, 0, 100, 0, 2 * Math.PI);
        // 内轮廓1
        let path1 = new THREE.Path();
        path1.arc(0, 0, 40, 0, 2 * Math.PI);
        // 内轮廓2
        let path2 = new THREE.Path();
        path2.arc(80, 0, 10, 0, 2 * Math.PI);
        // 内轮廓3
        let path3 = new THREE.Path();
        path3.arc(-80, 0, 10, 0, 2 * Math.PI);
        //三个内轮廓分别插入到holes属性中
        shape.holes.push(path1, path2, path3);

        let geometry = new THREE.ShapeGeometry(shape, 30);
        let material = new THREE.MeshPhongMaterial({
          color: 0xff0066, //三角面颜色
          side: THREE.DoubleSide //两面可见
        }); //材质对象
        // material.wireframe = true; //线条模式渲染(查看细分数)
        // mesh = new THREE.Mesh(geometry, material); 
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