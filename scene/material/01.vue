<template>
  <div ref="demo1"></div>
</template>

<script>
//纹理贴图加载器TextureLoader
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

      // 创建渲染器
      {
        this.renderer = new THREE.WebGLRenderer({ antialias: true }); // 渲染器
        this.renderer.setSize(
          window.innerWidth - 600,
          window.innerHeight - 100
        );
        this.renderer.shadowMap.enabled = true; // 开启阴影
        this.$refs.demo1.append(this.renderer.domElement);
      }

      // 创建一个相机对象， (视野角度1，长宽比2，近截面3，远截面4)，1越大，物体越远离屏幕；
      // 相机位置
      let width = window.innerWidth; //窗口宽度
      let height = window.innerHeight; //窗口高度
      let k = width / height; //窗口宽高比
      let s = 200; //三维场景显示范围控制系数，系数越大，显示的范围越大
      //创建相机对象
      this.camera = new THREE.OrthographicCamera(-s * k, s * k, s, -s, 1, 1000);
      this.camera.position.set(200, 300, 200); //设置相机位置
      this.camera.lookAt(this.scene.position);

      let axes = new THREE.AxesHelper(200); // 坐标轴
      this.scene.add(axes); // 场景添加坐标轴

      var mesh;
      {
        //   创建一个矩形平面
        let geometry = new THREE.PlaneGeometry(204, 102);
        // 创建一个纹理对象加载器
        let textureLoader = new THREE.TextureLoader();
        let _this = this;
        textureLoader.load("static/img/box.jpg",function(texture) {
            console.log(texture);
            let material = new THREE.MeshLambertMaterial({
              // 设置颜色纹理贴图：Texture对象作为材质map属性的属性值
              map: texture //设置颜色贴图属性值
              // color: 0xfffffff
            });
            //材质对象Material
            mesh = new THREE.Mesh(geometry, material); //网格模型对象Mesh
            _this.scene.add(mesh);
            _this.renderer.render(_this.scene, _this.camera);
          },
          undefined,
          function(err) {
            console.log(err, "err");
          }
        );
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
    }
  }
};
</script>