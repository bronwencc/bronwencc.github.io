---
layout: post
title:      "A Higher-Rated Strategy Game App"
date:       2021-02-28 17:25:34 -0500
permalink:  a_higher_rated_strategy_game_app
---

The project I completed for the Data Science bootcamp Module 5 involved finding a dataset I was unfamiliar with and doing some analysis on it. I browsed Kaggle and came across a set of variables about strategy game apps from the Apple App Store.

I chose the target feature to be the average user rating and found an AdaBoost model to have 64% accuracy with 11 significant, high-importance features.

One of my conclusions was based on higher-rated apps having more characters in their Description, having a Subtitle, and having fewer characters in their app name. This led me to suggest that a lower-rated app could improve its rating by renaming the app and moving extraneous text to the Subtitle or Description field.

Another finding was that a higher number of user ratings likely meant the app had a higher average user rating. There were higher-rated apps that had a small number of ratings.

![Plot of log number of user ratings vs average user rating](https://raw.githubusercontent.com/bronwencc/Module-5-Project/master/images/Distribution%20of%20Rating%20Count.png)
I made a scatter plot using the log of the number of user ratings because otherwise it was harder to compare the distribution on the low end and high end.

If an app is lower-rated, I would suggest asking (more) users to rate it or to look to higher-rated apps' tactics. For instance, I have seen apps (not only strategy games) that occasionally ask whether the user likes the app. If the user says they don't, it links to a feedback form and if they say they do, it sends them to leave a rating on the app store. This kind of marketing nudge filters out potentially negative reviews and low ratings.

### Features

The analysis found 11 important features that account for whether an app has a higher rating or a lower rating. A higher-rated app was defined as having an average user rating of 4.5 or higher and a lower-rated app was defined as having an average rating of 4 or under. The apps' average ratings were provided at halves, so there are no apps in the dataset with an average rating between 4 and 4.5.

The six features with the highest importance were:
* The number of ratings
* Whether the app has a Subtitle
* The size of the application
* The length of Description text
* Whether the application is in the Simulation genre
* Whether the application is in the Casual genre

I also engineered over 200 features using dummy (or one-hot encoding) techniques. I separated in-app purchase price points into bins, as well as converted the lists of language and subgenre into their own dummy variable.