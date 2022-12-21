Module 5 Deliverable 3
# PyBer Analysis

## Overview of Project
This project represents a program written in Python 3 with the goal of analyzing trends within the data regarding a fictional ridesharing company, "PyBer" which has operations within many cities of various types and contracts numerous drivers within each.  The corpus itself consists of two separate CSV datasets, one describing the aforementioned cities and another documenting the rides transacted therein by the company.  The cities are each described by name, type (e.g.; "Urban", "Suburban", or "Rural") and the number of drivers contracted therein.  The ride data comprises documentation of the name of the city where each individual ride occurred, the date and time it took place, the fare transacted, and the unique ID number assigned for tracking purposes.  Both the data corpus and the analysis are included in this repository alongside the program script.

### Purpose
The purpose of this project's analysis was to use the Pandas and Matplotlib libraries in Python to produce a multiple-line graph data visualization comparing the total weekly fares transacted by all drivers within all cities of each descriptive type, and to draw useful conclusions from this comparison.   

## Results
The first step in producing the desired results for this project was merging the two aforementioned datasets into a single dataframe using Pandas.  
```
pyber_data_df = pd.merge(ride_data_df, city_data_df, how="left", on=["city", "city"])
```
The combined dataset containing all relevant information for analysis appeared in the format seen below:
![Merged Dataset](https://github.com/AC-Melamed/PyBer_Analysis/blob/main/analysis/MergedDataset.png)

The next step was to find the total sums of all rides, drivers, and fares for each city type.  This was accomplished using the '.groupby' function in combination with the '.count' and '.sum' functions.  Then these totals could be used to find the averages fare per ride and per driver for each city type by simple division.  With these values calculated, it was possible to produce a new Pandas dataframe summarizing the entire data corpus.
```
pyber_summary_df = pd.DataFrame({
    "Total Rides":ridesTotal,
    "Total Drivers":driversTotal,
    "Total Fares":faresTotal,
    "Average Fare per Ride":faresAvg_perRide,
    "Average Fare per Driver":faresAvg_perDriver
                                })
```
The resulting dataframe appeared as seen below:
![Summary Dataframe](https://github.com/AC-Melamed/PyBer_Analysis/blob/main/analysis/SummaryDataframe.png)

This new dataframe included a redundant index label however, which was removed for visual clarity.
```
pyber_summary_df.index.name = None
```
Additionally, the values displayed in the dataframe's columns lacked certain typological conventions suitable for the data being described, i.e., dollar signs for monetary values and commas for quantities in the thousands.  These were also included for clarity.
```
pyber_summary_df["Total Fares"] = pyber_summary_df["Total Fares"].map("${:,.2f}".format)
pyber_summary_df["Average Fare per Ride"] = pyber_summary_df["Average Fare per Ride"].map("${:.2f}".format)
pyber_summary_df["Average Fare per Driver"] = pyber_summary_df["Average Fare per Driver"].map("${:.2f}

pyber_summary_df = pyber_summary_df.style.format(precision=2, thousands=',')
```
The thusly reformatted dataframe appeared as seen below:
![Summary Dataframe CLEAN](https://github.com/AC-Melamed/PyBer_Analysis/blob/main/analysis/SummaryDataframeCLEAN.png)

Next, in order to prepare the data for visualization using the '.pivot' function, a second new dataframe was produced from the previous one using the '.groupby' function to group the data by city type, and the '.reset_index' function to reassign the new dataframe a numerical index.  
```
faresSummed_df = pyber_data_df.groupby(['type','date']).sum()['fare']
```
```
faresSummed_df = faresSummed_df.reset_index()
```
From there, it became possible using the '.pivot' function to reorient the dataframe such that the city type categories became the column headers, and the datetime value for each ride became the index.    
```
faresSummed_df_Pivot = faresSummed_df.pivot(index='date', columns='type',values='fare')
```
```
faresSummed_df_Pivot.index = pd.to_datetime(faresSummed_df_Pivot.index)
```
This process additionally made it easy to limit the dataframe was to rides transacted during a particular three month period in 2019, sacrificing sample size for computing speed and concision.  From this formatted dataframe, a second new dataframe could then be derived using the '.resample' function to display the summed fares for each week during the selected three month period, still grouped by city type.
```
faresSummed_df_Pivot_perWeek = faresSummed_df_Pivot.resample("W").sum()
```
![Summed Fares Dataframe per Week](https://github.com/AC-Melamed/PyBer_Analysis/blob/main/analysis/SummedFaresDataframeWEEKLY%20.png)

Finally, from this last dataframe, the desired visualization could be produced according to the specified parameters.  
```
fig, ax = plt.subplots(figsize=(15,5))
faresSummed_df_Pivot_perWeek.plot(ax=ax)
ax.set_xlabel("")
ax.set_ylabel("Fare ($USD)")
ax.set_title("Total Fare by City Type")

ax.legend(loc='center')

from matplotlib import style
style.use('fivethirtyeight')
```
![Data Comparison Visualization](https://github.com/AC-Melamed/PyBer_Analysis/blob/main/analysis/PyBer_fare_summary.png)

#### Comparison of Ridesharing Data Between City Types
From the merged dataframe it is apparent that urban type cities greatly exceed all other types in terms of total rides, drivers, and fares, while being in turn exceeded in terms of average fare per ride and per driver.  Rural type cities rank last in terms of total rides, drivers, and fares, but first in terms of average fare per ride and per driver; the latter value represents nearly four times that of suburban type cities, which in turn represents nearly three and a half times that of urban type cities.
From the line chart visualization it is apparent that the total fare trends for each city type are also correlated slightly as a whole with the time of year; specifically, all cities experience a sharp increase in total fares during the end of February.  This could possibly be due to the state holidays during that period, during which workers would be given days off for travel and leisure, potentially leading to increased traffic and therefore increased ride totals and/or fares.  It is also notable that urban and suburban cities display a rising trend in total fares from the beginning of January to roughly a third of the way through the month, while rural cities exhibit the inverse.  This could potentially be the result of another holiday-based phenomenon, in which case it would be assumed that urban and suburban riders return from their end of year vacations around that time while rural riders wait until roughly halfway through the month when the chart shows a spike in that particular subset of the data.      

## Summary
Extrapolating from the comparative trends observed within the provided data, this project can propose three different potential strategies for optimizing rideshare operations.  Without knowing more specifically what the priorities and goals of the company are beyond net profit maximization, it is difficult to judge the propriety of any given possible suggestion.  However, the below options should prove at least somewhat effective at flattening the difference in fare rates between the various city types if nothing else.   

#### Business Recommendations
1)  Labor-side incentives for intercity travel.  By granting a higher percentage of fare transactions to drivers who leave their urban home cities to offer rides between there and rural or suburban areas, the imbalance in the labor pool between the city types could be somewhat ameliorated, and the lost profit potentially recouped overall via the increase in longer and therefore more expensive rides between cities.     
2)  City type-specific referral program.  To boost the labor pool in rural and suburban areas, a referral program could be highly effective, especially one targeted towards customers.  Since the drivers in rural and suburban areas transact a greater number of rides per driver than those in urban areas, their exposure to customers is presumably overall more frequent, requiring less overall training for the proposed program while yielding greater returns on that investment. 
3)  Flat increases in city type-specific rates.  The simplest and most straightforward means of leveling the difference between the most underforming rural and suburban cities and the company's urban operations would be to implement a flat fare hike for those former areas.  Given the assumption that rural and suburban residents without other means of transportation would be inherently more reliant on rideshares than urban residents with access to public transit, and given also that the data shows rural and suburban drivers already perform higher rates of ride transactions than their urban counterparts, it might be ventured that the labor of those workers could be valued higher than current rates and greater profits thereby yielded without a proportional loss in business.    

