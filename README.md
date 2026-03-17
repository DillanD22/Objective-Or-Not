# Recipe Ratings and Nutritional Content Analysis

Author: Dillan Desai

# Overview

This analysis was done using a dataset of over 83,000 recipe ratings as part of a project for the course: Data Science 80 at University of California San Diego. Recipes tagged as healthy by raters were compared to ratings without healthy tags, in order to better explore differences in their nutrional makeups and how they may influence user ratings. The main focus was on using the nutritional features (calories, sugar (PDV), protein (PDV), etc.) and structural features (minutes - recipe timing, n_steps - number of steps in recipes, n_ingredients - number of ingredients in recipe) try to predict the target variable,  average_ratings of recipes. 

# Introduction

This dataset was chosen out of my own curiousity for nutrition and food choices. I started intermittent fasting about 3 years ago, and ever since then I have enjoyed finding optimal recipes that are both tasty and good for me. However, it's not always the case that we find healthy recipes that also taste good. We often desire to eat food that tastes good, regardless of its health. For example, we all love a good burger or slice or pizza or ice cream cone from time to time, even though those are not the healthiest of options. Using this dataset, I wanted to explore the central question of: What types of recipes tend to have higher average ratings? Do recipes that are tagged by users as healthy receive a higher average rating than recipes not tagged as healthy? 

The dataset was created out of two smaller datasets: recipes and ratings. The recipes dataset contains 83,782 rows (unique recipes) and 12 columns (features). The ratings dataset contains 731,927 rows (unique ratings reviews) and 5 columns (features). These two datasets were then merged together to create a dataset called merged, which matched the unique recipes with their ratings and reviews. **Merged contains 234,429 rows (recipes) and 17 columns (combined features from both previous datasets)**. Here is a look at the merged dataset columns and their descriptions: 

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

I will be using the information in this dataset to explore the questions mentioned above. The most relevant columns for this exploration will be the `minutes`, `tags`, `name`, `nutrition`, `n_ingredients`, and `rating` columns. The `tags` column will allow me to explore the differences in ratings between the recipes with a healthy tag and the recipes without healthy tags. The 'nutrition' column and the structural columns (`minutes`, `n_steps`, `n_ingredients`) will allow me to explore the nutritional content of the two types of recipes and how I can use those features to try and predict the ratings of those recipes. 

# Data Cleaning and Exploratory Data Analysis

Data cleaning was a crucial part of making sure my dataset was ready for analysis and it took several steps.

1. The first step, as I mentioned above, was to merge the recipes dataset was left-merged with the ratings dataset on ID. This ensured that the merged dataset contained ratings and reviews for each unique recipe.
2. Next, I replaced all ratings of 0 with NaN because a rating of 0 actually represented no rating being given by the user, not an actual rating of 0. This ensures that no numeric value is given to ratings of 0 and instead that they are ignored.
3. I then found the average rating per recipe by grouping recipe IDs by their rating and taking the mean of those ratings. I added the average ratings as additional column in the merged dataset. This allowed me to create my primary dataset for analysis, recipes_avg_rating, by left-merging the recipes dataset with the average_ratings.
4. I converted the 'submitted' column to datetime value just in case analysis needed to be done with it later.
5. From there, I focused on converting the columns that contained multiple string elements to lists. I started by converting the nutrition column to a list, and then decided that it would be better off if I made each nutritional value its own separate column in the dataframe. After creating a smaller dataframe with each value, I connected it back to the recipes_avg_rating dataframe and dropped the nutrition column (since that was no longer needed). This big step allowed me to have `calories`, `protein (PDV)`, and the others as their own columns, allowing for deeper analysis with individual nutritional features.
6. Finally, I converted the other string columns (`tags`, `ingredients`, `steps`) with multiple elements into lists. This made analysis with those columns easier as it placed them back into their proper data type.

