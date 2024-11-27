# LeagueOfLegendsDataModeling
This is a DSC80 Final Project at UCSD that intends to utilize data science skills to analyze and create model predictions based on professional League of Legends match data

## Introduction
The data that I am using is League of Legends (LOL) match information, from the years 2014 to 2024.  There are 922080 rows in the data, each representing a player or team for some LOL game. There is always 12 rows of data per match, representing the 2 teams, and 5 players on each. From this, we also know that there is 76840 different matches in our entire dataset.
There are 131 columns of data, most of them representing information about a team or player during the match. There is informational data about the match, such as the `date`, and names of the team, tournament, or player, including the many rows of game statistics during the match.

Obviously because this has to do with a competition, the obvious questions are all related to performance, who is the best performing player, who is the best performing teams and in what years? What are the most important statistics to look at to figure out how to best define the performance of these categories?
**How can we use data from historical League of Legends matches to predict their outcomes?**
### LOL Overview
There is a lot of interesting data in this dataset. It is really important that we first understand the data that we have. First it is important to understand that there is really three different groupings that the data is pertaining to, the match, the teams, and each individuals. There is categorical data as well as quantitative data for what happened in each match.

Here is an overview of important game values:
- **gold**:  Gold for purchasing items and powerups
- **xp**: Experience Points unlock new/improved abilities
- **cs**: Creep score is number of minions/monsters killed
- **vs**: Vision Score represents number of wards (monsters on your side) killed/spawned

Here is an overview of the different games statistics in this dataset:
- Time based statistics: We have access to kills, deaths, assists, gold, xp, and cs at the 10 and 15 minute mark for the data. This can be used to show which players or teams have made the most progress at this time of the game.
- Other stats: 
	- kills, deaths, assists
	- more detailed gold, xp, cs, vs stats
	- damage recieved/delt stats
- Monsters: Neutral monsters (drakes, dragons, inhibitors, etc. ) in the game that can be killed for gold/xp/powerups
- Match/player information: The league, player/team name, type of game, position, champion

## Data Preparation
### Data Cleaning
There is a lot of missing data. For many columns such as the 'pick'/'ban' columns we can fill no choice with 'none' since it is a string selection of a character, and no selection will be 'none' anyways. Other 'first xyz' and statistics denoting the number of kills for a certain monster, since if they were not available or not collected during the match information that means there is 0 kills. We can also start to form some aggregations to reduce the number of columns while preserving the quality of the data. Values such as the multiple kill categories can be aggregated into a single 'multikill' column, and we can create a total neutral monsters killed columns as well.
### Exploratory Analysis
Here is two boxplots showing the distributions of the xpat10 and xpat15 columns this shows us how the distribution of this value seems to move up as the game time increases.
<iframe
  src=".\\plots\\boxplot.png"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src=".\\plots\\boxplot2.png"
  width="800"
  height="600"
  frameborder="0"
></iframe>
This plot shows us a logorithmic bar plot that displays the distribution of neutral monster kills. The vast majority only totals to one, but when we compare with result, a single value is unlikely to tell us much, so we should utilize an aggregate measure.
<iframe
  src=".\\plots\\neutralkills.png"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This other plot shows us creep score compared with number of minions killed. However there is clear grouping in the data, showing us there are multiple other variables contributing to creep score.

<iframe
  src=".\\plots\\creep.png"
  width="800"
  height="600"
  frameborder="0"
></iframe>
This scatterplot is colored based on wether a team wins or loses. It uses xp and creep score during early game, to see how these relate to win rates. We can clearly see that there is an overlap, but clear grouping where shorter games and higher kills seem to indicate a win, whereas the longer and lower kills and up losing.
<iframe
  src = ".\\plots\\xp.png"
  width="800"
  height="600"
  frameborder="0"
></iframe>
## Data Missingness
There is no NMAR data in the dataset.
Lets choose the xpat10 column to determine missingness, which is apparent in ~10% of the columns. This along with the rest of the time-based statistics, are missing in many of the values. By performing permutation testing against each of the other columns, we can create a list of p-values to see if there is an association with its missingness.
From our data, the column year is associated with missingness. This makes sense, because by plotting different values, we can see the distribution of games per year is different from the distribution of games with missing time statistics per year. There is also perfect association with the other time-statistics values. 
Other random statistical columns, for example the netural monster columns are not associated with this column's missingness. 
## Hypothesis Test
One permutation test that we can perform is to see whether or not the neutral monster kills actually have an affect on the outcome of the match.
$H_0$: The distribution of neutral monster kills among matches resulting in a win is different from the distribution of those ending in a loss
$H_A$: There is no difference between the distributions.
For this problem we should use the K-S statistic. We are dealing with match data for a spectrum of values, so comparing distributions makes the most sense for this test. For this data, we should make the alpha value to compare a standard 0.05. We can see based on our observed statistic of 0.23, that it is way higher than the stats from our permutation tests. We end with a resulting p-value of 0.0, accepting the null hypothesis that the distributions are different.
## Prediction Problem
Based of our previous analyses, we know that KDE performance, early game advantages, and controlling tower/monster areas are all significant factors when contributing towards whether a team wins or not. So we will suppose, that based on these metrics that we will define, we are able to predict the outcome of the match.
