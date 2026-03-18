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

| YEAR | MONTH   | U.S._STATE | POSTAL.CODE | CLIMATE.REGION     | ANOMALY.LEVEL | CLIMATE.CATEGORY | OUTAGE.START.DATE     | OUTAGE.RESTORATION.DATE | CAUSE.CATEGORY      | OUTAGE.DURATION | CUSTOMERS.AFFECTED | TOTAL.CUSTOMERS | DEMAND.LOSS.MW | RES.PERCEN | COM.PERCEN | IND.PERCEN | POPPCT_URBAN | POPULATION | PC.REALGSP.STATE | is_long |
|------|---------|------------|-------------|------------------|---------------|-----------------|---------------------|------------------------|--------------------|----------------|------------------|----------------|----------------|------------|------------|------------|---------------|------------|-----------------|---------|
| 2011 | July    | Minnesota  | MN          | East North Central | -0.3         | normal          | 2011-07-01 00:00:00 | 2011-07-03 00:00:00    | severe weather     | 3060           | 70000            | 2595696        | nan            | 35.5491    | 32.225     | 32.2024    | 73.27         | 5348119    | 51268           | True    |
| 2014 | May     | Minnesota  | MN          | East North Central | -0.1         | normal          | 2014-05-11 00:00:00 | 2014-05-11 00:00:00    | intentional attack | 1              | nan              | 2640737        | nan            | 30.0325    | 34.2104    | 35.7276    | 73.27         | 5457125    | 53499           | False   |
| 2010 | October | Minnesota  | MN          | East North Central | -1.5         | cold            | 2010-10-26 00:00:00 | 2010-10-28 00:00:00    | severe weather     | 3000           | 70000            | 2586905        | nan            | 28.0977    | 34.501     | 37.366     | 73.27         | 5310903    | 50447           | False   |
| 2012 | June    | Minnesota  | MN          | East North Central | -0.1         | normal          | 2012-06-19 00:00:00 | 2012-06-20 00:00:00    | severe weather     | 2550           | 68200            | 2606813        | nan            | 31.9941    | 33.5433    | 34.4393    | 73.27         | 5380443    | 51598           | False   |
| 2015 | July    | Minnesota  | MN          | East North Central | 1.2          | warm            | 2015-07-18 00:00:00 | 2015-07-19 00:00:00    | severe weather     | 1740           | 250000           | 2673531        | 250            | 33.9826    | 36.2059    | 29.7795    | 73.27         | 5489594    | 54431           | False   |


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

### Interesting Aggregates:
Now knowing that seasonality may have certain implications on the length or frequency of outages, I aggregate the data grouping by month and calculating the count of outages per month, the median duration (since duration is heavliy skewed by extreme outages), and the average number of customers affected. In this table, we notice that while some months still rank higher in the frequency of outages, they don't always have the longest median durations. For example, June and July have the highest number of outages, but they have only moderate median duration times. It can be hypothesized that more outages may happen in summer months that are shorter, while winter months are frequented by fewer, longer outages.


| MONTH     |   count_outages |   median_duration |   cust_affected |
|:----------|----------------:|------------------:|----------------:|
| January   |             136 |            1265   |          240046 |
| February  |             136 |             810   |          153526 |
| March     |             100 |             451   |          132655 |
| April     |             111 |             597   |          127153 |
| May       |             127 |             617   |          178815 |
| June      |             195 |             578.5 |          134008 |
| July      |             181 |             762   |          123329 |
| August    |             153 |            1200   |          250718 |
| September |              94 |            1544   |          315566 |
| October   |             109 |            1034.5 |          223333 |
| November  |              72 |             636.5 |          129511 |
| December  |             111 |            1572   |          169397 |


As another indicator of climate patterns, I also decide to group outages by their anomaly level which describes oceanic El Niño/La Niña. Sorting this anomaly levels by descending median outage durations, I find that larger, negative values (indicating La Niña conditions of cooler temperatures) end up having higher median durations. However, mild El Niño conditions (anomaly levels of 0.2 and 0.9) are still affiliated with long durations. This may be because mild El Niño conditions are more likely to be present than stronger La Niña episodes.


