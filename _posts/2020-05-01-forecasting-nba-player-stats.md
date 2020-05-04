---
layout: post
title: Forecasting NBA Player Stats
subtitle: A Simple Projection System to forecast NBA player stats for a new season
gh-repo: dktlee/dylanlee/all-nba_predictions
gh-badge: [star, fork, follow]
tags: [side-project]
comments: true
published: true
---

Taking inspiration from <a target="_blank" href="https://www.basketball-reference.com/">Basketball Reference</a>, I implemented a Simple Projection System to forecast the stats for a given player in the NBA for a brand new season based on their stats from prior seasons.

There were two main goals for this project:
- I used this system to project players stats for their entire career by recursively iterating on previous seasons. So, in combination with my other <a target="_blank" href="https://dktlee.github.io/2019-12-28-all-nba-predictions/">project</a> where I predicted All-NBA players after a given season, I calculated how many All-NBA teams different players would make in their career.
- I plan to use this tool to help with drafting players in different Fantasy Leagues I compete in against friends every season. Even though sites like Yahoo and ESPN already have their own projections, it would be nice to have an additional source of information when drafting players.

The Simple Projection System is very similar to baseball's <a target="_blank" href="http://www.tangotiger.net/marcel/">Marcel the Monkey Forecasting System</a>. It is a basic forecasting system that uses as little intelligence as possible. That's the allusion of the monkey. Adopting from baseball to basketball, the system uses NBA data from the 3 prior seasons, with the most recent year of data weighted heavier. It regresses towards the mean. And it has an age factor.

*Disclaimer:* I would not heavily rely on this system or the results. I used this method because it is very easy to implement on my own and it was fun working with the data.

## Methodology

Let me walk you through the method by projecting <a target="_blank" href="https://www.basketball-reference.com/players/l/lowryky01.html">Kyle Lowry's </a> assists for the 2019-20 season as an example:

![Data: Kyle Lowry Assists](/img/kyle_lowry_ast.png)
*Example Data: Kyle Lowry's MP and AST numbers for the last 3 seasons*

**Assigning weights for the prior seasons**

Forecasting for the 2019-20 season, I used the following weights for the prior 3 seasons:
- 2018-19: `6`
- 2017-18: `3`
- 2016-17: `1`

**Kyle Lowry's weighted sum of minutes played from the past 3 seasons**

`6 * 2213 + 3 * 2510 + 1 * 2244 = 23052` minutes played.

**Kyle Lowry's weighted sum of assists from the past 3 seasons**

`6 * 564 + 3 * 537 + 1 * 417 = 5412` assists.

**Calculate the weighted sum of assists for a league average player playing Lowry's minutes, then scale to 1000 minutes**

`6 * 2213 * (60483 / 594450) + 3 * 2510 * (57136 / 593850) + 1 * 2244 * (55660 / 594900) = 2293.46` assists. Scaled to 1000 minutes this is `1000 * (2293.46 / 23052) = 99.49` assists.

**Calculate Lowry's projected per-36 minute value**

`(5412 + 99.49) / (23052 + 1000) * 36 = 8.249` assists per 36 minutes.

**Calculate Age Adjustment**

If a player is younger than 28, then the age adjustment is equal to `(28 - age) * 0.004`. If the player is older than 28, then the age adjustment is equal to `(28 - age) * 0.002`. 
Lowry is 33 years old during the 2019-20 season, so his age adjustment is `(28 - 33) * 0.002 = -0.01`.

**Apply age adjustment to Lowry's projected per-36 minute value**

`(1 - 0.01) * 8.249 = 8.167` assists per 36 minutes projected for the 2019-20 season.

Considering that the 2019-20 season hasn't concluded, Kyle Lowry's actual Assists per 36 minutes is `7.6` after 52 games played this season. Not too far off from our projection!


A few notes about the Simple Projection System:
- The sign of the age adjustment is reversed for the following stats: `FG Missed, 3 Pointers Missed, FT Missed, turnovers, and personal fouls`
- For shooting categories, shots missed are projected rather than shots attempted. Projected shots attempted are then calculated by adding projected shots made and projected shots missed
- Projected 2 pointers made/missed are calculated by subtracting projected 3 pointers made/missed from projected field goal made/missed
- Projected points are calculated using the projected 2 pointers made, projected 3 pointers made, and projected free throws made


Thanks for reading. If you're interested, my entire codebase is available <a target="_blank" href="https://github.com/dktlee/dylanlee/tree/master/all-nba_predictions">here</a>.
