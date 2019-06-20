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
![Pandora](https://asset.barrons.com/public/resources/images/ON-CN207_Pandor_B620_20180503171700.jpg)

*Photo via washingtonpost.com*

So it is very useful to predict which user are at risk to churn, either downgrading from premium to free tier or cancelling their service altogether. If you can accuartely identify these users before they leave, your business can offer them discounts and incentives, potentially save your business millions in revenue.

To tackle this project, I use a large dataset that contains the events described above. I will load, expolre and clean the dataset with Spark. After explorationg, I will create features and build machine learning models with Spark to predict which user were churn from the digital music service.

## Metrics
Metrics used to measure performance of a model or result are clearly defined. Metrics are justified based on the characteristics of the problem.

Since churn is a categorical variable 

# Analysis

## Data Exploration
Features and calculated statistics relevant to the problem have been reported and discussed related to the dataset, and a thorough description of the input space or input data has been made. Abnormalities or characteristics about the data or input that need to be addressed have been identified.

## Data Visualization
Build data visualizations to further convey the information associated with your data exploration journey. Ensure that visualizations are appropriate for the data values you are plotting.

# Methodology

## Data Preprocessing
All preprocessing steps have been clearly documented. Abnormalities or characteristics about the data or input that needed to be addressed have been corrected. If no data preprocessing is necessary, it has been clearly justified.

## Implementation
	
The process for which metrics, algorithms, and techniques were implemented with the given datasets or input data has been thoroughly documented. Complications that occurred during the coding process are discussed.

## Refinement
	
The process of improving upon the algorithms and techniques used is clearly documented. Both the initial and final solutions are reported, along with intermediate solutions, if necessary.

# Results

## Model Evaluation and Validation
If a model is used, the following should hold: The final model’s qualities — such as parameters — are evaluated in detail. Some type of analysis is used to validate the robustness of the model’s solution.

Alternatively a student may choose to answer questions with data visualizations or other means that don't involve machine learning if a different approach best helps them address their question(s) of interest.

## Justification
The final results are discussed in detail.
Exploration as to why some techniques worked better than others, or how improvements were made are documented.

# Conclusion

## Reflection
Student adequately summarizes the end-to-end problem solution and discusses one or two particular aspects of the project they found interesting or difficult.

## Improvement
Discussion is made as to how at least one aspect of the implementation could be improved. Potential solutions resulting from these improvements are considered and compared/contrasted to the current solution.