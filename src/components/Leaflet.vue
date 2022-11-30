<template>
    <div id="map-container"></div>
    <!-- <svg id="legend" height=19 width=18></svg> -->
  </template>

<script>
import "leaflet/dist/leaflet.css";
import L from "leaflet";
import "leaflet-feature-legend"
import coordinates from '../data/coordinates.json'
import * as d3 from "d3";
import * as d3ToPng from 'd3-svg-to-png';

export default {
  name: "LeafletVue",
  data() {
    return {
      countryArray: [],
      countryMap: {},
    }
  },
  props: {
    myData: Array
  },
  mounted() {
    this.countryArray = this.preProcessCountry(this.myData);
    this.countryArray.sort(function(a, b){return b.avg_happiness_score - a.avg_happiness_score})
    this.assignRanking(this.countryArray);
    this.initializeLeafletMap();
  },
  methods: {
    assignRanking(arr) {
      arr.forEach((a, i) => {
        this.countryMap[a.country] = i + 1;
      });
    },

    preProcessCountry(arr) {
      var countryMap = new Map();

      arr.forEach(d => {
          if (!countryMap.has(d.Country)) {
          countryMap.set(d.Country, [d]);
          }
          else {
          var temp = countryMap.get(d.Country);
          temp.push(d);
          countryMap.set(d.Country, temp);
          }
      });

      var averagedCountries = []
      for (let [key, value] of countryMap) {
          var avg_happiness_score = 0;
          var avg_gdp_per_capita = 0;
          var avg_family = 0;
          var avg_health = 0;
          var avg_freedom = 0;
          var avg_generosity = 0;
          var avg_government_trust = 0;
          var avg_dystopia_residual = 0;
          var avg_social_support = 0;
          var avg_cpi_score = 0;

          var happiness_score_count = 0;
          var gdp_per_capita_count = 0;
          var family_count = 0;
          var health_count = 0;
          var freedom_count = 0;
          var generosity_count = 0;
          var government_trust_count = 0;
          var dystopia_residual_count = 0;
          var social_support_count = 0;
          var cpi_score_count = 0;

          var country = {}
          value.forEach(d => {
          country['country'] = d.Country;
          country['continent'] = d.continent;
          if (d.happiness_score != 0) {
              avg_happiness_score += d.happiness_score;
              happiness_score_count += 1;
          }
          if (d.gdp_per_capita != 0) {
              avg_gdp_per_capita +=  d.gdp_per_capita;
              gdp_per_capita_count += 1;
          }
          if (d.family != 0) {
              avg_family += d.family;
              family_count += 1;
          }
          if (d.health != 0) {
              avg_health += d.health;
              health_count += 1;
          }
          if (d.freedom != 0) {
              avg_freedom += d.freedom;
              freedom_count += 1;
          }
          if (d.generosity != 0) {
              avg_generosity += d.generosity;
              generosity_count += 1;
          }
          if (d.government_trust != 0) {
              avg_government_trust += d.government_trust;
              government_trust_count += 1;
          }
          if (d.dystopia_residual != 0) {
              avg_dystopia_residual += d.dystopia_residual;
              dystopia_residual_count += 1;
          }
          if (d.social_support != 0) {
              avg_social_support += d.social_support;
              social_support_count += 1;
          }
          if (d.cpi_score != 0) {
              avg_cpi_score += d.cpi_score;
              cpi_score_count += 1;
          }

          });
          country.avg_happiness_score = avg_happiness_score / happiness_score_count;
          country.avg_gdp_per_capita = avg_gdp_per_capita / gdp_per_capita_count;
          country.avg_family = avg_family / family_count;
          country.avg_health = avg_health / health_count;
          country.avg_freedom = avg_freedom / freedom_count;
          country.avg_generosity = avg_generosity / generosity_count;
          country.avg_government_trust = avg_government_trust / government_trust_count;
          country.avg_dystopia_residual = avg_dystopia_residual / dystopia_residual_count;
          country.avg_social_support = avg_social_support / social_support_count;
          country.avg_cpi_score = avg_cpi_score / cpi_score_count;
          averagedCountries.push(country)
      }

      return averagedCountries
    },


    async initializeLeafletMap() {
        let map = L.map("map-container", {
          minZoom: 2,
          // maxZoom: 4,
          // zoomSnap: 0.05
        }).setView([45, 5], 2);

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


    // *****************************************************************************************
      var continentLayer = new L.FeatureGroup();
      var countryLayer = new L.FeatureGroup();

    // *****************************************************************************************   
      const continents = [
        {name: "Africa", coordinates : [6.303731, 23.487895]},
        {name: "Asia", coordinates : [46.800059, 116.71875]},
        {name: "South America", coordinates : [-10.258548, -58.099121]},
        {name: "Europe", coordinates : [41.902277, 21.621094]},
        {name: "North America", coordinates : [30.600094, -96.152344]},
        {name: "Australia", coordinates : [-21.029250, 133.020550]}
    ]
      
    continents.forEach(c => {
      const nameWithoutSpaces = c.name.replace(/\s/g, "");
      var glyph = L.icon({
        iconUrl: `src/assets/glyph/continents/${nameWithoutSpaces}-glyph.png`,
        iconSize: [150, 175],
        //iconAnchor: [0, 0],
        popupAnchor: [0, -10],
      });
      var photoImg = `<h3>${c.name}</h3><img src=src/assets/bar/continents/${nameWithoutSpaces}-bar.png height="250px" width="250px"/>`;
      continentLayer.addLayer(L.marker(c.coordinates, {icon: glyph}).bindPopup(photoImg));
    });
    map.addLayer(continentLayer)

      const response = await fetch('https://restcountries.com/v3.1/all');
      const data = await response.json();
      data.forEach(d => {
            if (coordinates.hasOwnProperty(d.name.common)) {
                const nameWithoutSpaces = d.name.common.replace(/\s/g, "");
                var glyph = L.icon({
                  iconUrl: `src/assets/glyph/countries/${nameWithoutSpaces}-glyph.png`,
                  iconSize: [150, 175],
                  //iconAnchor: [0, 0],
                  popupAnchor: [0, -10],
                });
                const ranking = this.countryMap[d.name.common];
                var photoImg = `<h3>${d.name.common}, Rank #${ranking}</h3><img src=src/assets/bar/countries/${nameWithoutSpaces}-bar.png height="250px" width="250px"/>`;
                coordinates[d.name.common] = [d.latlng[0], d.latlng[1]];
                countryLayer.addLayer(L.marker(coordinates[d.name.common], {icon: glyph}).bindPopup(photoImg));
            }
          });
    // *****************************************************************************************
    // select the svg area
      // var svg = d3.select("#legend")

      // // // Handmade legend
      // svg.append("circle").attr("cx",10).attr("cy",11).attr("r", 6).style("fill", "#FF69B4")
      // svg.append("circle").attr("cx",10).attr("cy",11).attr("r", 6).style("fill", "#808080")
      // svg.append("circle").attr("cx",200).attr("cy",160).attr("r", 6).style("fill", "#006400")
      // svg.append("circle").attr("cx",200).attr("cy",190).attr("r", 6).style("fill", "#00008B")
      // svg.append("circle").attr("cx",200).attr("cy",220).attr("r", 6).style("fill", "#DAA520")
      // svg.append("circle").attr("cx",200).attr("cy",250).attr("r", 6).style("fill", "#808080")
      // svg.append("text").attr("x", 220).attr("y", 130).text("Family").style("font-size", "15px").attr("alignment-baseline","middle")
      // svg.append("text").attr("x", 220).attr("y", 160).text("Health").style("font-size", "15px").attr("alignment-baseline","middle")
      // svg.append("text").attr("x", 220).attr("y", 190).text("Freedom").style("font-size", "15px").attr("alignment-baseline","middle")
      // svg.append("text").attr("x", 220).attr("y", 220).text("GDP_per_capita").style("font-size", "15px").attr("alignment-baseline","middle")
      // svg.append("text").attr("x", 220).attr("y", 250).text("Government_trust").style("font-size", "15px").attr("alignment-baseline","middle")
      
      // d3ToPng('#legend', 'grey', {
      //   quality: 1,
      //   // background: "white"
      // })



      // Leaflet icons are used to create the legend
      const pinkIcon = L.icon({ iconUrl: 'src/assets/legend/pink.png', iconSize: [15, 15] });
      const greenIcon = L.icon({ iconUrl: 'src/assets/legend/green.png', iconSize: [15, 15] });
      const blueIcon = L.icon({ iconUrl: 'src/assets/legend/blue.png', iconSize: [15, 15] });
      const yellowIcon = L.icon({ iconUrl: 'src/assets/legend/yellow.png', iconSize: [15, 15] });
      const greyIcon = L.icon({ iconUrl: 'src/assets/legend/grey.png', iconSize: [15, 15] });
      const legend = L.control.featureLegend({
          position: "topright",
          title: "Glyph Legend",
          items: {
              "Family": { icon: pinkIcon },
              "Health": { icon: greenIcon },
              "Freedom": { icon: blueIcon },
              "GDP_per_capita": { icon: yellowIcon },
              "Government_trust": { icon: greyIcon }
          }
      }).addTo(map);

      // *****************************************************************************************

      map.on('zoomend', function() {
          if (map.getZoom() < 4){
            map.addLayer(continentLayer);
            map.removeLayer(countryLayer);
          }
          else {
            map.removeLayer(continentLayer);
            map.addLayer(countryLayer);
          }
      });

      // *****************************************************************************************
    }
  },
}
</script>