Here are the first 5 rows of the cleaned dataframe, which contains 83,782 rows and 20 columns (I only picked the most relevant columns for EDA to show here, since the dataframe would be very wide if I didn't): 

| name                                 |   minutes |   n_steps |   n_ingredients |   average_rating |   calories |
|:-------------------------------------|----------:|----------:|----------------:|-----------------:|-----------:|
| 1 brownies in the world    best ever |        40 |        10 |               9 |                4 |      138.4 |
| 1 in canada chocolate chip cookies   |        45 |        12 |              11 |                5 |      595.1 |
| 412 broccoli casserole               |        40 |         6 |               9 |                5 |      194.8 |
| millionaire pound cake               |       120 |         7 |               7 |                5 |      878.3 |
| 2000 meatloaf                        |        90 |        17 |              13 |                5 |      267   |


## Univariate Analysis

I wanted to explore the distribution of average_ratings within the dataframe to get a better sense of the range of values. As we can see above, the graph is left-skewed with a high concentration of values around the 4-5 average_rating range. There are very few values in the 1-3 range, which is somewhat surprising because it highlights a possible tendency for users to overrate recipes higher than they maybe should be.

<iframe
  src="assets/avg-rating-distribution.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>


## Bivariate Analysis

Additionally, I was interested in exploring the relationship between the number of ingredients vs the number of calories. Maybe certain recipes have only a small subset of ingredients that lead to a higher calorie total and maybe other healthy recipes use several ingredients that each have lower calorie counts. However, I assumed there would be a positive relationship between the two, as more ingredients typically tend to add up to more calories and indeed there was. The bright linear area in the graph highlights the positive correlation between the two features.

<iframe
  src="assets/n-ingredients-vs-calories.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

## Interesting Aggregates

Now one of my favorite tables is the grouped one below, as it highlights the differences in nutritonal makeups between recipes with the `is_healthy` tag and recipes without the tag. I like this one because it allows for direct comparison between the two groups in terms of nutrional content and shows some variety in the numbers. For example, the is_healthy tagged recipes were higher in `sugar (PDV)` content, which was surprising to me. 


| is_healthy   |   calories |   total_fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated_fat (PDV) |   carbohydrates (PDV) |
|:-------------|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|
| True         |    355.049 |           11.392  |       90.698  |        30.3171 |         26.5765 |               11.9766 |               18.8234 |
| False        |    444.603 |           36.7871 |       64.3458 |        28.6721 |         34.4185 |               45.7847 |               12.7996 |

This chart provides a visual of that previous table (removed calories from the visual because their scale would have made it harder to see the other features). The sugar difference is very clear in the chart, as well as differences in `total_fat (PDV)` and `saturated_fat (PDV)`.

<iframe
  src="assets/healthy-vs-non-healthy.html"
  width="500"
  height="400"
  frameborder="0"
></iframe>

# Assessment of Missingness

There were three columns in the recipes_avg_rating dataset that contained missing values: `name`, `description`, and `average_rating`.

## MNAR Analysis

I do believe that the `description` column in the dataset is MNAR (Missing Not At Random), which essentially means that the values that are missing in that column are missing because of themselves. It's possible that people who wanted to quickly get a recipe up on food.com without writing a description about it. Maybe they don't engage with the website or post recipes very often, so they may not have felt the need to write a description for their one-time recipe. This is an engagement or user activity feature that is not currently capture in the dataset. Obtaining additional data such as a column containing the number of times a user has posted a new recipe in the last year or so could be useful here to explain why a recipe lacks a description or seems to lack context, which would make its missingness MAR (Missing at Random - dependent on the data in other columns).

## Missingness Dependency

Furthermore, I wanted to check the missingness of the `average_rating` column with a column that may be able to explain its non-trivial missingness, the `minutes`. I chose to explore the dependence of the `average_rating` column on `minutes` because maybe longer recipes have been tried less by users, hence resulting in the fewer ratings and a lack of an average rating.

- Null hypothesis: The missingness of average_rating does not depend on minutes
 (Distribution of minutes is same for missing and not missing)
- Alternative hypothesis: The missingness of average_rating does depend on minutes
 (Distribution of minutes is different for missing and not missing)
- Significance level: alpha = 0.05 
- Test statistic: The absolute difference of mean in mean minutes of the group with  missing ratings and the group without missing ratings.

<iframe
  src="assets/minutes-vs-average-rating-missingness.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

I ran a permutation test with 1000 repetitions to arrive at a p-value of 0.029. The observed difference was 117.34 minutes, as you can see in the graph above. Since the p-value of 0.029 is less than 0.05, the null is rejected at the 0.05 significance level, showing that the missingness of the `average_rating` column is likely dependent on the `minutes` column.

The same type of permutation test with 1000 repetitions was also done with the `sodium (PDV)` column. I wanted to explore a column in which the `average_rating` likely did not depend on it, and I figured that one nutritional value is not a likely determinant of missingness of average ratings.

- Null hypothesis: The missingness of average_rating does not depend on sodium (PDV)
 (Distribution of sodium (PDV) is same for missing and not missing)
- Alternative hypothesis: The missingness of average_rating does depend on sodium (PDV)
 (Distribution of sodium (PDV) is different for missing and not missing)
- Significance level: alpha = 0.05
- Test statistic: The absolute difference of mean in mean sodium (PDV) of the group with  missing ratings and the group without missing ratings.

<iframe
  src="assets/sodium-vs-average-rating-missingness.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>

The p-value was 0.863, with an observed difference of 0.35 percent. Since the p-value of 0.863 is much greater than 0.05, I failed to reject the null hypothesis at the 0.05 significance level. This suggests that the missingness of the `average_rating` column is likely not dependent on the `sodium (PDV)` column.

# Hypothesis Testing

