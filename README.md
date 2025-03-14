# Winter 2025 Pro League of Legends Attack Damage Carry Analysis
Winter 2025 Pro League of Legends Attack Damage Carry (ADC) Analysis is a data science project undertaken at UCSD. The project spans multiple phases, including data cleaning, exploratory data analysis, missingness assesstment, hypothesis testing, baseline model development, and fairness assessment. Its central aim is to determine whether ADC players truly carry their teams in 2025 Pro League matches.

Authors: Jesus Tello
## Introduction

### General Introduction

League of Legends (LoL) is a globally renowned multiplayer online battle arena (MOBA) game developed and published by Riot Games. Since its release in 2009, it has grown into one of the most influential titles in esports, attracting millions of players and fans worldwide. In LoL, two teams of five champions each compete on a dynamic battlefield called Summoner’s Rift, where the primary goal is to destroy the enemy’s nexus while defending one’s own. The game features a diverse roster of over 150 champions, each equipped with unique abilities that cater to various roles, ranging from high-damage carries to supportive and strategic playmakers.

The ADC is designed to deliver sustained physical damage and is designed to scale or get stronger as they progressively get more gold within a match. Traditionally partnered with a support in the bottom lane, ADCs must navigate a delicate balance between survivability and damage output, positioning themselves to inflict critical damage during team fights. In 2024, ADCs transcended their typical role, finding success in other lanes, such as Top and Mid lane, due to their high damage potential and scaling prowess. This flexibility saw them becoming a meta mainstay across various positions, significantly impacting team's strategies and game outcomes. However, this dominance also led to deliberate adjustments by Riot. The runes and items that fueled ADCs’ power were subsequently nerfed in an effort to restore balance.

The competitive landscape underwent further transformation with the shift into 2025. A significant change was the removal of ADC's upgradeable boot option, Zephyr boots, in favor of a new Feats of Strength system featuring upgradeability for all boot options. While this system introduces dynamic choices for most roles, ADCs find themselves at a disadvantage. Their go-to option, Berserker’s Greaves, now pales in comparison to other upgrade paths, raising concerns about whether the ADC can continue to serve as the primary sustained damage dealer in professional play.

With the given context, the central question of this project arises: **In tier 1 professional leagues, the highest level of League of Legends professional play, does the ADC tend to have the highest amount of kills and damage when compared to other rules? Does the ADC truly carry at such a high-level of play?** Using comprehensive data analysis techniques, the project seeks to determine if ADC players in tier 1 professional League of Legends matches consistently record higher kills and damage than their counterparts, thereby assessing whether ADCs truly serve as the primary carry in elite play, providing insights that could influence strategic team decision-making in future matches.

### Column Introduction

<a href="https://oracleselixir.com/tools/downloads" target="_blank">Oracles Elixir Match Data</a>

**The 2025 dataset** contains 21,264 rows and 161 columns and was sourced from Oracle. It includes detailed match and player-level statistics for League of Legends, ranging numerous leagues across different tiers of professional play. Here are some of the columns and basic descriptions: 

- `gameid`: A unique identifier for each individual match, allowing you to distinguish between different games.
- `league`: The name or identifier of the professional league (e.g., LCK, LPL, etc.).
- `position`: The role played by a participant (e.g., Top, Jungle, Mid, Bot, Support).
- `teamname`: The name of the team or organization the player is representing.
- `gamelength`: The total duration of the match, typically recorded in seconds.
- `result`: Indicates whether the team associated with this row won (1) or lost (0) the match.
- `kills`: The number of enemy champions killed by the player.
- `deaths`: The number of times the player died.
- `assists`: The number of kills in which the player assisted.
- `pentakills`: The number of times the player achieved a “penta kill” (killing all five enemy champions in rapid succession).
- `firstbloodkill`: A binary indicator (1 or 0) denoting whether this player secured the first kill of the match.
- `damagetochampions`: The total damage the player dealt to enemy champions.
- `damageshare`: The fraction of the team’s total damage contributed by the player.
- `goldspent`: The total amount of gold the player spent on items during the match.
- `total cs`: The total “creep score,” representing the number of minions and jungle monsters the player last-hit.

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

