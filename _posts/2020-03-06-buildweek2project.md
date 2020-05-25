---
layout: post
title: 🍕🍕 Predicting Tip Income
image: /img/domino.jpg
---
While working as a delivery driver for many years, I kept recored of how much I made in tips. 
I decided to use that information to create a model that predicts daily tips. 🤑  
Check out my deployed application>>>[Here](https://dominos-tip-prediction.herokuapp.com/)<<<

**Model Features**  
When I worked, I kept track of the date, day of the week, how many miles I drove, reimbursement I received for mileage, 
my daily tips, and the total hours I spent on the road. Most of this I categorized as data leakage and had to throw away. 
However, I was able to extract weather data to get the daily precipitation. I also engineered a new feature to capture 
the business demand by using the average miles per hour I drve to determine if a day was normal, slow, or busy. That left 
me with seven features I could use in my model.  
``` target = 'Tips'  
features = ['Day_of_the_week', 'Year', 'Month', 'Day', 'Hours', 'Prep', 'Demand']```

**Models**  
As a baseline, I wanted to see how accurate my predictions would be if I selected the mean tip value of $66.34 for every 
prediction. With that model, my mean absolute error was $25.85. That gave me a good starting point and a benchmack to compare 
other models to. I also trained a linear regression and random forest model. After testing them on validation data, the linear 
regression seemed to perform better with a MAE of $13.47.

**Insights**  
I used the eli5 library to compute the permutation importances for my model features. I was pleased the the "Demand" feature 
I create was very useful in predicting tips. The precipitation was not as important as I though in determining the value of tips.

<img src="/img/importances.PNG" height="400" width="1600" />  
<img src="/img/demandprcp.png" height="400" width="1600" />  
