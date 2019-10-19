<template>
  <div>
    <h1>高德地图</h1>
    <div id="container"></div>
  </div>
</template>

<script>
export default {
  mounted() {
    window.onLoad = function() {
      var map = new AMap.Map("container");
      var map = new AMap.Map("container", {
        resizeEnable: true,
        center: [116.397428, 39.90923],
        zoom: 13
      }); //实时路况图层 var trafficLayer = new AMap.TileLayer.Traffic({ zIndex: 10 }); map.add(trafficLayer);//添加图层到地图
      var marker = new AMap.Marker({
        position: [45, -100]
      });
      map.add(marker); //添加到地图
    };
    var url =
      "https://webapi.amap.com/maps?v=1.4.15&key=	3d3219938659e799b91ece1e3bfd6bce&callback=onLoad";
    var jsapi = document.createElement("script");
    jsapi.charset = "utf-8";
    jsapi.src = url;
    document.head.appendChild(jsapi);
  }
};
</script>

<style>
#container {
  width: 100%;
  height: 1000px;
}
</style>