# league-match-time-analysis

# League of Legends Game Time Analysis

**Name(s)**: Chia-Lun Tsao (Gallant)

## Step 1: Introduction

__League of Legends__ (LOL) is a Multiplayer Online Battle Arena (MOBA) game made by Riot games in 2009. In this game, 10 players are divided into 2 teams of 5 each (the blue side and the red side), and pick their respective champions (I will explain what this is later) to try to win the game by defeating the other team. Here is the game map:

![image](data/rift.jpeg)

As you can see above, there are three lanes where players play their champions on, and the rest of the area of the map is called the jungle. There are 5 traditional roles:

Top Laner (TOP): Normally these are bruisers or tanks who stand in front of a teamfight to provide the necessary space for others to deal damage to the enemy team.

Jungler (JUG): The main roaming character of the game who is responsible for slaying jungle monsters and also controlling the neutral objectives of the game.

Mid Laner (MID): The player in the middle lane who can dictate the pace of the game by reaching out to the jungle, or the top and bottom lane.

Bot Laner (BOT): Normally the hypercarry who is relatively weak earlier but gets progressively stronger as the game goes on and takes over at the late game.

Support (SUP): The helper of the bot lane in the early game. Usually support players are responsible of being the in-game leader as their load is relatively less heavy in terms of being mechanicallly skilled.

The objective of the game is to destroy the opponents' nexus (which are located on the top-right and bottom-left corner respectively). Normally, each game will last around 15-45 minutes depending on how fast the pace of the game is dictated by the teams.

![Image](data/comp.jpeg)

