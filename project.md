# Predicting Demand Loss in Major Power Outages in Different States

### by Elif Yildiz

## Introduction

In an increasingly electricity-dependent world, understanding the factors contributing to major power outages is vital for minimizing economic and societal disruptions. This project analyzes a comprehensive dataset documenting significant power outages across the United States from January 2000 to July 2016 across different states. Defined by the Department of Energy, these major outages impacted at least 50,000 customers or resulted in an unplanned firm load loss of at least 300 MW.

The dataset is robust, encompassing not only the primary details of the outages, such as their geographical locations, dates, and times, but also data on regional climatic conditions, land-use characteristics, electricity consumption patterns, and economic attributes of the affected states. This multifaceted approach allows for a nuanced analysis of the factors influencing power outages and provides a fertile ground for developing predictive models to estimate demand loss based on various predictors. 

The dataset is available at Purdue University’s Laboratory for Advancing Sustainable Critical Infrastructure, https://engineering.purdue.edu/LASCI/research-data/outages

## Data Cleaning and Exploratory Data Analysis

I will start my analysis by cleaning the data, and limiting it to only the columns I need since it contains 57 columns. Some of the columns like 'u.s._state' and 'postal.code' contain almost the same information, which can lead to multicollinearity. The same thing happens for the anomaly levels and climate category as they are highly correlated. Because of this, I will only be using one of each. For the sake of simplicity, I converted all the columns to lower case.

I have also excluded columns that won't be available at the time of predicting the demand loss such as outage duration or restoration date.

Let's take a look at the columns:

| Column | Description |
| ----------- | ----------- |
| `year` | Indicates the year when the outage event occurred |
| `month` | Indicates the month when the outage event occurred |
| `u.s._state` | State the outage occured in |
| `nerc.region` | The North American Electric Reliability Corporation (NERC) regions involved in the outage event |
| `climate.category` | Climate category as warm, cold or normal and episodes of the climate are based on a threshold of ± 0.5 °C for the Oceanic Niño Index (ONI) |
| `outage.start.date` | Date of when the outage has started |
| `outage.start.time` | Time of when the outage has started |
| `cause.category` | The general cause of the outage |
| `cause.category.detail` | The detailed cause of the outage |
| `demand.loss.mw` | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported] This is also the columns we want to predict |
| `customers.affected` | Number of customers affected by the power outage event |
| `res.sales`	| Electricity consumption in the residential sector (megawatt-hour) |
| `com.sales`	| Electricity consumption in the commercial sector (megawatt-hour) |
| `ind.sales`	| Electricity consumption in the industrial sector (megawatt-hour) |
| `total.sales`| Total electricity consumption in the U.S. state (megawatt-hour) |
| `population` | Population in the U.S. state in a year |
| `popden_urban` | Population density of the urban areas (persons per square mile) |
| `popden_rural` | Population density of the rural areas (persons per square mile) |
| `popden_uc` | Population density of the urban clusters (persons per square mile) |


By examining historical trends and patterns, the dataset facilitates a deeper understanding of the dynamics of power outages. It enables the identification and assessment of key risk factors associated with sustained power outages, thus offering valuable insights for policymakers, utility companies, and researchers. The ultimate goal of this project is to enhance the predictive capabilities concerning demand loss due to different causes of outages, thereby contributing to more resilient and responsive power systems.

