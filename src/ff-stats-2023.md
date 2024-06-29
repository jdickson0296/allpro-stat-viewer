---
theme: ocean-floor
title: Fantasy Football 2023
toc: false
---

# 2023 Data 🏈

<!-- Load & Clean Data - 2023 Sunday Funday League -->
```js
var playerData = FileAttachment("data/players_espn_2023.csv").csv({typed: true})
```

```js
var filteredPlayerData = playerData.map(item => ({
  name: item.name,
  position: item.position,
  projected_points: item.projected_points,
  points: item.points
}));
```


<!-- Create position selection button -->
```js
import {select, color} from "npm:@observablehq/inputs";

var position = view(
    Inputs.select(["WR", "QB", "RB", "TE", "K","D/ST"], 
    {label: "Position"
    })
);
```


<!-- Create league selection button -->
```js
var league = view(
    Inputs.select(["Sunday Funday", "B&H", "Johnson's"], 
    {label: "League"
    })
);
```


<!-- Filter the data -->
```js
var positionData = playerData.filter(item => item.position === position);
```

```js
var playerRatioData = [];
for (var i = 0; i < positionData.length; i++) {
    playerRatioData.push({
                    name: positionData[i].name, 
                    position: positionData[i].position, 
                    ratio: ((positionData[i].points / positionData[i].projected_points) * 100)
    });
};
```

```js
var splitData = [];
for (let i = 0; i < positionData.length; i++) {
    splitData.push({
        name: positionData[i].name,
        pointType: "actual",
        points: positionData[i].points,
    });
    splitData.push({
        name: positionData[i].name,
        pointType: "projected",
        points: positionData[i].projected_points,
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
        color: {scheme: "blues", legend: true},
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
            sort: {y: null, color: null, fy: {value: '-x', reduce: 'sum'}},
            }),
            Plot.text(splitData, {y:'pointType', x:'points',text:'points', dx: 20, fy: "name"}),
            Plot.ruleX([0])
        ]
    });
}
```

```js
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
            Plot.rectX(playerRatioData, {x: "ratio", y: "name", sort: {y: "x", reverse: true}, fill: "#2C83DB", tip: true}),
            Plot.text(playerRatioData, {x:'ratio', y:'name',text:'ratio', dx: 25}),
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