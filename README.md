# Lights Out: Identifying Risk Factors for Severe Power Outages
Name: Natalie Huynh (n3huynh@ucsd.edu) 

## Introduction:

In this dataset, I explore recorded major power outages in the continental U.S. spanning from January 2000 to July 2026. Taken from Purdue University, the *Major Power Outage Risks in the U.S.*  dataset includes information on the location of the outages, regional climate features, land-use, electricity consumption, and economic characteristics of the states. Given these details, what might cause an area to be of high risk, where risk is defined by it's duration? Does seasonality have an impact on the severity of the types of outages that ensue? More broadly, what are the risk factors which characterize severe power outages? For energy companies in states which are more heavily impacted by frequent outages such as California or Texas, these factors may play large roles in emergency measures to deploy crew members to repair certain areas faster, or plan for resource allocation. Furthermore, knowing the risk factors of unplanned outages may give energy companies more opportunities to initiate shorter, unplanned outages to maintan equipment before equipment failure can occur. Thus, this investigation is motivated by the necessity to better understand what can cause an outage to be severe, and thus, mitigate it's impacts or shorten repair times altogether.

To answer these questions, I analyze `power`, a dataset of 1,534 rows. I extract the following columns for analysis below:

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
| `TOTAL.CUSTOMERS`         | Number of customers affected by the power outage event |
| `DEMAND.LOSS.MW`          | Peak demand lost during the outage (MW)                |
| `RES.PERCEN`              | Residential electricity as % of total consumption      |
| `COM.PERCEN`              | Commercial electricity as % of total consumption       |
| `IND.PERCEN`              | Industrial electricity as % of total consumption       |
| `POPPCT_URBAN`            | % of population living in urban areas                  |
| `POPULATION`              | Total population of the state in a year.               |
| `PC.REALGSP.STATE`        | Per capita real GSP of the state (2009 USD)            |

<table>
  <thead>
    <tr>
      <th>Category</th>
      <th>Variables</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>General Info</td>
      <td>
        <code>YEAR</code>, <code>MONTH</code>, <code>U.S._STATE</code>, <code>POSTAL.CODE</code>
      </td>
    </tr>
    <tr>
      <td>Climate</td>
      <td>
        <code>CLIMATE.REGION</code>, <code>ANOMALY.LEVEL</code>, <code>CLIMATE.CATEGORY</code>
      </td>
    </tr>
    <tr>
      <td>Outage Timing</td>
      <td>
        <code>OUTAGE.START.DATE</code><br>
        <code>OUTAGE.START.TIME</code><br>
        <code>OUTAGE.RESTORATION.DATE</code><br>
        <code>OUTAGE.RESTORATION.TIME</code>
      </td>
    </tr>
    <tr>
      <td>Outage Info</td>
      <td>
        <code>CAUSE.CATEGORY</code>, <code>OUTAGE.DURATION</code>, <code>CUSTOMERS.AFFECTED</code>, <code>DEMAND.LOSS.MW</code>
      </td>
    </tr>
    <tr>
      <td>Electricity Consumption (Percentages)</td>
      <td>
        <code>RES.PERCEN</code>, <code>COM.PERCEN</code>, <code>IND.PERCEN</code>
      </td>
    </tr>
    <tr>
      <td>Customers</td>
      <td>
        <code>TOTAL.CUSTOMERS</code>
      </td>
    </tr>
    <tr>
      <td>Land-use / Population</td>
      <td>
        <code>POPPCT_URBAN</code>, <code>POPULATION</code>
      </td>
    </tr>
    <tr>
      <td>Economy</td>
      <td>
        <code>PC.REALGSP.STATE</code>
      </td>
    </tr>
  </tbody>
</table>


## Data Cleaning and Exploratory Data Analysis:
To prepare the data for analysis, I conduct the following:

1. **Converting to Tidy Data**: Since the data previously conatined rows describing the units in an Excel File, I assigned the correct column names to be the headers and removed any rows containing notes.

2. **Handling Missing Variables**: In the dataset, I observe that `OUTAGE.DURATION`, `DEMAND.LOSS.MW`, and `CUSTOMERS.AFFECTED` are the only columns containing missing values, represented by 0s. These 0s are replaced by NaNs for modeling purposes.

3. **Column Conversions**: Some of the columns such as `OUTAGE.DURATION` are represented as string types. I convert these to be numeric. Date and time columns are combined into a single datetime column for both the start and end of the outages. The `MONTH` column is also converted from a numeric column to Categorical columns by the name of the month.

4. **Creating New Columns**: I create a binary column, `is_long`, to measure the severity of the outage for later modeling purposes. This column is based off `OUTAGE.DURATION` where outages which lasted in the top 25% of power outages is considered a long outage. More specifically, these are outages which last over 3000 minutes. This is later important for my predictive task to predict severe outages.

