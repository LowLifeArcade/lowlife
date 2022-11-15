<script setup>
import df from '/data/df.json'
import * as d3 from 'd3'
const jsonDf = df
const state = reactive({
    xArr: [],
    xCount: null,
    local250Count: null,
})

function useD3() {
    var w = window.innerWidth;
    var h = window.innerHeight;

    var svg = d3.select("#line")
        .append("svg")
        .attr("width", w)
        .attr("height", h)
        .attr("id", "visualization");

    var x = d3.scaleLinear().domain([0, 10]).range([0, w]);
    var y = d3.scaleLinear().domain([0, 10]).range([10, h - 10]);

    var line = d3.line()
        .x(function (d, i) { return x(i); })
        .y(function (d) { return y(d); })
        .curve(d3.curveNatural)

    // data is created inside the function so it is always unique
    let repeat = () => {
        // var data = d3.range(11).map(function () { return Math.random() * 10 })
        // data = d3.json('df.json')
        // let data = [1,5,3,6,3,1,11,2,9,5]
        // var data = d3.range(12).map(function () { return state.xCount / state.local250Count })


        // Uncomment following line to clear the previously drawn line
        //svg.selectAll("path").remove();

        // Set a light grey class on old paths
        svg.selectAll("path").attr("class", "old");

        var path = svg.append("path")
            .attr("d", line(data))
            .attr("stroke", "darkgrey")
            .attr("stroke-width", "2")
            .attr("fill", "none");

        var totalLength = path.node().getTotalLength();

        path
            .attr("stroke-dasharray", totalLength + " " + totalLength)
            .attr("stroke-dashoffset", totalLength)
            .transition()
            .duration(4000)
            .ease(d3.easeLinear)
            .attr("stroke-dashoffset", 0)
            .on("end", repeat);
    };
    repeat();

}


function getCount() {
    for (const k in jsonDf['Local 250']) {
        // if (Object.hasOwnProperty.call(jsonDf, k)) {
        const element = jsonDf['Local 250'][k];
        if (element === 'X') {
            state.xArr.push(element);
        }
        // }
    }
    state.local250Count = Object.keys(jsonDf['Local 250']).length
    state.xCount = state.xArr.length
    console.log("local250Count", state.local250Count, state.xCount);
}

// console.log("jsonDf", jsonDf['Local 250'])
onMounted(() => {
    getCount()
    useD3()
})

</script>

<template>
    <!-- <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/d3/4.4.1/d3.min.js"></script> -->
    <h1>
        <!-- {{ jsonDf['Local 250'] }} -->
        <!-- {{ state.xCount }} / {{ state.local250Count }} -->
        <div id="line"></div>
    </h1>
</template>

<style>
#line {
    width: 100%;
    margin: 20px 0;
    height: 300px;
}

.old {
    stroke: lightgrey;
    stroke-dasharray: 5;
    stroke-dashoffset: 5;
}
</style>