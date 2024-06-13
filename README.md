# Predictive Analysis on Power Outages
Project for DSC 80 at UC San Diego

By Ken Ogihara

## Introduction
In this project, I analyze a dataset that includes major power outages across the U.S from 2000-2016. This dataset is from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure. This data contains information on the geographical location of the outages, climactic data, and electricity consumption patterns and economic characteristics of the states affected by the outages. 

This project revolves around one question: "What risk factors should an energy company want to look into when predicting the location and severity of its next major power outage?" To answer this question, I will first perform basic exploratory data analysis, assess missingness dependency, and finally build a model that predicts whether a power outage is caused by the weather or an intentional attack. This is important for the Department of Education and private energy companies to know because it allows them to take specific measures to prevent the onset of the next power outage. This dataset contains 1534 rows and 57 columns but for the sake of this project I will focus on the following columns:

| Column | Description |
| ----------- | ----------- |
| OBS | Observation number |
| YEAR | Year in which an outage occurred |
| MONTH | Month in which an outage occurred |
| U.S._STATE | State in which an outage occurred |
| CLIMATE.REGION | Region of the U.S in which an outage occurred |
| ANOMALY.LEVEL | Oceanic Niño Index(ONI) used for identifying cool and warm seasons in the tropical Pacific |
| CLIMATE.CATEGORY | Temperature at the time of an outage (normal, cold, warm) |
| OUTAGE.DURATION | Duration of an outage (minutes) |
| OUTAGE.START.DATE | Day of the week in which an outage occurred |
| OUTAGE.START.TIME | Time of the day in which an outage occurred |
| OUTAGE.RESTORATION.DATE | Day of the week in which an outage was restored |
| OUTAGE.RESTORATION.TIME | Time of the day in which an outage was restored |
| CAUSE.CATEOGORY | Cause of an outage |
| CUSTOMERS.AFFECTED | Number of people that were affected from an outage |
| TOTAL.PRICE | Average monthly electricity cost in that state (cents/kilowatt-hour) |
| TOTAL.CUSTOMERS | Total number of customers in that state |
| POPULATION | Total population of that state |
| POPDEN_URBAN | Population density of the urban areas |
| POPDEN_RURAL | Population density of the rural areas |
| DEMAND.LOSS.MW | Amount of demand lost in an outage (megawatt) |


## Data Cleaning and Exploratory Data Analysis

### Cleaning

The cleaning process is divided into 4 main parts:

1. **Removing irrelevant columns** I kept only the columns that are relevant to the question at hand. These are the columns I am working with: OBS, YEAR, MONTH, U.S._STATE, CLIMATE.REGION, ANOMALY.LEVEL, CLIMATE.CATEGORY, OUTAGE.DURATION, OUTAGE.START.DATE, OUTAGE.START.TIME, OUTAGE.RESTORATION.DATE, OUTAGE.RESORATION.TIME, CAUSE.CATEGORY, CUSTOMERS.AFFECTED, TOTAL.PRICE, TOTAL.CUSTOMERS, POPULATION POPDEN_URBAN, POPDEN_RURAL.

2. **Combining columns** I combined OUTAGE.START.DATE and OUTAGE.START.TIME into a pd.Timestamp column called OUTAGE.START. Similarly, I combined OUTAGE.RESTORATION.DATE and OUTAGE.RESTORATION.TIME into a pd.Timestamp column called OUTAGE.RESTORATION. 

3. **Replacing values** Dependent variables include OUTAGE.DURATION, CUSTOMERS.AFFECTED, and DEMAND.LOSS.MW. I replaced all 0's in these columns with np.nan. It does not make sense for these columns to have 0's. For example, it is highly unlikely that an outage affected 0 people or lasted 0 minutes.

4. **Creating columns** I created a new column, OUTAGE.DURATION.HOURS by dividing the OUTAGE.DURATION column by 60 since it is easier to visualize values in hours than in minutes.

