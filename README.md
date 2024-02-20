# Predicting MLB Home Runs for the 2024 Season

## Overview of Methodology:
1) Obtain data
2) Split data into training and testing sets
3) Perform exploratory data analysis
4) Create preprocessing pipeline
5) Model selection
6) Hyperparameter optimization
7) Feature importance
8) Repeat Steps 3-7
9) Analyze Test Set
10) Obtain Predictions
    
## Methodology:
The first step to building this model is obtaining the data. I downloaded batting data from MLB
Baseball Savant in the Statcast Era (2015-Present). Only qualified batters (2.1 PA per team game)
were included in the data set to remove outliers with low plate appearances. The final downloaded
CSV file contained 17 columns with an emphasis on Statcast Stats. It contained 1112 rows. Below
are the features in the final dataset:
- `last_name, first_name`
- `player_id`
- `year`
- `home_run`
- `xba`
- `xslg`
- `xwoba`
- `xobp`
- `xiso`
- `xwobacon`
- `xbacon`
- `exit_velocity_avg`
- `launch_angle_avg`
- `sweet_spot_percent`
- `barrel_batted_rate`
- `solidcontact_percent`
- `hard_hit_percent`
  
A combination of intuition and feature importance was used to determine which of these features
will be used in the model. I initially used 36 features that included both basic and Statcast statistics,
however, after repeating steps 3-7 many times, the RFE feature selection performed removed many
of these metrics, so I opted to use a smaller number of features in the initial dataset to optimize the
performance of the model.

After the data set was finalized, data wrangling was performed to add the next years home runs to
each of the players. The data was split into 80-20 training and testing sets. In addition, due to the
shortened 2020 season, the 2019 data with 2020 home runs and the 2020 data with 2021 home
runs were removed due to the uniqueness of the season. So, the final training set had 417 rows and
the testing set had 105 rows.

Using the training set, feature selection was performed using RFECV. Since this was not the first
iteration of the process, all of the features were selected and were used to train the model:
- `home_run`
- `xba`
- `xslg`
- `xwoba`
- `xobp`
- `xiso`
- `xwobacon`
- `xbacon`
- `exit_velocity_avg`
- `launch_angle_avg`
- `sweet_spot_percent`
- `barrel_batted_rate`
- `solidcontact_percent`
- `hard_hit_percent`


Next, the model selection was performed using mean absolute error as the scoring metric because
it handles outliers better than RMSE. In addition, it is easy to interpret as it can be interpreted as
the average number of home runs the model is off by.
I used a large variety of linear models and ensembles. The models used are as followed:
- Dummy Regressor
- KNN Regressor
- Linear Regression (OLS)
- Ridge Regression
- Bayesian Ridge Regression
- LASSO Regression
- Decision Tree
- Random Forest
- XGBoost
- LGBM
- CatBoost
  
After fitting the models on the training set and using cross-validation, linear models performed the
best. This makes sense because after feature selection was conducted, all of the features in the
training set were important. Since all of the features are good indicators for home runs hit, the
linear regression model was able to outperform more complex ensemble and boosting methods
such as random forest and XGBoost.

Linear regression was chosen as the best model because it has just slightly higher than the lowest
mean absolute error of Ridge (-6.158 vs -6.129) so it was selected due to its better interpretability.
Since linear regression uses no hyperparameters step 6 for this final model was skipped. Next, the
linear regression model was used to predict the values on the test set. The mean absolute error on
the test set was 5.6 which is better than the training error, indicating that overfitting is likely not an
area of concern.

Finally, the 2023 season data was used to obtain the predictions for the 2024 season. Below is a
preview of the leaderboard for the top 10.

Name | HR Prediction
---|---
Olson, Matt |43
Schwarber, Kyle |39
Ohtani, Shohei |38
Alonso, Pete |37
Ozuna, Marcell |36
Muncy, Max |36
Acuna Jr., Ronald |35
Garcia, Adolis |35
Betts, Mookie |34
Soler, Jorge |34


## Results and Potential Next Steps:
Overall, the model performed relatively well and had an error of about 5-7 home runs in both the
training and test sets. However, there are definitely some areas of improvement and next steps that
may increase performance of the model.

By comparing the results with the 2023 home runs, the distribution of home runs aligns with
historical averages. However, for the top few batters, the model appears to be underpredicting. This
is somewhat expected for a linear regression model since in general, linear regression models
perform worse with outliers. However, other projection models such as Zeile projections for home
runs appear to have a similar issue. A potential next step to try and improve the model would be to
try and address this issue. A combination of classification and regression is a potential idea.
Classifying batters into tiers and then fitting regression models based on those tiers may lead to
better results for the top hitters. In addition, the tradeoff between the leaders of the leaderboard
and the overall accuracy of the model needs to be discussed with stakeholders should this model
be developed further.

Another potential area for improvement for the model is found by analyzing the coefficients of the
model. Some of the features that would be expected to have a positive effect on the number of
home runs in the next year such as xSLG and xBACON have negative coefficients in the fitted
model. This issue may be due to multicollinearity among the predictors. Since all of these metrics
are measuring how good a hitter is, they are all highly correlated to one another. The linear
assumptions of the model may need to be analyzed further to improve the performance of this
model.