<template>
    <div id="barChart"></div>
  </template>
  
  <script>

  import * as d3 from "d3";
  import * as d3ToPng from "d3-svg-to-png"
  // const d3ToPng = require('d3-svg-to-png');

  export default {
    // Properties returned from data() become reactive state
    // and will be exposed on `this`.
    data() {
      return {
        // count: 0
      }
    },
    // Methods are functions that mutate state and trigger updates.
    // They can be bound as event listeners in templates.
    methods: {
        drawBarChart() {
            // set the dimensions and margins of the graph
            const margin = {top: 15, right: 30, bottom: 200, left: 110},
                width = 460 - margin.left - margin.right,
                height = 450 - margin.top - margin.bottom;

            // append the svg object to the body of the page
            const svg = d3.select("#barChart")
            .append("svg")
                .attr("width", width + margin.left + margin.right)
                .attr("height", height + margin.top + margin.bottom)
                .attr("class", 'bar')
            .append("g")
                .attr("class", 'transform')
                .attr("transform", `translate(${margin.left},${margin.top})`);

            // select country
            const data = [
                {name: "gdp_per_capita", value: 0.717327489980916},
                {name: "family", value: 0.8260768195381246},
                {name: "health", value: 0.7850917911189015},
                {name: "freedom", value: 0.8886840402854513},
                {name: "government_trust", value: 0.6504244058511955}
                ]
            // console.log(data)
            
            // X axis
            const x = d3.scaleBand()
            .range([ 0, width ])
            .domain(data.map(d => d.name))
            .padding(0.2);
            svg.append("g")
            .style("font-size","30px")
            .attr("transform", "translate(0," + height + ")")
            .call(d3.axisBottom(x))
            .selectAll("text")
                .attr("transform", "translate(-10,0)rotate(-45)")
                .style("text-anchor", "end");

            // Add Y axis
            const y = d3.scaleLinear()
            // .domain([0, 13000])
            .domain([0, d3.max(data, function(d) { return +d.value + 0.1; })])
            .range([ height, 0]);
            svg.append("g")
            .style("font-size","20px")
            .call(d3.axisLeft(y));
            

            // Bars
            svg.selectAll("mybar")
            .data(data)
            .join("rect")
                .attr("x", d => x(d.name))
                .attr("width", x.bandwidth())
                .attr("fill", "#69b3a2")
                // no bar at the beginning thus:
                .attr("height", d => height - y(0)) // always equal to 0
                .attr("y", d => y(0))

            // Animation
            svg.selectAll("rect")
            .transition()
            .duration(800)
            .attr("y", d => y(d.value))
            .attr("height", d => height - y(d.value))
            .delay((d,i) => {return i*100})

            // d3ToPng(".bar", 'Norway_exported', {
            //   background: 'white',
            //   format: 'jpeg'
            // });
            return svg.node();
        },
  
    },
  
    // Lifecycle hooks are called at different stages
    // of a component's lifecycle.
    // This function will be called when the component is mounted.
    mounted() {
      this.drawBarChart();
    }
  }
  </script>