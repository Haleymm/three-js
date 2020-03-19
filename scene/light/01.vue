<template>
  <div ref="demo1"></div>
</template>

<script>
//常见光源类型
import * as THREE from "three";
import dat from "dat.gui";
import { Mesh } from "three";
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
        this.camera.position.set(1, 200, 100); //设置相机位置
        this.camera.lookAt(this.scene.position);
      }

      // 坐标轴
      {
        let axes = new THREE.AxesHelper(200);
        // this.scene.add(axes); // 场景添加坐标轴
      }

      // 创建一个模型对象
      var mesh;
      {
        let geometry = new THREE.PlaneGeometry(600, 200);
        let meterial = new THREE.MeshPhongMaterial({
          color: 0xfff00ff
        });
        mesh = new THREE.Mesh(geometry, meterial);
        this.scene.add(mesh);
      }

      //点光源
      {
        let point = new THREE.PointLight(0xffffff);
        point.position.set(400, 200, 300); //点光源位置
        this.scene.add(point);
      }
      {
        // 平行光源
        let directionalLight = new THREE.DirectionalLight(0xffffff, 1);
        directionalLight.position.set(400, 200, 300);
        // 方向光指向对象网格模型mesh2，可以不设置，默认的位置是0,0,0
        directionalLight.target = mesh;
        // this.scene.add(directionalLight);
      }
      {
        // 聚光灯光源
        let spotLight = new THREE.SpotLight(0xffffff);
        // 设置聚光光源位置
        spotLight.position.set(400, 200, 300);
        spotLight.target = mesh;
        // 设置聚光光源发散角度
        spotLight.angle = Math.PI / 6;
        // this.scene.add(spotLight); //光对象添加到scene场景中
      }
      {
        // 环境光源
        var ambient = new THREE.AmbientLight(0xffffff);
        // this.scene.add(ambient); //环境光对象添加到scene场景中
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