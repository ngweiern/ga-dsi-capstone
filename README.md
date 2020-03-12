# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) Capstone Project: Prediction of travel time for flights from Taiwan to Hong Kong

## Table of Contents
- [Executive Summary](#Executive-Summary)
- [Introduction](#Introduction)
- [Problem Statement](#Problem-Statement)
- [Overview](#Overview)
- [Datasets](#Datasets)
- [Conclusion and Recommendations](#Conclusion-and-Recommendations)

--- 

### Executive Summary

This project aims to build a model which predicts the travel duration of flights departing from Taiwan Taoyuan International Airport (IATA: TPE, ICAO: RCTP) and are headed to Hong Kong International Airport (IATA: HKG, ICAO: VHHH). This predictive model will be valuable to the arrival airport's management in terms of ground resource optimization and scheduling, and also to ride-hailing services whereby drivers who subscribed to a particular ride-hailing service may be notified of a possible surge in demand of passengers at the arrival airport and respond to that demand in time, thereby maximizing their revenue. 

The datasets provided consist of radar tracking information of flights which departed from Taiwan Taoyuan International Airport and arrived at Hong Kong International Airport in January 2017. The datasets were obtained from a commercial flight tracking website, FlightRadar24. Weather information at the arrival airport, Hong Kong International Airport, was obtained from Iowa Environmental Mesonet (IEM).

An algorithm was implemented to calculate the time since departure of each available record in the radar tracking information by deducting from the timestamp of each record the real departure time provided in the flight summary information. Thereafter, flights with complete travel information, from departure to arrival, are retrieved for analysis, by checking the radar records whether they have reached a final altitude of 0 after departing from the origin airport. The record in which the aircraft first achieved an altitude of 0 is taken as the time at which the aircraft has completed the route. The total flight duration is then calculated by taking this time and deducting from it its departure time.

The analysis of each variable showed that most features of the dataset were not normally distributed and often skewed.

Pairwise analysis of features with the target variable, calculated time before arrival, revealed that there are moderate to strong positive correlation for spatial features such as latitude, longitude, and altitude.

Categorical features such as flight callsign, aircraft model, aircraft registration, and airline were subject to an ANOVA test but none of the tests achieved a p-value < 0.05. Thus, it was deduced that all levels or groups in these features have equal variabe and hence, none were likely to have an impact on the target variable and were omitted from the model.

The final features chosen were latitude, longitude, altitude, heading, and speed and the models used in this analysis were linear regression, lasso regression, ridge regression, elasticnet regression, random forest regression, and adaboost.

The random forest regressor turned out to be the best model with the lowest RMSE of 300 seconds and the highest adjust R2 value of 0.975. A calculation of the feature importance in the random forest regression model indicated that the longitude, latitude, and altitude were important features, whereas the heading and speed had negligible importance.

---

### Introduction

This project is an analysis of all flights complete information information pertaining to its departure and arrival from Taiwan Taoyuan International Airport (IATA: TPE, ICAO: RCTP) to Hong Kong International Airport (IATA: HKG, ICAO: VHHH), in Jan 2017. It studies and identifies patterns in the flight's spatial-temporal data and performs the necessary computation to identify the time at which an aircraft arrives at the destination airport, Hong Kong International Airport. In so doing, it aims to identify the best possible predictive model for estimating time taken for an aircraft, plying the aforementioned route, at any point in time at which it is detected on the flight radar to arrive to Hong Kong.  This predictive model will be valuable to the arrival airport's management in terms of ground resource optimization and scheduling, and also to ride-hailing services whereby drivers who subscribed to a particular ride-hailing service may be notified of a possible surge in demand of passengers at the arrival airport and respond to that demand in a timely fashion, thereby maximizing revenue.

---

### Problem Statement

To predict the estimated time taken for an aircraft, plying the route from from Taiwan Taoyuan International Airport (IATA: TPE, ICAO: RCTP) to Hong Kong International Airport (IATA: HKG, ICAO: VHHH), at any point in time at which it is detected on the flight radar to arrive to Hong Kong.

---

### Overview

The pipeline:
- Data Cleaning
- Exploratory Data Analysis
- Data Visualization
- Descriptive and Inferential Statistics
- Outside Research
- Conclusions and Recommendations

---

### Datasets

#### Provided Data

For this project, there are four datasets provided:

- [2017 SAT Scores](./data/sat_2017.csv)
- [2018 SAT Scores](./data/sat_2018.csv)
- [2017 ACT Scores](./data/act_2017.csv)
- [2018 ACT Scores](./data/act_2018.csv)

These data give average SAT and ACT scores by state, as well as participation rates, for the graduating class of 2017 and for that of 2018.

#### Data Dictionary

**admin.csv**
|Feature|Type|Description|
|:---|:---|:---|
| **flight_id**               | *object* |Unique identifier for every flight record|
| **flight_callsign**         | *object* |Used by Air Traffic Control to denote a specific flight|
| **aircraft_model**          | *object* |Aircraft model of the flight|
| **aircraft_registration**   | *object* |Each aircraft is assigned a registration number—often called a tail number—that is unique to the aircraft. Registration numbers are based on the country of registration, with the United Kingdom employing 'G' as their first letter identifier, followed by a hyphen and four further letters e.g. G-STBA. In the United States 'N' is used as the first letter followed by either letters or numbers e.g. N463UA.|
| **airline**                 | *object* |Airline operating the flight|
| **origin**                  | *object* |Country which the flight departed from |
| **destination**             | *object* |Country which the flight arrived at|
| **scheduled_departure_utc** |  *float* |Scheduled departure in Unix Time (UTC); elapsed seconds since January 1st 1970 00:00:00 UTC (Universal Time)|
| **scheduled_arrival_utc**   |  *float* |Scheduled arrival in Unix Time (UTC); elapsed seconds since January 1st 1970 00:00:00 UTC (Universal Time)|
| **real_departure_utc**      |  *float* |Real departure in Unix Time (UTC); elapsed seconds since January 1st 1970 00:00:00 UTC (Universal Time)|
| **estimated_arrival_utc**   |  *float* |Estimated arrival in Unix Time (UTC); elapsed seconds since January 1st 1970 00:00:00 UTC (Universal Time)|
| **real_flight_duration**    |  *float* |Duration in which the plane is in the air (in seconds)|


**metar-vhhh.csv**
|Feature|Type|Description|
|:---|:---|:---|
| **timestamp**               | *datetime* |Timestamp of observation (YYYY-MM-DD HH:MM)|
| **lon**         | *float* |Longitude (WGS 84)|
| **lat**          | *float* |Latitude (WGS 84)|
| **air_temp**   | *float* |Air Temperature in Fahrenheit, typically @ 2 meters|
| **dewpoint_temp**                 | *float* |Dew Point Temperature in Fahrenheit, typically @ 2 meters|
| **rel_humidity**                  | *float* |Relative Humidity in %|
| **wind_dir**             | *int* |Wind Direction in degrees from north|
| **wind_speed** |  *float* |Wind Speed in knots|
| **precipitation**   |  *float* |One hour precipitation for the period from the observation time to the time of the previous hourly precipitation reset. Values are in inches. This value may or may not contain frozen precipitation melted by some device on the sensor or estimated by some other means.|
| **altimeter_pressure**      |  *float* |Pressure altimeter in inches|
| **sealevel_pressure**   |  *float* |Sea Level Pressure in millibar|
| **visibility**    |  *float* |Visibility in miles|
| **gust_speed**               | *int* |Wind Gust in knots|
| **sky_level_1_coverage**         | *object* |Sky Level 1 Coverage|
| **sky_level_2_coverage**          | *object* |Sky Level 2 Coverage|
| **sky_level_3_coverage**   | *object* |Sky Level 3 Coverage|
| **sky_level_4_coverage**                 | *object* |Sky Level 4 Coverage|
| **sky_level_1_altitude**                  | *int* |Sky Level 1 Altitude in feet |
| **sky_level_2_altitude**             | *int* |Sky Level 2 Altitude in feet|
| **sky_level_3_altitude** |  *int* |Sky Level 3 Altitude in feet|
| **sky_level_4_altitude**   |  *int* |Sky Level 4 Altitude in feet|
| **weather_codes**      |  *object* |Present Weather Codes (space seperated)|
| **ice_accretion_1hr**   |  *float* |Ice Accretion over 1 Hour (inches)|
| **ice_accretion_3hr**    |  *float* |Ice Accretion over 3 Hours (inches)|
| **ice_accretion_6hr**                  | *float* |Ice Accretion over 6 Hours (inches)|
| **peak_wind_gust**             | *float* |Peak Wind Gust (from PK WND METAR remark) (knots)|
| **peak_wind_drct** |  *float* |Peak Wind Gust Direction (from PK WND METAR remark) (deg)|
| **peak_wind_time**   |  *time* |Peak Wind Gust Time (from PK WND METAR remark)|
| **apparent_temp**      |  *float* |Apparent Temperature (Wind Chill or Heat Index) in Fahrenheit|
| **metar**   |  *object* |Unprocessed reported observation in METAR format


**trails-twn-to-hkg.feather**
|Feature|Type|Description|
|:---|:---|:---|
| **flight_id**               | *object* |Unique identifier for every flight record|
| **timestamp_utc**         | *int* |Time of observation in Unix Time (UTC); elapsed seconds since January 1st 1970 00:00:00 UTC (Universal Time)|
| **timestamp_dt**         | *int* |Timestamp of observation (YYYY-MM-DD HH:MM)|
| **latitude**          | *float* |Latitude of aircraft's coordinate (WGS 84) at time of observation|
| **longitude**   | *float* |Longitude of aircraft's coordinate (WGS 84) at time of observation|
| **altitude** | *int* |Altitude of aircraft (feet above mean sea level) at time of observation|
| **heading** | *int* |Compass heading of aircraft, denoted as 0-359|
| **speed** | *int* |Speed of the aircraft over the ground, measured in Knots (nautical miles per hour)|

### Conclusion and Recommendations

Although the participation rates of either test are negatively correlated with each of the scores of the respective test's section, it does not suggest a causal relationship between these pairs of variables, specifically, reducing the scores by making the test more difficult for example would not necessarily result in an increase in the participation rates.

Given that the participation rates are highest in states where the SAT is mandatory and paid for, the College Board could lobby the state government of North Dakota, which has the lowest SAT participation rates for both years and does not mandate either the ACT or SAT nor pays for either tests, to adopt the SAT as the state's compulsory examination for high-schoolers and perhaps even sponsor the test.

Additional data such as the universities within each state and the ranking of the universities throughout the U.S. might provide additional insight into the participation rate of either test as there may be individuals who take one test over the other with the intention of entering his/her desired college which may require either the SAT or the ACT.