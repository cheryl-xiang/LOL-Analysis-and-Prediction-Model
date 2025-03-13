# League of Legends Vision Control and Support Role Analysis
UCSD DSC80 Final Project

League of Legends Vision Control and Support Role Analysis is a comprehensive data science project conducted at UCSD. The project includes in-depth  analysis, starting from EDA to hypothesis testing, development of baseline models, and a fairness analysis. 

Author: Cheryl Xiang

## Introduction
League of Legends is a popular multiplayer online battle arena (MOBA) game developed by Riot Games, where two teams of five players compete to destroy the opposing team's Nexus, a core building located in their respective bases. The game combines strategy, teamwork, and skill, featuring a wide roster of champions, each with unique abilities.

The dataset I will be working with is from 	[Oracle's Elixir](https://oracleselixir.com/tools/downloads), which contains data on League of Legend Competitive Matches from 2014 to the 2025. For this project, I will be focusing on data from 2022 since it has the most recorded games of of recent years. The LOL landscape changes significantly from year to year, so I have chosen not to combine data across multiple years.

In this project, I will investigate the relationship between vision control/placing wards and winning a match. While there are many moving components to a LOL match, one that is sometimes overlooked is **vision control**. Vision control refers to the strategic management of "vision" or the ability to see and monitor parts of the game map that are not directly in a player's line of sight. This is achieved for the most part through placing your own wards as well as clearing enemy wards. Wards are used to provide vision in specific areas, allowing a team to detect enemy movements, objectives, or potential threats, which allows teams to make informed decisions about key areas of the map and help teams to secure important match objectives. 

### DataFrame Columns 

The original data set has 149232 rows, and 123 columns from 12549 recorded matches. For the purposes of this analysis, I will be focusing on the columns listed below:

- `gameid`: (str) The 'gameid' column includes a unique identifier for each game in the dataset. 

- `position`: (str) The 'position' column specifies the role played by each individual player. They are separated into 'top,' 'jng (jungle),' 'mid (middle),' 'bot (bottom),' and 'sup (support).'

- `win`: (bool) The 'win' column indicates the outcome of the match each for player. True indicates that the player's team won, and False indicates that they lost.

- `kills`: (int) The 'kills' column contains the number of enemy champions (players) a player eliminated during the match. 

- `deaths`: (int) The 'deaths' column records the number of times a player was eliminated by enemy champions (players). 

- `assists`: (int) The 'assists' column records the number of assists a player has made. An assist occurs when a player contributes to eliminating an enemy champion without landing the final blow (kill). This includes actions like dealing damage, providing crowd control, or setting up a kill for an ally player

- `wardsplaced`: (int) The 'wardsplaced' column indicates the number of wards a player has placed on the map throughout a match. 
- 
- `wardscleared`: (int) The 'wardscleared' column records the number of enemy wards the player has cleared during the game. Clearing wards helps deny vision to the opposing team, reducing their map awareness.

- `visionscore`: (int) The 'visionscore' column represents a player's contribution to vision on the map. It includes both placing and clearing wards, as well as providing vision through abilities or items. The higher the score, the more a player has contributed to vision control during the match.

- `damagetochampions`: (int) The 'damagetochampions' column tracks the total amount of damage a player has dealt to enemy champions (players) throughout the game.

- `monsterkills`: (int) The 'monsterkills' column records the number of neutral monsters the player has killed. This includes objectives like Dragons, Rift Heralds, Barons, and jungle camps. Killing monsters provides gold, experience.

- `split`: (str) The 'split' column indicates the split or stage of the competitive season in which the data was collected.

- `teamid`: (str) The 'teamid' column contains the identifier or name of the team that the player is part of.
