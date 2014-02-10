---
layout: post
title: "HighCharts"
date: 2014-02-10 12:58:18 -0600
comments: true
categories: 
---
<script src="http://code.highcharts.com/stock/highstock.js"></script>
<script src="http://code.highcharts.com/stock/modules/exporting.js"></script>





Methods for adding highcharts graphics into Octopress posts. 

* Found from tutorial [here](http://www.alexpotrykus.com/blog/2013/11/21/using-highcharts-and-highstocks-with-octopress/)
* Trick is to add Javascript library to source, and add link on post 
* Also add id 'container' to render to. 

<div id="container" style="height: 500px; min-width: 500px"></div>

<script>

$(function() {
	$.getJSON('http://www.highcharts.com/samples/data/jsonp.php?filename=aapl-ohlcv.json&callback=?', function(data) {

		// split the data set into ohlc and volume
		var ohlc = [],
			volume = [],
			dataLength = data.length;
			
		for (i = 0; i < dataLength; i++) {
			ohlc.push([
				data[i][0], // the date
				data[i][1], // open
				data[i][2], // high
				data[i][3], // low
				data[i][4] // close
			]);
			
			volume.push([
				data[i][0], // the date
				data[i][5] // the volume
			])
		}

		// set the allowed units for data grouping
		var groupingUnits = [[
			'week',                         // unit name
			[1]                             // allowed multiples
		], [
			'month',
			[1, 2, 3, 4, 6]
		]];

		// create the chart
		$('#container').highcharts('StockChart', {
		    
		    rangeSelector: {
		        selected: 1
		    },

		    title: {
		        text: 'AAPL Historical'
		    },

		    yAxis: [{
		        title: {
		            text: 'OHLC'
		        },
		        height: 200,
		        lineWidth: 2
		    }, {
		        title: {
		            text: 'Volume'
		        },
		        top: 300,
		        height: 100,
		        offset: 0,
		        lineWidth: 2
		    }],
		    
		    series: [{
		        type: 'candlestick',
		        name: 'AAPL',
		        data: ohlc,
		        dataGrouping: {
					units: groupingUnits
		        }
		    }, {
		        type: 'column',
		        name: 'Volume',
		        data: volume,
		        yAxis: 1,
		        dataGrouping: {
					units: groupingUnits
		        }
		    }]
		});
	});
});


</script>


