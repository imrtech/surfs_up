# surfs_up

## Overview of the Analysis

In this project we analyzed precipitation trends over the course of a year. The way we get the temperature data is by running two separate queries using SQLAlchemy to connect to and query a SQLite database. We also used a Flask application to run quick queries and visualize our data in a browser. To get a better understanding of the weather trends we also needed to look at temperture during the year, specifically for the months of June and December to see if operating a surf shop during the year is sustainable. 


## Results

### Deliverable 1:

We ran the following analysis:
- We queried precipitation and temperature data for 12 months. 
- We also ran statistical analysis—such as the mean, standard deviation, minimum, and maximum for each weather feature.
- We queried the most active stations

```
# Design a query to retrieve the last 12 months of precipitation data and plot the results. 
#Starting from the last data point in the database. 
# Calculate the date one year from the last date in data set.
# Perform a query to retrieve the data and precipitation scores

prev_year = dt.date(2017, 8, 23) - dt.timedelta(days=365)

results = []
results = session.query(Measurement.date, Measurement.prcp).filter(Measurement.date >= prev_year).all()
print(results)

# Save the query results as a Pandas DataFrame and set the index to the date column
df = pd.DataFrame(results, columns=['date','precipitation'])
df.set_index(df['date'], inplace=True)
print(df.to_string(index=False))


# Sort the dataframe by date
df = df.sort_index()

# Use Pandas Plotting with Matplotlib to plot the data
df.plot()
```

![This is an image](/resources/precipitation12mos.png)

```
df.describe()
```

![This is an image](/resources/precipitationstats.png)

We ran temperature statistics for 12 months and created a histogram:

```
# Choose the station with the highest number of temperature observations.
# Query the last 12 months of temperature observation data for this station and plot the results as a histogram
results = session.query(Measurement.tobs).\
filter(Measurement.station == 'USC00519281').\
filter(Measurement.date >= prev_year).all()
print(results)

df = pd.DataFrame(results, columns=['tobs'])
print(df)


df.plot.hist(bins=12)
plt.tight_layout()

```

![This is an image](/resources/temp_hist.png)


We then developed a flash application that could be easily accessed via a browser to display routes for temperature observations for a year including precipitation, active station and temperature.


```
#################################################
# Database Setup
#################################################
from flask import Flask, jsonify
engine = create_engine("sqlite:///hawaii.sqlite")

# reflect an existing database into a new model
Base = automap_base()
Base.prepare(engine, reflect=True)
Measurement = Base.classes.measurement
Station = Base.classes.station

# Save reference to the table
session = Session(engine)

#################################################
# Flask Setup
#################################################
app = Flask(__name__)

#################################################
# Flask Routes
#################################################

@app.route("/")

def welcome():
    return(
    f"Welcome to the Climate Analysis API!<br/>"
    f"Available Routes:<br/>"
    f"/api/v1.0/precipitation<br/>"
    f"/api/v1.0/stations<br/>"
    f"/api/v1.0/tobs<br/>"
    f"/api/v1.0/temp/start/end"
    )

@app.route("/api/v1.0/precipitation")
def precipitation():
   prev_year = dt.date(2017, 8, 23) - dt.timedelta(days=365)
   precipitation = session.query(Measurement.date, Measurement.prcp).\
      filter(Measurement.date >= prev_year).all()
   precip = {date: prcp for date, prcp in precipitation}
   return jsonify(precip)

@app.route("/api/v1.0/stations")
def stations():
    results = session.query(Station.station).all()
    stations = list(np.ravel(results))
    return jsonify(stations=stations)

@app.route("/api/v1.0/tobs")
def temp_monthly():
    prev_year = dt.date(2017, 8, 23) - dt.timedelta(days=365)
    results = session.query(Measurement.tobs).\
      filter(Measurement.station == 'USC00519281').\
      filter(Measurement.date >= prev_year).all()
    temps = list(np.ravel(results))
    return jsonify(temps=temps)

@app.route("/api/v1.0/temp/<start>/<end>")
def stats(start=None, end=None):
    sel = [func.min(Measurement.tobs), func.avg(Measurement.tobs), func.max(Measurement.tobs)]

    if not end:
        results = session.query(*sel).\
            filter(Measurement.date >= start).all()
        temps = list(np.ravel(results))
        return jsonify(temps)

    results = session.query(*sel).\
        filter(Measurement.date >= start).\
        filter(Measurement.date <= end).all()
    temps = list(np.ravel(results))
    return jsonify(temps)
```

We were asked to find the temperature trends for the month of June:

![This is an image](/resources/june_temps.png)

- The results demonstrated ideal rainfall for the surf shop location. 
- Out of the 9 stations, station USC00519281 was the most active. 
- The average temperature in the month of June is 75.
- The range was between 64 degrees and 85 degrees.

### Deliverable 2: 

Finally, we analyzed the temperatures in the month of December.

![This is an image](/resources/dec_temps.png)

- The average temperature in the month of December is 71. 
- The range was between 56 degrees and 83 degrees.

## Summary:

At a quick glance, it seems like the surf shop would perform well given the fair weather during these periods. While the lowest temperature in December, 56 degrees, may not be conducive to surfing, it's not enough to stop from moving ahead with the opening of the surf shop. Overall, the temeratures in June and December are similar and rain doesn't appear to impact the conditions. 