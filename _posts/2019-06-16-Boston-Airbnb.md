---
layout: post
title: Boston AirBNB Data Exploration
---

![Boston Skyline](https://cdn10.bostonmagazine.com/wp-content/uploads/sites/2/2017/11/boston-skyline.jpg)

*Photo via iStock.com/lisegagne*

As a Bostonian for almost 2 years, I have lived in Boston at several places. While wandering around different neighborhoods of this fabulous city, I am always wondering, **what would it be like to live in this neighborhood?** 

And as summer is coming, which is the best season of the city, I have noticed that the traffic has become heavier than usual when I get to work, and even is heavier than the same time last year. This also makes me wondering, **how busy is the summer time in Boston?** **By how much do hotel or AirBNB prices spike during this season?** And **is there a general upward trend of both new Airbnb listings and total Airbnb visitors to Boston?**

With these questions in mind, I found the [Boston AirBNB Data](https://www.kaggle.com/airbnb/boston) on Kaggle. Let's explore the data and find out the answer to my questions!

## The vibe of each Boston neighborhood
Maybe many of you are not from Boston area and do not have a clear view of which part of Boston is what, below is a map of Boston with all the neighborhood and districts of Boston:
![boston district map](https://maps-boston.com/img/0/boston-districts-map.jpg)
*Photo via maps-boston.com*

When we group by the neighborhood of each listing  in the AirBNB data, here is a bar chart showing the frequency of listing in each neighborhood:
![freq_listing_neighborhood](../images/freq_listing_neighborhood.png)

We can see that the majority of the AirBNB housings are in the south-western side of Boston, which makes total sense since the north-eastern side is all harbor, port and airport area. We will take the top ten neighborhood to do further analysis.

