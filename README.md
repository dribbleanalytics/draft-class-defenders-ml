# METHODOLOGY: Using machine learning to predict the best defenders in the 2018 draft

[Link to blog post.](https://dribbleanalytics.blogspot.com/2018/06/using-machine-learning-to-predict-best.html
)

## Data collection

First, I created a database of all first round picks who played in college and were drafted in or later than the 2011 draft. The oldest draft used is 2011 because players before the 2011 draft class do not have college DBPM data.

171 players met these restrictions. The following stats were recorded for each player:

| College stats | NBA stats | Other information |
| ------------- | ------------- | ------------- |
| Seasons in college | G | Wingspan (in) |
| G | MPG | Height (in) |
| MPG  | STL/G | Position |
| STL/G  | BLK/G |
| BLK/G  | DWS |
| PF/G  | DWS/48 |
| STL% | DBPM |
| BLK% | |
| DWS | |
| DWS/40 | |
| DBPM | |

G and MPG statistics were recorded so I could calculate the DWS/40 or DWS/48 (by doing DWS/40 = DWS * 40 / MPG / G).

College data was taken from [Sports Reference](http://sports-reference.com/cbb). NBA data was taken from [Basketball Reference](http://basketball-reference.com).

Wingspan and height data was taken from my first article which analyzed how wingspan and height correlate to defense. The data collection for that article can be found [here](https://github.com/dribbleanalytics/wingspan-height-defense). I manually filled in the wingspan and height for players who did not play in the 2018 season (and were therefore not in the wingspan and height article's database).

## Data organization

After collecting all the data, I split up the data by position. This is important because without splitting up the data the models would be skewed - centers' defense does not depend on the same things as guards' defense.

Initially, the data was split into guards, forwards, and centers using the same positions in the wingspan and height article (from bbref). Players who were a guard/forward or forward/center were included in both positional groups.

## Model creation

Using scikit-learn, I created three models: a linear regression, a ridge regression, and a lasso regression. Each model used scikit-learn's train test split function with varying test set sizes.

The models were created to take most of the college stats and other information as inputs and predict the player's NBA DBPM. Though no defensive metric truly captures a player's defensive ability, DBPM can give us a general idea of the player's defensive impact.

Each model's mean squared error and variance score (rsquared) was measured to find the most accurate regression. Each model's cross-validation score for explained variance was also measured to help determine the most accurate model. Note that a higher rsquared and explained variance indicates a more accurate regression, but a lower mean squared error indicates a more accurate regression.

After finding these model accuracy metrics, the data was split up differently. The guards and centers regressions were accurate, but the forwards regression was very noisy. So, I split up the forwards regression into a SF regression and PF regression (players who were listed as SF/PF were in both regressions).

The SF regression was accurate to a similar extent as the guards and centers regressions. However, the PF regression was still noisy and inaccurate. Therefore, I did not plug in players to the PF regression to predict their defense.

## Predicting the best defenders in the draft

After making the models mentioned above, I recorded the college data (all the same parameters as the college data for the original 171 players) of college players in the top 35 of [The Ringer's 2018 NBA Draft Guide](http://nbadraft.theringer.com) as of the 6/13 update. Their defensive data was taken from Sports Reference, and the wingspan and height data was taken from The Ringer's mock draft.

The only college player who The Ringer projects in the first round but was not in the analysis is Michael Porter Jr., because he only played 3 games and 53 total minutes. Therefore, his sample size is too small for the model to make a reasonable projection for his defense.

The players were split into guards, forwards, and centers using The Ringer's listed positions. Players such as Jaren Jackson Jr. and Marvin Bagley III who were listed by The Ringer as "bigs" were tested in both the forwards and centers regression. Though the forwards regression ultimately was trained using only NBA SF or SF hybrids, and Jackson and Bamba will likely never play SF, they were still plugged into the SF regression. I tested them in both the centers and SF regressions because the SF regression should theoretically predict their ability to be not only a rim protector (which the centers regression would find) but also a perimeter defender. Therefore, I plugged them into the SF regression and analyzed their results. The same goes for other C/PF players.

Using the aforementioned methods, the players were plugged into the three models to predict their DBPM in the NBA.
