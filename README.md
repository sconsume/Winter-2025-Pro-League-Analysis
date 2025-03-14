# Winter 2025 Pro League of Legends Attack Damage Carry Analysis
Winter 2025 Pro League of Legends Attack Damage Carry (ADC) Analysis is a data science project undertaken at UCSD. The project spans multiple phases, including data cleaning, exploratory data analysis, missingness assesstment, hypothesis testing, baseline model development, and fairness assessment. Its central aim is to determine whether ADC players truly carry their teams in 2025 Pro League matches.

Authors: Jesus Tello
## Introduction
### General Introduction
League of Legends (LoL) is a globally renowned multiplayer online battle arena (MOBA) game developed and published by Riot Games. Since its release in 2009, it has grown into one of the most influential titles in esports, attracting millions of players and fans worldwide. In LoL, two teams of five champions each compete on a dynamic battlefield called Summoner’s Rift, where the primary goal is to destroy the enemy’s nexus while defending one’s own. The game features a diverse roster of over 150 champions, each equipped with unique abilities that cater to various roles, ranging from high-damage carries to supportive and strategic playmakers.

The ADC is designed to deliver sustained physical damage and secure key objectives. Traditionally partnered with a support in the bottom lane, ADCs must navigate a delicate balance between survivability and damage output, positioning themselves to inflict critical damage during team fights. In 2024, ADCs transcended their typical role, finding success in other lanes, such as Top and Mid lane, due to their high damage potential and scaling prowess. This flexibility saw them becoming a meta mainstay across various positions, significantly impacting team's strategies and game outcomes. However, this dominance also led to deliberate adjustments by Riot. The runes and items that fueled ADCs’ power were subsequently nerfed in an effort to restore balance.

The competitive landscape underwent further transformation with the shift into 2025. A significant change was the removal of ADC's upgradeable boot option, Zephyr boots, in favor of a new Feats of Strength system featuring upgradeability for all boot options. While this system introduces dynamic choices for most roles, ADCs find themselves at a disadvantage. Their go-to option, Berserker’s Greaves, now pales in comparison to other upgrade paths, raising concerns about whether the ADC can continue to serve as the primary sustained damage dealer in professional play.

With the given context, the central question of this project arises: **In tier 1 professional leagues, the highest level of League of Legends professional play, does the ADC tend to have the highest amount of kills and damage when compared to other rules? Does the ADC truly carry at such a high-level of play?** Using comprehensive data analysis techniques, the project seeks to determine if ADC players in tier 1 professional League of Legends matches consistently record higher kills and damage than their counterparts, thereby assessing whether ADCs truly serve as the primary carry in elite play, providing insights that could influence strategic team decision-making in future matches.
### Column Introduction
<a href="https://oracleselixir.com/tools/downloads" target="_blank">Oracles Elixir Match Data</a>
**The 2025 dataset** contains 21,264 rows and 161 columns and was sourced from Oracle. It includes detailed match and player-level statistics for League of Legends, ranging numerous leagues across different tiers of professional play. Here are some of the columns and basic descriptions: 

- **gameid**: A unique identifier for each individual match, allowing you to distinguish between different games.
- **league**: The name or identifier of the professional league (e.g., LCK, LPL, etc.).
- **position**: The role played by a participant (e.g., Top, Jungle, Mid, Bot, Support).
- **teamname**: The name of the team or organization the player is representing.
- **gamelength**: The total duration of the match, typically recorded in seconds.
- **result**: Indicates whether the team associated with this row won (1) or lost (0) the match.
- **kills**: The number of enemy champions killed by the player.
- **deaths**: The number of times the player died.
- **assists**: The number of kills in which the player assisted.
- **pentakills**: The number of times the player achieved a “penta kill” (killing all five enemy champions in rapid succession).
- **firstbloodkill**: A binary indicator (1 or 0) denoting whether this player secured the first kill of the match.
- **damagetochampions**: The total damage the player dealt to enemy champions.
- **damageshare**: The fraction of the team’s total damage contributed by the player.
- **goldspent**: The total amount of gold the player spent on items during the match.
- **total cs**: The total “creep score,” representing the number of minions and jungle monsters the player last-hit.
## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
To streamline data cleaning, **(1)** only columns that were deemed necessary for honing in on the project's focus were kept, such as: `gameid`, `league`, `position`, `teamname`, `gamelength`, `result`, `kills`, `deaths`, `assists`, `pentakills`, `firstbloodkill`, `damagetochampions`, `damageshare`, `goldspent`, and `total cs`. After, was the **(2)** removal of rows where the `position` was labeled as “team,” since this information was redundant due to the project's focus on the 5 player-per-team role entries in a given match. Next, was **(3)** filtering the dataset to include only tier 1 professional leagues and the most recent up-to-date matches First Stand Tournament as of the finalization of this project. Following this, was **(4)** converting `gamelength` from seconds to minutes for easier interpretation, and **(5)** updating position labels to more readable names like “Top,” “Jungle,” “Mid,” “Bot,” and “Support.” Lastly, was **(6)** renaming the `total cs` column to `total_cs` for consistency. 

