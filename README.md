# Investigation of Protein to Calorie Ratio in Recipes 
Author: Saahithi Myneedu
## Overview
This data project was done at UC San Diego and intends to explore how protein efficiency varies across recipes by observing the protein-to-calorie ratio. In addition to observing the ratio, this project will also develop a predictive model to estimate a recipe's protein-to-calorie ratio according to its features.

## Introduction
To guide my project, I used the Recipes and Ratings dataset, which contains thousands of recipes with data on nutritional composition, ratings, and metadata such as preparation time. 

I was especially interested in exploring this dataset because of the shift in dietary trends the 2020’s have brought. While we have seen the fat-free diets of the 2000s and the keto diets of the 2010s, recent diet trends have been more consciously prioritizing high-protein diets for health, fitness, and satiation.

However, not all high-protein foods have the same efficiency. For example, a recipe promising 30g of protein with 1000 calories is necessarily efficient despite boasting a high protein density. Therefore, we can determine the efficiency of the protein in a recipe by interpreting the protein-to-calorie ratio; this may vary across recipe types. This essentially led me to the question: 

**Which types of recipes offer the best protein-to-calorie ratio?**

By answering this question, we can identify categories of food that provide the most optimal protein source relative to the amount of calories. The insights will be able to help individuals find options that are more nutritionally efficient. 

The  dataset, `recipe`, contains 83782 rows, each representing a distinct recipe, with 10 columns recording the following information:

| Column             | Description                                                                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `'name'`           | Recipe name                                                                                                                                                                                       |
| `'id'`             | Recipe ID                                                                                                                                                                                         |
| `'minutes'`        | Minutes to prepare recipe                                                                                                                                                                         |
| `'contributor_id'` | User ID who submitted this recipe                                                                                                                                                                 |
| `'submitted'`      | Date recipe was submitted                                                                                                                                                                         |
| `'tags'`           | Food.com tags for recipe                                                                                                                                                                          |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe                                                                                                                                                                  |
| `'steps'`          | Text for recipe steps, in order                                                                                                                                                                   |
| `'description'`    | User-provided description                                                                                                                                                                         |
| `'ingredients'`    | Text for recipe ingredients                                                                                                                                                                       |
| `'n_ingredients'`  | Number of ingredients in recipe                                                                                                                                                                   |

The second dataset, `interactions`, contains 731927 rows. Each row is a unique review from a user with columns describing:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

In order to effectively find this relationship for my guiding question, I chose to especially focus on the following columns: 'nutrition' and recipe 'tags' to see the impact of these variables across various recipes. Later on, to create a prediction model, I focus on columns: 'nutrition' and 'reviews' more. 

## Data Cleaning and Exploratory Data Analysis
Describe, in detail, the data cleaning steps you took and how they affected your analyses. The steps should be explained in reference to the data generating process. Show the head of your cleaned DataFrame (see Part 2: Report for instructions).
|    | name                                 |     id |   minutes |   contributor_id | submitted           | tags                                                                                                                                                                                                                                                                                               | nutrition                                     |   n_steps | ingredients                                                                                                                                                                                                                             |   n_ingredients |          user_id |   recipe_id | date                |   rating |   avg_rating | category   |   calories (#) |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) |   protein_grams |   protein_calories |   protein_to_cal_ratio | is_meat   |
|---:|:-------------------------------------|-------:|----------:|-----------------:|:--------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------|----------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|-----------------:|------------:|:--------------------|---------:|-------------:|:-----------|---------------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|----------------:|-------------------:|-----------------------:|:----------|
|  0 | 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings']                                                                        | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour']                                                          |               9 | 386585           |      333281 | 2008-11-19 00:00:00 |        4 |            4 | dessert    |          138.4 |                10 |            50 |              3 |               3 |                    19 |                     6 |             1.5 |                  6 |              0.0433526 | False     |
|  1 | 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                                                                                                      | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                                                                             |              11 | 424680           |      453467 | 2012-01-26 00:00:00 |        5 |            5 | other      |          595.1 |                46 |           211 |             22 |              13 |                    51 |                    26 |             6.5 |                 26 |              0.0436901 | False     |
|  2 | 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                                                                                               | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']                                                                   |               9 |  29782           |      306168 | 2008-12-31 00:00:00 |        5 |            5 | vegetable  |          194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 |            11   |                 44 |              0.225873  | True      |
|  3 | millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12 00:00:00 | ['time-to-make', 'course', 'cuisine', 'preparation', 'occasion', 'north-american', 'desserts', 'american', 'southern-united-states', 'dinner-party', 'holiday-event', 'cakes', 'dietary', 'christmas', 'thanksgiving', 'low-sodium', 'low-in-something', 'taste-mood', 'sweet', '4-hours-or-less'] | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 | ['butter', 'sugar', 'eggs', 'all-purpose flour', 'whole milk', 'pure vanilla extract', 'almond extract']                                                                                                                                |               7 | 813055           |      286009 | 2008-04-09 00:00:00 |        5 |            5 | dessert    |          878.3 |                63 |           326 |             13 |              20 |                   123 |                    39 |            10   |                 40 |              0.0455425 | False     |
|  4 | 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06 00:00:00 | ['time-to-make', 'course', 'main-ingredient', 'preparation', 'main-dish', 'potatoes', 'vegetables', '4-hours-or-less', 'meatloaf', 'simply-potatoes2']                                                                                                                                             | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 | ['meatloaf mixture', 'unsmoked bacon', 'goat cheese', 'unsalted butter', 'eggs', 'baby spinach', 'yellow onion', 'red bell pepper', 'simply potatoes shredded hash browns', 'fresh garlic', 'kosher salt', 'white pepper', 'olive oil'] |              13 |      2.20436e+06 |      475785 | 2012-03-07 00:00:00 |        5 |            5 | meat       |          267   |                30 |            12 |             12 |              29 |                    48 |                     2 |            14.5 |                 58 |              0.217228  | True      |
### Univariate Analysis 
<iframe src="assets/Calories per Recipe.html" width="800" height="600" frameborder="0"
></iframe> 
This is a plot of the distribution of calories between 0 and 2000 (to avoid extreme outliers). The majority of the recipes within the common range of 0-2000 calories primarily exist in the 0-500 calories range, which is important to note, as even the small amount of protein in recipes in this range can result in a high protein-to-calorie ratio. 

