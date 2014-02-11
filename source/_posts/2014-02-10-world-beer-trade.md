---
layout: post
title: "World Beer Trade"
date: 2014-02-10 10:51:57 -0600
comments: true
categories: 
---
*Problem:* Finding a good way to visually analyze world trade data. The UN Comtrade website has a great interfacte to download current data (2010-Present) from most countries. However, how can we make sense of severel hundred thousand lines of data from a top down view. 

*Solution:* Using the R statistical programming language, it is easy to condense this data into a network graph that shows the density of trade between nations and the linkages for different producucts.
An outline is found [here](2014/02/05/trade-network-graphs/)
As an examples, I have downloaded January 2013 data for Beer ([HTS code](http://hts.usitc.gov/): 2203) he graph below shows a network graph of international trade between nations for Beer.

Primary Analysis:	
1. The trade is limited to the top 80% of interactions so that the smaller countries do not clutter up the image. 
2. It is immediately apparent 

![center](/figs/2014-02-10-world-beer-trade/tradeplot.png) 


[pdf download](/figs/2014-02-10-world-beer-trade/pdfplot.pdf)
