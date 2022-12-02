<template>
    <div id="png-container"></div>
</template>



<script>
    import * as d3 from "d3";
    import _ from "lodash";
    import * as d3ToPng from 'd3-svg-to-png';

    export default{
        name: "PNGGeneratorVue",
        setup(){

        },
        data(){
            return {
                countryMap: [],
                continentMap: [],
                countriesProcessed:[],

                countryNames: [],

                continentNames: ['Africa', 'Asia', 'South America', 'Europe', 'North America', 'Australia'],

                regionalNames:[],

                regionalMap: [],

                countryGlyphData: Map,
                countryBarChartData: Map,

                continentGlyphData: Map,
                continentBarChartData: Map,

                regionalGlyphData: Map,
                regionalBarChartData: Map,

                // color meaning: https://www.incredibleart.org/lessons/middle/color2.htm
                // family: pink
                // health: dark green
                // freedom: dark blue
                // gdp: gold
                // government trust: grey
                color : ['#FF69B4', '#006400', '#00008B', '#DAA520', '#808080'],


            }
        },
        props:{
            myData: Array,
            myRegionalData: Array,
        },
        mounted(){

            // preprocess the data
            this.countryMap = this.normalizeData(this.myData);
            this.countryMap = this.preProcessCountry(this.countryMap);

            this.continentMap = this.preProcessContinent(this.countryMap);

            // sort by happiness score
            this.countryMap.sort(function(a, b){return a.avg_happiness_score - b.avg_happiness_score})

            // hash tables for country glyphs and bar charts
            this.countryGlyphData = this.preProcessGlyph(this.countryMap);
            this.countryBarChartData = this.preProcessBarChart(this.countryMap);


            // hash tables for continents glyphs and bar charts
            this.continentGlyphData = this.preProcessGlyph(this.continentMap);
            this.continentBarChartData = this.preProcessBarChart(this.continentMap);

            // get regionalData
            this.regionalMap = this.preProcessRegionalData(this.countryMap);

            // hashtables for regional glyphs and bar charts
            this.regionalGlyphData = this.preProcessGlyph(this.regionalMap);
            this.regionalBarChartData = this.preProcessBarChart(this.regionalMap)

            // console.log(this.regionalMap)
            // console.log(this.regionalNames)

            // WARNING THESE FUNCTIONS are purposely commented out
            // Before uncommenting ensure that you intend to create a 132 * 2 pngs
            // Make sure this code is commented out before committing any changes
            // and comment out immediately after using
            // this.createGlyphs("#png-container", this.countryNames, this.countryGlyphData)
            // this.createBarChart("#png-container", this.countryNames, this.countryBarChartData)

            // continents
            // this.createGlyphs("#png-container", this.continentNames, this.continentGlyphData)
            // this.createBarChart("#png-container", this.continentNames, this.continentBarChartData)

            // regions
            // this.createGlyphs("#png-container", this.regionalNames, this.regionalGlyphData)
            // this.createBarChart("#png-container", this.regionalNames, this.regionalBarChartData)


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
                    country['place'] = d.Country;
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

                    // push country name to countryNames list
                    this.countryNames.push(country['place']);
                }

                return averagedCountries
            },
            preProcessContinent(arr) {
                var continentMap = new Map();

                arr.forEach(d => {
                    if (!continentMap.get(d.continent)) {
                    continentMap.set(d.continent, [d]);
                    }
                    else {
                    var temp = continentMap.get(d.continent);
                    temp.push(d);
                    continentMap.set(d.continent, temp);
                    }
                });
                // return continentMap;

                var averagedContinents = []
                for (let [key, value] of continentMap) {

                    var continent = {}
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

                    value.forEach(d => {
                    // continent['country'] = d.Country;
                    continent['place'] = d.continent;
                    if (d.avg_happiness_score != 0) {
                        avg_happiness_score += d.avg_happiness_score;
                        happiness_score_count += 1;
                    }
                    if (d.avg_gdp_per_capita != 0) {
                        avg_gdp_per_capita += d.avg_gdp_per_capita;
                        gdp_per_capita_count += 1;
                    }
                    if (d.avg_family != 0) {
                        avg_family += d.avg_family;
                        family_count += 1;
                    }
                    if (d.avg_health != 0) {
                        avg_health += d.avg_health;
                        health_count += 1;
                    }
                    if (d.avg_freedom != 0) {
                        avg_freedom += d.avg_freedom;
                        freedom_count += 1;
                    }
                    if (d.avg_generosity != 0) {
                        avg_generosity += d.avg_generosity;
                        generosity_count += 1;
                    }
                    if (d.avg_government_trust != 0) {
                        avg_government_trust += d.avg_government_trust;
                        government_trust_count += 1;
                    }
                    if (d.avg_dystopia_residual != 0) {
                        avg_dystopia_residual += d.avg_dystopia_residual;
                        dystopia_residual_count += 1;
                    }
                    if (d.avg_social_support != 0) {
                        avg_social_support += d.avg_social_support;
                        social_support_count += 1;
                    }
                    if (d.avg_cpi_score != 0) {
                        avg_cpi_score += d.avg_cpi_score;
                        cpi_score_count += 1;
                    }

                    });
                    console.log(continent)
                    continent.avg_happiness_score = avg_happiness_score / happiness_score_count;

                    // console.log(avg_happiness_score);
                    // console.log(continent.avg_happiness_score);

                    continent.avg_gdp_per_capita = avg_gdp_per_capita / gdp_per_capita_count;
                    continent.avg_family = avg_family / family_count;
                    continent.avg_health = avg_health / health_count;
                    continent.avg_freedom = avg_freedom / freedom_count;
                    continent.avg_generosity = avg_generosity / generosity_count;
                    continent.avg_government_trust = avg_government_trust / government_trust_count;
                    continent.avg_dystopia_residual = avg_dystopia_residual / dystopia_residual_count;
                    continent.avg_social_support = avg_social_support / social_support_count;
                    continent.avg_cpi_score = avg_cpi_score / cpi_score_count;
                    averagedContinents.push(continent)
                }

                return averagedContinents

            },
            preProcessRegionalData(arr){
                var mapCountry = new Map()
                var mapRegional = new Map()

                var regionalData = [];

                // first place all country Data into a map for easy lookup when parsing regional data
                for(let i = 0; i < arr.length; i++){
                    let country = arr[i];
                    mapCountry.set(country['place'], country)
                }

                // now go through regional csv to put in Map
                for(let i = 0; i < this.myRegionalData.length; i++){
                    let place = this.myRegionalData[i];

                    let region = place['Region']
                    let country = place['Country']

                    if(mapCountry.has(country)){
                        let countryData = mapCountry.get(country)
                        if(mapRegional.has(region)){
                            let regionData = mapRegional.get(region)

                            regionData['happiness_score'].push(countryData.avg_happiness_score)
                            regionData['gdp_per_capita'].push(countryData.avg_gdp_per_capita)
                            regionData['family'].push(countryData.avg_family)
                            regionData['health'].push(countryData.avg_health)
                            regionData['freedom'].push(countryData.avg_freedom)
                            regionData['generosity'].push(countryData.avg_generosity)
                            regionData['government_trust'].push(countryData.avg_government_trust)
                            regionData['dystopia_residual'].push(countryData.avg_dystopia_residual)
                            regionData['social_support'].push(countryData.avg_social_support)
                            regionData['cpi_score'].push(countryData.avg_cpi_score)
                        }else{
                            // add region to regionalNames
                            this.regionalNames.push(region)

                            let regionData = {}

                            regionData['happiness_score'] = [countryData.avg_happiness_score]
                            regionData['gdp_per_capita'] = [countryData.avg_gdp_per_capita]
                            regionData['family'] = [countryData.avg_family]
                            regionData['health'] = [countryData.avg_health]
                            regionData['freedom'] = [countryData.avg_freedom]
                            regionData['generosity'] = [countryData.avg_generosity]
                            regionData['government_trust'] = [countryData.avg_government_trust]
                            regionData['dystopia_residual'] = [countryData.avg_dystopia_residual]
                            regionData['social_support'] = [countryData.avg_social_support]
                            regionData['cpi_score'] = [countryData.avg_cpi_score]

                            mapRegional.set(region, regionData)
                        }
                    }
                }


                // now we need to take average for each region and place in regionalData
                mapRegional.forEach((value, key) =>{
                    let regionData = {}
                    regionData.place = key;

                    // now need to take average of each parameter
                    // happiness score
                    let happiness = value['happiness_score']
                    let gdp = value['gdp_per_capita']
                    let family = value['family']
                    let health = value['health']
                    let freedom = value['freedom']
                    let generosity = value['generosity']
                    let government = value['government_trust']
                    let dystopia = value['dystopia_residual']
                    let social = value['social_support']
                    let cpi = value['cpi_score']

                    regionData.avg_happiness_score = happiness.reduce((a, b) => a + b) / happiness.length;
                    regionData.avg_gdp_per_capita = gdp.reduce((a, b) => a + b) / gdp.length;
                    regionData.avg_family = family.reduce((a, b) => a + b) / family.length;
                    regionData.avg_health = health.reduce((a, b) => a + b) / health.length;
                    regionData.avg_freedom = freedom.reduce((a, b) => a + b) / freedom.length;
                    regionData.avg_generosity = generosity.reduce((a, b) => a + b) / generosity.length;
                    regionData.avg_government_trust = government.reduce((a, b) => a + b) / government.length;
                    regionData.avg_dystopia_residual = dystopia.reduce((a, b) => a + b) / dystopia.length;
                    regionData.avg_social_support = social.reduce((a, b) => a + b) / social.length;
                    regionData.avg_cpi_score = cpi.reduce((a, b) => a + b) / cpi.length;

                    regionalData.push(regionData)
                })
                return regionalData
            },
            preProcessBarChart(arr) {
                var barChartData = new Map()

                arr.forEach(d => {

                    var family = {}
                    family.name = 'family'
                    family.value = d.avg_family

                    var health = {}
                    health.name = 'health'
                    health.value = d.avg_health

                    var freedom = {}
                    freedom.name = 'freedom'
                    freedom.value = d.avg_freedom

                    var gdp_per_capita = {}
                    gdp_per_capita.name = 'gdp_per_capita'
                    gdp_per_capita.value = d.avg_gdp_per_capita

                    var government_trust = {}
                    government_trust.name = 'government_trust'
                    government_trust.value = d.avg_government_trust

                    barChartData.set(d.place, [family, health, freedom, gdp_per_capita, government_trust]);
                });

                return barChartData;

            },
            preProcessGlyph(arr){
                var glyphData = new Map()

                // define the petal path for each type of data
                const petalPath = 'M 0,0 C -30,-30 -30,-30 0,-100 C 30,-30 30,-30 0,0';

                arr.forEach(d => {
                    const tempPetals = [d.avg_family, d.avg_health, d.avg_freedom, d.avg_gdp_per_capita, d.avg_government_trust];
                    const happiness = d.avg_happiness_score;

                    let petals = []
                    tempPetals.forEach((value, i) => {
                        petals.push({
                        petSize: value * 0.6,
                        angle: 360 * i / 5,
                        petalPath,
                        radius: 50,
                        offset: 5
                        })
                    })

                    glyphData.set(d['place'],
                    {
                        place: d['place'],
                        petSize: 0.4,
                        petals: petals,
                        happiness: +happiness.toFixed(3),
                    })
                });

                return glyphData

            },
            // for creating glyphs
            createGlyphs(viz, arr, map){

                // this.createEachGlyph(viz, "Afghanistan");
                // this.createEachGlyph(viz, "India");

                console.log(this.countryNames);

                function timer(ms) { return new Promise(res => setTimeout(res, ms)); }

                // need to add a small delay so that it doesn't get overloaded

                let creation = async() => {
                    for(let i = 0; i < arr.length; i++){
                        await timer(500);
                        this.createEachGlyph(viz, arr[i], map);
                    }
                };

                creation();


            },
            createEachGlyph(viz, place, map){

                let data = [map.get(place)] // needs to be in an array for creation of petals

                // clear out svg to create next one
                d3.select(viz).selectAll("svg").remove();

                const petalSize = 200
                const height = 250
                const width = 200



                const svg = d3.select(viz)
                    .append("svg")
                        .attr('height', height)
                        .attr('width', width)
                        .attr('id', 'glyphs')

                const flowers = svg
                    .selectAll('#topFlowers')
                    .data(data)
                    .enter()
                    .append('g')
                    .attr('transform', (d, i) => `translate(${(i) * petalSize + 100}, ${100})`)
                    .attr('id', 'topFlowers')

                const circles = svg
                    .selectAll("#topCircles")
                    .data(data)
                    .join("circle")
                    .attr("id", "topCircles")
                    .attr("cx", (d, i) => i * petalSize + 100)
                    .attr("cy", (d, i) => 100 + 20) // + 20 because we rotate around (0,20)
                    .attr("r", 20)
                    .style("fill", "white")
                    .style("stroke", "black")


                flowers.selectAll('path')
                    .data(d => d.petals)
                    .enter()
                    .append('path')
                    .attr('d', d => d.petalPath)
                    .attr('transform', d => `rotate(${d.angle}, 0, 20)scale(${d.petSize})`) // key thing here is the arguments to rotate: angle, plus point that you want to rotate around
                    .attr('fill', (d,i) => this.color[i])
                    .attr("stroke", "black")
                    .style("stroke-width", 2)


                const titles = svg
                    .selectAll("#topTitles")
                    .data(data)
                    .join("text")
                    .attr('id', 'topTitles')
                    .attr("x", (d, i) => i * petalSize + 100)
                    .attr("y", (d, i) => 100 + 20) // + 20 because we rotate around (0,20)
                    .text(d => d.happiness)
                    .style("fill", "black")
                    .style("font", "10px times")
                    .attr("text-anchor", "middle")


                d3ToPng('#glyphs', place.replace(/ /g,'') + '-glyph', {scale: 7, quality: 1})


            },

            createBarChart(viz, arr, map){

                // need to add for loop to go through every arr

                function timer(ms) { return new Promise(res => setTimeout(res, ms)); }

                // need to add a small delay so that it doesn't get overloaded

                let creation = async() => {
                    for(let i = 0; i < arr.length; i++){
                        await timer(500);
                        this.createEachBar(viz, arr[i], map);
                    }
                };

                creation();
            },
            createEachBar(viz, place, map){

                // clear out svg to create next one
                d3.select(viz).selectAll("svg").remove();

                // set the dimensions and margins of the graph
                const margin = {top: 15, right: 50, bottom: 200, left: 50},
                    width = 600 - margin.left - margin.right,
                    height = 500 - margin.top - margin.bottom;

                // append the svg object to the body of the page
                const svg = d3.select(viz).append("svg")
                    .attr("width", width + margin.left + margin.right)
                    .attr("height", height + margin.top + margin.bottom)
                    // .append('g')
                    .attr("transform", `translate(${margin.left},${margin.top})`)
                    .attr('id', 'barChart')

                // const svg = d3.select(DOM.svg(width + margin.left + margin.right, height + margin.top + margin.bottom))

                // const svg = d3.select("#my_dataviz")
                //   .append("svg")
                //     .attr("width", width + margin.left + margin.right)
                //     .attr("height", height + margin.top + margin.bottom)
                //   .append("g")
                //     .attr("transform", `translate(${margin.left},${margin.top})`);

                // select country
                const data = map.get(place)
                console.log(data)

                // X axis
                const x = d3.scaleBand()
                .range([ margin.left, width ])
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
                .domain([0, 1.1])
                .range([ height, 10]); //10 so top of y-axis doesn't get cut off
                svg.append("g")
                .style("font-size","20px")
                .call(d3.axisLeft(y))
                .attr("transform", `translate(${margin.left}, 0)`)


                // Bars
                svg.selectAll("mybar")
                .data(data)
                .join("rect")
                    .attr("x", d => x(d.name))
                    .attr("width", x.bandwidth())
                    .attr("fill", "#69b3a2")
                    // no bar at the beginning thus:
                    .attr("height", d => height - y(d.value)) // always equal to 0
                    .attr("y", d => y(d.value))
                    .attr('fill', (d,i) => this.color[i])


                d3ToPng('#barChart', place.replace(/ /g,'') + '-bar', {scale: 5, quality: 1, background: "white"})

            }
        },
    }
</script>
