# Air Quality Prediction using Weather Features
In this project, we are going to build a Air Quality Prediction Service. The prediction (PM25) is made based on weather data ( Temperature, Precipitation, Wind speed, Wind direction & Previous days AR)

The project is based on the book [Building Machine Learning Systems with a Feature Store](https://www.oreilly.com/library/view/building-machine-learning/9781098165222/) and the course Scalable Machine Learning and Deep Learning by [KTH University](https://www.kth.se/en)

## Demo
The predicted air quality of Hanoi, the lovely capital of Vietnam.

📌 *The prediction pipeline is run, and the figure below is updated daily.*

[Demo Url](https://imminh123.github.io/air-quality-prediction-service/air-quality/)

![PM25 Predicted (Log Scale) for Hanoi](https://github.com/imminh123/air-quality-prediction-service/blob/main/docs/air-quality/assets/img/pm25_forecast.png?raw=true)


## Project Components

### Backfill Feature Pipeline
1. Download a CSV file with historical air quality data of Hanoi for the past 5 years, and historical weather data since the start date of the AQ dataset.

2. Create & upload cleaned data to 2 Hopsworks feature groups for model training and predictions.

### Daily Feature Pipeline
Schedule a daily pipeline to download the latest weather data, air quality data, and weather predictions for the next 7 days.

### Training Pipeline
1. Base on features fetched from Feature View in Hopsworks, we train a model to predict air quality (PM2.5) using historical data. We use XGBRegressor - a regression model implemented in XGBoost.
2. The model is used to predict PM2.5 air quality & uploaded to Hopsworks for future inference pipelines.

### Batch Inference Pipeline
Download the registered model from Hopswork and perform batch inference to predict air quality for the next 7 days and visualize the predictions in a [dashboard](https://imminh123.github.io/air-quality-prediction-service/air-quality/).

### Model Monitoring & Accuracy Evaluation
Monitor the performance of the model and assess prediction accuracy. We will asses the prediction before and after adding the lagged PM25 values feature.

Without the lagged air quality, **temperature** is the feature with the highest weight on prediction.
![Feature importance](https://github.com/imminh123/air-quality-prediction-service/blob/main/notebooks/ch03/air_quality_model/images_historical/feature_importance.png?raw=true)


Comparing the predicted vs. actual values of PM2.5, we can see that although the trend is similar, the actual values are considerably off compared to the predicted ones. We hope to improve this by incorporating a new feature that already exists in our dataset.

![Predicted vs Actual Prediction](https://github.com/imminh123/air-quality-prediction-service/blob/main/notebooks/ch03/air_quality_model/images_historical/pm25_hindcast.png?raw=true)


### Introducing New Feature (Lagged Air Quality)
Improve model accuracy by incorporating additional feature of **lagged air quality for the previous 1-3 days**.

#### PM2.5 Prediction with Lag AQ Features 
To predict PM2.5 values for the next day while incorporating lagged features from the previous 3 days, the process involves iteratively forecasting one day at a time and using the predicted PM2.5 values as the new lagged features for subsequent predictions.

**New features for lagged air quality from 1 to 3 days**</br>
<img src="https://github.com/imminh123/air-quality-prediction-service/blob/main/notebooks/ch03/air_quality_model/images_historical/new_lagged_aq.png?raw=true" alt="predict_pm25_with_lags function" width="400" />

</br>

**Functions to iteratively forecasting 1 day and use it as lagged value for subsequent predictions**

<img src="https://github.com/imminh123/air-quality-prediction-service/blob/main/notebooks/ch03/air_quality_model/images_historical/predict_pm25_with_lagged_aq.png?raw=true" alt="predict_pm25_with_lags function" width="400" />


#### Result
With the introduction of new features related to lagged air quality, there are changes in feature importance. `pm25_lag1`, which represents the PM2.5 level from the previous day, now has the highest importance. `temperature_2m_mean` ranks second, approximate to `pm25_lag2`.

![Feature important with lagged air quality](https://github.com/imminh123/air-quality-prediction-service/blob/main/notebooks/ch03/air_quality_model/images_historical/new_feature_importance.png?raw=true)

We also notice a significant improvement in predict vs actual prediction plot.

![Predict vs actual plot](https://github.com/imminh123/air-quality-prediction-service/blob/main/notebooks/ch03/air_quality_model/images_historical/pm25_hindcast.png?raw=true)

The improvement is also showed by the 1-day hindcast plot comparing the predictions vs outcomes.

![1-day hindcast Predictions vs Outcomes](https://github.com/imminh123/air-quality-prediction-service/blob/main/docs/air-quality/assets/img/pm25_hindcast_1day.png?raw=true)

