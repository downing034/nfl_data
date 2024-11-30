You are an NFL game predictor. I want you to analyze this play-by-play data and simulate an NFL game between a provided away team and home team. The simulation should include drive initialization, realistic play outcomes, scoring, and readable outputs for field_position. Follow these guidelines:

1. **Drive Initialization:**
   - The game starts with a coin toss. Each team has a 50/50 chance to win and start with the opening possession
   - Each drive starts with `current_down` = 1 and `field_position` start should be determined by the following info, likely the 28 or 29.
   - The first row of the game should start with `current_down` = 1, `togo = 10`, `previoius_play_yards_gained = 0`
   - The average starting field position for all drives after receiving a kickoff and not returning the kick was the receiving team's own 29 yard line. The average starting field position for all drives after a kickoff return was the receiving team's own 28 yard line. Team's returned the ball 33.3% of the time.
   - If a team commits a fumble turnover, the `field_position` will likely start near the previous `field_position`. If a team commits an interception, the `field_position` could vary based on the length of the attempted pass, but will likely end up near the original `field_position`. On occasion, if an interception is thrown when a teams `field_position` is between 0 and 20, the result is likely a touchdown for the opposing team, an extra point or two point conversion is attempted, and then team which through the interception will start a new drive afterwards following previous drive initialization rules.

2. **Timeout Logic**
  - teams are awarded 3 timeouts each half. These are sometimes used in the middle of drives.
  - Teams will typically have 2 timeouts remaining when `time_left > 120 && quarter = 2`.
    
    
    
    <!-- - If a team has the ball in this scenario, they are likely to call more passing plays than running plays.
    - Teams in `field_position > 50` will follow these rules
      - Use timeouts to stop the clock from running out before the end of the half so they can score points.
    
  - Teams will typically have 2 timeouts remaining when `time_left > 240 && quarter = 4`, however, each team will use. -->

3. **Base Play Outcome:**
  - Drive Data for the league can be found here: https://stathead.com/football/drive_finder.cgi and used as reference to help determine how often a particular outcome of drive should happen.
  - Drive logs can be supplied for each team to help determine a more realistic approach, but may not always be provided.

  - Time spent per play can vary by team and by scenario.  This site can be used for average time cost per play by team https://www.teamrankings.com/nfl/stat/seconds-per-play
    - Teams that are winning with `time_left < 360` and `quarter = 4` will take more time (closer to 35 seconds per play)
    - Teams that are losing with `time_left < 360` and `quarter = 4` will take less time (closer to 25 seconds per play)
    - Extra points and 2pt conversions should not affect `time_left`

  - Teams will try to stay balanced (50/50 split) between pass plays and running plays, but this isn't always the case.
  - If a team is on `current_down = 3` and the `togo` is 5 or greater, they will likely attempt a pass instead of run.
  - Teams will pass the ball more often when `time_left < 120` and `quarter = 2`
  - Teams that are winning with `time_left < 360` and `quarter = 4` will run the ball more often and tend to only throw the ball in `current_down = 3` and `togo > 5`
  - Teams which are losing tend to throw the ball more:
    - If a team is losing by 8 points or less, `time_left < 180`, and `quarter = 4`, a team will almost exclusively pass instead of run.
    - If a team is losing by 9 points or more, `time_left < 360`, and `quarter = 4`, a team will almost exclusively pass instead of run.

  - If `time_left > 10`, `quarter = 2`, and `field_position > 60` the team with the ball will not run or pass and instead will attempt a field goal.
  - If `time_left > 10`, `quarter = 4`, and `field_position > 60`:
    - If the team with the ball is losing by 3 points or less, they will attempt a field goal.
    - If the team with the ball is losing by more than 3 points, they will attempt a pass regardless of field position and the yards gained will be enough to get to 100 or greater. The further the `field_position` is from 100, the longer the pass attempt and the less likely it is to produce any yards.
      - `field_position < 60` likely of completion = 5%,
      - `field_position between 61 and 70` 7%
      - `field_position between 71 and 80` 10%
      - `field_position between 81 and 90` 30%
      - `field_position between 90 and 100` 50%
    - In the previous scenarios and probabilities if successful yards gained updates to 100. Ex. if `field_position = 65`, the pass attempt has 5% chance to succeed. If there is success, `field_position = 100` and the team scores a touchdown. An extra point or two point conversion will always be attempted after regardless of `time_left` in both `quarter = 2` and `quarter = 4`. If `quarter = 4` and the team is down more than 1 point, they will go for 2pt conversion.

  - Team Data is available at: https://www.teamrankings.com/nfl/stats/ to assist in prediction between pass/run balance. Keep in mind though the scenarios above though as teams will pass/run more in those specific scenarios and break from tendencies. Stats such as "Plays per game", "First Downs per game", "Rushing Attempts per game", "yards per rush attempt", similar stats for passing, "scoring defense", "opponent yards per play", etc can be used from this site to help determine rush, pass, and other attempt tendencies and their expected yards gained/lost

  - if drive or play by play data can not be found, the following probabilities can be used to assess if a play should gain, lose, or maintain the same yards.
   - Positive Yards: 70%
   - Zero Yards: 20%
   - Negative Yards: 10%.

4. **Scoring:**
   - Touchdowns occur when `field_position >= 100` (6 points).
   - Extra points are successful 96.2% of the time.
   - If a team has scored a touchdown when `quarter = 4`, they may choose to go for two points instead an extra point. This article explains some analytics on when to go for two instead of kicking an extra point: https://fivethirtyeight.com/features/when-to-go-for-2-for-real/
   - Field goals are attempted generally inside of 60 yards. This means 110 (counting the 10 yards for the endzone) - `Field_Position` >= 60. Occasionally, teams will attempt longer kicks between 60 and 63 yards, but only certain kickers can do that.
   - Accuracy or success rates vary based on the length of kick and kicker. This site can be used to check individual players or aggregate a general league success rate from a particular distance range. https://www.nfl.com/stats/player-stats/category/field-goals/2022/REG/all/kickingfglong/DESC?aftercursor=AAAAGQAAABZASwAAAAAAADFleUp6WldGeVkyaEJablJsY2lJNld5STFOQ0lzSWpNeU1EQTFNRFE1TFRSbE16Y3ROVFUwTWkweVltTXhMVEV5TmpSalpXVXpPVFU1WkNJc0lqSXdNaklpWFgwPQ==
   - If a kicker's specific stats can be found, it may make the most sense to use those vs the league average.
   - If a field goal (not an extra point) is missed, the other team takes over possession, gains 7 yards of `field_position`, and starts at `current_down = 1`.
   - If the offense is tackled in their own endzone (`field_position <=0`), a saftey is scored by the opposing team. In this scenairo, the opposing team gets 2pts, and they get the ball starting their next drive at their own 35 yard line.