|   ANOMALY.LEVEL |   count_outages |   median_duration |   mean_cust_affected |   mean_demand_loss |
|----------------:|----------------:|------------------:|---------------------:|-------------------:|
|            -1.6 |               2 |              7020 |             107000   |            375     |
|             0.2 |              37 |              3960 |             373213   |           2324     |
|             0.9 |              29 |              3300 |             209802   |            269.105 |
|            -1.2 |              13 |              2775 |              82012.5 |            295.25  |
|            -1.5 |               8 |              2460 |             116185   |            252     |

Together, these aggregates are significant in model use for including temporal, seasonal, and climate indicators.

## Assessment of Missingness
### MNAR Analysis
In the `power` dataset, we observed above that `OUTAGE.DURATION`, `DEMAND.LOSS.MW`, `CUSTOMERS.AFFECTED` had values of 0, which we replaced with NaNs. Other columns that already had NaN values include `MONTH`, `CLIMATE.REGION`, `ANOMALY.LEVEL`, `CLIMATE.CATEGORY`, `OUTAGE.START.DATE`, `OUTAGE.RESTORATION.DATE`, `RES.PERCEN`, `COM.PERCEN`, and `IND.PERCEN`.

We have observed that the following covariates have the highest missingness:
In the `power` dataset, we observed above that `OUTAGE.DURATION`, `DEMAND.LOSS.MW`, `CUSTOMERS.AFFECTED` had values of 0, which we replaced with NaNs. Other columns that already had NaN values include `MONTH`, `CLIMATE.REGION`, `ANOMALY.LEVEL`, `CLIMATE.CATEGORY`, `OUTAGE.START.DATE`, `OUTAGE.RESTORATION.DATE`, `RES.PERCEN`, `COM.PERCEN`, and `IND.PERCEN`.

We have observed that the following covariates have the highest missingness:

| Column                  | NaN_Count |
|:------------------------|----------:|
| DEMAND.LOSS.MW          |       901 |
| CUSTOMERS.AFFECTED      |       655 |
| OUTAGE.DURATION         |       136 |
| OUTAGE.RESTORATION.DATE |        58 |
| RES.PERCEN              |        22 |
| COM.PERCEN              |        22 |

Tying this back to the data generating process, missingness not at random (MNAR) can be hypothesized to be caused by the following for the top 4 missing covariates:

- `DEMAND.LOSS.MW`: This was intended to be a report of the amount of peak demand lost during an outage. However, in many cases, total demand was reported instead. This signifies that if peak demand is not recorded, `DEMAND.LOWW.MW` might be missing altogether.

- `CUSTOMERS.AFFECTED`: If outages were on the smaller side, it is likely that the number of customers affected may not have been recorded. 

- `OUTAGE.DURATION` and `OUTAGE.RESTORATION.DATE`: It is likely that if either of these were missing, the event was trivial/very short, so companies did not record how long they took or when they were restored.

To actually understand if these data are MAR, it might be useful to understand what certain reporting protocols are for energy companies across different states (for example, additional data on if different companies or states have different thresholds for reporting severe outages). 

### Missingness Dependency

Since `OUTAGE.DURATION` is one measure of outage severity with high, non-trivial missingnes which I plan to use as my target variable, I start by assessing missingness in this variable. Since `OUTAGE.DURATION` is likely affiliated with less extreme outage (perhaps it was an outage that occurred during a month where more outages occurred at smaller scales and were not reported since less customers were affected), or one where not too many customers were affected and the company may not have decided to report the duration. Finally, it is plausible that if the outage was in a climatic region which is not typically affected by extreme weather conditions and was a less severe outage, the duration may also not have been reported. Dependency is tested in the below permutation tests based on these expectations:

