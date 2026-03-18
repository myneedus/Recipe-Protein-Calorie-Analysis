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

In order to effectively find this relationship for my guiding question, I chose to especially focus on the following columns: 'nutrition' and recipe 'tags' to see the impact of these variables across various recipes. Later on, to create a prediction model, I focus on columns: 'nutrition', and 'reviews' more. 

## Data Cleaning and Exploratory Data Analysis
### Univariate Analysis 
<img width="602" height="402" alt="image" src="https://github.com/user-attachments/assets/d827d306-33bf-4a78-a6f6-c220543327ac" />
This is a plot of the distribution of calories between 0 and 2000 (to avoid extreme outliers). The majority of the recipes within the common range of 0-2000 calories primarily exist in the 0-500 calories range, which is important to note, as even the small amount of protein in recipes in this range can result in a high protein-to-calorie ratio. 
### Bivariate Analysis

## Assessment of Missingness
## Hypothesis Testing
## Framing a Prediction Problem
## Baseline Model
## Final Model
## Fairness Analysis

