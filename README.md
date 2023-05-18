# Are Recipes with Higher Fat Contents more of a Crowd Pleaser?
by Katelyn Wong (kkw004@ucsd.edu)

---
## Introduction

In this project, I studied two datasets of recipes from food.com, one that contained instructional and nutritional information about each recipe itself and the other containing information about individual users' interactions with various recipes and their food.com account information.

While analyzing the dataset and ways that the different attributes of the recipe were related to one another, I wanted to further examine how users respond to various health information of each recipe and whether they generally prefered recipes with higher or lower fat content. Specifically, I wanted to answer the question of ***whether fattier foods are more highly rated on average.*** Answering this question would open up more insight on whether users generally choose tastier over healthier recipes, and potentially allow us to explore different ways to promote or change a recipe post in order to boost user interactions.

#### Dataset Detailed Description
The first dataset (called 'recipes') contains recipe information from food.com's Raw_recipes.csv file and includes the recipe's creator, name, date submitted, tags, nutritonal content, steps, ingredients, description. There are 83782 rows in the data, one for each recipe. For my question in particular, I looked specifically at the 'nutrition' column, which contains nutrition information in the form `[calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]`; PDV stands for “percentage of daily value".

```py
print(recipes.dtypes.to_markdown())
```

| column name    | type   |
|:---------------|:-------|
| name           | object |
| id             | int64  |
| minutes        | int64  |
| contributor_id | int64  |
| submitted      | object |
| tags           | object |
| nutrition      | object |
| n_steps        | int64  |
| steps          | object |
| description    | object |
| ingredients    | object |
| n_ingredients  | int64  |

The second dataset (called 'interactions') contains user interaction data from food.com's Raw_interactions.csv file and includes user account information, reviews, and ratings for various recipes. There are 731927 rows in this dataframe, one for each interaction. The main column I used to answer my question was the 'ratings' column, which contains the rating given by each user for the recipe.

```py
print(interactions.dtypes.to_markdown())
```

|column name| type   |
|:----------|:-------|
| user_id   | int64  |
| recipe_id | int64  |
| date      | object |
| rating    | int64  |
| review    | object |

---
## Cleaning and Exploratory Data Analysis

#### Data Cleaning
The data cleaning steps I took to organize my two datasets were to merge the data and then clean up the resulting dataframe to make it easily usable for data anlysis. My steps specifically included:
1. Left merging the recipes and interaction data sets together on 'recipe_id' column.
2. Dropping all unnecessary columns that I wasn't planning to use in my data analysis ('review', 'contributor_id', 'description',  'tags', 'steps', 'ingredients', and 'user_id').
2. Filling all user ratings of 0 with `np.nan`. This is a necessary step to allow for accurate data analysis without having all the 0's skew the results. In addition, the rating system in food.com is from 1 to 5 and so the ratings of 0 in the data were likely because the recipe itself had no ratings and therefore, a 0 was inputted to represent the lack of ratings, not that users disliked the recipe. (In fact, many of these recipes with ratings of 0 had great reviews!)
3. Calculating the average rating for recipe and adding these averages to the overall full_recipe dataframe for the recipes in each row.
5. Renaming columns in the dataframe so that their names are more descriptive.
6. Converting the data types of each column respectively to match the type of data that they are representing:
   - Changing columns with dates to datetime format.
   - Splitting the nutrition column into 7 separate columns of float type for each nutrition fact.
7. Reordering the columns in the dataframe so that it is more legible for the reader reading from left to right.


Below are the first 5 rows of the fully cleaned `full_recipes` dataframe:
```py
print(full_recipes.head().to_markdown(index = False))
```

