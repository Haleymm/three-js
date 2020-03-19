<template>
  <div ref="demo1"></div>
</template>

<script>
// 透视投影相机
import * as THREE from "three";
import dat from "dat.gui";
import { BoxGeometry } from "three";
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
        // 透视投影相机设置
        var width = window.innerWidth; //窗口宽度
        var height = window.innerHeight; //窗口高度
        /**透视投影相机对象*/
        this.camera = new THREE.PerspectiveCamera(90, width / height, 1, 1000);

        this.camera.position.set(0, 0, 300); //设置相机位置
        this.camera.lookAt(this.scene.position); //设置相机方向(指向的场景对象)
      }

      // 坐标轴
      {
        let axes = new THREE.AxesHelper(200);
        this.scene.add(axes); // 场景添加坐标轴
      }

      // 创建一个模型对象
      var mesh;
      {
        let geometry = new THREE.BoxGeometry(100, 100, 100);
        let material = new THREE.MeshLambertMaterial({
          color: 0xffff00
        });
        mesh = new THREE.Mesh(geometry, material);
        this.scene.add(mesh);
      }
      {
        let geometry = new THREE.BoxGeometry(100, 100, 100);
        let material = new THREE.MeshLambertMaterial({
          color: 0xffff00
        });
        let mesh = new THREE.Mesh(geometry, material);
        mesh.translateX(-150);
        mesh.translateZ(100);

        this.scene.add(mesh);
      }
      {
        let geometry = new THREE.BoxGeometry(100, 100, 100);
        let material = new THREE.MeshLambertMaterial({
          color: 0xffff00
        });
        let mesh = new THREE.Mesh(geometry, material);
        mesh.translateX(150);
        mesh.translateZ(-100);

        this.scene.add(mesh);
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