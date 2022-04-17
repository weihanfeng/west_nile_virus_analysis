# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) West Nile Virus Analysis

## Introduction & Problem Statement 

You just graduated from General Assembly Data Science Immersive program and managed to secure an interview with the Disease And Treatment Agency, division of Societal Cures In Epidemiology and New Creative Engineering (DATA-SCIENCE). You have been assigned into a group with other interviewees and was given a take home BI test. You will be required to work together and present as a group during the interview. Time to look at the test!

Due to the recent epidemic of West Nile Virus in the Windy City, we've had the Department of Public Health set up a surveillance and control system. We're hoping it will let us learn something from the mosquito population as we collect data over time. Pesticides are a necessary evil in the fight for public health and safety, not to mention expensive! We need to derive an effective plan to deploy pesticides throughout the city, and that is **exactly** where you come in!


## Dataset

The datasets used consists of:

[`train.csv`](datasets/original_data/train.csv): The training set consists of data from the years 2007, 2009, 2011, and 2013

[`test.csv`](datasets/original_data/test.csv): The test set consists of data for 2008, 2010, 2012, and 2014. The test set is similar to the training set other than the lack of WnvPresent and NumMosquitos columns, which we are required to predict.

[`spray.csv`](datasets/original_data/spray.csv): Records of spraying efforts in Chicago in 2011 and 2013

[`weather.csv`](datasets/original_data/weather.csv): Weather data of Chicago from 2007 to 2014.


## Findings from Exploratory Data Analysis
Some interesting observations were gathered through the EDA process. 

**Imbalanced class**:The response feature `WnvPresent` for the training set is imbalanced. Oversampling of minority class has to be conducted for preprocessing. 

**Lack of representation for non-Pippens/Restuans species**: The `Species` Features are also highly imbalanced. Furthermore, these minority rows have all 0 values for WnvPresent, which may mean that our model may not be able to generalize well.

**Spatial-Temporal Relations**: A level of time and spatial correlation between observations were observed. 

**Lack of linear correlation**: We did not observa a strong linear correlation between predictors and the response variable.

## Feature Engineering
Some steps were performed for feature engineering:

1. Wind data were processed to account for the effect of Lake Michigan, an important water body of Chicago

2. One hot encoding for categorical variables such as `Species`, `Month`, etc

3. A feature was created to indicate if the row is of pippiens/restuans species or not

4. Lagged weather features by 5-14 days to account for time series relations

5. A weighted distance matrix created to account for spatial autocorrelation

## Modelling
A two step approach was developed for modelling. First, a regression model was fitted to the training data to obtain the `NumMosquitos` prediction for the test data. This is followed by a classification model to obtain predictions for the `WnvPresent`. 

During preprocessing, steps such as oversampling of minority class, scaling and Principle Component Analysis (PCA) were performed. 

For regression, the following models were put through a 5-fold cross-validation and tuned using Root Mean Squared Error(RMSE) as metric:
- Linear Regression 
- Ridge Regression
- Lasso Regression
- Elastic Net
- Poission Regression
- KNN Regression
- Random Forest Regression

The best model was found to be random forest with with an RMSE of 7.7 and a distribution that resembles the training dataset.

For Classification, 3 models were put through a 5-fold cross-validation, we assessed multiple metrics including ROC-AUC score, accuracy, recall and precision. 

- Logistics Regression
- Gradient Boosting Classifier
- Random Forest Classifier

The best model chosen was the random forest classifier, which achieved a ROC-AUC score of 0.91 and an accuracy of 0.93. The model also has 0.70 for recall and 0.14 for precision. Due to our focus on curbing future outbreaks, the low precision is not a focus for this project, although future improvements such as better features, more rigourous feature selection and hyperparameter tuning, and more advanced models such as XGboost and Recurrent Neural Networks would definitely serve to improve our results.

## Cost Benefit Analysis
A simple cost function was set up. The costs were driven by two main factors, namely healthcare/productivity costs and spraying costs. 
In our research, it was found that each spray was estimated to reduce the mosquito population by 65.3%.
Each spray was projected to bring the costs down significantly, till the spray cost dominated the total costs. 

In summary, the total minimized costs were estimated at $669,321, with a requirement of 9 sprays.

## Conclusions and Recommendations
#### Analysis on Feature Importance - NumMosquitos

Based on the plots provided, we see that the species type, trap location and weather data play a significant role in determining the model's prediction on the NumMosquitos. 

While this is fairly obvious, the interesting highlight is with the features <u>"Month 7" and "Month 8"</u> being highlighted as immporant features. From this, we are now able to confidently predict that the <u>months of July and August are likely to see a sharp spike in the number of mosquitos</u>. 

#### Analysis on Feature Importance - WnvPresent

Likewise, we see that some traps <u>selected traps</u> have a significant role in determining the predictability of WNV prevalence. This signals that these traps are likely to have some inherent features which encourages/discourages the presence of the Wnv Virus. 

### Recommendations

#### 1. Preventive spraying
While the strategies may differ, one potential approach is to <u>conduct spraying in the earlier months</u> to reduce the number of mosquitos early. This should have an effect of reducing the number of mosquitos able to lay eggs in the water, and hence limit the ability of the mosquitos to have a wide explosion in numbers.

#### 2. Increase Surveillance and Data Quality

Given the poor correlation between the target and the features available, we are unable to infer any reliable insights into the trap locations. However, given the current model, more resources should be dedicated towards these traps.

For example, given the relationship with birds in the ecosystem, <u>data on migratory flight patterns and bird species should be explored to give the model more training data</u>. Also, given that humans are the direct target of the effort, some resources should be put into <u>obtaining the population data at a more granular level</u>. Combining these data points along with classical population modelling techniques, the model's accuracy can then be further improved.  
