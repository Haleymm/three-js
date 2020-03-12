<template>
  <div ref="demo1"></div>
</template>

<script>
// 环境贴图(.envMap)
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
        let geometry = new THREE.BoxGeometry(100, 100, 100); //立方体
        let loader = new THREE.CubeTextureLoader();
        // 所有贴图在同一目录下，可以使用该方法设置共用路径
        loader.setPath("static/img/");
        let _this = this;
        // 立方体纹理加载器返回立方体纹理对象CubeTexture
        loader.load(
          [
            "yx.jpg",
            "box.jpg",
            "box.jpg",
            "grass.jpg",
            "bump.jpg",
            "circle.jpg"
          ],
          CubeTexture => {
            let material = new THREE.MeshLambertMaterial({
              envMap: CubeTexture //设置环境贴图
            });

            mesh = new THREE.Mesh(geometry, material); //网格模型对象Mesh
            _this.scene.add(mesh);
            _this.renderer.render(_this.scene, _this.camera);
          },undefined,err=>{
              console.log(err)
          });
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