4. **Fourth Down Decisions:**
   - Teams will punt in most 4th down scenarios unless:
    - In a late-game risk scenario (e.g., `quarter = 4` and `time_left ≤ 300`) a team will likely go for it on 4th down regardless if they are losing and there is not enough time left to get the ball back and score. If a team is in the lead, they will punt the ball away almost every time on 4th down regardless of the yards togo.
    - `field_position` is between 50 and 60 and the `togo` is ≤ 3 yards. In this type of scenario, teams may try to make the yards to gain since punting may yield the same net field position. Team's punt in this scenario 50% of the time. If a team goes for it and does not make enough yards to get a first down, the opposing team starts their drive at that field_position (existing `field_position` + `previous_play_yards_gained`)

5. **Play Type:**
  - Play types should include:
   - Run: rushing attempt that doesn't result in a fumble or touchdown
   - Run - TD: rushing attempt that results in `field_position >= 100`
   - Fumble: rushing attempt or passing attempt resulting in change of possession, reminder some passes can be caught and fumbled
   - Pass - Complete: passing attempt which gains yards and doesnt result in a fumble, touchdown, or interception
   - Pass - Incomplete: passing attempt which gains 0 yards anddoesnt result in a fumble, touchdown, or interception
   - Pass - TD: passing attempt that results in `field_position >= 100`
   - Interception: passing attempt resulting in change of possession
   - Sack: passing attempt that results in loss of yards
   - Punt: when a team kicks the ball to the other team as a result of not making the `togo` yards
   - XP - Miss: extra point attempt after scoring a touchdown that fails
   - XP - Made: extra point attempt after scoring a touchdown that is successful
   - 2pt - Fail: two point attempt after scoring a touchdown that fails
   - 2pt - Success: two point attempt after scoring a touchdown that is successful
   - FG - Miss: field goal attempt which is missed
   - FG - Success: field goal attempt which is successful
   - Safety: when the offense `field_position <= 0`
   - Penalty: - play which results in loss of yards, no loss of down, no `time_left` change

5. **Field Position Display Logic:**
   - 0–49: `<Team> <Field_Position>` (e.g., `MIA 38`).
   - 50: Displayed as `50`.
   - 51–100: `<Opposing Team> <100 - Field_Position>` (e.g., `GNB 40`).
   <!-- - May need to define turnover position logic here -->

6. **Columns for Output:**
   - `quarter`, `time_left`, `away_score`, `home_score`, `possession`, `current_down`, `togo`, `display_field_position`, `previous_play_yards_gained`, `play_type`.

