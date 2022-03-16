<template>
  <div id="cesiumContainer"></div>
  <button v-on:click="backToOrgin">回到初始视角</button>
  <button v-on:click="lockView">锁死视角</button>
  <button v-on:click="rotateLeft">左旋</button>
</template>

<script>
import * as Cesium from "cesium";
import { onMounted } from "@vue/runtime-core";
export default {
  setup() {
    let viewer;
    onMounted(() => {
      var arcMap = new Cesium.ArcGisMapServerImageryProvider({
        url: "https://services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer",
      });
      var gaodeMap = new Cesium.UrlTemplateImageryProvider({
        url: "https://webst02.is.autonavi.com/appmaptile?style=6&x={x}&y={y}&z={z}",
      });
      var tianMap = new Cesium.UrlTemplateImageryProvider({
        url: "https://t5.tianditu.gov.cn/DataServer?T=img_w&x={x}&y={y}&l={z}&tk=49190982fae336528563aaf761b39e67",
      });
      viewer = new Cesium.Viewer("cesiumContainer", {
        //viewer初始化设置
        baseLayerPicker: false,
        imageryProvider: arcMap,
        timeline: false,
        navigationHelpButton: false,
        navigationInstructionsInitiallyVisible: false,
        fullscreenButton: false,
        homeButton: false,
        infoBox: false, //单击会显示信息
        selectionIndicator: false, //关闭长按出现选取方块的功能
        animation: false,
        geocoder: true, //搜索功能，暂时打开
        sceneModePicker: true, //选择场景模式，关掉，手动设为2.5D
        //imageryProvider:
      });
      var scene = viewer.scene;
      // scene.screenSpaceCameraController.enableZoom = false; //禁止缩放
      // scene.screenSpaceCameraController.enableTilt = false; //禁止倾斜
      viewer.cesiumWidget.creditContainer.style.display = "none"; //移除版权信息
      scene.skyAtmosphere.show = false; // 关闭大气层
      scene.debugShowFramesPerSecond = true; //显示帧数
      scene.screenSpaceCameraController.enableCollisionDetection = false; // 是否允许相机进入地下
      scene.mode = Cesium.SceneMode.COLUMBUS_VIEW; //默认2.5D

      //设置初始化视角
      viewer.camera.setView({
        destination: Cesium.Cartesian3.fromDegrees(
          111.68292393061465,
          21.840709070727815,
          1000
        ),
        orientation: {
          //方向、俯视和仰角
          heading: Cesium.Math.toRadians(80),
          pitch: Cesium.Math.toRadians(-30),
        },
      });

      //改为右键
      scene.screenSpaceCameraController.tiltEventTypes = [
        Cesium.CameraEventType.RIGHT_DRAG,
        Cesium.CameraEventType.PINCH,
        {
          eventType: Cesium.CameraEventType.LEFT_DRAG,
          modifier: Cesium.KeyboardEventModifier.CTRL,
        },
        {
          eventType: Cesium.CameraEventType.RIGHT_DRAG,
          modifier: Cesium.KeyboardEventModifier.CTRL,
        },
      ];
      scene.screenSpaceCameraController.zoomEventTypes = [
        Cesium.CameraEventType.MIDDLE_DRAG,
        Cesium.CameraEventType.WHEEL,
        Cesium.CameraEventType.PINCH,
      ];

      var MatrixRoute1 = Cesium.Transforms.eastNorthUpToFixedFrame(
        Cesium.Cartesian3.fromDegrees(111.695937290002, 21.8428761847095)
      );
      var point1 = scene.primitives.add(
        Cesium.Model.fromGltf({
          id: "标记1",
          url: "../src/3Dmodel/标注点.gltf",
          modelMatrix: MatrixRoute1,
          scale: 10, //放大倍数
          incrementallyLoadTextures: true,
        })
      );

      var MatrixRoute2 = Cesium.Transforms.eastNorthUpToFixedFrame(
        Cesium.Cartesian3.fromDegrees(111.694978343395, 21.8427164366642)
      );
      var point2 = scene.primitives.add(
        Cesium.Model.fromGltf({
          id: "标记2",
          url: "../src/3Dmodel/标注点.gltf",
          modelMatrix: MatrixRoute2,
          scale: 10, //放大倍数
          incrementallyLoadTextures: true,
        })
      );

      var MatrixRoute3 = Cesium.Transforms.eastNorthUpToFixedFrame(
        Cesium.Cartesian3.fromDegrees(111.694036062564, 21.8425401000858)
      );
      var point3 = scene.primitives.add(
        Cesium.Model.fromGltf({
          id: "标记3",
          url: "../src/3Dmodel/标注点.gltf",
          modelMatrix: MatrixRoute3,
          scale: 10, //放大倍数
          incrementallyLoadTextures: true,
        })
      );

      var MatrixRoute4 = Cesium.Transforms.eastNorthUpToFixedFrame(
        Cesium.Cartesian3.fromDegrees(111.693091601014, 21.8423708733865)
      );
      var point4 = scene.primitives.add(
        Cesium.Model.fromGltf({
          id: "标记4",
          url: "../src/3Dmodel/标注点.gltf",
          modelMatrix: MatrixRoute4,
          scale: 10, //放大倍数
          incrementallyLoadTextures: true,
        })
      );

      //点击检测
      var handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);
      handler.setInputAction(function (click) {
        var pick = viewer.scene.pick(click.position);
        if (!pick) {
          return;
        }
        if (pick.id === undefined) {
          return;
        }
        //选中某模型   pick选中的对象
        if (Cesium.defined(pick)) {
          console.log("点到了" + pick.id);
        }
      }, Cesium.ScreenSpaceEventType.LEFT_DOWN);

      //不用
      function mode3D_play(checkbox) {
        if (checkbox.checked == true) {
          model.show = true;
        } else {
          model.show = false;
        }
      }
    });
    //跳转到初始视角，与setView的区别在于有飞行的过程
    function backToOrgin() {
      console.log("跳转到初始视角");
      viewer.camera.flyTo({
        destination: Cesium.Cartesian3.fromDegrees(
          111.68292393061465,
          21.840709070727815,
          1000
        ),
        orientation: {
          //方向、俯视和仰角
          heading: Cesium.Math.toRadians(80),
          pitch: Cesium.Math.toRadians(-30),
        },
        duration: 0.4,
      });
    }
    function lockView() {
      //锁死视角，现在没用了
      window.setInterval(function () {
        var lon =
          (viewer.camera.positionCartographic.longitude * 180) / Math.PI;
        var lan = (viewer.camera.positionCartographic.latitude * 180) / Math.PI;
        if (
          Cesium.Math.toRadians(-90) < viewer.camera.pitch &&
          viewer.camera.pitch < Cesium.Math.toRadians(-20)
        ) {
          //console.log("好视角")
        } else if (Cesium.Math.toRadians(-90) > viewer.camera.pitch) {
          //console.log("差视角")
          viewer.camera.setView({
            destination: Cesium.Cartesian3.fromDegrees(lon, lan, 1000),
            orientation: {
              //方向、俯视和仰角
              heading: Cesium.Math.toRadians(0),
              pitch: Cesium.Math.toRadians(-89),
            },
          });
        } else if (viewer.camera.pitch > Cesium.Math.toRadians(-20)) {
          //console.log("差视角");
          viewer.camera.setView({
            destination: Cesium.Cartesian3.fromDegrees(lon, lan, 1000),
            orientation: {
              //方向、俯视和仰角
              heading: Cesium.Math.toRadians(0),
              pitch: Cesium.Math.toRadians(-21),
            },
          });
        }

        console.log("经度" + lon + ", 纬度" + lan);
      }, 800);
    }
    //每秒检测一下视角对不对，此处问题非常多，建议锁定上下视角
    function normalized3D(x, y, z) {
      const new_x = x / Math.sqrt(x * x + y * y + z * z);
      const new_y = y / Math.sqrt(x * x + y * y + z * z);
      const new_z = z / Math.sqrt(x * x + y * y + z * z);
      console.log(new_x, new_y, new_z);
      return {
        x: new_x,
        y: new_y,
        z: new_z,
      };
    }
    function rotateLeft() {
      const point = { lon: 111.695937290002, lat: 21.8428761847095 }; // 桩点
      const lon =
        (viewer.camera.positionCartographic.longitude * 180) / Math.PI;
      const lat = (viewer.camera.positionCartographic.latitude * 180) / Math.PI;
      const rotate = 30;
      const newHeading =
        viewer.camera.heading + Cesium.Math.toRadians(rotate * 2.705);
      const newPoint = {
        lon:
          Math.cos(rotate) * (lon - point.lon) -
          Math.sin(rotate) * (lat - point.lat) +
          point.lon,
        lat:
          Math.cos(rotate) * (lat - point.lat) +
          Math.sin(rotate) * (lon - point.lon) +
          point.lat,
      };
      const x1 = Cesium.Cartesian3.fromDegrees(point.lon, point.lat, 0).x,
        y1 = Cesium.Cartesian3.fromDegrees(point.lon, point.lat, 0).y,
        z1 = Cesium.Cartesian3.fromDegrees(point.lon, point.lat, 0).z,
        old_x = Cesium.Cartesian3.fromDegrees(lon, lat, 1000).x,
        old_y = Cesium.Cartesian3.fromDegrees(lon, lat, 1000).y,
        old_z = Cesium.Cartesian3.fromDegrees(lon, lat, 1000).z;
      const { x, y, z } = normalized3D(x1, y1, z1);
      const c = Math.cos(rotate);
      const s = Math.sin(rotate);
      const new_x =
        (x * x * (1 - c) + c) * old_x +
        (x * y * (1 - c) - z * s) * old_y +
        (x * z * (1 - c) + y * s) * old_z;

      const new_y =
        (y * x * (1 - c) + z * s) * old_x +
        (y * y * (1 - c) + c) * old_y +
        (y * z * (1 - c) - x * s) * old_z;

      const new_z =
        (x * z * (1 - c) - y * s) * old_x +
        (y * z * (1 - c) + x * s) * old_y +
        (z * z * (1 - c) + c) * old_z;
      console.log(Cesium.Cartesian3.fromDegrees(point.lon, point.lat, 0));
      console.log(new_x, new_y, new_z);
      console.log(new Cesium.Cartesian3(new_x, new_y, new_z));
      viewer.camera.setView({
        // destination: Cesium.Cartesian3.fromDegrees(
        //   newPoint.lon,
        //   newPoint.lat,
        //   1000
        // ),
        destination: new Cesium.Cartesian3(new_x, new_y, new_z),
        orientation: {
          //方向、俯视和仰角
          heading: newHeading,
          pitch: viewer.camera.pitch,
        },
      });
    }
    return {
      backToOrgin,
      lockView,
      rotateLeft,
    };
  },
};
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
html,
body,
#cesiumContainer {
  width: 95%;
  height: 90%;
}
</style>
