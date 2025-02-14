# [Recruit Restaurant Visitor Forecasting](https://www.kaggle.com/c/recruit-restaurant-visitor-forecasting)

## Problem Statement 
Running a thriving local restaurant isn't always as charming as first impressions appear. There are often all sorts of unexpected troubles popping up that could hurt business.

One common predicament is that restaurants need to know how many customers to expect each day to effectively purchase ingredients and schedule staff members. The daily number of customers affects all sorts of business decisions, including the quantity of perishable ingredients to purchase as well as how many staff members to schedule. If underestimated the number of customers, restaurants would run into shortages and customers would be unhappy. If overestimated, restaurant would have borne unnecessary costs. However, This forecast isn't easy to make because many unpredictable factors affect restaurant attendance, such as weather and local competition. It's even harder for newer restaurants with little historical data.

## Background
### How can Recruit Holdings help solve this challenege 
Recruit Holdings has access to several proprietary datasets that can be leveraged to make the process of customer count prediction easier. Specifically, Recruit Holdings owns 
- Hot Pepper Gourmet (a restaurant review service)
- AirREGI (a restaurant point of sales service)
- Restaurant Board (reservation log management software)

If successful, Recruit Holdings will be able to market that capability to not only retain existing customers/restaurants but also attract new ones. 

### Introduction of the dataset 

#### Brief overview
![image](https://user-images.githubusercontent.com/76879882/117075674-dcbb5c80-acfa-11eb-92c3-942ca82f8275.png)

#### Some feature exploration 
- Most of the restaurants in the dataset are located in metropolises in Southern Japan
- On average customers make reservations 7-8 days in advanced, and reservations are made typically during lunch and dinner time on non-holidays 
- Tables are reserved mostly for Friday and Saturday 
- Reservations only account for a small fraction of total visits, hence it poses a great challenge for prediction 
![image](https://user-images.githubusercontent.com/76879882/117076174-99adb900-acfb-11eb-8f99-3ea7e29021de.png)


## Overall Process 

### Data generation 
The goal is to predict the number of visitors of Japan’s restaurant starting from Apr 23rd to May 31st in 2017 (the testing data: a total of 39 days starting from a Sunday and ending in a Wednesday), and we have training data ranging from Jan 1st, 2016 to Apr 22sd, 2017. Hence, this is a time-series regression and the prediction label (Y) is 39 days with the start date as a Sunday and end date as Wednesday. Listed below is how the training data is generated. 
![image](https://user-images.githubusercontent.com/76879882/117077393-a206f380-acfd-11eb-9fc7-3afdf86f6748.png)
The latest Wednesday before 2017-04-23 is 2017-04-19, we generate label-range / validation data from 2017-03-12 to 2017-04-19, and feature-range/ training data from 2016-01-01 to 2017-03-11. We then generate more data following this pattern by backpropogate validaion data by one week and repeat the process for a consecutive 58 weeks. 

### Feature engineering 
A total of 407 features are generated and used. Most of the features are generated by taking their statistical aggregation such as min, max, mean, median, count, standard deviation, skew and kurtosis within different combinations such as store_id, day of week, is_not_holiday etc. To preserve the sequence in days, we also apply different weights in these statistical aggregations. For example, if the difference between prediction date and training date is small, a larger weight is applied and vice versa.

To capture more time-series information, features such as the ratio of statistical aggregation between the first week and second week are generated to preserve the changes in trend. 
#### Data size after feature engineering 
![image](https://user-images.githubusercontent.com/76879882/117078725-0f1b8880-ad00-11eb-9c41-63087fe9d09a.png)

### Model selection 
Lightgbm is selected over other models because it has great compatability with large datasets. Also, it has faster training speed and uses less memory by bucketing continuous features into discrete bins. In general, lightgbm has better accuracy than any other boosting algorithm because it produces much more complex trees by following leaf wise split approach rather than a level-wise approach adopted by other tree-based models. 

### Evaluation metric 
Root mean squared log error (RMSLE) is used as the evaluation metric. It penalizes underprediction more than overprediction. The reason for this is that in the case of underprediction, restaurants might not have enough ingredients or adequate staffs to serve customers; hence they will suffer from unhappy customers turning away. RMSLE is also more relevant to this situation given the fact Japan has a large number of small restaurants that will suffer more from underprediction. 

### Result 
Our model reaches a great performance and compared to the baseline model, our model lowered the error rate by 6%. On Kaggle's public leaderboard, we get 15th place out of 2148 teams, and get 2nd place on private leaderboard. This means our model is robust even if it transfers to another data without facing a big shake-up.
![image](https://user-images.githubusercontent.com/76879882/117080752-15136880-ad04-11eb-868a-ed008c9c821e.png)

## Future Work 
Recruit Holdings could build different models targeting different segments of the restaurant population. For instance, high-performing restaurants that see a large number of customers daily may be better represented by a different model versus restaurants that only see a few customers each day.

Also, Recruit Holdings can adjust visitor predictions from daily to hourly if hourly information could be obtained. By adjusting the granularity, Recruit Holdings can not only help restaurants better staff their employees, it also gives restaurant managers more control to prepare for the next influx of customers. 

## Business Values 
In conclusion, this model can help increase the profit for Recruit holdings’ clients by 2% and this is coming from better staffing and a better management of perishable ingredients. We believe a 2% profit increase is going to be a satisfying result given that most of the restaurants are operating on a thin margin around 3-5%. With that, not only this model can help keep existing customers satisfied but it would help Recruite Holdins attract new customers. 











