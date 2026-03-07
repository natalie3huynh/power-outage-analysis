# Lights Out
Name: Natalie Huynh (n3huynh@ucsd.edu) 

## Introduction:

In this dataset, we explore recorded major power outages in the continental U.S. spanning from January 2000 to July 2026. Taken from Purdue University, the *Major Power Outage Risks in the U.S.*  dataset includes information on the location of the outages, regional climate features, land-use, electricity consumption, and economic characteristics of the states. Given these details, what might cause an area to be of high risk, where risk is defined by it's duration? Does seasonality have an impact on the severity of the types of outages that ensue? More broadly, what are the risk factors which characterize severe power outages? 

To answer these questions, we analyze `power`, a dataset of 1,534 rows. We extract the following columns for analysis below:

| Variable                  | Description                                            |
| ------------------------- | ------------------------------------------------------ |
| `YEAR`                    | Year of the outage event                               |
| `MONTH`                   | Month of the outage event                              |
| `U.S._STATE`              | U.S. state where the outage occurred                   |
| `POSTAL.CODE`             | Postal abbreviation of the U.S. state                  |
| `CLIMATE.REGION`          | Regional climate classification                        |
| `ANOMALY.LEVEL`           | El Niño/La Niña index (oceanic anomaly level)          |
| `CLIMATE.CATEGORY`        | Climate episode: Warm, Cold, or Normal                 |
| `OUTAGE.START.DATE`       | Date when the outage event began                       |
| `OUTAGE.START.TIME`       | Time of day when the outage event began                |
| `OUTAGE.RESTORATION.DATE` | Date when power was restored                           |
| `OUTAGE.RESTORATION.TIME` | Time when power was restored                           |
| `CAUSE.CATEGORY`          | Main cause of the outage                               |
| `OUTAGE.DURATION`         | Duration of the outage (minutes)                       |
| `CUSTOMERS.AFFECTED`      | Number of customers affected by the power outage event |
| `DEMAND.LOSS.MW`          | Peak demand lost during the outage (MW)                |
| `RES.PERCEN`              | Residential electricity as % of total consumption      |
| `COM.PERCEN`              | Commercial electricity as % of total consumption       |
| `IND.PERCEN`              | Industrial electricity as % of total consumption       |
| `RES.CUST.PCT`            | % of residential customers served in the state         |
| `COM.CUST.PCT`            | % of commercial customers served in the state          |
| `IND.CUST.PCT`            | % of industrial customers served in the state          |
| `POPULATION`              | Total population of the state                          |
| `POPPCT_URBAN`            | % of population living in urban areas                  |
| `POPDEN_URBAN`            | Population density in urban areas (persons/sq mile)    |
| `PC.REALGSP.STATE`        | Per capita real GSP of the state (2009 USD)            |

We group the above variables into the following categories:

| Category                              | Variables                                                           |
|:--------------------------------------|:--------------------------------------------------------------------|
| General Info                          | YEAR, MONTH, U.S._STATE, POSTAL.CODE                                |
| Climate                               | CLIMATE.REGION, ANOMALY.LEVEL, CLIMATE.CATEGORY                     |
| Outage Timing                         | OUTAGE.START.DATE                                                   |
|                                       | OUTAGE.START.TIME                                                   |
|                                       | OUTAGE.RESTORATION.DATE                                             |
|                                       | OUTAGE.RESTORATION.TIME                                             |
| Outage Info                           | CAUSE.CATEGORY, OUTAGE.DURATION, CUSTOMERS.AFFECTED, DEMAND.LOSS.MW |
| Electricity Consumption (Percentages) | RES.PERCEN, COM.PERCEN, IND.PERCEN                                  |
| Customers                             | RES.CUST.PCT, COM.CUST.PCT, IND.CUST.PCT                            |
| Land-use / Population                 | POPULATION, POPPCT_URBAN, POPDEN_URBAN                              |
| Economy                               | PC.REALGSP.STATE                                                    |
## Data Cleaning and Exploratory Data Analysis:

To prepare the data for analysis, we conduct the following:

