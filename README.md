# PyBer_Analysis

## Purpose
In an effort to gain a better understanding on how ride sharing programs vary based on the type of city that they are in, we are going to use pandas to create a summary dataframe based on the ride sharing data by city type (urban, suburban, rural).  We will also create a visual representation that will display the total fares per week for each city type.

## Results

### PyBer Summary DataFrame

The first thing that we are tasked with in our analysis is creating a PyBer summary dataframe.  In order to do this, we need to use pandas to calculate certain metrics.  The first thing that we want to calculate is the total number of rides made by each city type.  We can do this by simply using the ```groupby()``` function and then applying the count method to the ride_id column.  

```total_rides_by_type=pyber_data_df.groupby(["type"]).count().ride_id
total_rides_by_type
```

We then want to calculate the the total drivers and total fares paid based by city type.  We're able to simply do this using the groupby function and the sum method as shown below.

```
# 2. Get the total drivers for each city type
total_drivers_by_type=city_data_df.groupby(["type"]).sum().driver_count


total_drivers_by_type
#  3. Get the total amount of fares for each city type
total_fares_by_type=pyber_data_df.groupby(["type"]).sum().fare
```
We then calculated the average fare per ride as well as the average fare per driver both being based by city type.  The code used for this can be seen below.

```
#  4. Get the average fare per ride for each city type. 

average_fare_per_ride=pyber_data_df.groupby(["type"]).mean()["fare"]

average_fare_per_driver=pyber_data_df.groupby(["type"]).sum().fare/total_drivers_by_type
```

Once we calculated our 5 metrics, we simply added them all to a new dataframe and then formatted the values in the data in order to make it more presentable.

<img width="1145" alt="Screen Shot 2021-11-14 at 3 39 17 AM" src="https://user-images.githubusercontent.com/87248687/141673892-ccc19318-c5d1-4579-9a2a-23c0a584391f.png">


### Total Fares Based By City Type
In our next analysis, we want to show a multiple line graph that shows how much fare was spent in each city type over a specific span of time.  In order to do this, we first decide to use another groupby function showing the sums of the fares based by each city type.  

```weekly_fares_by_city_type=pyber_data_df.groupby(["type", "date"]).sum()["fare"]```

Next, in order to create a pivot table, we remove the index on the newly created DataFrame.  We then are able to create a pivot table with columns for each city type as well as having the date as the index.  A snippet of the code we used can be found below.

``` weekly_fares_by_city_type_pivot = weekly_fares_by_city_type.pivot(index='date', columns='type', values='fare')```

<img width="1095" alt="Screen Shot 2021-11-14 at 3 47 57 AM" src="https://user-images.githubusercontent.com/87248687/141674145-a8b2ff01-badc-47c0-85c1-ed8deace15b0.png">

Once we've created the Pivot Table dataframe, we now will use the loc function to locate a specific range of dates and add them to a new dataframe.

```weekly_fares_2019=weekly_fares_by_city_type_pivot.loc['2019-01-01':'2019-04-29']```

We then just have to change the date index to the datetime datatype ```weekly_fares_2019.index=pd.to_datetime(weekly_fares_2019.index)``` and then we are able to resample the date index to include the total fare for each week by city type in the dataframe.  The new df shows the last day of each week in the index column.

<img width="1086" alt="Screen Shot 2021-11-14 at 3 54 03 AM" src="https://user-images.githubusercontent.com/87248687/141674317-0512abbd-f671-45e6-acf2-5a714c109529.png">

With the df in this new week format, we can now have a more articulate graph.  By importing the fivethirtyeight graph style from matplotlib, we can now use that style for the graph that we are about to plot.  The code below shows how we specified the axis labels, plot title, as well as specified the size for the plot.

```# Import the style from Matplotlib.
from matplotlib import style
# Use the graph style fivethirtyeight.
style.use('fivethirtyeight')


weekly_fares_2019_w.plot(figsize=(20,10))

plt.title("Total Fare By City Type")
plt.ylabel("Fare ($USD)")
plt.xlabel("")
plt.legend(loc=10)
plt.savefig("analysis/PyBer_fare_summary.png")

plt.show()
```

The complete graph can be seen below.

<img width="1161" alt="Screen Shot 2021-11-14 at 3 59 18 AM" src="https://user-images.githubusercontent.com/87248687/141674461-1a40bfda-32d3-41fa-b386-0bb2513e0aee.png">

## Summary

Based on the results from this analysis, I believe there are a few things that can be implemented to help reduce some of the dispairities between the different city types.  There are significantly less rides taken within rural and suburban cities than there are in urban cities.  Here are a few ways that we can attempt to change this:

1. Based on the data, the number of rides taken during the year is directly correlated with the amount of available drivers in the city.  If we can attract more available rideshare drivers in rural rural and suburban cities we may be able to generate more rides and thus generate more fares.
2. There is also a correlation between the average fare per ride and the amount of rides taken.  In suburban and rural cities the average fare per ride is significantly higher than that of urban cities.  This may prove to be one of the biggest factors why there are significantly more rides in urban cities than there is in rural and suburban cities.  If we can lower the fare prices in rural and suburban cities then we may be able to positively affect the amount of rides people take with this ride sharing service.
3. One of the biggest disparities present in our data is the difference in the average fare per driver in urban cities versus that of rural and suburban cities.  The average fare per driver is almost double or triple in rural and suburban cities.  This could be due to the length of rides in suburban and rural areas.  The big fare disparity could pose as a problem for retaining drivers in urban settings long term.  One solution to counteract this problem could be the implementation of a surge pricing model.  This would increase the price for fares in areas of high demand during periods of high demand.  This would positively influence the amount of active drivers as well as help raise the average fare they would generate.



