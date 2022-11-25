<template>
    <div id="overlay-container"></div>
</template>

<script>
    import * as d3 from "d3";
    import _ from "lodash";

    export default{
        name: "OverlayVue",
        setup() {

        },
        data(){
            return {
                countryMap: [],
                bottom5Countries: [],
                top5Countries: [],
                dimensions:[],

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

            this.dimensions = ["Family", "Health", "Freedom", "GDP_per_capita", "Government_trust"];


            this.init("#overlay-container");


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

                // define the petal path for each type of data
                const petalPath = 'M 0,0 C -30,-30 -30,-30 0,-100 C 30,-30 30,-30 0,0';

                const petalSize = 500

                let color = ['#FF69B4', '#006400', '#00008B', '#DAA520', '#808080']

                let viewBox_x = 800
                let viewBox_y = 800

                let top5_x = 200
                let top5_y = 150

                let bottom5_x = 600
                let bottom5_y = 150

                let top5Average = 0
                let bottom5Average = 0

                const top5Data = _.map(this.top5Countries, d => {
                    const tempPetals = [d.avg_family, d.avg_health, d.avg_freedom, d.avg_gdp_per_capita, d.avg_government_trust];
                    const happiness = d.avg_happiness_score;

                    let petals = []
                    tempPetals.forEach((value, i) => {
                        petals.push({
                        petSize: value,
                        angle: 360 * i / 5,
                        petalPath,
                        radius: 50,
                        offset: 5
                        })
                    })

                    return {
                        country: d.country,
                        petSize: 0.4,
                        petals: petals,
                        happiness: +happiness.toFixed(3)
                    }
                })

                top5Data.forEach(d => {
                    top5Average += d.happiness
                })

                top5Average = +(top5Average / top5Data.length).toFixed(3)

                const bottom5Data = _.map(this.bottom5Countries, d => {
                    const tempPetals = [d.avg_family, d.avg_health, d.avg_freedom, d.avg_gdp_per_capita, d.avg_government_trust];
                    const happiness = d.avg_happiness_score;

                    let petals = []
                    tempPetals.forEach((value, i) => {
                        petals.push({
                        petSize: value,
                        angle: 360 * i / 5,
                        petalPath,
                        radius: 50,
                        offset: 5
                        })
                    })

                    return {
                        country: d.country,
                        petSize: 0.4,
                        petals: petals,
                        happiness: +happiness.toFixed(3)
                    }
                })

                bottom5Data.forEach(d => {
                    bottom5Average += d.happiness
                })

                bottom5Average = +(bottom5Average / bottom5Data.length).toFixed(3)


                // append the svg object to the body of the page
                const svg = d3.select(viz)
                    .append('svg')
                        .attr("preserveAspectRatio", "xMidYMid meet")
                        .attr("viewBox", [0, 0, viewBox_x, viewBox_y])
                        .classed("svg-content-responsive", true)
                    .append('g')
                    //.attr("transform", `translate(${margin.left},${margin.top})`);


                const topFlowers = svg
                    .selectAll('g')
                    .data(top5Data)
                    .enter()
                    .append('g')
                    .attr('transform', (d, i) => `translate(${top5_x}, ${top5_y})`)

                const topCircles = svg
                    .selectAll("circle")
                    .data(top5Data)
                    .join("circle")
                    .attr("cx", (d, i) => top5_x)
                    .attr("cy", (d, i) => top5_y + 20) // + 20 because we rotate around (0,20)
                    .attr("r", 20)
                    .style("fill", "white")
                    .style("stroke", "black")

                const top5CircleValue = svg.selectAll("#top5CircleValue")
                    .data([top5Average])
                    .join("text")
                    .attr("x", (d, i) => top5_x)
                    .attr("y", (d, i) => top5_y + 20) // + 20 because we rotate around (0,20)
                    .text(d => d)
                    .style("fill", "black")
                    .style("font", "10px times")
                    .attr("text-anchor", "middle")


                topFlowers.selectAll('path')
                    .data(d => d.petals)
                    .enter()
                    .append('path')
                    .attr('d', d => d.petalPath)
                    //.attr("dx", d => d.offset + Math.cos(d.angle) * 5)
                    //.attr("dy", d => d.offset + Math.sin(d.angle) * 5)
                    .attr('transform', d => `rotate(${d.angle}, 0, 20)scale(${d.petSize})`) // key thing here is the arguments to rotate: angle, plus point that you want to rotate around
                    //.attr('transform', d => `scale(${d.petSize})`)
                    .attr('fill', (d,i) => color[i])
                    .attr("fill-opacity", 0.3)
                    .attr("stroke", "black");



                const bottomFlowers = svg
                    .selectAll('bottomFlowers')
                    .data(bottom5Data)
                    .enter()
                    .append('g')
                    .attr('transform', (d, i) => `translate(${bottom5_x}, ${bottom5_y})`)

                const bottomCircles = svg
                    .selectAll("bottomCircles")
                    .data(bottom5Data)
                    .join("circle")
                    .attr("cx", (d, i) => bottom5_x)
                    .attr("cy", (d, i) => bottom5_y + 20) // + 20 because we rotate around (0,20)
                    .attr("r", 20)
                    .style("fill", "white")
                    .style("stroke", "black")

                const bottom5CircleValue = svg.selectAll("#bottom5CircleValue")
                    .data([bottom5Average])
                    .join("text")
                    .attr("x", (d, i) => bottom5_x)
                    .attr("y", (d, i) => bottom5_y + 20) // + 20 because we rotate around (0,20)
                    .text(d => d)
                    .style("fill", "black")
                    .style("font", "10px times")
                    .attr("text-anchor", "middle")


                bottomFlowers.selectAll('path')
                    .data(d => d.petals)
                    .enter()
                    .append('path')
                    .attr('d', d => d.petalPath)
                    //.attr("dx", d => d.offset + Math.cos(d.angle) * 5)
                    //.attr("dy", d => d.offset + Math.sin(d.angle) * 5)
                    .attr('transform', d => `rotate(${d.angle}, 0, 20)scale(${d.petSize})`) // key thing here is the arguments to rotate: angle, plus point that you want to rotate around
                    //.attr('transform', d => `scale(${d.petSize})`)
                    .attr('fill', (d,i) => color[i])
                    .attr("fill-opacity", 0.3)
                    .attr("stroke", "black");

                // add a legend for each feature
                const legendCircles = svg.selectAll("#legendCircles")
                    .data(color)
                    .join("circle")
                    .attr("cx", (d) => (top5_x + bottom5_x) / 2 - 50)
                    .attr("cy", (d, i) => (top5_y - 20 + i * 20))
                    .attr("r", 4)
                    .style("fill", (d,i) => color[i])

                const legendTitles = svg.selectAll("#legendTitles")
                    .data(this.dimensions)
                    .join("text")
                    .text(function(d) {return d})
                    .attr("x", (d) => (top5_x + bottom5_x) / 2 - 40)
                    .attr("y", (d, i) => (top5_y - 15 + i * 20))
                    .style("fill", "black")
                    .style("font", "15px times")
                    //.style("font-family", "monospace")

                const top5Title = svg.selectAll("#top5Title")
                    .data(["Happiest 5 Countries"])
                    .join("text")
                    .text(d => d)
                    .attr("x", (d) => top5_x)
                    .attr("y", (d) => top5_y + 125)
                    .attr("text-anchor", "middle")
                    .style("font", "20px times")
                    .style("font-weight", "bold")

                const bottom5Title = svg.selectAll("#bottom5Title")
                    .data(["Unhappiest Five Countries"])
                    .join("text")
                    .text(d => d)
                    .attr("x", (d) => bottom5_x)
                    .attr("y", (d) => bottom5_y + 125)
                    .attr("text-anchor", "middle")
                    .style("font", "20px times")
                    .style("font-weight", "bold")

                const top5Names = svg.selectAll("#top5Names")
                    .data(top5Data)
                    .join("text")
                    .text(d => d.country)
                    .attr("x", (d) => top5_x)
                    .attr("y", (d, i) => top5_y + 150 + i * 20)
                    .attr("text-anchor", "middle")
                    .style("font", "15px times")

                const bottom5Names = svg.selectAll("#bottom5Names")
                    .data(bottom5Data)
                    .join("text")
                    .text(d => d.country)
                    .attr("x", (d) => bottom5_x)
                    .attr("y", (d, i) => bottom5_y + 150 + i * 20)
                    .attr("text-anchor", "middle")
                    .style("font", "15px times")


            },
        }
    }
</script>


<style scoped>
#overlay-container{
    width: 92vw;
    height: 92vh;
    margin-left: 4%;
}

</style>