Below is what the dataframe looks like now:
|   YEAR | MONTH   | U.S._STATE   | POSTAL.CODE   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | OUTAGE.START.DATE   | OUTAGE.RESTORATION.DATE   | CAUSE.CATEGORY     |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED |   TOTAL.CUSTOMERS |   DEMAND.LOSS.MW |   RES.PERCEN |   COM.PERCEN |   IND.PERCEN |   POPPCT_URBAN |   POPULATION |   PC.REALGSP.STATE | is_long   |
|-------:|:--------|:-------------|:--------------|:-------------------|----------------:|:-------------------|:--------------------|:--------------------------|:-------------------|------------------:|---------------------:|------------------:|-----------------:|-------------:|-------------:|-------------:|---------------:|-------------:|-------------------:|:----------|
|   2011 | July    | Minnesota    | MN            | East North Central |            -0.3 | normal             | 2011-07-01 00:00:00 | 2011-07-03 00:00:00       | severe weather     |              3060 |                70000 |           2595696 |              nan |      35.5491 |      32.225  |      32.2024 |          73.27 |      5348119 |              51268 | True      |
|   2014 | May     | Minnesota    | MN            | East North Central |            -0.1 | normal             | 2014-05-11 00:00:00 | 2014-05-11 00:00:00       | intentional attack |                 1 |                  nan |           2640737 |              nan |      30.0325 |      34.2104 |      35.7276 |          73.27 |      5457125 |              53499 | False     |
|   2010 | October | Minnesota    | MN            | East North Central |            -1.5 | cold               | 2010-10-26 00:00:00 | 2010-10-28 00:00:00       | severe weather     |              3000 |                70000 |           2586905 |              nan |      28.0977 |      34.501  |      37.366  |          73.27 |      5310903 |              50447 | False     |
|   2012 | June    | Minnesota    | MN            | East North Central |            -0.1 | normal             | 2012-06-19 00:00:00 | 2012-06-20 00:00:00       | severe weather     |              2550 |                68200 |           2606813 |              nan |      31.9941 |      33.5433 |      34.4393 |          73.27 |      5380443 |              51598 | False     |
|   2015 | July    | Minnesota    | MN            | East North Central |             1.2 | warm               | 2015-07-18 00:00:00 | 2015-07-19 00:00:00       | severe weather     |              1740 |               250000 |           2673531 |              250 |      33.9826 |      36.2059 |      29.7795 |          73.27 |      5489594 |              54431 | False     |
/var/folders/g_/vqtx8mxn1vx_26q8s5whsgh40000gn/T/ipykernel_66845/1113245167.py:109: FutureWarning: Downcasting behavior in `replace` is deprecated and will be removed in a future version. To retain the old behavior, explicitly call `result.infer_objects(copy=False)`. To opt-in to the future behavior, set `pd.set_option('future.no_silent_downcasting', True)`
  power[cols_with_missing] = power[cols_with_missing].replace(0, np.nan)
### Univariate Analysis:
As an initial visual demonstration, we are interested in seeing the visual spread of these outages across the U.S. As mentioned in our introduction, outages typically occur much more frequently in California and Texas, as well as the East Coast and Washington. The frequency of outages in these states which tend to experience more extreme weather patterns (heat waves or rain/snow storms) may be interesting in later analyses.

<iframe
  src="assets/map.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Response Variable: OUTAGE.DURATION**:

Next, since I plan to use `OUTAGE.DURATION` as my target variable in later analysis, it may be interesting to analyze it's distribution. The distribution of outages measured in minutes is heavily right skewed, meaning that there are a few instances of extreme outages which took much longer than the average outage to be restored. In later aggregates, we take the median rather than mean to handle these outliers.

<iframe
  src="assets/outage-duration-hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis
Based on our initial assumption that weather patterns may have different effects on outage durations based on the distribution of outages across the U.S., I next explore possible associations between `CAUSE.CATEGORY` and `OUTAGE.DURATION`. Since `CAUSE.CATEGORY` includes severe weather patterns as one of it's causes, it is interesting to see if durations are longer for this cause in comparison to other causes.

<iframe
  src="assets/outage-duration-by-cause.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

As expected, outages caused by severe weather or fuel supply emergencies tend to have slightly higher outage durations when observing the median outage durations. 

Exploring another relationship, it would also be interesting to see how temporal features may be related to differences in outage durations. Grouping by month and observing the median outage duration per month, outage durations spike in September (early Fall), December, and January (early Winter). This might suggest that seasonality may be an interesting feature to incorporate in later modeling.

<iframe
  src="assets/outage-duration-by-month.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

