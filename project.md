# Predicting Demand Loss in Major Power Outages in Different States

### by Elif Yildiz

In an increasingly electricity-dependent world, understanding the factors contributing to major power outages is vital for minimizing economic and societal disruptions. This project analyzes a comprehensive dataset documenting significant power outages across the United States from January 2000 to July 2016 across different states. Defined by the Department of Energy, these major outages impacted at least 50,000 customers or resulted in an unplanned firm load loss of at least 300 MW.

The dataset is robust, encompassing not only the primary details of the outages, such as their geographical locations, dates, and times, but also data on regional climatic conditions, land-use characteristics, electricity consumption patterns, and economic attributes of the affected states. This multifaceted approach allows for a nuanced analysis of the factors influencing power outages and provides a fertile ground for developing predictive models to estimate demand loss based on various predictors. 

I will start my analysis by cleaning the data, and limiting it to only the columns I need since it contains 57 columns. Some of the columns like 'u.s._state' and 'postal.code' contain almost the same information, which can lead to multicollinearity. The same thing happens for the anomaly levels and climate category as they are highly correlated. Because of this, I will only be using one of each. For the sake of simplicity, I converted all the columns to lower case.

I have also excluded columns that won't be available at the time of predicting the demand loss such as outage duration of restoration date.

Let's take a look at the columns:

| Column | Description |
| ----------- | ----------- |
| `year` | Indicates the year when the outage event occurred |
| `month` | Indicates the month when the outage event occurred |
| `u.s._state` | State the outage occured in |
| `nerc.region` | The North American Electric Reliability Corporation (NERC) regions involved in the outage event |
| `anomaly.level` | This represents the oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season. (as it goes down it's colder) |
| `outage.start.date` | Date of when the outage has started |
| `outage.start.time` | Time of when the outage has started |
| `cause.category` | The general cause of the outage |
| `cause.category.detail` | The detailed cause of the outage |
| `hurricane.names` | If the outage is due to a hurricane, then the hurricane name is given by this variable |
| `demand.loss.mw` | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported] This is also the columns we want to predict |
| `customers.affected` | Number of customers affected by the power outage event |
| `com.sales`	| Electricity consumption in the commercial sector (megawatt-hour) |
| `ind.sales`	| Electricity consumption in the industrial sector (megawatt-hour) |
| `total.sales`| Total electricity consumption in the U.S. state (megawatt-hour) |
| `res.customers` | Annual number of customers served in the residential electricity sector of the U.S. state |
| `com.customers` | Annual number of customers served in the commercial electricity sector of the U.S. state |
| `ind.customers` | Annual number of customers served in the industrial electricity sector of the U.S. state |
| `total.customers` | Annual number of total customers served in the U.S. state |
| `pc.realgsp.state` | Per capita real gross state product (GSP) in the U.S. state |
| ``pc.realgsp.usa`` | Per capita real GSP in the U.S. |
| `pc.realgsp.rel` | Relative per capita real GSP as compared to the total per capita real GDP of the U.S. |
| `pc.realgsp.change` | Percentage change of per capita real GSP from the previous year (in %) |
| `population` | Population in the U.S. state in a year |
| `popden_urban` | Population density of the urban areas (persons per square mile) |
| `popden_rural` | Population density of the rural areas (persons per square mile) |
| `popden_uc` | Population density of the urban clusters (persons per square mile) |



The dataset is available at Purdue University’s Laboratory for Advancing Sustainable Critical Infrastructure, https://engineering.purdue.edu/LASCI/research-data/outages

By examining historical trends and patterns, the dataset facilitates a deeper understanding of the dynamics of power outages. It enables the identification and assessment of key risk factors associated with sustained power outages, thus offering valuable insights for policymakers, utility companies, and researchers. The ultimate goal of this project is to enhance the predictive capabilities concerning demand loss due to different causes of outages, thereby contributing to more resilient and responsive power systems.


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
