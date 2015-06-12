---
layout: post
title: "World Beer Trade Update"
date: 2015-06-09 07:23:00 -0500
comments: true
categories:
---


*Problem:* I last updated the world beer trade in Feb 2014. The comtrade webstie now has a nice API interface that are linked to the databases.
The old database setup allowed for 1000 records per query, but the new API allows for 50000 records per request. 

*Solution:* A guide to the UN Comtrade data API with R can be found [here](http://comtrade.un.org/data/Doc/api/ex/r)


Sample download to csv or 
http://comtrade.un.org/api/get?max=50000&type=C&freq=M&px=HS&ps=201504&r=826&p=0&rg=all&cc=AG2&fmt=csv


Getting Json, csv, and 
{% highlight r %}
library("rjson")
s1 <- get.Comtrade(r="842", p="124,484")
s2 <- get.Comtrade(r="842", p="124,484", fmt="csv")
#s3 <- get.Comtrade(r="842", p="124,484",ps="201501,201502,201503", freq="M", cc="2203",fmt="csv")
s4 <- get.Comtrade(r="842", p="all",ps="201503", freq="M", cc="2203",fmt="csv")

#Get multiple countries, monthly data, by month: rg=2 ("exports"), cc="2203" ("beer")
s5 <- get.Comtrade(r="842,276", rg="2",p="all",ps="201403", freq="M", cc="2203",fmt="csv", max="50000")

#Notes not all data my present, for examples German data from two months ago, not yet listed. 


{% endhighlight %}

Set up the network graphing

{% highlight r %}


library(network)
library(plyr)


df = s4
## Take only the trade


#Update new column names from new API format. ("ReporterDescription == Reporter")
Ex.df = subset(df,subset = Trade.Flow == "Exports", 
                   c("Reporter", "Partner", "Trade.Value..US.."))
Ex.df <- Ex.df[!Ex.df$Partner== "World",]
Ex.df <- Ex.df[!Ex.df$Reporter == "EU-27",]

Ex.df$Value <- Ex.df$Trade.Value..US../1000

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




{% highlight r %}
get.Comtrade <- function(url="http://comtrade.un.org/api/get?"
                         ,maxrec=50000
                         ,type="C"
                         ,freq="A"
                         ,px="HS"
                         ,ps="now"
                         ,r
                         ,p
                         ,rg="all"
                         ,cc="TOTAL"
                         ,fmt="json"
)
{
  string<- paste(url
                 ,"max=",maxrec,"&" #maximum no. of records returned
                 ,"type=",type,"&" #type of trade (c=commodities)
                 ,"freq=",freq,"&" #frequency
                 ,"px=",px,"&" #classification
                 ,"ps=",ps,"&" #time period
                 ,"r=",r,"&" #reporting area
                 ,"p=",p,"&" #partner country
                 ,"rg=",rg,"&" #trade flow
                 ,"cc=",cc,"&" #classification code
                 ,"fmt=",fmt        #Format
                 ,sep = ""
  )
  
  if(fmt == "csv") {
    raw.data<- read.csv(string,header=TRUE)
    return(list(validation=NULL, data=raw.data))
  } else {
    if(fmt == "json" ) {
      raw.data<- fromJSON(file=string)
      data<- raw.data$dataset
      validation<- unlist(raw.data$validation, recursive=TRUE)
      ndata<- NULL
      if(length(data)> 0) {
        var.names<- names(data[[1]])
        data<- as.data.frame(t( sapply(data,rbind)))
        ndata<- NULL
        for(i in 1:ncol(data)){
          data[sapply(data[,i],is.null),i]<- NA
          ndata<- cbind(ndata, unlist(data[,i]))
        }
        ndata<- as.data.frame(ndata)
        colnames(ndata)<- var.names
      }
      return(list(validation=validation,data =ndata))
    }
  }
}{% endhighlight %}