To streamline data cleaning, **(1)** only columns that were deemed necessary for honing in on the project's focus were kept, such as: `gameid`, `league`, `position`, `teamname`, `gamelength`, `result`, `kills`, `deaths`, `assists`, `pentakills`, `firstbloodkill`, `damagetochampions`, `damageshare`, `goldspent`, and `total cs`. After, was the **(2)** removal of rows where the `position` was labeled as “team,” since this information was redundant due to the project's focus on the 5 player-per-team role entries in a given match. Next, was **(3)** filtering the dataset to include only tier 1 professional leagues and the most recent up-to-date matches First Stand Tournament as of the finalization of this project. Following this, was **(4)** converting `gamelength` from seconds to minutes for easier interpretation, and **(5)** updating position labels to more readable names like “Top,” “Jungle,” “Mid,” “Bot,” and “Support.” Lastly, was **(6)** renaming the `total cs` column to `total_cs` for consistency. 

Below is the head of the pro_league_clean dataframe

<style>
.table-wrapper {
  overflow-x: scroll;
}

</style>

<div class="table-wrapper" markdown="block">

| gameid             | league   | position   | teamname     |   gamelength |   result |   kills |   deaths |   assists |   pentakills |   firstbloodkill |   damagetochampions |   damageshare |   goldspent |   total_cs |
|--------------------|----------|------------|--------------|--------------|----------|---------|----------|-----------|--------------|------------------|---------------------|---------------|-------------|------------|
| 11715-11715_game_1 | LPL      | Top        | Weibo Gaming |      35.3833 |        1 |       4 |        0 |         3 |          nan |                0 |                9830 |     0.123949  |       12934 |        314 |
| 11715-11715_game_1 | LPL      | Jungle     | Weibo Gaming |      35.3833 |        1 |       4 |        0 |         5 |          nan |                1 |               10013 |     0.126255  |       13193 |        273 |
| 11715-11715_game_1 | LPL      | Mid        | Weibo Gaming |      35.3833 |        1 |       5 |        1 |         8 |          nan |                0 |               37571 |     0.473724  |       14249 |        286 |
| 11715-11715_game_1 | LPL      | Bot        | Weibo Gaming |      35.3833 |        1 |       3 |        2 |         6 |          nan |                0 |               15603 |     0.196738  |       13169 |        305 |
| 11715-11715_game_1 | LPL      | Support    | Weibo Gaming |      35.3833 |        1 |       1 |        2 |        11 |          nan |                0 |                6292 |     0.0793345 |        8620 |         38 |

</div>

This cleaned DataFrame includes all the columns needed for the remainder of the project, and any additional columns introduced in subsequent sections will be noted accordingly.

### Univariate Analysis

The nature of this project's question is inherently bivariate - however, looking at the distribution of Game Length reveals something interesting.

<iframe
  src = "/assets/game-length-hist.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

The distribution is almost normal, but still right skewed. Most of the games in the dataset are between roughly 25-35 minutes. The analysis also included plotting the distribution of total kills per game to further examine average game metrics.

<iframe
  src = "/assets/kills-hist.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

The distribution is also almost normal, with a smaller right skew. Most of the total kills per game in the dataset are between roughly 20-35. Both of these visualizations are important because they offer insights into overall match dynamics and the intensity of gameplay, which will later help determine whether an ADC's median or average kills per game exceed normal levels.

### Bivariate Analysis

A box plot was created to display the distribution of kills by position. This will help visualize whether ADCs truly have more kills on average than other roles.

<iframe
  src = "/assets/kills-box.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

The plot reveals that ADC does indeed have a higher median kill than every other role. Furthermore, it seems that the Support role contributes almost no kills in tier 1 professional League of Legends. Let's investigate this further by looking at a box plot to display the distribution of damage by position.

<iframe
  src = "/assets/dmg-box.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

As evident by the two plots, it seems entirely safe to assume that ADCs will always have more damage and kills than the Support role. The kills and damage dealt by supports when compared to ADCs is significantly smaller. However, statistical tests are needed to confirm if ADCs also outperform Top, Mid, and Jungle roles in these metrics. 

### Interesting Aggregates

Lastly for this section are the interesting aggregates - they conveniently summarize the current general trend of the data. 

<div class="table-wrapper" markdown="block">