The first few rows of this cleaned DataFrame is shown below:

```py
print(outage.head()[["YEAR", "U.S._STATE", "CLIMATE.REGION", "CAUSE.CATEGORY", "CUSTOMERS.AFFECTED", "OUTAGE.DURATION.HOURS"]].to_markdown(index = False))
```

|   YEAR | U.S._STATE   | CLIMATE.REGION     | CAUSE.CATEGORY     |   CUSTOMERS.AFFECTED |   OUTAGE.DURATION.HOURS |
|-------|-------------|-------------------|-------------------|---------------------|------------------------|
|   2011 | Minnesota    | East North Central | severe weather     |                70000 |                    51   |
|   2014 | Minnesota    | East North Central | intentional attack |                  nan |              0.0166667 |
|   2010 | Minnesota    | East North Central | severe weather     |                70000 |                    50   |
|   2012 | Minnesota    | East North Central | severe weather     |                68200 |                  42.5   |
|   2015 | Minnesota    | East North Central | severe weather     |               250000 |                    29   |


### EDA

I first create a univariate plot that shows the number of outages between 2000-2016.

<iframe
  src="assets/html.plot1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


My second univariate plot shows the distribution of the average power outage time in hours for each cause category.

<iframe
  src="assets/html.plot2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

My third univariate plot shows the distribution of outages for each cause category.

<iframe
  src="assets/html.plot3.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

My first bivariate plot shows the association between customers affected and outage duration in hours.

<iframe
  src="assets/html.plot4.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

My second bivariate plot shows the distribution of outage duration for each cause category.

<iframe
  src="assets/html.plot5.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Aggregates

I first created a pivot table that shows how many customers were affected by each cause category in each climate region.

| equipment failure | fuel supply emergency | intentional attack | islanding | public appeal | severe weather | system operability disruption |
|------------------:|----------------------:|-------------------:|----------:|--------------:|---------------:|------------------------------:|
|           87750   |                   nan |               775  |     9666.67 |         nan   |         148707 |                        210450 |
|             nan   |                   nan |              5941  |       nan  |        7600   |         134972 |                        759738 |
|           38101   |                     1 |             21375.5|       nan  |       18600   |         170482 |                        530759 |
|          46651.5  |                   nan |              2500  |       nan  |        8000   |         169284 |                         35000 |
|          62721.7  |                   nan |              6257  |     14500  |      13523.5  |         223231 |                        227102 |
|          145420   |                   nan |               nan  |       nan  |         nan   |         217788 |                         75555.5 |
|          55666.7  |                   nan |            2837.67 |     35230  |         nan   |         99328.2|                        135656 |
|          198608   |                   nan |             47804  |     5459.12|         nan   |         367489 |                        152040 |
|             nan   |                   nan |               nan  |       nan  |       34500   |          74178 |                           nan |


I created another pivot table that shows the number of outages per cause category per climate region.

| equipment failure | fuel supply emergency | intentional attack | islanding | public appeal | severe weather | system operability disruption |
|------------------:|----------------------:|-------------------:|----------:|--------------:|---------------:|------------------------------:|
|                 7 |                     4 |                 38 |         3 |             2 |            135 |                             11 |
|                 3 |                     5 |                 20 |         1 |             2 |            104 |                              3 |
|                 5 |                    14 |                135 |         1 |             4 |            176 |                             15 |
|                 2 |                     1 |                 89 |         5 |             2 |             29 |                              4 |
|                10 |                     7 |                 28 |         2 |            42 |            113 |                             27 |
|                 5 |                   nan |                  9 |       nan |             5 |            118 |                             16 |
|                 5 |                     2 |                 64 |         1 |             1 |             10 |                              9 |
|                21 |                    17 |                 31 |        28 |             9 |             70 |                             41 |
|                 1 |                     1 |                  4 |         5 |             2 |              4 |                            nan |