>Outage Duration and Month: 
**Null Hypothesis**: Missingness of outage duration does not depend on month.

**Alternative Hypothesis**: Missingness of outage duration does depend on month. 

**Results**: 
**Observed statistic**: 0.143
**p-value**: 0.162

Here, we visualize missingness of outage duration information by month.
<iframe
  src="assets/missing-duration-month.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

After conducing a permutation test shuffling the month labels with 1000 trials, I find that the results are not significant and I fail to reject the null hypothesis. This means that there is not enough evidence to suggest that the missingness of Outage Duration does depend on month. Below is the empirical distribution of the TVD:

<iframe
  src="assets/tvd-month-hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


> Outage Duration and Climate Region: 

One component that we noticed in our aggregated anomaly levels is that climate patterns defined by El Niño/La Niña events correspond to longer/shorter durations. This is supported by additional analysis of climate regions where certain climatic regions tend to have significantly longer median outage durations in comparison to others (for example East North Central has a median outage duration of 3210 minutes in comparison to West North Central which has a median of 60.5 minutes). Thus, outage duration may be less reported in regions with less severe outages if they are not long lasting. Since we have already discovered that outage duration missingness does not depend on month, it would be interesting to see whether or not it's missingness depends on climate region.

I start by conducting a permutation test at a significance level of 0.05, and a test statistic of Total Variation Distance (tvd) since I am testing the dependency of the missingness of outage duration on a categorical variable, `CLIMATE.REGION`. To use the TVD, the columns are normalized so that each column sums to 1. The following hypotheses and results are reported:

- **Null Hypothesis**: The missingness of outage duration does **NOT** depend on climate region.
- **Alternative Hypothesis**: The missingness of outage duration does depend on climate region.

**Results**:

- **Observed statistic**: 0.27
- **Reported p-value**: 0.0

Here, we visualize missingness of outage duration information by climate region.

<iframe
  src="assets/missing-duration-climate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value is less than 0.05, the null hypothesis is rejected in favor of the alternative hypothesis. There is statistically significant evidence that missingness of outage duration does depend on climate region. Below is the empirical distribution of the TVD:

<iframe
  src="assets/missing-duration-climate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing:
Based on bivariate analysis examing the duration of outages based on cause category, it appears that some cause categories tend to have outages that last longer than other categories. For example, outages caused by severe weather tends to have longer outages than other non-weather related categories. Thus, it would be interesting to do conduct a hypothesis test to determine whether or not this is truly the case. To do so, a new column named `is_severe_weather` is created which categorizes outages to be caused by severe weather (represented by 1) or other (represented by the value 0). Since I am testing to see if durations differ between these two numerical distributions, absolute difference in means will effectively capture this difference in either direction (shorter or longer average durations). Futhermore, I choose to do a permutation test since the purpose of this test is to determine if these two groups (outages caused by severe weather vs other causes) look as if they were drawn from the same population.  

**Null Hypothesis**: The distribution of outage durations is the same for severe weather outages and for outages caused by other reasons.

**Alternative Hypothesis**: The distribution of outage durations is NOT the same for severe weather outages and for outages caused by other reasons.

**Test Statistic**: Absolute difference in mean outage duration between severe weather outages and outages caused by other reasons.

**Significance Level**:  0.05

Conducting a permutation test by shuffling the labels in `is_severe_weather` and using 1000 trials, it is discovered that the results are significant at a p-value of 0.0, meaning that there is evidence that the distribution of outage durations is not the same for severe weather outages and outages caused by other reasons.

## Framing a Prediction Problem
Now, for my predictive task, I am interested in answering the following:

**Prediction Problem**:
> What risk factors can be used to predict the severity of a major power outage, where severity is measured by the duration of the outage?

**Response Variable**: Severity using the column `is_long`. 

This column measures severity such that outages which last over 3000 minutes are considered to be long. This threshold is determined by observing the outages which last in the 75th percentile of outage durations. 

**Prediction Type**: Binary Classifier

