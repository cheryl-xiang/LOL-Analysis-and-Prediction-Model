# League of Legends Vision Control and Support Role Analysis
**UCSD DSC80 Final Project**

League of Legends Vision Control and Support Role Analysis is a comprehensive data science project conducted at UCSD. The project includes in-depth  analysis, starting from EDA to hypothesis testing, development of baseline models, and a fairness analysis. 

Author: Cheryl Xiang

## Introduction
**League of Legends** is a popular multiplayer online battle arena (MOBA) game developed by Riot Games, where two teams of five players compete to destroy the opposing team's Nexus, a core building located in their respective bases. The game combines strategy, teamwork, and skill, featuring a wide roster of champions, each with unique abilities.

The dataset I will be working with is from 	[Oracle's Elixir](https://oracleselixir.com/tools/downloads), which contains data on League of Legend Competitive Matches from 2014 to the 2025. For this project, I will be focusing on data from 2022 since it has the most recorded games of of recent years. The LOL landscape changes significantly from year to year, so I have chosen not to combine data across multiple years.

In this project, I will investigate the relationship between vision control/placing wards and winning a match. While there are many moving components to a LOL match, one that is sometimes overlooked is **vision control**. Vision control refers to the strategic management of "vision" or the ability to see and monitor parts of the game map that are not directly in a player's line of sight. This is achieved for the most part through placing your own wards as well as clearing enemy wards. Wards are used to provide vision in specific areas, allowing a team to detect enemy movements, objectives, or potential threats, which allows teams to make informed decisions about key areas of the map and help teams to secure important match objectives. 

Through my exploration of this data set, I aim to answer the question: **How important is vision control in a League of Legends match?**

### DataFrame Columns 

The original data set has `149232` rows, and `123` columns from `12549` recorded matches. For the purposes of this analysis, I will be focusing on the columns listed below:

- `gameid`: (str) The 'gameid' column includes a unique identifier for each game in the dataset. 

- `position`: (str) The 'position' column specifies the role played by each individual player. They are separated into 'top,' 'jng (jungle),' 'mid (middle),' 'bot (bottom),' and 'sup (support).'

- `win`: (bool) The 'win' column indicates the outcome of the match each for player. True indicates that the player's team won, and False indicates that they lost.

- `kills`: (int) The 'kills' column contains the number of enemy champions (players) a player eliminated during the match. 

- `deaths`: (int) The 'deaths' column records the number of times a player was eliminated by enemy champions (players). 

- `assists`: (int) The 'assists' column records the number of assists a player has made. An assist occurs when a player contributes to eliminating an enemy champion without landing the final blow (kill). This includes actions like dealing damage, providing crowd control, or setting up a kill for an ally player

- `wardsplaced`: (int) The 'wardsplaced' column indicates the number of wards a player has placed on the map throughout a match. 

- `wardscleared`: (int) The 'wardscleared' column records the number of enemy wards the player has cleared during the game. Clearing wards helps deny vision to the opposing team, reducing their map awareness.

- `visionscore`: (int) The 'visionscore' column represents a player's contribution to vision on the map. It includes both placing and clearing wards, as well as providing vision through abilities or items. The higher the score, the more a player has contributed to vision control during the match.

- `damagetochampions`: (int) The 'damagetochampions' column tracks the total amount of damage a player has dealt to enemy champions (players) throughout the game.

- `monsterkills`: (int) The 'monsterkills' column records the number of neutral monsters the player has killed. This includes objectives like Dragons, Rift Heralds, Barons, and jungle camps. Killing monsters provides gold, experience.

- `split`: (str) The 'split' column indicates the split or stage of the competitive season in which the data was collected.

- `teamid`: (str) The 'teamid' column contains the identifier or name of the team that the player is part of.

        


## Data Cleaning and Exploratory Data Analysis
### Data Cleaning

To prepare the data for analysis, only relevant columns were kept in the dataset:  `gameid`, `position`, `win`, `kills`, `deaths`, `assists`, `wardsplaced`, `wardscleared`, `visionscore`, `damagetochampions`, `monsterkills`, `split`, `teamid`. 

Furthermore, the I renamed the `win` column, originally called `result` and converted its original binary values of 1 (indicating win) and 0 (indicating lose) to boolean values for clarity. In order to preserve consistency, I also converted the `wardsplaced`, `wardscleared`, `visionscore`, `damagetochampions`, `monsterkills` columns, which originally contained whole number floats into integers. 

Within the League of Legend community, the act of removing enemy wards from the map is most often referred to as "clearing wards" rather than "killing wards," so I renamed the `wardskilled` column to `wardscleared` in order to reflect this convention.

The original data set includes rows with team summary statistics for each match. I will be looking primarily at individual players statisitics, so these rows are not needed, and were filtered out based on the `position` column which contained the string "team" rather any of the five playable positions for team rows. For any analysis that needs to be performed on a team level, team data can be recovered by aggregating the cleaned DataFrame using the `gameid` and `teamid` columns. 

The resulting cleaned DataFrame has `125490` rows, `13` columns. 

Below is head of my cleaned DataFrame:


| gameid                | position   | win   |   kills |   deaths |   assists |   wardsplaced |   wardscleared |   visionscore |   damagetochampions |   monsterkills | split   | teamid                                  |
|:----------------------|:-----------|:------|--------:|---------:|----------:|--------------:|---------------:|--------------:|--------------------:|---------------:|:--------|:----------------------------------------|
| ESPORTSTMNT01_2690210 | top        | False |       2 |        3 |         2 |             8 |              6 |            26 |               15768 |             11 | Spring  | oe:team:733ebb9dbf22a401c0127a0c80193ca |
| ESPORTSTMNT01_2690210 | jng        | False |       2 |        5 |         6 |             6 |             18 |            48 |               11765 |            115 | Spring  | oe:team:733ebb9dbf22a401c0127a0c80193ca |
| ESPORTSTMNT01_2690210 | mid        | False |       2 |        2 |         3 |            19 |              7 |            29 |               14258 |             16 | Spring  | oe:team:733ebb9dbf22a401c0127a0c80193ca |
| ESPORTSTMNT01_2690210 | bot        | False |       2 |        4 |         2 |            12 |              6 |            25 |               11106 |             18 | Spring  | oe:team:733ebb9dbf22a401c0127a0c80193ca |
| ESPORTSTMNT01_2690210 | sup        | False |       1 |        5 |         6 |            29 |             14 |            69 |                3663 |              0 | Spring  | oe:team:733ebb9dbf22a401c0127a0c80193ca |

        


### Univariate Analysis
For my initial EDA, I performed Univariate Analysis on some of the columns of my DataFrame.  


<iframe
  src="assets/UnivariateWards.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Above, we can see the distribution of `wardsplaced` by players across all matches. The histograms is skewed right, with a majority of players placing between 5 and 20 wards and 95 players placing at least 100 wards.  

        


<iframe
  src="assets/UnivariateVisionScore.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The distribution of `visionscore` across all players is also skewed right, but appears to be closer to normal than the distribution of wards placed by players as the right side of the histogram tapers off much more gradually. According to this distribution, around 44% players had a vision score between 20 and 39.  

        


### Bivariate Analysis
I also performed Bivariate Analysis on some of the columns of my DataFrame to look for any possiple patterns or relationships. 


<iframe
  src="assets/BivariateVisionScore.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Looking at the distributions of `visionscore` for winning and losing players, we can see that the median for winning players (41) is higher than the median for losing players (35). This suggests that a higher `visionscore` may be associated with winning a match and that vision control could be an important aspect of a match when it comes to securing a win.

         


<iframe
  src="assets/BivariateVSPosition.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

From this plot, we can clearly see that the median `visionscore` for players in the support role (77) is noticeably higher than the median scores of other positions, which range from 29 to 41. This suggests that the primary objectives of a support player often revolve around vision control. As such, support players are likely more involved in placing and clearing wards, as well as utilizing champion abilities that enhance vision during the match.


         

### Interesting Aggregates

Below is an aggregated DataFrame looking at the averages vision related statistics grouped by the players' `position`

| position   |   wardsplaced |   wardscleared |   visionscore |
|:-----------|--------------:|---------------:|--------------:|
| bot        |       13.7045 |       10.1866  |       37.8401 |
| jng        |       11.4096 |       11.1984  |       43.1106 |
| mid        |       13.112  |        6.60225 |       33.7589 |
| sup        |       46.7577 |       10.1764  |       79.2108 |
| top        |       12.0445 |        5.44186 |       30.8509 |

We can see that although the players in the bottom and and jungle position clear a comparable number of wards to the support postion on average, the support player places significantly more wards (over three times as many) on average and has an mean vision score nearly 36 points higher than the next highest position (jungle). This further highlights the importance of vision and vision control for the support player's role within their team and within a match. The significantly higher number of wards placed by support players reflects the unique responsibility they have in maintaining vision throughout the match.

          


Here is another aggregated DataFrame with the averages of vision related statistics grouped by winning and losing players.

| win   |   wardsplaced |   wardscleared |   visionscore |
|:------|--------------:|---------------:|--------------:|
| False |       19.0455 |        8.29616 |       42.3038 |
| True  |       19.7659 |        9.14608 |       47.6047 |

Based on the DataFrame, it appears that teams that win tend to have slightly higher ward placement and clearance averages, as well as a noticeably higher vision score. The difference in the average number of wards placed between wins and losses is relatively small, with winning teams placing slightly more wards on average. This suggests that while placing wards is important, the overall number of wards placed may not be the sole determining factor for winning or losing. Other elements like ward positioning and map awareness may also contribute to the result. Winning teams cleared slightly more wards than losing teams. This could imply that the winning team is more proactive in denying vision and clearing enemy wards, possibly giving them a better sense of map control, reducing the enemy's vision, and preventing the opposing team from setting up crucial wards. The vision score of winning teams is notably higher (by about 5.3 points) compared to losing teams. A higher vision score can indicate that the winning team is more actively controlling vision on the map.


      

## Assessment of Missingness

### NMAR Analysis

Looking at the 13 columns in my subset of the original data set, I can see several columns with missing values. 

There are also `35210` missing values in the `split` column. I believe that these values may be NMAR (Not Missing at Random) because it is possible that some matches are **not played within a specific split**. Matches within League of Legends can occur outside of the specific splits through events like Playoffs, Worlds, Mid-Season Invitational (MSI), and regional special events or off-season tournaments. This means that `split` could be systematically missing for matches that should not have a split which implies that the reason for missingness is intrinsic to the missing value itself.

Some additional data that could be obtained to make the `split` column MAR (Missing ar Random) is `match_type` which would specify the context of the match whether it be a regular split match or part of an event that occurs outside of splits such as Playoffs, Worlds, Mid-Season Invitational (MSI), and regional special events. If we find that the missingness of `split` occurs for certain match types, then we can conclude that this column's missingness depends on `match_type`.

         

           

### Missingness Dependency

In this part, I will be running permutation tests to check if the missingness of values in `teamid` is dependent on another column in my DataFrame. For both tests, my test statistic will be Total Variation Distance (TVD) with a significance level of `0.05`.

First, I will be testing if the missingness of `teamid` is dependent on `split` using the following hypotheses:

**Null Hypothesis:** The distribution of `split` when `teamid` is missing is the same as the distribution of `split` when `teamid` is not missing.

**Alternate Hypothesis:** The distribution of `split` when `teamid` is missing is ***NOT** the same as the distribution of `split` when `teamid` is not missing.

Here is the distribution of `split` when `teamid` is and is not missing: 

| split   | Missing ID =  False | Missing ID = True |
|:--------|-----------:|---------:|
| 2022    | 0.00221791 | 0        |
| Champ 1 | 0.0110895  | 0        |
| Champ 2 | 0.0140837  | 0        |
| Closing | 0.00998059 | 0        |
| Fall    | 0.0128639  | 0        |
| Opening | 0.00998059 | 0        |
| Pro-Am  | 0.00798447 | 0        |
| Regular | 0.00421403 | 0        |
| Split 1 | 0.0659274  | 0.809524 |
| Split 2 | 0.0467979  | 0        |
| Spring  | 0.389021   | 0.190476 |
| Summer  | 0.403216   | 0        |
| Winter  | 0.0226227  | 0        |

     
      
Here is the Empirical Distribution of the TVD between the two columns:

<iframe
  src="assets/TeamSplitTVD.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

  
Following my permutation tests, I found an observed TVD of `0.743589134206336` and a p-value of `0.0`. This p-value is lower than our significance level of `0.05`, so we reject the null hypothesis.

   


I will also be testing if the missingness of `teamid` depends on `position` using the folowing hypotheses:

**Null Hypothesis:** The distribution of `position` when `teamid` is missing is the same as the distribution of `position` when `teamid` is not missing.

**Alternate Hypothesis:** The distribution of `position` when `teamid` is missing is ***NOT** the same as the distribution of `position` when `teamid` is not missing.


Here is the distribution of `position` when `teamid` is and is not missing: 

| position   | Missing ID =  False | Missing ID = True |
|:-----------|--------:|-------:|
| bot        |     0.2 |    0.2 |
| jng        |     0.2 |    0.2 |
| mid        |     0.2 |    0.2 |
| sup        |     0.2 |    0.2 |
| top        |     0.2 |    0.2 |

     
      
Here is the Empirical Distribution of the TVD between the two columns:

<iframe
  src="assets/TeamPositionTVD.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

  
This time, I found an observed TVD of `0.0` and a p-value of `1.0`. This p-value is much higher than our significance level of `0.05`, so we fail to reject the null hypothesis.

  



## Hypothesis Testing

For this hypothesis test, I will be testing the significance of `visionscore` as a factor when it comes to winning a League of Legends match by assessing if there is a significant difference in the distribution of combined `visionscore` between teams who did and did not win. Through this test, we can further understand the relationship between Vision Score/Vision Control and winning a match. 


**Null Hypothesis:** The distribution of combined `visionscore` for teams who won is the same for teams who did not win

**Alternate Hypothesis**: The distribution of combined `visionscore` for teams who won is **NOT** the same for teams who did not win

**Test Statistic:** The difference in means of combined `visionscore` for teams who won and teams who lost (Win - Lose)

**Significance Level:** 0.05

  
The null and alternative hypotheses were selected to directly test the impact of vision control on match outcomes.

I will be using a simplified DataFrame for my analysis with a column containing the combined `visionscore` of each team in every match and a column indicating if they won or lost the match. Here is the head of the simplified DataFrame.


| win   |   visionscore_combined |
|:------|-----------------------:|
| True  |                    339 |
| True  |                    317 |
| False |                    312 |
| False |                    343 |
| False |                    226 |


Below is the observed mean combined `visionscore` of winning and losing teams. There is an observed difference in means of `26.139580309728075`.

| win   |   visionscore_combined |
|:------|-----------------------:|
| False |                212.07  |
| True  |                238.209 |


<iframe
  src="assets/VSDiffMeans.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Above we can see the empirical distribution of the difference in means of combined `visionscore` (win - lose) obtained from our permutation test. According to the plot, the majority of the differences are centered around `0`. Additionally, this hypothesis test resulted in a p-value of `0.0`. As such, we reject our null hypothesis that the distribution of combined `visionscore` for teams who won is the same for teams who did not win. Specifically, teams that won tended to have a higher combined `visionscore`, suggesting that vision control may be a key factor in achieving victory in League of Legends.


## Framing a Prediction Problem

Throughout this analysis, I noticed some patterns in the columns for each of the five positions played in League of Legends. In particular, I noticed that many of the vision related statistics (including `wardsplaced`, and `visionscore`) seem to be skewed higher for the support role compared to other roles such as jungle or bottom. Based on this, I want to answer the following prediction problem: **Can I predict whether or not a player's position was support based on their game statistics?**


In order to do this, I will build a binary classifier model with a One-Hot Encoded column `position_sup` derived from the `position` column in my DataFrame with `1` indicating that the player was in the support role and `0` indicating that they were some other role. This column will be my response variable, as I will be predicting whether or not person played support based on their match statistics.


The metric I have chosen to evaluate my model is **precision**. I want my model to correctly identify players who are actually in the support role and avoid incorrectly labeling non-support players as support. Precision helps focus on minimizing these false positives. Additionally, there is an imbalance in the data between players who play support and those who play other roles, as only 1 in 5 players on a team will be in the suppport role. If I evaluated my model based on accuracy, I could easily achieve a high accuracy score by simply predicting the majority class (non-support players) most of the time. However, this would ignore the minority class (support players) and fail to correctly identify them. Furthermore, while the F1-score focuses equally on precision recall, my main focus is avoiding incorrectly identifying non-support players as support, so I have chosen to use precision rather than working to simultaneously maximize recall.

To avoid overfitting my model and allow for evaluation of my model, I will be splitting my dataset into two part: **25%** testing data and **75%** training data. For both my baseline and final model, I will be using the same split of seen and unseen data so that I can compare precision between the two models.


All the columns I will be using for my model are statistics that are collected throughout the match. We are predicting if someone played support after the match, so they will be known at the time of prediction. 

    


## Baseline Model

For my baseline model, I used a **Random Forest Classifier**, with the following four features: `visionscore`, `wardsplaced`, `kills`, and `assists`. Based on my exploration of the data set, I have noticed that support players tend to have higher values in the `visionscore`, `wardsplaced`, and `assists` columns compared to the four other positions in a League of Legends team. Conversely, they also tend to have a lower `kills` count compared to other positions. These patterns suggest that these columns would be good predictors of whether or not a player's position was support. All four of the selected feature are numerical. Match length times can vary greatly, which can lead to skewed distributions for each feature, so I used a StandardScaler Transformer to standardize the features.

After fitting, this baseline model earned a precision score of `0.9322591252877342`. This high precision indicates that, out of all the instances the model predicted as "positive" (player is support), a very high proportion were actually correct. Given this, I believe the model is performing well, especially in terms of minimizing false positives.

  

## Final Model

To improve my model, I first added two new numerical features: `damagetochampions` and `monsterkills`. Both of these columns are numerical, so I will be using the StandardScaler transformer on them. 

In League of Legends matches, the role of support does not play a big part in dealing damage or making kills, but rather focuses on assisting other players on your team in terms of vision, crowd control, and utility (such as heals and shields). As a result, support players tend to have lower damage to champions (the opposing team's players) and monster kills, since dealing damage is not their focus. Players in support roles should have lower numbers in these DataFrame columns, so I believe they would make suitable features for my improved model.

I also used `GridSearchCV` to tune the following hyperparameters for my final Random Forest Classifier model: max_depth, min_samples_split, and criterion. 

I chose to tune max depth, because it is an important factor when it comes to overfitting or underfitting models. A bigger max depth results in less bias and more variance, while a bigger 'min_samples_split' results in more bias and less variance, so I also want to tune this hyperparameter in order to balance the two out. Finally, I am looking at criterion to improve the overall quality of the splits.

After verifying that **entropy** was the best criterion for my model, I focused on the two numerical hyperparameters. In each iteration of `GridSearchCV`, I selected 3 values for both max_depth and min_samples_split. From there, I performed `GridSearchCV` within the range of the resulting ideal hyperparameter until I narrowed my range enough for the values to stop changing. After this process, the best hyperparameters turned out to be: criterion = **entropy**, max_depth = **12**, and min_samples_split = **10**.

  
My final model achieved a precision score of `0.9790356394129979`, which is around a **5%** improvement from my baseline model's precision score. This significant improvement in precision suggests that my final model is much better at correctly identifying support players, with a lower rate of false positives compared to the baseline model.

  
Here is the **confusion matrix** for my final model:


<iframe
  src="assets/FinalConfusionMatrix.png"
  width="800"
  height="600"
  frameborder="0"
></iframe>

  
    

## Fairness Analysis


For this section, I am assessing the fairness of my model among different groups. I am trying to answer the question: **“does my model perform worse for players  withless than 12000 `damagetochampions` than it does for players with at least 12000 `damagetochampions`?”** To answer this question, I will perform a permutation test and examine the difference in precision between the two groups.

**Group X:** Players with with less than 12000 `damagetochampions`

**Group Y:** Players with greater than or equal to 12000 `damagetochampions`

- **Null Hypothesis:** The classifier's precision is the same for both players with over and under 12000 `damagetochampions`, and any differences are due to chance.

- **Alternative Hypothesis:** The classifier's precision is higher for players with at least 12000 `damagetochampions`.

- **Test Statistic:** Difference in Precision (X - Y)

- **Significance Level:** 0.05

- **Evaluation Metric:** Precision


   

Here is the empirical distribution of the difference in precision (X - Y)

<iframe
  src="assets/DiffPrecision.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


This permutation test yielded an observed difference of `0.14087139876292287` and a p-value of `0.0`, which is less than my significance level of `0.05`. As a result, I reject the null hypothesis that the classifier's precision is the same for both players with over and under 12000 `damagetochampions`, and any differences are due to chance. Based on this, my model seems to be unfair, achieving a higher precision for players with at least 12000 `damagetochampions`.