# Are Recipes with a Higher Fat Content more of a Crowd Pleaser?
by Katelyn Wong (kkw004@ucsd.edu)

---
## Introduction

In this project, I studied two datasets of recipes from food.com, one that contained instructional and nutritional information about each recipe itself and the other containing information about individual users' interactions with various recipes and their food.com account information.

While analyzing the dataset and ways that the different attributes of the recipe were related to one another, I wanted to further examine how users respond to various health information of each recipe and whether they generally prefered recipes with higher or lower fat content. Specifically, I wanted to answer the question of ***whether fattier foods are more highly rated on average.*** Answering this question would open up more insight on whether users generally choose tastier over healthier recipes, and potentially allow us to explore different ways to promote or change a recipe post in order to boost user interactions.

#### Dataset Detailed Description
The first dataset (called 'recipes') contains recipe information from food.com's Raw_recipes.csv file and includes the recipe's `creator, name, date submitted, tags, nutritonal content, steps, ingredients, description`. There are 83782 rows in the data, one for each recipe. For my question in particular, I looked specifically at the 'nutrition' column, which contains nutrition information in the form `[calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]`; PDV stands for “percentage of daily value".

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

The second dataset (called 'interactions') contains user interaction data from food.com's Raw_interactions.csv file and includes user account information, reviews, and ratings for various recipes. There are 731927 rows in this dataframe, one for each interaction. The main column I used to answer my question was the `ratings` column, which contains the rating given by each user for the recipe.

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
The data cleaning steps I took to organize my data included merging the two datasets and thening clean up the resulting dataframe to make it easily usable for data anlysis. My steps specifically included:
1. Left merging the recipes and interaction data sets together on 'recipe_id' column.
2. Dropping all unnecessary columns unused in data analysis (`review, contributor_id, description,  tags, steps, ingredients`, and `user_id`).
2. Filling all user ratings of 0 with `np.nan`. This is a necessary step to allow for accurate data analysis without having the 0's skew the results. In addition, the rating system in food.com ranges from 1 to 5  so the ratings of 0 in the data were likely inputted due to the lack of ratings for the recipe itself, not that users disliked the recipe. (In fact, many of these recipes with ratings of 0 had great reviews!)
3. Calculating the average rating for recipe and merging these averages to the overall full_recipe dataframe.
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
- According to the distribution of average recipe ratings histogram, we see that the distribution is unimodal, skewed to the left, and has a range of 5. In addition, the median average recipe rating on food.com is 4.863 and a majority of the recipes have at least a 4 star rating. The severe skew in the data could potentially affect data analyses on the distribution, especially if mean is used.

<iframe src="assets/uni-fat.html" width=600 height=450 frameBorder=0></iframe>
- According to this histogram, the distribution of total fat of each recipe is unimodal, centered on the median of 20, and right skewed. The IQR is 31 and there are many outliers outside of the interquartile range, but I chose to keep these outliers in the dataset and instead, limit the range of the x-axis (total_fat) to 0-150 PDV to place more focus on the IQR of the data. Again, the skew in the data indicates that using the median for analysis may yield more accurate results than using the mean.

<iframe src="assets/uni-sfat.html" width=600 height=450 frameBorder=0></iframe>
- This distribution is also similar to the distribution above. It is unimodal, there is a right skew, and the median is centered at 22 PDV (which is close to the median at 20 PDV of the Total Fat Distribution). The IQR is approximately 42 which means that the spread of this distribution is wider than the Total Fat Distribution. Similarly, the skew in this distribution and the large number of outliers indicates that using the median for any hypothesis testing is more accurate than using the mean, which can be affected by the skew and outlier data.

#### Bivariate Analysis
<iframe src="assets/avg-fat.html" width=800 height=500 frameBorder=0></iframe>
- This comparison between average recipe rating and total fat (PDV) in a recipe reveals that the correlation coefficient r seems to have a low positive value. This means there may possibly be a slight trend where higher average recipe ratings also have higher total fat content.

<iframe src="assets/avg-sfat.html" width=800 height=500 frameBorder=0></iframe>
- Again, this graph is similar to the scatterplot between total fat and average recipe rating. There is also a similar trend where the data follows a correlation coefficient r that has a low positive value.

#### Interesting Aggregates
As part of my exploratory data analysis, I chose a few columns from `full_recipes` to examine aggregate statistics.

```py
print(full_recipes.groupby('indiv_rating')[['total_fat', 'saturated_fat']].median().to_markdown())
```

