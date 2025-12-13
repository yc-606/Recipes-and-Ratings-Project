# Analyzing Dietary Trends and Carbohydrate Content Within Recipes and Ratings Data
Name: Yagnasri Chilukuri

## Overview
This project was completed for the DSC80 (The Practice and Application of Data Science) class at UCSD during the Fall Quarter of 2025. The goal of this project was to explore the relationship between carbohydrate content and recipe ratings and investigate the dietary trends that occurred between the years of 2008 to 2018. This project analyzes two datasets consisting of recipes and their ratings posted on [food.com](food.com) between the years of 2008 to 2018. The original purpose of these datasets was for a personalized recipe generator proposed within "Generating Personalized Recipes from Historical User Preferences" by Majumder et al. The full datasets can be found here: [DATA](https://drive.google.com/drive/folders/1m_JXpgS2rR1-UYWAnffuUYiA50LvljF1)

## Introduction
As fashion and lifestyle trends continue to evolve in a cyclical manner, dietary trends also continue to do the same. The projected trends for the year 2026 seem to hinge largely on the nostalgia of the 2000's——from fashion to technological trends, it seems that many people are beginning to revert back to old ways of living, including in their eating habits. According to an article published by [ELLE](https://www.elle.com/uk/life-and-culture/culture/a65463685/diet-culture-back/) earlier this year, "diet culture" has begun to resurface online through dangerous content that promotes limited or extremely "healthy" consumption of food in order to obtain an ideally slim figure. As these trends begin to reemerge from their all-time popularity during the 2000's, it would be interesting to analyze the changes in nutritional content and dietary descriptors of recipes that were created during these years.

#### Information about the `recipes` dataset
Below are the first few rows of the `recipes` dataset which consists of **83782** rows and the following columns: `'name'` - the name of the recipe, `'id'` - the ID of the recipe, `'minutes'` - the number of minutes needed to prepare the recipe, `'contributor_id'` - the user ID of whoever submitted the recipe, `'submitted'` - the date when the recipe was submitted to the website, `'tags'` - "food.com" keywords that represent / describe the recipe, `'nutrition'` - numbers that represent the *total number of calories, total fat percentage (%DV), sugar percentage (%DV), sodium percentage (%DV), protein percentage (%DV), saturated fat percentage (%DV), and total carbohydrate percentage (%DV)* (in that order), `'n_steps'` - the number of steps in the recipe, `'steps'` - the descriptions of each step in order, `'description'` - a user-previded description of the recipe, `'ingredients'` - the ingredient names, and `'n_ingredients'` - the number of ingredients. Some of these columns are omitted from the below figure for readability.

| name                                 |     id |   minutes |   contributor_id | submitted   |   n_steps |   n_ingredients |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|----------:|----------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  |        10 |               9 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  |        12 |              11 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  |         6 |               9 |
| millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12  |         7 |               7 |
| 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06  |        17 |              13 |

The chart below describes each column within the `recipes` dataset once more.

| Column             | Description                                                                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`             | Recipe name                                                                                                                                                                                       |
| `id`               | Recipe ID                                                                                                                                                                                         |
| `minutes`          | Minutes to prepare recipe                                                                                                                                                                         |
| `contributor_id`   | User ID who submitted this recipe                                                                                                                                                                 |
| `submitted`        | Date recipe was submitted                                                                                                                                                                         |
| `tags`             | Food.com tags for recipe                                                                                                                                                                          |
| `nutrition`        | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `n_steps`          | Number of steps in recipe                                                                                                                                                                         |
| `steps`            | Text for recipe steps, in order                                                                                                                                                                   |
| `description`      | User-provided description                                                                                                                                                                         |
| `ingredients`      | Text for recipe ingredients                                                                                                                                                                       |
| `n_ingredients`    | Number of ingredients in recipe                                                                                                                                                                   |

#### Information about the `ratings` dataset
Below are the first few rows of the `ratings` dataset which consists of **731927** rows and the following columns: `'user_id'` - the user ID of whoever submitted the rating and / or review, `'recipe_id'` - the ID of the recipe, `'date'` - the date the rating and / or review was submitted, `'rating'` the rating on on a scale of 1-5, and `'review'` - the review text.

|    user_id |   recipe_id | date       |   rating |
|-----------:|------------:|:-----------|---------:|
|    1293707 |       40893 | 2011-12-21 |        5 |
|     126440 |       85009 | 2010-02-27 |        5 |
|      57222 |       85009 | 2011-10-01 |        5 |
|     124416 |      120345 | 2011-08-06 |        0 |
| 2000192946 |      120345 | 2015-05-10 |        2 |

The chart below describes each column within the `ratings` dataset once more.

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

#### Question Identification
The main question I am interested in answering about this dataset is if there exists a *preference for certain diets* based on the amount of recipes that were published with certain nutritional values during certain years. I am interested in this because I find fashion, dietary, and lifestyle trends very interesting as they are often cyclical. I want to know if there was a time that certain diets (such as low-carb diets) were most prevalent. A related problem would to see if carbohydrate-dense recipes are rated highly in comparison to recipes that consist of less carbohydrates. I am interested in answering this question because, at least in Western media, carbohydrates are often demonized and regarded as "unhealthy". People often opt to cut out nutrients such as carbohydrates and sugar. It may also be the case that "healthy" (carbohydrate- or sugar-lacking) meals taste worse than carbohydrate- or sugar-dense meals.

## Data Cleaning and Exploratory Data Analysis

#### Merging `recipes` and `ratings`
For the rest of my analysis, I will be using the `recipes_and_ratings` dataset, which is a merged dataset of `recipes` and `ratings`. First, I left merged the `recipes` and `ratings` datasets. Then, I filled all ratings of 0 with NaN values, since the corresponding recipes *do not have any ratings, not intentional ratings of 0*. Then, I added another column to the `recipes_and_ratings` dataset, `'average_rating'`, which is the average rating of each recipe.

The `recipes_and_ratings` dataset consists of all of the same columns that were mentioned earlier, plus one more column: `'average_rating'` - the average rating of the recipe.

| Column             | Description |
| :----------------- | :---------- |
| `'name'`           | object      |
| `'id'`             | int64       |
| `'minutes'`        | int64       |
| `'contributor_id'` | int64       |
| `'submitted'`      | object      |
| `'tags'`           | object      |
| `'nutrition'`      | object      |
| `'n_steps'`        | int64       |
| `'steps'`          | object      |
| `'description'`    | object      |
| `'ingredients'`    | object      |
| `'n_ingredients'`  | int64       |
| `'user_id'`        | float64     |
| `'recipe_id'`      | float64     |
| `'date'`           | object      |
| `'rating'`         | float64     |
| `'review'`         | object      |
