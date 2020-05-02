---
layout: post
title: Forecasting NBA Player Stats
subtitle: A Simple Projection System to forecast NBA player stats for a new season
gh-repo: dktlee/dylanlee/all-nba_predictions
gh-badge: [star, fork, follow]
tags: [side-project]
comments: true
published: false
---

Taking inspiration from <a target="_blank" href="https://www.basketball-reference.com/">Basketball Reference </a>, I implemented a Simple Projection System to forecast the stats for a given player in the NBA for a brand new season based on stats from prior seasons.

There were two main goals for this project:

First, I used this system to project players stats for their entire career by recursively iterating on previous seasons. So, in combination with my other project where I predicted All-NBA players after a given season, I calculated how many All-NBA teams different players would make in their career.

Second, I plan to use this tool to help with drafting players for the Fantasy Leagues I compete in with friends every season. Even though sites like Yahoo and ESPN already have their own projections, it would be nice to have a second source of information to draft based off.

The Simple Projection System is very similar to baseball's <a target="_blank" href="http://www.tangotiger.net/marcel/">Marcel the Monkey Forecasting System </a>. It is a basic forecasting system that uses as little intelligence as possible. And that's the allusion of the monkey. Adopting from baseball to basketball, the system uses 3 years of NBA data, with the most recent year of data weighted heavier. It regresses towards the mean. And it has an age factor.

*Disclaimer:* I would not heavily rely on this system or these results. I used these projections because it was very easy to implement on my own and it was fun working with the data.

## Methodology

Let me walk you through the method by projecting <a target="_blank" href="https://www.basketball-reference.com/players/l/lowryky01.html">Kyle Lowry's </a> assists for the 2019-20 season as an example:

![Data: Kyle Lowry Assists](/img/kyle_lowry_ast.png)
*Data for Example*



[//]: # ## Future Work
[//]: # 
[//]: # While the current model is performing well, there are still many other options I'd like to consider as next steps.
[//]: # 
[//]: # First, the most obvious change would be to use more complex models than a simple logistic regression model, such as random forest.
[//]: # 
[//]: # Second, I'd like to implement the use of other player data. Basketball Reference has information on players per game stats and advance stats that might have more power in prediction than players' total season stats.
[//]: # 
[//]: # Lastly, the ultimate goal for this project is to be able to predict the number of All-NBA selections for any given player at any point in their NBA career. I would like to estimate the total number of All-NBA selections remaining in different player's careers.
[//]: # 
[//]: # Thanks for reading. If you're interested, my entire codebase is available <a target="_blank" href="https://github.com/dktlee/dylanlee/tree/master/all-nba_predictions">here</a>.
