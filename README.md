# Predictive Analysis on Power Outages
Project for DSC 80 at UC San Diego
By Ken Ogihara

## Introduction
In this project, I analyze a dataset that includes major power outages across the U.S from 2000-2016. This dataset is from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure. This data contains information on the geographical location of the outages, climactic data, and electricity consumption patterns and economic characteristics of the states affected by the outages. 

This project revolves around one question: "What risk factors should an energy company want to look into when predicting the location and severity of its next major power outage?" To answer this question, I will first perform basic exploratory data analysis, assess missingness dependency, and finally build a model that predicts whether a power outage is caused by the weather or an intentional attack. This is important for the Department of Education and private energy companies to know because it allows them to take specific measures to prevent the onset of the next power outage. This dataset contains 1534 rows and 57 columns but for the sake of this project I will focus on the following columns:

| Column | Description |
| ----------- | ----------- |
| YEAR | Year in which an outage occurred |
| MONTH | Month in which an outage occurred |
| U.S._STATE | State in which an outage occurred |
| CLIMATE.REGION | Region of the U.S in which an outage occurred |
| ANOMALY.LEVEL | Oceanic Niño Index(ONI) used for identifying cool and warm seasons in the tropical Pacific |
| CLIMATE.CATEGORY | Month in which an outage occurred |
| OUTAGE.START.DATE | Year in which an outage occurred |
| OUTAGE.START.TIME | Month in which an outage occurred |
| OUTAGE.RESTORATION.DATE | Year in which an outage occurred |
| OUTAGE.RESTORATION.TIME | Month in which an outage occurred |
| CAUSE.CATEOGORY | Year in which an outage occurred |
| CUSTOMERS.AFFECTED | Month in which an outage occurred |
| TOTAL.PRICE | Month in which an outage occurred |
| TOTAL.CUSTOMERS | Month in which an outage occurred |
| POPULATION | Month in which an outage occurred |
| POPDEN_URBAN | Month in which an outage occurred |
| POPDEN_RURAL | Month in which an outage occurred |




