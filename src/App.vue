<template>
  <LeafletVue msg= "Testing props"/>
  <ParallelCoordinatesVue v-if="dataExists" :myData="myCsvData"/>
  
  <!-- Add this one in ONLY if you want to generate PNGs, else keep commented out-->
  <!-- <PNGGeneratorVue v-if="dataExists" :myData="myCsvData"/> -->
</template>

<script>

import * as d3 from 'd3';
import csvPath from './assets/data/data.csv?raw';
import LeafletVue from './components/Leaflet.vue';
import ParallelCoordinatesVue from './components/ParallelCoordinates.vue'
import PNGGeneratorVue from './components/PNGGenerator.vue';


export default {
  // Properties returned from data() become reactive state
  // and will be exposed on `this`.
  data() {
    return {
      dataExists: false,
      myCsvData: [],
    }
  },
  components: {
    LeafletVue,
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
      this.dataExists = true;
      console.log(this.myCsvData)
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
