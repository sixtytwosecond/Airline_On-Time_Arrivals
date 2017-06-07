# Problem
By using the US Dept. of Transportation on-time arrival data for non-stop domestic flights by major air carriers to predict arrival delays.


# Data Source
The data is downloaded from http://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time 

The following parameters are selected:
```
YEAR, MONTH, DAY_OF_MONTH, ORIGIN_AIRPORT_ID, DEST_AIRPORT_ID, CRS_DEP_TIME, DEP_DELAY, DEP_DEL15, CRS_ARR_TIME, ARR_DELAY, ARR_DEL15, CANCELLED, AIR_TIME, CARRIER_DELAY, WEATHER_DELAY, NAS_DELAY, SECURITY_DELAY, LATE_AIRCRAFT_DELAY
```

# Pre-processing
And these columns are dropped before we train our predictor:
```
ARR_DEL15:          It depends on ARR_DELAY which is already included 
DEP_DELAY:          It is directly related to DEP_DEL15
YEAR:               We only use the data within the same year
MONTH:              We only use the data within the same month
CANCELLED:          It determines if the flight is cancelled
CARRIER_DELAY:      It is directly related to DEP_DELAY
WEATHER_DELAY:      It is directly related to DEP_DELAY
NAS_DELAY:          It is directly related to DEP_DELAY
SECURITY_DELAY:     It is directly related to DEP_DELAY
LATE_AIRCRAFT_DELAY:It is directly related to DEP_DELAY
```
Flight schedules which are cancelled are filtered out and any cell without value will be filled with 0

# Process
We run Random Forest and Logistic Regression and compare their performance.

And then we find out the correlation between 
```
ARR_DELAY 
```
and 
```
CARRIER_DELAY, WEATHER_DELAY, NAS_DELAY, SECURITY_DELAY, LATE_AIRCRAFT_DELAY 
```
to
and understand which factor has the highest correlation to the delay

Finally we run Multivariance Regression between 
```
ARR_DELAY
```
and 
```
DAY_OF_MONTH, ORIGIN_AIRPORT_ID, DEST_AIRPORT_ID, CRS_DEP_TIME CRS_ARR_TIME, ARR_DELAY, AIR_TIME, CARRIER_DELAY, WEATHER_DELAY, NAS_DELAY, SECURITY_DELAY, LATE_AIRCRAFT_DELAY 
```
to predict the Arrival delay in minutes


# Result
The predictor has over 95% accuracy. The performance of Random Forest is better than Logistic Regression in this case as the AUC is larger.

Interestingly CARRIER_DELAY is found to be closely correlated to the Arrival Delay, followed by WEATHER_DELAY and NAS_DELAY.

From the importance table, DEP_DELAY is the most important factor to determine if the flight will delay for 15 minutes which it is not surprised. Interestingly TAXI_OUT and WHEELS_ON are the second and third important factors. 

Furthermore we can predict how many minutes the flight is going to be late by using the result from the Multivariance regression.

## Weakness and Further Improvement
This prediction reies sololy on the flight departure data. If the departure data can only be provided after the flight journy completes. This prediction is literally useless. 

In the future, we can also use the statistic model to calculate the probabily of the delay on a given day or a given flight.

From our correlation result CARRIER_DELAY is closely correlated to the delay, if we can fetch more data on this topic it would be definitely helpful to the prediction.

Although WEATHER_DELAY only shows little correlation to the delay, weather data is easier to acquire on the other hand. It can also improve the accuracy.


## Performance
```
Random Forest
Score: 0.954536458
AUC: 0.985454718
```
```
Logistic Regression
Score: 0.944566310963
AUC: 0.972940120531
```

## Correlation
```
CARRIER_DELAY
Correlation: 0.687167394
Mean: 18.93067658
Stddev: 59.80018393
```	         
```
WEATHER_DELAY
Correlation: 0.254758241
Mean: 2.577601768
Stddev: 32.30374947
```
```
NAS_DELAY
Correlation: 0.240030894
Mean: 15.93859704
Stddev: 59.80018393
```
```
SECURITY_DELAY
Correlation: 0.009949962
Mean: 0.042885597
Stddev: 1.704423366
```

## Importance table by Random Forest
```
          Importances
==============================
DEP_DELAY:         0.54644626
TAXI_OUT:          0.083148708
WHEELS_ON:         0.048965989
AIR_TIME:          0.043947774
CRS_ARR_TIME:      0.043520116
WHEELS_OFF:        0.039560117
CRS_DEP_TIME:      0.037265333
DISTANCE:          0.036743023
TAXI_IN:           0.036419217
DEST_AIRPORT_ID:   0.024682258
ORIGIN_AIRPORT_ID: 0.024181256
DAY_OF_MONTH:      0.022696157
TOTAL_ADD_GTIME:   0.008303234
FIRST_DEP_TIME:    0.004120558
==============================
```

## Multivariance Regression
|OLS Regression Results   ||||
|--|--|--|--|
|**Dep. Variable**|ARR_DELAY|**R-squared**|0.922|
|**Model**|OLS|**Adj. R-squared**|0.922|
|**Method**|Least Squares|**F-statistic**|3.980e+05|
|**Date**|Sun, 04 Jun 2017|**Prob (F-statistic)**|0.00|
|**Time**|19:47:26|**Log-Likelihood**|-1.5608e+06|
|**No. Observations**|404205|**AIC**|3.122e+06|
|**Df Residuals**|404192|**BIC**|3.122e+06|
|**Df Model**|12|||                                       
|**Covariance Type**|nonrobust|||                                     

||coef|std err|t|P>\|t\||[0.025|0.975]|
|--|--|--|--|--|--|--|
|DAY_OF_MONTH|-0.0157| 0.002|-7.046| 0.000|-0.020|-0.011|
|ORIGIN_AIRPORT_ID| 0.0006| 1.2e-05| 50.795| 0.000| 0.001| 0.001|
|DEST_AIRPORT_ID| 0.0005| 1.2e-05| 41.191| 0.000| 0.000| 0.001|
|CRS_DEP_TIME| 0.0020| 0.000| 16.693| 0.000| 0.002| 0.002|
|DEP_DELAY| 0.9823| 0.000| 2063.763| 0.000| 0.981| 0.983|
|TAXI_OUT| 0.7145| 0.002| 344.740| 0.000| 0.710| 0.719|
|WHEELS_OFF|-0.0005| 0.000|-4.219| 0.000|-0.001|-0.000|
|WHEELS_ON| 0.0021| 7.86e-05| 27.196| 0.000| 0.002| 0.002|
|TAXI_IN| 0.6718| 0.003| 198.502| 0.000| 0.665| 0.678|
|CRS_ARR_TIME|-0.0028| 8.22e-05|-34.112| 0.000|-0.003|-0.003|
|AIR_TIME| 0.2482| 0.001| 245.426| 0.000| 0.246| 0.250|
|FLIGHTS|-38.7498| 0.226|-171.525| 0.000|-39.193|-38.307|
|DISTANCE|-0.0328| 0.000|-268.129| 0.000|-0.033|-0.033|

|||||
|--|--|--|--|
|Omnibus|757746.458|Durbin-Watson|1.328|
|Prob(Omnibus)|0.000|Jarque-Bera (JB)|33790739356.903|
|Skew|-13.008|Prob(JB)|0.00|
|Kurtosis|1419.219|Cond. No.|2.28e+05|

