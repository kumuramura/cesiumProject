<template>
 <div id="cesiumContainer"> </div>
 <button v-on:click="BackToOrgin">回到初始视角</button>
</template>

<script setup>
import * as Cesium from 'cesium';
import {onMounted} from '@vue/runtime-core'
onMounted(()=>{
  var custom= new Cesium.ArcGisMapServerImageryProvider({
  url:'https://services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer',

  });
  const viewer= new Cesium.Viewer('cesiumContainer',{
    baseLayerPicker:false,
    imageryProvider:custom,
    timeline:false,
    navigationHelpButton:false,
    navigationInstructionsInitiallyVisible:false,
    fullscreenButton:false,
    homeButton:false,
    infoBox:false,//单击会显示信息
    selectionIndicator:false,//关闭长按出现选取方块的功能
    animation:false,
    geocoder:true,//搜索功能，暂时打开
    sceneModePicker:true,//选择场景模式，关掉，手动设为2.5D
    //imageryProvider:

  });
  var scene=viewer.scene;
  //viewer.scene.screenSpaceCameraController.enableZoom =false;
  viewer.cesiumWidget.creditContainer.style.display = "none";//移除版权信息
  scene.skyAtmosphere.show=false;    // 关闭大气层
  scene.debugShowFramesPerSecond = true;//显示帧数
  scene.screenSpaceCameraController.enableCollisionDetection = false;// 是否允许相机进入地下
  scene.mode = Cesium.SceneMode.COLUMBUS_VIEW;//默认2.5D



    window.setInterval( function(){
      if(Cesium.Math.toRadians(-75)<viewer.camera.pitch&&viewer.camera.pitch<Cesium.Math.toRadians(-35)){
           console.log("好视角")
        }
        else if(Cesium.Math.toRadians(-75)>viewer.camera.pitch){
           console.log("差视角")
           viewer.camera.flyTo({
              orientation:{//方向、俯视和仰角
              heading:Cesium.Math.toRadians(0),
              pitch:Cesium.Math.toRadians(-75),
            }
         });
        }
        else if(viewer.camera.pitch>Cesium.Math.toRadians(-35)){
            console.log("差视角")
            viewer.camera.flyTo({
              orientation:{//方向、俯视和仰角
              heading:Cesium.Math.toRadians(0),
              pitch:Cesium.Math.toRadians(-35),
            }
         });
        }
    
    }, 100);

   

 //改为右键
  scene.screenSpaceCameraController.tiltEventTypes = [
     Cesium.CameraEventType.RIGHT_DRAG, Cesium.CameraEventType.PINCH,
     { eventType: Cesium.CameraEventType.LEFT_DRAG, modifier: Cesium.KeyboardEventModifier.CTRL },
     { eventType: Cesium.CameraEventType.RIGHT_DRAG, modifier: Cesium.KeyboardEventModifier.CTRL }
     ];
  scene.screenSpaceCameraController.zoomEventTypes = [
     Cesium.CameraEventType.MIDDLE_DRAG, Cesium.CameraEventType.WHEEL, Cesium.CameraEventType.PINCH];


   viewer.camera.setView({
     destination:Cesium.Cartesian3.fromDegrees(113.318977,23.114155,1800),
     orientation:{//方向、俯视和仰角
       heading:Cesium.Math.toRadians(0),
       pitch:Cesium.Math.toRadians(-45),
     }
 })

    var modelMatrix = Cesium.Transforms.eastNorthUpToFixedFrame(
         Cesium.Cartesian3.fromDegrees(113.318977,23.114155));
    
    var model = scene.primitives.add(Cesium.Model.fromGltf({
         id:'模型1',
         url : '../src/3Dmodel/city2-draco.gltf',
         modelMatrix : modelMatrix,
         scale : 100,  //放大倍数
         incrementallyLoadTextures:true,
         })
    );
    
    const scale=Cesium.Matrix4.fromScale(new Cesium.Cartesian3(0.5,0.5,0.5),new Cesium.Matrix4)
    model.modelMatrix=Cesium.Matrix4.multiply(model.modelMatrix,scale,model.modelMatrix)
    var handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);
     handler.setInputAction(function (click) {
         var pick = viewer.scene.pick(click.position);
         if (!pick ) {
             return;
         }
         if (pick.id === undefined) {
             return;
         }
         //选中某模型   pick选中的对象
         if (Cesium.defined(pick)) {
             console.log("点到了！")
         }
     }, Cesium.ScreenSpaceEventType.LEFT_DOWN);
    

    function mode3D_play(checkbox){
        if(checkbox.checked==true){
            model.show=true;
        }else{
            model.show=false;
        }
    }


  //跳转到初始视角
  function BackToOrgin(){
        viewer.camera.flyTo({
          destination:Cesium.Cartesian3.fromDegrees(113.318977,23.114155,1800),
          orientation:{//方向、俯视和仰角
            heading:Cesium.Math.toRadians(0),
            pitch:Cesium.Math.toRadians(-45),
            }
         });
      }
 }
)

  
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
html,body,#cesiumContainer{
  width:95%;
  height:90%;
}


</style>