|   indiv_rating |   total_fat |   saturated_fat |
|---------------:|------------:|----------------:|
|              1 |          19 |              22 |
|              2 |          20 |              24 |
|              3 |          20 |              21 |
|              4 |          19 |              20 |
|              5 |          20 |              22 |

<iframe src="assets/indiv-fats.html" width=800 height=500 frameBorder=0></iframe>

- The dataframe and graph above shows a comparison of the median PDV for `total_fat` and `saturated_fat` for each `indiv_rating`. They reveal the general IQR distribution of fat and allow the reader to directly see how average fat levels change as the recipe rating goes higher. I chose to represent this comparison with a grouped box plot to directly show the side by side distribution differences between `total_fat` and `saturated_fat`


I was also interested in how the recipe's `date_submitted` values related with the length of each recipe (`minutes`) so I performed an additional aggregate exploration shown below using 10 date bins to compoare various recipe attributes:

```py
print(split_time.groupby('date_submitted(percentile)')[['minutes', 'n_steps', 'n_ingredients', 'calories', 'total_fat']].median().to_markdown())
```
|   date_submitted(percentile) |   minutes |   n_steps |   n_ingredients |   calories |   total_fat |
|-----------------------------:|----------:|----------:|----------------:|-----------:|------------:|
|                          0.1 |        35 |         8 |               9 |      296.2 |          19 |
|                          0.2 |        35 |         8 |               8 |      295.4 |          19 |
|                          0.3 |        35 |         8 |               8 |      298.5 |          19 |
|                          0.4 |        35 |         9 |               9 |      310   |          19 |
|                          0.5 |        35 |         9 |               9 |      306.4 |          20 |
|                          0.6 |        31 |         8 |               9 |      296.3 |          20 |
|                          0.7 |        39 |         9 |               9 |      296.3 |          19 |
|                          0.8 |        33 |         8 |               9 |      287.1 |          18 |
|                          0.9 |        35 |         9 |               9 |      312.4 |          20 |
|                          1   |        40 |        10 |               9 |      327.5 |          24 |


<iframe src="assets/date-min.html" width=800 height=500 frameBorder=0></iframe>

- Because the range of the `date_submitted` values were so big in this dataset (from `2008-01-01 00:00:00` to `2018-12-04 00:00:00`), I decided to split the `date_submitted` column into percentiles in order to calculate the aggregate statistics for `minutes`, `n_steps`, `n_ingredients`, `calories`, and `total_fat`.

---
## Assessment of Missingness

#### NMAR Analysis
**I don't believe there is a column in my dataset that is NMAR.** Of all the columns in the full_recipes dataframe, only four had missing values: `interaction_date, name, indiv_rating, average_rating.` 

`interaction_date` and `name` both only had one missing value, likely because the raw `recipes` dataframe had an additional recipe that the `interactions` dataframe did not so when merging the two dataframes together, `np.nans` would replace user interaction information about that recipe in the `interactions` dataframe. In addition, upon further investigation, this recipe also has a missing value in the `name` column under the recipes dataframe as well. Therefore, without loss of generality, you can tell that these individual columns are dependent on each other because anytime there are any recipe_id's in the raw `recipes` dataframe that are not in the `interactions` dataframe, there will be missing values in the attributes from the interactions dataframe. In conclusion, the missing value in these two columns are likely not NMAR.

`Indiv_rating` and `average_rating` columns are also likely not NMAR as we will see in the following MAR permutation tests below. 

Ultimately, because these four columns that contain missing values can be explained by other columns in the dataset, there is likely no NMAR data within the `full_recipes` dataset.


#### Missingness Dependency Analysis

Here, I investigated the missingness dependency of the `average_ratings` column on the missing values of the `indiv_ratings` column, the `date_submitted` column, and the `sodium` nutritional column. I hypothesized that `average_ratings` would be MAR dependent on the `indiv_ratings` column and the `date_submitted` column, but not the `sodium` column.

**When comparing missingness between `average_ratings` and `indiv_ratings`, I used the hypotheses:**
- null: `average_ratings` is not MAR dependent on `indiv_ratings`
- alternative: `average_ratings` is MAR dependent on `indiv_ratings`

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


**When comparing missingness between `average_ratings` and `date_submitted`, I used the hypotheses:**
- null: `average_ratings` is not MAR dependent on `date_submitted`
- alternative: `average_ratings` is MAR dependent on `dated_submitted`

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

<iframe src="assets/date-avg.html" width=800 height=500 frameBorder=0></iframe>
This histogram above reveals the differences in observed distributions of recipe's `date_submitted` attribute for recipes with missing `average_ratings` compared to those with `average_ratings` to show that there is likely a MAR dependency of `average_ratings` to `date_submitted`.


