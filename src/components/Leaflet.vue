<template>
    <div id="map-container"></div>
  </template>

<script>
import "leaflet/dist/leaflet.css";
import L from "leaflet";
import BarChartVue from "./BarChart.vue";
import coordinates from '../data/coordinates.json'

export default {
  name: "LeafletVue",
  data() {
    return {
    //   count: 0
    // map: null
    }
  },
  components: {
    BarChartVue
  },
  props: {
    msg: String
  },

  methods: {
    async initializeLeafletMap() {
        let map = L.map("map-container", {
          minZoom: 2,
          // maxZoom: 4,
          // zoomSnap: 0.05
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

      const glyph = L.icon({
        iconUrl: 'src/bar_charts/glyph.png',
        iconSize: [70, 70],
        iconAnchor: [20, 20],
        popupAnchor: [0, -10],
      });

      // const photoImg = '<h3>Test Title</h3><img src="src/bar_charts/Norway.png" height="175px" width="175px"/>'

      // const marker = L.marker([42.3377,-71.0908], {icon: testIcon});
      // const marker = L.marker([64.5783089, 17.888237], {icon: testIcon});
      // marker.bindPopup("<b>School of Journalism</b><br>Northeastern University."); // "bind" basically means "connect"
      // marker.bindPopup(photoImg); // "bind" basically means "connect"

      const marker2 = L.marker([-17.978733, -38.320312], {icon: glyph});
      marker2.bindPopup("<b>Cool place</b><br>for cool cats");

      // make this async await and use arrow function
      var continentLayer = new L.FeatureGroup();
      var countryLayer = new L.FeatureGroup();
      // continentLayer.addLayer(marker);
      // countryLayer.addLayer(marker2);
      
      const continents = [
        {name: "Africa", coordinates : [6.303731, 23.487895]},
        {name: "Asia", coordinates : [46.800059, 116.71875]},
        {name: "South America", coordinates : [-10.258548, -58.099121]},
        {name: "Europe", coordinates : [41.902277, 21.621094]},
        {name: "North America", coordinates : [30.600094, -96.152344]},
        {name: "Australia", coordinates : [-21.029250, 133.020550]}
    ]
      
    continents.forEach(c => {
      var photoImg = `<h3>${c.name}</h3><img src="src/bar_charts/Norway.png" height="175px" width="175px"/>`;
      continentLayer.addLayer(L.marker(c.coordinates, {icon: testIcon}).bindPopup(photoImg));
    });
    map.addLayer(continentLayer)

      const response = await fetch('https://restcountries.com/v3.1/all');
      const data = await response.json();
      data.forEach(d => {
            if (coordinates.hasOwnProperty(d.name.common)) {
                var photoImg = `<h3>${d.name.common}</h3><img src="src/bar_charts/Norway.png" height="175px" width="175px"/>`;
                coordinates[d.name.common] = [d.latlng[0], d.latlng[1]];
                countryLayer.addLayer(L.marker(coordinates[d.name.common], {icon: glyph}).bindPopup(photoImg));
            }
          });
          

      // const fetchData = async () => {
      //     return (await fetch('https://restcountries.com/v3.1/all')).json();
      // }
      // const data = async () => (await (await fetch('https://restcountries.com/v3.1/all')).json())
      // console.log(json);

      // console.log('HELLO');

      // var continentLayer = new L.FeatureGroup();
      // var countryLayer = new L.FeatureGroup();
      // continentLayer.addLayer(marker);
      // countryLayer.addLayer(marker2);
      // map.addLayer(continentLayer)

      // this.make132FreakingMarkers();

      // fetch('https://restcountries.com/v3.1/all')
      //   .then((response) => response.json())
      //   .then((data) => {
      //     data.forEach(d => {
      //       if (coordinates.hasOwnProperty(d.name.common)) {
      //           coordinates[d.name.common] = [d.latlng[0], d.latlng[1]];
      //           L.marker(coordinates[d.name.common], {icon: glyph}).bindPopup(photoImg);
      //       }
      //     });
      //     console.log(coordinates)
      //   });

      // var continentLayer = new L.FeatureGroup();
      // var countryLayer = new L.FeatureGroup();
      // continentLayer.addLayer(marker);
      // countryLayer.addLayer(marker2);
      // map.addLayer(continentLayer)

      // console.log(coordinates);
      
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
    // this.make132FreakingMarkers();
    this.initializeLeafletMap();
    // console.log(`The initial count is ${this.count}.`)
    // console.log(`The initial count is ${this.msg}.`)
  }
}
</script>

<style scoped>
#map-container {
  width: 92vw;
  height: 92vh;
  margin-left: 4%;
  margin-top: 2%
}
</style>
