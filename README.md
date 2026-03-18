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
| name                                 | category   |   calories (#) |   protein_to_cal_ratio | is_meat   |   rating |
|:-------------------------------------|:-----------|---------------:|-----------------------:|:----------|---------:|
| 1 brownies in the world    best ever | dessert    |          138.4 |              0.0433526 | False     |        4 |
| 1 in canada chocolate chip cookies   | other      |          595.1 |              0.0436901 | False     |        5 |
| 412 broccoli casserole               | vegetable  |          194.8 |              0.225873  | True      |        5 |
| millionaire pound cake               | dessert    |          878.3 |              0.0455425 | False     |        5 |
| 2000 meatloaf                        | meat       |          267   |              0.217228  | True      |        5 |
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

This is a pivot table showing the average protein density across various categories, further split by whether the recipe has meat. When meat is true, it consistently shows higher protein content across all categories. Seafood is the next category, following meat, that has the highest protein density.

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

