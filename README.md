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
For the rest of my analysis, I will be using the `recipes_and_ratings` dataset, which is a merged dataset of `recipes` and `ratings`. First, I left merged the `recipes` and `ratings` datasets. Then, I filled all ratings of 0 with NaN values, since the corresponding recipes *do not have any ratings, not intentional ratings of 0*. Finally, I added another column to the `recipes_and_ratings` dataset, `'average_rating'`, which is the average rating of each recipe.

I then made sure that the datatypes of certain columns in `recipes_and_ratings` were the datatypes I wanted before moving forward.

Within recipes_and_ratings, all of the date-related columns needed to be TimeStamp objects, so I converted the `'submitted'` and `'date'` columns to TimeStamp objects. The `'submitted'` column proved to be useful throughout this project because I was able to easily extract the year component from the transformed column.

I also converted the `'tags'` and `'ingredients'` columns to actual lists of strings. Previously, the "lists" were actually strings themselves. Similarly, I converted the `'nutrition'` column to actual lists of floats. The `'nutrition'` column was one of the most useful columns within the dataset after it was transformed such that I could access the percent daily value (PDV) information of each nutrient. All of these resulting columns are shown below.

The final `recipes_and_ratings` dataset consists of all of the same columns that were mentioned earlier, plus one more column: `'average_rating'` - the average rating of the recipe.

Below are all of the columns in the new dataset, `recipes_and_ratings` and their datatype descriptions.

| Column                  | Description    |
| :---------------------- | :------------- |
| `'name'`                | object         |
| `'id'`                  | int64          |
| `'minutes'`             | int64          |
| `'contributor_id'`      | int64          |
| `'submitted'`           | datetime64[ns] |
| `'tags'`                | object         |
| `'nutrition'`           | object         |
| `'n_steps'`             | int64          |
| `'steps'`               | object         |
| `'description'`         | object         |
| `'ingredients'`         | object         |
| `'n_ingredients'`       | int64          |
| `'user_id'`             | float64        |
| `'recipe_id'`           | float64        |
| `'date'`                | datetime64[ns] |
| `'rating'`              | float64        |
| `'review'`              | object         |
| `'average_rating'`      | object         |
| `'n_cals'`              | float64        |
| `'pdv_fat'`             | float64        |
| `'pdv_sugar'`           | float64        |
| `'pdv_sodium'`          | float64        |
| `'pdv_protein'`         | float64        |
| `'pdv_sat_fat'`         | float64        |
| `'pdv_carb'`            | float64        |

Below are the first few unique rows of the new `recipes_and_ratings` dataset. Some of the columns are ommitted for the sake of readability. This dataset ended up with 234429 rows and 27 columns.

| name                                 |     id |   minutes |   contributor_id | submitted           |   n_steps |   n_ingredients |          user_id |   recipe_id | date                |   rating |   average_rating |   n_cals |   pdv_fat |   pdv_sugar |   pdv_sodium |   pdv_protein |   pdv_sat_fat |   pdv_carb |   pdv_carb_capped |
|:-------------------------------------|-------:|----------:|-----------------:|:--------------------|----------:|----------------:|-----------------:|------------:|:--------------------|---------:|-----------------:|---------:|----------:|------------:|-------------:|--------------:|--------------:|-----------:|------------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27 00:00:00 |        10 |               9 | 386585           |      333281 | 2008-11-19 00:00:00 |        4 |                4 |    138.4 |        10 |          50 |            3 |             3 |            19 |          6 |                 6 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11 00:00:00 |        12 |              11 | 424680           |      453467 | 2012-01-26 00:00:00 |        5 |                5 |    595.1 |        46 |         211 |           22 |            13 |            51 |         26 |                26 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30 00:00:00 |         6 |               9 |  29782           |      306168 | 2008-12-31 00:00:00 |        5 |                5 |    194.8 |        20 |           6 |           32 |            22 |            36 |          3 |                 3 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30 00:00:00 |         6 |               9 |      1.19628e+06 |      306168 | 2009-04-13 00:00:00 |        5 |                5 |    194.8 |        20 |           6 |           32 |            22 |            36 |          3 |                 3 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30 00:00:00 |         6 |               9 | 768828           |      306168 | 2013-08-02 00:00:00 |        5 |                5 |    194.8 |        20 |           6 |           32 |            22 |            36 |          3 |                 3 |