| position   |   kills |   deaths |   assists |   pentakills |   firstbloodkill |   damagetochampions |   damageshare |   goldspent |   total_cs |
|:-----------|--------:|---------:|----------:|-------------:|-----------------:|--------------------:|--------------:|------------:|-----------:|
| Bot        |    4437 |     2351 |      5647 |            1 |              157 |         2.45964e+07 |      282.863  | 1.32359e+07 |     305702 |
| Jungle     |    2837 |     2919 |      7505 |            0 |              142 |         1.46373e+07 |      168.853  | 1.07686e+07 |     210158 |
| Mid        |    3488 |     2595 |      6096 |            2 |               83 |         2.30555e+07 |      267.318  | 1.25519e+07 |     282120 |
| Support    |     882 |     3606 |      9790 |            0 |               59 |         6.61041e+06 |       78.2206 | 7.49069e+06 |      35285 |
| Top        |    2654 |     2863 |      5448 |            0 |               62 |         1.81098e+07 |      210.745  | 1.15682e+07 |     247617 |


</div>

As a general rule of thumb from the above aggregate, ADC role has the most amount of **total kills**, **total damage to champions**, **damage share**, **total creep score**, **total gold spent**, and **first blood kills** from all the games combined. Let's review the dataset further.

## Assessment of Missingness

### NMAR Analysis

In the original data, it seems to be the case that `ban1`, `ban2`, `ban3`, `ban4`, `ban5` columns are **Not Missing at Random (NMAR)**. These columns seemingly have missing values not explainable by any other columns, nor do the missing values follow any sort of trend. In professional League of Legends matches, each player selects to ban one champion, meaning that each team is allowed to ban 5 champions, resulting in a total of 10 champions banned per game. The only possible explanation for the missing values in the ban columns is that pro players themselves elect not to ban any particular champion. For this reason alone, it seems to hold true that the ban columns are NMAR - the missingness of these values are entirely determined by the players selecting the banned champions. These NMAR columns could be made **Missing at Random** through the introduction of a column named `team_banned`, that would contain a value of 1 if every player on a team in a given game banned a champion, and would contain a value of 0 if not every player on a team in a given game banned a champion.

### Missingness Dependency

As showcased above, the `pentakills` column contains missing values, which is strange since the value should've just been a 0 if the player did not perform a pentakill in the given match. Regardless, testing whether the missingness of the `pentakills` column depends on other columns could prove noteworthy. The two other columns chosen are `league` and `result`, the significance level is 0.05, and the test statistic is Total Variance Distance.

Null Hypothesis (H₀)
- The distribution of `league` does not differ between rows where `pentakills` is missing and rows where it is not missing.

Alternative Hypothesis (H₁)
- The distribution of `league` does differ between rows where `pentakills` is missing and rows where it is not missing.

Here is the observed distribution of `league` when `pentakills` is missing and not missing.

<div class="table-wrapper" markdown="block">

| league   |   missing_pentakills = False |   missing_pentakills = True |
|:---------|-----------------------------:|----------------------------:|
| FST      |                    0.0481283 |                           0 |
| LCK      |                    0.291444  |                           0 |
| LCP      |                    0.237968  |                           0 |
| LEC      |                    0.224599  |                           0 |
| LPL      |                    0         |                           1 |
| LTA      |                    0.0481283 |                           0 |
| LTA N    |                    0.0695187 |                           0 |
| LTA S    |                    0.0802139 |                           0 |

</div>

The permutation tests revealed that the **observed statistic** was simply 1, and that the **p-value** was 0, smaller than our significance level. Below is the empirical distribution of total variance distances. 

<iframe
  src = "/assets/perm-league.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

Given the observed TVD, empirical distribution of TVDs, and that the p-value is less than our significance threshold of 0.05, the missingness of `pentakills` depends on the professional `league` - specifically, it is entirely driven by the LPL. Therefore, we **reject** the null hypothesis in favor of the alternative hypothesis. 

Now, does the distribution of `result` differ between rows where `pentakills` is missing and rows where it is not missing. Here is the other null hypothesis and alternative hypothesis, and the observed distribution of `result` when `pentakills` is missing and not missing.

Null Hypothesis (H₀)
- The distribution of the `result` does not differ between rows where `pentakills` is missing and rows where it is not missing.

Alternative Hypothesis (H₁)
- The distribution of the `result` does differ between rows where `pentakills` is missing and rows where it is not missing.

<div class="table-wrapper" markdown="block">

