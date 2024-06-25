# Predictive Analysis on Power Outages
Project for DSC 80 at UC San Diego

By Ken Ogihara

## Introduction
In this project, I analyze a dataset that includes major power outages across the U.S from 2000-2016. This dataset is from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure. This dataset contains information on the geographical location of the outages, climactic data, and electricity consumption patterns and economic characteristics of the states affected by the outages. 

This project revolves around one question: "What risk factors should an energy company want to look into when predicting the location and severity of its next major power outage?" To answer this question, I will first perform basic exploratory data analysis, assess missingness dependency, and finally build a model that predicts whether a power outage is caused by the weather or an intentional attack. This is important for the Department of Education and private energy companies because it allows them to take specific measures to prevent the onset of the next power outage. This dataset contains 1534 rows and 57 columns but for the sake of this project I will focus on the following columns:

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

1. **Removing irrelevant columns** I will keep only the columns that are relevant to the question at hand. These are the columns I will work with: OBS, YEAR, MONTH, U.S._STATE, CLIMATE.REGION, ANOMALY.LEVEL, CLIMATE.CATEGORY, OUTAGE.DURATION, OUTAGE.START.DATE, OUTAGE.START.TIME, OUTAGE.RESTORATION.DATE, OUTAGE.RESORATION.TIME, CAUSE.CATEGORY, CUSTOMERS.AFFECTED, TOTAL.PRICE, TOTAL.CUSTOMERS, POPULATION POPDEN_URBAN, POPDEN_RURAL.

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

I first created a univariate plot that shows the number of outages between 2000 and 2016.

<iframe
  src="assets/html.plot1.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>


My second univariate plot shows the distribution of the average power outage time in hours for each cause category.

<iframe
  src="assets/html.plot2.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

My third univariate plot shows the distribution of outages for each cause category.

<iframe
  src="assets/html.plot3.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

My first bivariate plot shows the association between customers affected and outage duration in hours.

<iframe
  src="assets/html.plot4.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

My second bivariate plot shows the distribution of outage duration for each cause category.

<iframe
  src="assets/html.plot5.html"
  width="800"
  height="400"
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

## Assessment of Missingness

### NMAR Analysis

DEMAND.LOSS.MW is most likely NMAR because it measures the total
amount of electricity that was lost during the outage, which is a quantity that you may not be able to calculate 100% of the time. This means that the data was not even collected in the first place, implying that the missingness depends on the missing value itself.

There isn't really additional data that we can obtain from this scenario because lost energy is lost. The values will become MAR in the hypothetical scenario where we can figure out the values for the missingness. We can make a prediction that the missingness depends on TOTAL.CUSTOMERS. Perhaps, companies that have more customers have more profit which means they can make better investments into monitoring technology.

### Cause Category and Outage Duration

I checked if the missing values in the OUTAGE.DURATION.HOURS column is dependent on CAUSE.CATEGORY. Here's my hypothesis:

**Null Hypothesis:** The distribution of CAUSE.CATEGORY is similar when OUTAGE.DURATION.HOURS is missing and when it is not missing.

**Alternative Hypothesis:** The distributions are not similar and  the missingness of the OUTAGE.DURATION.HOURS column is indeed dependent on CAUSE.CATEGORY.

I created a pivot table to compute the observed test statistic (Total variation distance), then ran a permutation test on the CAUSE.CATEGORY column to compute the p-value.

<iframe
  src="assets/html.plot6.html"
  width="800"
  height="450"
  frameborder="0"
></iframe>

According to the figure, we can see that the observed test statistic is far from the permuted values. This is evidence that the alternative hypothesis is true and that the missingness of the OUTAGE.DURATION.HOURS column is dependent on CAUSE.CATEGORY. Therefore, we will reject the null hypothesis.

## Hypothesis Testing

Previously, I showed that outages caused by fuel supply emergencies are longer on average than outages caused by severe weather. We will test if this is true by implementing a permutation test again.

**Null Hypothesis:** There is no relationship between the two events. The differences are due to random chance.

**Alternative hypothesis:** Outages caused by fuel supply emergency are indeed worse on average than outages caused by severe weather. The differences are not due to random chance and the data from each case comes from different data generating processes.

<iframe
  src="assets/html.plot7.html"
  width="1000"
  height="400"
  frameborder="0"
></iframe>

I calculated a p-value of 0.0. Therefore, we reject the null hypothesis again. We can conclude that there is enough evidence that shows that outages caused by fuel supply emergency are indeed worse (longer) on average than outages caused by severe weather.

