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



display the simulation results.
<!-- Provide a detailed Python implementation and display the simulation results. -->