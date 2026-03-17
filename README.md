# Objective Or Not: What Influences Recipe Ratings?

Author: Dillan Desai

# Overview

This analysis was done using a dataset of over 83,000 recipe ratings as part of a project for the course: Data Science 80 at University of California San Diego. Recipes tagged as healthy by raters were compared to ratings without healthy tags, in order to better explore differences in their nutrional makeups and how they may influence ratings. The main focus was on using the nutritional features (calories, sugar (PDV), protein (PDV), etc.) and structural features (minutes, n_steps, and n_ingredients) to try to predict the target variable, average_ratings of recipes. 

# Introduction

This dataset was chosen out of my own curiousity for nutrition and food choices. I started intermittent fasting about 3 years ago, and ever since then I have enjoyed finding optimal recipes that are both tasty and good for me. However, it's not always the case that we find healthy recipes that also taste good. We often desire to eat food that tastes good, regardless of its health. For example, we all love a good burger or ice cream cone from time to time, even though those aren't the healthiest of options. Using this dataset, I wanted to explore a central question: What types of recipes tend to have higher average ratings? Furthermore, do recipes that are tagged by users as healthy receive a higher average rating than recipes not tagged as healthy? 

The dataset was created out of two smaller datasets: recipes and ratings. The recipes dataset contains 83,782 rows (unique recipes) and 12 columns (features). The ratings dataset contains 731,927 rows (unique rating reviews) and 5 columns (features). These two datasets were then merged together to create a dataset called merged, which matched the unique recipes with their ratings and reviews. **Merged contains 234,429 rows (recipes) and 17 columns (combined features from both previous datasets)**. Here is a look at the merged dataset columns and their descriptions: 

| Column | Description |
|--------|-------------|
| 'name' | Recipe name |
| 'id'   | Recipe ID   |
| 'minutes' | Minutes to prepare recipe |
| 'contributor_id' | User ID who submitted this recipe |
| 'submitted' | Date recipe was submitted |
| 'tags' | Food.com tags for recipe |
| 'nutrition' | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| 'n_steps' | Number of steps in recipe |
| 'steps' | Text for recipe steps, in order |
| 'description' | User-provided description |
| 'ingredients' | Ingredients used in recipe |
| 'n_ingredients' | Number of ingredients used in recipe |
| 'user_id' | User ID |
| 'recipe_id' | Recipe ID |
| 'date' | Date of interaction |
| 'rating' | Rating given |
| 'review' | Review text |

I will be using the information in this dataset to explore the questions mentioned above. The most relevant columns for this exploration will be the `minutes`, `tags`, `name`, `nutrition`, `n_ingredients`, and `rating` columns. The `tags` column will allow me to explore the differences in ratings between the recipes with a healthy tag and the recipes without healthy tags. The 'nutrition' column and the structural columns (`minutes`, `n_steps`, `n_ingredients`) will allow me to explore the nutritional content and parameters of the two types of recipes and how I can use those features to try and predict their ratings.

# Data Cleaning and Exploratory Data Analysis

Data cleaning was a crucial part in making sure my dataset was ready for analysis and it took several steps:

1. The first step, as I mentioned above, was to left-merge the recipes dataset with the ratings dataset on ID. This ensured that the merged dataset contained ratings and reviews for each unique recipe.
2. Next, I replaced all ratings of 0 with NaN because a rating of 0 actually represented no rating being given by the user, not an actual rating of 0. This ensures that no numeric value is given to ratings of 0.
3. I then found the average rating per recipe by grouping recipe IDs by their rating and taking the mean of those ratings. I added the average ratings as an additional column in the merged dataset. This allowed me to create my primary dataset for analysis, recipes_avg_rating, by left-merging the recipes dataset with the average_ratings series.
4. I converted the 'submitted' column to a datetime type just in case analysis needed to be done with it later.
5. From there, I focused on converting the columns that contained multiple string elements to lists. I started by converting the nutrition column to a list, and then decided that it would be better off if I made each nutritional value its own separate column in the dataframe. After creating a smaller dataframe with each value, I connected it back to the recipes_avg_rating dataframe and dropped the nutrition column (since that was no longer needed). This big step allowed me to have `calories`, `protein (PDV)`, and the others as their own columns, allowing for deeper analysis with the individual nutritional features.
6. Finally, I converted the other string columns (`tags`, `ingredients`, `steps`) with multiple elements into lists. This made analysis with those columns easier as it placed them back into their proper data type.