**Play By Play Examples**
- SEA vs DEN
Quarter,Time,Down,ToGo,Location,DEN,SEA,Detail
,,,,,,,DEN won the coin toss and deferred. SEA to receive the opening kickoff.
1,15:00,,,DEN 35,0,0,DEN kicks off 66 yards returned by SEA for 31 yards
1,14:55,1,10,SEA 30,0,0,SEA sacked by DEN for -7 yards
1,14:22,2,17,SEA 23,0,0,SEA pass deep middle is intercepted by DEN at SEA 41 and returned for 21 yards SEA 20
1,14:12,1,10,SEA 20,0,0,DEN rush for 9 yards
1,13:27,2,1,SEA 11,0,0,DEN rush for -1 yards
1,12:47,3,2,SEA 12,0,0,DEN pass incomplete
1,12:40,4,2,SEA 12,0,0,Penalty on DEN: "Delay of Game" -5 yards
1,12:40,4,7,SEA 17,3,0,DEN FG - Success, 35 yard field goal good
1,12:36,,,DEN 35,3,0,DEN kicks off 67 yards returned by SEA for 29 yards
1,12:31,1,10,SEA 27,3,0,Penalty on SEA: "False Start"  -5 yards
1,12:31,1,15,SEA 22,3,0,SEA rush for 8 yards
1,11:47,2,7,SEA 30,3,0,SEA pass complete short right for 2 yards
1,11:09,3,5,SEA 32,3,0,SEA pass incomplete deep right
1,11:04,4,5,SEA 32,3,0,SEA punts 44 yards returned by DEN for 11 yards
1,10:54,1,10,DEN 35,3,0,DEN pass complete short right for 3 yards
1,10:15,2,7,DEN 38,3,0,DEN pass complete short left for -2 yards
1,9:50,3,9,DEN 36,3,0,DEN pass complete short right for 4 yards
1,9:11,4,5,DEN 40,3,0,DEN punts 39 yards fair catch (no return) at SEA 21
1,9:04,1,10,SEA 21,3,0,SEA rush for 3 yards. Penalty on SEA: "Offensive Holding -10 yards"
1,8:38,1,17,SEA 14,3,0,SEA rush for -3 yards
1,8:00,2,20,SEA 11,3,0,SEA pass complete short right for 9 yards
1,7:18,3,11,SEA 20,3,0,SEA pass incomplete short right
1,7:14,4,11,SEA 20,3,0,SEA punts 52 yards returned by DEN for 5 yards
1,7:02,1,10,DEN 33,3,0,DEN rush for 3 yards
1,6:25,2,7,DEN 36,3,0,DEN rush for 4 yards
1,5:47,3,3,DEN 40,3,0,DEN pass incomplete short left
1,5:44,4,3,DEN 40,3,0,DEN punts 38 yards fair catch (no return) at SEA 22
1,5:38,1,10,SEA 22,3,0,SEA rush for 4 yards
1,5:16,2,6,SEA 26,3,0,SEA pass complete short right for 9 yards
1,4:52,1,10,SEA 35,3,0,Penalty on DEN: "Neutral Zone Infraction" +5 yards
1,4:39,1,5,SEA 40,3,0,SEA pass complete short right for 10 yards
1,3:59,1,10,DEN 50,3,0,SEA pass incomplete deep right. Penalty on DEN: "Roughing the Passer" +15 yards
1,3:56,1,10,DEN 35,3,0,SEA pass complete short left for no gain
1,3:20,2,10,DEN 35,3,0,SEA rush for 3 yards
1,2:49,3,7,DEN 32,3,0,SEA pass incomplete short right
1,2:46,4,7,DEN 32,3,3,SEA FG - Success, 50 yard field goal good
1,2:41,,,SEA 35,3,3,SEA kicks off 65 yards touchback
1,2:41,1,10,DEN 30,3,3,DEN pass complete short right for 4 yards
1,2:03,2,6,DEN 34,3,3,DEN pass complete short left for 3 yards
1,1:21,3,3,DEN 37,3,3,Den rush right end for 3 yards
1,0:56,1,10,DEN 40,3,3,DEN pass incomplete deep middle
1,0:51,2,10,DEN 40,3,3,DEN pass complete short left for 9 yards
1,0:09,3,1,DEN 49,3,3,DEN rush for 4 yards
2,15:00,1,10,SEA 47,3,3,DEN rush for 12 yards. DEN fumbles recovered by DEN at SEA 35 and returned for +2 yards
2,14:13,1,10,SEA 33,3,3,DEN rush for 4 yards
2,13:32,2,6,SEA 29,3,3,DEN rush for 7 yards
2,12:51,1,10,SEA 22,3,3,DEN pass complete short right for 3 yards
2,12:15,2,7,SEA 19,3,3,DEN pass complete short right for -2 yards
2,11:34,3,9,SEA 21,3,3,DEN pass deep left is intercepted by SEA at SEA 1 and returned for 0 yards (no gain)
2,11:28,1,10,SEA 1,5,3,SEA pass incomplete short left. Penalty on SEA: "Offensive Holding" -1 yard, SEA Safety
2,11:22,,,SEA 20,5,3,SEA kicks off 65 yards returned by DEN for 17 yards
2,11:18,1,10,DEN 32,5,3,DEN rush for 4 yards
2,10:35,2,6,DEN 36,5,3,DEN pass incomplete short right
2,10:31,3,6,DEN 36,5,3,DEN pass incomplete short middle
2,10:26,4,6,DEN 36,5,3,DEN punts 54 yards. Fumble by SEA recovered by DEN at SEA 10
2,10:15,1,9,SEA 9,5,3,Penalty on DEN: "False Start" -5 yards
2,10:15,1,14,SEA 14,5,3,Den rush for 1 yard
2,9:34,2,13,SEA 13,5,3,DEN rush for 2 yards
2,8:52,3,11,SEA 11,5,3,DEN pass incomplete short middle
2,8:48,4,11,SEA 11,8,3,DEN FG - Success, 30 yard field goal good
2,8:45,,,DEN 35,8,3,DEN kicks off 65 yards touchback (no return)
2,8:45,1,10,SEA 30,8,3,SEA rush for 3 yards
2,8:05,2,7,SEA 33,8,3,SEA pass incomplete short left. Penalty on DEN: "Defensive Pass Interference" +6 yards
2,8:00,1,10,SEA 39,8,3,SEA rush for -4 yards
2,7:14,2,14,SEA 35,8,3,SEA pass complete short middle for 12 yards
2,6:38,3,2,SEA 47,8,3,SEA pass complete deep left for 19 yards
2,6:04,1,10,DEN 34,8,9,SEA Rush - Touchdown for 34 yards
2,5:55,,,DEN 2,8,9,Two Point Attempt: SEA pass incomplete
2,5:55,,,SEA 35,8,9,SEA kicks off 65 yards touchback (no gain)
2,5:55,1,10,DEN 30,8,9,DEN pass incomplete short left
2,5:49,2,10,DEN 30,8,9,DEN rush for 1 yard
2,5:18,,,,,,SEA - Timeout #1,,
2,5:18,3,9,DEN 31,8,9,DEN pass complete short left for 3 yards
2,4:47,4,6,DEN 34,8,9,DEN punts 65 yards
2,4:36,1,10,SEA 1,10,9,SEA right guard for -1 yards, SEA Safety
2,4:24,,,SEA 20,10,9,SEA kicks off 70 yards returned by DEN for 26 yards
2,4:14,1,10,DEN 36,10,9,DEN rush for 2 yards
2,3:41,2,8,DEN 38,10,9,DEN pass complete short left for no gain
2,2:57,,,,,,DEN - Timeout #1,,
2,2:57,3,8,DEN 38,10,9,DEN pass complete deep left for 17 yards
2,2:12,1,10,SEA 45,10,9,DEN rush for 4 yards
2,2:00,2,6,SEA 41,10,9,DEN pass incomplete short middle
2,1:56,3,6,SEA 41,10,9,DEN sacked by SEA for -6 yards
2,1:48,4,12,SEA 47,10,9,DEN punts 37 yards
2,1:41,1,10,SEA 10,10,9,SEA sacked by DEN for -6 yards
2,1:33,,,,,,DEN - Timeout #2,,
2,1:33,2,16,SEA 4,10,9,SEA pass complete short left for -2 yards
2,1:28,,,,,,DEN - Timeout #3,,
2,1:28,3,18,SEA 2,10,9,SEA rush for 9 yards
2,0:42,,,,,,SEA - Timeout #2,,
2,0:42,4,9,SEA 11,10,9,SEA punts 52 yards returned by DEN for 10 yards
2,0:31,1,10,DEN 47,10,9,DEN rush for 1 yard
2,0:24,2,9,DEN 48,10,9,DEN pass incomplete deep middle
2,0:19,,,,,,SEA - Timeout #3,,
2,0:19,3,9,DEN 48,10,9,DEN pass complete deep left for 25 yards
2,0:13,1,10,SEA 27,10,9,DEN sacked by SEA for 0 yards (no gain)
2,0:07,2,10,SEA 27,13,9,DEN FG - Success, 45 yard field goal good
2,0:02,,,DEN 35,13,9,DEN kicks off 63 yards returned by SEA for 14 yards
3,15:00,,,SEA 35,13,9,SEA kicks off 64 yards returned by DEN for 26 yards
3,14:55,1,10,DEN 27,13,9,DEN rush for -4 yards
3,14:13,2,14,DEN 23,13,9,DEN pass incomplete short left
3,14:10,3,14,DEN 23,13,9,Penalty on DEN: "Delay of Game" -5 yards
3,14:10,3,19,DEN 18,13,9,DEN pass complete short right for 1 yard
3,13:36,4,18,DEN 19,13,9,DEN punts for 42 yards
3,13:31,1,10,SEA 39,13,9,SEA rush for 5 yards
3,13:10,2,5,SEA 44,13,9,SEA pass complete deep right for 13 yards
3,12:40,1,10,DEN 43,13,9,SEA rush for 6 yards
3,12:05,2,4,DEN 37,13,9,SEA rush for 15 yards
3,11:43,1,10,DEN 22,13,9,Penalty on SEA: "False Start" -5 yards
3,11:27,1,15,DEN 27,13,9,SEA rush for 4 yards
3,10:51,2,11,DEN 23,13,15,SEA rush - Touchdown for 23 yards
3,10:44,,,DEN 15,13,16,SEA XP - made
3,10:44,,,SEA 35,13,16,SEA kicks off 65 yards touchback (no gain)
3,10:44,1,10,DEN 30,13,16,DEN pass complete short right for 2 yards
3,10:03,2,8,DEN 32,13,16,DEN rush for 15 yards
3,9:23,1,10,DEN 47,13,16,DEN rush for 2 yards
3,8:42,2,8,DEN 49,13,16,DEN rush for -3 yards
3,8:12,3,11,DEN 46,13,16,DEN pass complete short left for 1 yard. DEN fumbles recovered by SEA at DEN 47
3,8:03,1,10,DEN 47,13,16,SEA rush for 6 yards
3,7:29,2,4,DEN 41,13,16,SEA rush for 3 yards
3,6:49,3,1,DEN 38,13,16,SEA rush for 2 yards
3,6:10,1,10,DEN 36,13,16,SEA pass incomplete deep right. Penalty on DEN: "Defensive Pass Interference" +14 yards
3,6:04,1,10,DEN 22,13,16,SEA rush for 8 yards
3,5:31,2,2,DEN 14,13,16,SEA pass complete short right for 8 yards
3,4:52,1,6,DEN 6,13,16,SEA rush - Touchdown for 2 yards. Penalty on SEA: "Offensive Holding" -10 yards, touchdown removed
3,4:48,1,14,DEN 14,13,16,SEA pass incomplete short right
3,4:45,2,14,DEN 14,13,16,SEA rush for 4 yards
3,4:19,3,10,DEN 10,13,16,SEA pass incomplete short left
3,4:12,4,10,DEN 10,13,19,SEA FG - Success, 28 yard field goal good
3,4:09,,,SEA 35,13,19,SEA kicks off 65 yards touchback (no gain)
3,4:09,1,10,DEN 30,13,19,DEN rush for 1 yard
3,3:30,2,9,DEN 31,13,19,DEN pass complete short left for 4 yards
3,2:48,3,5,DEN 35,13,19,DEN pass incomplete short right
3,2:46,4,5,DEN 35,13,19,DEN punts 65 yards touchback (no return)
3,2:37,1,10,SEA 20,13,19,SEA rush for no gain
3,2:02,2,10,SEA 20,13,19,SEA rush for 12 yards
3,1:35,1,10,SEA 32,13,19,SEA pass complete short right for 16 yards
3,0:57,1,10,SEA 48,13,19,SEA pass complete short left for 11 yards
3,0:15,1,10,DEN 41,13,19,SEA pass complete short right for 11 yards
4,15:00,1,10,DEN 30,13,25,SEA pass - touchdown deep right for 30 yards
4,14:54,,,DEN 15,13,26,SEA XP - made
4,14:54,,,SEA 35,13,26,SEA kicks off 65 yards touchback (no gain)
4,14:54,1,10,DEN 30,13,26,DEN rush for no gain
4,14:12,2,10,DEN 30,13,26,DEN pass complete short right for 4 yards
4,13:43,3,6,DEN 34,13,26,DEN pass complete short middle for 5 yards
4,13:07,4,1,DEN 39,13,26,DEN punts 49 yards
4,12:59,1,10,SEA 12,13,26,SEA rush for -4 yards
4,12:15,2,14,SEA 8,13,26,SEA pass complete short left for 9 yards
4,11:38,3,5,SEA 17,13,26,SEA pass complete short right for 6 yards
4,10:57,1,10,SEA 23,13,26,SEA rush for 1 yard
4,10:11,2,9,SEA 24,13,26,SEA rush for 7 yards
4,9:30,3,2,SEA 31,13,26,SEA pass complete short right for -1 yards
4,8:47,4,3,SEA 30,13,26,SEA punts 50 yards returned by DEN for 3 yards
4,8:37,1,10,DEN 23,13,26,DEN pass complete short middle for 6 yards
4,8:11,2,4,DEN 29,13,26,DEN pass complete short left for 7 yards
4,7:34,1,10,DEN 36,13,26,DEN pass incomplete deep right
4,7:26,2,10,DEN 36,13,26,DEN rush for 1 yard
4,6:46,3,9,DEN 37,13,26,DEN pass complete short left for 7 yards
4,6:05,4,2,DEN 44,13,26,DEN pass complete short middle for 7 yards
4,5:40,1,10,SEA 49,13,26,DEN pass incomplete deep left
4,5:37,2,10,SEA 49,13,26,DEN pass short right is intercepted by SEA at SEA 34 and returned for 4 yards
4,5:30,1,10,SEA 38,13,26,SEA rush for 1 yard
4,4:50,2,9,SEA 39,13,26,SEA pass complete short right for no gain. Penalty on SEA: "Offensive Pass Interference" -10 yards
4,4:44,,,,,,SEA - Timeout #1,,
4,4:44,2,19,SEA 29,13,26,SEA pass incomplete short right
4,4:42,3,19,SEA 29,13,26,SEA rush for no gain
4,4:37,,,,,,DEN - Timeout #1,,
4,4:37,4,19,SEA 29,13,26,SEA punts 43 yards returned by DEN for 18 yards
4,4:28,1,10,DEN 46,13,26,DEN rush for 23 yards
4,4:20,1,10,SEA 31,13,26,DEN pass incomplete short middle
4,4:17,2,10,SEA 31,13,26,DEN pass complete short left for 7 yards
4,3:35,3,3,SEA 24,13,26,DEN pass complete short right for 5 yards
4,3:10,1,10,SEA 19,13,26,DEN pass complete short right for 5 yards
4,2:43,2,5,SEA 14,13,26,DEN pass complete short middle for 10 yards
4,2:17,1,4,SEA 4,19,26,DEN rush - Touchdown for 4 yards
4,2:09,,,SEA 15,20,26,DEN XP - Made
4,2:09,,,DEN 35,20,26,DEN kicks off 65 yards touchback (no gain)
4,2:09,1,10,SEA 30,20,26,SEA rush for no gain
4,2:00,2,10,SEA 30,20,26,SEA rush for -1 yards
4,1:54,,,,,,DEN - Timeout #2,,
4,1:54,3,11,SEA 29,20,26,SEA pass complete short left for no gain. Penalty on DEN: "Defensive Offside" +5 yards
4,1:48,3,6,SEA 34,20,26,SEA pass complete short right for 9 yards
4,1:40,,,,,,DEN - Timeout #3,,
4,1:40,1,10,SEA 43,20,26,SEA rush (kneels) for -2 yards
4,0:59,2,12,SEA 41,20,26,SEA rush (kneels) for -1 yards
4,0:38,3,13,SEA 40,20,26,SEA rush (kneels) for -1 yards

