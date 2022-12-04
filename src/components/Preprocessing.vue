<template>
    <div id="data">
        <h1 id="data-header">Data</h1>
        <p>To understand happiness in different countries, we used the Kaggle dataset: Happiness and Corruption by Country from 2015-2020.
        <br>
        The data list many factors that affect happiness for each country and year: GDP per capita, Family, Health, Freedom, Government Trust, Social Support, Dystopia Residual, Generosity, Continent name, CPI Score.
        <br>
        <br>
        </p>
        <h1 id="question-header">The Question</h1>
        <p>
            Which are the happiest and unhappiest countries, and out of the 12 factors provided, which ones contribute most to a country’s happiness?
        </p>
        <br>
        <h1 id="preprocessing-header">Processing the Data</h1>
        <p>Using Recurisve Feature Elimination with Cross Validation, 5 features appear to affect happiness the most:</p>
        <br>
        <table id="feature-table">
            <tr>
                <th id="table-header">Feature</th>
                <th id="table-header">Rank</th>
            </tr>
            <tr>
                <td style="font-weight:bolder">Family</td>
                <td style="font-weight:bolder">1</td>
            </tr>
            <tr>
                <td style="font-weight:bolder">Health</td>
                <td style="font-weight:bolder">1</td>
            </tr>
            <tr>
                <td style="font-weight:bolder">Freedom</td>
                <td style="font-weight:bolder">1</td>
            </tr>
            <tr>
                <td style="font-weight:bolder">GDP per capita</td>
                <td style="font-weight:bolder">1</td>
            </tr>
            <tr>
                <td style="font-weight:bolder">Government Trust</td>
                <td style="font-weight:bolder">1</td>
            </tr>
            <tr>
                <td>Social Support</td>
                <td>2</td>
            </tr>
            <tr>
                <td>Dystopia Residual</td>
                <td>3</td>
            </tr>
            <tr>
                <td>Year</td>
                <td>4</td>
            </tr>
            <tr>
                <td>Continent name</td>
                <td>5</td>
            </tr>
            <tr>
                <td>Generosity</td>
                <td>6</td>
            </tr>
            <tr>
                <td>Corruption Perception Index Score</td>
                <td>7</td>
            </tr>
            <tr>
                <td>Country name</td>
                <td>8</td>
            </tr>

        </table>
    </div>

</template>

<style scoped>

    #data{

    }

    #data-header{
        text-align: center;
        font-size: 30px;
        font-weight: bolder;
    }

    #question-header{
        text-align: center;
        font-size: 30px;
        font-weight: bolder;
    }

    #preprocessing-header{
        text-align: center;
        font-size: 30px;
        font-weight: bolder;
    }

    p{
        text-align: center;
        font-size: 20px;
    }

    #feature-table{
        margin-left: auto;
        margin-right: auto;
    }

    table, th, td{
        border: 1px solid;
        font-size: 20px;
    }
    th{
        font-weight: bold;
        font-size: 22px;
    }
</style>







<!---->