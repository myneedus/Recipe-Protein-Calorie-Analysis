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

In order to effectively find this relationship, I chose to focus on the following key variables: calories, protein content, and recipe tags to see the impact of these variables across various recipes. 
The dataset contains **83,782 recipes** with the following relevant columns:

| Column | Description |
|--------|-------------|
| `nutrition` | Contains calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates as percent daily values |
| `protein (PDV)` | Protein content as a percentage of daily value, used to derive protein-to-calorie ratio |
| `calories (#)` | Total calories per recipe, the denominator of our key metric |
| `tags` | Food.com tags used to categorize recipes, used to derive `is_meat` and `category` |
| `protein_to_cal_ratio` | Derived column representing how efficiently a recipe delivers protein per calorie |
| `is_meat` | Boolean indicating whether a recipe is meat-based, derived from tags |
## Data Cleaning and Exploratory Data Analysis
## Assessment of Missingness
## Hypothesis Testing
## Framing a Prediction Problem
## Baseline Model
## Final Model
## Fairness Analysis

