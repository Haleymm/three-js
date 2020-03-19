<template>
  <div ref="demo1"></div>
</template>

<script>
// 鼠标控制场景移动
const THREE = require("three");
const OrbitControls = require("three-orbitcontrols");
import dat from "dat.gui";

export default {
  data: () => ({
    scene: null,
    camera: null,
    renderer: null,
    rotationSpeed: 0.02,
    controls: null
  }),
  created() {
    this.$nextTick(() => {
      this.init();
    });
  },
  methods: {
    init() {
      //   const gui = new dat.GUI(); // gui监测器
      //   gui.add(controls, "rotationSpeed", 0, 0.5);
      this.initScene();
      this.initCamera();
      this.initMesh();
      this.initLight();
      this.initRender();
      this.initControls();
      this.animate();
      window.onresize = this.onWindowResize;
    },
    initScene() {
      // 创建一个场景对象
      this.scene = new THREE.Scene();
      this.scene.background = new THREE.Color(0xcccccc);

      let axes = new THREE.AxesHelper(200);
      this.scene.add(axes); // 场景添加坐标轴
    },
    initCamera() {
      // 创建一个相机对象， (视野角度1，长宽比2，近截面3，远截面4)，1越大，物体越远离屏幕；
      let width = window.innerWidth; //窗口宽度
      let height = window.innerHeight; //窗口高度
      let k = width / height; //窗口宽高比
      let s = 200; //三维场景显示范围控制系数，系数越大，显示的范围越大
      this.camera = new THREE.OrthographicCamera(-s * k, s * k, s, -s, 1, 1000);
      this.camera.position.set(100, 100, 100); //设置相机位置
      this.camera.lookAt(this.scene.position);
    },
    initMesh() // 模型
    {
      let geometry = new THREE.BoxGeometry(50, 50, 100);
      let material = new THREE.MeshPhongMaterial({ color: 0xff0000 });
      let mesh = new THREE.Mesh(geometry, material);
      this.scene.add(mesh);
    },
    initLight() {
      let point = new THREE.PointLight(0xffffff);
      point.position.set(400, 200, 300); //点光源位置
      this.scene.add(point);
    },
    // 初始化渲染器
    initRender() {
      this.renderer = new THREE.WebGLRenderer({ antialias: true }); // 渲染器
      this.renderer.setSize(window.innerWidth - 600, window.innerHeight - 100);
      this.renderer.shadowMap.enabled = true; // 开启阴影
      this.$refs.demo1.append(this.renderer.domElement);
    },
    // 执行渲染操作
    render() {
      this.renderer.render(this.scene, this.camera);
    },
    // 初始化控制参数
    initControls() {
      // 创建控制器
      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
      // 惯性控制，阻尼或自转
      this.controls.enableDampling = false;
      // 缩放控制
      this.controls.enableZoom = true;
      // 鼠标右键拖拽
      this.controls.enablePan = true;
      // 自动旋转
      this.controls.autoRotate = false;
      // 动态阻尼系数：即鼠标拖拽旋转灵敏度
      this.controls.dampingFactor = 0.25;
      //设置相机距离原点的最近距离；
      this.controls.minDistance = 200;
      //设置相机距离原点的最远距离；
      this.controls.maxDistance = 600;
    },
    //更新控制器
    animate() {
      this.controls.update();
      this.render();
      requestAnimationFrame(this.animate);
    },
    // window resize触发
    onWindowResize() {
      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();
      this.render();
      this.rendererer.setSize(window.innerWidth, window.innerHeight);
    }
  }
};
</script>