#### Univariate analysis
To start exploratory data analysis on this dataset, I started by analyzing the `'pdv_carb'` column to see what range of percent daily value of carbohydrates were the most common. Displayed below, this graph follows a right-skew trend, illustrating how there tends to be less recipes with a higher PDV of carbohydrates. Visually, we can see that the mean PDV of carbohydrates is around 10-15%.

In this plot, I chose to cap the `'pdv_carb'` column because there were some outliers that had extremely high `'pdv_carb'` values. For simplicity, I grouped all of the values that were above the 99th percentile of the `'pdv_carb'` column into one group (that is positioned around 200 visually on the graph).

<iframe
  src="assets/pdv_carb_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Another feature that was interesting to graph was the `'n_ingredients'` column. Recipes with less ingredients may be considered healthier, as recipes with more simplicity might be less for enjoyment and more for dietary reasons, which is something I could have used later on within this project. The graph of the distribution of recipes (count) given the number of ingredients needed for each recipe is shown below. We can see that the distribution is slightly skewed to the right, but a general bell-curve trend is followed. The mean number of ingredients is around 9.

<iframe
  src="assets/n_ing_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#### Bivariate analysis
In regard to bivariate analysis, I wanted to illustrate the relationship between rating and number of ingredients, which I do below. I first calculated the average rating of recipes with *n* number of ingredients by grouping by `'n_ingredients'`, then graphed the relationship between the average rating and `'n_ingredients'`. This visualization is interesting because there seems to be a potentially quadratic relationship between average rating of recipes with *n* number of ingredients. Higher average ratings seem to come from recipes with more ingredients, which may be a result of these recipes tasting better due to the inclusion of more diverse ingredients.

<iframe
  src="assets/rating_vs_n_ing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Finally, I wanted to see if there existed a relationship between recipes that had certain tags and their ratings. To do this, I added an additional column to the `recipes_and_ratings` dataset, titled `'has_dietary_tags'`. The values of this column were boolean values of either True or False and indicated whether or not the respective recipe had a tag within its `'tags'` list that I was interested in. After analyzing all of the unique tags that appeared within the dataset and their relevance to dietary choices (such as low-carb diets), I chose the following tags to be representative of a True value in the `'has_dietary_tags'` column: 'dietary', 'low-sodium', 'low-in-something', 'diabetic', 'free-of-something', 'low-protein', 'very-low-carbs', 'low-cholesterol', 'low-saturated-fat', 'low-calorie', 'low-carb', 'healthy-2', 'healthy', 'low-fat', and 'high-in-something-diabetic-friendly'. This means that if a recipe had at least one of the aforementioned tags within its tag list, it had a True value within the `'has_dietary_tags'` column. By observing the graph below, it seems that there exists a decrease in recipes with at least on dietary tag over time (the opposing relationship is also true). The bars representing the percentage of recipes per year that have at least one dietary tag (pink) are above 60% until 2012, where this percentage decreases significantly moving forward. Similarly, the bars representing the percentage of recipes per year that do not have any dietary tags (teal) are below 40% until 2012, where this percentage increases significantly moving forward.

<iframe
  src="assets/year_and_tag.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#### Interesting aggregates
Below, I create a pivot table to show how the number of calories per recipe differ between years separately for recipes that have and do not have dietary tags. Within this pivot table, I observed that the mean number of calories for recipes that do not have dietary tags were greater than the number of calories for recipes that have dietary tags up until 2016. From 2016 onward, the opposite relationship is true. I found this to be interesting because we know from earlier that the usage of dietary tags had increased as time went on, but according to this pivot table, the more common usage of dietary tags in recent years does not mean that recent recipes have a lower calorie count on average.

|   year |   False |    True |
|-------:|--------:|--------:|
|   2008 | 439.206 | 402.916 |
|   2009 | 442.588 | 389.943 |
|   2010 | 441.878 | 390.224 |
|   2011 | 467.016 | 414.848 |
|   2012 | 465.909 | 406.742 |
|   2013 | 501.196 | 422.233 |
|   2014 | 458.903 | 433.178 |
|   2015 | 607.392 | 392.998 |
|   2016 | 490.408 | 655.034 |
|   2017 | 605.058 | 717.538 |
|   2018 | 828.096 | 995.568 |

