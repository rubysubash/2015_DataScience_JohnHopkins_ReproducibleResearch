> 

**** Reproducible Research - Course project 2****
-------------------------------------------------


**NOAA Storm Database Report - Weather Events in the US Between 1950 and 2011**

***
***Synopsis***



In this report, I aim to describe several weather events in the United States between the years 1950 and 2011. The data are taken from the U.S. National Oceanic and Atmospheric Administration's (NOAA) and include weather events informations, like when and where they occur, estimates of any fatalities, injuries and property damage. The goal is to analyze the data and answer the following questions:

1- Across the United States, which types of events are most harmful with respect to population health?

2- Across the United States, which types of events have the greatest economic consequences?

After loading and processing the data, I came to the following conclusions:

1- Tornado is the most harmful event in terms of human fatalites and injuries;

2- Floods have the greatest economic consequences.


***Data Processing***



The data are downloaded from NOAA Storm Database, if necessary, and read to R.

    if (!file.exists('./storm_data')) { dir.create('./storm_data') }
    if (!file.exists("storm_data/repdata-data-StormData.csv.bz2")) {
      download.file("https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2", destfile="storm_data/repdata-data-StormData.csv.bz2", mode = "wb",  method = "curl")
    }
    if (!exists('storm_data')) {
      storm_data <- read.csv("storm_data/repdata-data-StormData.csv.bz2")
The required packages are plyr and ggplot2:

    ## Loading required package: plyr
    ## Loading required package: ggplot2
A brief summary of the data can be obtained using the following code.

    summary(storm_data)
The selected variables, which are more likely to address the initial questions are: fatalities, injuries, property damage and crop damage.
**

    summary(storm_data$FATALITIES)
##   

      Min.    1st Qu.   Median   Mean  3rd Qu. Max. 
     0.0000   0.0000    0.0000   0.0168 0.0000  583.0000
The summary of  injuries can be got using the following code.

    summary(storm_data$INJURIES)
  
  
  The result obtained is given below:
    
   Min.           1st Qu.     Median        Mean   3rd Qu.        Max. 
      0.0000        0.0000    0.0000    0.1557    0.0000 1700.0000

    summary(storm_data$PROPDMG)

According to the documentation (page 12), the PROPDMG and CROPDMG variables are encoded by PROPDMGEXP and CROPDMGEXP variables, respectively. They represent magnitude values, including "H" for hundreds, "K" for thousands, "M"" for millions and "B" for billions. In order to decode PROPDMG and CROPDMG, I created new numeric variables.

    storm_data$PROPMULT <- 1
    storm_data$PROPMULT[storm_data$PROPDMGEXP =="H"] <- 100
    storm_data$PROPMULT[storm_data$PROPDMGEXP =="K"] <- 1000
    storm_data$PROPMULT[storm_data$PROPDMGEXP =="M"] <- 1000000
    storm_data$PROPMULT[storm_data$PROPDMGEXP =="B"] <- 1000000000
    
    storm_data$CROPMULT <- 1
    storm_data$CROPMULT[storm_data$CROPDMGEXP =="H"] <- 100
    storm_data$CROPMULT[storm_data$CROPDMGEXP =="K"] <- 1000
    storm_data$CROPMULT[storm_data$CROPDMGEXP =="M"] <- 1000000
    storm_data$CROPMULT[storm_data$CROPDMGEXP =="B"] <- 1000000000

**Results**

    aggregate_data <- ddply(.data = storm_data, .variables = .(EVTYPE), fatalities = sum(FATALITIES), injuries = sum(INJURIES), property_damage = sum(PROPDMG * PROPMULT), crop_damage = sum(CROPDMG * CROPMULT), summarize)
    
    population_data <- arrange(aggregate_data, desc(fatalities + injuries))
    damage_data <- arrange(aggregate_data, desc(property_damage + crop_damage))

**Question 1: Across the United States, which types of events are most harmful with respect to population health?**

    ggplot(data = head(population_data, 15), aes(x = factor(EVTYPE), y = (fatalities + injuries), fill = EVTYPE)) + geom_bar(stat="identity") + coord_flip() + labs(y = "Injuries and fatalities", x = "Event type", title = "Injuries and fatalites per event type across US")

![enter image description here](https://lh3.googleusercontent.com/-QbeWmHTgVZU/VsP5eR2_rQI/AAAAAAAAAEI/1-ehbiPcNJY/s0/Proj2.fig1.JPG "Proj2.fig1.JPG")

**Question 2: Across the United States, which types of events have the greatest economic consequences?**

    ggplot(data = head(damage_data, 15), aes(x = factor(EVTYPE), y = (property_damage + crop_damage), fill = EVTYPE)) + geom_bar(stat="identity") + coord_flip() + labs(y = "Property and crop damage", x = "Event type", title = "Property and crop damage by event type accross the US")



![enter image description here](https://lh3.googleusercontent.com/-HT5mA5ZsUEg/VsP5wf4vqcI/AAAAAAAAAEQ/S1bS_X38iu0/s0/Prof_fig2.JPG "Prof_fig2.JPG")

**Conclusion**

**Question 1: Across the United States, which types of events are most harmful with respect to population health?**

Tornadoes are responsible for the largest proportion of both deaths and injuries out of all event types. 

**Question 2: Across the United States, which types of events have the greatest economic consequences?**

Flooding is responsible for the largeset proportion of total economic impact out of all event types.