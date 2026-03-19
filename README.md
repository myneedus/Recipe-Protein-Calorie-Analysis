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

The  dataset, `recipe`, contains **83,782** rows, each representing a distinct recipe, with 10 columns recording the following information:

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

The second dataset, `interactions`, contains **73,1927** rows. Each row is a unique review from a user with columns describing:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

In order to effectively find this relationship for my guiding question, I chose to especially focus on the following columns: 'nutrition' and recipe 'tags' to see the impact of these variables across various recipes. Later on, to create a prediction model, I focus on columns: 'nutrition' and 'reviews' more. 

## Data Cleaning and Exploratory Data Analysis
To prepare the dataset for analysis, the following cleaning steps were taken:

1. **Merged recipes and ratings** using a left join on recipe ID. This ensured that all of the recipes were kept, even if they were missing reviews.
2. **Replaced 0 ratings with NaN** — a rating of 0 likely indicates the user did not actually submit a rating, so instead of treating it as a 0 and risk skewing average ratings, I chose to treat it as missing.
3. **Converted date columns** (`submitted` and `date`) from strings to datetime objects for general proper handling.
4. **Computed `avg_rating`** by grouping by recipe ID and taking the mean of all non-missing ratings, then merging it back onto the recipes DataFrame.
5. **Deduplicated to one row per recipe** using `drop_duplicates(subset='id')` since the merged DataFrame had one row per interaction, popular recipes with many reviews were overrepresented. Deduplication ensures each recipe contributes equally to the analysis.
6. **Parsed the `nutrition` column** from a string into individual numeric columns for calories, protein, fat, sugar, sodium, saturated fat, and carbohydrates.
7. **Filtered out recipes with 0 or unreasonably high calories** (above 10,000) as these are likely data entry errors that would produce misleading protein-to-calorie ratios.
8. **Derived `protein_to_cal_ratio`** by converting protein PDV to grams, calculating protein calories (4 calories per gram), then dividing by total calories. Values were clipped at 1.0 because protein cannot physically contribute more than 100% of a recipe's calories.
9. **Created `is_meat`** as a boolean flag by checking whether the ingredients column contained any of the keywords: meat, chicken, beef, pork, or fish.
10. **Created `category`** by matching each recipe's tags against a list of broad food category keywords, defaulting to "other" if none matched.

The resulting cleaned DataFrame is shown below:

| name | category | calories (#) | protein\_to\_cal\_ratio | is\_meat | rating |
|:---|:---|---:|---:|:---|---:|
| 1 brownies in the world best ever | dessert | 138.4 | 0.0433526 | False | 4 |
| 1 in canada chocolate chip cookies | other | 595.1 | 0.0436901 | False | 5 |
| 412 broccoli casserole | vegetable | 194.8 | 0.225873 | True | 5 |
| millionaire pound cake | dessert | 878.3 | 0.0455425 | False | 5 |
| 2000 meatloaf | meat | 267 | 0.217228 | True | 5 |

### Univariate Analysis 
<iframe src="assets/Calories per Recipe.html" width="800" height="600" frameborder="0"
></iframe> 

This histogram is a plot of the distribution of calories between 0 and 2000 (to avoid extreme outliers). The majority of the recipes within the common range of 0-2000 calories primarily exist in the 0-500 calories range, which is important to note, as even the small amount of protein in recipes in this range can result in a high protein-to-calorie ratio. 

### Bivariate Analysis
<iframe src="assets/Meat vs. Non-Meat.html" width="800" height="600" frameborder="0"
></iframe> 

The box plot below compares protein-to-calorie ratios between meat and non-meat recipes. The **median** of the meat category is higher than the non-meat category, indicating meat has a more efficient protein-to-calorie ratio.
The meat-based category appears to be more **spread** out, with a taller box likely because of the multiple protein contents various sorts of meats provide. 
Although both categories include a number of **outliers**, the non-meat category has a very dense set of outliers, suggesting there are some non-meat recipes providing extremely protein-dense options. 

### Grouping and Aggregates 
| category | False | True |
|:---|---:|---:|
| breakfast | 0.134938 | nan |
| dessert | 0.0577054 | 0.140059 |
| meat | nan | 0.266056 |
| other | 0.119007 | 0.285941 |
| pasta | 0.143259 | nan |
| salad | 0.0951108 | nan |
| seafood | 0.289317 | 0.320133 |
| soup | 0.200785 | 0.30888 |
| vegetable | 0.123033 | 0.300254 |

This is a pivot table showing the average protein density across various categories, further split by whether the recipe has meat. When meat is true, it consistently shows higher protein content across all categories. Seafood is the next category, following meat, that has the highest protein density.

## Assessment of Missingness
### MNAR Analysis
I believe the certain missing values in the `avg_rating` column in this dataset are **MNAR** (Missing Not At Random). This can be attributed to the fact that certain recipes can be difficult, highly niche, or unpopular. Recipes such as these are likely less frequented or even attempted by people. As a result, this can affect the missingness of the unobserved rating itself. In order to confirm and qualify as an MAR, however, more data would need to be collected, such as recipe views or page traffic, to explain why certain recipes receive less engagement.

### Average Rating Column Depends on Calories Column
To determine if the missingness of the 'avg_ratings' column could be attributed to the 'calories (#)', I ran a permutation test. The result of the permutation test found an  observed difference in mean calories between recipes with and without missing ratings was 68.3102861445783. After 1000 permutations, the resulting p-value was **0.0**, which is below my significance level of 0.05. Therefore, I was able to effectively conclude that the missingness of 'avg rating' indeed **does** depend on calories. The distribution showed that the recipes with missing ratings tend to have significantly higher calorie counts. My interpretation of this is that individuals are consciously reviewing and attempting recipes with regard to calorie count. 

<iframe
  src="assets/P-value for Calories.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Average Rating Column Does Not Depend on Sodium Column
In my second permutation test run on the  `sodium (PDV)` column. The observed difference in mean sodium between recipes with and without missing ratings was 2.2938378514056197. After 1000 permutations, the resulting p-value was **0.146**, which is above my established significance level of 0.05. Therefore, I was able to conclude that the missingness of `avg_rating` **does not** depend on sodium content. More generally, the saltiness of the recipe did not affect its ratings. This did come as a surprise, as I initially had assumed saltier dishes may have had lower average ratings. 

<iframe
  src="assets/P-value for Sodium.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Hypothesis Testing
**Null Hypothesis**: There is no difference in the mean protein-to-calorie ratio across recipe categories. Any observed differences are due to random variation.

**Alternative Hypothesis**: At least one recipe category has a significantly different mean protein-to-calorie ratio compared to the others.

**Test Statistic**: Variance of category means. I chose this variance because it is able to capture the spread across multiple groups simultaneously, making it appropriate for comparing more than two categories at once.

**Significance Level**: 0.05

**Result**: The observed variance of category means was 0.007003991121994941. After 1000 permutations, the resulting p-value was **0.00**.

**Conclusion**: Since the p-value is significantly below 0.05, I rejected the null hypothesis. This indeed does suggest that protein-to-calorie ratio varies significantly across recipe categories, indicating that recipe type is a meaningful predictor of protein efficiency.

## Framing a Prediction Problem
**Prediction Problem**: Predict the protein-to-calorie ratio of a recipe.

**Type**: Regression: I chose regression because `protein_to_cal_ratio` is a continuous variable between 0 and 1, and as a result, made it effectively a regression problem rather than a classification problem.

**Response Variable**: I chose the **`protein_to_cal_ratio`** because it is the central metric of the entire analysis. Predicting it from basic recipe features is a natural extension of the question explored in Steps 1–4 and would allow someone to estimate how protein-efficient a recipe is before cooking it. 

**Evaluation Metric**: RMSE (Root Mean Squared Error). I chose this over R² because it is interpretable in the same units as the response variable, making it easier to understand how far off predictions are in practical terms. RMSE also penalizes large errors more heavily than MAE, which is appropriate here since a large misprediction of protein ratio could meaningfully mislead someone making dietary decisions.

**Features available at time of prediction**: All features used — `is_meat`, `n_steps`, `category`, and `n_ingredients` — are knowable at the moment a recipe is submitted to food.com, before any ratings or interactions occur.

## Baseline Model
**Model**: Linear Regression

**Features**:
- `is_meat` (nominal) — one-hot encoded using `OneHotEncoder(drop='first')` since it is a boolean categorical variable
- `n_steps` (quantitative) — left as-is since it is already numeric

**Performance**:
- Test RMSE: 0.1047
- Test R²: 0.3238

The baseline model is not particularly strong. An R² of 0.3238 indicates that `is_meat` and `n_steps` alone explain only a small portion of the variance in protein-to-calorie ratio. This makes sense because while meat recipes do tend to have higher protein ratios, the number of steps in a recipe has a weak relationship with its nutritional composition. The baseline serves as a useful reference point for evaluating the improvement made by later in the final model.

## Final Model
**New Features Added**:
- `category` (nominal) — one-hot encoded. This feature was added because different food categories have fundamentally different nutritional profiles from the data-generating process perspective. A dessert recipe and a seafood recipe differ in protein efficiency not by accident but by the nature of their ingredients. Adding a category gives the model a meaningful signal about what kind of food is being prepared.
- `n_ingredients` (quantitative) — standardized using `StandardScaler`. Recipes with more ingredients tend to be more complex and are more likely to include a wider variety of protein sources. From a data-generating process perspective, ingredient count is a proxy for recipe complexity, which correlates with nutritional diversity.

**Modeling Algorithm**: Random Forest Regressor — chosen over Linear Regression because it can capture non-linear relationships between features and protein-to-calorie ratio, which the scatter plots from Step 2 suggested exist.

**Hyperparameters Tuned**:
- `n_estimators` — controls the number of trees in the forest. More trees reduce variance but increase computation time
- `max_depth` — controls how deep each tree grows. Deeper trees can overfit, so tuning this helps generalization
- `min_samples_split` — controls the minimum samples required to split a node, helping prevent overfitting on small groups

**Best Hyperparameters**: [INSERT FROM search.best_params_]

**Performance**:
- Final Test RMSE: 0.0989
- Final Test R²: 0.3958
- Baseline Test RMSE: 0.1047

The final model improved over the baseline with a lower RMSE of 0.0989 compared to the baseline RMSE of 0.1047, indicating that adding category and ingredient count meaningfully improved the model's ability to predict protein-to-calorie ratio on unseen recipes. Although the change is not as drastic as I would have hoped, therefore in the future I want to further refine this model to become a better predictor.

## Fairness Analysis
**Group X**: Meat-based recipes (`is_meat = True`)

**Group Y**: Non-meat recipes (`is_meat = False`)

**Evaluation Metric**: RMSE — used to measure how accurately the model predicts protein-to-calorie ratio for each group

**Null Hypothesis**: The model is fair — its RMSE for meat-based recipes and non-meat recipes are roughly the same, and any observed difference is due to random chance.

**Alternative Hypothesis**: The model is unfair — its RMSE differs significantly between meat-based and non-meat recipes.

**Test Statistic**: Absolute difference in RMSE between the two groups

**Significance Level**: 0.05

**Result**: The observed RMSE difference was [INSERT VALUE]. After 1000 permutations, the resulting p-value was **[INSERT VALUE]**.

**Conclusion**: Since the p-value is [above/below] 0.05, we [fail to reject/reject] the null hypothesis. This suggests the model [is/is not] fair with respect to meat vs. non-meat recipes.