**Lastly, when comparing missingness between `average_ratings` and `sodium`, I used the hypotheses:**
- null: `average_ratings` is not MAR dependent on `sodium`
- alternative: `average_ratings` is MAR dependent on `sodium`

Because TDV of sodium is numerical data, I used a permutation test with the absolute difference in median of sodium between the missing and nonmissing `average_ratings` groups. My observed test statistic was 1.0 and I calculated a p-value of 0.35. Therefore, with a significance threshold of 0.5, I failed to reject the null hypothesis and concluded that there is no statistical evidence that average_ratings is MAR dependent on sodium.

<iframe src="assets/sodium-avg.html" width=800 height=500 frameBorder=0></iframe>
This histogram above reveals how similar the distributions for missing and not-missing `average_ratings` groups in regards to the `sodium` column. Since the distributions are so similar, we can conclude based on our assessment of missingness and the histogram that `average_ratings` is not MAR dependent on `sodium`.

---
## Hypothesis Testing

Finally, I will now use hypothesis testing to answer the question: ***Are fattier foods more highly rated on average?*** I will utilize the `total_fat`, `saturated_fat`, and `average_ratings` columns in the `full_recipes` dataframe in my analysis.
In addition, I filled the missing values in the `average_ratings` column by randomly choosing values from the `average_ratings` column. In doing so, I am able to maintain an overall column probability distribution similar to the initial distribution of non-missing values.

I also decided to determine the cutoff value of fatty v. not fatty foods by using the 50th percentile of the `total_fat` and `saturated_fat` columns. This allows me to directly compare the average ratings of the 2 fat groups in each test, one with fattier recipes and one with less fatty recipes.

#### Hypothesis Testing of Fattiness v Average User Rating (using `total_fat`):
**Hypotheses:**
- Null: The average rating for fattier foods (based on `total_fat`) is the same as the average rating for less fatty foods.
- Alternative: The average rating for fattier foods (based on `total_fat`) is higher than the average rating for less fatty foods.

    Conducting a hypothesis test under these two hypotheses will directly answer the question of whether fattier foods are more highly rated on average because rejecting the null will allow me to conclude that there is evidence that fattier foods are more highly rated on average while failing to reject the null will lead me to conclude that there is not enough evidence that fattier foods are more highly rated on average.
    
**Simulation:**
- I used a test statistic of **difference in group medians between fattier foods and less fattier foods**. Because the alternative hypothesis is claiming that fattier foods are more highly rated, there is a direction in the hypothesis, which is why I refrained from using absolute difference. Also, since average rating is a numerical type of data, I decided to use group medians rather than TVD.
- For the simulation, I used a *permutation test* because I am comparing whether the two fat distributions samples are from the same distribution.
- I used a significance level of 0.05 as this is a standard significance level that is usually used for hypothesis testing.
- My observed test statistic was 0.010

**Results and conclusion:**
- I calculated a p-value of 0.0, which led me to reject the null hypothesis and conclude that there is significant statistical evidence that the average rating for fattier foods based on `total_fat` is higher than the average rating for less fatty foods.


#### Hypothesis Testing of Fattiness v Average User Rating (using `saturated_fat`):
**Hypotheses:**
- Null: The average rating for fattier foods (based on `saturated_fat`) is the same as the average rating for less fatty foods.
- Alternative: The average rating for fattier foods (based on `saturated_fat`) is higher than the average rating for less fatty foods.

    Again, similar to the hypothesis test using `total_fat`, conducting a hypothesis test under these two hypotheses will directly allow me to answer the question of whether fattier foods are more highly rated on average. I am merely using another similar recipe attribute to answer the same question.
    
**Simulation:**
- I used permutation testing and the same significance level of 0.05 as the previous hypothesis test with `total_fat`. However, this time, I had to use the **difference in group means between fattier foods and less fattier foods** as the test statistic rather than the difference in group medians because if I used group medians for this distribution specifically, the observed statistic would be 0.0 and every test statistic would be 0 or higher, leading to a p-value of 1.0. Therefore, I decided to use means so that the fatty and nonfatty distributions wouldn't directly cancelling each other out. 
- My observed statistic was 0.005.

**Results and conclusion:**
- I calculated a p-value of 0.02, which still led me to reject the null hypothesis because my p-value is less than the significance level of 0.05. Ultimately, I concluded that there is still significant statistical evidence that the average rating for fattier foods is higher than the average rating for less fatty foods even when using `saturated_fat`.


---
# Conclusion
Overall, statistical evidence appears to support the conclusion that there is a higher average rating for fattier foods according to the hypothesis tests.

---