## Framing a Prediction Problem

Let's recall our research question.

**Question:** "What risk factors should an energy company want to look into when predicting the location and severity of its next major power outage?"

The distribution in my EDA analysis showed that 'severe weather' and 'intentional attack' were the two most common causes of power outages. This means that the probabilitiy of the next outage being caused by one of those two factors is relatively high.

I used decision tree classification to predict whether an outage was caused by severe weather or an intentional attack. Since we resticted the response variables to two outcomes, our predictive analysis is an example of **binary classification.**

I used F-1 score to determine the model's accuracy because this is an imbalanced classification problem. There are 750 cases that were caused by severe weather and 418 cases that were caused by intentional attacks. F-1 score's ability to handle imbalanced data sets justifies its use.


## Baseline Model

### Features 

I used two numerical features for the baseline model: ANOMALY.LEVEL and POPDEN_RURAL. Anomaly level is a metric used by the Oceanic Niño Index (ONI) used for identifying cool and warm seasons in the tropical Pacific. Anomaly levels indicate unusual conditions that may lead to particular outages. POPDEN_RURAL measures how densely popualated each rural area is (persons/square miles). Population density in rural areas could influence the likelihood of particular types of outages. For example, rural areas are more vulnerable to severe weather changes.

### Baseline Model Performance Metrics

Here are the performance metrics for the baseline model:

|                |   Baseline Model |
|:---------------|-----------------:|
| Training score |         0.915525 |
| Testing score  |         0.815068 |
| F1 score       |         0.860825 |

Our testing and F-1 score are pretty high at 82% and 86% respectively. However, since these are the results of the baseline model, we cannot determine how "good" it is unless we have another model to compare it to.

## Final Model

In my final model, I included three more features: POPDEN_URBAN, CLIMATE.CATEGORY, and YEAR. 

**POPDEN_URBAN:** Urban areas tend to have higher population densities, which can make them more attractive targets for intentional attacks. A larger concentraion of people and economic activitiy generally means that disruptions to the power supply can have significant impact.

**CLIMATE.CATEGORY:** Different climate categories (such as cold, warm, or normal) gives insight into weather conditions like storms, hurricanes, or extreme temperatures which often results in power disruptions.

**YEAR:** The year in which an outage occurs can capture trends and changes in infrastructure, technology, and laws, which may have a direct correlation with the freqency of outages causes.

I performed log transformation using FunctionTransformer on POPDEN_URBAN because its data is highly skewed. Next, I one-hot-encoded the CLIMATE.CATEGORY column since it's a categorical variable. I used GridSearchCV, a dictionary of hyperparameters, and k-fold cross validation of 5 to determine the best combiation of parameters.

### Model Performance Comparison

Here is a comparison of the performance metrics between the baseline model and the final model:

|                |   Baseline Model |   Final Model |
|:---------------|-----------------:|--------------:|
| Training score |         0.915525 |      0.964853 |
| Testing score  |         0.815068 |      0.874576 |
| F1 score       |         0.860825 |      0.902375 |

We can see that the model has improved in every aspect. However, we must perform a **fairness analysis** to determine if the model is fair for particular groups. 

## Fairness Analysis

My fairness analysis includes a permutation test that will determine the model's accuracy performance for two groups: states with an urban population density that's less than 2461 and states with an urban population density that's more than 2461. 

### Density Prediction Results

Here are the prediction results based on density classification:

| is_dense   |   Prediction |
|:-----------|-------------:|
| dense      |     0.548387 |
| not dense  |     0.673267 |


This is showing us that among densly urbanly populated states, 54% of cases were caused by severe weather. Meanwhile, 67% of cases among not densly urbanly populated states are attributed to severe weather.

### Density Classification F1 Accuracy

Here are the F1 accuracy scores based on density classification:

| is_dense   |   f1_accuracy |
|:-----------|--------------:|
| dense      |      0.899471 |
| not dense  |      0.899471 |


I ran a permutation test to determine if the difference in F-1 score is statistically significant between densly and not densly urbanly populated states.

**Null Hypothesis:** The classifier's F-1 is the same for both groups, and the differences are simply due to random chance.

**Alternative Hypothesis:** The classifier's F-1 is the same for both groups, and the differences are not due to random chance.

<iframe
  src="assets/html.plot8.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Our p-value of 1 suggests that we will fail to reject the null hypothesis regardless of the signficance level.