<template>
  <div ref="demo1"></div>
</template>

<script>
// 操作节点，命名、查找、遍历
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
      {
        // 创建一个场景对象
        this.scene = new THREE.Scene();
        this.scene.background = new THREE.Color(0xcccccc);
      }
      {

        // 创建一个相机对象， (视野角度1，长宽比2，近截面3，远截面4)，1越大，物体越远离屏幕；
        // 相机位置
        let width = window.innerWidth; //窗口宽度
        let height = window.innerHeight; //窗口高度
        let k = width / height; //窗口宽高比
        let s = 200; //三维场景显示范围控制系数，系数越大，显示的范围越大
        //创建相机对象
        this.camera = new THREE.OrthographicCamera(
          -s * k,
          s * k,
          s,
          -s,
          1,
          1000
        );
        this.camera.position.set(200, 300, 200); //设置相机位置
        this.camera.lookAt(this.scene.position);
      }

      // 坐标轴
      {
        let axes = new THREE.AxesHelper(200);
        this.scene.add(axes); // 场景添加坐标轴
      }

      // 创建一个模型对象
      var mesh;
      {
        var geometry = new THREE.BoxGeometry(50, 50, 50);
        var material = new THREE.MeshLambertMaterial({ color: 0x0000ff });
        var group = new THREE.Group();
        var mesh1 = new THREE.Mesh(geometry, material);
        var mesh2 = new THREE.Mesh(geometry, material);
        mesh2.translateX(100);
        //把mesh1型插入到组group中，mesh1作为group的子对象
        group.add(mesh1);
        //把mesh2型插入到组group中，mesh2作为group的子对象
        group.add(mesh2);
        //把group插入到场景中作为场景子对象
        group.translateZ(100)
        this.scene.add(group);
        console.log(group.children,'查看子对象')
      }

      //点光源
      {
        let point = new THREE.PointLight(0xffffff);
        point.position.set(400, 200, 300); //点光源位置
        this.scene.add(point);
      }
      // 环境光源
      {
        var ambient = new THREE.AmbientLight(0x444444);
        this.scene.add(ambient);
      }
      // 创建渲染器
      {
        this.renderer = new THREE.WebGLRenderer({ antialias: true }); // 渲染器
        this.renderer.setSize(
          window.innerWidth - 600,
          window.innerHeight - 100
        );
        this.renderer.shadowMap.enabled = true; // 开启阴影
        this.$refs.demo1.append(this.renderer.domElement);
        this.renderer.render(this.scene, this.camera);
      }
    }
  }
};
</script>