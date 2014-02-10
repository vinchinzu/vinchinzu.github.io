---
layout: post
title: "Trade Network Graphs"
description: ""
category: r
tags: [R, tutorial]
---

### Step 1

Gather data from UN Comtrade. 
  1. Choose your 

[here](http://comtrade.un.org/monthly/Main/Data.aspx)
Create a log-in and account 
This allows downloads of 5000 lines each, so each query has to be small enough to account for this. 
Design the query based on exporter, importer, commodity (ies), period, and trade flow (import/export). 
Search for the appropriate HTS codes for the commodity classification. 




### Step 2

Open the R Console and process the source file:

This script will directly process the csv file downloaded from Comtrade. 

Change the query file as needed. 

{% highlight r %}
library(network)
library(plyr)

# setwd("./Downloads")

df = read.csv("QueryResults (34).csv", header = TRUE,
                   stringsAsFactors = FALSE)

## Take only the trade

Ex.df = subset(df,subset = TradeFlowDescription == "Exports", 
                   c("ReporterDescription", "PartnerDescription", "Value"))
Ex.df <- Ex.df[!Ex.df$PartnerDescription == "World",]
Ex.df <- Ex.df[!Ex.df$ReporterDescription == "EU-27",]

Ex.df$Value <- Ex.df$Value/1000

## Sort the data and take only the top 80% of the trade
Ex.df = arrange(Ex.df, desc(Value))

Ex.df$sWine = scale(Ex.df$Value, center = FALSE)
Ex.df$cs = cumsum(as.numeric(Ex.df$Value))
Ex.df[Ex.df[, 1] == "China, Hong Kong SAR ", 1] = "China, Hong Kong SAR"

## Adjust Top Percentage to be included
Final.df = subset(Ex.df, cs < tail(Ex.df$cs, 1) * 0.9)

## Set edge and arrow size
Final.df$edgeSize = with(Final.df, Value/sum(Value))
Final.df$arrowSize = ifelse(Final.df$edgeSize * 30 < 0.5 , 0.5,
                                Final.df$edgeSize * 15)

## Create the network and plot
net = network(Final.df[, 1:2])
plot(net, displaylabels = TRUE, label.col = "steelblue",
     edge.lwd = c(Final.df$edgeSize) * 100,
     arrowhead.cex = c(Final.df$arrowSize),
     label.cex = 0.5, vertex.border = "white",
     vertex.col = "skyblue", edge.col = rgb(0, 0, 0, alpha = 0.5))





pdf(file='pdfplot')
plot(net, displaylabels = TRUE, label.col = "steelblue",
     edge.lwd = c(Final.df$edgeSize) * 100,
     arrowhead.cex = c(Final.df$arrowSize),
     label.cex = 0.5, vertex.border = "white",
     vertex.col = "skyblue", edge.col = rgb(0, 0, 0, alpha = 0.5))
dev.off()

{% endhighlight %}