Competitive League of Legends was at its peak a few years ago, and is still one of the most viewed channels on popular stream platforms including YouTube and Twitch. The competitive scene is divided into competitions within the domestic leagues and also the international tournaments (which lots of League players look forward to.

In this project, the question that we are interested in is:

__"Around how long does a League of Legends game take?"__

The designers of League of Legends do not want the average competitive game to last too long, or else people will think it's too boring and not want to watch the game. However, they also will not want the game to be too short, as this wil lead to a one-sided affair every time so the game becomes more predictable and less interesting. Perhaps if there is any significance in the time, this could help them adjust the game accordingly so that more players are willing to join this wonderful community. As such, we are going to conduct on some analysis from League of Legends competitive data.

For this project, we will be mainly focusing on League of Legends competitive data from Season 13 (2023). The dataset contains information about all the professional matches that occured in Season 13 within the competitive scene. Some of its columns include the champions that professional LOL players picked in their games, the result of the game, and certain statistics that are related within the competitive match.

## Step 2: Data Cleaning and Exploratory Data Analysis

### Data Cleaning

First of all, here is the raw dataframe:

| gameid                | datacompleteness   |   url | ...   |   opp_killsat15 |   opp_assistsat15 |   opp_deathsat15 |
|:----------------------|:-------------------|------:|:------|----------------:|------------------:|-----------------:|
| ESPORTSTMNT06_2753012 | complete           |   nan | ...   |               0 |                 0 |                0 |
| ESPORTSTMNT06_2753012 | complete           |   nan | ...   |               0 |                 0 |                0 |
| ESPORTSTMNT06_2753012 | complete           |   nan | ...   |               0 |                 0 |                0 |
| ESPORTSTMNT06_2753012 | complete           |   nan | ...   |               1 |                 0 |                0 |
| ESPORTSTMNT06_2753012 | complete           |   nan | ...   |               0 |                 1 |                0 |

There are a total of 130 columns, which is far more than what we need for our analysis.

Regarding the analysis of our data, we are most interested in the following columns:

`gameid`: The unique identification for the League match, as a string

`gamelength`: The length of the competitive match in seconds, as an integer

`league`: The league (region) that the match took place in

`****at**`: The specific game statistic at xx minutes into the game. For example, `opp_assistsat15` would correspond to the number of assists that the particular opponent has at the 15 minute mark. We won't talk all of this obviously, but chances are that some of it have clear relations.

One important point (which we will get into later) of this dataframe is that each unique professional game is encapsulated in 12 rows: The first five rows consists of player statistics for the blue team, and the next five rows correspond to the player statistics of the red team. The eleventh and twelveth row are just team statistics for the blue and red team respectively. This will also matter as it decides what we can or cannot impute into the dataset when cleaning up this dataframe.

Before we start imputing the missing data, we first extract the useful columns that will be used for our analysis for this part (until the end of hypothesis testing): `gamelength`, `league`, `position`, `champion`, `dpm`, & `damagetakenperminute`.

Here is the first few columns of the dataframe that has the columns that are mentioned above (before we start the cleaning):

| gameid                |   gamelength | league   | position   | champion   |     dpm |   damagetakenperminute |   earnedgold |   result |   goldat15 |
|:----------------------|-------------:|:---------|:-----------|:-----------|--------:|-----------------------:|-------------:|---------:|-----------:|
| ESPORTSTMNT06_2753012 |         2612 | LFL2     | top        | Jax        | 328.093 |                719.541 |        13251 |        1 |       5059 |
| ESPORTSTMNT06_2753012 |         2612 | LFL2     | jng        | Poppy      | 142.856 |                847.305 |         6478 |        1 |       4325 |
| ESPORTSTMNT06_2753012 |         2612 | LFL2     | mid        | Taliyah    | 620.858 |                365.352 |        10118 |        1 |       4956 |
| ESPORTSTMNT06_2753012 |         2612 | LFL2     | bot        | Ezreal     | 964.893 |                398.453 |        11728 |        1 |       5217 |
| ESPORTSTMNT06_2753012 |         2612 | LFL2     | sup        | Karma      | 130.199 |                360.276 |         3212 |        1 |       2827 |

For our dataframe, we will not fill in values for the champion column, as the players are playing the champions, not the teams, so it does not make sense for a team to play a champion. Other than that column, there is not much to do as for the analysis in part 1. Again, we will clean the data in part 2 when we use different columns.

### Univariate Analysis

First of all, let's take a look at how the overall game lengths are distributed across our data. Notice we only need to take every row that is a multiple of twelve because every twelve rows in our dataset represents a unique League of Legends competitive match in 2023.

<iframe
  src="plotly_figs/lengths.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The distribution seems to be roughly [log-normal](https://en.wikipedia.org/wiki/Log-normal_distribution#:~:text=In%20probability%20theory%2C%20a%20log,X\)%20has%20a%20normal%20distribution.) with the peak of the histogram being somewhere arounf the 1800 second mark, which agrees with the average time of a League of Legends game: 30 minutes (even not in the competitive scene).

Next, let's look at the damage taken per minute, but only for junglers. In 2023, a lot of the jungler picks are curated so that they are meant to provide the necessary space for the bot lane players to deal heavy damage. Therefore we would expect them to 'tank' the most damage in a teamfight.

<iframe
  src="plotly_figs/dtpmjg.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Again, we see this roughly log-normal pattern appear for the junglers' data. Note that the peak of this histogram is somewhere around the 850-900 mark. Combining this with the above plt about the distributions of game length, we can generate a very rough estimate for the damage taken per minute for all League of Legends professional games in 2023: We have that about 
$$
[850 \cdot 30, 900 \cdot 30] = [25500, 27000]
$$
damage is taken from the jungler in a normal professional match in League of Legends in 2023.

### Bivariate Analysis

Once when I was watching a League of Legends game in China, one of casters was criticizing the bot lane player of the losing team. According to them, the player "was given huge amounts of resources that are sacrificed by his team, yet his impact in team fights were far lower compared to the amount of resources that he got". A natural reasoning when we play League of Legends is that the more amount of gold a player has compared to his opponent, the more damage the player is able to deal throughout the game. Is this actually a reasonable claim? To answer this question, we are going to compare the total amount of gold in the game with the damage per minute, but only for bot laners, as they are the main source of damage threat to the enemy team. Here is the first few rows of the dataframe that we are going to use:

|     dpm |   earnedgold |
|--------:|-------------:|
| 964.893 |        11728 |
| 507.267 |        11219 |
| 714.163 |        14318 |
| 524.163 |        14858 |
| 344.212 |         9219 |