<template>
  <div ref="demo1"></div>
</template>

<script>
//编辑关键帧并解析播放
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
        let s = 100; //三维场景显示范围控制系数，系数越大，显示的范围越大
        //创建相机对象
        this.camera = new THREE.OrthographicCamera(
          -s * k,
          s * k,
          s,
          -s,
          1,
          1000
        );
        this.camera.position.set(100, 100, 200); //设置相机位置
        this.camera.lookAt(this.scene.position);
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
      }

      // 坐标轴
      {
        let axes = new THREE.AxesHelper(200);
        axes.position.x = -100;
        this.scene.add(axes); // 场景添加坐标轴
      }

      {
        let spheregeometry = new THREE.SphereGeometry(20, 20, 20);
        let boxgeometry = new THREE.BoxGeometry(50, 10, 10);
        let material = new THREE.MeshPhongMaterial({ color: 0xff0066 });
        let mesh2 = new THREE.Mesh(spheregeometry, material);
        let mesh1 = new THREE.Mesh(boxgeometry, material);
        mesh1.name = "Box"; //网格模型1命名
        mesh2.name = "Sphere"; //网格模型2命名
        let group = new THREE.Group();
        group.translateX(-100);
        group.add(mesh1); //网格模型添加到组中
        group.add(mesh2); //网格模型添加到组中
        this.scene.add(group);

        // 创建位置关键帧对象：0时刻对应位置(0, 0, 0)  // 10时刻对应位置(150, 0, 0)
        var posTrack = new THREE.KeyframeTrack("Box.position", [0, 10], [0, 0, 0, 150, 0, 0]);
        // 创建颜色关键帧对象
        var colorKF = new THREE.KeyframeTrack(
          "Box.material.color",
          [10, 20],
          [1, 0, 0, 0, 0, 1]
        );
        // 创建名为Sphere对象的关键帧数据  从0~20时间段，尺寸scale缩放3倍
        var scaleTrack = new THREE.KeyframeTrack(
          "Sphere.scale",
          [0, 20],
          [1, 1, 1, 3, 3, 3]
        );

        // duration决定了默认的播放时间，一般取所有帧动画的最大时间
        // duration偏小，帧动画数据无法播放完，偏大，播放完帧动画会继续空播放
        var duration = 20;
        // 多个帧动画作为元素创建一个剪辑clip对象，命名"default"，持续时间20
        var clip = new THREE.AnimationClip("default", duration, [
          posTrack,
          colorKF,
          scaleTrack
        ]);
        var mixer = new THREE.AnimationMixer(group);
        // 剪辑clip作为参数，通过混合器clipAction方法返回一个操作对象AnimationAction
        var AnimationAction = mixer.clipAction(clip);
        //通过操作Action设置播放方式
        AnimationAction.timeScale = 20; //默认1，可以调节播放速度
        // AnimationAction.loop = THREE.LoopOnce; //不循环播放
        AnimationAction.play(); //开始播放
        var clock = new THREE.Clock();
        // 渲染函数
        let _this = this;
        function render() {
          _this.renderer.render(_this.scene, _this.camera); //执行渲染操作
          requestAnimationFrame(render); //请求再次执行渲染函数render，渲染下一帧

          //clock.getDelta()方法获得两帧的时间间隔
          // 更新混合器相关的时间
          mixer.update(clock.getDelta());
        }
        render();
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