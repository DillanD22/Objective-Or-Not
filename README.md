# Recipe Ratings and Nutritional Content Analysis

Author: Dillan Desai

# Overview

This analysis was done using a dataset of over 83,000 recipe ratings as part of a project for the course: Data Science 80 at University of California San Diego. Recipes tagged as healthy by raters were compared to ratings without healthy tags, in order to better explore differences in their nutrional makeups and how they may influence user ratings. The main focus was on using the nutritional features (calories, sugar (PDV), protein (PDV), etc.) and structural features (minutes - recipe timing, n_steps - number of steps in recipes, n_ingredients - number of ingredients in recipe) try to predict the target variable,  average_ratings of recipes. 

# Introduction

Provide an introduction to your dataset, and clearly state the one question your project is centered around. Why should readers of your website care about the dataset and your question specifically? Report the number of rows in the dataset, the names of the columns that are relevant to your question, and descriptions of those relevant columns.


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

I will be using the information in this dataset to explore the questions mentioned above. The most relevant columns for this exploration will be the 'minutes', 'tags', 'nutrition', 'n_steps', 'n_ingredients', and 'rating' columns. The 'tags' column will allow me to explore the differences in ratings between the recipes with a healthy tag and the recipes without healthy tags. The 'nutrition' column and the structural columns ('minutes', 'n_steps', 'n_ingredients') will allow me to explore the nutritional content of the two types of recipes and how I can use those features to try and predict the ratings of those recipes. 