- GB vs PHI
Quarter,Time,Down,ToGo,Location,GNB,PHI,Detail,EPB,EPA
,,,,,,,Packers won the coin toss Packers to receive the opening kickoff.,,
1,15:00,,,PHI 35,0,0,Braden Mann kicks off 65 yards touchback.. Penalty on Kelee Ringo: Illegal Formation 5 yards (accepted),0.000,1.270
1,15:00,1,10,GNB 35,0,0,Josh Jacobs up the middle for -1 yards (tackle by Josh Sweat and Milton Williams),1.270,0.590
1,14:21,2,11,GNB 34,0,0,Jordan Love pass complete short right to Christian Watson for 5 yards (tackle by Avonte Maddox and Darius Slay),0.590,0.560
1,13:39,3,6,GNB 39,0,0,Jordan Love pass complete short left to Romeo Doubs for 19 yards (tackle by Avonte Maddox and Reed Blankenship),0.560,2.790
1,12:59,1,10,PHI 42,0,0,Josh Jacobs left guard for 4 yards (tackle by Zack Baun and Quinyon Mitchell),2.790,2.780
1,12:17,2,6,PHI 38,0,0,Josh Jacobs left tackle for no gain (tackle by Zack Baun and Nolan Smith),2.780,2.080
1,12:01,3,6,PHI 38,0,0,Jordan Love pass complete deep middle to Jayden Reed for no gain touchdown. Penalty on Romeo Doubs: Offensive Too Many Men on Field 5 yards (offset) . Penalty on Bryce Huff: Defensive Too Many Men on Field 5 yards (offset) (no play),2.080,2.080
1,11:52,3,6,PHI 38,0,0,Jordan Love pass incomplete deep left intended for Christian Watson,2.080,0.720
1,11:47,,,,,,Timeout #1 by Green Bay Packers,,
1,11:47,4,6,PHI 38,0,0,Penalty on Zach Tom: False Start 5 yards (accepted) (no play),0.720,0.400
1,11:47,4,11,PHI 43,0,0,Daniel Whelan punts 33 yards out of bounds,0.400,0.380
1,11:40,1,10,PHI 10,0,0,Saquon Barkley left end for -5 yards (tackle by Eric Wilson),-0.380,-1.150
1,11:03,2,15,PHI 5,0,0,Jalen Hurts pass incomplete deep left intended for A.J. Brown,-1.150,-2.010
1,10:57,3,15,PHI 5,0,0,Jalen Hurts pass deep left intended for DeVonta Smith is intercepted by Xavier McKinney at PHI-36 and returned for 17 yards (tackle by Jordan Mailata),-2.010,-4.310
1,10:46,1,10,PHI 19,0,0,Jordan Love sacked by Zack Baun for -5 yards. Penalty on Jalen Carter: Unnecessary Roughness / Defense 12 yards (accepted),4.310,4.780
1,10:19,1,10,PHI 12,0,0,Josh Jacobs right tackle for no gain (tackle by C.J. Gardner-Johnson),4.780,4.100
1,9:39,2,10,PHI 12,0,0,Jordan Love pass complete short right to Luke Musgrave for no gain (tackle by C.J. Gardner-Johnson). Penalty on Rasheed Walker: Offensive Holding 10 yards (accepted) (no play),4.100,2.870
1,9:17,2,20,PHI 22,0,0,Jordan Love pass incomplete short middle intended for Dontayvion Wicks,2.870,2.210
1,9:13,3,20,PHI 22,0,0,Jordan Love pass complete short left to Josh Jacobs for 9 yards (tackle by Avonte Maddox and Thomas Booker),2.210,2.570
1,8:30,4,11,PHI 13,3,0,Brayden Narveson 31 yard field goal good,2.570,3.000
1,8:26,,,GNB 35,3,0,Brayden Narveson kicks off 61 yards recovered by Kenneth Gainwell at PHI-7 (tackle by Edgerrin Cooper),0.000,-0.140
1,8:21,1,10,PHI 16,3,0,Saquon Barkley right guard for 3 yards (tackle by Tedarrell Slaton),-0.140,-0.180
1,7:46,2,7,PHI 19,3,0,Jalen Hurts pass complete short left to Dallas Goedert for 1 yard (tackle by Javon Bullard),-0.180,-0.820
1,7:11,3,6,PHI 20,3,0,Jalen Hurts aborted snap recovered by Devonte Wyatt at PHI-14,-0.820,-4.710
1,7:06,1,10,PHI 13,3,0,Josh Jacobs right end for no gain (tackle by Darius Slay). Penalty on Tucker Kraft: Offensive Holding 10 yards (accepted) (no play),4.710,4.040
1,6:43,1,20,PHI 23,3,0,Jordan Love pass complete short right to Jayden Reed for 9 yards (tackle by Reed Blankenship and Zack Baun),4.040,3.900
1,5:58,2,11,PHI 14,3,0,Jordan Love pass complete short right to Romeo Doubs for 10 yards (tackle by Darius Slay),3.900,5.010
1,5:27,3,1,PHI 4,3,0,Josh Jacobs up the middle for -1 yards (tackle by Nakobe Dean and Brandon Graham),5.010,3.030
1,4:44,4,2,PHI 5,3,0,Josh Jacobs right tackle for 2 yards (tackle by C.J. Gardner-Johnson and Zack Baun),3.030,6.510
1,3:58,,,,,,Timeout #2 by Green Bay Packers,,
1,3:58,1,3,PHI 3,3,0,Jordan Love pass incomplete short left intended for Christian Watson. Penalty on Romeo Doubs: Offensive Pass Interference 10 yards (accepted) (no play),6.510,4.530
1,3:53,1,13,PHI 13,3,0,Jordan Love pass complete short right to Tucker Kraft for 8 yards (tackle by C.J. Gardner-Johnson and Jalen Carter),4.530,5.150
1,3:15,2,5,PHI 5,3,0,Jordan Love pass incomplete short right,5.150,4.260
1,3:09,3,5,PHI 5,3,0,Jordan Love pass incomplete short right intended for Christian Watson,4.260,3.010
1,3:04,4,5,PHI 5,6,0,Brayden Narveson 23 yard field goal good,3.010,3.000
1,3:01,,,GNB 35,6,0,Brayden Narveson kicks off 65 yards touchback.,0.000,0.940
1,3:01,1,10,PHI 30,6,0,Jalen Hurts pass complete short right to Dallas Goedert for 4 yards (tackle by Jaire Alexander),0.940,0.930
1,2:23,2,6,PHI 34,6,0,Saquon Barkley left tackle for 5 yards (tackle by Quay Walker),0.930,0.890
1,1:40,3,1,PHI 39,6,0,Jalen Hurts pass complete short left to A.J. Brown for 8 yards (tackle by Jaire Alexander). Penalty on Kenny Clark: Defensive Offside 5 yards (declined),0.890,2.060
1,1:21,1,10,PHI 47,6,0,Jalen Hurts right end for 2 yards (tackle by Keisean Nixon),2.060,1.790
1,0:39,2,8,PHI 49,6,0,Jalen Hurts pass complete short right to DeVonta Smith for 8 yards (tackle by Karl Brooks and Quay Walker),1.790,2.720
1,0:06,1,10,GNB 43,6,0,Jalen Hurts pass complete short left to DeVonta Smith for 9 yards (tackle by Keisean Nixon),2.720,3.390
2,15:00,2,1,GNB 34,6,0,Jalen Hurts pass incomplete short right,3.390,2.680
2,14:54,3,1,GNB 34,6,0,Jalen Hurts up the middle for no gain (tackle by Quay Walker),2.680,1.100
2,14:21,4,1,GNB 34,6,0,Penalty on Tedarrell Slaton: Encroachment 6 yards (accepted) (no play),1.100,3.710
2,14:08,1,10,GNB 28,6,0,Saquon Barkley up the middle for 11 yards (tackle by Xavier McKinney),3.710,4.440
2,13:27,1,10,GNB 17,6,0,Saquon Barkley up the middle for -1 yards (tackle by Eric Wilson and Tedarrell Slaton),4.440,3.720
2,12:43,2,11,GNB 18,6,6,Jalen Hurts pass complete deep left to Saquon Barkley for 18 yards touchdown,3.720,7.000
2,12:38,,,GNB 15,6,7,Jake Elliott kicks extra point good,0.000,0.000
2,12:38,,,PHI 35,6,7,Braden Mann kicks off 65 yards touchback.,0.000,0.940
2,12:38,1,10,GNB 30,6,7,Jordan Love pass incomplete deep left intended for Dontayvion Wicks,0.940,0.390
2,12:33,2,10,GNB 30,6,7,Emanuel Wilson left tackle for 14 yards (tackle by Reed Blankenship),0.390,1.860
2,11:57,1,10,GNB 44,6,7,Emanuel Wilson left end for 5 yards (tackle by Zack Baun),1.860,1.990
2,11:13,2,5,GNB 49,6,7,Emanuel Wilson up the middle for 18 yards (tackle by Zack Baun),1.990,3.380
2,10:24,,,,,,Timeout #3 by Green Bay Packers,,
2,10:24,1,10,PHI 33,12,7,Jayden Reed right end for 33 yards touchdown,3.380,7.000
2,10:16,,,PHI 15,12,7,Brayden Narveson kicks extra point good. Penalty on Jalen Carter: Defensive Offside 1 yard (accepted) (no play),0.000,1.000
2,10:16,,,PHI 1,12,7,Two Point Attempt: Josh Jacobs rushes conversion fails.,1.000,-1.000
2,10:16,,,GNB 35,12,7,Brayden Narveson kicks off 65 yards touchback.,0.000,0.940
2,10:16,1,10,PHI 30,12,7,Saquon Barkley left tackle for 2 yards (tackle by Karl Brooks and Isaiah McDuffie),0.940,0.660
2,9:33,2,8,PHI 32,12,7,Jalen Hurts pass incomplete deep right,0.660,-0.030
2,9:26,3,8,PHI 32,12,7,Jalen Hurts pass complete short left to A.J. Brown for 20 yards (tackle by Xavier McKinney),-0.030,2.390
2,8:47,1,10,GNB 48,12,7,Jalen Hurts right end for 1 yard (tackle by Quay Walker),2.390,1.980
2,8:04,2,9,GNB 47,12,7,Jalen Hurts pass complete short left to DeVonta Smith for 25 yards (tackle by Javon Bullard),1.980,4.110
2,7:19,1,10,GNB 22,12,7,Jalen Hurts pass complete short left to Saquon Barkley for 5 yards (tackle by Quay Walker),4.110,4.290
2,6:40,2,5,GNB 17,12,7,Jalen Hurts right guard for 4 yards (tackle by Edgerrin Cooper and Tedarrell Slaton),4.290,4.390
2,6:08,3,1,GNB 13,12,7,Jalen Hurts up the middle for 2 yards (tackle by Quay Walker),4.390,4.840
2,5:38,1,10,GNB 11,12,13,Saquon Barkley right guard for 11 yards touchdown,4.840,7.000
2,5:34,,,GNB 15,12,14,Jake Elliott kicks extra point good,0.000,0.000
2,5:34,,,PHI 35,12,14,Braden Mann kicks off 65 yards touchback.,0.000,0.940
2,5:34,1,10,GNB 30,12,14,Jordan Love pass incomplete short right intended for Josh Jacobs,0.940,0.390
2,5:29,2,10,GNB 30,12,14,Jordan Love pass incomplete deep left intended for Luke Musgrave (defended by Quinyon Mitchell),0.390,-0.300
2,5:22,3,10,GNB 30,18,14,Jordan Love pass complete deep right to Jayden Reed for 70 yards touchdown,-0.300,7.000
2,5:11,,,PHI 15,18,14,Penalty on Rasheed Walker: False Start 5 yards (accepted) (no play),0.000,0.000
2,5:11,,,PHI 20,19,14,Brayden Narveson kicks extra point good,0.000,0.000
2,5:11,,,GNB 35,19,14,Brayden Narveson kicks off 71 yards returned by Kenneth Gainwell for 29 yards (tackle by Zayne Anderson),0.000,0.480
2,5:06,1,10,PHI 23,19,14,Jalen Hurts pass incomplete short middle intended for A.J. Brown,0.480,-0.070
2,5:01,2,10,PHI 23,19,14,Jalen Hurts pass complete short left to A.J. Brown for 13 yards (tackle by Jaire Alexander),-0.070,1.330
2,4:20,1,10,PHI 36,19,14,Jalen Hurts pass incomplete short right,1.330,0.790
2,4:11,2,10,PHI 36,19,14,Saquon Barkley right end for 3 yards (tackle by Karl Brooks),0.790,0.500
2,3:33,3,7,PHI 39,19,14,Jalen Hurts pass complete short left to Dallas Goedert for 21 yards (tackle by Jaire Alexander),0.500,2.920
2,2:54,1,10,GNB 40,19,14,Saquon Barkley up the middle for 2 yards (tackle by Lukas Van Ness),2.920,2.640
2,2:10,2,8,GNB 38,19,14,Jalen Hurts pass incomplete deep right intended for Johnny Wilson,2.640,1.950
2,2:03,3,8,GNB 38,19,14,Jalen Hurts up the middle for 5 yards (tackle by Rashan Gary and Kenny Clark),1.950,1.190
2,1:59,,,,,,Timeout #1 by Philadelphia Eagles,,
2,1:59,4,3,GNB 33,19,14,Jalen Hurts pass complete short left to DeVonta Smith for 7 yards (tackle by Isaiah McDuffie),1.190,3.840
2,1:24,1,10,GNB 26,19,14,Kenneth Gainwell up the middle for 2 yards (tackle by Kenny Clark),3.840,3.570
2,0:53,2,8,GNB 24,19,14,Jalen Hurts pass complete short left to Kenneth Gainwell for 10 yards (tackle by Xavier McKinney and Quay Walker),3.570,4.650
2,0:43,,,,,,Timeout #2 by Philadelphia Eagles,,
2,0:43,1,10,GNB 14,19,14,Jalen Hurts sacked by Keisean Nixon for -6 yards,4.650,3.280
2,0:31,,,,,,Timeout #3 by Philadelphia Eagles,,
2,0:31,2,16,GNB 20,19,14,Penalty on Dallas Goedert: False Start 5 yards (accepted) (no play),3.280,2.610
2,0:31,2,21,GNB 25,19,14,Jalen Hurts pass complete short left to Dallas Goedert for 5 yards (tackle by Javon Bullard),2.610,2.610
2,0:05,3,16,GNB 20,19,14,Jalen Hurts spiked the ball,2.610,2.120
2,0:04,4,16,GNB 20,19,17,Jake Elliott 38 yard field goal good,2.120,3.000
3,15:00,,,GNB 35,19,17,Brayden Narveson kicks off 65 yards touchback.,0.000,0.940
3,15:00,1,10,PHI 30,19,17,Saquon Barkley right tackle for 3 yards (tackle by Eric Wilson),0.940,0.800
3,14:22,2,7,PHI 33,19,23,Jalen Hurts pass complete deep right to A.J. Brown for 67 yards touchdown,0.800,7.000
3,14:09,,,GNB 15,19,24,Jake Elliott kicks extra point good,0.000,0.000
3,14:09,,,PHI 35,19,24,Braden Mann kicks off 65 yards touchback.,0.000,0.940
3,14:09,1,10,GNB 30,19,24,Jordan Love pass incomplete deep left,0.940,0.390
3,13:59,2,10,GNB 30,19,24,Josh Jacobs right guard for 5 yards. Josh Jacobs fumbles (forced by Darius Slay) recovered by Bo Melton at GB-39,0.390,0.890
3,13:31,3,1,GNB 39,19,24,Jordan Love pass incomplete short left intended for Bo Melton. Penalty on Quinyon Mitchell: Defensive Pass Interference 15 yards (declined) . Penalty on Zack Baun: Roughing the Passer 16 yards (accepted) (no play),0.890,2.590
3,13:25,1,10,PHI 45,19,24,Jordan Love pass complete short left to Tucker Kraft for 29 yards (tackle by Reed Blankenship),2.590,4.510
3,12:45,1,10,PHI 16,19,24,Josh Jacobs left guard for 1 yard (tackle by Zack Baun and C.J. Gardner-Johnson),4.510,4.050
3,12:06,2,9,PHI 15,19,24,Jordan Love pass incomplete short right intended for Tucker Kraft,4.050,3.220
3,12:01,3,9,PHI 15,19,24,Jordan Love pass incomplete short right intended for Dontayvion Wicks. Penalty on Avonte Maddox: Defensive Pass Interference 13 yards (accepted) (no play),3.220,6.740
3,11:58,1,2,PHI 2,25,24,Jordan Love pass complete short left to Christian Watson for 2 yards touchdown,6.740,7.000
3,11:55,,,PHI 15,26,24,Brayden Narveson kicks extra point good,0.000,0.000
3,11:55,,,GNB 35,26,24,Brayden Narveson kicks off 65 yards touchback.,0.000,0.940
3,11:55,1,10,PHI 30,26,24,Jalen Hurts pass incomplete short right intended for Dallas Goedert (defended by Rashan Gary),0.940,0.390
3,11:50,2,10,PHI 30,26,24,Saquon Barkley right end for 2 yards (tackle by Edgerrin Cooper),0.390,-0.030
3,11:25,3,8,PHI 32,26,24,Jalen Hurts sacked by Rashan Gary for -6 yards,-0.030,-1.630
3,10:33,4,14,PHI 26,26,24,Braden Mann punts 54 yards fair catch by Jayden Reed at GB-20,-1.630,-0.280
3,10:26,1,10,GNB 20,26,24,Jordan Love pass complete short left to Romeo Doubs for 12 yards (tackle by Quinyon Mitchell),0.280,1.070
3,9:46,1,10,GNB 32,26,24,Emanuel Wilson right guard for no gain (tackle by Zack Baun). Penalty on Tucker Kraft: Illegal Shift 5 yards (accepted) (no play),1.070,0.740
3,9:23,1,15,GNB 27,26,24,Jordan Love pass incomplete deep left intended for Jayden Reed,0.740,-0.150
3,9:18,2,15,GNB 27,26,24,Jordan Love pass incomplete short middle intended for Romeo Doubs,-0.150,-0.820
3,9:13,3,15,GNB 27,26,24,Jordan Love pass incomplete short left intended for Emanuel Wilson (defended by Nakobe Dean). Penalty on Tucker Kraft: Illegal Shift 5 yards (declined),-0.820,-1.570
3,9:08,4,15,GNB 27,26,24,Daniel Whelan punts returned by Britain Covey for no gain (tackle by Eric Wilson). Penalty on Isaiah McDuffie: Illegal Formation 5 yards (offset) . Penalty on Tristin McCollum: Illegal Blindside Block 15 yards (offset) (no play),-1.570,-1.570
3,8:56,4,15,GNB 27,26,24,Daniel Whelan punts 41 yards fair catch by Britain Covey at PHI-32,-1.570,-1.070
3,8:47,1,10,PHI 32,26,24,Saquon Barkley left tackle for 8 yards (tackle by Keisean Nixon),1.070,1.610
3,8:12,2,2,PHI 40,26,24,Jalen Hurts left end for no gain (tackle by Preston Smith),1.610,0.890
3,7:32,3,2,PHI 40,26,24,Jalen Hurts pass incomplete short right,0.890,-0.720
3,7:27,4,2,PHI 40,26,24,Braden Mann punts 47 yards fair catch by Jayden Reed at GB-13,-0.720,0.320
3,7:21,1,10,GNB 13,26,24,Josh Jacobs up the middle for 9 yards (tackle by Reed Blankenship and Bryce Huff),-0.320,0.490
3,6:37,2,1,GNB 22,26,24,Josh Jacobs left end for no gain (tackle by C.J. Gardner-Johnson and Quinyon Mitchell). Penalty on Rasheed Walker: Offensive Holding 10 yards (accepted) (no play),0.490,-0.850
3,6:14,2,11,GNB 12,26,24,Jordan Love pass short middle intended for Luke Musgrave is intercepted by Reed Blankenship at GB-25 and returned for 1 yard (tackle by Dontayvion Wicks),-0.850,-3.970
3,6:08,1,10,GNB 24,26,24,Jalen Hurts pass complete short right to DeVonta Smith for 8 yards (tackle by Isaiah McDuffie),3.970,4.650
3,5:35,2,2,GNB 16,26,24,Saquon Barkley left guard for 5 yards (tackle by Lukas Van Ness and Javon Bullard),4.650,4.840
3,5:04,1,10,GNB 11,26,24,Saquon Barkley up the middle for 9 yards (tackle by Keisean Nixon and Javon Bullard),4.840,5.850
3,4:32,2,1,GNB 2,26,30,Saquon Barkley up the middle for 2 yards touchdown,5.850,7.000
3,4:26,,,GNB 15,26,31,Jake Elliott kicks extra point good,0.000,0.000
3,4:26,,,PHI 35,26,31,Braden Mann kicks off 65 yards touchback.,0.000,0.940
3,4:26,1,10,GNB 30,26,31,Jordan Love pass complete short left to Romeo Doubs for 9 yards (tackle by Nakobe Dean),0.940,1.610
3,3:48,2,1,GNB 39,26,31,Josh Jacobs up the middle for 3 yards (tackle by Zack Baun),1.610,1.730
3,3:10,1,10,GNB 42,26,31,Jordan Love pass incomplete short left intended for Dontayvion Wicks (defended by Quinyon Mitchell),1.730,1.190
3,3:07,2,10,GNB 42,26,31,Jordan Love pass complete short right to Josh Jacobs for 11 yards (tackle by Jalen Carter and C.J. Gardner-Johnson),1.190,2.460
3,2:32,1,10,PHI 47,26,31,Josh Jacobs right guard for 22 yards (tackle by Reed Blankenship),2.460,3.910
3,1:51,1,10,PHI 25,26,31,Josh Jacobs right end for no gain (tackle by Zack Baun),3.910,3.360
3,1:10,2,10,PHI 25,26,31,Jordan Love pass complete short left to Emanuel Wilson for no gain (tackle by Zack Baun),3.360,2.680
3,0:24,3,10,PHI 25,26,31,Jordan Love pass incomplete short left intended for Jayden Reed,2.680,1.800
3,0:21,4,10,PHI 25,26,31,Brayden Narveson 43 yard field goal no good,1.800,-1.140
3,0:17,1,10,PHI 33,26,31,Saquon Barkley left guard for 34 yards (tackle by Javon Bullard),1.140,3.380
4,15:00,1,10,GNB 33,26,31,Jalen Hurts pass incomplete short right intended for A.J. Brown (defended by Edgerrin Cooper),3.380,2.840
4,14:57,2,10,GNB 33,26,31,Jalen Hurts pass complete short right to Grant Calcaterra for 11 yards (tackle by Javon Bullard),2.840,4.110
4,14:28,1,10,GNB 22,26,31,Jalen Hurts pass complete short left to A.J. Brown for 11 yards (tackle by Javon Bullard and Keisean Nixon),4.110,4.840
4,13:51,1,10,GNB 11,26,31,Jalen Hurts left guard for -3 yards (tackle by Keisean Nixon and Preston Smith),4.840,3.690
4,13:14,2,13,GNB 14,26,31,Jalen Hurts pass incomplete short left intended for Jahan Dotson (defended by Keisean Nixon),3.690,3.030
4,13:09,3,13,GNB 14,26,31,Jalen Hurts pass deep middle intended for A.J. Brown is intercepted by Jaire Alexander in end zone and returned for 17 yards (tackle by DeVonta Smith),3.030,0.320
4,12:53,1,10,GNB 13,26,31,Josh Jacobs left tackle for 32 yards (tackle by Nakobe Dean),-0.320,1.930
4,12:16,1,10,GNB 45,26,31,Josh Jacobs left tackle for -1 yards (tackle by Nakobe Dean),1.930,1.250
4,11:36,2,11,GNB 44,26,31,Jordan Love pass complete short middle to Christian Watson for 6 yards (tackle by Quinyon Mitchell),1.250,1.360
4,10:56,3,5,GNB 50,26,31,Jordan Love pass complete deep left to Jayden Reed for 26 yards (tackle by Reed Blankenship),1.360,3.970
4,10:11,1,10,PHI 24,26,31,Emanuel Wilson left guard for 9 yards (tackle by Quinyon Mitchell and Jalen Carter),3.970,4.850
4,9:28,2,1,PHI 15,26,31,Jordan Love pass complete short right to Emanuel Wilson for 2 yards (tackle by Zack Baun),4.850,4.710
4,8:48,1,10,PHI 13,26,31,Josh Jacobs right guard for 5 yards (tackle by Zack Baun and Reed Blankenship),4.710,4.960
4,8:06,2,5,PHI 8,26,31,Jordan Love pass incomplete short left,4.960,4.100
4,8:00,3,5,PHI 8,26,31,Jordan Love pass incomplete short middle intended for Romeo Doubs,4.100,2.970
4,7:56,4,5,PHI 8,29,31,Brayden Narveson 26 yard field goal good,2.970,3.000
4,7:52,,,GNB 35,29,31,Brayden Narveson kicks off 65 yards touchback.,0.000,0.940
4,7:52,1,10,PHI 30,29,31,Saquon Barkley left end for -2 yards (tackle by Quay Walker),0.940,0.120
4,7:08,2,12,PHI 28,29,31,Jalen Hurts pass incomplete deep right. Penalty on Jaire Alexander: Defensive Holding 5 yards (accepted) (no play),0.120,1.140
4,6:59,1,10,PHI 33,29,31,Saquon Barkley up the middle for 9 yards (tackle by Isaiah McDuffie),1.140,1.810
4,6:17,2,1,PHI 42,29,31,Saquon Barkley up the middle for 2 yards (tackle by Edgerrin Cooper and Rashan Gary),1.810,1.860
4,5:33,,,,,,Timeout #1 by Philadelphia Eagles,,
4,5:33,1,10,PHI 44,29,31,Jalen Hurts scrambles right tackle for 8 yards (tackle by Javon Bullard),1.860,2.400
4,4:54,2,2,GNB 48,29,31,Jalen Hurts pass complete short right to Kenneth Gainwell for no gain (tackle by Eric Stokes). Penalty on Lane Johnson: Illegal Formation 5 yards (accepted) (no play),2.400,1.720
4,4:48,2,7,PHI 47,29,31,Jalen Hurts pass incomplete short right intended for A.J. Brown (defended by Jaire Alexander). Penalty on Cam Jurgens: Ineligible Downfield Pass 5 yards (declined),1.720,1.030
4,4:32,3,7,PHI 47,29,31,Jalen Hurts scrambles up the middle for 6 yards (tackle by Quay Walker and Karl Brooks). Penalty on Kenny Clark: Defensive Holding 5 yards (accepted),1.030,2.790
4,4:32,1,10,GNB 42,29,31,Saquon Barkley left tackle for 2 yards (tackle by Isaiah McDuffie and Eric Wilson),2.790,2.510
4,3:48,2,8,GNB 40,29,31,Jalen Hurts scrambles left end for 8 yards (tackle by Eric Stokes and Quay Walker),2.510,3.450
4,3:04,1,10,GNB 32,29,31,Saquon Barkley up the middle for -3 yards (tackle by Devonte Wyatt),3.450,2.500
4,2:22,2,13,GNB 35,29,31,Jalen Hurts pass complete short left to DeVonta Smith for 16 yards (tackle by Xavier McKinney),2.500,4.310
4,2:15,,,,,,Timeout #1 by Green Bay Packers,,
4,2:15,1,10,GNB 19,29,31,Saquon Barkley up the middle for 2 yards (tackle by Isaiah McDuffie and Tedarrell Slaton),4.310,4.030
4,2:10,,,,,,Timeout #2 by Green Bay Packers,,
4,2:10,2,8,GNB 17,29,31,Jalen Hurts pass complete short left to DeVonta Smith for 11 yards (tackle by Javon Bullard and Eric Stokes),4.030,5.830
4,2:00,1,6,GNB 6,29,31,Saquon Barkley up the middle for 2 yards (tackle by Javon Bullard),5.830,5.340
4,1:57,,,,,,Timeout #3 by Green Bay Packers,,
4,1:57,2,4,GNB 4,29,31,Saquon Barkley right guard for 3 yards (tackle by Quay Walker and Tedarrell Slaton),5.340,5.170
4,1:12,,,,,,Timeout #2 by Philadelphia Eagles,,
4,1:12,3,1,GNB 1,29,31,Jalen Hurts aborted snap recovered by Saquon Barkley at GB-2,5.170,3.040
4,0:30,,,,,,Timeout #3 by Philadelphia Eagles,,
4,0:30,4,3,GNB 3,29,34,Jake Elliott 21 yard field goal good. Penalty on Xavier McKinney: Defensive Offside 5 yards (declined),3.040,3.000
4,0:27,,,PHI 35,29,34,Braden Mann kicks off 73 yards returned by Keisean Nixon for 24 yards (tackle by Will Shipley and Nolan Smith),0.000,-0.140
4,0:22,1,10,GNB 16,29,34,Jordan Love pass complete deep left to Jayden Reed for 33 yards (tackle by Quinyon Mitchell),-0.140,2.190
4,0:15,1,10,GNB 49,29,34,Josh Jacobs left end for 4 yards (tackle by Zack Baun),2.190,2.190
4,0:06,2,6,PHI 47,29,34,Malik Willis pass incomplete short left intended for Romeo Doubs,2.190,1.490
4,0:03,3,6,PHI 47,29,34,Malik Willis sacked by Zack Baun for -4 yards,1.490,-0.130






display the simulation results.
<!-- Provide a detailed Python implementation and display the simulation results. -->