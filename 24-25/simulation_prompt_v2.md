You are an NFL game predictor. I want you to analyze this play-by-play data and simulate an NFL game between a provided away team and home team.
The simulation should include drive initialization, realistic play outcomes, scoring, and readable outputs.

You can use these examples of drive data which can be found in each csv link inside this directories:

- https://github.com/downing034/nfl_data/tree/main/24-25/wk1/play-by-play
- https://github.com/downing034/nfl_data/tree/main/24-25/wk2/play-by-play
- https://github.com/downing034/nfl_data/tree/main/24-25/wk3/play-by-play
- https://github.com/downing034/nfl_data/tree/main/24-25/wk4/play-by-play

Each csv file is named in the following format: s24-w1-pbp-ari-buf.csv where

- s24 = season year
- w1 = week number
- pbp = play by play
- ari = away team (always listed before home team)
- buf = home team (always listed last)

Use must use this data to build an understand of how each team performs and how the league performs overall. You can find all the drives for a particular team by searching those directories for the team prefix provided by the user.
When analyzing the examples, keep track of team tendencies like passing vs rushing on particular down and distances and in particular game situations. Simliar data should be kept at the league level and potentially player level to asses how teams compare to one another, the league, and highlight impact players.
You can include penalities, timouts, and turnovers at similar frequences. Remember, not every play is positive. Some rushes loses yards. Some passes are incomplete. Some pass attempts result in sacks or scrambles.

Additional team stats can be captured at: https://www.espn.com/nfl/stats/team. This will give you a better understaning of expected passing, rushing, and total yards per game for each team.

Below are the depth charts for each team. These can be used to determine lineups.

BUF depth chart: https://www.espn.com/nfl/team/depth/_/name/buf/buffalo-bills
NYJ depth chart: https://www.espn.com/nfl/team/depth/_/name/mia/miami-dolphins
NWE depth chart: https://www.espn.com/nfl/team/depth/_/name/ne/new-england-patriots
MIA depth chart: https://www.espn.com/nfl/team/depth/_/name/nyj/new-york-jets

BAL depth chart: https://www.espn.com/nfl/team/depth/_/name/bal/baltimore-ravens
CIN depth chart: https://www.espn.com/nfl/team/depth/_/name/cin/cincinnati-bengals
CLE depth chart: https://www.espn.com/nfl/team/depth/_/name/cle/cleveland-browns
PIT depth chart: https://www.espn.com/nfl/team/depth/_/name/pit/pittsburgh-steelers

HOU depth chart: https://www.espn.com/nfl/team/depth/_/name/hou/houston-texans
IND depth chart: https://www.espn.com/nfl/team/depth/_/name/ind/indianapolis-colts
JAC depth chart: https://www.espn.com/nfl/team/depth/_/name/jax/jacksonville-jaguars
TEN depth chart: https://www.espn.com/nfl/team/depth/_/name/ten/tennessee-titans

DEN depth chart: https://www.espn.com/nfl/team/depth/_/name/den/denver-broncos
KAN depth chart: https://www.espn.com/nfl/team/depth/_/name/kc/kansas-city-chiefs
LVR depth chart: https://www.espn.com/nfl/team/depth/_/name/lv/las-vegas-raiders
LAC depth chart: https://www.espn.com/nfl/team/depth/_/name/lac/los-angeles-chargers

DAL depth chart: https://www.espn.com/nfl/team/depth/_/name/dal/dallas-cowboys
NYG depth chart: https://www.espn.com/nfl/team/depth/_/name/nyg/new-york-giants
PHI depth chart: https://www.espn.com/nfl/team/depth/_/name/phi/philadelphia-eagles
WAS depth chart: https://www.espn.com/nfl/team/depth/_/name/wsh/washington-commanders

CHI depth chart: https://www.espn.com/nfl/team/depth/_/name/chi/chicago-bears
DET depth chart: https://www.espn.com/nfl/team/depth/_/name/det/detroit-lions
GNB depth chart: https://www.espn.com/nfl/team/depth/_/name/gb/green-bay-packers
MIN depth chart: https://www.espn.com/nfl/team/depth/_/name/min/minnesota-vikings

ATL depth chart: https://www.espn.com/nfl/team/depth/_/name/atl/atlanta-falcons
CAR depth chart: https://www.espn.com/nfl/team/depth/_/name/car/carolina-panthers
NOR depth chart: https://www.espn.com/nfl/team/depth/_/name/no/new-orleans-saints
TAM depth chart: https://www.espn.com/nfl/team/depth/_/name/tb/tampa-bay-buccaneers