<style scoped>

#map-container {
  width: 92vw;
  height: 88vh;
  margin-left: 4%;
  margin-top: 2%
}

/* .leaflet-control-feature-legend.leaflet-control {
  font: icon;
  padding-right: 5px;
} */

</style>

<!-- // const glyph = L.icon({
  //   iconUrl: 'src/bar_charts/glyph.png',
  //   iconSize: [70, 70],
  //   iconAnchor: [20, 20],
  //   popupAnchor: [0, -10],
  // });

  // const afghanGlyph = L.icon({
  //   iconUrl: 'src/bar_charts/Afghanistan-glyph.png',
  //   iconSize: [70, 70],
  //   iconAnchor: [20, 20],
  //   popupAnchor: [0, -10],
  // });

  // const norwayGlyph = L.icon({
  //   iconUrl: 'src/bar_charts/Norway-glyph.png',
  //   iconSize: [70, 70],
  //   iconAnchor: [20, 20],
  //   popupAnchor: [0, -10],
  // });

  // const afghanGlyphBig = L.icon({
  //   iconUrl: 'src/bar_charts/Afghanistan-glyph.png',
  //   iconSize: [100, 100],
  //   iconAnchor: [20, 20],
  //   popupAnchor: [0, -10],
  // });

  // const norwayGlyphBig = L.icon({
  //   iconUrl: 'src/bar_charts/Norway-glyph.png',
  //   iconSize: [100, 100],
  //   iconAnchor: [20, 20],
  //   popupAnchor: [0, -10],
  // });

  // const afghanGlyphBigger = L.icon({
  //   iconUrl: 'src/bar_charts/Afghanistan-glyph.png',
  //   iconSize: [130, 130],
  //   iconAnchor: [20, 20],
  //   popupAnchor: [0, -10],
  // });

  // const norwayGlyphBigger = L.icon({
  //   iconUrl: 'src/bar_charts/Norway-glyph.png',
  //   iconSize: [130, 130],
  //   iconAnchor: [20, 20],
  //   popupAnchor: [0, -10],
  // });


