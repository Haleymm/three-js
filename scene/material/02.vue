<template>
  <div ref="demo1"></div>
</template>

<script>
//顶点纹理坐标UV
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
      // 相机位置
      let width = window.innerWidth; //窗口宽度
      let height = window.innerHeight; //窗口高度
      let k = width / height; //窗口宽高比
      let s = 200; //三维场景显示范围控制系数，系数越大，显示的范围越大
      //创建相机对象
      this.camera = new THREE.OrthographicCamera(-s * k, s * k, s, -s, 1, 1000);
      this.camera.position.set(200, 300, 200); //设置相机位置
      this.camera.lookAt(this.scene.position);

      // 坐标轴
      {
        let axes = new THREE.AxesHelper(200);
        this.scene.add(axes); // 场景添加坐标轴
      }

      var mesh;
      // 自定义几何体
      {
        let geometry = new THREE.Geometry();
        let p1 = new THREE.Vector3(0, 0, 0); //顶点1坐标
        let p2 = new THREE.Vector3(160, 0, 0); //顶点2坐标
        let p3 = new THREE.Vector3(160, 80, 0); //顶点3坐标
        let p4 = new THREE.Vector3(0, 80, 0); //顶点4坐标
        geometry.vertices.push(p1, p2, p3, p4); //顶点坐标添加到geometry对象
        /** 三角面1、三角面2*/
        let normal = new THREE.Vector3(0, 0, 1); //三角面法向量
        let face0 = new THREE.Face3(0, 1, 2, normal); //三角面1
        let face1 = new THREE.Face3(0, 2, 3, normal); //三角面2
        geometry.faces.push(face0, face1); //三角面1、2添加到几何体
        /**纹理坐标*/
        let t0 = new THREE.Vector2(0, 0); //图片左下角
        let t1 = new THREE.Vector2(1, 0); //图片右下角
        let t2 = new THREE.Vector2(1, 1); //图片右上角
        let t3 = new THREE.Vector2(0, 1); //图片左上角
        let uv1 = [t0, t1, t2]; //选中图片一个三角区域像素——映射到三角面1
        let uv2 = [t0, t2, t3]; //选中图片一个三角区域像素——映射到三角面2
        geometry.faceVertexUvs[0].push(uv1, uv2); //纹理坐标传递给纹理三角面属性
        
        let material = new THREE.MeshLambertMaterial({
          color: 0xff00ff
        });
        mesh = new THREE.Mesh(geometry, material); //网格模型对象Mesh
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