# surfs_up

## Overview of the Analysis

In this project we analyzed temperature trends for the months of June and December to see if operating a surf shop during the year is sustainable. The way we get the temperature data is by running two seperate queries using SQLAlchemy to connect to and query a SQLite database. We also used a Flask application to run quick queries and visualize our data in a browser.


## Results

### Deliverable 1:

The following query was run to summarize the temperatures in June.

![This is an image](/resources/june_temps.png)


### Deliverable 2: 

The following query was run to summarize the temperatures in December.

![This is an image](/resources/dec_temps.png)

## Summary:

The average temperature in the month of June is 75. While the average temperature in the month of December is 71. At a quick glance, it seems like the surf shop would perform well given the fair weather during these periods. However, if you look at the month of December, the temperature range is 56 degrees to a max of 83 degrees. The low temperature in December is not conducive to surfing.

We should run additional queries for other months and include precipitation results. We should also look at the station locations closest to the proposed surf shop as temperature and precipitation will differ.  