I would also like to see if recipe complexity (based on the number of steps or the number of ingredients) affects nutritional content. If there are a higher number of ingredients or steps, there may be a chance for higher PDV of carbohydrates, sugar, or fat. Below, I plot the average nutritional content of recipes depending on the number of ingredients. In this plot, I excluded extreme outliers that were present within the `'pdv_carb'`, `'pdv_sugar'`, and `'pdv_fat'` columns as they made the original plot hard to interpret.

<iframe
  src="assets/nutr_and_ing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Assessment of Missingness
After checking each column, the results were that the `'name'`, `'description'`, `'user_id'`, `'recipe_id'`, `'date'`, `'rating'`, `'review'`, and `'average_rating'` columns had NaN values. Using this information, I then decided which columns needed to be cleaned. 

The `'description'` column had NaN values because it was optional for recipe authors to include a description of their recipe. The `'user_id'`, `'recipe_id'`, and `'date'` columns can have NaN values because a recipe can have 0 reviews or ratings, meaning all of the columns from the previous `rating` dataset will appear as NaN for these rows. The `'rating'` column has NaN values because I replaced all ratings of 0 with np.nan. I also know that `'review'` can be NaN because a person can rate a recipe and not provide a review. Finally, the `'average_rating'` column can have NaN values because a recipe has no ratings.

Out of these columns, I wanted to investigate the missingness of the `'name'`, `'description'`, `'rating'`, and `'review'` columns.

For the `'name'` column, I found that after copy-and-pasting the recipe's ID (stored within the `'id'` column) into food.com, the recipe was actually a salad dressing recipe with the title "-------------". Because these characters were stripped when the data was scraped, the name of the recipe was listed as a NaN value. Although the `'name'` column is not important, I filled this NaN value with a simple name, 'salad dressing'. I concluded that the reason why there was a singular NaN value within the `'name'` column was due to the original name itself. This means that the missingness of the `'name'` column is NMAR (Not Missing At Random), because the name itself (multiple dashes) caused itself to appear as NaN within the final cleaned dataset. This is because the developers of this dataset chose to omit unecessary characters such as dashes, resulting in this title appearing as a NaN value due to it consisting of only dashes.

Regarding the `'review'` column, it is most likely that the missingness is NMAR (Not Missing At Random) as well, meaning the missingness depends on the review itself. This may be the case because a person might only review a recipe if it is either extremely bad or extremely good by their standards. If a recipe tastes like an ordinary meal to the user, they might be less likely to write a review because they do not have anything impactful to state about the recipe.

Next, we can test the dependency of the `'rating'` column on another column, which may demonstrate a relationship between the missingness of `'rating'` and the values of another column. This would indicate a missingness classification of MAR (Missing At Random) in regard to the `'rating'` column. Below, I test if the `'rating'` column is dependent on the PDV of carbs (`'pdv_carb'`).

##### Null Hypothesis:
The missingness of the `'rating'` column does not depend on the `'pdv_carb'` column; the distribution of `'pdv_carb'` values is the same for recipes with missing ratings and recipes with non-missing ratings.
##### Alternative Hypothesis:
The missingness of the `'rating'` column does depend on the `'pdv_carb'` column; the distribution of `'pdv_carb'` values is not the same for recipes with missing ratings and recipes with non-missing ratings.
##### Test Statistic:
The absolute difference between the means of the PDV of carbohydrates for missing vs. non-missing ratings.

<iframe
  src="assets/first_missingness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