### Bivariate Analysis
<iframe src="assets/Meat vs. Non-Meat.html" width="800" height="600" frameborder="0"
></iframe> 

The **median** of the meat category is higher than the non-meat category, indicating meat has a more efficient protein-to-calorie ratio.
The meat-based category appears to be more **spread** out, with a taller box likely because of the multiple protein contents various sorts of meats provide. 
Although both categories include a number of **outliers**, the non-meat category has a very dense set of outliers, suggesting there are some non-meat recipes providing extremely protein-dense options. 
### Grouping and Aggregates 
| category   |       False |       True |
|:-----------|------------:|-----------:|
| breakfast  |   0.134938  | nan        |
| dessert    |   0.0577054 |   0.140059 |
| meat       | nan         |   0.266056 |
| other      |   0.119007  |   0.285941 |
| pasta      |   0.143259  | nan        |
| salad      |   0.0951108 | nan        |
| seafood    |   0.289317  |   0.320133 |
| soup       |   0.200785  |   0.30888  |
| vegetable  |   0.123033  |   0.300254 |

This is a pivot table showing the average protein density across various categories, further split by whether the recipe has meat. Where meat is true, it consistently shows higher protein content across all the categories. Seafood is the next category, following meat, that has the highest protein density.

## Assessment of Missingness
### MNAR Analysis
State whether you believe there is a column in your dataset that is MNAR. Explain your reasoning and any additional data you might want to obtain that could explain the missingness (thereby making it MAR). Make sure to explicitly use the term “MNAR.”
### Missingness Dependency
Present and interpret the results of your missingness permutation tests with respect to your data and question. Embed a plotly plot related to your missingness exploration. Ideas for this plot include: • The distribution of column Y when column X is missing and the distribution of column Y when column X is not missing, as was done in Lecture 8. • The empirical distribution of the test statistic used in one of your permutation tests, along with the observed statistic.
## Hypothesis Testing
Clearly state your null and alternative hypotheses, your choice of test statistic and significance level, the resulting p-value, and your conclusion. Justify why these choices are good choices for answering the question you are trying to answer.
## Framing a Prediction Problem
Clearly state your prediction problem and type (classification or regression). If you are building a classifier, make sure to state whether you are performing binary classification or multiclass classification. Report the response variable (i.e. the variable you are predicting) and why you chose it, the metric you are using to evaluate your model and why you chose it over other suitable metrics (e.g. accuracy vs. F1-score).
## Baseline Model
Describe your model and state the features incorporated in your model, specifying whether each is quantitative, ordinal, or nominal. Describe any encodings performed. Report the performance of your model and whether or not you believe your current model is “good” and why. : Both now and in Step 7: Final Model, make sure to evaluate your model not only on the training data, but on the model’s ability to generalize to unseen data!
## Final Model
State the features you added and why they are good for the data and prediction task. Note that you can’t simply state “these features improved my accuracy”, since you’d need to choose these features and fit a model before noticing that – instead, talk about why you believe these features improved your model’s performance from the perspective of the data generating process.

Describe the modeling algorithm you chose, the hyperparameters that ended up performing the best, and the methods you used to select your modeling algorithm and hyperparameters. Describe how your final model’s performance is an improvement over your baseline model’s performance.
## Fairness Analysis
Clearly state your choice of Group X and Group Y, your evaluation metric, your null and alternative hypotheses, your choice of test statistic and significance level, the resulting p-value, and your conclusion.