Since the response variable I am predicting will only take on values of 0 or 1, this is a binary classifier.

**Metric**: While I will report accuracy, recall, and F1-Score, I will mainly focus on recall as my most important metric. This is due to the fact that because the data has severe class imbalance (only ~22% of the data is labeled as severe with a value of 1 in the `is_long` column), F1-score is helpful to address class imbalance and also act as a metric which balances precision and recall. Accuracy however may be more misleading to use as an evaluation metric since a model which predicts that all outages are not severe will still achieve an accuracy rate of 78% since 22% of the data is labeled as severe. Above all, recall would be the most important metric for this situation. Recall, which emphasizes true positives, is important in this case because we would prefer to correctly predict severe outages to prepare for resource allocation during emergencies. With this, I proceed with the following models.

## Baseline Model
In the hypothesis testing, it was discovered that there is evidence that the distribution of outage duration is not the same for outages caused by severe weather vs. other causes. This suggests that severe weather may impact the length, and thus, the severity of the outage. However, cause category is often not available at the time of prediction, so instead, `CLIMATE.REGION` is used as a proxy for weather patterns to capture regions with different climates which are consistent. Furthermore, analysis has previously suggested that certain months (such as months that fall within winter), tend to have longer lasting outages. Thus, these features our included in the baseline model. Feature specifics and model performance are descibed below.

**Features of Interest**:
- `MONTH` (Nominal): This variable describes what month the outage occurred in. 
- `CLIMATE.REGION`(Nominal): This variable describes what caused the outage.

**Response Variable**:
`is_long`: Categorical, nominal

Among the 1534 outages, 348 are considered to be severe outages (containing a value of 1 in the `is_long` column). To account for this class imbalance, the parameter "class_weight" in the pipeline is set to "balanced".

**Baseline Model**:
The data is split so that 80% of the data is part of the training set, and 20% is part of the test set.
Since all of the initial covariates are categorical, they are transformed using one hot encoding to be included in the model. After being preprocessed, these columns are then passed through a pipeline using a Random Forest Classifier of depth 5 and 100 trees. After fitting the pipeline, the following results are achieved for accuracy, precision, and recall.


**Baseline Model Results on Training Set**: 
- **Accuracy**: 0.69
- **Recall**: 0.59
- **F1-Score**: 0.46


**Baseline Model Results on Test Set (Generalizing to Unseen Data)**:

- **Accuracy**: 0.68
- **Recall**: 0.60
- **F1-Score**: 0.48

**Model Performance**:

Above, we report accuracy and evaluate recall and the F1-Score on both the training and test set to ensure that the model is not overfitting the data. These values appear to be slightly higher on the test set for recall and F1-score, indicating that the model is not overfitting and does generalize to unseen data. 

From the above, accuracy, recall, and F1-Score are not very high. For further interpretation, a recall of 0.60 means that the baseline only correclty identifies 60% of severe/longer outages. Since the goal of this predictive task is to detect long outages, this metric is relatively low, as is the F1-score, suggesting that there is room for improvement in the model. We can use this model as a benchmark for our final model.

## Final Model
When selecting variables to include in our final model, it is important to know what features are actually available at the time of prediction. These features include outage information including the year, month, state, postal code, climate information, land usage/energy usage, economic factors of the state, and urbanization levels. At the time of prediction, we do not have access to outage restoration times, the number of customers affected, or cause category so we refrain from using these features in our model.

Given the available covariates at the time of prediction, I decided to include the below features and describe why each feature/transformed feature may be meaningful toward improving my model.

For the final model, I maintain `CLIMATE.REGION`, but `MONTH` is now transfomed as follows: 

**Engineered Features**: These two features are engineered for the final model.
1. `MONTH` mapped to seasons (Nominal): 
In my bivariate analysis, I was able to find trends across winter months for longer durations. Thus, I found it interesting to maintain this column, but transform it by mapping the months to seasons (Winter, Spring, Summer, Fall). I do so by using a FunctionTransformer below.

