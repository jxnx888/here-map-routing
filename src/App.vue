<template>
  <div>
  <div style="text-align: center;margin: 20px; color:red">使用前请替换成自己的apikey</div>
  <div id="app">
    <div class="each-map">
      <p>使用起始点的方式绘制地图</p>
      <HereMapOriginDest
          :appid="appid"
          :apikey="apikey"
          :coordinate="lagLng"
          :zoom="zoom"
      />
      <p class="note">此方法的缺点是，仅知道起始点，实际路线也许和推荐的绘制的路线不一致</p>
    </div>
    <div class="each-map">
      <p>使用waypoint的方式</p>
      <HereMapWaypoint
          :appid="appid"
          :apikey="apikey"
          :coordinate="lagLng"
          :zoom="zoom"
      />
      <p class="note">此方法的缺点是，需要去使用付费的定位纠偏，否则如果定位有偏差，就会根据有偏差的点去绘制。</p>
      <p class="note">（即，代码会按照坐标依次绘制，就会造成所有的点都会通过）</p>
    </div>

  </div>
  </div>
</template>

<script>

import HereMapOriginDest from "@/components/HereMapOriginDest";
import HereMapWaypoint from "@/components/HereMapWaypoint";

export default {
  name: 'App',
  components: {
    HereMapOriginDest,
    // eslint-disable-next-line vue/no-unused-components
    HereMapWaypoint
  },
  data () {
    return {
      apikey: "cjd3ufQPD0cbrw6EVt4_RNu5CAoJOXoj46-LWmRX4qw",
      appid: "hhlTS0tWATLpCoPqarPi",
      zoom: 14,
      lagLng: [{lat: 7.9779041, lng: 98.3613595},
        {lat: 7.9784737, lng: 98.3604177},
        {lat: 7.978429, lng: 98.3604375},
        {lat: 7.978429, lng: 98.3604375},
        {lat: 7.9784379, lng: 98.3604517},
        {lat: 7.9783802, lng: 98.3605468},
        {lat: 7.9783775, lng: 98.3605479},
        {lat: 7.9808786, lng: 98.3586479},
        {lat: 7.9784648, lng: 98.3604396},
        {lat: 7.9795464, lng: 98.3620979},
        {lat: 7.9795514, lng: 98.3623861},
        {lat: 8.0153829, lng: 98.3387908},
        {lat: 8.0228943, lng: 98.3348006},
        {lat: 8.2716864, lng: 98.3414945},
        {lat: 8.0228943, lng: 98.3348006},
        {lat: 8.2716864, lng: 98.3414945},
        {lat: 8.2736892, lng: 98.3485967},
        {lat: 8.2788242, lng: 98.3617297},
        {lat: 8.3521359, lng: 98.4388148},
        {lat: 8.3660211, lng: 98.4457001},
        {lat: 8.3705372, lng: 98.4464033},
        {lat: 8.4134241, lng: 98.4610986},
        {lat: 8.4309176, lng: 98.5195579},
        {lat: 8.4457063, lng: 98.5307164},
        {lat: 8.4457108, lng: 98.5307429},
        {lat: 8.4317412, lng: 98.5183761},
        {lat: 8.4317408, lng: 98.5183599},
        {lat: 8.4317472, lng: 98.518423},
        {lat: 8.4317408, lng: 98.5183599},
        {lat: 8.4317472, lng: 98.518423}]
    };
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  flex-direction: row;
  justify-content: space-around;
}

.each-map {
  width: 45vw;
  height: 45vh;
  border: 2px solid #333;
  min-width: 360px;
}

.each-map p {
  height: 3vh;
  margin: 0;
}
.each-map .note{
  margin-top: 30px;
}
</style>
