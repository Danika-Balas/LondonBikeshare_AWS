# Predicting Bike Share Usage in London
URL: http://latest-londonbikeshare-env.eba-uyfumhmm.us-east-1.elasticbeanstalk.com/prediction

This web application can be used to predict usage of city bike shares in London based on weather, date, and time input data. It uses a Flask API and hosted on AWS. It uses a gradient boosted tree model.

This [Jupyter notebook](LondonBikeshare_modelDev.ipynb) includes the code to develop the predictive model, including EDA, data processing, model training, and model evaluation.

## Web Application
### Enter data

<img src="https://github.com/Danika-Balas/LondonBikeshare/blob/main/images/input_data.png" width="512" />
### Receive a prediction based on input data
<img src="https://github.com/Danika-Balas/LondonBikeshare/blob/main/images/prediction.png" width="512" />

This value is the predicted number bike share rides that will be initiated for a given hour in London.

## Developing the Model
Code available [here](LondonBikeshare_modelDev.ipynb).

Data was sourced from the [Transport for London](<https://cycling.data.tfl.gov.uk/>) open cycling data. This specific dataset was cleaned by Hristo Mavrodiev and can be downloaded [here](<https://www.kaggle.com/hmavrodiev/london-bike-sharing-dataset>).

Several exploratory data analysis techniques were used to understand the dataset and determine which variables would be the most effective to predict the count of bike share rides initiated (cnt).

This scatterplot illustrates the relationship between humidity, hour of the day, and total bike share count per hour.

<img src="https://github.com/Danika-Balas/LondonBikeshare/blob/main/images/scatterplot.png" width="512" />

The heatmap below shows the correlations between each variable.
<img src="https://github.com/Danika-Balas/LondonBikeshare/blob/main/images/heatmap.png" width="512" />

Tempfeel was not selected because it is not independent from temp. The variables **temp**, **windspeed**, **hour**, and **month** were selected as predictors for total hourly rides.

[Gradient boosting regression](<https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.GradientBoostingRegressor.html>) was used to train and compare several ensembles. The final predictive model was based on 5000 trees.


A deep neural network was also trained on this data in [Google Colab](LondonBikeshare_DeepLearning.ipynb) in order to explore using Tensorflow in AWS, but this kind of model is ill-suited for this dataset, and was not used in the final web application.

Flask was then used to develop a web application with a prediction API that could field new queries and return predictions from the gradient boosted tree model.

Finally, the Flask web application was deployed to AWS using Elastic Beanstalk. CodeBuild was used to support a continuous delivery pipeline. Every time that a change is committed to this GitHub repo, it triggers a new build on AWS.

## Deploying locally
1. Clone this repository
2. Create a Python virtualenv and install necessary dependencies

`python3 -m venv ./.lb`

`source ./.lb/bin/activate`

`make all`

3. Run Flask app

`flask run`

4. Open app on local server

Go to http://127.0.0.1:5000/ to enter predictions

## Deploying to AWS
1. Create a new Cloud9 environment
Use this command to set up public keys for environment

`ssh-keygen -t rsa`

Access key to copy it into GitHub for encrypted communication

`cat ~/.ssh/id_rsa.pub`

2. Clone this repository

3. Create a Python virtualenv and install necessary dependencies

`python3 -m venv ~/.eb`

`source ~/.eb/bin/activate`

`make all`

Note, that awsebcli is installed via requirements

4. Initialize Elastic Beanstalk app

`eb init -p python-3.7 flask-londonbikeshare --region us-east-2`

Create ssh keys (optional)

`eb init`

5. Create remote Elastic Beanstalk instance

`eb create flask-continuous-delivery-env`

## Future Directions
* Add more validation requirements to Flask query fields, including minimum and maximum values
* Train LSTM model in order to take advantage of time series data

## References
Deza, A., Gift, N. (2021, February). AWS Elastic Beanstalk continuous delivery with Flask video course. https://learning.oreilly.com/videos/aws-elastic-beanstalk/62022021VIDEOPAIML/

Gupta, S. (2019, December 2). Deploying a machine learning model on the web using Flask and Python. https://medium.com/analytics-vidhya/deploying-a-machine-learning-model-on-web-using-flask-and-python-54b86c44e14a

Mavrodiev, H. (2019, October 10). London bike sharing dataset. https://www.kaggle.com/hmavrodiev/london-bike-sharing-dataset
