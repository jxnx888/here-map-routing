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
    coordinate: Array,
  },
  data () {
    return {
      map: null,
      platform: null,
    };
  },
  async mounted () {
    // Initialize the platform object:
    this.platform = new H.service.Platform({
      apikey: this.apikey
    });
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
          if (result.routes.length) {
            result.routes[0].sections.forEach((section) => {
              // Create a linestring to use as a point source for the route line
              let linestring = H.geo.LineString.fromFlexiblePolyline(section.polyline);

              // Create a polyline to display the route:
              let routeLine = new H.map.Polyline(linestring, {
                style: {strokeColor: 'blue', lineWidth: 3}
              });

              // Create a marker for the start point:
              let startMarker = new H.map.Marker(section.departure.place.location);

              // Create a marker for the end point:
              let endMarker = new H.map.Marker(section.arrival.place.location);

              // Add the route polyline and the two markers to the map:
              this.map.addObjects([routeLine, startMarker, endMarker]);

              // Set the map's viewport to make the whole route visible:
              this.map.getViewModel().setLookAtData({bounds: routeLine.getBoundingBox()});
            });
          }
        }

        var routingParameters = {
          routingMode: 'fast',
          transportMode: 'car',
          passThrough: true,
          // The start point of the route:
          origin: `${this.coordinate[0].lat},${this.coordinate[0].lng}`,
          // The end point of the route:
          destination: `${this.coordinate[this.coordinate.length - 1].lat},${this.coordinate[this.coordinate.length - 1].lng}`,
          // Include the route shape in the response
          return: "polyline,summary",
        };

        // Get an instance of the routing service version 8:
        var router = this.platform.getRoutingService(null, 8);

        // Call calculateRoute() with the routing parameters,
        // the callback and an error callback function (called if a
        // communication error occurs):

        router.calculateRoute(routingParameters,
            data => {
              onResult(data)
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