Here are the first 5 rows of the cleaned dataframe, recipes_avg_rating, which contains 83,782 rows and 20 columns (I only picked the most relevant columns for EDA to show here, since the dataframe would be very wide if I didn't): 

| name                                 |   minutes |   n_steps |   n_ingredients |   average_rating |   calories |
|:-------------------------------------|----------:|----------:|----------------:|-----------------:|-----------:|
| 1 brownies in the world    best ever |        40 |        10 |               9 |                4 |      138.4 |
| 1 in canada chocolate chip cookies   |        45 |        12 |              11 |                5 |      595.1 |
| 412 broccoli casserole               |        40 |         6 |               9 |                5 |      194.8 |
| millionaire pound cake               |       120 |         7 |               7 |                5 |      878.3 |
| 2000 meatloaf                        |        90 |        17 |              13 |                5 |      267   |


## Univariate Analysis

I wanted to explore the distribution of average_ratings within the dataframe to get a better sense of the range of values. As we can see below, the graph is left-skewed with a high concentration of values around the 4-5 average_rating range. There are very few values in the 1-3 range, which is somewhat surprising because it hints at a possible tendency for users to overrate recipes higher than they maybe should be.

<iframe
  src="assets/avg-rating-distribution.html"
  width="700"
  height="425"
  frameborder="0"
></iframe>


## Bivariate Analysis

Additionally, I was interested in exploring the relationship between the number of ingredients vs. the number of calories. Maybe certain recipes have only a small subset of ingredients that lead to a higher calorie total and maybe other healthy recipes use several ingredients that each have lower calorie counts. However, I assumed there would be a positive relationship between the two, as more ingredients typically tend to add up to more calories and indeed there was. The bright linear blob area in the graph highlights the positive correlation between the two features.

<iframe
  src="assets/n-ingredients-vs-ncalories.html"
  width="800"
  height="800"
  frameborder="0"
></iframe>

## Interesting Aggregates

One of my favorite tables from this analysis is the grouped one below, as it highlights the differences in nutritonal makeups between recipes with the `is_healthy` tag and recipes without the tag. I appreciate it because it allows for direct comparison between the two groups in terms of nutrional content and shows some variety in the numbers. For example, the `is_healthy` tagged recipes were higher in `sugar (PDV)` content, which was surprising to me. 


| is_healthy   |   calories |   total_fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated_fat (PDV) |   carbohydrates (PDV) |
|:-------------|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|
| True         |    355.049 |           11.392  |       90.698  |        30.3171 |         26.5765 |               11.9766 |               18.8234 |
| False        |    444.603 |           36.7871 |       64.3458 |        28.6721 |         34.4185 |               45.7847 |               12.7996 |

This chart provides a visual of that previous table (I removed calories from the visual because their scale would have made it harder to see the other features). The sugar difference is very clear in the chart, as well as differences in `total_fat (PDV)` and `saturated_fat (PDV)`.

<iframe
  src="assets/healthy-vs-non-healthy.html"
  width="650"
  height="425"
  frameborder="0"
></iframe>

# Assessment of Missingness

There were three columns in the recipes_avg_rating dataset that contained missing values: `name`, `description`, and `average_rating`.

## MNAR Analysis

I do believe that the `description` column in the dataset is MNAR (Missing Not At Random), which essentially means that the values that are missing in that column are missing because of themselves. It's possible people who wanted to quickly get a recipe up on food.com did so without writing a description about it. Maybe they don't engage with the website much or post recipes very often, so they may not have felt the need to write a description for their one-time recipe. This is an engagement or user activity feature that is not currently captured in the dataset. Obtaining additional data such as a column containing the number of times a user has posted a new recipe in the last year or so could be useful here to explain why a recipe lacks a description or seems to lack context, which would make its missingness MAR (Missing at Random - dependent on the data in other columns).

## Missingness Dependency

Furthermore, I wanted to check the missingness of the `average_rating` column with a column that may be able to explain its non-trivial missingness, `minutes`. I chose to explore the dependence of the `average_rating` column on `minutes` because maybe longer recipes have been tried less by users, hence resulting in the fewer ratings.

- **Null hypothesis**: The missingness of average_rating does not depend on minutes
 (distribution of minutes is same for missing and not missing).
- **Alternative hypothesis**: The missingness of average_rating does depend on minutes
 (distribution of minutes is different for missing and not missing).
- **Significance level**: alpha = 0.05 
- **Test statistic**: The absolute difference in mean minutes of the group with missing ratings and the group without missing ratings.

<iframe
  src="assets/minutes-vs-average-rating-missingness.html"
  width="650"
  height="425"
  frameborder="0"
></iframe>

I ran a permutation test with 1000 repetitions to arrive at a p-value of **0.03**. The observed difference was about 117.34 minutes, as you can see in the graph above. Since the p-value of **0.03** is less than 0.05, the null is rejected at the 0.05 significance level, showing that the missingness of the `average_rating` column is likely dependent on the `minutes` column.

The same type of permutation test with 1000 repetitions was also done with the `sodium (PDV)` column. I wanted to explore a column in which the `average_rating` likely did not depend on it, and I figured that one nutritional value is not a likely determinant of the missingness of average ratings.

- **Null hypothesis**: The missingness of average_rating does not depend on sodium (PDV)
 (distribution of sodium (PDV) is same for missing and not missing).
- **Alternative hypothesis**: The missingness of average_rating does depend on sodium (PDV)
 (distribution of sodium (PDV) is different for missing and not missing).
- **Significance level**: alpha = 0.05
- **Test statistic**: The absolute difference in mean sodium (PDV) of the group with  missing ratings and the group without missing ratings.

<iframe
  src="assets/sodium-vs-average-rating-missingness.html"
  width="650"
  height="425"
  frameborder="0"
></iframe>

The p-value was **0.884**, with an observed difference of about 0.35 percent. Since the p-value of **0.884 is much greater than 0.05**, I failed to reject the null hypothesis at the 0.05 significance level. This suggests that the missingness of the `average_rating` column is likely not dependent on the `sodium (PDV)` column.

# Hypothesis Testing

Back to exploring the differences in the healthy-tagged group and the group without healthy tags. I conducted a permutation test to discover if recipes with the healthy tag have the same average rating as recipes without the healthy tag. Essentially, do people rate recipes as higher because they are healthier or does that not really matter?

- **Null Hypothesis**: Recipes with the healthy tag have the same average rating as recipes without the healthy tag.
- **Alternate Hypothesis**: Recipes with the healthy tag have a different average rating than recipes without the healthy tag.
- **Test Statistic**: Absolute difference in mean average ratings between the healthy and non-healthy tagged recipe groups.
- **Significance level**: alpha = 0.05

I am conducting a permutation test because we have two samples and no known population in which to compare them to. As part of the test, group labels were shuffled (healthy tags vs. no healthy tags). Also, I am choosing a two-tailed test here by using the absolute difference as the test statistic, because I have no strong prior reason to suspect that average ratings differ between the two groups. In addition, my EDA showed that the average rating distributions were pretty similar across the two groups.

Similar to my earlier missingness tests, the permutation test was done with 1000 repetitions and the observed statistic came out to about 0.04 pts, with a p-value of **0.0**.

<iframe
  src="assets/difference-mean-average-rating-between-tags.html"
  width="650"
  height="425"
  frameborder="0"
></iframe>

## Conclusion

Since the p-value of **0.0** is less than 0.05, the null hypothesis was rejected at a significance level of 0.05. This suggests that recipes with healthy tage do have a different average rating than recipes without the healthy tag. However, I must note something important to consider here. The actual observed difference of 0.04 is very small in reality when looking at our recipe rating scale of 1-5. This difference is likely due to the large sample of close to 84,000 recipes, in which even a small difference can become statistically significant in permutation tests. In summary, despite the statistically significant result, this does not emphasize a large real-world difference in average_rating between recipes with the healthy tag and recipes without it.

# Framing a Prediction Problem

I chose to try and predict the `average_rating` of a recipe using **regression**, as it is a continous variable, ranging from 1-5. I am choosing average rating because I am curious to see if it can be predicted using the objective nutritional and structural features that exist in the dataset. Being able to predict this is also intriguing to me because it would show me that people may be able to evaluate the number and type of ingredients they plan on using, as well as how long they plan on their recipe taking, to try and predict if it will have a high or low average rating on food.com (i.e. if it would be evaluated/received well by others).

The metric I am using to evaluate my model is **R^2**, which measures how much of the variance in `average_rating` can be explained by the model's predictions. Since this is regression, I am also incorporating RMSE (root mean squared error) as a secondary metric to evaluate the average prediction error of my model in terms of rating scale points. 

At the "time of prediction" all the nutritional and structural features I will be using are available in the recipes_avg_rating dataframe and would be available to anyone because they inherent in the identity of the recipe. For example, the calories of a recipe are clearly available at the time of prediction because they are baked in to the recipe.

# Baseline Model

For my baseline model, I will be using a multiple linear regression model with three nutritional features: `calories`, `sugar (PDV)`, `protein (PDV)`. Each of the three features are quantitative, continous variables. `calories` is a column that contains the # of calories for each recipe. `sugar (PDV)` and `protein (PDV)` are columns that contain the precentage of daily value for sugar and protein in each recipe. Since these features are on two different scales, a StandardScaler was applied in the model pipeline to ensure that each feature was on the same scale before running the model.

The model ended up with an R^2 of about **-0.000095** and RMSE of about **0.6359**, which shows that the model is very weak and not that useful. This does kind of track, because it makes sense that just using three nutritional feature alone doesn't do much at all to predict a recipe's average rating. This does also show that users don't usually consider calories, sugar, or protein very much when evaluating a recipe.

# Final Model

For my final model, I used all of the nutritional features and added structural features: `calories`, `sugar (PDV)`, `protein (PDV)`, `total_fat (PDV)`, `sodium (PDV)`, `saturated_fat (PDV)`, `carbohydrates (PDV)`, `minutes`, `n_steps`, `n_ingredients`, and `is_healthy`. 

`calories`, `sugar (PDV)`, `protein (PDV)`, `total_fat (PDV)`, `sodium (PDV)`, `saturated_fat (PDV)`, `carbohydrates (PDV)`

- I chose to incorporate all of the nutritional features into my final model because I figured that the average rating of a recipe is partly reflective of how it tastes, suggesting that the nutrional makeup of a recipe is influential in crafting its taste. 
- For example, maybe recipes with more sodium are received better by users because they taste saltier, hence having a higher average rating. Users may also value healthy recipes that combine low sodium with high protein because they fit their diet. 
- Adding more of the nutritional features may improve the model's performance because they explain more of the complete taste of each recipe. `calories` was log-transformed using FunctionTransformer to account for the right-skew seen in the EDA plot. In addition, each of the nutritional features besides `calories` and plus `n_ingredients` were standardized using StandardScaler because n_ingredients is on a different scale from the nutritional (PDV) features.

`minutes`, `n_steps`, `n_ingredients`

- I chose to add three structural features to my final model, because, in addition to taste, users may value recipes that don't take as long or that have fewer ingredients and steps. These may improve my model's performance because if users do value efficient recipes, than they may rate them higher, allowing for these structural features to be more predictive of a recipe's average rating. `minutes` was log-transformed using FunctionTransformer to account for the right-skew seen in the EDA plot. `n_steps` was transformed using QuantileTransfomer to account for its skew and the fact that it is a discrete variable. 

`is_healthy`

- This was one of the aspects of this dataset that I explored earlier, and I chose to include it here because there was a statistically significant difference in average ratings between healthy tagged recipes and recipes without the tag, albeit small. Maybe the type of recipe influences how users rate it, possibly rating a recipe as higher because it is healthier or lower because healthy recipes don't taste as good. I converted this binary feature to an int (0/1) encoding, which will allow for my final model to differentiate between the two types of recipes. 

I used **Ridge regression** as a more powerful upgrade to multiple linear regression for my final model. I chose it because it improves upon the baseline model by better accounting for multicollinearity and reducing overfitting. It does this by using a hyperparameter **alpha**, which controls the regularization strength in the model, essentially working to find the right balance between trying to fit the training while not overfitting. 

Alpha is the most important hyperparameter to tune and it is the one I chose to tune using GridSearch CV, which found that the optimal alpha to choose was **100** out of 5 options: 0.01, 0.1, 1, 10. The final model resulted in an R^2 of about **0.001** and an RMSE of about **0.6354**, which is a marked improvement in R^2 from the baseline model. That being said, the R^2 value is still not significant and the RMSE basically didn't change, highlighting the reality that many of these objective features do not truly capture the factors that influence and predict `average_rating`. 

This is a surprising and interesting result to me, because this shows that people likely rate recipes more subjectively than I had initially believed. For example, I believed that taste, a subjective feeling, was captured by some of the nutritional features, but it likely was not. Maybe this also further suggests that people don't rate recipes based on how healthy they think they will be or how nutritious they are. Maybe they instead rate them based on how easy they are to make or how presentable they are. The healthiness of a recipe is not as predictive of its average rating, and that is noteworthy. It does also make sense because we don't often view healthy recipes as super tasty, and when we are looking for good recipes, we often want something delicious, but that may come at the cost of being healthy (such as desserts for example). 

**In summary, the average rating of a recipe was more challenging to predict due to the subjective factors that influence it, factors that are not effectively captured by the objective features within the dataset. Users likely value taste, ease of completion, presentation, and others factors more highly than whether a recipe hits certain nutritional benchmark or appears to be healthy.**

# Fairness Analysis

As part of my fairness analysis, I wanted to finish up my comparison between the group with the `is_healthy` tag and the group without. Since my model is a regression model, I will be using **RMSE** (root mean squared error) as my evaluation metric.

- **Null hypothesis**: The model is fair. Its RMSE for healthy-tagged recipes and recipes without the healthy tag are roughly the same and any differences are due to random chance.
- **Alternate Hypothesis**: The model is unfair. Its RMSE for healthy-tagged recipes and recipes without the healthy tag are different.
- **Test Statistic**: Absolute difference in RMSE between the healthy and non-healthy tagged recipe groups.
- **Significance level**: alpha = 0.05

<iframe
  src="assets/difference-rmse-between-tags.html"
  width="650"
  height="425"
  frameborder="0"
></iframe>

Another permutation test was run to conduct this analysis, using 1000 repetitions and resulting in an observed RMSE difference of about **0.0268** and a p-value of **0.203**. Since the p-value of 0.203 is greater than 0.05, I fail to reject the null hypothesis at the 0.05 significance level. This suggests that the model performs fairly in terms of RMSE for both recipes with the healthy tag and recipes without it. A result like this makes sense given the fact that the model essentially predicts close to the mean for all recipes. 

This is both good and not as good, because it means the model doesn't discriminate, but also reflects the difficulty the model has with differentiating between the two types of recipes. In general, this is a symptom of trying to predict `average_rating` from the features availabe in the dataset, something that is difficult given the many subjective factors that play into recipe ratings.