Below is the head of the pro_league_clean dataframe:

'<table border="1" class="dataframe">\n  <thead>\n    <tr style="text-align: right;">\n      <th></th>\n      <th>gameid</th>\n      <th>league</th>\n      <th>position</th>\n      <th>teamname</th>\n      <th>gamelength</th>\n      <th>result</th>\n      <th>kills</th>\n      <th>deaths</th>\n      <th>assists</th>\n      <th>pentakills</th>\n      <th>firstbloodkill</th>\n      <th>damagetochampions</th>\n      <th>damageshare</th>\n      <th>goldspent</th>\n      <th>total_cs</th>\n      <th>missing_pentakills</th>\n      <th>adc_target</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>11715-11715_game_1</td>\n      <td>LPL</td>\n      <td>Top</td>\n      <td>Weibo Gaming</td>\n      <td>35.383333</td>\n      <td>1</td>\n      <td>4</td>\n      <td>0</td>\n      <td>3</td>\n      <td>NaN</td>\n      <td>0.0</td>\n      <td>9830</td>\n      <td>0.123949</td>\n      <td>12934</td>\n      <td>314.0</td>\n      <td>True</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>11715-11715_game_1</td>\n      <td>LPL</td>\n      <td>Jungle</td>\n      <td>Weibo Gaming</td>\n      <td>35.383333</td>\n      <td>1</td>\n      <td>4</td>\n      <td>0</td>\n      <td>5</td>\n      <td>NaN</td>\n      <td>1.0</td>\n      <td>10013</td>\n      <td>0.126255</td>\n      <td>13193</td>\n      <td>273.0</td>\n      <td>True</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>11715-11715_game_1</td>\n      <td>LPL</td>\n      <td>Mid</td>\n      <td>Weibo Gaming</td>\n      <td>35.383333</td>\n      <td>1</td>\n      <td>5</td>\n      <td>1</td>\n      <td>8</td>\n      <td>NaN</td>\n      <td>0.0</td>\n      <td>37571</td>\n      <td>0.473724</td>\n      <td>14249</td>\n      <td>286.0</td>\n      <td>True</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>11715-11715_game_1</td>\n      <td>LPL</td>\n      <td>Bot</td>\n      <td>Weibo Gaming</td>\n      <td>35.383333</td>\n      <td>1</td>\n      <td>3</td>\n      <td>2</td>\n      <td>6</td>\n      <td>NaN</td>\n      <td>0.0</td>\n      <td>15603</td>\n      <td>0.196738</td>\n      <td>13169</td>\n      <td>305.0</td>\n      <td>True</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>11715-11715_game_1</td>\n      <td>LPL</td>\n      <td>Support</td>\n      <td>Weibo Gaming</td>\n      <td>35.383333</td>\n      <td>1</td>\n      <td>1</td>\n      <td>2</td>\n      <td>11</td>\n      <td>NaN</td>\n      <td>0.0</td>\n      <td>6292</td>\n      <td>0.079335</td>\n      <td>8620</td>\n      <td>38.0</td>\n      <td>True</td>\n      <td>0</td>\n    </tr>\n  </tbody>\n</table>'








