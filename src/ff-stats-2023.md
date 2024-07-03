---
theme: stark
title: Fantasy Football 2023
toc: false
---

# 2023 Data 🏈


<!-- Load & Clean Data - 2023 Sunday Funday League -->
```js
var playerData = FileAttachment("data/players_espn_2023.csv").csv({typed: true})
```


<!-- Create position and league selection buttons -->
```js
import {select, color} from "npm:@observablehq/inputs";

var position = view(
    Inputs.select(["WR", "QB", "RB", "TE", "K","D/ST"], 
    {label: "Position"
    })
);

var league = view(
    Inputs.select(["Sunday Funday", "Johnson's"], 
    {label: "League"
    })
);
```


<!-- Filter the data -->
```js
var teamData = playerData.filter(item => item.league_name === league);
var positionData = teamData.filter(item => item.position === position);
var playerRatioData = [];
var splitData = [];

for (var i = 0; i < positionData.length; i++) {
    playerRatioData.push({
                    name: positionData[i].name, 
                    position: positionData[i].position, 
                    ratio: ((positionData[i].total_points / positionData[i].total_projected_points) * 100)
    });
};

for (let i = 0; i < positionData.length; i++) {
    splitData.push({
        name: positionData[i].name,
        pointType: "actual",
        points: positionData[i].total_points,
    });
    splitData.push({
        name: positionData[i].name,
        pointType: "projected",
        points: positionData[i].total_projected_points,
    });
};
```


<!-- Create Plots -->
```js
function playerTotal(data, {width} = {}) {
    return Plot.plot({
        title: "Players Total & Projected Points",
        y: {axis: null},
        x: {tickFormat: "s", grid: true},
        color: {scheme: "rdgy", legend: true},
        marginLeft: 120,
        marginRight: 100,
        marginTop: 10,
        marginBottom: 50,
        aspectRatio: 0.15,
        width,
        marks: [
            Plot.barX(splitData, {
                y: "pointType",
                x: "points",
                fill: "pointType",
                fy: "name",
                tip: true,
                sort: {fy: {value: '-x', reduce: 'max'}},
            }),
            Plot.text(splitData, {y:'pointType', x:'points',text:'points', dx: 20, fy: "name"}),
            Plot.ruleX([0])
        ]
    });
}

function playerRatio(data, {width} = {}) {
    return Plot.plot({
        title: "Player Scored vs. Projected Ratio (%)",
        marginLeft: 110,
        marginRight: 1,
        marginTop: 20,
        marginBottom: 100,
        width,
        grid: true,
        marks: [
            Plot.rectX(playerRatioData, {
                x: "ratio", 
                y: "name", 
                sort: {y: "x", reverse: true}, 
                fill: "#ef8a62", tip: true}),
            Plot.text(playerRatioData, {
                x:'ratio', 
                y:'name', 
                text:'ratio', 
                dx: 25}),
            Plot.ruleX([0])
        ]
    });
}
```


<!-- Plot -->
<div class="grid grid-cols-1">
  <div class="card">
    ${resize((width) => playerTotal(splitData, {width}))}
  </div>
</div>

<div class="grid grid-cols-1">
  <div class="card">
    ${resize((width) => playerRatio(playerRatioData, {width}))}
  </div>
</div>