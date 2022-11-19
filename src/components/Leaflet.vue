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
    // map: null
    }
  },
  props: {
    msg: String
  },

  methods: {

    initializeLeafletMap() {
        let map = L.map("map-container", {
          minZoom: 2,
          maxZoom: 4,
          zoomSnap: 0.05
        }).setView([25, 10], 2);

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
      ).addTo(map);
      map.setMaxBounds(map.getBounds());

      const testIcon = L.icon({
        iconUrl: 'https://web.northeastern.edu/mihc/images/NortheasternLogo.png',
        iconSize: [40, 40],
        iconAnchor: [20, 20],
        popupAnchor: [0, -10],
      });

      const coolCat = L.icon({
        iconUrl: 'https://img.pngio.com/download-cool-cat-png-image-library-library-coolcat-boogie-cool-cat-png-352_352.png',
        iconSize: [40, 40],
        iconAnchor: [20, 20],
        popupAnchor: [0, -10],
      });

      // const photoImg = '<img src="https://static.pexels.com/photos/189349/pexels-photo-189349.jpeg" height="150px" width="150px"/>';
      const photoImg = '<img src="src/bar_charts/Norway.png" height="175px" width="175px"/>'

      // const marker = L.marker([42.3377,-71.0908], {icon: testIcon});
      const marker = L.marker([64.5783089, 17.888237], {icon: testIcon});
      // marker.bindPopup("<b>School of Journalism</b><br>Northeastern University."); // "bind" basically means "connect"
      marker.bindPopup(photoImg); // "bind" basically means "connect"

      const marker2 = L.marker([-17.978733, -38.320312], {icon: coolCat});
      marker2.bindPopup("<b>Cool place</b><br>for cool cats");

      var continentLayer = new L.FeatureGroup();
      var countryLayer = new L.FeatureGroup();
      continentLayer.addLayer(marker);
      countryLayer.addLayer(marker2);
      map.addLayer(continentLayer)
      
      map.on('zoomend', function() {
          if (map.getZoom() === 2){
            map.addLayer(continentLayer);
            map.removeLayer(countryLayer);
          }
          else {
            map.removeLayer(continentLayer);
            map.addLayer(countryLayer);
          }
      });



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
