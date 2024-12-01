---
theme: stark
title: Fantasy Football 2023
toc: false
---

# 2023 Data 🏈

<!-- Load & Clean Data - 2023 Sunday Funday League -->
```js
var playerData = FileAttachment("data/players_2023.csv").csv({typed: true})
```

<!-- Create position and league selection buttons -->
```js
import {select, color} from "npm:@observablehq/inputs";

var position = view(
    Inputs.select(["WR", "QB", "RB", "TE", "K","DEF"], 
    {label: "Position"
    })
);

var league = view(
    Inputs.select(["Sunday Funday", "Johnson's", "BNH"], 
    {label: "League"
    })
);
```

<!-- Filter the data -->
```js
var teamData = playerData.filter(item => item.league_name === league);
var positionData = teamData.filter(item => item.position === position);
```

<!-- Create Plots -->
```js
function playerTotal(data, {width} = {}) {
    return Plot.plot({
        title: "Player Scored vs. Projected Ratio (%)",
        marginLeft: 110,
        marginRight: 1,
        marginTop: 20,
        marginBottom: 100,
        width,
        grid: true,
        marks: [
            Plot.rectX(positionData, {
                x: "total_points", 
                y: "name", 
                sort: {y: "x", reverse: true}, 
                fill: "#ef8a62", tip: true}),
            Plot.text(positionData, {
                x:'total_points', 
                y:'name', 
                text:'total points', 
                dx: 25}),
            Plot.ruleX([0])
        ]
    });
}
```

<!-- Plot -->
<div class="grid grid-cols-1">
  <div class="card">
    ${resize((width) => playerTotal(positionData, {width}))}
  </div>
</div>