//     const afghanMarker = L.marker([35.047343, 65.130580], {icon: afghanGlyph}).addTo(map);
//     const norwayMarker = L.marker([62.394931, 7.790979], {icon: norwayGlyph}).addTo(map);

//     const afghanBarChart = `<h3>Afghanistan</h3><img src="src/bar_charts/Afghanistan-bar.png" height="200px" width="200px"/>`
//     afghanMarker.bindPopup(afghanBarChart);

//     const norwayBarChart = `<h3>Norway</h3><img src="src/bar_charts/Norway-bar.png" height="200px" width="200px"/>`
//     norwayMarker.bindPopup(norwayBarChart);

//     var afghanLayer = new L.GeoJSON();
//     var norwayLayer = new L.GeoJSON();
//     afghanLayer.addLayer(afghanMarker);
//     norwayLayer.addLayer(norwayMarker);
//     const defaultIconSize = [70, 70]
//     const zoomedInIconSize = [100, 100]
//     const maxIconSize = [130, 130]

//     map.on('zoomend', function() {
//     var currentZoom = map.getZoom();
//     if (currentZoom === 2 || currentZoom === 3) {
//         afghanLayer.eachLayer(function(layer) {
//           return layer.setIcon(afghanGlyph);
//         });
//         norwayLayer.eachLayer(function(layer) {
//           return layer.setIcon(norwayGlyph);
//         });
//     }
//     else if (currentZoom === 4 || currentZoom === 5) {
//       afghanLayer.eachLayer(function(layer) {
//           return layer.setIcon(afghanGlyphBig);
//         });
//         norwayLayer.eachLayer(function(layer) {
//           return layer.setIcon(norwayGlyphBig);
//         });
//     } else {
//       afghanLayer.eachLayer(function(layer) {
//           return layer.setIcon(afghanGlyphBigger);
//         });
//         norwayLayer.eachLayer(function(layer) {
//           return layer.setIcon(norwayGlyphBigger);
//         });
//     }
// }); -->