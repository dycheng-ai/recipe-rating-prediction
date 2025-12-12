# recipe-rating-prediction

## Introduction
Have you ever wondered am I spending too much time making my recipe right now? Whether it was the type of ingredients, the time taken to cook your recipe, the nutritional facts, what **really** determines whether or not the food is good? This project seeks to understand which factors are best correlated to the "rating" of a recipe, on a scale of 1-5, in order to determine where your priorities should lie when making a recipe.

Thus, this project is centered on utlimately creating a model to predict the rating of a recipe. The dataset we're working with is a merged dataset that has 234,429 rows and 17 columns. Of the 17 columns, we'll be focusing on these 4 and their impact on prediciting the rating of your recipe: **minutes, n_steps, n_ingredients, review_length**. **Minutes** details the number of minutes taken to cook the recipe, **n_steps** details the number of steps in the recipe, **n_ingredients** details the number of ingredients in the recipe, and **review_length** details the length of the review regarding the recipe.  

## Data Cleaning and Exploratory Data Analysis
### I took the following steps to clean the dataset: 
1. Left-merged the recipes DataFrame with the interactions (reviews) Dataframe. 
2. Dropped all NaN values.
3. Created a new column for average rating for each recipe. 
4. Updated all values of rating = 0 in the rating column to NaN.
5. Fixed data types in the nutrition column to ensure they were all lists. 
6. Created new columns regarding the data in the nutrition column: calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates.
7. Dropped the nutrition column. 
8. Queried the data to only include rows where the minutes value was non-zero. 
9. Queried the data to only include rows where the minutes value was less than or equal to 500 minutes.

|    |     id |   minutes |   n_steps |   n_ingredients |
|---:|-------:|----------:|----------:|----------------:|
|  0 | 333281 |        40 |        10 |               9 |
|  1 | 453467 |        45 |        12 |              11 |
|  2 | 306168 |        40 |         6 |               9 |
|  3 | 306168 |        40 |         6 |               9 |
|  4 | 306168 |        40 |         6 |               9 |

<iframe
  src="assets/time-plot.html"
  width="600"
  height="600"
  frameborder="0"
></iframe>
This plot details the distribution of minutes taken for each recipe. As the graph indicates, the bounds of the distribution are [0, 500] due to a vast majority of the data laying within these boudnaries.

<iframe
  src="assets/time-n-rating.html"
  width="600"
  height="600"
  frameborder="0"
></iframe>
This plot visualizes the correlation between the minutes taken for each recipe and their associated rating. There is also  trendline that shows a slight negative correlation between the two variables.

|   rating |   minutes |
|---------:|----------:|
|        1 |   70.7926 |
|        2 |   67.3216 |
|        3 |   63.0586 |
|        4 |   57.1737 |
|        5 |   54.7138 |

The grouped table showcases how as the rating increases, the number of minutes taken for the recipe decreases.

## Assessment of Missingness
The statistic I looked at tested whether recipes with missing ratings differ from recipes with non-missing ratings in terms of the number of minutes the recipe took (**minutes**) and how long the recipe's reviewes were (**review_length**).

The missinginess of the **rating** column is **NMAR** which is shown through the permutation testing done with the new column **review_length**. Through permutation testing, the code outputted an observered difference of -56.9 with a p-value of 0.0, we reject the null indicating that the missingness of **ratings** is related to **review_length** column. 

In contrast, through permutation testing for the **minutes** column, the p-value was 0.114 indicating that the observed difference of 51.45 was within the normaly range, indicating that the missingness of **ratings** is not related to the **minutes** column. 

## Hypothesis Testing
**Null Hypothesis:** There is no relationship between a recipe's cooking time and its average rating.
**Alternative Hypothesis:** There is a relationship between a recipe's cooking time and its average rating.

The test statistic we utilized was the correlation between the average recipe rating and the minutes taken for the recipe with a significance level of 0.05. By testing this null & alternative hypothesis, we are able to determine whether or not there is a relationship or strong correlation between the minutes taken for each recipe and the recipe's rating. By understanding this, we're able to pivot and determine either what factors do influence a recipe's rating or if the null is rejected, then we can utilize the number of minutes taken for each recipe as a central component of the ratings prediction model. 

The resulting p-value was 0.272 indicating we fail to reject the null hypothesis, meaning that there is no relationship bewteen a recipe's cooking time and its average rating.

## Framing a Prediction Problem
**Prediction Problem:** Predict ratings of recipes.

Originally, I was attempting to utilize a **Linear Regression** model to **predict the rating of a recipe** but the models performed poorly as there was a weak correlation between any of the columns and the recipe's rating. Eventually, I chose to use a **Decision Tree Regressor** to better fit the data as it captures non-linear relationships (although the predictive model still isn't very good, it is better than when the Linear Regression model was used). I am using the R^2 score to evaulate my model in order to understand how much of the variancein my target variable my model captures.

At the time of prediction, we would expect to know the minutes taken for the recipe, the number of steps, the number of ingredients, as well as the length of the review in order to use our predictive model.

## Baseline Model
The baseline model utilizes a **Linear Regression** object with **2 quantitative features: minutes and number of steps**. I utilized a the train_test_split method with a 0.8/0.2 split in order to train and test my data. I create a Pipeline object with the data preprocessing simply passing the original data through and then fit the Linear Regression object on the train data. Then we score the data using R^2. The results show that the model is bad because the R^2 values are extremely close to 0 indicating that the model is not explaining much of the variance in my target variable.

## Final Model
Like previously mentioned, due to the poor fit of the Linear Regression object, I sought to utilize a **Decision Tree Regressor** object instead. In addition, I introduced new features including the number of ingredients, the review's length, and the number of calories. I then tested all the feature combinations (pairs of 2) with varying hyperparameters and a **GridSearchCV** object to perform **k-fold cross-validation** in order to deduce the best hyperparameters. 

The best feature combination included the columns **minutes & review_length** with the best max_depth being 5, best min_samples_split being 5000 with a test score of 0.0040. Although this score is still extremely poor, it is a drastic improvement compared to our Linear Regression object which held a score of 0.01*10^-6. 

## Fairness Analysis
For this fairness analysis, I examined whether my Decision Tree Regressor model performs differently for recipes based on review length. I defined **Group X as recipes with short reviews** (review length ≤ median) and **Group Y as recipes with long reviews (review length > median)**. I chose **RMSE (Root Mean Squared Error) as my evaluation metric** because it measures the average prediction error in actual rating units, allowing me to assess whether the model makes larger errors for one group than the other. My **null hypothesis is that the model is fair and its RMSE for both groups are roughly the same, with any differences due to random chance**. My **alternative hypothesis is that the model is unfair and performs significantly differently between the two groups**. I used the **test statistic RMSE_long - RMSE_short** to measure the difference in prediction error between groups. With a significance level of α = 0.05, I conducted a permutation test with 10,000 permutations by randomly shuffling the review_length labels and recalculating the test statistic. Based on my results with p-value = 0.0002, I reject the null hypothesis and conclude the model is UNFAIR as it performs significantly differently for recipes with short versus long reviews, suggesting the model has a fairness concern based on review length. 