<template>
  <div id="mapContainer" ref="hereMap"></div>
</template>

<script>

import H from "@here/maps-api-for-javascript";

export default {
  name: 'App',
  props: {
    apikey: String,
    appid: String,
    zoom: Number,
    coordinate: {},
  },
  data () {
    return {
      map: null,
      platform: null,
    };
  },
  async mounted () {
    // Initialize the platform object:
    const platform = new H.service.Platform({
      apikey: this.apikey
    });
    this.platform = platform;
    this.initializeHereMap();
  },
  methods: {
    initializeHereMap () { // rendering map
      if (!this.map) {
        const mapContainer = this.$refs.hereMap;
        // Obtain the default map types from the platform object
        var maptypes = this.platform.createDefaultLayers();

        // Instantiate (and display) a map object:
        // 创建地图实例
        this.map = new H.Map(mapContainer, maptypes.vector.normal.map, {
          zoom: this.zoom,
          pixelRatio: window.devicePixelRatio || 1,
          center: this.coordinate[0]
        });

        const markerBegin = new H.map.Marker(
            this.coordinate[0]
        );
        this.map.addObject(markerBegin);

        addEventListener("resize", () => this.map.getViewPort().resize());

        // add behavior control
        new H.mapevents.Behavior(new H.mapevents.MapEvents(this.map));

        // add UI
        // 创建ui实例
        H.ui.UI.createDefault(this.map, maptypes);
        // End rendering the initial map


        // eslint-disable-next-line no-unused-vars
        let onResult = (result) => {
          // ensure that at least one route was found
          if (result.response.route.length > 0) {
            // Create a linestring to use as a point source for the route line
            let linestring = new H.geo.LineString();
            result.response.route[0].shape.forEach((point) => {
              let [lat, lng] = point.split(',');
              linestring.pushPoint({lat: lat, lng: lng})
            });
            let polyline = new H.map.Polyline(
                linestring,
                {
                  style: {strokeColor: 'blue', lineWidth: 3}

                }
            );
            this.map.addObject(polyline)
            this.map.getViewModel().setLookAtData({bounds: polyline.getBoundingBox()});
          }
        }

        const pointsCoordinate = {};
        for (var i in this.coordinate) {
          pointsCoordinate[`waypoint${i}`] = `${this.coordinate[i].lat},${this.coordinate[i].lng}`
        }
        console.log(pointsCoordinate, 'pointsCoordinate')
        var routingParameters = {
          mode: 'fastest;car;traffic:enabled',
          representation: 'display',
          ...pointsCoordinate,
          return: 'polyline,turnByTurnActions,actions,instructions,travelSummary'
        };

        var router = this.platform.getRoutingService();

        // Call calculateRoute() with the routing parameters,
        // the callback and an error callback function (called if a
        // communication error occurs):

        router.calculateRoute(routingParameters,
            data => {
              onResult(data)
              console.log("success: ")
              console.log(data)
            },
            error => {
              console.log(error.message, ':: error.message');
            });
      }
    },
  }
}
</script>

<style>
#mapContainer {
  width: 100%;
  height: 42vh;
  min-width: 360px;
  text-align: center;
  margin: 0;
  background-color: #ccc;
}
</style>