|   result |   missing_pentakills = False |   missing_pentakills = True |
|---------:|-----------------------------:|----------------------------:|
|        0 |                          0.5 |                         0.5 |
|        1 |                          0.5 |                         0.5 |

</div>

The permutation tests show that the **observed statistic** was 0, and that the **p-value** was 1, the direct opposite of our previous results. Below is the empirical distribution of total variance distances. 

<iframe
  src = "/assets/perm-result.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

Given the observed TVD, empirical distribution of TVDs, and that the p-value is greater than our significance threshold of 0.05, the missingness of `pentakills` does not depend on the `result`, or rather the outcome of a given match. Therefore, we **fail to reject** the null hypothesis.

## Hypothesis Testing

The primary objective of this project is to determine whether ADC players truly carry their teams in 2025 Pro League matches by analyzing key performance metrics. To address this goal, six hypothesis tests were conducted comparing the mean number of kills and the mean damage dealt by ADC players against those of Top, Jungle, and Mid lane players. For each metric, the null hypothesis asserts that the mean for ADC (Bot) is less than or equal to the mean for the compared role, while the alternative hypothesis states that the ADC mean is greater.

A permutation test was used to evaluate these hypotheses, with the observed test statistic defined as the difference in means between the ADC group and the comparison group for the given metric. Using an overall significance level of α = 0.05 and applying a Bonferroni correction for the six tests, the per-test significance level was set to approximately 0.0083. If a test yields a p-value below 0.0083, the null hypothesis is rejected, indicating that ADC players have a statistically significant advantage in that metric over the compared role. Conversely, if the p-value is 0.0083 or higher, there is insufficient evidence to conclude that ADC performance exceeds that of the other role in that metric.