|   recipe_id | date_submitted      | interaction_date    | name                                 |   indiv_rating |   average_rating |   minutes |   n_steps |   n_ingredients |   calories |   total_fat |   sugar |   sodium |   protein |   saturated_fat |   carbs |
|------------:|:--------------------|:--------------------|:-------------------------------------|---------------:|-----------------:|----------:|----------:|----------------:|-----------:|------------:|--------:|---------:|----------:|----------------:|--------:|
|      333281 | 2008-10-27 00:00:00 | 2008-11-19 00:00:00 | 1 brownies in the world    best ever |              4 |                4 |        40 |        10 |               9 |      138.4 |          10 |      50 |        3 |         3 |              19 |       6 |
|      453467 | 2011-04-11 00:00:00 | 2012-01-26 00:00:00 | 1 in canada chocolate chip cookies   |              5 |                5 |        45 |        12 |              11 |      595.1 |          46 |     211 |       22 |        13 |              51 |      26 |
|      306168 | 2008-05-30 00:00:00 | 2008-12-31 00:00:00 | 412 broccoli casserole               |              5 |                5 |        40 |         6 |               9 |      194.8 |          20 |       6 |       32 |        22 |              36 |       3 |
|      306168 | 2008-05-30 00:00:00 | 2009-04-13 00:00:00 | 412 broccoli casserole               |              5 |                5 |        40 |         6 |               9 |      194.8 |          20 |       6 |       32 |        22 |              36 |       3 |
|      306168 | 2008-05-30 00:00:00 | 2013-08-02 00:00:00 | 412 broccoli casserole               |              5 |                5 |        40 |         6 |               9 |      194.8 |          20 |       6 |       32 |        22 |              36 | 

#### Univariate Analysis

<iframe src="assets/uni-avg.html" width=600 height=450 frameBorder=0></iframe>
- According to the distribution of average recipe ratings histogram, we see that the distribution is unimodal, skewed to the left, and has a range of 5. In addition, the average recipe rating on food.com is 4.677 (obtained by calculating `full_recipes['average_rating'].mean()`) and a majority of the recipes have at least a 4 star rating. This severe skew in data could potentially affect data analyses on the distribution, especially if mean is used.

<iframe src="assets/uni-fat.html" width=600 height=450 frameBorder=0></iframe>
- This distribution reveals that 

#### Bivariate Analysis


#### Interesting Aggregates
As part of my exploratory data analysis, I chose a couple columns from `full_recipes` to examine aggregate statistics.

```py
print(full_recipes.groupby('indiv_rating')[['total_fat', 'saturated_fat']].mean().to_markdown())
```

|   indiv_rating |   total_fat |   saturated_fat |
|---------------:|------------:|----------------:|
|              1 |     37.0564 |         46.6791 |
|              2 |     32.7669 |         42.8822 |
|              3 |     31.6405 |         40.0881 |
|              4 |     29.9404 |         36.4327 |
|              5 |     31.7924 |         39.2268 |

- The dataframe above shows a comparison of the mean TDV for `total_fat` and `saturated_fat` for each `indiv_rating`. This reveals the general distribution of mean fat compared with recipe ratings and allows the reader to directly see how average fat levels change as the recipe rating goes higher.
INSERT GRAPH

I was also interested in how the recipe's `date_submitted` values related with the length that each recipe was estimated to take (`minutes`) so I performed an additional aggregate exploration shown below:

```py
print(split_time.groupby('date_submitted(percentile)')[['minutes', 'n_steps', 'n_ingredients', 'calories', 'total_fat']].mean().to_markdown())
```

|   date_submitted(percentile) |   minutes |   n_steps |   n_ingredients |   calories |   total_fat |
|-----------------------------:|----------:|----------:|----------------:|-----------:|------------:|
|                          0.1 |   80.2761 |   9.61853 |         8.98569 |    397.839 |     29.8684 |
|                          0.2 |   94.7813 |   9.39606 |         8.8121  |    409.634 |     31.1828 |
|                          0.3 |   82.3899 |   9.32449 |         8.79641 |    414.967 |     31.3078 |
|                          0.4 |  165.948  |  10.2368  |         8.97657 |    439.147 |     33.204  |
|                          0.5 |  100.972  |   9.95503 |         9.04796 |    412.489 |     30.5909 |
|                          0.6 |  101.541  |   9.68788 |         8.9592  |    410.773 |     31.4095 |
|                          0.7 |   82.8657 |  10.2275  |         9.18446 |    413.02  |     31.4222 |
|                          0.8 |  100.15   |   9.61097 |         8.93267 |    404.016 |     30.7462 |
|                          0.9 |  178.461  |  10.3345  |         9.38282 |    431.66  |     32.4035 |
|                          1   |   79.6851 |  11.7793  |         9.63483 |    461.484 |     37.0418 |

