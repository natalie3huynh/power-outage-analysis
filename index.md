# Lights Out

**Name**: Natalie Huynh

**Website Link**: [(Power Outage Analysis)](https://natalie3huynh.github.io/power-outage-analysis/)

## Step 1: Introduction

In this dataset, we explore recorded major power outages in the continental U.S. spanning from January 2000 to July 2026. Taken from Purdue University, the *Major Power Outage Risks in the U.S.*  dataset includes information on the location of the outages, regional climate features, land-use, electricity consumption, and economic characteristics of the states. Given these details, what might cause an area to be of high risk, where risk is defined by it's duration? Does seasonality have an impact on the severity of the types of outages that ensue? More broadly, what are the risk factors which characterize severe power outages? 

To answer these questions, we analyze `power`, a dataset of 1,534 rows. We extract the following columns for analysis below:

## COME BACK AND UPDATE TO INCLUDE POSTAL.CODE AND START AND FINISH TIMES

| Variable | Description |
|---------|-------------|
| `YEAR` | Year of the outage event |
| `MONTH` | Month of the outage event |
| `U.S._STATE` | U.S. state where the outage occurred |
| `CLIMATE.REGION` | Regional climate classification |
| `ANOMALY.LEVEL` | El Niño/La Niña index (oceanic anomaly level) |
| `CLIMATE.CATEGORY` | Climate episode: Warm, Cold, or Normal |
| `CAUSE.CATEGORY` | Main cause of the outage |
| `OUTAGE.DURATION` | Duration of the outage (minutes) |
| `CUSTOMERS.AFFECTED` | Number of customers affected by the power outage event |
| `DEMAND.LOSS.MW` | Peak demand lost during the outage (MW) |
| `RES.PERCEN` | Residential electricity as % of total consumption |
| `COM.PERCEN` | Commercial electricity as % of total consumption |
| `IND.PERCEN` | Industrial electricity as % of total consumption |
| `RES.CUST.PCT` | % of residential customers served in the state |
| `COM.CUST.PCT` | % of commercial customers served in the state |
| `IND.CUST.PCT` | % of industrial customers served in the state |
| `POPULATION` | Total population of the state |
| `POPPCT_URBAN` | % of population living in urban areas |
| `POPDEN_URBAN` | Population density in urban areas (persons/sq mile) |
| `PC.REALGSP.STATE` | Per capita real GSP of the state (2009 USD) |

We group the above variables into the following categories:
| Category | Variables |
|----------|-----------|
| **General Info** | `YEAR`, `MONTH`, `U.S._STATE` |
| **Climate** | `CLIMATE.REGION`, `ANOMALY.LEVEL`, `CLIMATE.CATEGORY` |
| **Outage Info** | `CAUSE.CATEGORY`, `OUTAGE.DURATION`, `CUSTOMERS.AFFECTED`, `DEMAND.LOSS.MW` |
| **Electricity Consumption (Percentages)** | `RES.PERCEN`, `COM.PERCEN`, `IND.PERCEN` |
| **Customers** | `RES.CUST.PCT`, `COM.CUST.PCT`, `IND.CUST.PCT` |
| **Land-use / Population** | `POPULATION`, `POPPCT_URBAN`, `POPDEN_URBAN` |
| **Economy** | `PC.REALGSP.STATE` |

## Step 2: Data Cleaning and Exploratory Data Analysis

As an initial visual demonstration, we're intrerested in seeing the visual spread of these outages across the U.S.

<iframe src="assets/plot_1.html" width="800" height="600" frameborder="0"></iframe>

<iframe src="assets/plot_2.html" width="800" height="600" frameborder="0"></iframe>

<iframe src="assets/plot_3.html" width="800" height="600" frameborder="0"></iframe>

<iframe src="assets/plot_4.html" width="800" height="600" frameborder="0"></iframe>

## Step 3: Assessment of Missingness

## Step 4: Hypothesis Testing

## Step 5: Framing a Prediction Problem

## Step 6: Baseline Model

## Step 7: Final Model

## Step 8: Fairness Analysis