Hypothesis 1 (Kills: ADC vs. Top)
- Null Hypothesis (H₀): The mean number of kills for ADC (Bot`) is less than or equal to the mean number of kills for Top lane.
- Alternative Hypothesis (H₁): The mean number of kills for ADC (Bot) is greater than the mean number of kills for Top lane.

<iframe
  src = "/assets/perm-Top-kills.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

Hypothesis 2 (Kills: ADC vs. Jungle)
- Null Hypothesis (H₀): The mean number of kills for ADC (Bot) is less than or equal to the mean number of kills for Jungle.
- Alternative Hypothesis (H₁): The mean number of kills for ADC (Bot) is greater than the mean number of kills for Jungle.

<iframe
  src = "/assets/perm-Jungle-kills.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

Hypothesis 3 (Kills: ADC vs. Mid)
- Null Hypothesis (H₀): The mean number of kills for ADC (Bot) is less than or equal to the mean number of kills for Mid lane.
- Alternative Hypothesis (H₁): The mean number of kills for ADC (Bot) is greater than the mean number of kills for Mid lane.

<iframe
  src = "/assets/perm-Mid-kills.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

Hypothesis 4 (Damage: ADC vs. Top)
- Null Hypothesis (H₀): The mean damage for ADC (Bot) is less than or equal to the mean damage for Top lane.
- Alternative Hypothesis (H₁): The mean damage for ADC (Bot) is greater than the mean damage for Top lane.

<iframe
  src = "/assets/perm-Top-damagetochampions.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

Hypothesis 5 (Damage: ADC vs. Jungle)
- Null Hypothesis (H₀): The mean damage for ADC (Bot) is less than or equal to the mean damage for Jungle.
- Alternative Hypothesis (H₁): The mean damage for ADC (Bot) is greater than the mean damage for Jungle.

<iframe
  src = "/assets/perm-Jungle-damagetochampions.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

Hypothesis 6 (Damage: ADC vs. Mid)
- Null Hypothesis (H₀): The mean damage for ADC (Bot) is less than or equal to the mean damage for Mid lane.
- Alternative Hypothesis (H₁): The mean damage for ADC (Bot) is greater than the mean damage for Mid lane.

<iframe
  src = "/assets/perm-Mid-damagetochampions.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

<div class="table-wrapper" markdown="block">

| Metric            | ADC vs.   |   Observed Diff |   t-statistic |      p-value | Reject H0   |
|-------------------|-----------|-----------------|---------------|--------------|-------------|
| kills             | Top       |        1.76885  |      14.7841  | 4.86354e-47  | True        |
| kills             | Jungle    |        1.5873   |      12.8773  | 1.02506e-36  | True        |
| kills             | Mid       |        0.941468 |       7.25132 | 2.95372e-13  | True        |
| damagetochampions | Top       |     6435.1      |      15.9264  | 6.1304e-54   | True        |
| damagetochampions | Jungle    |     9880.05     |      25.2273  | 1.50143e-120 | True        |
| damagetochampions | Mid       |     1528.65     |       3.53261 | 0.000210439  | True        |

</div>

The results indicate that in this specific sample of data, ADCs have a significantly higher average number of kills than Top, Jungle, and Mid laners, with observed differences of approximately 1.77, 1.59, and 0.94 kills, respectively, and all tests yielding p-values far below 0.05. Additionally, in this specific sample of data, ADCs deal substantially more damage to champions compared to these roles, with differences of about 6,435 (Top), 9,880 (Jungle), and 1,529 (Mid), again with highly significant p-values. Overall, the null hypothesis is rejected in all comparisons, supporting the conclusion that ADCs outperform these roles in both kills and damage in this dataset of tier 1 professional League of Legends matches.

## Framing a Prediction Problem

As a result of the last portion of this project, I recognize that in this specific sample, it seems apparent that ADC has higher kills and damage dealt to champions on average than other roles. Thus, this project aims to develop a binary classification model that predicts whether a player is an ADC on a few in-game metrics. The response variable, `adc_target` is defined as 1 if the player's role is "Bot" (ADC) and 0 otherwise, because ADCs are hypothesized to have higher kills and damage compared to other roles.

The model is developed using four key numerical features: `kills`, `deaths`, `assists`, and `damagetochampions`. The features kills and damagetochampions were variables I examined thoroughly during the hypothesis tests, while deaths and assists serve as closely related performance indicators. Therefore, these are the only 4 variables to be used in the creation of the baseline model. Below is the head of dataframe to be used for the binary classification problem.

<div class="table-wrapper" markdown="block">

|   kills |   deaths |   assists |   damagetochampions |   adc_target |
|---------|----------|-----------|---------------------|--------------|
|       4 |        0 |         3 |                9830 |            0 |
|       4 |        0 |         5 |               10013 |            0 |
|       5 |        1 |         8 |               37571 |            0 |
|       3 |        2 |         6 |               15603 |            1 |
|       1 |        2 |        11 |                6292 |            0 |

</div>

Model performance is evaluated using both accuracy and F1-score. Given that the dataset is imbalanced - where our desired response variable is incredibly underrepresented relative to others - the F1-score is particularly useful as it offers a balanced measure of precision and recall that accuracy might not be able to quite grasp.

## Baseline Model

The model is a binary classifier built using a scikit‑learn Pipeline that integrates preprocessing with a RandomForestClassifier. The features included in the model are four quantitative variables - `kills`, `deaths`, `assists`, and `damagetochampions`, which capture various aspects of a player's in-game performance. These features are scaled using StandardScaler to normalize their distributions, and no additional encoding was necessary since all the features used are numeric. The target variable, `adc_target`, is binary (1 if the player's position is "Bot" and 0 otherwise) and was derived from the nominal `position` column - the transformation makes it a binary response variable. Below is the performance of the model

|           |          0 |          1 |   accuracy |   macro avg |   weighted avg |
|:----------|-----------:|-----------:|-----------:|------------:|---------------:|
| precision |   0.829909 |   0.401515 |    0.77381 |    0.615712 |       0.74406  |
| recall    |   0.901985 |   0.262376 |    0.77381 |    0.582181 |       0.77381  |
| f1-score  |   0.864447 |   0.317365 |    0.77381 |    0.590906 |       0.754814 |
| support   | 806        | 202        |    0.77381 | 1008        |    1008        |

The overall accuracy of the baseline model is 77%, and the macro-averaged F1 score is 59%. These values are not satisfactory, given that the distribution of adc_target is imbalanced with far fewer ADC (Bot) instances compared to non-ADC, and yet the model struggles to accurately identify the minority class. These initial results indicate that further refinement is necessary to improve the model's ability to accurately classify ADC players. The next step is to incorporate additional in-game metrics and perform model tuning to determine the optimal parameters that will boost the classifier's accuracy in identifying ADC players.

## Final Model
The final model incorporates seven numerical features: `kills`, `deaths`, `assists`, `damagetochampions`, `goldspent`, `total_cs`, and `firstbloodkill`. In addition to the core performance metrics, the extra features `goldspent`, `total_cs`, and `firstbloodkill` all capture crucial aspects of ADC gameplay. Goldspent is vital because ADCs scale with their item purchases, and as shown by the earlier interesting aggregate, total_cs is typically highest for ADCs as it reflects their farming efficiency and ADCs as a role have the most total first blood kills. These features were chosen because they collectively reflect the economic and performance dynamics unique to ADCs. The addition of these variables will increase the predictive power of our model and as a result the performance accuracy. 

Like the baseline model, the final model is also a RandomForestClassifier. Once again, all numerical variables, except `firstbloodkill`, are standardized using a StandardScaler to make sure that they are on a comparable scale, since the values of these numerical features tend to increase as the length of the game increases. On the contrary, `firstbloodkill` being a binary variable, is simply passed through the pipeline unchanged, in order to keep the binary information the column holds.

Hyperparameters were optimized using GridSearchCV with 5-fold cross-validation and macro-averaged F1 as the scoring metric. The grid search evaluated different values for n_estimators, max_depth, class_weight, min_samples_split, and min_samples_leaf. The best performing configuration found was with the following parameters:
- **n_estimators = 200**
- **max_depth = 15,**
- **class_weight = "balanced_subsample"**
- **min_samples_split = 4**
- **min_samples_leaf = 3**


Discovering the most optimal parameters, the pipeline was re-run, and the results of the final model are below

|           |          0 |          1 |   accuracy |   macro avg |   weighted avg |
|:----------|-----------:|-----------:|-----------:|------------:|---------------:|
| precision |   0.910039 |   0.551867 |   0.824405 |    0.730953 |       0.838263 |
| recall    |   0.866005 |   0.658416 |   0.824405 |    0.76221  |       0.824405 |
| f1-score  |   0.887476 |   0.600451 |   0.824405 |    0.743964 |       0.829957 |
| support   | 806        | 202        |   0.824405 | 1008        |    1008        |

Compared to the baseline model that used only the initial four features, the final model — with an expanded feature set and optimized hyperparameters — achieves an overall **accuracy** of about **82.4%** and a **macro-averaged F1 score** of roughly **74.4%.** These improvements are impressive given that the numerical features `kills`, `deaths`, `assists`, `damagetochampions`, `goldspent`, `total_cs` span a wide range of values, capturing diverse aspects of in-game performance. The enhanced model demonstrates better balance across classes and improved ability to identify ADC players in a complex, real-world competitive environment.

## Fairness Analysis

With the completion of the final predictive model, it is now necessary to assess whether the model is fair among two different groups. The two groups chosen here are players with less than or equal to 275 total creep score corresponding to Group X, and players with greater than 275 total creep score corresponding to Group Y. Now, the hypotheses:

- Fairness Hypothesis:
    - Null Hypothesis (H₀): The macro-averaged F1 score difference between Group X and Group Y is zero (i.e., the model is fair). Any observed difference is due to random chance.
    - Alternative Hypothesis (H₁): The macro-averaged F1 score difference between Group X and Group Y is not zero (i.e., the model is unfair). There is a real difference in macro F1 score performance for these groups.
- Significance Level: α = 0.05
    - If the p-value from our permutation test is less than 0.05, we reject H₀ and conclude the model is unfair.
    - If the p-value is greater than or equal to 0.05, we fail to reject H₀ and conclude there is no evidence of unfairness at this threshold.
- Test Statistic:-
    - Macro-averaged F1 score difference between Group X and Group Y

<iframe
  src = "/assets/macro-final-perm.html"
  width = "600"
  height = "600"
  frameborder="2"
></iframe>

To clarify further, a permutation test with 10,000 iterations was conducted by shuffling the group labels and computing the empirical distribution of the macro-averaged F1 score differences. The evaluation metric is the macro-averaged F1 score, and the test statistic is the observed difference in macro F1 scores between these two groups. The null hypothesis (H₀) states that there is no difference in macro F1 score between Group X and Group Y, while the alternative hypothesis (H₁) asserts that there is a difference. With a significance level of α = 0.05, the observed macro F1 difference was 0.0601 and the p-value was 0.0958. Since the p-value exceeds 0.05, we fail to reject the null hypothesis, indicating that there is no statistically significant evidence of unfairness based on total_cs.