- Because the range of the `date_submitted` values were so big in this dataset (from `2008-01-01 00:00:00` to `2018-12-04 00:00:00`), I decided to split the `date_submitted` column into percentiles in order to calculate the aggregate statistics for `minutes`, `n_steps`, `n_ingredients`, `calories`, and `total_fat`.
INSERT GRAPH

---
## Assessment of Missingness

#### NMAR Analysis
Of all the columns in the full_recipes dataframe, only four had missing values: `interaction_date, name, indiv_rating, average_rating.` 

`interaction_date` and `name` both only had one missing value, likely because the raw `recipes` dataframe had an additional recipe that the `interactions` dataframe did not so when merging the two dataframes together, there are is no user interaction information about that recipe in the `interactions` dataframe. In addition, upon further investigation, this recipe also has a missing value in the `name` column under the recipes dataframe as well. Therefore, without loss of generality, you can tell that these individual columns are dependent on each other because in the scenario that there are any recipe_id's in the raw `recipes` dataframe that are not in the `interactions` dataframe, there will be missing values in the attributes coming from the interactions dataframe. In conclusion, the missing value in these two columns are likely not NMAR.

`Indiv_rating` and `average_rating` columns are also likely not NMAR as we will see in the following MAR permutation tests below. 

Ultimately, because these four columns that contain missing values can be explained by other columns in the dataset, there is likely no NMAR data within the `full_recipes` dataset.


#### Missingness Dependency Analysis

Here, I investigated the missingness dependency of the `average_ratings` column on the missing values of the `indiv_ratings` column, the `date_submitted` column, and the `sodium` nutritional column. I hypothesized that `average_ratings` would be MAR dependent on the `indiv_ratings` column and the `date_submitted` column, but not the `sodium` column.

When comparing missingness between `average_ratings` and `indiv_ratings`, I used the hypotheses:
- null: average_ratings is not MAR dependent on indiv_ratings
- alternative: average_ratings is MAR dependent on indiv_ratings

Then for the `indiv_ratings` column, I created two new boolean columns for whether a value is missing in the `average_ratings` column and the `indiv_ratings` column. Below is a pivot_table of these two boolean columns using the code:

```py
missing_pivot = missing_df.pivot_table(index = 'indiv_missing', columns = 'average_missing', aggfunc = 'count', values = 'recipe_id', fill_value = 0)
missing_pivot = missing_pivot / missing_pivot.sum(axis = 0)
```

| indiv_missing   |     False |   True |
|:----------------|----------:|-------:|
| False           | 0.94708   |      0 |
| True            | 0.0529199 |      1 |

I then ran a permutation test using the total variation distance as my test statistic. What I got was a p-value of 0.0 in which all values in the test statistic array were less than my observed test statistic (0.947). Therefore, I rejected the null hypothesis to claim that there is significant statistical evidence that missingness in `average_ratings` is MAR dependent on missingness in `indiv_ratings`.
INSERT PLOTS

When comparing missingness between `average_ratings` and `date_submitted`, I used the hypotheses:
- null: average_ratings is not MAR dependent on date_submitted
- alternative: average_ratings is MAR dependent on dated_submitted

Below is a pivot table comparing the date a recipe is submitted and whether the recipe is missing its average rating or not:
```py
missing_pivot = missing_df.pivot_table(index = 'date_submitted', columns = 'average_missing', aggfunc = 'count', values = 'recipe_id', fill_value = 0)
missing_pivot = missing_pivot / missing_pivot.sum(axis = 0)
```

| date_submitted      |       False |        True |
|:--------------------|------------:|------------:|
| 2011-03-04 00:00:00 | 9.92869e-05 | 0           |
| 2012-05-03 00:00:00 | 4.31682e-05 | 0           |
| 2009-10-12 00:00:00 | 0.0012821   | 0.00288081  |
| 2012-12-20 00:00:00 | 2.15841e-05 | 0           |
| 2010-05-05 00:00:00 | 0.00145045  | 0.000360101 |

