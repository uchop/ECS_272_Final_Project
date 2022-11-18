<template>
    <div id="map-container"></div>

  </template>

<script>
import "leaflet/dist/leaflet.css";
import L from "leaflet";

export default {
  name: "LeafletVue",
  data() {
    return {
    //   count: 0
    map: null
    }
  },
  props: {
    msg: String
  },

  methods: {

    initializeLeafletMap() {
        this.map = L.map("map-container").setView([50, 10], 2);

        // Commented line below is for using default map with mixed country languages
        // L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        //   attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        //   }).addTo(this.map); //This adds the tile layer to the map. The map dynamically loads tiles based on current view the experience smooth and seamless. Make sure to add attribution to your map. osm = open street map. L = layer
        L.tileLayer(
        "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token={accessToken}",
        {
          attribution:
            'Map data © <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, <a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery (c) <a href="https://www.mapbox.com/">Mapbox</a>',
          maxZoom: 18,
          id: "mapbox/streets-v11",
          accessToken:
            "pk.eyJ1IjoiYWJpZGlzaGFqaWEiLCJhIjoiY2l3aDFiMG96MDB4eDJva2l6czN3MDN0ZSJ9.p9SUzPUBrCbH7RQLZ4W4lQ",
        }
      ).addTo(this.map);
      this.map.setMaxBounds(this.map.getBounds());
    }
  },

  // Lifecycle hooks are called at different stages
  // of a component's lifecycle.
  // This function will be called when the component is mounted.
  mounted() {
    this.initializeLeafletMap();
    // console.log(`The initial count is ${this.count}.`)
    // console.log(`The initial count is ${this.msg}.`)
  }
}
</script>

<style scoped>
#map-container {
  width: 90vw;
  height: 90vh;
  margin-left: 4%;
  margin-top: 2%
}
</style>