2. `POPPCT_URBAN` (Nominal after transformation): 
Using a Binarizer, this column is transformed such that an urbanization percentage above 84% is considered high. Including this will attempt to capture the effects of high urbanization on power outages. This percentage is drawn from taking the median urbanization percentage of `POPPCT_URBAN`. The justification behind this may be that states considered to have higher urbanization levels may resolve power outages faster rather than rural areas.


**Including Additional Features**:

- `TOTAL.CUSTOMERS` (Quantitative): 
Accounts for energy usage where areas of higher energy usage may lead to more blackouts.

- `PC.REALGSP.STATE`(Quantitative): 
This attempts to capture economic indicators of the state in which the outage occurred (outages which occur in states with higher GSP may have better energy infrastructure, and thus, shorter outage durations).

- `ANOMALY.LEVEL` (Nominal): 
Since this level captures information representing the oceanic El Niño/La Niña and has previously shown in bivariate analysis that outages of certain anomaly levels have longer durations, anomaly level is important to include to capture seasonal climate patterns.

Thus, our final model now includes `MONTH`, `POPPCT_URBAN`, and `PC.REALGSP.STATE`, `TOTAL.CUSTOMERS`, and `ANOMALY.LEVEL`, ideally capturing temporal, seasonal, economic, and energy usage features. Similar to the basline, the final model used is also a Random Forest Classifier since it allows the inclusion of both categorical and numerical features without necessarily requiring extensive feature engineering for either.

To improve the model, GridSearch is used to identify the best hyperparameters for the number of trees, maximum depth, the minimum number of samples split, and the criterion while maximizing recall. I found these to be the best hyperparameters:

- **min_samples_split**: 5
- **criterion**: gini
- **max_depth**: 5
- **n_estimators**: 20

Using these tuned hyperparameters, I achieve these scores:

**Final Model Results on Training Set**: 
- **Accuracy**: 0.77
- **Recall**: 0.67
- **F1-Score**: 0.57


**Final Model Results on Test Set (Generalizing to Unseen Data)**:
- **Accuracy**: 0.76
- **Recall**: 0.71
- **F1-Score**: 0.57

**Final Model Performance**:

Final Model Performance: By using these additional, transformed features, the final model now achieves an accuracy score of 0.76, an F1-Score of 0.57, and a moderately high recall score of 0.71 on the test set. Furthermore, the final model does not show signs of overfitting since the metrics computed on the seen and unseen data are close. Compared to the baseline model, recall increased by 11% on the test set. Since recall is the most important metric for this scenario and there is a significant increase, we can say that the final model has improved since the baseline.


## Fairness Analysis
Finally, it is important to assess fairness for the final model. To do so, model performance is tested on the following groups. 84% is used as the threshold for percent urbanization as it represents the median percentage of `POPPCT_URBAN` in the datset. Since failing to predict a severe outage would have extreme consequences in resource allocation or preemptive emergency measures, recall parity, which has emphasisis on the true positive rate, is the most important metric to use for this assessment.

- **Group A**: Outages with less than or equal to 84% state urbanization.
- **Group B**: Outages with over 84% state urbanization.
- **Test Statistic**: Differences in recall
- **Significance Level**: 0.05

**Null Hypothesis**: The model is fair. It's recall is the same for outages which occur in states with low to moderate urbanization (urbanization < 84%), and any differences are due to chance. 

**Alternative Hypothesis**: The model is unfair. It's recall is higher for outages which occur in states with high urbanization (urbanization > 84%) than with lower urbanization.

<iframe
  src="assets/urban-recall-difference.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Results**: Running a permutation test with 1,000 trials, the resulting p-value is 0.003 which is less than 0.05; thus, we reject the null hypothesis. This result is statistically significant, suggesting there is evidence that the final model is not fair with respect to urbanization level and may achieve higher recall for outages which occur in states with higher urbanization levels in comparison to states with lower urbanization levels.
