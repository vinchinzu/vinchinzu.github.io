---
layout: post
title: "D3 Test with Trade Data"
date: 2015-06-12 12:06:58 -0500
comments: true
categories:
---


<script src="http://d3js.org/d3.v3.js"></script>


## Running the test from the Comtrade API site:

This is a simiple D3 chart that uses the API reference to pull the data directly into the site.

<div>
  <style type="text/css">

.bar {
    fill: steelblue;
}
.bar:hover {
    fill: brown;
}
.axis {
    font: 10px sans-serif;
}
.axis path, .axis line {
    fill: none;
    stroke: #000;
    shape-rendering: crispEdges;
}
.x.axis path {
    display: none;
}
  </style>

</div>



<!-- D3.js Chart -->


<div id='chart-2'></div>

<script type='text/javascript'>

(function() {

  function draw() {

    $('#chart-2').empty();



<!-- FROM SAMPLE -->

var margin = {
    top: 20,
    right: 20,
    bottom: 30,
    left: 40
},

width = 960 - margin.left - margin.right,
    height = 500 - margin.top - margin.bottom;

var x = d3.scale.ordinal().rangeRoundBands([0, width], .1);

var y = d3.scale.linear()
    .range([height, 0]);

var xAxis = d3.svg.axis()
    .scale(x)
    .orient("bottom");

var yAxis = d3.svg.axis()
    .scale(y)
    .orient("left")
    .ticks(10)
    .tickFormat(function (d) {
        var prefix = d3.formatPrefix(d);
        return prefix.scale(d) + prefix.symbol;
    })

var svg = d3.select("#chart-2").append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
    .append("g")
    .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

d3.json("http://comtrade.un.org/api/get?max=500&type=C&freq=A&previewMaxRecords=500&downloadMaxRecords=50000&px=HS&ps=2013%2C2012%2C2011%2C2010%2C2009&r=398&p=0&rg=2&cc=TOTAL", function (error, json) {
    debugger;
    data = json.dataset;
    data = data.sort(compare);

        x.domain(data.map(function (d) {
        return d.yr;
    }));
    y.domain([0, d3.max(data, function (d) {
        return d.TradeValue;
    })]);

    svg.append("g")
        .attr("class", "x axis")
        .attr("transform", "translate(0," + height + ")")
        .call(xAxis);

    svg.append("g")
        .attr("class", "y axis")
        .call(yAxis)
        .append("text")
        .attr("transform", "rotate(-90)")
        .attr("y", 6)
        .attr("dy", ".71em")
        .style("text-anchor", "end")
        .text("Trade Value");

    svg.selectAll(".bar")
        .data(data)
        .enter().append("rect")
        .attr("class", "bar")
        .attr("x", function (d) {
        return x(d.yr);
    })
        .attr("width", x.rangeBand())
        .attr("y", function (d) {
        return y(d.TradeValue);
    })
        .attr("height", function (d) {
        return height - y(d.TradeValue);
    });

});

function compare(a,b) {
  if (a.TradeValue < b.TradeValue)
     return -1;
  if (a.TradeValue > b.TradeValue)
    return 1;
  return 0;
}

}

  draw();

  $(window).resize(function() {
    draw();
  });

})();



</script>


<div>
<p>Second Test Graph</p>
</div>

<!-- D3.js Chart -->
<div id='chart-1'></div>
<script type='text/javascript'>
(function() {

  function draw() {

    $('#chart-1').empty();

    var x = d3.scale.linear()
        .domain([0, d3.max(data)])
        .range([0, width - margin.left - margin.right]);

    var y = d3.scale.ordinal()
        .domain(d3.range(data.length))
        .rangeRoundBands([height - margin.top - margin.bottom, 0], 0.2);

    var xAxis = d3.svg.axis()
        .scale(x)
        .orient('bottom')
        .tickPadding(8);

    var yAxis = d3.svg.axis()
        .scale(y)
        .orient('left')
        .tickPadding(8)
        .tickSize(0);

    var svg = d3.select('#chart-1').append('svg')
        .attr('width', width)
        .attr('height', height)
        .attr('class', 'chart')
      .append('g')
        .attr('transform', 'translate(' + margin.left + ', ' + margin.top + ')');

    svg.selectAll('.chart')
        .data(data)
      .enter().append('rect')
        .attr('class', 'bar')
        .attr('y', function(d, i) { return y(i) })
        .attr('width', x)
        .attr('height', y.rangeBand());

    svg.append('g')
        .attr('class', 'x axis')
        .attr('transform', 'translate(0, ' + y.rangeExtent()[1] + ')')
        .call(xAxis);

    svg.append('g')
        .attr('class', 'y axis')
        .call(yAxis)
      .selectAll('text')
        .text(function(d) { return String.fromCharCode(d + 65); });

  }

  draw();

  $(window).resize(function() {
    draw();
  });

})();
</script>