For the `date_submitted` column, I also used a permutation test similar to the test with `indiv_ratings` by calculating the total variation distance between the missing and non-missing `average_ratings` distributions with respect to the dates each recipe was submitted. Here, my observed test statistic was 0.425 and my p-value was also 0.0. I then rejected my null hypothesis again to conclude that there is significant statistical evidence that missingness in `average_ratings` is MAR dependent on `date_submitted`.
INSERT PLOTS

Lastly, when comparing missingness between `average_ratings` and `sodium`, I used the hypotheses:
- null: average_ratings is not MAR dependent on sodium
- alternative: average_ratings is MAR dependent on sodium

Because TDV of sodium is numerical data, I used a permutation test with the absolute difference in median of sodium between the missing and nonmissing `average_ratings` groups. My observed test statistic was 1.0 and I calculated a p-value of 0.38. Therefore, with a significance threshold of 0.5, I failed to reject the null hypothesis and concluded that there is no statistical evidence that average_ratings is MAR dependent on sodium.
INSERT PLOTS

---
## Hypothesis Testing

Finally, I will now use hypothesis testing to answer the question: ***Are fattier foods more highly rated on average?*** I will utilize both the `total_fat` and `saturated_fat` columns in the `full_recipes` dataframe in my analysis.
In addition, I filled the missing values in the `average_ratings` column by randomly choosing values from the `average_ratings` column. In doing so, I am able to maintain an overall column probability distribution similar to the initial distribution of non-missing values.

I also decided to determine the cutoff value of fatty v. not fatty foods by using the 50th percentile of the `total_fat` and `saturated_fat` columns. This allows me to directly compare the average ratings of the 2 fat groups, one with fattier recipes and one with less fatty recipes.

#### Hypothesis Testing of Fattiness v Average User Rating (using `total_fat`):
Hypotheses:
- Null: The average rating for fattier foods (based on `total_fat`) is the same as the average rating for less fatty foods.
- Alternative: The average rating for fattier foods (based on `total_fat`) is higher than the average rating for less fatty foods.

    Conducting a hypothesis test under these two hypotheses will directly allow me to answer the question of whether fattier foods are more highly rated on average because rejecting the null will allow me to conclude that there is evidence that fattier foods are more highly rated on average while failing to reject the null will allow me to conclude that there is not enough evidence that fattier foods are more highly rated on average.
    
Simulation:
- I used a test statistic of **difference in group medians between fattier foods and less fattier foods**. Because the alternative hypothesis is claiming that fattier foods are more highly rated, there is a direction in the hypothesis, which is why I refrained from using absolute difference. Also, since average rating is a numerical type of data, I decided to use group medians rather than TVD.
- For the simulation, I used a *permutation test* because I am comparing whether the two fat distributions samples are from the same distribution.
- I used a significance level of 0.05 as this is a standard significance level that is usually used for hypothesis testing.
- My observed test statistic was 0.010

Results and conclusion:
- I calculated a p-value of 0.0, which led me to reject the null hypothesis and conclude that there is significant statistical evidence that the average rating for fattier foods based on `total_fat` is higher than the average rating for less fatty foods.


#### Hypothesis Testing of Fattiness v Average User Rating (using `saturated_fat`):
Hypotheses:
- Null: The average rating for fattier foods (based on `saturated_fat`) is the same as the average rating for less fatty foods.
- Alternative: The average rating for fattier foods (based on `saturated_fat`) is higher than the average rating for less fatty foods.

    Again, similar to the hypothesis test using `total_fat`, conducting a hypothesis test under these two hypotheses will directly allow me to answer the question of whether fattier foods are more highly rated on average. I am merely using another similar category to answer the same question.
    
Simulation:
- I used permutation testing, the **difference in group medians between fattier foods and less fattier foods** test statistic, and the same significance level of 0.05 as the previous hypothesis test with `total_fat`.
- My observed statistic was 0.182

Results and conclusion:
- I calculated a p-value of 0.02, which still led me to reject the null hypothesis because my p-value is less than the significance level of 0.05. Ultimately, I concluded that there is still significant statistical evidence that the average rating for fattier foods is higher than the average rating for less fatty foods even when using `saturated_fat`.




---
