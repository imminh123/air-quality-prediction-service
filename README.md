# Air Quality Prediction using Weather Features
In this project, we are going to build a Air Quality Prediction Service. The prediction (PM25) is made based on weather data ( Temperature, Precipitation, Wind speed, Wind direction & Previous days AR)

The project is based on the book [Building Machine Learning Systems with a Feature Store](https://www.oreilly.com/library/view/building-machine-learning/9781098165222/) and the course Scalable Machine Learning and Deep Learning by [KTH University](https://www.kth.se/en)

## Demo
The predicted air quality of Hanoi, the lovely capital of Vietnam.
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

Without the lagged air quality, air quality is the feature with the highest weight on prediction.
![Feature importance](https://github.com/imminh123/air-quality-prediction-service/blob/main/notebooks/ch03/air_quality_model/images_historical/feature_importance.png?raw=true)


Comparing the predicted vs. actual values of PM2.5, we can see that although the trend is similar, the actual values are considerably off compared to the predicted ones. We hope to improve this by incorporating a new feature that already exists in our dataset.

![Predicted vs Actual Prediction](https://github.com/imminh123/air-quality-prediction-service/blob/main/notebooks/ch03/air_quality_model/images_historical/pm25_hindcast.png?raw=true)


### Introducing New Feature (Lagged Air Quality)
Improve model accuracy by incorporating additional feature of **lagged air quality for the previous 1-3 days**.

#### PM2.5 Prediction with Lag AQ Features 
To predict PM2.5 values for the next day while incorporating lagged features from the previous 3 days, the process involves iteratively forecasting one day at a time and using the predicted PM2.5 values as the new lagged features for subsequent predictions.

#### Result

