<template>
  <div ref="demo1"></div>
</template>

<script>
// 骨骼动画
import * as THREE from "three";
import { MTLLoader, OBJLoader } from "three-obj-mtl-loader";
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
      const gui = new dat.GUI(); // gui监测器
      gui.add(controls, "rotationSpeed", 0, 0.5);
      initMesh();
    },
    initMesh() {
      {
        // 创建一个场景对象
        this.scene = new THREE.Scene();
        // 设置场景颜色
        this.scene.background = new THREE.Color(0xcccccc);
      }
      {
        //正投影相机设置
        var width = window.innerWidth; //窗口宽度
        var height = window.innerHeight; //窗口高度
        var k = width / height; //窗口宽高比
        var s = 100; //三维场景显示范围控制系数，系数越大，显示的范围越大
        //创建相机对象
        this.camera = new THREE.OrthographicCamera(
          -s * k,
          s * k,
          s,
          -s,
          1,
          1000
        );

        this.camera.position.set(100, 100, 100); //设置相机位置
        this.camera.lookAt(this.scene.position); //设置相机方向(指向的场景对象)
      }

      this.renderer = new THREE.WebGLRenderer({ antialias: true }); // 渲染器
      this.renderer.setSize(window.innerWidth - 600, window.innerHeight - 100);
      this.renderer.shadowMap.enabled = true; // 开启阴影

      let axes = new THREE.AxesHelper(200); // 坐标轴

      
      {
        let mtlLoader = new MTLLoader();
        let objLoader = new OBJLoader();
        let _this = this;
        MTLLoader.load("static/model/material.mtl", function(materials) {
          OBJLoader.setMaterials(materials);
          OBJLoader.load("static/model/model.obj", function(obj) {
            _this.scene.add(mesh);
            _this.renderer.render(_this.scene, _this.camera);
          });
        });
      }

      let spotLight = new THREE.SpotLight(0xffffff);
      spotLight.position.set(100, 100, 100);
      spotLight.castShadow = true;

      this.scene.add(axes); // 场景添加坐标轴
      this.scene.add(spotLight);

      this.$refs.demo1.append(this.renderer.domElement);
      this.renderScene();

    },
    renderScene() {
      let { controls, cube, scene, sphere, plane, camera, renderer } = this;
    //   cube.rotation.x += controls.rotationSpeed;
    //   sphere.rotation.y += controls.rotationSpeed;
    //   plane.rotation.z += controls.rotationSpeed;
    cube.scale.x += controls.rotationSpeed
    plane.position.z += controls.rotationSpeed
      requestAnimationFrame(this.renderScene);
      renderer.render(scene, camera);
    }
  }
};
</script>