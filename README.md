# Title

The Sabermetric Revolution: Have Sabermetric Stats Become More Reliable at Predicting MVPs and Cy Young Winners than Traditional Stats

# Problem Statement

Since the new millenium, Baseball has been dominated by one word: Sabermetrics.  These stats are said to be able to enumerate player performance into easily digestible and interpretible numbers.  Need to know who had the most successful offensive season?  WAR (Wins Above Replacement) will tell you what you need to know.  

Since Billy Beane built up the early 2000s Athletics with players who were regarded as poor and competed for (and won) the division against the Mariners and Angels, MLB front offices have adopted his techniques to build their teams on bigger budgets, often with great success.  

However, do the Baseball Writer's Association of America(BBWAA), the organization that votes on the official MVP and Cy Young Awards,feel the same way?  Or are they stuck in the past and still vote for whoever has the highest average or most pitcher wins?

I will attempt to train boosted models on resampled data in order to work around the problem of imbalanced classes inherent in baseball awards.  

# Other Examples

The most relevant example is [here](https://tht.fangraphs.com/mvp-2018-mookie-betts-christian-yelich-machine-learning/), where author Ryan Pollack of Fangraphs used 2018's data with a machine learning model in order to predict which players would win MVP.  Here, he overcame the issue of imbalanced classes by using a boosting model, and he used rankings for the stats (i.e, Mike Trout led the American League in OPS, so he received a 1 in that stat) instead of the raw stats.  He was able to correctly predict the 2018 MVPs using his model, and had success in identifying mvps from 2007-2017.

# Summary

The first thing I did was read in a dataset of hitters with several stats, both sabermetric and traditional, from 1995 to 2018.  The exact stats are detailed in the data dictionary below.  Once I split the stats into 7 traditional and 7 sabermetric stats, I fit two boosted models onto a training set and predicted the MVPs from the original dataset.  This didn't work as well as i had hoped, due to imbalanced classes, so I resampled MVPs and non-MVPs and tried again, with improved results.  Once that was done, I imputed some missing stats onto FANs projections and predicted the MVPs for the 2019 season, with very interesting results.  

Using the tactics that worked on hitters, I resampled pitcher seasons from 1995 to 2018 and trained 2 boosted models onto sabermetric and traditional pitching stats.  One stat I had was not present on projections (LOB%), so I could not use the sabermetric model to predict Cy Young winners.  Considering the lack of success of my previous model with predicting MVPs, I decided to not perform this on the pitching projections. 

With some extra time, I trained neural networks on the 4 sets as well.  I used two dense layers of 14 nodes for each model.  These performed well, but not better than my boosted models, likely due to the class imabalance.  

# Models

## Hitting

### Traditional

AdaBoostClassifier(LogisticRegression(C = 5, penalty = 'l2'))

### Sabermetric

AdaBoostClassifier(LogisticRegression(C = 2, penalty = 'l2'))

## Pitching

### Traditional

AdaBoostClassifier(LogisticRegression(C = 2, penalty = 'l1'))

### Sabermetric

AdaBoostClassifier(LogisticRegression(C = 5, penalty = 'l1'))

# Data Dictionary

T/S stands for Tradtional or Sabermetric

## Pitching

|Stat| T/S| Description|
|---|---|---|
|W|T|Wins By Starting Pitcher|
|L|T|Losses By Starting Pitcher|
|IP|T|Innings Pitched|
|SO|T|Total Strikeouts|
|H|T|Hits Surrendered|
|HR|T|Home Runs Surrendered|
|ERA|T|Earned Run Average; Runs Surrendered per 9 Innings|
|K/9|S|Strikeouts per 9 Innings|
|ERA-|S|ERA compared to the rest of the league, lower is better|
|FIP-|S|Fielding Independent Pitching compared to the rest of the league, lower is better|
|BB/9|S|Walks Surrendered per 9 Innings|
|LOB%|S|Runners Stranded on the Basepaths|
|WHIP|S|Walks and Hits per innings pitched|
|WAR|S|Wins above Replacement|

## Hitting
|Stat| T/S| Description|
|---|---|---|
|H|T|Raw number of hits|
|RBI|T|Runs Batted In|
|HR|T|Home Runs Hit|
|SO|T|Times Struck out|
|AVG|T|Hits divided by At Bats|
|OBP|T|Times on base divided by plate appearances|
|OPS|T|On Base Percentage + Slugging Percentage|
|R|S|Runs Scored|
|BB|S|Number of Walks|
|IBB|S|Number of times Intentionally Walked|
|OPS+|S|OPS compared to the rest of the league, higher is better|
|WRC+|S|Weighted Runs Created compared to the rest of the league, higher is better|
|TTO%|S|% of plate appearences that end in a Strikeout, Walk, or Home Run|
|WAR|S|Wins Above Replacement|

# Conclusion

Overall the models performed well.  The specific scores are listed below for the 4 boosted models, evaluated on Sensitivity(Recall) and Specificity, rounded to three decimal places.

## Hitting

### Traditional
|Metric|Score|
|---|---|
|Recall |0.493|
|Specificity|0.978|

### Sabermetric
|Metric|Score|
|---|---|
|Recall|0.473|
|Specificity|0.977

## Pitching

### Traditional
|Metric|Score|
|---|---|
|Recall|0.857|
|Specificity|0.972|

### Sabermetric
|Metric|Score|
|---|---|
|Recall|0.693|
|Specificity|0.974|

Overall, the pitching models performed better in terms of recall, since it was a smaller dataset and was slightly more balanced, even after the resample.  All models performed similarly in terms of specififity, showing that neither set of statistics are better than the other at predicting the outcome of an MVP or a Cy Young award.  

Both models also generated a high amount of false postives.  I consider this a good thing, as my model felt there were multiple people that could've won but shouldn't.  In addition, it did a good job of not misclassifcying MVPs and Cy Young winners, which was what I wanted to minimize when evaluating the model.  

I believe that, while front offices are shifting toward analytics and sabermetrics, the voters of awards are a different story.  Many of them have been voting on awards for decades and may not have embraced analytics.  In contrast, younger members may be more inclined to use sabermetrics in their analysis to determine their vote.  I believe, in time, we will reach a tipping point where sabermetrics is the more reliable indicator of an award winner.  

# Sources

All data was pulled from custom fangraphs reports for [pitchers](https://www.fangraphs.com/leaders.aspx?pos=all&stats=pit&lg=all&qual=y&type=c%2c4%2c5%2c11%2c7%2c8%2c13%2c-1%2c36%2c37%2c40%2c43%2c44%2c-1%2c6%2c45%2c-1%2c59%2c117%2c118%2c15%2c18%2c24%2c42&season=2018&month=0&season1=1995&ind=1&team=0&rost=0&age=0&filter=&players=0&page=67_30) and [hitters](https://www.fangraphs.com/leaders.aspx?pos=all&stats=bat&lg=all&qual=y&type=c%2c4%2c5%2c6%2c-1%2c7%2c12%2c13%2c-1%2c11%2c14%2c16%2c15%2c-1%2c23%2c37%2c38%2c39%2c-1%2c61%2c58&season=2018&month=0&season1=1995&ind=1&team=0&rost=0&age=0&filter=&players=0)