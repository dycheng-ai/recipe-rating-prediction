# recipe-rating-prediction

## Introduction
Have you ever wondered am I spending too much time making my recipe right now? Or maybe how can you make the best recipe possible? Whether it was the type of ingredients, the time taken to cook your recipe, the nutritional facts, what **really** determines whether or not the food is good? This project seeks to understand which factors are best correlated to the "rating" of a recipe, on a scale of 1-5, in order to determine where your priorities should lie when making a recipe.

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
  width="900"
  height="600"
  frameborder="0"
></iframe>
This plot details the distribution of minutes taken for each recipe. As the graph indicates, the bounds of the distribution are [0, 500] due to a vast majority of the data laying within these boudnaries.

<iframe
  src="assets/time-n-rating.html"
  width="900"
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

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis