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

'|   OBS |   YEAR |   MONTH | U.S._STATE   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | CAUSE.CATEGORY     |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   TOTAL.PRICE |   TOTAL.CUSTOMERS |   POPULATION |   POPDEN_URBAN |   POPDEN_RURAL | OUTAGE.START        | OUTAGE.RESTORATION   |   OUTAGE.DURATION.HOURS |\n|------:|-------:|--------:|:-------------|:-------------------|----------------:|:-------------------|:-------------------|------------------:|-----------------:|---------------------:|--------------:|------------------:|-------------:|---------------:|---------------:|:--------------------|:---------------------|------------------------:|\n|     1 |   2011 |       7 | Minnesota    | East North Central |            -0.3 | normal             | severe weather     |              3060 |              nan |                70000 |          9.28 |           2595696 |      5348119 |           2279 |           18.2 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |              51         |\n|     2 |   2014 |       5 | Minnesota    | East North Central |            -0.1 | normal             | intentional attack |                 1 |              nan |                  nan |          9.28 |           2640737 |      5457125 |           2279 |           18.2 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |               0.0166667 |\n|     3 |   2010 |      10 | Minnesota    | East North Central |            -1.5 | cold               | severe weather     |              3000 |              nan |                70000 |          8.15 |           2586905 |      5310903 |           2279 |           18.2 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |              50         |\n|     4 |   2012 |       6 | Minnesota    | East North Central |            -0.1 | normal             | severe weather     |              2550 |              nan |                68200 |          9.19 |           2606813 |      5380443 |           2279 |           18.2 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |              42.5       |\n|     5 |   2015 |       7 | Minnesota    | East North Central |             1.2 | warm               | severe weather     |              1740 |              250 |               250000 |         10.43 |           2673531 |      5489594 |           2279 |           18.2 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |              29         |


### EDA