1. **Converting to Tidy Data**: Since the data previously conatined rows describing the units in an Excel File, I assigned the correct column names to be the headers and removed any rows containing notes.

2. **Handling Missing Variables**: In the dataset, I observe that `OUTAGE.DURATION`, `DEMAND.LOSS.MW`, and `CUSTOMERS.AFFECTED` are the only columns containing missing values, represented by 0s. These 0s are replaced by NaNs for modeling purposes.

3. **Column Conversions**: Some of the columns such as `OUTAGE.DURATION` are represented as string types. I convert these to be numeric. Date and time columns are combined into a single datetime column for both the start and end of the outages. The `MONTH` column is also converted from a numeric column to Categorical columns by the name of the month.

4. **Creating New Columns**: I create a binary column, `is_long`, to measure the severity of the outage for later modeling purposes. This column is based off `OUTAGE.DURATION` where outages which lasted in the top 25% of power outages is considered a long outage. 

|   YEAR | MONTH   | U.S._STATE   | POSTAL.CODE   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | OUTAGE.START.DATE   | OUTAGE.START.TIME   | OUTAGE.RESTORATION.DATE   | OUTAGE.RESTORATION.TIME   |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   RES.PERCEN |   COM.PERCEN |   IND.PERCEN |   RES.CUST.PCT |   COM.CUST.PCT |   IND.CUST.PCT |   CUSTOMERS.AFFECTED |   POPULATION |   POPPCT_URBAN |   POPDEN_URBAN |   PC.REALGSP.STATE | OUTAGE.START        | OUTAGE.RESTORATION   |
|-------:|:--------|:-------------|:--------------|:-------------------|----------------:|:-------------------|:-------------------|:--------------------|:--------------------|:--------------------------|:--------------------------|------------------:|-----------------:|-------------:|-------------:|-------------:|---------------:|---------------:|---------------:|---------------------:|-------------:|---------------:|---------------:|-------------------:|:--------------------|:---------------------|
|   2011 | July    | Minnesota    | MN            | East North Central |            -0.3 | normal             | severe weather     | 2011-07-01 00:00:00 | 17:00:00            | 2011-07-03 00:00:00       | 20:00:00                  |              3060 |              nan |      35.5491 |      32.225  |      32.2024 |        88.9448 |        10.644  |       0.411181 |                70000 |      5348119 |          73.27 |           2279 |              51268 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |
|   2014 | May     | Minnesota    | MN            | East North Central |            -0.1 | normal             | intentional attack | 2014-05-11 00:00:00 | 18:38:00            | 2014-05-11 00:00:00       | 18:39:00                  |                 1 |              nan |      30.0325 |      34.2104 |      35.7276 |        88.8335 |        10.7916 |       0.37482  |                  nan |      5457125 |          73.27 |           2279 |              53499 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |
|   2010 | October | Minnesota    | MN            | East North Central |            -1.5 | cold               | severe weather     | 2010-10-26 00:00:00 | 20:00:00            | 2010-10-28 00:00:00       | 22:00:00                  |              3000 |              nan |      28.0977 |      34.501  |      37.366  |        88.9206 |        10.687  |       0.392361 |                70000 |      5310903 |          73.27 |           2279 |              50447 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |
|   2012 | June    | Minnesota    | MN            | East North Central |            -0.1 | normal             | severe weather     | 2012-06-19 00:00:00 | 04:30:00            | 2012-06-20 00:00:00       | 23:00:00                  |              2550 |              nan |      31.9941 |      33.5433 |      34.4393 |        88.8954 |        10.6822 |       0.422355 |                68200 |      5380443 |          73.27 |           2279 |              51598 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |
|   2015 | July    | Minnesota    | MN            | East North Central |             1.2 | warm               | severe weather     | 2015-07-18 00:00:00 | 02:00:00            | 2015-07-19 00:00:00       | 07:00:00                  |              1740 |              250 |      33.9826 |      36.2059 |      29.7795 |        88.8216 |        10.8113 |       0.367005 |               250000 |      5489594 |          73.27 |           2279 |              54431 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |

