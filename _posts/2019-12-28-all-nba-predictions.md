---
layout: post
title: All-NBA Team Predictions
subtitle: Predicting All-NBA Team Selections After a Given Season
gh-repo: dktlee/dylanlee/all-nba_predictions
gh-badge: [star, fork, follow]
tags: [side-project]
comments: true
---

The All-NBA Team is an honour bestowed on the best players in the league after every season. The team is composed of three 5-man lineups -- a first, second, and third team. Voting is conducted by a panel of sportswritters and broadcasters around the league. 

Every year, towards the end of another NBA season, the argument surrounding who's going to make the All-NBA Team is always up for discussion. There are seasons where a player's performance should guarantee him a spot on the roster by unanimous decsion (the Greak Freak in the 2018-19 campaign), but there are also cases where the decision is not so straightforward and a player might end up getting snubbed (*ahem* Bradley Beal). 

To assist me in the debate, I created a model to help me predict who will make the All-NBA Team based on their performance after a given season. Going one step further, I also forecasted players' stats to determine how many *more* All-NBA Team selections a certain player has remaining in their career. 

## Data Collection

Data was collected from the fan-beloved third-party Basketball Reference website. Basketball Reference has a comprehensive database of historical player data and the site allows me to directly download CSV files. I only considered All-NBA selections from the 1999-00 season and onwards, so I collected data for all players from the beginning of the 1999-00 season up until the end of the 2018-19 season. The raw data consists of player season total stats and is well formatted so only minor data processing is required. I also combined the dataset with information on a player's draft year to calculate how long he has been in the NBA, and whether or not a player made the All-NBA Team for that given season. 

![Data: Player Totals](/img/big-imgs/bball_ref_2018_19_player_totals.png)
*Basketball Reference: Player Totals for 2018-19 season*

## Data Processing

The dataset is nice and consistantly formated for all seasons, but there are a couple minor subtleties that I needed to sort out. 

First, I removed any asterisks from player names if found. This is rather straightforward since I can replace any instances of a '*' with an empty string ''. 

Second, the formatting of a player's name needed to be fixed. The raw data comes in the format of 'Chris Paul\paulch01' when really we just want 'Chris Paul'. This is to handle the odd chance that two players have the same exact name. I solved this issue with some simple text cleaning in R:
```R
# treat names at strings
df$Player <- as.character(df$Player)
  # split name variable by [\\]
  names <- strsplit(as.character(df$Player), "[\\]")
  # reassign the clean name
  for (i in 1:dim(df)[1]) {
    name <- names[[i]][1]
    df$Player[i] <- name
  }
```

Last, I noticed that there are rows of data for players' total (TOT) stats that aggregated their single season stats for all the teams they played for that year (i.e. when a player gets traded in the middle of the season). I decided to remove these *TOT* rows and consider each individual team a player played for in a given season as independent data points. This did not seem to be a problem because there were only two instances of this occuring in the last 20 seasons where a player was traded mid-season and still wound up making the All-NBA Team (Chauncy Billups traded from Detroit to Denver in the 2008-09 season and Dikembe Mutumbo traded from Atlanta to Philadelphia in the 2000-01).

From here, I looped through each year in our dataset, loaded in the CSV for that year, cleaned and processed the data, and finally merged the results into one larger dataset. The fnal dataset consists of the players' total stats (in the format shown above), the players' draft year, and whether or not that player made the All-NBA Team, for every season. 

## Data Analysis

Before modelling, I did some summary analysis on the large dataset. First, a simple `summary()` in R with out total player stats for all seasons: 

![Data: Player Totals Summary](/img/big-imgs/data_summary_player_totals.png)
*Data Summary: Player Totals from 1999 - 2019*

As expected, the data shows that there are a large range of values for any given statistic, so I decided to normalize each statistic for each season. Simply, for each season we have in the dataset, I divided each stat for every player by the given season's max. 

Also, note that we have nearly 8300 observations with 35 columns each. So to get a sense of which columns might serve as useful predictors I created a correlation plot to see which stats correlated most with making the All-NBA Team. After that, I modelled the distribution of each statistic, comparing stats of players who made the All-NBA-Team vs players who did not. Let's take a look at these results: 

![Correlation Plot: Player Totals vs All-NBA](/img/big-imgs/correlation_plot.png)
*Correlation Plot: Player Totals vs All-NBA*

The correlation plot shows slightly positive correlation for making the All-NBA Team with Games Started, Field Goals Made, Field Goals Attempted, Free Throws Made, Free Throws Attempted, Rebounds, Assists, Steals, Blocks, and Turnovers, and even stronger correlation in the scoring stats.

![Distribution Plot: Minutes Played vs All-NBA](/img/big-imgs/minutes_played.png)
*Distribution Plot: Minutes Played vs All-NBA*

By modelling the distribution of each statistic comparing ALl-NBA players against the rest of the league, I might be able to find ways to filter out data points that could potentioally dilute the model's ability to showcase elite players. I do not want to flood the model with bench / rotational players who aren't in consideration for All-NBA honours. I used Minutes Played during a season as a proxy and only considered players who play at least 1600 minutes in a season. 

![Distribution Plot: eFG% vs All-NBA](/img/big-imgs/efg.png)
*Distribution Plot: eFG% vs All-NBA*

From the data, I found that All-NBA players are performing above average in the counting stats compared to the rest of the league, which is a fairly trivial solution. However, a more interesting discovery is that the distribution of the different shooting percentages (FG%, 3P%, 2P%, eFG%, and FT%) don't vary much. Above, we see that the distribution of effective Field Goal Percentage for All-NBA players follows a very similar shape compared to the rest of the league. In fact, All-NBA players have similar distributions in all shooting percentages compared to the rest of the league, and are not actually shooting consistently better or more efficiently. 

![Filtered Correlation Plot: Player Totals vs All-NBA](/img/big-imgs/filtered_correlation_plot.png)
*Filtered Data Correlation Plot: Player Totals vs All-NBA*

After removing observations from the dataset for players who did not play more than 1600 minutes in a given season, the new correlation plot shows slightly stronger correlation for various stats. What I found interesting from this plot is that Turnovers have a slightly strong correlation, but I infer that turnovers might be a indicator for usage rates, and that All-NBA players have high usage rates, and with high usage rates comes more turnovers. 

## Modelling

