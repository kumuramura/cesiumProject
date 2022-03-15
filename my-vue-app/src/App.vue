<template>
  <div id="cesiumContainer"></div>
  <button v-on:click="backToOrgin">回到初始视角</button>
  <button v-on:click="lockView">锁死视角</button>
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
      var gaodeMap=new Cesium.UrlTemplateImageryProvider({
        url: "https://webst02.is.autonavi.com/appmaptile?style=6&x={x}&y={y}&z={z}"
      });
      var tianMap=new Cesium.UrlTemplateImageryProvider({
        url: "https://t5.tianditu.gov.cn/DataServer?T=img_w&x={x}&y={y}&l={z}&tk=49190982fae336528563aaf761b39e67"
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
      viewer.scene.screenSpaceCameraController.enableZoom = false;
      viewer.cesiumWidget.creditContainer.style.display = "none"; //移除版权信息
      scene.skyAtmosphere.show = false; // 关闭大气层
      scene.debugShowFramesPerSecond = true; //显示帧数
      scene.screenSpaceCameraController.enableCollisionDetection = false; // 是否允许相机进入地下
      scene.mode = Cesium.SceneMode.COLUMBUS_VIEW; //默认2.5D

      //设置初始化视角
      viewer.camera.setView({
        destination: Cesium.Cartesian3.fromDegrees(
          111.695937290002,
          21.8428761847095,
          1000
        ),
        orientation: {
          //方向、俯视和仰角
          heading: Cesium.Math.toRadians(0),
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

      //缩放模型，作用不大
      const scale = Cesium.Matrix4.fromScale(
        new Cesium.Cartesian3(0.5, 0.5, 0.5),
        new Cesium.Matrix4()
      );
      model.modelMatrix = Cesium.Matrix4.multiply(
        model.modelMatrix,
        scale,
        model.modelMatrix
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
          console.log("点到了"+pick.id);
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
          111.695937290002,
          21.8428761847095,
          1800
        ),
        orientation: {
          //方向、俯视和仰角
          heading: Cesium.Math.toRadians(0),
          pitch: Cesium.Math.toRadians(-45),
        },
        duration: 5,
      });
    }
    function lockView() {
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

        console.log(viewer.camera.position.y);
      }, 800);
    }
    //每秒检测一下视角对不对，此处问题非常多，建议锁定上下视角

    return {
      backToOrgin,
      lockView,
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
