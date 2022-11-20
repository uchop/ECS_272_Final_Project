<template>
    <div id="parallel-coordinates-container"></div>
</template>

<script>
    import * as d3 from "d3";
    import _ from "lodash";
    import * as d3ToPng from 'd3-svg-to-png';

    export default{
        name: "ParallelCoordinatesVue",
        setup() {

        },
        data(){
            return {
                countryMap: [],
                bottom5Countries: [],
                top5Countries: [],
                barChartData:[],

            }
        },
        props:{
            myData: Array,
        },
        mounted() {

            // preprocess the data
            this.countryMap = this.normalizeData(this.myData);
            this.countryMap = this.preProcessCountry(this.countryMap);

            // get the top 5 and bottom 5
            // top five and bottom five countries
            this.countryMap.sort(function(a, b){return a.avg_happiness_score - b.avg_happiness_score})
            this.bottom5Countries = JSON.parse(JSON.stringify(this.countryMap)).splice(0, 5);
            this.top5Countries = JSON.parse(JSON.stringify(this.countryMap)).splice(-5);

            this.init("#parallel-coordinates-container");


        },
        methods: {
            normalizeData(arr){
                var max_happiness_score = 0;
                var max_gdp_per_capita = 0;
                var max_family = 0;
                var max_health = 0;
                var max_freedom = 0;
                var max_generosity = 0;
                var max_government_trust = 0;
                var max_dystopia_residual = 0;
                var max_social_support = 0;
                var max_cpi_score = 0;


                arr.forEach(d => {
                    max_happiness_score = Math.max(max_happiness_score, d.happiness_score);
                    max_gdp_per_capita = Math.max(max_gdp_per_capita, d.gdp_per_capita);
                    max_family = Math.max(max_family, d.family);
                    max_health = Math.max(max_health, d.health);
                    max_freedom = Math.max(max_freedom, d.freedom);
                    max_generosity = Math.max(max_generosity, d.generosity);
                    max_government_trust = Math.max(max_government_trust, d.government_trust);
                    max_dystopia_residual = Math.max(max_dystopia_residual, d.dystopia_residual);
                    max_social_support = Math.max(max_social_support, d.social_support);
                    max_cpi_score = Math.max(max_cpi_score, d.cpi_score);

                });

                // normalize data
                var countries = []
                arr.forEach(d => {
                    var country = {}
                    country.Country = d.Country;
                    country.continent = d.continent;
                    country.happiness_score = d.happiness_score / max_happiness_score;
                    country.gdp_per_capita = d.gdp_per_capita / max_gdp_per_capita;
                    country.family = d.family / max_family;
                    country.health = d.health / max_health;
                    country.freedom = d.freedom / max_freedom;
                    country.generosity = d.generosity / max_generosity;
                    country.government_trust = d.government_trust / max_government_trust;
                    country.dystopia_residual = d.dystopia_residual / max_dystopia_residual;
                    country.social_support = d.social_support / max_social_support;
                    country.cpi_score = d.cpi_score / max_cpi_score;

                    countries.push(country);

                });

                return countries;

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
                // return countryMap;

                // var averages = [avg_happiness_score, avg_gdp_per_capita, avg_family, avg_health, avg_freedom, avg_generosity, avg_government_trust, avg_dystopia_residual, avg_continent, avg_Year, avg_social_support, avg_cpi_score]



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


            init(viz){

                // set the dimensions and margins of the graph
                const margin = {top: 50, right: 50, bottom: 10, left: 30},
                    width = 400 - margin.left - margin.right,
                    height = 400 - margin.top - margin.bottom;

                // append the svg object to the body of the page
                const svg = d3.select(viz)
                    .append('svg')
                        .attr("preserveAspectRatio", "xMidYMid meet")
                        .attr("viewBox", [0, 0, 1000, 1000])
                        .classed("svg-content-responsive", true)
                    .append('g')
                    .attr("transform", `translate(${margin.left},${margin.top})`);

                // Color scale: give me a specie name, I return a color
                const color = d3.scaleOrdinal()
                    .domain(["top5", "bottom5",])
                    .range([ "#440154ff", "#21908dff",])


                // Here I set the list of dimension manually to control the order of axis:
                let dimensions = ["GDP_per_capita", "Family", "Health", "Freedom", "Government_trust"]

                // a bit more processing of top 5 and bottom 5

                let top5CountriesProcessed = [];
                let bottom5CountriesProcessed = [];

                this.top5Countries.forEach(d => {
                    let processed = {};
                    processed.country = d.country;
                    processed.continent = d.continent;
                    processed.type = "top5";
                    processed["GDP_per_capita"] = d.avg_gdp_per_capita;
                    processed["Family"] = d.avg_family;
                    processed["Health"] = d.avg_health;
                    processed["Freedom"] = d.avg_freedom;
                    processed["Government_trust"] = d.avg_government_trust;
                    top5CountriesProcessed.push(processed);

                });

                this.bottom5Countries.forEach(d => {
                    let processed = {};
                    processed.country = d.country;
                    processed.continent = d.continent;
                    processed.type = "bottom5";
                    processed["GDP_per_capita"] = d.avg_gdp_per_capita;
                    processed["Family"] = d.avg_family;
                    processed["Health"] = d.avg_health;
                    processed["Freedom"] = d.avg_freedom;
                    processed["Government_trust"] = d.avg_government_trust;
                    bottom5CountriesProcessed.push(processed);


                });


                // For each dimension, I build a linear scale. I store all in a y object
                const y = {}
                for (let i in dimensions) {
                    let name = dimensions[i]
                    y[name] = d3.scaleLinear()
                    .domain( [0,1] ) // --> Same axis range for each group
                    // --> different axis range for each group --> .domain( [d3.extent(data, function(d) { return +d[name]; })] )
                    .range([height - margin.top, 0])
                }

                // Build the X scale -> it find the best position for each Y axis
                let x = d3.scalePoint()
                    .range([margin.left, width])
                    .domain(dimensions);


                // Highlight the specie that is hovered
                const highlight = function(event, d){

                    let selected_type = d.type

                    // first every group turns grey
                    d3.selectAll(".line")
                    .transition().duration(200)
                    .style("stroke", "lightgrey")
                    .style("opacity", "0.2")
                    // Second the hovered specie takes its color
                    d3.selectAll("." + selected_type)
                    .transition().duration(200)
                    .style("stroke", color(selected_type))
                    .style("opacity", "1")
                }

                // Unhighlight
                const doNotHighlight = function(event, d){
                    d3.selectAll(".line")
                    .transition().duration(200).delay(1000)
                    .style("stroke", function(d){ return( color(d.type))} )
                    .style("opacity", "1")
                }

                // The path function take a row of the csv as input, and return x and y coordinates of the line to draw for this raw.
                function path(d) {
                    return d3.line()(dimensions.map(function(p) { return [x(p), y[p](d[p])]; }));
                }



                // Draw the lines
                svg
                    .selectAll("myPath")
                    .data(top5CountriesProcessed.concat(bottom5CountriesProcessed))
                    .join("path")
                    .attr("class", function (d) { return "line " + d.type } ) // 2 class for each line: 'line' and the group name
                    .attr("d",  path)
                    .style("fill", "none" )
                    .style("stroke", function(d){ return( color(d.type))} )
                    .style("opacity", 0.5)
                    .on("mouseover", highlight)
                    .on("mouseleave", doNotHighlight )

                // Draw the axis:
                svg.selectAll("myAxis")
                    // For each dimension of the dataset I add a 'g' element:
                    .data(dimensions).enter()
                    .append("g")
                    .attr("class", "axis")
                    // I translate this element to its right position on the x axis
                    .attr("transform", function(d) { return `translate(${x(d)})`})
                    // And I build the axis with the call function
                    .each(function(d) { d3.select(this).call(d3.axisLeft().ticks(5).scale(y[d])); })
                    // Add axis title
                    .append("text")
                    .style("text-anchor", "middle")
                    .style("fill", "black")
                    .attr("y", -9)
                    .text(function(d) { return d; })
                    .style("fill", "black")

            },
        }
    }
</script>


<style scoped>
#parallel-coordinates-container{
    width: 92vw;
    height: 92vh;
    margin-left: 4%;
    background: pink;
}

</style>