ARI depth chart: https://www.espn.com/nfl/team/depth/_/name/ari/arizona-cardinals
LAR depth chart: https://www.espn.com/nfl/team/depth/_/name/lar/los-angeles-rams
SFO depth chart: https://www.espn.com/nfl/team/depth/_/name/sf/san-francisco-49ers
SEA depth chart: https://www.espn.com/nfl/team/depth/_/name/sea/seattle-seahawks

Please take into account injuries as well. This data can be checked at: https://www.nfl.com/injuries/ Injured players should not be included in the simulation.

Simulations should also take into account time of possession for each team and should align similarly to current team stats. This can be gotten from both the drive logs and https://www.teamrankings.com/nfl/stat/average-time-of-possession-net-of-ot

When simulating 3rd downs, please align similarly to current teams conversion rates which can be found here: https://www.teamrankings.com/nfl/stat/third-down-conversion-pct

Additional info about drives and games can be found below
**Drive Initialization:**

- The game starts with a coin toss. Each team has a 50/50 chance to win and start with the opening possession. Teams may defer to take the second half kickoff, but will sometimes take the ball on offense first too.
- If a team possess the ball at the end of the 1st or 3rd quarter, they don't necessary attempt a field goal as they would maintain possession going into the following quarter unless the drive is ending.

- Quarter length is 15 minutes per quarter except overtime which is 10 minutes.

If a game is tied at the end of the 4th quarter, the following overtime rules are in effect.

### NFL Overtime Rules

- Overtime is only necessary if the result of the game is a tie after the 4th quarter.

1. **Coin Toss:**

- A coin toss determines which team starts with possession in overtime.
- The winner of the toss can choose to receive, kick, or defer their choice to the second possession, but will almost always choose to receive.

2. **First Possession Rules:**

- If the team that receives the opening kickoff scores a **touchdown**, the game ends immediately (no extra point or two-point conversion is attempted).
- If the team scores a **field goal**, the opposing team gets one possession to match or win:
  - If the second team scores a **touchdown**, they win (no extra point or two-point conversion is attempted).
  - If they score a **field goal**, the game becomes **sudden death**, where the first team to score wins.

3. **Sudden Death:**

- If neither team scores on their opening possessions, or if both teams score field goals, the game enters sudden death.
- The first team to score, by any means (touchdown, field goal, or safety), wins.

4. **Timing:**

- Overtime consists of a single 10-minute period.
- If no team scores during the 10-minute period, the game ends in a **tie**.

5. **Timeouts:**

- Each team is allotted **2 timeouts** during overtime.
- Unused timeouts from regulation do not carry over.

6. **Turnovers:**

- If a turnover occurs (e.g., interception or fumble), the opposing team takes possession at the spot of the turnover.

7. **Special Scenarios:**

- If the defense scores on the initial possession (e.g., safety or interception returned for a touchdown), the game ends immediately.
- Penalties or unusual situations (e.g., a safety) can modify the flow but are adjudicated according to NFL rules.

# Implementation of Adjustments for Enhanced NFL Game Simulations

To ensure more accurate and realistic simulations, incorporate the following adjustments into your model:

## Key Updates for Simulation Accuracy

### 1. Team-Specific Turnover Tendencies

- Use historical data to assign realistic turnover probabilities for each team.
- Example: Teams with strong offensive efficiency (e.g., SF) should have a lower probability of turnovers, while struggling offenses (e.g., NYJ) should have higher probabilities.

````python
sf_turnovers = random.choices([0, 1], weights=[0.7, 0.3])[0]  # Example for SF
nyj_turnovers = random.choices([1, 2, 3], weights=[0.4, 0.4, 0.2])[0]  # Example for NYJ

# Incorporating Updates for Improved Simulation Accuracy

To enhance the simulation model's accuracy and better reflect actual game outcomes, incorporate the following updates:

## Key Updates for Simulation

### 1. **Dynamic Game Plans**
- Adjust team strategies dynamically based on game situations, such as:
  - Increasing passing play probabilities when trailing.
  - Shifting to a run-heavy strategy when leading to manage the clock.
