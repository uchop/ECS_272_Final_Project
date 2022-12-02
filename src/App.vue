<template>
  <div id="flex-container">
    <TitleVue/>
    <PreprocessingVue/>
    <GlyphVue/>
    <div id="leaflet"><LeafletVue v-if="dataExists" :myData="myCsvData"/></div>
    <div id="overlay"><OverlayVue v-if="dataExists" :myData="myCsvData"/></div>
    <div id="paracords"><ParallelCoordinatesVue v-if="dataExists" :myData="myCsvData"/></div>
</div>
  
  <!-- Add this one in ONLY if you want to generate PNGs, else keep commented out-->
  <!-- <PNGGeneratorVue v-if="dataExists" :myData="myCsvData" :myRegionalData="myRegionalData"/>-->
</template>

<script>

import * as d3 from 'd3';
import csvPath from './assets/data/data.csv?raw';
import regionalCsvPath from './assets/data/regions.csv?raw'
import TitleVue from "./components/Title.vue";
import PreprocessingVue from "./components/Preprocessing.vue";
import GlyphVue from "./components/Glyph.vue"
import LeafletVue from './components/Leaflet.vue';
import OverlayVue from './components/Overlay.vue';
import ParallelCoordinatesVue from './components/ParallelCoordinates.vue'
import PNGGeneratorVue from './components/PNGGenerator.vue';


export default {
  // Properties returned from data() become reactive state
  // and will be exposed on `this`.
  data() {
    return {
      dataExists: false,
      myCsvData: [],
      myRegionalData: [],
    }
  },
  components: {
    TitleVue,
    PreprocessingVue,
    GlyphVue,
    LeafletVue,
    OverlayVue,
    ParallelCoordinatesVue,
    PNGGeneratorVue,
  },
  created(){
    this.drawFromCSV();
  },
  // Methods are functions that mutate state and trigger updates.
  // They can be bound as event listeners in templates.
  methods: {
    async drawFromCSV(){
      //async method
      this.myCsvData = d3.csvParse(await csvPath, d3.autoType)
      this.myRegionalData = d3.csvParse(await regionalCsvPath, d3.autoType)
      this.dataExists = true;
      // console.log(this.myCsvData)
      // d3.csv(csvPath.text()).then((data) =>{
      //           console.log(data.length);
      //           //console.log(data);
      //           this.dataExists = true; // updates the v-if to conditionally show the barchart only if our data is here.

      //           this.myCsvData = data; // updates the prop value to be the recieved data, which we hand in to our bar-chart
      // });
      /*
      d3.csv(csvPath).then((data) => {
                // array of objects
                console.log(data.length);
                console.log(data);
                this.dataExists = true; // updates the v-if to conditionally show the barchart only if our data is here.

                this.myCsvData = data; // updates the prop value to be the recieved data, which we hand in to our bar-chart

      });*/
    }

  },

  // Lifecycle hooks are called at different stages
  // of a component's lifecycle.
  // This function will be called when the component is mounted.
  mounted() {
    
  }
}
</script>
<style scoped>

#flex-container {
  display: flex;
  width: 100%;
  height: 100%;
  flex-direction: column;
  justify-content: center;
  /* Used to enable scrolling */
  /* height: 2000px; */
  background: linear-gradient(55deg, #0fb8ad 0%, #1fc8db 51%, lightgoldenrodyellow 85%);
}

/* #leaflet {
  background-color: lightblue;
}

#overlay {
  background-color: #0fb8ad;
}

#paracords {
  background-color: lightgoldenrodyellow;
} */

  /* background-color: lightgoldenrodyellow; */

</style>
















<!-- <script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
import LeafletVue from './components/Leaflet.vue';
</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />
      <LeafletVue msg= "Testing props"/>
    </div>
  </header>

  <main>
    <TheWelcome />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style> -->