### Data Cleaning Process

  First, I start by dropping columns that won't be necessary for my analysis. These include columns such as land area which can make my model for predicting demand loss more complicated as they are probably not as highly correlated as other columns such as cause category or state. I also wanted to get rid of columns that will not be available at the time of prediction for demand loss. These include information about how long the outage was or when it was restored since we want to predict demand loss as soon as the outage happens. Some other columns I dropped were highly correlated columns such as anomaly level and climate category to avoid multicollinearity. This was a tricky one since anomaly level is a numeric continuous data while climate category is categorical. I have come to the conclusion that it might be more beneficial to use climate category for the case of my analysis since it is more simple and anomaly level might increase the complexity of the model a little bit to high, causing too much variation.

  Additionally, I dropped columns that I don't think would be as relevant to my analysis such as the hurricane type which also had a lot of missing values since most of the outages are not caused by hurricanes.
  
  After dropping these columns, I ended up with the ones I show above which are : [`year`, `month`, `u.s._state`, `nerc.region`, `climate.category`, `outage.start.date`, `outage.start.time`, `cause.category`, `cause.category.detail`,  `demand.loss.mw`, `customers.affected`, `res.sales`, `com.sales`, `ind.sales`, `total.sales`, `population`, `popden_urban`, `popden_uc`, `popden_rural`]

  I also wanted to combine the columns outage start date and time as one series with dtype timestamp object in `outage.start` column. I dropped the other columns since I will not need them anymore.

  As I was looking at the dataset, I have realized that demand loss and customers affected had a lot of 0 values. Since this cannot be possible, I decided to change them with nan values. After that, I also dropped the rows where demand loss had missing values because it is the column I am trying to predict and missing values can mess with my model.

  Here is a sample of the dataset:

  |   OBS |   year |   month | u.s._state   | nerc.region   | climate.category   | outage.start.date           | outage.start.time   | cause.category                | cause.category.detail   |   demand.loss.mw |   customers.affected |   res.sales |   com.sales |   ind.sales |   total.sales |   population |   popden_urban |   popden_uc |   popden_rural |\n|------:|-------:|--------:|:-------------|:--------------|:-------------------|:----------------------------|:--------------------|:------------------------------|:------------------------|-----------------:|---------------------:|------------:|------------:|------------:|--------------:|-------------:|---------------:|------------:|---------------:|\n|   624 |   2011 |      10 | Pennsylvania | RFC           | cold               | Tuesday, October 18, 2011   | 3:45:00 AM          | intentional attack            | vandalism               |                0 |                    0 | 3.61972e+06 | 3.47153e+06 | 4.13196e+06 |   1.12879e+07 |     12745202 |         2123.4 |      1528.6 |           67.7 |\n|  1226 |   2015 |       6 | California   | WECC          | warm               | Saturday, June 20, 2015     | 1:52:00 PM          | intentional attack            | vandalism               |              nan |                    0 | 7.19206e+06 | 9.94213e+06 | 4.69385e+06 |   2.19015e+07 |     39144818 |         4303.7 |      2124.1 |           12.7 |\n|  1176 |   2010 |       1 | California   | WECC          | warm               | Wednesday, January 20, 2010 | 1:00:00 PM          | severe weather                | storm                   |              nan |               147223 | 7.62945e+06 | 9.0208e+06  | 3.53787e+06 |   2.0256e+07  |     37334079 |         4303.7 |      2124.1 |           12.7 |\n|   705 |   2013 |       7 | Ohio         | RFC           | normal             | Wednesday, July 10, 2013    | 5:30:00 PM          | severe weather                | thunderstorm            |              nan |               122314 | 5.1172e+06  | 4.40552e+06 | 4.39368e+06 |   1.39192e+07 |     11572232 |         2033.7 |      1740.1 |           69.9 |\n|  1230 |   2008 |       7 | California   | WECC          | normal             | Wednesday, July 2, 2008     | 7:16:00 PM          | severe weather                | wildfire                |              208 |               200000 | 9.33413e+06 | 1.2179e+07  | 4.70113e+06 |   2.62885e+07 |     36604337 |         4303.7 |      2124.1 |           12.7 |\n|   640 |   2015 |       6 | Kentucky     | SERC          | warm               | Monday, June 1, 2015        | 12:27:00 AM         | intentional attack            | vandalism               |                2 |                  110 | 2.20099e+06 | 1.67262e+06 | 2.56922e+06 |   6.44283e+06 |      4425092 |         1795.8 |      1351.7 |           47.4 |\n|  1093 |   2010 |       9 | California   | WECC          | cold               | Monday, September 27, 2010  | 3:15:00 PM          | system operability disruption | nan                     |              595 |                  nan | 8.25382e+06 | 1.1236e+07  | 4.43606e+06 |   2.39959e+07 |     37334079 |         4303.7 |      2124.1 |           12.7 |
 


<iframe
  src="assets/figure.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/frequency_cause.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