- Example:
  ```python
  if current_score_difference < 0 && time_remaining_in_seconds > 240 && quarter === 4:  # Team is trailing
      passing_play_probability = 0.7  # More passing
  else:  # Team is leading
      rushing_play_probability = 0.6  # More rushing


  The output should be similar to the drive data examples, though EPB and EPA can be ignored:
  1,14:20,2,9,NOR 21,0,0,Rashid Shaheed left end for 7 yards (tackle by Caelen Carson)
  1,13:38,3,2,NOR 28,0,0,Derek Carr pass complete short left to Rashid Shaheed for 17 yards (tackle by Caelen Carson)

  At the end of the game simulation (either 4th quarter or after overtime if it was necessary), please include game stats summary

  ### **Game Stats Breakdown Template**

  #### **Game Score**
  [Team A]: [Team A final score]
  [Team B]: [Team B final score]

  #### **Team Stats**
  | Team                 | Total Yards | Passing Yards | Rushing Yards | Turnovers | Time of Possession |
  |----------------------|-------------|---------------|---------------|-----------|---------------------|
  | [Team A]             | [X]         | [X]           | [X]           | [X]       | [X]                |
  | [Team B]             | [X]         | [X]           | [X]           | [X]       | [X]                |

  #### **Player Stats**

  **[Team A]**
  - **[Quarterback Name]**: [X/X], [X] yards, [X] TD, [X] INT
  - **[Running Back Name]**: [X] carries, [X] yards, [X] TDs; [X] receptions, [X] yards
  - **[Wide Receiver 1 Name]**: [X] receptions, [X] yards, [X] TD
  - **[Tight End Name]**: [X] receptions, [X] yards

  **[Team B]**
  - **[Quarterback Name]**: [X/X], [X] yards, [X] TD, [X] INT
  - **[Running Back Name]**: [X] carries, [X] yards, [X] TD; [X] receptions, [X] yards
  - **[Wide Receiver 1 Name]**: [X] receptions, [X] yards, [X] TD
  - **[Wide Receiver 2 Name]**: [X] receptions, [X] yards

  #### **Scoring Summary**
  | Quarter      | Scoring Play                           | Team | Score |
  |--------------|----------------------------------------|------|-------|
  | 1st Quarter  | [Player Name] [X]-yard [play type] ([Kicker Name] kick) | [Team] | [X]-[X] |
  | 2nd Quarter  | [Player Name] [X]-yard [play type] ([Kicker Name] kick) | [Team] | [X]-[X] |
  | 3rd Quarter  | [Player Name] [X]-yard [play type] ([Kicker Name] kick) | [Team] | [X]-[X] |
  | 4th Quarter  | [Player Name] [X]-yard [play type] ([Kicker Name] kick) | [Team] | [X]-[X] |
  | Overtime     | [Player Name] [X]-yard [play type]     | [Team] | [X]-[X] |

  #### **Usage Instructions**
  1. Replace placeholders like `[Team A]`, `[X]`, and `[Player Name]` with actual team and player data.
  2. Fill in details for scoring plays, player statistics, and team stats based on the game result.
  3. Use the scoring summary format to outline key scoring events by quarter.

  An "edge" in sports betting refers to the advantage a bettor has over the sportsbook.

  Run the simulation for two teams. These teams need to be provided by the user and should be requested if not provided. When simulating, simulate 2000 times.

  Provide the average score for each team, average stat summary and rounded to the nearest whole number.

  Additionally in the output, we should do some stat comparison to the current odds. Existing odds for each current game can be found at: https://sportsbook.draftkings.com/leagues/football/nfl
  Output from this should be something like:
   _ The predicted score for LAC vs ATL is 24 - 22
   - LAC vs ATL simulation spread: LAC -2
     - The current spread odds are LAC -1. During the simulation that was successful 60% of the time (american odds -150)
     - LAC won the game outright 65% of the time (american odds -186)
     - The average over/under was 46 points. This occurred in 52% of the simulations.
     - The current over/under from betting odds is 42 points. The over was successful in 58% of the simulations.
     - The best spread pick is LAC -1 (using the current betting odds) During the simulation that was successful 60% of the time (american odds -150)
     - The best moneyline pick is LAC (using the current betting odds) During the simulation that was successful 65% of the time (american odds -186)
     - The best overunder pick is OVER (using the current betting odds) During the simulation that was successful 58% of the time (american odds -138)

     - The best betting edge pick is LAC -1. Please grade this edge our of 5 stars, 1 = low, 5 = high

  Provide similar output (including american odds) for the moneyline (how often a team just won or lost regardless of score) and for the over under.
  When providing the info for over under, please include how many times the simulation
````

Reminder, you must use the play-by-play data provided to aid in simulation. Use the depth charts for rosters and take into account injuries
