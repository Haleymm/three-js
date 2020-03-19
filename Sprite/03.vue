<template>
  <div ref="demo1"></div>
</template>

<script>
// 精灵模型-动态雨滴
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
      // const gui = new dat.GUI(); // gui监测器
      // gui.add(controls, "rotationSpeed", 0, 0.5);
      initMesh();
    },
    render(group) {
      // 每次渲染遍历雨滴群组，刷新频率30~60FPS，两帧时间间隔16.67ms~33.33ms
      // 每次渲染都会更新雨滴的位置，进而产生动画效果
      group.children.forEach(sprite => {
          console.log(sprite,'sp')
        // 雨滴的y坐标每次减1
        sprite.position.y -= 1;
        if (sprite.position.y < 0) {
          // 如果雨滴落到地面，重置y，从新下落
          sprite.position.y = 200;
          return;
        }
      });
      this.renderer.render(this.scene, this.camera); //执行渲染操作
      requestAnimationFrame(this.render(group)); //请求再次执行渲染函数render，渲染下一帧
    },
    initMesh() {
      // 创建一个场景对象
      this.scene = new THREE.Scene();
      // 设置场景颜色
      //   this.scene.background = new THREE.Color(0xcccccc);

      // 创建渲染器
      {
        this.renderer = new THREE.WebGLRenderer({ antialias: true }); // 渲染器
        this.renderer.setSize(
          window.innerWidth - 100,
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
      //创建相机对象
      this.camera = new THREE.PerspectiveCamera(60, k, 1, 1000);
      this.camera.position.set(292, 109, 268); //设置相机位置
      this.camera.lookAt(this.scene.position);

      // 坐标轴
      {
        let axes = new THREE.AxesHelper(200);
        // this.scene.add(axes); // 场景添加坐标轴
      }

      // 创建一个精灵模型
      {
        let textureTree = new THREE.TextureLoader().load("static/img/rain.png");
        let group = new THREE.Group();
        for (let i = 0; i < 100; i++) {
          let spriteMaterial = new THREE.SpriteMaterial({
            map: textureTree //设置精灵纹理贴图
          });

          // 创建精灵模型对象
          let sprite = new THREE.Sprite(spriteMaterial);
          sprite.scale.set(8, 10, 1); //// 只需要设置x、y两个分量就可以
          var k1 = Math.random() - 0.5;
          var k2 = Math.random() - 0.5;
          var k3 = Math.random() - 0.5;
          // 设置精灵模型位置，在整个空间上上随机分布
          sprite.position.set(200 * k1, 200 * k3, 200 * k2);
          group.add(sprite);
        }
        this.scene.add(group);
        this.render(group);
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