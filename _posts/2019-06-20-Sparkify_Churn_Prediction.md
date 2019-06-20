---
layout: post
title: Sparkify Churn Prediction
---

![Sparkify](..\images\2019-06-20-Sparkify_Churn_Prediction\sparkify.png)

# Project Definition

## Project Overview
Recently I am finishing up the Udacity Data Science Nanodegree. In order to graduate, each person need to finish a capstone project on their chosen area of interest. I have choesen the Spark project, which is to manipulate large and realistic datasets with Spark to engineer relevant features and implement machine learning models for predicting churn. 

The data I used in the project can be found in this [link](https://s3.amazonaws.com/video.udacity-data.com/topher/2018/December/5c1d6681_medium-sparkify-event-data/medium-sparkify-event-data.json)


## Problem Statement
Imagine you are working on the data team for a popular digital music service similar to Spotify or Pandora. Many of their users stream their favorite songs to your service everyday either using the free tier that place advertisements between the songs or use the premium subscription model, where they stream music as free but pay a monthly flat rate. Users can upgrade, downgrade or cancel their service at any time, so it's crucial to maker sure your users love the service. 
![Spotify](https://www.thestar.com.my/~/media/online/2018/09/06/12/22/spotifybloomberg.ashx/?w=620&h=413&crop=1&hash=B2A1B10B976D404846953E29B7629E919E45F163)

*Photo via thestar.com*

Every time a user interacts with the service while they are playing songs, logging out, like a song with a thumbs up, hearing an ad, or downgrading the service, it generates data. All the data contains key insights for keeping your users happy and helping your business thrive.
![Pandora](https://image.cnbcfm.com/api/v1/image/104520466-GettyImages-680026216-pandora.jpg?v=1537788254&w=630&h=419)

*Photo via barrons.com*

So it is very useful to predict which user are at risk to churn, either downgrading from premium to free tier or cancelling their service altogether. If you can accuartely identify these users before they leave, your business can offer them discounts and incentives, potentially save your business millions in revenue.

To tackle this project, I use a large dataset that contains the events described above. I will load, expolre and clean the dataset with Spark. After explorationg, I will create features and build machine learning models with Spark to predict which user were churn from the digital music service.

## Metrics
Since churn is a binary variable, metrics sepcific to classification problem should be used. For this project, I will use AUC(area under ROC curve) as final metrics, since the presicion, recal and F1 score all involve in selecting a threshold for the classification, while AUC is consisting of true positive rate and false positive rate of all possible threshold. Selecting an threshold is especially curcial in this project since we may want to embed the true positive rate and false positive rate into future encomics analysis.(Trade-off between losing churn users and giving incentive to non-churn user)


# Analysis

## Data Exploration
Our data is a sample of an event log between October and November 2018. In our 237 MB sample data we have 543705 events of 448 users with the following 18 features:

- artist: Artist of the song being played, if the event does not concern listening to a song the field is null.
- auth (authentication): Whether the user Logged in, Logged out, is canceled or guest.
- firstName: First name of the user. Null if the user has not logged in.
- gender: Gender of the user (M/F), null if the user has not logged in.
- itemInSession: Number of events so far in the session. Starts at 0.
- lastName: Last name of the user. Null if the user has not logged in.
- length: Time in seconds that a song has been played. if the event does not concert listening to a song the field is null.
- level: Whether the user has a paid subscription (paid/free).
- location: Location of the user in the format city, state. All users are from the USA.
- page: Page where the event took place. The following pages exist in the dataset: Cancel, Submit Downgrade, Thumbs Down, Home, Downgrade, Roll Advert, Logout, Save Settings, Cancellation Confirmation, About, Submit Registration, Settings, Login, Register, Add to Playlist, Add Friend, NextSong, Thumbs Up, Help, Upgrade, Error and Submit Upgrade.
- registration: Registration number associated with each user.
- sessionId: Number of the session initiated.
- song: Name of the song being played.
- status: HTTP status code (200, 307, 404).
- ts: Timestamp in milliseconds.
- userAgent: Information about the device and browser accessing the data.
- userId: Id associated with each user

### Check null or empty values
The data do not have any null value in the data, while there are some entries with empty userId. Since data without userId cannot be used for any feature construction or prediction, we will remove it from our data. After removing these records, the number of rows decrease by 2.9%, which is not a significant data loss.

Another column that is worth checking is sessionId for empty values. Surprisingly in this data, sessionId is recorded as numeric, so it will not have empty value.

### Turn ts column into correct timestamp
Since ts column is in milliseconds, we need to convert it into normal timestamp. We will first devide the milliseconds by 1000, and use `to_timestamp` function to convert it.

### Define Churn target variable
We will define `Churn` as, in the `page` variable, if a user has ever had `Cancellation Confirmation` status, we will categorize the user as `Churn`. Below is the churn distribution of users:

| Churn | count |
|-------|-------|
| 1     | 99    |
| 0     | 349   |

## Data Visualization
I have explored a lot of relevant columns and calculated fields, and plot the relationship between them and the `Churn` target variable. Here I will put some of the most insightful and significant results. For all the plots, please view my databricks notebook.

### Number of operations
First let us look at the number of operations. The operation is defined as an event a user has been through, a.k.a a row in the dataset. We will calcualte the number of opereation for each user, and check whether the means of number of operations are different between churn group and non-churn group.

**Mean of number of operations between churn and non-churn group**
![mean_operation](..\images\2019-06-20-Sparkify_Churn_Prediction\mean_operation.png)

We can see non-churn group has a higher number of operations, which make sense because we would think heavy user of the service will less likely to churn.

### User has ever been paid user
Next let's look at whether the user has ever been a paid user. I will define this variable as, in the history of the user in the data, if the `level` variable has ever been 'paid', I will define the `ever_paid` varaible as 1.

**Stacked bar chart of churn rate between user has ever been paid user or not**
![ever_paid](..\images\2019-06-20-Sparkify_Churn_Prediction\ever_paid.png)

Suprisingly, the churn rate is higher in the paid user group! The rationale behind it might be, when user want to downgrade their premium service, sometime they may go the cancellation page, which has similar purpose, and cancel the service.

### Region
In the data, the `location` variable has the city name and state name. We will first extract the state information from it. And notice that some state are recorded as multi-state such as 'PA-NJ', we will extract the first state from it. Then we will use a dictionary to convert the state into the region in US.

**Stacked bar chart of churn rate between different regions**
![region](..\images\2019-06-20-Sparkify_Churn_Prediction\region.png)

We can see that the sourthern regions have higher churn rate.

### PUT percentage
There are two categories in the `method` column: PUT and GET. PUT request are usually streaming songs, and GET are usually webpage operetions. We want to see for user with different PUT percentage over total operations, whether there is difference in churn rate.

**Mean of PUT percentage between churn and non-churn group**
![PUT_percentage](..\images\2019-06-20-Sparkify_Churn_Prediction\PUT_percentage.png)

We can see that for the churn group, the PUT percnetage is lower, which means the users in the churn group are streaming less songs than the non-churn group.

### Platform
In the `userAgent` column, there are five kinds of devices in total: "Macintosh", "Windows", "Linux", "iPhone" and "iPad". We will extract the device platform information from the column, and try to see whether the churn rate is different across different platform.

**Stacked bar chart of churn rate between different platforms**
![platform](..\images\2019-06-20-Sparkify_Churn_Prediction\platform.png)

We can see iphone has generally higher churn rate, ipad user do not have any churn, and the rest are about the same.

### Tenure days
We also want to know how new is the user. Tenure day is defined as the difference between the max and min timestamp. If the tenure day of the user is lower, probably he will be more likely to churn.

**Mean  of tenure days between churn and non-churn group**
![tenure_days](..\images\2019-06-20-Sparkify_Churn_Prediction\tenure_days.png)

We can see that churn group has lower tenure days on average.


# Methodology

## Data Preprocessing
The list of features that is created from the data is listed below, along with their definitions and calcualtions:
- `ft_gender`: raw gender column from data
- `ft_n_operation`: count of rows per user
- `ft_n_session`: count of distinct sessionId per user
- `ft_operation_per_session`: `ft_n_operation` over `ft_n_session`
- `ft_mean_length`: average length of the song per user
- `ft_ever_paid`: user ever has 'paid' in his `level` column
- `ft_region`: user location extracts out state, then map to region
- `ft_PUT_perc`: percentage of PUT request over all operation per user
- `ft_platform`: extract device platform information from `userAgent` column
- `ft_tenure_days`: date difference between max and min `ts` of a user
- `ft_avg_time_per_session`: average of second difference between max and min `ts` of all sessions for a user
- `ft_min_time_per_session`: min of second difference between max and min `ts` of all sessions for a user
-  `ft_max_time_per_session`: max of second difference between max and min `ts` of all sessions for a user
-  `ft_n_songs`: number of distinct songs one user has listened
-  `ft_mean_song_per_session`: average of number of distinct songs in all sessions of one user

```python
df_with_churn_feature = df_with_churn\
  .withColumnRenamed('gender','ft_gender')\
  .withColumn('ft_n_operation',F.count('*').over(wf_user))\
  .withColumn('ft_n_session',
              F.size(F.collect_set(F.col('sessionId')).over(wf_user)))\
  .withColumn('ft_operation_per_session',F.col('ft_n_operation')/F.col('ft_n_session'))\
  .withColumn('ft_mean_length',F.mean('length').over(wf_user))\
  .withColumn('level_list',F.collect_set(F.col('level')).over(wf_user))\
  .withColumn('ft_ever_paid',
              F.udf(lambda x: int('paid' in x), IntegerType())('level_list'))\
  .withColumn('state',F.split(F.col('location'),',')[1])\
  .withColumn('state',F.split(F.col('state'),'-')[0])\
  .withColumn('ft_region',F.udf(lambda x: regions[x.strip()], StringType())('state'))\
  .withColumn('n_PUT',F.sum(F.when(F.col('method')=='PUT',1).otherwise(0)).over(wf_user))\
  .withColumn('ft_PUT_perc',F.col('n_PUT')/F.col('ft_n_operation'))\
  .withColumn('ft_platform',
              F.udf(lambda x: re.search(r'Macintosh|Windows|Linux|iPhone|iPad',x)\
                    .group(0))('userAgent'))\
  .withColumn('ft_tenure_days',
                  F.datediff(F.max('ts').over(wf_user).cast(DateType()),
                             F.min('ts').over(wf_user).cast(DateType())))\
  .withColumn('time_per_session',
              F.unix_timestamp(F.max('ts').over(wf_session)) - 
              F.unix_timestamp(F.min('ts').over(wf_session)))\
  .withColumn('ft_avg_time_per_session',F.mean('time_per_session').over(wf_user))\
  .withColumn('ft_min_time_per_session',F.min('time_per_session').over(wf_user))\
  .withColumn('ft_max_time_per_session',F.max('time_per_session').over(wf_user))\
  .withColumn('ft_n_songs',F.size(F.collect_set(F.col('song')).over(wf_user)))\
  .withColumn('song_per_session',F.size(F.collect_set(F.col('song')).over(wf_session)))\
  .withColumn('ft_mean_song_per_session',F.mean('song_per_session').over(wf_user))\
```

Since we have already cleaned out data, no abnormalities detected during the feature engineering process.

After the feature creation, the data is reduced to user-level, and split into train-validation-test sets with probability of 60%, 20% and 20%.

## Implementation

Pysparkling is a machine learning package that is developed by H2O. It enables people to use very user-friendly API on Spark, without dealing with vecterization of categorical variables or any other Spark ML libary complications. It also has state-of-the-art algorithms like XGBoost or nerual networks. Since in this project we are dealing with medium size tabular data, so XGBoost would be our best choice.

eXtreme Gradient Boosting(XGBoost) is a start-of-the-art ensemble tree algorithms. It fits iterative trees that is based on the residual of the previous trees, and has some optimization algorithms that utilize the Hessian matrix(matrix of second order derivatives) of the loss function. Unlike other gradient boosting trees it has found a smart way to distributed compute the tree growing process. And it also has the ability to deal with missing value internally.

We first initialize the pysparkling h2ocontext, turn Spark dataframe into h2o frame, which takes no time because the internal of Spark dataframe and h2o frame is share the same memory. Instead of turning categorical variable into dummies and then vecterize, we just need to tell h2o frame to turn those columns into factor, and the 'dummize' will take place under the hood.

We will use the 'lossguide' growth policy and 'hist' tree method, in order to mimic the algorithm of another great gradient boosting package, `ligthgbm`, which is faster than XGBoost default algorithm, but has similar performance. 

After first, we will use 30 trees, fit on the training data and track the performance of validation data. We can check the learning curve of the model, the learning curve is defined as the change of metrics log loss by the number of trees increase, for both training and validation data:

![learning_curve_30](..\images\2019-06-20-Sparkify_Churn_Prediction\learning_curve_30.png)


## Refinement

We can clearly see from the above plot that the training loss is always decreasing, while the validation loss starts to increase after 15 trees. We can reduce the number of trees to 15 trees and check the result:
![learning_curve_15](..\images\2019-06-20-Sparkify_Churn_Prediction\learning_curve_15.png)

Now the learning curve for both training and validation sets are decreasing as the number of tree increase.

# Results

## Model Evaluation and Validation
The AUC from the training, validation and testing data are:

| Dataset | AUC   |
|-------|-------|
| Train | 0.94    |
| Validation |  0.89  |
| Test | 0.84 |

We can see that the model performance is pretty robust across different dataset, and the AUC is high enugh.

Then let's check the ROC curve: 
![roc_curve](..\images\2019-06-20-Sparkify_Churn_Prediction\roc_curve.png)


If we want to have a true positive rate of 0.7, then the false positive rate is only 0.2, which is good when we want to provide incentives to the users.

Another good feature of tree based model is, we can look at the variable importance plot. Variable importance is defined as number of splits each feature is splitted on in the model.
![varimp_plot](..\images\2019-06-20-Sparkify_Churn_Prediction\varimp_plot.png)

We can see that the top important feature is the tenure day, and all the top features are align with our exploratory analysis.

## Justification
We can see that the XGBoost does yield a great model for us. As stated in the previous sections, we have a medium size tabular data, which is the best fit for ensemble tree model. This has been proved in multiple Kaggle compititions. If we have larger data size, or we are going to treat the data as time sries data, then nerual networks can be tried. 
XGBoost also has a very good default parameter set, which generally yield pretty good result without hyperparameter tuning(i.e. I did not do hyperparameter tuning but get an AUC of .88), and is easy to do model explanation(variable importance plot, partial dependence plot).

# Conclusion

## Reflection
The work presented here shows how features can be engineered from event logs to create data to be used in machine learning models to predict customer churn. We defined customer churn as to whether a user visited the Cancellation Confirmation page. We used a sample dataset in a Databricks pyspark cluster that can be easily scaled to much larger datasets (big data). We trained a XGBoost model on the engineered features, which give us AUC of 0.84 in the testing data. The variable importance plot is inline with the exploratory analysis we did before implementing the model. Overall spearking, we have a pretty great model that can predict customer churn! Nice job!

![Nice job](https://sayingimages.com/wp-content/uploads/you-did-good-job-meme.jpg)

Thinking retrospectively, I really appreciate the h2o pysparkling package, which really make the machine learning process a lot easier on Spark. Not only it can deal with category variables, has state-of-the-art algorithms, it also has model evaluation methods like variable importance plot and partial denpendence plot built in. Really recommend you to check it out!

## Improvement
Discussion is made as to how at least one aspect of the implementation could be improved. Potential solutions resulting from these improvements are considered and compared/contrasted to the current solution.

We breifly talked about the treat the data as time series in the `justification` section. Right now we are aggregating all the features onto the user level, to see what is the proportion of each categories for the user. But thinking some users are impulsive, if they encounter some bad things in their last operations, they will go to the cancellation page to churn? In this case, we would only have one bad operation appear in the user aggregation, but we do not have the temporal order of the operations. So treating the data as time serise maybe a better choice, but this will certainly bring hardness to the feature engineering process and modeling process.

If we decide to treat the data as time series, recurrent neural networks might be a good modeling choice. It will take long to fine tune the model since the default hyperparameters are not as robust as XGBoost, and it would be harder to get the model explanation like variable importance. There is a new paper in end of 2017 introducing a very promising universal model explaner which is called SHAP. It can be applied to neural networks to get both variable importance plot and dependence plot. If you are interested, please check out following [link](https://github.com/slundberg/shap)!