In the above graph, I chose not to graph the observed test statistic, as it is extremely far to the right of the graph at an x-value of approximately 2.5. Due to this fact, our p-value came out to be 0.0. Selecting a standard alpha value of 0.05, our p-value, 0.0, is less than this. This means that we may reject the null hypothesis. This means that the missingness of the `'rating'` column did depend on the `'pdv_carb'` column; the '`rating'` column is MAR (Missing At Random) dependent on the `'pdv_carb'` column.

Once again, we can test the dependency of the `'rating'` column on another column. This time, I chose to test the dependency of the `'rating'` column on the `'minutes'` column. Below, I follow the same steps as I did in the previous permutation test to test whether or not the missingness of the `'rating'` column is dependent on the `'minutes'` column.

##### Null Hypothesis:
The missingness of the `'rating'` column does not depend on the `'minutes'` column; the distribution of `'minutes'` values is the same for recipes with missing ratings and recipes with non-missing ratings.
##### Alternative Hypothesis:
The missingness of the `'rating'` column does depend on the `'minutes'` column; the distribution of `'minutes'` values is not the same for recipes with missing ratings and recipes with non-missing ratings.
##### Test Statistic:
The absolute difference between the means of the mean number of minutes for missing vs. non-missing ratings.

<iframe
  src="assets/second_missingness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

In the graph above, I plotted the observed test statistic, which is approximately 51. Using an alpha value of 0.05, we fail to reject the null hypothesis as our p-value of 0.13 is greater than 0.05. This permutation test indicates that the missingness of the `'rating'` column is not dependent on the `'minutes'` column.

## Hypothesis Testing
For this section, I was interested in answering the following question: Is the average `'pdv_carb'` value for recipes that have dietary tags different from the `'pdv_carb'` value for recipes that do not have dietary tags?

The pair of hypotheses I was interested in testing within this section are the following:

##### Null Hypothesis:
The distribution of `'pdv_carb'` values is the same for recipes that have dietary tags and recipes that do not have dietary tags.
##### Alternative Hypothesis:
The distribution of `'pdv_carb'` values is not the same for recipes that have dietary tags and recipes that do not have dietary tags.
##### Test Statistic:
The absolute difference between the means of the PDV of carbohydrates for recipes with dietary tags and recipes without dietary tags.

Below, I run a permutation test where I shuffle the `'has_dietary_tags'` column (which consists of Trues and Falses, as we saw earlier). Using an alpha value of 0.05, we fail to reject the null hypothesis. The average `'pdv_carb'` value of recipes that have a dietary tag and recipes that do not have a dietary tag seem to come from the same distribution, meaning that there is most likely no relationship between the PDV of carbohydrates of a recipe and whether or not a recipe has a dietary related tag.

<iframe
  src="assets/hypothesis_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Framing a Prediction Problem
In the following section, I would like to predict the average *rounded* rating of a recipe given its PDV of carbohydrates and whether or not it has a dietary-related tag. I chose `'rating'` to be my response variable because I am interested in investigating whether or not "health" indicators such as tags and carbohydrate content truly impacts the average rating of a recipe.

This is a multi-class classification problem because the response variable `'rating'` has five possible values: 1, 2, 3, 4, or 5. The evaluation metric I will be using is F1-score because the number of recipes that are rated highly is much greater than the number of recipes that are not (see below). Using F1-score balances precision and recall; we do not want to use accuracy because the majority of the recipes within the `recipes_and_ratings` dataset are rated highly.

The issue with using accuracy can be explained in the following example: if we have a dataset with 80% of its recipes rated very highly (5) , 10% of its recipes rated highly (4), and 10% of its recipes rated low (1, 2, or 3), we can technically build a classifier that always predicts that a recipe will be rated 5 stars. Using just accuracy, this means that our model will be right 80% of the time, masking the true failure of our model.

## Baseline Model
The two features that I will be using to train my model are `'pdv_carb'` and `'has_dietary_tags'`. `'pdv_carb'` is a quantitative column that contains the percent daily value of carbohydrates for a recipe, so it is ratio data. I used a QuantileTransformer() on this feature (and other features that will be addressed in the next section) because, as seen earlier during the univariate analysis section, there exist large outliers and a skew in the distribution of `'pdv_carb'` values in the `recipes_and_ratings` dataset. Additionally, I will need to one-hot-encode the `'has_dietary_tags'` column since it is a categorical feature (nominal data) with two possible values: True and False. This feature was engineered from the `'tags'` column, which was done in a previous step. I also drop one of the one-hot-encoded columns, as seen below. We must drop one of the columns that results from one-hot-encoding to prevent multicollinearity or redundancy within our features.

The stratify parameter is set to y to prevent the test set from ending up with too few low-rated recipes. Stratifying ensures that all classes are represented proportionally.

The F1-Score of the baseline model came out to be approximately 0.63. However, the F1-score for each individual rating was as follows: {1: 0.00, 2: 0.00, 3: 0.01, 4: 0.01, 5: 0.85}. Additionally, there were many warnings that were presented when I ran the code for my model. While looking at the aforementioned results, I can deduced that the baseline model was not very effective in predicting rating. My baseline model never predicted low-rated recipes at all (hence the many warnings), only predicting a rating of 3, 4 or 5. The F1-score for a rating of 5 is high because this class dominates the dataset (there is a large number of recipes that are rated 5 stars on average). This might have occurred because this model has very limited features. In the next step, I add many more features that will improve the F1-score of my model.

## Final Model
For my final model, I chose to add the features `'year'`, `'n_ingredients'`, `'pdv_sugar'`, and `'pdv_fat'`. I chose to add `'year'` as a feature because I noticed that `'year'` demonstrated a relationship with the proportion of recipes that were tagged with a dietary-relevant tag during my exploratory data analysis section. `'year'` is treated as a categorical variable, as there are only 10 years represented within this dataset. The year represents discrete time periods rather than some continuous quantity. By one-hot-encoding the `'year'` column, my model will be able to "learn" effects that depend on individual years.

I chose to add `'n_ingredients'` as a feature because during my exploratory data analysis, I noticed a clear trend between the number of ingredients and average recipe rating. This trend seemed to be somewhat quadratic, indicating a potential relationship between average recipe rating and the number of ingredients.

Finally, I added `'pdv_sugar'` and `'pdv_fat'` for the same reason I wanted to use `'pdv_fat'` to predict rating: I wanted to see if the average rating of recipes did infact influence average rating for a recipe, since these nutrients are often considered to be harmful in large quantities yet very addicting.

I used a RandomForestClassifier for the model and used GridSearchCV to tune the following hyperparameters: max_depth and n_estimators. It is crucial to tune max_depth because decision trees are very likely to overfit to the data it is trained on if its max_depth is not limited. On the other hand, if max_depth is too constrained, the decision tree is likely to underfit to the data it is trained on. This is why it is important to tune this hyperparameter to guarantee the best generalizability of our model. n_estimators essentially represents how many trees you "ask" for a prediction. We do not want n_estimators to be too small because the model might rely on a small amount of bad predictions, which is why we want to tune this hyperparameter to a value that lets the model average out mistakes, thus making more reliable predictions.

The F1-score of my final model was approximately 0.92, with the F1-scores for each individual rating as follows: {1: 0.32, 2: 0.61, 3: 0.63, 4: 0.85, 5: 0.95}. As we can see from the weighted F1-score and the individual F1-scores of each rating, this model predicts rating much better than the baseline model. The F1-score of the final model is approximately 0.92, which is a 0.29 increase from the F1-Score of the baseline model. Additionally, we can see that the F1-score of a rating of 1, 2, and 3 are no longer 0. The baseline model had individual F1-scores of approximately 0.0, 0.0, 0.1, 0.1, and 0.85, while the improved model has individual F1-scores of approximately 0.32, 0.61, 0.63, 0.85, and 0.95. Although we still see the trend where the model is much worse as correctly identifying low-rated recipes, the increases in individual F1-scores are still significant, demonstrating the improvement between my baseline model and final model. The best combination of hyperparameters was: max_depth = None and n_estimators = 200.

## Fairness Analysis
For this section, I was curious to see if my final model would perform worse when predicting the average rating of recipes that do not have any dietary tags in comparison to recipes that do have at least one dietary tag.

##### Group X:
Recipes with at least one dietary-related tag (the value within the `'has_dietary_tags'` column will be True).
##### Group Y:
Recipes without any dietary-related tags (the value within the `'has_dietary_tags'` column will be False).
##### Null Hypothesis:
The model performs equally well for recipes in Group X and recipes in Group Y.
##### Alternative Hypothesis:
The model performs worse for either Group X or Group Y.
##### Test Statistic:
The signed difference between the F1-score for Group X and the F1-score for Group Y (a negative sign indicates that the model performed worse for recipes with a dietary tag while a positive sign indicates that the model performed worse for recipes without a dietary tag).

<iframe
  src="assets/fairness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

By looking at the distribution, we can see that our observed test statistic was not one that would appear within the distribution. Using an alpha value of 0.05 and our p-value of approximately 0.0, we get that 0.0 < 0.05, meaning that our final model is not fair in its performance across Group X and Group Y. In other words, the F1-score is significantly different between the two groups, meaning that the model performs significantly better for Group X than for Group Y. This is because our observed test statistic is positive, suggesting that Group X's F1-score was greater than Group Y's F1-score, which is not due to chance as demonstrated by the graph above.
